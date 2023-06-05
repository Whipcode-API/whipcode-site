# Whipcode API Documentation

A [RapidAPI](https://rapidapi.com) key and Whipcode [subscription](https://rapidapi.com/Whipcode/api/whipcode/pricing) are required to access the API.

## Endpoint

Whipcode's V1 endpoint is [https://whipcode.p.rapidapi.com](https://whipcode.p.rapidapi.com)

Only POST requests are supported, GET requests will be rejected.

## Headers

These headers are required for requests:
```text
Content-Type: application/json
X-RapidAPI-Key: 
X-RapidAPI-Host: whipcode.p.rapidapi.com
```

## Parameters

- `language_id` (string) - ID of the interpreter to use for running the provided code

    |Interpreter|ID|
    |--|--|
    |Python|1|
    |Node.js|2|
    |Bash|3|
    |Perl|4|
    |Lua|5|
    |Ruby|6|

- `code` (string) - The source code, base64 encoded

## Server Response

Successful requests will contain:
- `stdout` (string) - All data captured from stdout
- `stderr` (string) - All data captured from stderr
- `container_age` (float) - Duration the container allocated for your code ran, in seconds

In the event of an error, or an invalid request:
- `detail` (string) - Details about why the request failed to complete

The response will also contain the appropriate status codes, which follows the guidelines in [restfulapi.net/http-status-codes](https://restfulapi.net/http-status-codes/)

If the Whipcode API is down or your API key is invalid, RapidAPI will return their own response which will contain either:
- `messages` (string)
- `info` (string)

Or just:
- `message` (string)

## Example Request

Here's an example Python snippet makes a request to run Javascript with Whipcode:

```python3
import requests
import base64

url = "https://whipcode.p.rapidapi.com/"

source = """
console.log('Hello world!');
"""

encoded = base64.b64encode(source.encode()).decode()

payload = {
	"language_id": "2",
	"code": encoded
}

headers = {
	"Content-Type": "application/json",
	"X-RapidAPI-Key": "",
	"X-RapidAPI-Host": "whipcode.p.rapidapi.com"
}

response = requests.post(url, json=payload, headers=headers)

if response.status_code == 200:
    print("stdout: {}\nstderr:{}".format(
        response.json()["stdout"],
        response.json()["stderr"]
    ))

else:
    print(response.json())
```

The resulting output:

```text
stdout: Hello world!\n 
stderr:
```