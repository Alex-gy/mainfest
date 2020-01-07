#### some traefik2.1 yaml mainfest


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

