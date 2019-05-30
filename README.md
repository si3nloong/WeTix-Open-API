### WeTix RESTful OPEN API

#### 1. Request Access Token

`API URL` : https://sb-open.wetix.my/v1/access-token<br/>
`Method` : POST<br/>
`Request Body` :<br/>

```json
{
  "clientId": "P18KvBVftB",
  "clientSecret": "123456"
}
```

`Response Body` : <br/>

```jsonb
{
  "accessToken": "eyJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1NTc1NzI2MDEsImlhdCI6MTU1NzQ4NjIwMSwiaXNzIjoid2V0aXgubXkiLCJuYmYiOjE1NTc0ODYyMDEsInN1YiI6IndldGl4fEVpSUtGRVJsZG1Wc2IzQmxja0Z3Y0d4cFkyRjBhVzl1R2dwUU1UaExka0pXWm5SQyJ9.wBa3kaNxvGt1sxzypzGOZXGlCxgxTaGLq6kLYo_ABY4",
  "expiresIn": 86400 // expires time in seconds,
}
```

#### 2. Create Order

`API URL` : https://sb-open.wetix.my/v1/order<br/>
`Method` : POST<br/>
`Request Header`:

```yml
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1NTc1NzI2MDEsImlhdCI6MTU1NzQ4NjIwMSwiaXNzIjoid2V0aXgubXkiLCJuYmYiOjE1NTc0ODYyMDEsInN1YiI6IndldGl4fEVpSUtGRVJsZG1Wc2IzQmxja0Z3Y0d4cFkyRjBhVzl1R2dwUU1UaExka0pXWm5SQyJ9.wBa3kaNxvGt1sxzypzGOZXGlCxgxTaGLq6kLYo_ABY4 # token that generated in step 1
X-Signature: 73867b1625f2759da4a8add0c5e6b46ca6a08e6808dfb3e5d720a028116d3fb6cab22f4ab0eae1d081446f67e8e78b9c36153b97c93e6d8cfa74fe8fd4dd62539d01b5ec41a8239a5afd32daddb4a35e58d82a8fcf8bf74ed0eb5c9b31d5b4b782b6e4c2966a0426ff5eda8557cab24a0ba931eb2e3dcaf62e68137b46c7e031 # please refer to "How to generate signature?"
X-Timestamp: 1557486958918 # unix timestamp
X-Nonce: cSwY4AfCNLixNaDytlyF # random string, as least 20 characters
```

`Request Body` :<br/>

```jsonb
{
  "userId": "1234",
  "type": "WEB_PAYMENT",
  "order": {
    "id": "8888", // RXA unique order id
    "title": "5.2km Open (12yrs & above)", // max 100 characters
    "currencyCode": "MYR",
    "amountInCents": 1000 // RM10.00 is interpret as 1000
  },
  "payload": {
    "id": "6523", // RXA listing id
    "title": "Larian Kibarkan Malaysiaku 2.0",
    "imageUrl": "https://s3-ap-southeast-1.amazonaws.com/racexasia/wp-content/uploads/2019/04/03172608/400x240.jpg",
    "category": "5.2km Open (12yrs & above)",
    "participants": [{ "name": "user1" }, { "name": "user2" }]
  },
  "notifyUrl": "https://www.google.com" // RXA notify url, WeTix will send payment success nor failure response to this url
}
```

`Response Body` : <br/>

```jsonb
{
  "item": {
    "id": "8888",
    "transactionId": "FLMqKVv57H",
    "title": "5.2km Open (12yrs & above)",
    "currencyCode": "MYR",
    "amountInCents": 1000,
    "checkoutUrl": "https://local-open.wetix.my:8080/order/EiIKFERldmVsb3BlckFwcGxpY2F0aW9uGgpQMThLdkJWZnRCEhMKC09ubGluZU9yZGVyGgQ4ODg4/checkout?userKey=&expiresAt=&signature=67e7797dd8beb6be11d2ae62ecc39abc3d3d52aadf484e78b040c2164c6163b1",
    "notifyUrl": "https://www.google.com",
    "status": "PENDING",
    "expiredAt": "2019-05-10T11:26:30Z",
    "createdAt": "2019-05-10T11:16:30Z"
  }
}
```

#### How to generate signature?

`1. Prepare following items :`

