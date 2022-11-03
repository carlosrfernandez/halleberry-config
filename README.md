## SSH key generation with elliptical curves

`ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519 -C "john@example.com"`

## Docker rsync from synology

rsync -avrP -e "ssh -T -c aes256-gcm@openssh.com -o Compression=no -x" --log-file=/volume1/Backups/IoT/halleberry/rpi4-volumes.log pi@192.168.1.53:/var/lib/docker/volumes/ /volume1/Backups/halleberry/docker-volumes/
rsync -avrP -e "ssh -T -c aes256-gcm@openssh.com -o Compression=no -x" --log-file=/volume1/Backups/IoT/rpi4-home.log pi@192.168.1.53:~/ /volume1/Backups/IoT/halleberry/pi-home/