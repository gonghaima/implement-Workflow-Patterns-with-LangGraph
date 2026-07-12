# Cheat Sheet: Build GenAI Application with LangChain

<table>
<thead>
<tr>
<th>Package/Method</th>
<th>Description</th>
<th>Code Example</th>
</tr>
</thead>
<tbody>

<tr>
<td><b>mkdir and cd</b></td>
<td>Create and navigate into a new project directory.</td>
<td>

```bash
mkdir genai_flask_app
cd genai_flask_app
```

</td>
</tr>

<tr>
<td><b>Virtual environment</b></td>
<td>Set up a Python virtual environment for package management.</td>
<td>

```bash
python3.11 -m venv venv
source venv/bin/activate
```

</td>
</tr>

<tr>
<td><b>pip install ibm-watsonx-ai</b></td>
<td>Install the IBM watsonx AI library for LLM interactions.</td>
<td>

```bash
pip install ibm-watsonx-ai
```

</td>
</tr>

<tr>
<td><b>Credentials</b></td>
<td>Authenticate with IBM watsonx AI using credentials.</td>
<td>

```python
from ibm_watsonx_ai import Credentials

credentials = Credentials(
    url="https://us-south.ml.cloud.ibm.com",
    # api_key="<YOUR_API_KEY>"
)
```

</td>
</tr>

<tr>
<td><b>Model parameters</b></td>
<td>Define parameters for model inference.</td>
<td>

```python
from ibm_watsonx_ai.metanames import GenTextParamsMetaNames

params = {
    GenTextParamsMetaNames.DECODING_METHOD: "greedy",
    GenTextParamsMetaNames.MAX_NEW_TOKENS: 100
}
```

</td>
</tr>

<tr>
<td><b>Model inference</b></td>
<td>Initialize an AI model for text generation.</td>
<td>

```python
from ibm_watsonx_ai.foundation_models import ModelInference

model = ModelInference(
    model_id="ibm/granite-3-3-8b-instruct",
    params=params,
    credentials=credentials,
    project_id="skills-network"
)
```

</td>
</tr>

<tr>
<td><b>Generating AI response</b></td>
<td>Use an AI model to generate text based on a prompt.</td>
<td>

```python
text = """
Only reply with the answer. What is the capital of Canada?
"""

print(model.generate(text)['results'][0]['generated_text'])
```

</td>
</tr>

<tr>
<td><b>LangChain prompt templates</b></td>
<td>Define reusable prompt templates for different models.</td>
<td>

```python
from langchain.prompts import PromptTemplate

llama3_template = PromptTemplate(
    template='''<|begin_of_text|><|start_header_id|>system<|end_header_id|>
{system_prompt}<|eot_id|><|start_header_id|>user<|end_header_id|>
{user_prompt}<|eot_id|><|start_header_id|>assistant<|end_header_id|>
''',
    input_variables=["system_prompt", "user_prompt"]
)
```

</td>
</tr>

<tr>
<td><b>LangChain chaining</b></td>
<td>Pipe a prompt template into an AI model to generate structured output.</td>
<td>

```python
def get_ai_response(model, template, system_prompt, user_prompt):
    chain = template | model
    return chain.invoke({'system_prompt': system_prompt, 'user_prompt': user_prompt})
```

</td>
</tr>

<tr>
<td><b>Tokenization and prompt formatting</b></td>
<td>Specialized token formatting for different AI models.</td>
<td>

```python
# Llama 3 formatted prompt
text = """
<|begin_of_text|><|start_header_id|>system<|end_header_id|>
You are an expert assistant who provides concise and accurate answers.<|eot_id|>
<|start_header_id|>user<|end_header_id|>
What is the capital of Canada?<|eot_id|>
<|start_header_id|>assistant<|end_header_id|>
"""
```

</td>
</tr>

<tr>
<td><b>JSON output parser</b></td>
<td>Parse and structure AI-generated responses using LangChain.</td>
<td>

```python
from langchain_core.output_parsers import JsonOutputParser
from pydantic import BaseModel, Field

class AIResponse(BaseModel):
    summary: str = Field(description="Summary of the user's message")
    sentiment: int = Field(description="Sentiment score from 0 to 100")
    response: str = Field(description="Generated AI response")

json_parser = JsonOutputParser(pydantic_object=AIResponse)
```

</td>
</tr>

<tr>
<td><b>Enhancing AI outputs</b></td>
<td>Modify LangChain chaining to ensure structured JSON output.</td>
<td>

```python
def get_ai_response(model, template, system_prompt, user_prompt):
    chain = template | model | json_parser
    return chain.invoke({
        'system_prompt': system_prompt,
        'user_prompt': user_prompt,
        'format_prompt': json_parser.get_format_instructions()
    })
```

</td>
</tr>

<tr>
<td><b>Flask API integration</b></td>
<td>Create an API endpoint for AI model interactions.</td>
<td>

```python
from flask import Flask, request, jsonify
from model import get_model_response

app = Flask(name)

@app.route('/generate', methods=['POST'])
def generate():
    data = request.json
    model_name = data.get('model')
    user_message = data.get('message')

    if not user_message or not model_name:
        return jsonify({"error": "Missing message or model selection"}), 400

    system_prompt = "You are an AI assistant helping with customer inquiries. Provide a concise response."

    try:
        response = get_model_response(model_name, system_prompt, user_message)
        return jsonify(response)
    except Exception as e:
        return jsonify({"error": str(e)}), 500

if name == 'main':
    app.run(debug=True)
```

</td>
</tr>

</tbody>
</table>