```yml
url: https://sb-open.wetix.my/v1/order
algorithm: RSA-SHA256
method: POST # restful method
timestamp: 1557486958918 # unix timestamp
nonce: cSwY4AfCNLixNaDytlyF # random string, as least 20 characters
body: eyJ1c2VySWQiOiIxMjM0IiwidHlwZSI6IldFQl9QQVlNRU5UIiwib3JkZXIiOnsiaWQiOiI4ODg4IiwidGl0bGUiOiI1LjJrbSBPcGVuICgxMnlycyAmIGFib3ZlKSIsImN1cnJlbmN5Q29kZSI6Ik1ZUiIsImFtb3VudEluQ2VudHMiOjEwMDB9LCJwYXlsb2FkIjp7ImlkIjoiNjUyMyIsInRpdGxlIjoiTGFyaWFuIEtpYmFya2FuIE1hbGF5c2lha3UgMi4wIiwiaW1hZ2VVcmwiOiJodHRwczovL3MzLWFwLXNvdXRoZWFzdC0xLmFtYXpvbmF3cy5jb20vcmFjZXhhc2lhL3dwLWNvbnRlbnQvdXBsb2Fkcy8yMDE5LzA0LzAzMTcyNjA4LzQwMHgyNDAuanBnIiwiY2F0ZWdvcnkiOiI1LjJrbSBPcGVuICgxMnlycyAmIGFib3ZlKSIsInBhcnRpY2lwYW50cyI6W3sibmFtZSI6InVzZXIxIn0seyJuYW1lIjoidXNlcjIifV19LCJub3RpZnlVcmwiOiJodHRwczovL3d3dy5nb29nbGUuY29tIn0= # base64 encoded of request body (compact json, json without spaces) --> base64(`{"userId":"1234","type":"WEB_PAYMENT","order":{"id":"8888","title":"5.2km Open (12yrs & above)","currencyCode":"MYR","amountInCents":1000},"payload":{"id":"6523","title":"Larian Kibarkan Malaysiaku 2.0","imageUrl":"https://s3-ap-southeast-1.amazonaws.com/racexasia/wp-content/uploads/2019/04/03172608/400x240.jpg","category":"5.2km Open (12yrs & above)","participants":[{"name":"user1"},{"name":"user2"}]},"notifyUrl":"https://www.google.com"}`)
```

`2. Concatenate the items above and sort it with ascending order`

```bash
algorithm=RSA-SHA256&body=eyJ1c2VySWQiOiIxMjM0IiwidHlwZSI6IldFQl9QQVlNRU5UIiwib3JkZXIiOnsiaWQiOiI4ODg4IiwidGl0bGUiOiI1LjJrbSBPcGVuICgxMnlycyAmIGFib3ZlKSIsImN1cnJlbmN5Q29kZSI6Ik1ZUiIsImFtb3VudEluQ2VudHMiOjEwMDB9LCJwYXlsb2FkIjp7ImlkIjoiNjUyMyIsInRpdGxlIjoiTGFyaWFuIEtpYmFya2FuIE1hbGF5c2lha3UgMi4wIiwiaW1hZ2VVcmwiOiJodHRwczovL3MzLWFwLXNvdXRoZWFzdC0xLmFtYXpvbmF3cy5jb20vcmFjZXhhc2lhL3dwLWNvbnRlbnQvdXBsb2Fkcy8yMDE5LzA0LzAzMTcyNjA4LzQwMHgyNDAuanBnIiwiY2F0ZWdvcnkiOiI1LjJrbSBPcGVuICgxMnlycyAmIGFib3ZlKSIsInBhcnRpY2lwYW50cyI6W3sibmFtZSI6InVzZXIxIn0seyJuYW1lIjoidXNlcjIifV19LCJub3RpZnlVcmwiOiJodHRwczovL3d3dy5nb29nbGUuY29tIn0=&method=POST&nonce=cSwY4AfCNLixNaDytlyF&timestamp=1557486958918&url=https://sb-open.wetix.my/v1/order
```

`3. Prepare the RSA private key (PEM)`

