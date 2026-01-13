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

1. Access Pi-hole web interface at `http://192.168.0.100`
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