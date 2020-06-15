# micronaut-book-vue

## Project setup

```
npm install
```

### Compiles and hot-reloads for development

```
npm run serve
```

### Compiles and minifies for production

```
npm run build
```

### Lints and fixes files

```
npm run lint
```

### ローカル開発環準備

- Docker for Kubernetes や minikube などのローカル Kubernetes 環境をインストール
- skaffold インストール
- book-api デプロイ
  ```
  kubectl apply -f kubernetes/book-api
  ```

### 結合開発 dev 環境

webpack DevServer によるホットリロードな開発。初期起動は 5 分ほどかかるかも。

```
$ skaffold dev -p dev --port-forward
```

[http://localhost:8081/](http://localhost:8081/)

### 結合開発 production 環境

本番環境構成での開発。毎回 Docker build が走るため遅い。

```
$ skaffold dev --port-forward
```

[http://localhost:8081/](http://localhost:8081/)

### ローカル開発リセット

- book-api アンデプロイ
  ```
  kubectl delete -f kubernetes/book-api
  ```

# 参考

- [Vuetify 2.0 で datatable を使う - Qiita](https://qiita.com/trustbank_kei/items/45d02313241c8235ad5e)
