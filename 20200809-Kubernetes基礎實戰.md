# Kubernetes基礎實戰
安裝參考連結 : [https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/]

## docker 
特性  
1. Immutable 底層，不能改變  
2. Layers 層級  

## K8S 介紹  
幫忙管理container的系統  

### K8S與pod  
pod是K8S的最小單位  

### K8S基本元件  
* Master Componetes  
* Node Components  
* Add-on (外掛)  
Master：
APIserver
scheduler
controllor Manager
etcd：紀錄API所用到的VALUE  

Node只需：kubelet、proxy

### Service與Deployment
* Service
* Replica Set : 早期的方法
* Deployment : 新的方法，可以隨時延伸(scale)容器的數量

*cgroupfs 因為用Docker，所以可以暫時不理會

## 實作  
目錄裡的檔案  
```bash
[root@K8S container]# pwd;ll
/root/container
總計 12
-rw-r--r--. 1 root root 467  8月  9 14:29 app.py
-rw-r--r--. 1 root root 535  8月  9 14:26 Dockerfile
-rw-r--r--. 1 root root   6  8月  9 14:28 requirements.txt
```

Dockerfile
```bash
# Use an official Python runtime as a parent image
FROM python:3.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt

RUN pip install --trusted-host files.pythonhosted.org -r requirements.txt

# Make port 80 available to the world outside this container

EXPOSE 80

# Define environment variable

ENV NAME=World VERSION=v1

# Run app.py when the container launches

CMD ["python", "app.py"]
```

requirement.txt
```bash
Flask
```

``` python
from flask import Flask
import os
import socket

app = Flask(__name__)

@app.route("/")
def hello():

    html = "<h3>Hello {name}!</h3>" \
        "<font color='{color}'>Version: {version}</font><br/>"\
        "<b>Hostname:</b> {hostname}<br/>"
    return html.format(name=os.getenv("NAME", "world"), color=os.getenv("COLOR", "red"),version=os.getenv("VERSION", "v0"), hostname=socket.gethostname())

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

Build
```bash
[root@K8S container]# docker build --tag=friendlyhello .
Sending build context to Docker daemon  4.608kB
Step 1/7 : FROM python:3.7-slim
3.7-slim: Pulling from library/python
bf5952930446: Pull complete
385bb58d08e6: Pull complete
96908cd499d2: Pull complete
31e581fc14b2: Pull complete
73c2a1489f22: Pull complete
Digest: sha256:d37be9a444493e0a542a6d6d7465ac12f485b3c5bb4e40c69be72adfe3d8db6a
Status: Downloaded newer image for python:3.7-slim
 ---> f6d88f1a6438
Step 2/7 : WORKDIR /app
 ---> Running in efbf4504a436
Removing intermediate container efbf4504a436
 ---> aeba3764729f
Step 3/7 : COPY . /app
 ---> e3e911615ccc
Step 4/7 : RUN pip install --trusted-host files.pythonhosted.org -r requirements.txt
 ---> Running in 59de786c5961
Collecting Flask
  Downloading Flask-1.1.2-py2.py3-none-any.whl (94 kB)
Collecting Werkzeug>=0.15
  Downloading Werkzeug-1.0.1-py2.py3-none-any.whl (298 kB)
Collecting Jinja2>=2.10.1
  Downloading Jinja2-2.11.2-py2.py3-none-any.whl (125 kB)
Collecting itsdangerous>=0.24
  Downloading itsdangerous-1.1.0-py2.py3-none-any.whl (16 kB)
Collecting click>=5.1
  Downloading click-7.1.2-py2.py3-none-any.whl (82 kB)
Collecting MarkupSafe>=0.23
  Downloading MarkupSafe-1.1.1-cp37-cp37m-manylinux1_x86_64.whl (27 kB)
Installing collected packages: Werkzeug, MarkupSafe, Jinja2, itsdangerous, click, Flask
Successfully installed Flask-1.1.2 Jinja2-2.11.2 MarkupSafe-1.1.1 Werkzeug-1.0.1 click-7.1.2 itsdangerous-1.1.0
Removing intermediate container 59de786c5961
 ---> a0ef29b76ad1
Step 5/7 : EXPOSE 80
 ---> Running in ce452893dfaa
Removing intermediate container ce452893dfaa
 ---> 2eaf61370a3d
Step 6/7 : ENV NAME=World VERSION=v1
 ---> Running in 52e6d90fecc1
Removing intermediate container 52e6d90fecc1
 ---> 402bc8f9f380
Step 7/7 : CMD ["python", "app.py"]
 ---> Running in 3bc4d608584e
Removing intermediate container 3bc4d608584e
 ---> 5efca2da3db2
Successfully built 5efca2da3db2
Successfully tagged friendlyhello:latest
```

開啟4000的port
```docker run --name=friendlyhello -d -p 4000:80 friendlyhello```

## LABLE標籤
用來搜尋的重要關鍵字  

搜尋pod的label  
```bash
[root@K8S demo]# kubectl get pods --show-labels
NAME                READY   STATUS    RESTARTS   AGE   LABELS
friendlyhello-pod   1/1     Running   0          57s   app=friendlyhello,version=v3
```

相關指令
```bash
* Query labels
kubectl get pods --show-labels

* Update labels
kubectl label --overwrite pods friendlyhello-pod version=v0

* Add labels
kubectl label pods friendlyhello-pod stage=test

* Remove labels
kubectl label pods friendlyhello-pod stage-

* Select labels
kubectl get pods -l app=friendlyhello,version=v0
```

## Deployment
desired state : 預期要求的成果
常用
roll out
roll back

Deployment yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: friendlyhello-deployment
 labels:
  app: friendlyhello
spec:
 replicas: 3
 selector:
  matchLabels:
   app: friendlyhello
 template:
  metadata:
   labels:
    app: friendlyhello
    run: deployment
  spec:
   containers:
   - name: friendlyhello
     image: kennychennetman/friendlyhello:v3
     command: ['python', 'app.py']
     ports:
     - containerPort: 80
```


## kubectl completion  
```bash
echo 'source <(kubectl completion bash)' >>~/.bashrc
. ~/.bashrc
```  

安裝 metrics service  
```bash
https://github.com/kubernetes-sigs/metrics-server
```  

## 負載平衡
* LoadBalancer
* Ingress 
### 將 type 修改為 LoadBalancer  
master與node之間的必須要先做好  
網址 : https://metallb.universe.tf/  

## ConfigMaps



重新生成憑證  
* kubeadm init phase upload-certs --upload-certs  


## 參考文獻  
Netman簡報：https://drive.google.com/file/d/1Emfi50HvDV6SnNTFVsw-G_-AVGjiJz6O/view  

