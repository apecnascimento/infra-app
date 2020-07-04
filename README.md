# Infra-app
![Docker Image CI](https://github.com/apecnascimento/infra-app/workflows/Docker%20Image%20CI/badge.svg?branch=master)

It contains an HTTP server implementation. It responds to all HTTP requests to the URI paths `/app` and `/healthz`.

## Basic instructions

To run in development, just run:

```sh
PORT=5000 go run main.go
```

To build, just run:

```sh
go build -o app
PORT=5000 ./app
```

By using the GOOS and GOARCH environment variables, you can control which OS and architecture your final binary is built fo.

```sh
GOOS=linux GOARCH=amd64 go build -o app

# to check the type of file
file app
app: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, Go BuildID=VlCWiOY1myXoArwKJ8-P/gL-oXKeuH4tOr4nCvhNv/6WUnDAZ95hnz49f7CeAV/Lg9OfRg0c1768RSFbAi4, not stripped
```

## How to use

```sh
$ curl -i http://127.0.0.1:5000/app
HTTP/1.1 200 OK
Content-Type: application/json
Date: Fri, 26 Jun 2020 20:14:57 GMT
Content-Length: 95

{
  "app": "Infra Go App",
  "hostname": "ebc919dc7272",
  "version": "0.0.2"
}
```

```sh
$ curl -i http://127.0.0.1:5000/healthz
HTTP/1.1 200 OK
Date: Fri, 26 Jun 2020 20:14:53 GMT
Content-Length: 0
```
## How to deploy to k8s
K8S version 1.18

```sh
$ kubectl apply -f k8s/namespace.yml
```
```sh
$ kubectl apply -f k8s/infra-app
```

## How to check the app by node port
```sh
# check node port number
$ kubectl get service infra-app-service -n stage
NAME                TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
infra-app-service   NodePort   10.43.93.109   <none>        80:{{node_port}}/TCP   5m23s
```
```sh
# make a curl request
$ curl {{your_cluster_ip}}:{{node_port}}/app
HTTP/1.1 200 OK
Content-Type: application/json
Date: Sat, 04 Jul 2020 17:16:59 GMT
Content-Length: 94

{
  "app": "Infra Go App",
  "hostname": "infra-app-5b95f5859f-vn8rs",
  "version": "0.0.2"
}
```
```sh
$ curl -i {{your_cluster_ip}}:{{node_port}}/healthz
HTTP/1.1 200 OK
Date: Sat, 04 Jul 2020 17:20:28 GMT
Content-Length: 0

```
## How to check the app by ingress
```sh
# Add this line to you hosts file
your_cluster_ip       infraapp.stage
```
```sh
# make a curl request
$ curl -i infraapp.stage/app    
HTTP/1.1 200 OK
Content-Length: 94
Content-Type: application/json
Date: Sat, 04 Jul 2020 17:32:01 GMT
Vary: Accept-Encoding

{
  "app": "Infra Go App",
  "hostname": "infra-app-5b95f5859f-ljkzm",
  "version": "0.0.2"
}
```
```sh
$ curl -i infraapp.stage/healthz 
HTTP/1.1 200 OK
Content-Length: 0
Date: Sat, 04 Jul 2020 17:31:18 GMT
Vary: Accept-Encoding
```

## How to remove the app from your cluster
```sh
$ kubectl delete ns stage
```

