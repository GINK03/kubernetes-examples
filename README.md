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

GCPにアップロード
```console
$ gcloud docker -- push gcr.io/${PROJECT_ID}/hello-app:v1
```

Dockerfileをローカルで実行
```console
$ docker run --rm -p 8080:8080 gcr.io/${PROJECT_ID}/hello-app:v1
$ curl http://localhost:8080
```

Kubernetesのクラスタを立ち上げ（デプロイはできていない）
```console
$ gcloud container clusters create hello-cluster --num-nodes=3
```
KuberctlでDocker Imageをデプロイする
```console
$ kubectl run hello-web --image=gcr.io/${PROJECT_ID}/hello-app:v1 --port 8080
```

グローバルIPを与えて公開する
```console
$ kubectl expose deployment hello-web --type=LoadBalancer --port 80 --target-port 8080
```
