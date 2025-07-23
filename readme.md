# Installing and Setting Up TeamSpeak Server 3.13.7 on Ubuntu 22.04 LTS

This guide provides step-by-step instructions to install and set up TeamSpeak server version 3.13.7 on Ubuntu 22.04 LTS. These instructions are designed for 64-bit systems (x86_64).

## Prerequisites
- Operating System: Ubuntu 22.04 LTS (64-bit)
- User Access: `sudo` privileges or root access
- Required Tools: `wget`, `tar`, `bash`
- Internet connection for downloading files

## Step 1: Update the System
Ensure your system is up to date before starting.

```bash
sudo apt update
sudo apt upgrade -y
```

## Step 2: Create a Dedicated TeamSpeak User
For security, run the server with a non-root user. Create a `teamspeak` user:

```bash
sudo adduser --system --group --no-create-home teamspeak
```

## Step 3: Download and Extract TeamSpeak
Download the 64-bit TeamSpeak 3.13.7 server from the official website:

```bash
cd /tmp
wget https://files.teamspeak-services.com/releases/server/3.13.7/teamspeak3-server_linux_amd64-3.13.7.tar.bz2
```
Extract the file and move it to `/opt/teamspeak`:

```bash
tar -xjf teamspeak3-server_linux_amd64-3.13.7.tar.bz2
sudo mkdir -p /opt/teamspeak
sudo mv teamspeak3-server_linux_amd64/* /opt/teamspeak/
```

## Step 4: Set Permissions
Assign ownership and permissions to the `teamspeak` user:

```bash
sudo chown -R teamspeak:teamspeak /opt/teamspeak
sudo chmod -R 750 /opt/teamspeak
```

**Note**: If you want another user (e.g., `etasi`) to access the folder, add them to the `teamspeak` group:

```bash
sudo usermod -aG teamspeak etasi
```

Replace `etasi` with your username.

## Step 5: Start the Server
Run the server as the `teamspeak` user:

```bash
cd /opt/teamspeak
sudo -u teamspeak ./ts3server_startscript.sh start
```

If the server starts successfully, it will display information including the **Server Admin Token**. Save this token, as it is required for server administration.

## Step 6: Check Logs (If Needed)
If the server fails to start, check the logs for clues:

```bash
ls -l /opt/teamspeak/*.log
cat /opt/teamspeak/ts3server_*.log
```

## Step 7: Configure Firewall (Optional)
TeamSpeak uses the following ports:
- 9987/UDP (voice)
- 10011/TCP (ServerQuery)
- 30033/TCP (file transfer)

If a firewall (e.g., `ufw`) is active, open these ports:

```bash
sudo ufw allow 9987/udp
sudo ufw allow 10011/tcp
sudo ufw allow 30033/tcp
sudo ufw status
```

## Step 8: Set Up Autostart (Optional)
To make the server start automatically on boot, create a `systemd` service:

```bash
sudo nano /etc/systemd/system/teamspeak.service
```

Add the following content:

```ini
[Unit]
Description=TeamSpeak 3 Server
After=network.target

[Service]
User=teamspeak
Group=teamspeak
Type=forking
ExecStart=/opt/teamspeak/ts3server_startscript.sh start
ExecStop=/opt/teamspeak/ts3server_startscript.sh stop
PIDFile=/opt/teamspeak/ts3server.pid
Restart=always

[Install]
WantedBy=multi-user.target
```

Save the file and enable the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable teamspeak
sudo systemctl start teamspeak
```

## Security Tips
- **Admin Token**: Store the admin token securely.
- **Firewall**: Only open necessary ports.
- **Non-root User**: Always run the server as the `teamspeak` user, not root.
- **Updates**: Regularly update the server to newer versions.

## Troubleshooting
- **Server Fails to Start**: Ensure you downloaded the 64-bit version and check logs for errors.
- **Permission Issues**: Verify that your user is in the `teamspeak` group or use `sudo`.
- **Logs**: Always check logs for detailed error messages.
