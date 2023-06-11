# API Docs


## Endpoint

Whipcode's V1 endpoint is [https://whipcode.p.rapidapi.com](https://whipcode.p.rapidapi.com)

A [RapidAPI](https://rapidapi.com) key and Whipcode [subscription](https://rapidapi.com/Whipcode/api/whipcode/pricing) are required to access the endpoint.

Only POST requests are accepted, GET requests will be rejected.

## Headers

These headers are required for requests:
```text
Content-Type: application/json
X-RapidAPI-Key: 
X-RapidAPI-Host: whipcode.p.rapidapi.com
```

## Parameters

- `language_id` (integer/string) - ID of the interpreter to use for running the provided code.

    |Interpreter|ID|
    |--|--|
    |Python|1|
    |Node.js|2|
    |Bash|3|
    |Perl|4|
    |Lua|5|
    |Ruby|6|
    |C (tcc)|7|

- `code` (string) - The source code, base64 encoded. Whipcode will reject any code submission (after decoding) that has more than 50000 characters.

## Server Response

Successful requests will contain:
- `stdout` (string) - All data captured from stdout.
- `stderr` (string) - All data captured from stderr.
- `container_age` (float) - Duration the container allocated for your code ran, in seconds.
- `timeout` (boolean) - Boolean value depending on whether your container lived past the timeout period. Timeout is 8 seconds, after which your container gets forcefully killed. A reply from a timed-out request will not have any data in `stdout` and `stderr`

In the event of an error, or an invalid/rejected request:
- `detail` (string) - Details about why the request failed to complete.

The response will also contain the appropriate status codes, which follows the guidelines in [restfulapi.net/http-status-codes](https://restfulapi.net/http-status-codes/)

If the Whipcode API is down, RapidAPI will return their own response which will contain:
- `messages` (string)
- `info` (string)

Similarly, if your API key is invalid or your request size exceeds 500 KB, the response will contain:
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
	"language_id": 2,
	"code": encoded
}

headers = {
	"Content-Type": "application/json",
	"X-RapidAPI-Key": "",
	"X-RapidAPI-Host": "whipcode.p.rapidapi.com"
}

response = requests.post(url, json=payload, headers=headers)

if response.status_code == 200:
    response = response.json()
    if not response["timeout"]:
        print("stdout: {}\nstderr:{}".format(
            response.["stdout"],
            response.["stderr"]
        ))
    
    else:
        print("Code execution timed out.")

else:
    print(response.json())
```

The resulting output:

```text
stdout: Hello world!\n 
stderr:
```