## Day Extra - 到底是 k8s run Docker 還是 Docker run k8s

昨天玩了一下 `Docker` 的新版，覺得應該來跟大家分享一下，因此有了今天這篇番外篇。而關於標題的答案不用再糾結了！ `k8s` 預設的容器即是 `Docker` 而在 `Docker Edge` 最新版目前也可以直接運行 `k8s`。

### Stable vs Edge

`Docker` 會發布兩種不同的版本，分別是 `Stable` 與 `Edge`。根據 [官方說明](https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac)：

* `Stable 版本`：每一季會發布一次，屬於經過完整測試後的穩定版本，建議使用在正式環境中。
* `Edge 版本`：每個月會發布一次，最新最潮的功能都在這個版本，當然，太刺激的功能難免會有些不穩定，不過還是很值得一試的。
* 同一時間只可以安裝其中一個版本 (`Stable` 或 `Edge`)，如果已經有安裝過，請先移除後再安裝新版本。

今天要推薦給大家使用的，即是 `Edge` 版本上的新功能 `Kubernetes`。我們可以在安裝完 `Docker` 之後直接使用 `Kubernetes`，不需要再安裝 `minikube` 模擬 `k8s` 叢集。

### 安裝 Docker Edge

我們用 Mac 當作示範，首先[下載 Docker Edge](https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac)

![https://ithelp.ithome.com.tw/upload/images/20180121/20107062g6NL7BRXpx.png](https://ithelp.ithome.com.tw/upload/images/20180121/20107062g6NL7BRXpx.png)

下載完後，就按照步驟安裝 `Docker`，然後執行，然後你就會看到上面的小鯨魚：

![https://ithelp.ithome.com.tw/upload/images/20180121/20107062MPnqUl3zZ5.png](https://ithelp.ithome.com.tw/upload/images/20180121/20107062MPnqUl3zZ5.png)

接下來，當然是 `Enable Kubernetes`，它會自動幫你安裝 `k8s` 需要的檔案，完成後你就會在右下角看到 `Kubernetes is running` 的提示，就大功告成拉！

![https://ithelp.ithome.com.tw/upload/images/20180121/20107062zFCqSHkZEg.png](https://ithelp.ithome.com.tw/upload/images/20180121/20107062zFCqSHkZEg.png)

你也可以利用指令觀察 Client 端的 `Orchestrator`

```bash
$ docker version
Client:
 Version:	18.01.0-ce
 API version:	1.35
 Go version:	go1.9.2
 Git commit:	03596f5
 Built:	Wed Jan 10 20:05:58 2018
 OS/Arch:	darwin/amd64
 Experimental:	true
 Orchestrator:	kubernetes   <=== 這裡 Orchestrator 已經變成 Kubernetes

Server:
 Engine:
  Version:	18.01.0-ce
  API version:	1.35 (minimum version 1.12)
  Go version:	go1.9.2
  Git commit:	03596f5
  Built:	Wed Jan 10 20:13:12 2018
  OS/Arch:	linux/amd64
  Experimental:	true
```


### 使用 Docker Kubernetes

那麼該如何使用呢？就照著 [Day 8 - 與 k8s 溝通: kubectl](https://ithelp.ithome.com.tw/articles/10192193) 所提到的，先查看一下

```bash
$ kubectl config get-contexts
...
CURRENT   NAME                CLUSTER                     AUTHINFO        NAMESPACE
          minikube            minikube                    minikube                    
*         docker-for-desktop  docker-for-desktop-cluster  docker-for-desktop
<=== 這裡會多一個 docker-for-desktop 的設定
```

這裡你會看到多一個 `docker-for-desktop` 的設定，接著就切換到 `docker-for-desktop`

```bash
$ kubectl config use-context docker-for-desktop
Switched to context "docker-for-desktop".

$ kubectl get namespaces
NAME          STATUS    AGE
default       Active    5m
docker        Active    5m
kube-public   Active    5m
kube-system   Active    5m
```

到這裡，相信大家都知道要怎麼使用了吧！立馬部署一個 `Deployment` 試試

```bash

$ kubectl apply -f simple.yaml  <=== simple.yaml 在 Day 10 - 建構組件內
$ kubectl get pods
NAME                    READY     STATUS    RESTARTS   AGE
nginx-75f4785b7-25pvr   1/1       Running   0          1m
nginx-75f4785b7-pw4ln   1/1       Running   0          1m
nginx-75f4785b7-s9vrk   1/1       Running   0          1m
```

關於其他操作，你可以[參考這裡](https://docs.docker.com/docker-for-mac/#kubernetes)

