## traefik2.1 ingress学习文档
### 0.实现目标
```
LoadBalancer ---> jianyu.traefik.com  访问traefik后台
LoadBalancer ---> jianyu.ucloud.com   访问部署的web测试服务
LoadBalancer ---> mongo.ucloud.com    访问部署的mongo服务
```
### 1. 创建crd资源
### 2. 绑定rbac权限
### 3. 部署traefik ingress
### 4. 增加traefik 管理后台路由规则
### 5. 暴露traefik ingress服务
```
➜  .kube ku get service -n kube-system 
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                      AGE
csi-udisk-controller      ClusterIP      10.2.118.46    <none>         12345/TCP                                    3h45m
csi-ufile-controller      ClusterIP      10.2.229.44    <none>         12345/TCP                                    3h45m
kube-dns                  ClusterIP      10.2.0.2       <none>         53/UDP,53/TCP,9153/TCP                       3h45m
metrics-server            ClusterIP      10.2.213.16    <none>         443/TCP                                      3h45m
traefik-ingress-service   LoadBalancer   10.2.239.177   106.75.6.224   80:46863/TCP,443:36351/TCP,27017:49441/TCP   3m9s

修改本地hosts文件 添加 jianyu.traefik.com  106.75.6.224
```


### 6. 部署whoami应用并添加http路由
### 7. 在whoami应用上增加https路由

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=jianyu.ucloud.cn"

```
#### 通过 Secret 对象来引用证书文件：

```
# 要注意证书文件名称必须是 tls.crt 和 tls.key
$ kubectl create secret tls who-tls --cert=tls.crt --key=tls.key
secret/who-tls created
```
#### 创建一个 HTTPS 访问应用的 IngressRoute 对象

```
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: ingressroutetls
spec:
  entryPoints:
    - websecure
  routes:
  - match: Host(`jianyu.ucloud.cn`) && PathPrefix(`/tls`)
    kind: Rule
    services:
    - name: whoami
      port: 80
  tls:
    secretName: who-tls

```
### 8.测试TCP应用