```pem
-----BEGIN RSA PRIVATE KEY-----
MIICXQIBAAKBgQC+aISzkNuqbfgqYoHambKGrhAzRutuf2uas9BHyyePRTuI9eWp
rGcyQfXOVXv8bAeS1+kHKC/+VCNODlfALYPYUYkNwxuoFqLmMRGq5330HEKIJrH7
IE9iK4AEY/xyZ0SPJyfCgCfZxkINjZqahI/+Ulk/Pful2hD4e3TeZFNnGQIDAQAB
AoGAHzNbQ11iUWwRtUhNBPgYg+8z4mMlowaoKEJ8x7bndZefqNK6XnJMurSKRdRG
K9e76fkNS0nvi1pYKqs4/9m1d82OWvkCyvoGzfEwr4bzmPAf7ds5dXIaoopmexYl
lZlJkn08V4Rf9g8DQ24TlopigtkK69RKQK1GhurWP8R5qy0CQQDgwqpeJdQyEGZb
PPII7flIECF9P6sUsZ/5n4pHM6h67GNte5pLxl939v2mXZ7OZIFGAMkRcC/VH+w8
nhk+Z4OrAkEA2N+Mh9di7V+zWZ5CHYpVLs9B/q9Ywb1B63tRvTloPJqjLw5453eE
lK4frRiXWlHKiJKbPNMMYQY2MTkpD6t8SwJBAIdSbQTWPdYOrbHNFeRucyICJEem
p7iD4U+H0NdhsNShspNeUd3BiAVQfhNGVrDpLjQZkPWg2Au3dqJghc5ewJUCQAEE
ExFz1dfM0fdCgYbH5hxBBkseISlPLKbgvgJH6ZAXHVqUE8bpzWow4p8Z9WOL7In1
PdrsFivCLrDOVr3nDL8CQQDJHCpHEE574rJsnRMbNkGAyvfayoLxIaUAyYz/hlk2
sCL3oplt3X3Kcc5u2DHIQle7F3S8ZjxDFLITknrxA/TV
-----END RSA PRIVATE KEY-----
```

`4. Generate the signature in HEX format by signing the data in step 2 using RSA private key in step 3`

```
73867b1625f2759da4a8add0c5e6b46ca6a08e6808dfb3e5d720a028116d3fb6cab22f4ab0eae1d081446f67e8e78b9c36153b97c93e6d8cfa74fe8fd4dd62539d01b5ec41a8239a5afd32daddb4a35e58d82a8fcf8bf74ed0eb5c9b31d5b4b782b6e4c2966a0426ff5eda8557cab24a0ba931eb2e3dcaf62e68137b46c7e031
```

#### 3. Get Order by ID

`API URL` : https://sb-open.wetix.my/v1/order/8888<br/>
`Method` : GET<br/>
`Request Header`:

```yml
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1NTc1NzI2MDEsImlhdCI6MTU1NzQ4NjIwMSwiaXNzIjoid2V0aXgubXkiLCJuYmYiOjE1NTc0ODYyMDEsInN1YiI6IndldGl4fEVpSUtGRVJsZG1Wc2IzQmxja0Z3Y0d4cFkyRjBhVzl1R2dwUU1UaExka0pXWm5SQyJ9.wBa3kaNxvGt1sxzypzGOZXGlCxgxTaGLq6kLYo_ABY4 # token that generated in step 1
X-Signature: 05e296d320d456d816dbe83860c76ff8ee9c284f42b1cc95dac672bfaefed8c7969ba4d7fc7f4d867a9cb4c90ad0784ef65f6ba712b289b514c248d923907425bb488a9763e424a1bf97c0af8e8f90122e2e5eb4885ba1f9a87cf6a427cf869fa103292d5b5537dd8515b24ad3a73f6cd6f0a07ddb9ea1390912c096f93e384d # please refer to "How to generate signature?"
X-Timestamp: 1557489064983 # unix timestamp
X-Nonce: cSwY4AfCNLixNaDytlyF # random string, as least 20 characters
```

`1. Prepare following items :`

```yml
url: https://sb-open.wetix.my/v1/order/8888
algorithm: RSA-SHA256
method: GET # restful method
timestamp: 1557489064983 # unix timestamp
nonce: cSwY4AfCNLixNaDytlyF # random string, as least 20 characters
body: # empty body
```

`2. Concatenate the items above and sort it with ascending order`

```bash
algorithm=RSA-SHA256&body=&method=GET&nonce=cSwY4AfCNLixNaDytlyF&timestamp=1557489064983&url=https://sb-open.wetix.my/v1/order/8888
```

`3. Prepare the RSA private key (PEM)`

