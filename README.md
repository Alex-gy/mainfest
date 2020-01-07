some traefik2.1 yaml mainfest


增加https访问
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=jianyu.ucloud.cn"

```
