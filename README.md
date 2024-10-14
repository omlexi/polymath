# Polymath

A rewrite of [oraxen/polymath](https://github.com/oraxen/polymath) in typescript.

## How to use

### Using Pterodactyl

- Download and Install [Eggs](https://github.com/Arknesia/Polymath/blob/master/egg-polymath.json) to your Pterodactyl Panel.
- Create a new server using port `8181` since 8080 used for Pterodactyl Panel.
- Setup Nginx as below:

  ```
  server {
      listen 80;
      server_name atlas.domain.com;

      location / {
          proxy_pass http://0.0.0.0:8181;
          proxy_set_header X-Real-IP $remote_addr;
          client_max_body_size 16M;
      }
  }

  server {
      listen 443 ssl;
      server_name atlas.domain.com;

      ssl_certificate /etc/letsencrypt/live/atlas.domain.com/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/atlas.domain.com/privkey.pem;

      location / {
          proxy_pass http://0.0.0.0:8181;
          proxy_set_header X-Real-IP $remote_addr;
          client_max_body_size 16M;
      }
  }
  ```

  Remember to [Creating SSL Certificates](https://pterodactyl.io/tutorials/creating_ssl_certificates.html).

### Using Public Atlas

Edit file `Oraxen/settings.yml`

```
Pack:
  upload:
    enabled: true
    type: polymath
    polymath:
      server: atlas.arknesia.com
      secret: ChangeThisUsingYourUniqueId
```

---

## Polymath Setup Tutorial for Minecraft Server (Pterodactyl + NGINX)

### 1. Download the `egg.json`
Download the `egg.json` file from the following GitHub repository:  
[Polymath Egg JSON](https://github.com/Arknesia/Polymath/blob/master/egg-polymath.json)


### 2. Import the Egg into Pterodactyl
Navigate to:  
**Service Management** -> **Nests** -> **Import Egg** -> Upload the `egg.json` file from Step 1.


### 3. Verify the Polymath Egg
Go to the **Minecraft Nests** and check if a new Egg called **Polymath** was added successfully.


### 4. Add Port 8181 to Your Node
Ensure Port `8181` is available:
1. Go to **Management** -> **Nodes** -> `<Your Node>` -> **Allocation**.
2. Under **Assign New Allocations**, add **Port 8181**.


### 5. Create a New Server
Create a new server as you would normally in Pterodactyl:
- Select **Port 8181** as the game port.
- Choose the **Polymath Egg** added in Step 3.

### 6. Start Your Server
Start the newly created server. It should automatically download the necessary dependencies.

Now would be a good time to consider creating a new A record, such as `texture.domain.com`. 
You don’t need to use this exact name—it's just a placeholder, so feel free to choose something that fits your setup.

#### Troubleshooting Hint:
If the server hangs while downloading after all dependencies were successfully downloaded, this could be due to a configuration issue. For example, running the `config.ts` file in the `src` folder might help generate the necessary `config.json` file. Also check the config.json if the right url is set for example: 
```json
"url": "https://texture.domain.com"
```


### 7. Verify the Server is Running
After the server starts successfully, you should see a message like:
`Server is running on port 8181`
If you do not see this message, check for errors and follow the steps further in this tutorial.


### 8. Set Up NGINX Proxy
SSH into your server where Pterodactyl is hosted.

#### Create a New NGINX Configuration File:
Navigate to:
```bash
cd /etc/nginx/sites-available
```
Create a new file using `nano` or `vim`. 
Example command:
```bash 
sudo nano polymath.conf
```
Add the following content to the file:
```
server {
    listen 80;
    server_name texture.domain.com;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name texture.domain.com;

    ssl_certificate /etc/letsencrypt/live/texture.domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/texture.domain.com/privkey.pem;

    location / {
        proxy_pass http://0.0.0.0:8181;
        proxy_set_header X-Real-IP $remote_addr;
        client_max_body_size 16M;
    }
}

```
#### Troubleshooting Hints:
- **HTTPS Redirection**: The configuration above redirects HTTP traffic to HTTPS. Make sure to enable SSL certificates using Certbot. After you created a cert for your domain change the ssl_certificate & ssl_certificate_key above in the .conf file. Find more about Certbot here:  [Certbot SSL Tutorial](https://pterodactyl.io/tutorials/creating_ssl_certificates.html)

-   **proxy_pass IP Address**: If `0.0.0.0` doesn’t work, use the IP that the Polymath server is bound to. Check this by running: 
    ```bash 
    sudo netstat -tuln | grep 8181
    ```
    Use the IP address that shows up before `:8181`, for     example, `proxy_pass http://10.0.0.83:8181;`
    
- **Firewall**: Ensure Port `8181` is not blocked by the firewall. If it is, add it to the firewall using: 
   ```bash
  sudo firewall-cmd --permanent --add port=8181/tcp
  ```

### 9. Enable the NGINX Configuration
After saving `polymath.conf`, create a symbolic link to enable the site:
```bash
sudo ln -s /etc/nginx/sites-available/polymath.conf /etc/nginx/sites-enabled/
```

### 10. Restart NGINX and Wings

Restart NGINX to apply the configuration:
```bash
sudo systemctl restart nginx
``` 
You may also want to restart the Pterodactyl wings service:
```bash
sudo systemctl restart wings
``` 
Test the configuration by visiting:
https://texture.domain.com/debug 

If everything is working, you should see a message like:

`It seems to be working…`

If you get a **502 NGINX error**, review the NGINX configuration, checking for spelling errors and verifying all steps above. You can also check the NGINX error logs with:
```bash
sudo tail -f /var/log/nginx/error.log
```

### 11. Configure Oraxen for Polymath
On your Minecraft server, update the `Oraxen/settings.yml` to use your Polymath server:
```yaml
upload:
  enabled: true
  type: polymath # or transfer.sh
  polymath:
    server: texture.domain.com # Replace with your domain
    secret: "ADDWHATEVERHERE" # Set your secret for access control
```

### 12. Done!

Everything should now be set up correctly. Test your configuration, and enjoy using your Polymath server!

---

[Discord Support](https://discord.gg/mjmdE9C67a)
