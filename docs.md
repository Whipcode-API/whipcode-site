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

- `language_id` (integer/string) - ID of the language the submitted code is written in.

    |Language|ID|||
    |--|--|--|--|
    |Python|1|C (GCC)|7|
    |Javscript (Node.js)|2|C++ (GCC)|8|
    |Bash|3|Rust|9|
    |Perl|4|Fortran|10|
    |Lua|5|Haskell|11|
    |Ruby|6|||


- `code` (string) - The source code, base64 encoded. Submissions with more than 50000 characters (after decoding) will be rejected.

## Server Response

Successful requests will contain:
- `stdout` (string) - All data captured from stdout.
- `stderr` (string) - All data captured from stderr.
- `container_age` (float) - Duration the container allocated for your code ran, in seconds.
- `timeout` (boolean) - Boolean value depending on whether your container lived past the timeout period. Timeout is 10 seconds, after which your container gets forcefully killed. A reply from a timed-out request will not have any data in `stdout` and `stderr`

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
            response["stdout"],
            response["stderr"]
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

## Execution Limits

Each request starts a new container which runs the submitted code, and is destroyed after. Here are some of the resource limits that may affect your code's execution:

- Memory: During periods with low server load, containers can use up to 512 mb of memory each. During periods with medium to high server load, they will be limited to somewhere between 128 mb and 512 mb.

- Processes: The maximum number of processes allowed inside each container is 32. Spawning/forking processes too much may cause errors.

- CPU: Each container is only allowed to execute it's processes on a single cpu core, making multiprocessing ineffective.

- Networking: Disabled completely for security/abuse reasons.

- Filesystem: The root filesystem of the containers are read-only. However, a tmpfs of 64 mb is mounted at `/tmp` as read-write, letting you perform write operations there.