```pem
-----BEGIN RSA PRIVATE KEY-----
MIICXQIBAAKBgQC+aISzkNuqbfgqYoHambKGrhAzRutuf2uas9BHyyePRTuI9eWp
rGcyQfXOVXv8bAeS1+kHKC/+VCNODlfALYPYUYkNwxuoFqLmMRGq5330HEKIJrH7
IE9iK4AEY/xyZ0SPJyfCgCfZxkINjZqahI/+Ulk/Pful2hD4e3TeZFNnGQIDAQAB
AoGAHzNbQ11iUWwRtUhNBPgYg+8z4mMlowaoKEJ8x7bndZefqNK6XnJMurSKRdRG
K9e76fkNS0nvi1pYKqs4/9m1d82OWvkCyvoGzfEwr4bzmPAf7ds5dXIaoopmexYl
lZlJkn08V4Rf9g8DQ24TlopigtkK69RKQK1GhurWP8R5qy0CQQDgwqpeJdQyEGZb
PPII7flIECF9P6sUsZ/5n4pHM6h67GNte5pLxl939v2mXZ7OZIFGAMkRcC/VH+w8
nhk+Z4OrAkEA2N+Mh9di7V+zWZ5CHYpVLs9B/q9Ywb1B63tRvTloPJqjLw5453eE
lK4frRiXWlHKiJKbPNMMYQY2MTkpD6t8SwJBAIdSbQTWPdYOrbHNFeRucyICJEem
p7iD4U+H0NdhsNShspNeUd3BiAVQfhNGVrDpLjQZkPWg2Au3dqJghc5ewJUCQAEE
ExFz1dfM0fdCgYbH5hxBBkseISlPLKbgvgJH6ZAXHVqUE8bpzWow4p8Z9WOL7In1
PdrsFivCLrDOVr3nDL8CQQDJHCpHEE574rJsnRMbNkGAyvfayoLxIaUAyYz/hlk2
sCL3oplt3X3Kcc5u2DHIQle7F3S8ZjxDFLITknrxA/TV
-----END RSA PRIVATE KEY-----
```

`4. Generate the signature in HEX format by signing the data in step 2 using RSA private key in step 3`

```
05e296d320d456d816dbe83860c76ff8ee9c284f42b1cc95dac672bfaefed8c7969ba4d7fc7f4d867a9cb4c90ad0784ef65f6ba712b289b514c248d923907425bb488a9763e424a1bf97c0af8e8f90122e2e5eb4885ba1f9a87cf6a427cf869fa103292d5b5537dd8515b24ad3a73f6cd6f0a07ddb9ea1390912c096f93e384d
```

#### 4. Payment Webhook

`API URL` : https://rxa.com.my/webhook (This is your webhook url)<br/>
`Method` : POST<br/>
`Response Header`:

```yml
X-Signature: 05e296d320d456d816dbe83860c76ff8ee9c284f42b1cc95dac672bfaefed8c7969ba4d7fc7f4d867a9cb4c90ad0784ef65f6ba712b289b514c248d923907425bb488a9763e424a1bf97c0af8e8f90122e2e5eb4885ba1f9a87cf6a427cf869fa103292d5b5537dd8515b24ad3a73f6cd6f0a07ddb9ea1390912c096f93e384d # please refer to "How to generate signature?"
X-Timestamp: 1557489064983 # unix timestamp
X-Nonce: cSwY4AfCNLixNaDytlyF # random string, as least 20 characters
```

`Response Body`:

```json
{
  "item": {
    "id": "10000",
    "transactionId": "30019053009418793020",
    "title": "testing",
    "wallet": "",
    "currencyCode": "MYR",
    "amountInCents": 1800,
    "checkoutUrl": "https://local-open.wetix.my:8080/order/WP-BAwEBBmdvYktleQH_ggABBgEES2luZAEMAAEIU3RyaW5nSUQBDAABBUludElEAQQAAQZQYXJlbnQB_4IAAQVBcHBJRAEMAAEJTmFtZXNwYWNlAQwAAAAz_4IBC09ubGluZU9yZGVyAQUxMDAwMAIB",
    "notifyUrl": "https://www.google.com",
    "status": "STATUS",
    "expiredAt": "2019-05-30T09:51:21Z",
    "createdAt": "2019-05-30T09:41:21Z"
  }
}
```
