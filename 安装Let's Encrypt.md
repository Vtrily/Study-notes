title：安装Let's Encrypt    
tags:[https,Let's Encrypt]  
date:2017-07-09  

---

1.准备工作  
安装git和python  
2.获取Let's Encrypt免费SSL证书
```
git clone   https://github.com/letsencrypt/letsencrypt  
cd letsencrypt  
./letsencrypt-auto certonly   --standalone --email   it_lss@crayoncool.com -d crayoncool.com -d www.crayoncool.com  
```
等待点击Agree  
3.在完成Let's Encrypt证书的生成之后，我们会在"/etc/letsencrypt/live/crayoncool.com/"域名目录下有4个文件就是生成的密钥证书文件。  
```
cert.pem  - Apache服务器端证书
chain.pem  - Apache根证书和中继证书
fullchain.pem  - Nginx所需要ssl_certificate文件
privkey.pem - 安全证书KEY文件
```
在nginx配置文件中添加  
```
server{
    listen 443 ssl;
    ssl on;
    ssl_certificate /etc/letsencrypt/live/crayoncool.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/crayoncool.com/privkey.pem;
}
```
4.更新证书有效期  
```
./letsencrypt-auto certonly --renew-by-default --email it_lss@crayoncool.com -d crayoncool.com -d www.crayoncool.com  
```
