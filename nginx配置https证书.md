
# 为nginx添加https证书

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
0 0 * * * * /路径/certbot-auto renew
```
