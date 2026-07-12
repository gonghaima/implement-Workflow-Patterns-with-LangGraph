# Cheat Sheet: Web Development Using Flask

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
<td><b>Flask</b></td>
<td>Used to instantiate an object of the Flask class named app.</td>
<td>

```python
from flask import Flask

app = Flask(name)
```

</td>
</tr>

<tr>
<td><b>@app.route decorator</b></td>
<td>A decorator in Flask used to map URLs to specific functions in a Flask application.</td>
<td>

```python
@app.route('/')
def hello_world():
    return "My first Flask application in action!"
```

</td>
</tr>

<tr>
<td><b>200 OK status</b></td>
<td>Flask servers automatically return a 200 OK status when you return from the @app.route method. 200 is also returned by default when you use the jsonify() method to respond to a request. A successful response with a status code of 200 will be sent back when the given code executes.</td>
<td>

```python
@app.route('/')
def hello_world():
    return ("My first Flask application in action!", 200)
```

</td>
</tr>

<tr>
<td><b>Error 404</b></td>
<td>400 indicates an invalid request. This status could imply the parameters are missing or improper or the request is invalid in another way. 401 indicates the credentials are missing or invalid. 403 implies that the client credentials are not sufficient to fulfill the request. 404 If the server is unable to find the resource, it returns a 404 status. 405 indicates that the requested operation is not supported.</td>
<td>

```python
@app.route('/')
def search_response():
    query = request.args.get("q")
    if not query:
        return {"error_message": "Input parameter missing"}, 422

    # fetch the resource from the database
    resource = fetch_from_database(query)

    if resource:
        return {"message": resource}
    else:
        return {"error_message": "Resource not found"}, 404
```

</td>
</tr>

<tr>
<td><b>Error 500</b></td>
<td>500 is used when there is an error on the server.</td>
<td>

```python
@app.errorhandler(500)
def server_error(error):
    return {"message": "Something went wrong on the server"}, 500
```

</td>
</tr>

</tbody>
</table>
