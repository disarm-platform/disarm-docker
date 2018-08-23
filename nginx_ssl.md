Install Nginx first.
```
sudo apt install nginx
```

Setup a configuration block ([How To Install Nginx on Ubuntu 18.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04#step-5-%E2%80%93-setting-up-server-blocks-recommended))

Install certbot
```
sudo add-apt-repository ppa:certbot/certbot
[ENTER]
sudo apt install python-certbot-nginx

```

Request certificates
```
sudo certbot --nginx -d douma.nicolai.io -d douma-api.nicolai.io
```

Test auto renewal is setup
```
sudo certbot renew --dry-run
```
