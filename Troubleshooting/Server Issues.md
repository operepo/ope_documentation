
# Troubleshooting Server Issues

## Can't Connect To Server - apps are up
It may appear that things are listening (netstat -an) on port 80 or 443 but you are unable to connect. It may also appear that port 80/443 are listening on tcp6 (ipv6) but not ip4. You may need to enable ip4 forwarding.

#### One time enable
sysctl -w net.ipv4.ip_forward=1
#### Enable after reboot - add to /etc/sysctl.conf
net.ipv4.ip_forward = 1

## Weird network errors?
Docker uses an internal NAT network of 172.17.0.0/16.  If you run on a private network of 172.17.?? you might need to switch the docker network to a new subnet to prevebt routing issues.
#### Turn off all apps
cd /ope/docker_build_files; docker-compose down; service docker stop;
#### Edit /etc/docker/daemon.json
{ "bip": "192.168.200.1/16" }
#### Start docker/apps
cd /ope/docker_build_files; service docker start; ./up.sh;



## Apps won't start?
Sometimes docker will get stuck. First thing to try is use the Utility button on the Sync App and tell it to restart the apps. If that doesn't work, you can try some manual things.

### Logging in with Putty
#### Download Putty
Get the binary - no need to get full MSI installer.

[Download Putty] (https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
![Download Putty](./putty_download.png)

#### Login
It works best to login with the IP of the OPE server. If things are down, the domain name may not resolve. Login with "root" as the username and your password.

### Restart Docker Service
Try `docker ps` to see if things are running. If you get an error, you may need to restart the docker service.

Stop the service
`service docker stop`

Might need to remove the .sock file
`rm -Rf /var/run/docker.sock`

Start the service
`service docker start`

Give it a few seconds, then you should see your apps running
`docker ps`

### Restart OPE apps
You can do a full restart of the docker apps (smc, canvas, etc...)
1. `cd /ope/docker_build_files`  (assuming your stuff is stored in the /ope folder)
2. `docker-compose down`  (if you see an error, just run it again, sometimes it gets stuck. If it keeps showing an error, restart the docker service)
3. `./up ` (makes the configs rebuild and launch)
4. Most things should be up within 30 seconds. Canvas generally takes anywhere from 3-15 minutes to start.

### Cleanup old Docker images
As you do updates, old app images are left in the system. If you notice lots of space being used that isn't located in the /ope/volumes folder, it may be old docker images.

Warning: This may take several minutes and put a load on the server - services may be slow during cleanup

- Cleaup app that are stopped: `docker rm -v $(docker ps -a -q -f status=exited)`
- Cleanup old images that aren't referenced anymore: `docker rmi $(docker images -q -f dangling=true)`


### Canvas won't start after upgrade
During update from an older version of Canvas, it has been reported that the database migration gets stuck due to a constraint on the role_overrides table.  This should show up as a message in the canvas logs (view logs from sync app).  You end up with a stack trace referring to the role_overrides table and a constraint named fk_rails_ed57234287.

To fix this, the ope-postgresql app should be up and running. Login to the OPE server via Putty and do the following:

Go to the right folder

`
cd /ope/docker_build_files
`

Then run this command to tell postgres to drop the constraint on that table (Note where single quotes are - the ope-postgresql needs to be running):

`docker-compose exec ope-postgresql psql -U postgres -d canvas_production -c 'ALTER TABLE role_overrides DROP CONSTRAINT fk_rails_ed57234287;'
`

At this point, if canvas is failing and restarting, you can let it just restart and next time it will succeed in migrating things and starting up. Give it 5-10 minutes to finish.

If you are impatient, you can bring it all down and back up (run this from the /ope/docker_build_files folder);

`
docker-compose down; ./up.sh
`
