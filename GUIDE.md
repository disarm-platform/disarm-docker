# Setup guide for standalone DOUMA

## Requirements

- A server running Debian, Ubuntu or MacOS
- Basic command line knowledge


## Guide

**Install MongoDB**

Add the public keys
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
```

Create source list file
```
echo "deb http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
```

Update and install
```
sudo apt-get update
sudo apt-get install -y mongodb-org
```

Setup Mongo as a service
```
systemctl start mongod
systemctl enable mongod
```

**Create a mongo user**
```
# try this line if you get an error when using mongo
export LC_ALL=C

# login
mongo
```

Now in the mongo shell
```
use admin
db.createUser({user:"admin", pwd:"admin123", roles:[{role:"root", db:"admin"}]})
```

You should change the password to something secret.

Edit the file `/lib/systemd/system/mongod.service`

```
nano /lib/systemd/system/mongod.service
```

Add  `--auth` to the line:

```
ExecStart=/usr/bin/mongod --auth --config /etc/mongod.conf
```

Restart mongo

```
systemctl daemon-reload
sudo service mongod restart
```

Test that you can login:

```
mongo -u admin -p admin123 --authenticationDatabase admin
```

**Create a Google Sheets for users**

DOUMA-API needs a csv file of users. The easiest is to create a public Google Sheets with the following columns:

```
_id	name	password	username	read	write	instance_slug
```

Add a user:

```
_id: user1	
name: User	
password: m	
username: js	
read: irs_monitor, irs_record_point, irs_plan, irs_tasker, structure_recorder, data_wizard, debug, unity_dashboard, foci, config, seasons	
write: irs_monitor, irs_record_point, irs_plan, irs_tasker, structure_recorder, data_wizard, debug, unity_dashboard, foci, config, seasons	
instance_slug: all
```

Publish the doc

![](imgs/sheets_1.png)

Select csv as the format and keep hold of the url.

![](imgs/sheets_2.png)

**Local file for users**

Alternatively create a local file on the server with the same layout as described above.


**Install DOUMA-API**

First clone the repo:

TODO: Update url
```
git clone https://gitlab.com/disarm/douma.api douma-api
```

Install Node
```
# Adding the NodeSource APT repository for Debian-based distributions repository AND the PGP key for verifying packages
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -

# Install Node.js from the Debian-based distributions repository
sudo apt-get install -y nodejs
```

Install dependencies

```
cd douma-api
npm i 
```

Set environment variables:
```

export PORT="5000"

# Set secret for api_key generation
export SECRET="some_secret_can_by_anything"

# Insert username and password from mongo setup
export MONGODB_URI="mongodb://user:password@localhost/douma"

# Set SHEETS_URL for remote csv
export SHEETS_URL="https:https://docs.google.com/spreadsheets......"

# Or set SHEETS_PATH for remote csv
export SHEETS_PATH="users.cvs"
```


Lastly start the server

```
npm run start
```

**Setup Caddy**

Last step is setting up Caddy so we get free HTTPS. 


Download Caddy Server from: https://caddyserver.com/

Create a `Caddyfile` and add your domain.

```
api.domain.com {
  proxy /  :5000
}
```


Start caddy to finish the setup of DOUMA-API
```
./caddy
```

Test the setup by going to the domain, you should receive a reponse like this: 

```
{
  DOUMA_API: "DEV",
  route: "root"
}
```

**Setup DOUMA-APP**

Clone the repo: 

```
git clone https://gitlab.com/disarm/douma.app douma-app
```

Install dependencies

```
cd douma-app
npm i
```

Build the app

```
npm run build
```

Add domain to Caddyfile

```
...

app.domain.com {
  header /service-worker.js Cache-Control "max-age=0"
  header /VERSION Cache-Control "max-age=0"

  root douma-app/serve
}
```

Restart Caddy

```
./caddy
```

The setup is now done. All that is left is to add a configuration and access that instance.