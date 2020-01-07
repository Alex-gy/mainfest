### traefik2.1 ingress学习文档

#### 1. 创建crd资源
#### 2. 绑定rbac权限
#### 3. 部署traefik ingress
#### 4. 增加traefik 管理后台路由规则
#### 5. 暴露traefik ingress服务


#### 增加https访问
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
