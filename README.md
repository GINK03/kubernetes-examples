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

グローバルIPの確認（数分要する）  
```console
$ kubectl get service
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
hello-web    LoadBalancer   10.3.241.118   35.194.169.208   80:30517/TCP   1m
kubernetes   ClusterIP      10.3.240.1     <none>           443/TCP        5m
```
