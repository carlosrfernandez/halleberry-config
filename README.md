## Pi-hole Password Setup

The Pi-hole password is **not** set via environment variable for security reasons. Use one of these methods:

### Method 1: Interactive Setup (Most Secure - Recommended)

On first run, Pi-hole will generate a random password. Check the container logs to retrieve it:

```bash
docker logs pihole | grep "Assigning random password"
```

Then set your own password interactively:

```bash
docker exec -it pihole pihole -a -p
```

This prompts you to enter a new password securely without exposing it in logs or configuration files.

### Method 2: Set Password via Web Interface

1. Access Pi-hole web interface at `http://192.168.1.100`
2. If no password is set, you'll be prompted to set one
3. Navigate to Settings → System → Change Password

### Method 3: Docker Secrets (For Automation)

If you need automated setup, use Docker secrets with `WEBPASSWORD_FILE`:

1. Create a password file (ensure proper permissions):
   ```bash
   echo "your_secure_password" > /path/to/pihole_password.txt
   chmod 600 /path/to/pihole_password.txt
   ```

2. Add to docker-compose.yml:
   ```yaml
   environment:
     - WEBPASSWORD_FILE=/run/secrets/pihole_web_password
   secrets:
     - pihole_web_password
   secrets:
     pihole_web_password:
       file: /path/to/pihole_password.txt
   ```

**Note:** Method 1 is the most secure as it doesn't store the password in any file or environment variable.

## Pi-hole Access Troubleshooting (Raspberry Pi)

If you can't access Pi-hole at `http://192.168.1.100`, check the following:

### 1. Verify Container is Running
```bash
docker ps | grep pihole
```

### 2. Check Container Logs
```bash
docker logs pihole
```
Look for any errors or the initial password if it's a fresh install.

### 3. Check if Port 80 is in Use
Another service might be using port 80:
```bash
sudo netstat -tuln | grep :80
# or
sudo ss -tuln | grep :80
```

If something is using port 80, you can either:
- Stop the conflicting service
- Use an alternative port by uncommenting `WEB_PORT=8080` in docker-compose.yml

### 4. Verify Raspberry Pi IP Address
Make sure the IP matches your actual Pi IP:
```bash
hostname -I
# or
ip addr show
```

Update `ServerIP` in docker-compose.yml if it doesn't match.

### 5. Check Firewall (if enabled)
If you have a firewall enabled:
```bash
sudo ufw status
sudo ufw allow 80/tcp
```

### 6. Test from the Pi Itself
Try accessing from the Pi directly:
```bash
curl http://localhost/admin/
# or
curl http://192.168.1.100/admin/
```

### 7. Verify Network Mode
With `network_mode: host`, the container uses the host's network directly. Make sure:
- No other Docker containers are conflicting
- The Pi's network interface is up: `ip link show`

### 8. Alternative: Use Different Port
If port 80 conflicts persist, uncomment `WEB_PORT=8080` in docker-compose.yml and access at `http://192.168.1.100:8080/admin/`

## SSH key generation with elliptical curves

`ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519 -C "john@example.com"`

## Rsync to synology

### Docker volumes
```shell
rsync -avrP -e "ssh -i ./path.to/identity/file -T -c aes256-gcm@openssh.com -o Compression=no -x" --log-file=/volume1/Backups/IoT/halleberry/rpi4-volumes.log pi@192.168.1.53:/var/lib/docker/volumes/ /volume1/Backups/IoT/halleberry/docker-volumes/
```

### Pi home
```shell
rsync -avrP -e "ssh -i ./path.to/identity/file -T -c aes256-gcm@openssh.com -o Compression=no -x" --log-file=/volume1/Backups/IoT/halleberry/rpi4-home.log pi@192.168.1.53:~/ /volume1/Backups/IoT/halleberry/pi-home/
```