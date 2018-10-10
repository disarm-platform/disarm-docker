# Standalone
Self-host DOUMA

# Getting started

Before you start you must have two domains or one domain with two subdomains pointing to your server. We will be using `app.disarm.io` for the app and and `api.disarm.io` for the api. 

Install docker and docker-compose.

Set `MONGODB_URI` to a valid MongoDB url.
Set `SHEETS_URL` to a csv file formatted for douma-api usage.
Set `APP_URL` to the url for the app. I.e. `app.disarm.io`.
Set `API_URL` to the url for the api. I.e. `api.disarm.io`.
Set `SSL_EMAIL` to a valid email address. The email is used for Let's Encrypt.

Run with something like `SSL_EMAIL=js@ppls.io APP_URL=app.example.com API_URL=api.example.com docker-compose up`. 

Make sure firewall is open on ports 80 and 443 (80 for LetsEncrypt, 443 for serving).

You can now access douma-app on `app.example.com` and douma-api on `api.example.cm `.
