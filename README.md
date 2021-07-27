# Two Tier App Deployment - AWS

## Diagram
![img.png](img.png)

## Copying app to EC2 instance
- Navigate to the directory with the vagrant file in GitBash
- Run the following line `scp -i ~/.ssh/eng89_devops.pem -r app/ ubuntu@ec2-34-248-12-151.eu-west-1.compute.amazonaws.com:~/app/`
- This follows the convention `scp -i ~/.ssh/KEYPAIR -r DIRECTORYNAME/ ubuntu@EC2_INSTANCE_IP:~/LOCATION_TO_COPY_TO/`

## Creating a new instance
- Click launch instance and choose ubuntu server 16.04 (64-bit(x86))
- Choose the default instance type
- Choose subnet `DevOpsStudent default 1a` and `auto-assign public ip` to enable
- Keep default storage settings
- Add tags following naming convention `key = Name` and `value = eng89_saim_type`
- Security group for individual access `eng89_saim_SG_type`, `ssh`, `port = 22`, `MyIP` 
- Security group for group access `HTTP`, `port = 80`, `source = anywhere`
- Launch instance and choose key pair in this case `eng89_devops`
- Connect to instance, navigate to `SSH client` and copy `chmod...`
- Open GitBash, navigate to `.ssh` directory and paste the `chmod...`
- Copy `ssh -i ...` and paste it into the same directory to enter VM 

## Security group setup
### Connecting the app to the db
![img_1.png](img_1.png)
### Connecting the db to the app
![img_2.png](img_2.png)
## App instance setup
### SSH into the app instance through the .ssh directory and follow the steps below
- Update VM `sudo apt-get update -y`
- Upgrade VM `sudo apt-get upgrade -y`
- Install nginx `sudo apt-get install nginx -y`
- Install nodejs `sudo apt install node.js -y` and `curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -` and `sudo apt install nodejs -y`
- Install pm2 `sudo npm install pm2 -y -g`
- Check nginx status `systemctl status nginx`
### Reverse proxy setup
- Navigate to `/etc/nginx/sites-available`
- Delete default file `sudo rm -rf default`
- Remake default file `sudo echo default` remember to change proxy_pass ip
```
server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://PUBLIC_APP_IP:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
- Check nginx config `sudo nginx -t`
- Restart nginx `sudo systemctl restart nginx`
- Run `npm start` in app directory
### Set env variable
- Run the following line in the main directory and remember the database public IP `sudo echo export DB_HOST="mongodb://DATBASE_PUBLIC_IP:27017/posts" >> ~/.bashrc`
- Run the source file `source ~/.bashrc`
- Check if env variable is present using `env`
## DB instance setup


