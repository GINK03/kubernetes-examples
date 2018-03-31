# kubernetes-examples
kubernetesの例です

## Dockerfileでhello worldする

project idを環境変数にセットする

```console
$ export PROJECT_ID="$(gcloud config get-value project -q)"
```

Dockerfile  

(結構便利)  
```dockerfile
FROM golang:1.8-alpine
ADD . /go/src/hello-app
RUN go install hello-app

FROM alpine:latest
COPY --from=0 /go/bin/hello-app .
ENV PORT 8080
CMD ["./hello-app"]
```

Dockerfileのビルド
```console
$ docker build -t gcr.io/${PROJECT_ID}/hello-app:v1 .
```
