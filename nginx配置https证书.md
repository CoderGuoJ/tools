
# 使用letsencrypt注册https证书

1. 下载certbot-auto
```
wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
```
2. 配置nginx指向webroot目录
```
location /.well-known {
    root /var/www/html;
}
```
3. 创建证书
```
./certbot-auto certonly --email 邮箱 --agree-tos --no-eff-email --webroot -w /var/www/html -d 域名 
# email 证书有效期30天，不足10天时会邮件提醒
# webroot webroot校验方式指定目录，需要通过   域名[:端口]/.well-known  的方式验证
```
4. 添加定时任务，用于自动更新证书，避免过期
```
0 0 * * * /路径/certbot-auto renew
```
5. 查看本机上申请的证书
```
certbot-auto certificates
```
6. 注销证书
```
certbot-auto revoke --cert-path cert.pem文件路径 --reason superseded 
```
7. 证书认证方式
```
    1. standalone 模式
        本模式需要占用80或443端口(可以配置)来启动一个服务完成与证书服务的认证工作
    2. webroot 模式
        本模式不需要占用现有端口，只需将80端口添加一个/.well-known静态资源路径即可，letsencrypt 会在该路径下生成一个随机文件共认证服务访问，该路径必须可以被认证服务器访问到才可以完成认证
    3. manual 模式
        与webroot模式类似，只不过需要手动生成随机文件
```
        
