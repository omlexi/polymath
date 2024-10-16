# Polymath

A rewrite of [oraxen/polymath](https://github.com/oraxen/polymath) in **TypeScript**, forked from [gurrrrrrett3/polymath-ts](https://github.com/gurrrrrrett3/polymath-ts) to provide support for **Local Storage** and **S3 Storage**.

---

## Table of Contents

- [Features](#features)
- [How to Use](#how-to-use)
  - [Using Pterodactyl](#using-pterodactyl)
  - [Using Public Atlas](#using-public-atlas)
- [Configuration](#configuration)
- [Support](#support)

---

## Features

- **TypeScript rewrite** for improved type safety and code maintainability.
- **Dual storage support**:
  - **Local Storage** for smaller deployments.
  - **S3 Storage** for scalable cloud infrastructure.
- **Pterodactyl Egg integration** for server deployment.
- **Public Atlas support** for easy configuration with Oraxen.

---

## How to Use

### Using Pterodactyl

1. **Install the Polymath Egg**

   - Download the [Polymath Egg](https://github.com/Arknesia/Polymath/blob/master/egg-polymath.json) and import it to your **Pterodactyl Panel**.

2. **Create a New Server**

   - Use **port 8181** (since **port 8080** is reserved for the Wings).

3. **Configure Nginx**
   Add the following configuration to your **Nginx** setup:

   ```nginx
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

4. **Update the Domain**

   - Replace `atlas.domain.com` with **your own domain**.

5. **Generate SSL Certificates**
   - Follow the [Pterodactyl SSL Certificate Tutorial](https://pterodactyl.io/tutorials/creating_ssl_certificates.html) to create certificates for your domain.

---

### Using Public Atlas

1. **Edit the Oraxen Configuration File**  
   Open the `Oraxen/settings.yml` file and update the relevant section:

   ```yaml
   Pack:
     upload:
       enabled: true
       type: polymath
       polymath:
         server: atlas.arknesia.com
         secret: ChangeThisUsingYourUniqueId
   ```

2. **Update the Secret Key**
   - Replace `ChangeThisUsingYourUniqueId` with your **own unique ID** to authenticate the connection.

---

## Configuration

```json
{
  "server": {
    "port": 8181,
    "url": "https://atlas.domain.com"
  },
  "request": {
    "maxSize": 104857600 // 100MB
  },
  "cleaner": {
    "delay": 21600000, // 6 hours
    "packLifespan": 604800000 // 7 days
  },
  "useS3": false,
  "s3Config": {
    "accessKeyId": "",
    "secretAccessKey": "",
    "region": "",
    "bucketName": "polymath",
    "endpoint": ""
  }
}
```

---

## Support

Need help? Join our community on Discord: [Discord Support](https://discord.gg/5pZMBvjVQh)
