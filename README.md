# GenieACS installation and configuration guide


GenieACS is a powerful open-source Auto Configuration Server (ACS) for managing devices that support the TR-069 protocol. This guide will walk you through the installation and configuration of GenieACS.

## Prerequisites
Before you begin, ensure you have the following prerequisites:

- A server running a supported Linux distribution (e.g., Ubuntu, Debian, CentOS).
- Node.js (version 14 or later).
- MongoDB (version 4.0 or later).
- Basic knowledge of Linux command line.


## Installation Steps

### Step 1: Install Node.js
You can install Node.js using the package manager of your Linux distribution. For example, on Ubuntu, you can use:

```bash
sudo apt update
sudo apt install -y nodejs npm
```
### Step 2: Install MongoDB
Installation documentation for MongoDB can be found on the [MongoDB official website](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/).


### Install GenieACS


Clone the GenieACS repository from GitHub:

```bash
sudo npm install -g genieacs@1.2.13
```

### Configure systemd


Create a systemd service file for GenieACS:

```bash
sudo useradd --system --no-create-home --user-group genieacs
```

Create directory to save extensions and environment file


```bash
mkdir /opt/genieacs
mkdir /opt/genieacs/ext
chown genieacs:genieacs /opt/genieacs/ext
```

Create the file /opt/genieacs/genieacs.env 

```bash
sudo nano /opt/genieacs/genieacs.env
```

Add the following content to the file:

```bash
GENIEACS_CWMP_ACCESS_LOG_FILE=/var/log/genieacs/genieacs-cwmp-access.log
GENIEACS_NBI_ACCESS_LOG_FILE=/var/log/genieacs/genieacs-nbi-access.log
GENIEACS_FS_ACCESS_LOG_FILE=/var/log/genieacs/genieacs-fs-access.log
GENIEACS_UI_ACCESS_LOG_FILE=/var/log/genieacs/genieacs-ui-access.log
GENIEACS_DEBUG_FILE=/var/log/genieacs/genieacs-debug.yaml
NODE_OPTIONS=--enable-source-maps
GENIEACS_EXT_DIR=/opt/genieacs/ext
```

Generate a secure JWT secret and append to /opt/genieacs/genieacs.env:

```bash
node -e "console.log(\"GENIEACS_UI_JWT_SECRET=\" + require('crypto').randomBytes(128).toString('hex'))" >> /opt/genieacs/genieacs.env
```

Set file ownership and permissions:

```bash
sudo chown genieacs:genieacs /opt/genieacs/genieacs.env
sudo chmod 600 /opt/genieacs/genieacs.env
```

Create logs directory

```bash
mkdir /var/log/genieacs
chown genieacs:genieacs /var/log/genieacs
```

Run the following command to create genieacs-cwmp service:


```bash
sudo systemctl edit --force --full genieacs-cwmp
```

Add the following content to the file:

```ini
[Unit]
Description=GenieACS CWMP
After=network.target

[Service]
User=genieacs
EnvironmentFile=/opt/genieacs/genieacs.env
ExecStart=/usr/local/bin/genieacs-cwmp

[Install]
WantedBy=default.target
```

Run the following command to create genieacs-nbi service:


```bash
sudo systemctl edit --force --full genieacs-nbi
```
Add the following content to the file:

```ini
[Unit]
Description=GenieACS NBI
After=network.target

[Service]
User=genieacs
EnvironmentFile=/opt/genieacs/genieacs.env
ExecStart=/usr/local/bin/genieacs-nbi

[Install]
WantedBy=default.target
```

Run the following command to create genieacs-fs service:

```bash
sudo systemctl edit --force --full genieacs-fs
```

Add the following content to the file:

```ini
[Unit]
Description=GenieACS FS
After=network.target

[Service]
User=genieacs
EnvironmentFile=/opt/genieacs/genieacs.env
ExecStart=/usr/local/bin/genieacs-fs

[Install]
WantedBy=default.target
```

Run the following command to create genieacs-ui service:

```bash
sudo systemctl edit --force --full genieacs-ui
```

Add the following content to the file:

```ini
[Unit]
Description=GenieACS UI
After=network.target

[Service]
User=genieacs
EnvironmentFile=/opt/genieacs/genieacs.env
ExecStart=/usr/local/bin/genieacs-ui

[Install]
WantedBy=default.target
```

Configure log file rotation using logrotate

Create a logrotate configuration file for GenieACS:

```bash
sudo nano /etc/logrotate.d/genieacs
```

Add the following content to the file:

```ini
/var/log/genieacs/*.log /var/log/genieacs/*.yaml {
    daily
    rotate 30
    compress
    delaycompress
    dateext
}
```

### Step 3: Start GenieACS Services

Enable and start the GenieACS services:

```bash
sudo systemctl enable genieacs-cwmp
sudo systemctl start genieacs-cwmp
sudo systemctl status genieacs-cwmp

sudo systemctl enable genieacs-nbi
sudo systemctl start genieacs-nbi
sudo systemctl status genieacs-nbi

sudo systemctl enable genieacs-fs
sudo systemctl start genieacs-fs
sudo systemctl status genieacs-fs

sudo systemctl enable genieacs-ui
sudo systemctl start genieacs-ui
sudo systemctl status genieacs-ui
```

### Step 4: Access GenieACS UI
Open your web browser and navigate to `http://<your-server-ip>:3000`. You should see the GenieACS UI login page.
Log in with the default credentials:

- Username: `admin`
- Password: `admin`

### Step 5: Configure GenieACS
Once logged in, you can start configuring GenieACS to manage your devices. You can add devices, create profiles, and set up tasks as per your requirements.
## Conclusion
You have successfully installed and configured GenieACS on your server. You can now manage devices using the TR-069 protocol. For more advanced configurations and usage, refer to the [GenieACS documentation](https://docs.genieacs.com/).
## Troubleshooting
If you encounter any issues during installation or configuration, check the following:
- Ensure that all services are running by checking their status with `systemctl status <service-name>`.
- Check the log files located in `/var/log/genieacs/` for any error messages.
- Ensure that MongoDB is running and accessible.
- Verify that the GenieACS environment variables are correctly set in `/opt/genieacs/genieacs.env`.

## Additional Resources
- [GenieACS GitHub Repository](https://github.com/genieacs/genieacs)
- [GenieACS Documentation](https://docs.genieacs.com/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [Node.js Documentation](https://nodejs.org/en/docs/)

## License
This guide is provided under the [MIT License](https://opensource.org/license/mit/). You are free to use, modify, and distribute this guide as per the terms of the license.

## Acknowledgments
This guide is based on the official GenieACS documentation and community contributions. Special thanks to the GenieACS development team for their hard work in creating and maintaining this powerful ACS solution.

## Disclaimer
This guide is provided for informational purposes only. The installation and configuration of GenieACS are done at your own risk. The author and contributors are not responsible for any damages or issues that may arise from following this guide.

## Support
For support, you can join the [GenieACS community](https://community.genieacs.com/) or check the [GitHub issues page](


Happy Networking!
