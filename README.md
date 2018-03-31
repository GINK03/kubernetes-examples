# kubernetes-examples
kubernetesの例です

## Dockerfileでhello worldする

project idを環境変数にセットする

```console
$ export PROJECT_ID="$(gcloud config get-value project -q)"
```

Dockerfileのビルド
```console
$ docker build -t gcr.io/${PROJECT_ID}/hello-app:v1 .
```
