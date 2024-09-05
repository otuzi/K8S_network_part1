# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 1»

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Описание](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) Deployment и примеры манифестов.
2. [Описание](https://kubernetes.io/docs/concepts/services-networking/service/) Описание Service.
3. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.

------

### Задание 1. Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера

1. Создать Deployment приложения, состоящего из двух контейнеров (nginx и multitool), с количеством реплик 3 шт.
2. Создать Service, который обеспечит доступ внутри кластера до контейнеров приложения из п.1 по порту 9001 — nginx 80, по 9002 — multitool 8080.
3. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложения из п.1 по разным портам в разные контейнеры.
4. Продемонстрировать доступ с помощью `curl` по доменному имени сервиса.
5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

### Ответ

Deployment: https://github.com/otuzi/K8S_network_part1/blob/main/deployment_network.yaml

Service: https://github.com/otuzi/K8S_network_part1/blob/main/service_network.yaml

Вывод из комнадной строки:

```
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl apply -f deployment_network.yaml 
deployment.apps/multi-container-deployment created
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get pod
NAME                                          READY   STATUS              RESTARTS   AGE
multi-container-deployment-755c6d9fb4-lsxxh   0/2     ContainerCreating   0          4s
multi-container-deployment-755c6d9fb4-q6dlv   0/2     ContainerCreating   0          4s
multi-container-deployment-755c6d9fb4-wfh67   0/2     ContainerCreating   0          4s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get pod
NAME                                          READY   STATUS    RESTARTS   AGE
multi-container-deployment-755c6d9fb4-lsxxh   2/2     Running   0          6s
multi-container-deployment-755c6d9fb4-q6dlv   2/2     Running   0          6s
multi-container-deployment-755c6d9fb4-wfh67   2/2     Running   0          6s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get pod
NAME                                          READY   STATUS    RESTARTS   AGE
multi-container-deployment-755c6d9fb4-lsxxh   2/2     Running   0          8s
multi-container-deployment-755c6d9fb4-q6dlv   2/2     Running   0          8s
multi-container-deployment-755c6d9fb4-wfh67   2/2     Running   0          8s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get pod
NAME                                          READY   STATUS    RESTARTS   AGE
multi-container-deployment-755c6d9fb4-lsxxh   2/2     Running   0          9s
multi-container-deployment-755c6d9fb4-q6dlv   2/2     Running   0          9s
multi-container-deployment-755c6d9fb4-wfh67   2/2     Running   0          9s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl apply -f service.yaml
error: the path "service.yaml" does not exist
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl apply -f service_network.yaml 
service/multi-container-service created
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get pod
NAME                                          READY   STATUS    RESTARTS   AGE
multi-container-deployment-755c6d9fb4-lsxxh   2/2     Running   0          68s
multi-container-deployment-755c6d9fb4-q6dlv   2/2     Running   0          68s
multi-container-deployment-755c6d9fb4-wfh67   2/2     Running   0          68s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get svc
NAME                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
kubernetes                ClusterIP   10.152.183.1     <none>        443/TCP             33m
multi-container-service   ClusterIP   10.152.183.248   <none>        9001/TCP,9002/TCP   8s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get svc
NAME                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
kubernetes                ClusterIP   10.152.183.1     <none>        443/TCP             33m
multi-container-service   ClusterIP   10.152.183.248   <none>        9001/TCP,9002/TCP   12s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl run mymultitool --image=praqma/network-multitool:latest -i --tty --rm -- sh
If you don't see a command prompt, try pressing enter.
/ # 
/ # curl multi-container-service:9001
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
/ # 
/ # 
/ # 
/ # curl multi-container-service:9002
Praqma Network MultiTool (with NGINX) - multi-container-deployment-755c6d9fb4-q6dlv - 10.1.85.200 - HTTP: 8080 , HTTPS: 443
<br>
<hr>
<br>

<h1>05 Jan 2022 - Press-release: `Praqma/Network-Multitool` is now `wbitt/Network-Multitool`</h1>

<h2>Important note about name/org change:</h2>
<p>
Few years ago, I created this tool with Henrik Høegh, as `praqma/network-multitool`. Praqma was bought by another company, and now the "Praqma" brand is being dismantled. This means the network-multitool's git and docker repositories must go. Since, I was the one maintaining the docker image for all these years, it was decided by the current representatives of the company to hand it over to me so I can continue maintaining it. So, apart from a small change in the repository name, nothing has changed.<br>
</p>
<p>
The existing/old/previous container image `praqma/network-multitool` will continue to work and will remain available for **"some time"** - may be for a couple of months - not sure though. 
</p>
<p>
- Kamran Azeem <kamranazeem@gmail.com> <a href=https://github.com/KamranAzeem>https://github.com/KamranAzeem</a>
</p>

<h2>Some important URLs:</h2>

<ul>
  <li>The new official github repository for this tool is: <a href=https://github.com/wbitt/Network-MultiTool>https://github.com/wbitt/Network-MultiTool</a></li>

  <li>The docker repository to pull this image is now: <a href=https://hub.docker.com/r/wbitt/network-multitool>https://hub.docker.com/r/wbitt/network-multitool</a></li>
</ul>

<br>
Or:
<br>

<pre>
  <code>
  docker pull wbitt/network-multitool
  </code>
</pre>


<hr>

/ # 
/ # 
/ # 
/ # client_loop: send disconnect: Broken pipe

ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
```

------

### Задание 2. Создать Service и обеспечить доступ к приложениям снаружи кластера

1. Создать отдельный Service приложения из Задания 1 с возможностью доступа снаружи кластера к nginx, используя тип NodePort.
2. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.
3. Предоставить манифест и Service в решении, а также скриншоты или вывод команды п.2.

### Ответ

Service: https://github.com/otuzi/K8S_network_part1/blob/main/service_network_nodeport.yaml

Вывод из командной строки VM Linux:
```
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl apply -f service_network_nodeport.yaml 
service/nginx-nodeport-service created
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get svc
NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes               ClusterIP   10.152.183.1     <none>        443/TCP        44m
nginx-nodeport-service   NodePort    10.152.183.158   <none>        80:30001/TCP   6s
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ kubectl get nodes -o wide
NAME                   STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
fhmfa44nbnh1o8tinisv   Ready    <none>   45m   v1.30.4   10.0.1.31     <none>        Ubuntu 20.04.6 LTS   5.4.0-193-generic   containerd://1.6.28
ubuntu@fhmfa44nbnh1o8tinisv:~/k8s$ 
```

Так как у меня VM запущена в облаке Yandex, то в выводе виден внутренний адрес VM к котороой привязан белый IP 89.169.151.90

Вывод из браузера: 

<img width="1547" alt="Screenshot 2024-09-05 at 23 09 23" src="https://github.com/user-attachments/assets/2123035c-e2d3-4a58-9c0a-dfa35bfa144e">

------
