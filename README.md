# rest-app
This is the framework for a GO (GOLANG) based ReST API. This can be used as the basis for a GO based app, needing JWT user authentication, with logging and key/value store (KVS). 

There are 2 parts to this application"
* github.com/paulfdunn/rest-app-common - Application initialization and configuration. This is provided in a common package to allow leveraging many apps from the same configuration/initialization code. 
* github.com/paulfdunn/rest-app-prod1 is provided to show an example of one app, using the common configuration/initialization provided.

Key features:
* Leveled logging; provided by github.com/paulfdunn/logh 
* Key/value store (KVS); provided by github.com/paulfdunn/db. The KVS is used to store application configuration data and authentication data, but can be used for any other purpose as well.
* The KVS implements object serialization/deserialization, making it easy to persist objects. 
* Authentication is handled using JWT (JSON Web Tokens).
* Authentication supports 2 models: anyone can create a login, or only a registered user can create a new login. The later is the default in the example app.
* Authentication supports REGEX based validation/rules for passwords.

## Requirements
You must have GO installed. This code was build and tested against GO 1.16.2

Example of curl commands against the provided app, showing creating and deleting an auth (user).
Start the app in a terminal window:
```
paulfdunn@penguin:~/go/src/github.com/paulfdunn/rest-app-prod1$ ./readme_example_test.sh 
```
Example terminal session:
```
paulfdunn@penguin:~/go/src/github.com/paulfdunn/rest-app-prod1$ ./readme_example_test.sh 
++ basename ./readme_example_test.sh
+ ME=readme_example_test.sh
+ echo 'STARTING: readme_example_test.sh'
STARTING: readme_example_test.sh
+ cleanup
+ killall rest-app
rest-app: no process found
+ rm rest-app.db
rm: cannot remove 'rest-app.db': No such file or directory
+ rm 'rest-app.log.*'
rm: cannot remove 'rest-app.log.*': No such file or directory
+ go build
+ sleep 3
+ ./rest-app -https-port=8080 -log-level=0 -log-filepath=./rest-app.log
++ curl -k -s -d '{"Email":"admin", "Password":"P@ss!234"}' https://127.0.0.1:8080/auth/login/
+ TOKEN_ADMIN=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MTg4NzMyOTEsImlzcyI6InJlc3QtYXBwIiwiRW1haWwiOiJhZG1pbiIsIlRva2VuSUQiOiI2ODdlYzA2Yi1lMWZmLWFlOGUtY2RhNC1mM2E3NWEwMWM4NmQifQ.YvmkBgQbGStGZ6h83_4pemApCYnG6cwNy_4ae3JbuOs
++ grep -o -E '[0-9]*'
++ grep HTTP_STATUS
++ curl -k -s -w '\n|HTTP_STATUS=%{http_code}|\n' -d '{"Email":"user", "Password":"P@ss!234"}' -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MTg4NzMyOTEsImlzcyI6InJlc3QtYXBwIiwiRW1haWwiOiJhZG1pbiIsIlRva2VuSUQiOiI2ODdlYzA2Yi1lMWZmLWFlOGUtY2RhNC1mM2E3NWEwMWM4NmQifQ.YvmkBgQbGStGZ6h83_4pemApCYnG6cwNy_4ae3JbuOs' https://127.0.0.1:8080/auth/create/
+ HTTP_STATUS=201
+ [[ 201 != 201 ]]
++ grep HTTP_STATUS
++ curl -k -s -w '\n|HTTP_STATUS=%{http_code}|\n' https://127.0.0.1:8080/
++ grep -o -E '[0-9]*'
+ HTTP_STATUS=401
+ [[ 401 != 401 ]]
++ curl -k -s -w '\n|HTTP_STATUS=%{http_code}|\n' -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MTg4NzMyOTEsImlzcyI6InJlc3QtYXBwIiwiRW1haWwiOiJhZG1pbiIsIlRva2VuSUQiOiI2ODdlYzA2Yi1lMWZmLWFlOGUtY2RhNC1mM2E3NWEwMWM4NmQifQ.YvmkBgQbGStGZ6h83_4pemApCYnG6cwNy_4ae3JbuOs' https://127.0.0.1:8080/
++ grep -o -E '[0-9]*'
++ grep HTTP_STATUS
+ HTTP_STATUS=200
+ [[ 200 != 200 ]]
++ curl -k -s -d '{"Email":"user", "Password":"P@ss!234"}' https://127.0.0.1:8080/auth/login/
+ TOKEN_USER=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MTg4NzMyOTEsImlzcyI6InJlc3QtYXBwIiwiRW1haWwiOiJ1c2VyIiwiVG9rZW5JRCI6IjljNzg1M2RjLTkwYTYtNGNjMS1hMjZiLTdmYTNkZjgzMWViZSJ9.GMXu2loyzaB1hPCOMVN8_P3njTQVkSVnaXSzqBxPDs4
++ curl -k -s -w '\n|HTTP_STATUS=%{http_code}|\n' -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MTg4NzMyOTEsImlzcyI6InJlc3QtYXBwIiwiRW1haWwiOiJ1c2VyIiwiVG9rZW5JRCI6IjljNzg1M2RjLTkwYTYtNGNjMS1hMjZiLTdmYTNkZjgzMWViZSJ9.GMXu2loyzaB1hPCOMVN8_P3njTQVkSVnaXSzqBxPDs4' -X DELETE https://127.0.0.1:8080/auth/delete/
++ grep HTTP_STATUS
++ grep -o -E '[0-9]*'
+ HTTP_STATUS=204
+ [[ 204 != 204 ]]
+ cleanup
+ killall rest-app
+ rm rest-app.db
Terminated
+ rm rest-app.log.0 rest-app.log.audit.0
+ echo 'PASSED: readme_example_test.sh'
PASSED: readme_example_test.sh
```
And see what is in the logs:
```
paulfdunn@penguin:~/go/src/github.com/paulfdunn/rest-app-prod1$ cat rest-app.log.0
info: 2021/04/19 22:48:43.454889 config.go:93: rest-app is starting....
info: 2021/04/19 22:48:43.454955 config.go:95: logFilepath:./rest-app.log
info: 2021/04/19 22:48:43.454970 config.go:96: auditLogFilepath:./rest-app.log.audit
info: 2021/04/19 22:48:43.544240 rest-app.go:74: Config: {"HTTPSPort":8080,"LogFilepath":"./rest-app.log","LogLevel":0,"AppName":"rest-app","AuditLogName":"rest-app.audit","DataSourceName":"/home/paulfdunn/go/src/github.com/paulfdunn/rest-app-prod1/rest-app.db","JWTKeyFilepath":"/home/paulfdunn/go/src/github.com/paulfdunn/rest-app-prod1/key/jwt.rsa.public","JWTAuthRemoveInterval":60000000000,"JWTAuthTimeoutInterval":900000000000,"LogName":"rest-app","PasswordValidation":["^[\\S]{8,32}$","[a-z]","[A-Z]","[!#$%'()*+,-.\\\\/:;=?@\\[\\]^_{|}~]","[0-9]"],"PersistentDirectory":"/home/paulfdunn/go/src/github.com/paulfdunn/rest-app-prod1"}
info: 2021/04/19 22:48:43.545356 authJWT.go:110: Registered handler: /auth/create/
info: 2021/04/19 22:48:43.545469 authJWT.go:113: Registered handler: /auth/delete/
info: 2021/04/19 22:48:43.545532 authJWT.go:116: Registered handler: /auth/info/
info: 2021/04/19 22:48:43.545559 authJWT.go:119: Registered handler: /auth/login/
info: 2021/04/19 22:48:43.545603 authJWT.go:122: Registered handler: /auth/logout/
info: 2021/04/19 22:48:43.545632 authJWT.go:125: Registered handler: /auth/logout-all/
info: 2021/04/19 22:48:43.545659 authJWT.go:128: Registered handler: /auth/refresh/
info: 2021/04/19 22:48:43.813056 rest-app.go:119: Created default auth: admin
info: 2021/04/19 22:48:43.813090 rest-app.go:102: Registered handler: /
info: 2021/04/19 22:48:46.577271 rest-app.go:123: rest-app handler {GET / HTTP/2.0 2 0 map[Accept:[*/*] Authorization:[Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MTg4NzM0MjYsImlzcyI6InJlc3QtYXBwIiwiRW1haWwiOiJhZG1pbiIsIlRva2VuSUQiOiI3MWE3Mzg4NC0xZjYxLTk2NTYtZTU0Yy00ZDAyNzdiMWRlZTcifQ.8tcPh3kLFW7eXDB12snt-FaOC4Q6YFDYAXuaBxBseDs] User-Agent:[curl/7.64.0]] 0xc00006f290 <nil> 0 [] false 127.0.0.1:8080 map[] map[] <nil> map[] 127.0.0.1:59464 / 0xc0000de790 <nil> <nil> 0xc000012440}

paulfdunn@penguin:~/go/src/github.com/paulfdunn/rest-app-prod1$ cat rest-app.log.audit.0 
audit: 2021/04/19 22:48:43.454940 config.go:94: rest-app is starting....
audit: 2021/04/19 22:48:46.477993 handlers.go:50: status: 201| req:&{Method:POST URL:/auth/create/ Proto:HTTP/2.0 ProtoMajor:2 ProtoMinor:0 Header:map[Accept:[*/*] Authorization:[Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MTg4NzM0MjYsImlzcyI6InJlc3QtYXBwIiwiRW1haWwiOiJhZG1pbiIsIlRva2VuSUQiOiI3MWE3Mzg4NC0xZjYxLTk2NTYtZTU0Yy00ZDAyNzdiMWRlZTcifQ.8tcPh3kLFW7eXDB12snt-FaOC4Q6YFDYAXuaBxBseDs] Content-Length:[39] Content-Type:[application/x-www-form-urlencoded] User-Agent:[curl/7.64.0]] Body:0xc00006f170 GetBody:<nil> ContentLength:39 TransferEncoding:[] Close:false Host:127.0.0.1:8080 Form:map[] PostForm:map[] MultipartForm:<nil> Trailer:map[] RemoteAddr:127.0.0.1:59460 RequestURI:/auth/create/ TLS:0xc00031c0b0 Cancel:<nil> Response:<nil> ctx:0xc000012380}| body: body not logged, contains credentials for user|

audit: 2021/04/19 22:48:46.843543 handlers.go:50: status: 204| req:&{Method:DELETE URL:/auth/delete/ Proto:HTTP/2.0 ProtoMajor:2 ProtoMinor:0 Header:map[Accept:[*/*] Authorization:[Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2MTg4NzM0MjYsImlzcyI6InJlc3QtYXBwIiwiRW1haWwiOiJ1c2VyIiwiVG9rZW5JRCI6IjA0NTNmMDczLWVjOTYtMzczNS1jYjdkLWUxZWZhMDlmMDI3ZCJ9.4g5-LBnKqUw9uUis10owsMv-h8uPiCTksA5pNwBjGTY] User-Agent:[curl/7.64.0]] Body:0xc00006fbf0 GetBody:<nil> ContentLength:0 TransferEncoding:[] Close:false Host:127.0.0.1:8080 Form:map[] PostForm:map[] MultipartForm:<nil> Trailer:map[] RemoteAddr:127.0.0.1:59468 RequestURI:/auth/delete/ TLS:0xc000220420 Cancel:<nil> Response:<nil> ctx:0xc000012580}| body: |
```