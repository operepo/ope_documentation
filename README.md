# Overview - OPE (Open Prison Education)
It is a struggle to install and manage online services in an offline world. Simple things like updates or installing new packages becomes a hassle. We work in highly secure prison environments where internet is not available. 

The goal here is to create a tool where we can package apps that can be easily copied to a USB drive, then pushed to a server in the offline environment.

This project consists of building and staging of apps, easy deployment into facilities, and offline laptops that students can take back to their unit to continue their school work.

# Project Goals

1) Create a place for skilled IT personnel to stage and place apps that are ready to deploy

2) Limit the need for skilled IT personnel to deal with the actual deployment process

3) Provide laptop images that can sync to a learning management system, credential for an individual student, and apply security settings to keep the system secure during use

# Project Members
- Project Lead:
    - Brian Walsh <bwalsh@sbctc.edu>
- Software Development:
    - Ray Pulsipher <rpulsipher@pencol.edu>
    - Kent James <kent@caspia.com>
- Product Testing:
    - Mike Huse <mdhuse@DOC1.WA.GOV>
    - Daniel Gonzales <dgonzales@DOC1.WA.GOV>
- Docker App Development:
    - Kris Gonyo <kristophergonyo@gmail.com>
- Fabrication
    - Kristopher Margart <kwmargart@DOC1.WA.GOV>

# Documentation
You can find documentation here: https://github.com/operepo/ope_documentation
And video tutorials here: https://github.com/operepo/ope_vids
    
# Available Apps

## Required Apps
Some apps are needed so that the system will run properly

### Gateway
name: ope-gateway
DNS: gateway.ed
Reverse proxy/nginx to allow web traffic using a shared IP

### Router
Name: ope-router
DNS: router.ed
Deals with multicast routing and firewall rules

### DNS
Name: ope-dns
DNS: dns.ed
Status: required/stable
Deals with DNS requests - add conditional forwarder on your main DNS server to point to this IP

### REDIS
Name: ope-redis
DNS: redis.ed
Status: required/stable
Memory caching server, used by Canvas and other apps, ports not exposed outside docker network

### Postgresql
Name: ope-postgresql
DNS: postgresql.ed
Status: required/stable
Database server, used by Canvas and other apps

### FOG
Name: ope-fog
DNS: fog.ed
Status: recommended/stable
Imaging Server - stores windows images for OPE Laptops, used for network deployment

### Canvas
Name: ope-canvas
DNS: canvas.ed
Status: recommended/stable
LMS setup to run offline and integrate with OPE project

### SMC
Name: ope-smc
DNS: smc.ed
Status: recommended/stable
Student Management Console to import student accounts and integrate with Canvas and OPE Laptops

### ClamAV
Name: ope-clamav
DNS: clamav.ed
Status: recommended/beta
Holds anti-virus patterns for your network

### Khan Lite
Name: ope-kalite
DNS: kalite.ed, khan.ed
Status: optional/beta
Khan Academy Lite - choose version available in RACHEL server

### CodeCombat
Name: ope-codecombat
DNS: codecombat.ed
Status: optional/beta
Lean to write code by scripting games in a website and solving puzzles

### GCF Learn Free
Name: ope-gcf
DNS: gcf.ed
Status: optional/ not ready
Learn basic computer skills - (version also available in RACHEL server)

### FreeCodeCamp
Name: ope-freecodecamp
DNS: freecodecamp.ed
Status: optional/not ready
Learn to code with lessons and projects from freecodecamp.com

### JSBin
Name: ope-jsbin
DNS: jsbin.ed
Status: optional/not ready
Javascript learning environment

### RACHEL
Name: ope-rachel
DNS: rachel.ed
Status: optional/not ready
RACHEL Web server, large library of offline content available as modules

### StackDump
Name: ope-stackdump
DNS: stackdump.ed
Status: optional/not ready
StackDump is a copy of Stack Overflow filtered for inmate use

### WAMAP
Name: ope-wamap
DNS: wamap.ed
Status: optional/not ready
Open source math learning system with lesson materials

### WA WSL Re-entry Wiki
Name: ope-wsl
DNS: wsl.ed
Status: optional/beta
WA State Library wiki with re-entry resources


# Deployment Steps

1) Skilled IT personnel create docker apps and setup code to sync these apps and app data to a device (e.g. USB drive or Laptop)

2) Sync USB drive in online or offline mode with a few button clicks - Goal is to have non IT personnel do this

3) Image laptops and credential them for individual students - Goal is non IT personnel or local IT personnel to be able to do this easily

4) Student brings laptop to docking station to sync with learning management system - download materials, upload completed work

# Tech Details

## Micro Service Architecture
We utilize Docker containers for micro services. You will need a machine that can run docker containers on the outside to build and setup your services, and a docker machine on the inside to transfer images and data to.

```
  +----------------------------------------------------------------------+
  | Online Server                                                        |
  |                                                                      |
  | you need a linux machine running docker                              |
  |  - can host this on campus (recommended)                             |
  |  - or on your desktop in virtual box/hyper-v/vmware/etc...           |
  |                                                                      |
  | Pre built Linux with Docker Available Here:                          |
  | - https://susestudio.com/a/P08rUy/ope-docker                         |
  |                                                                      |
  +------------------^---------------------------------------------------+
                     |
                     |
                +----V--------------------------------------------------+
                | USB Drive (or laptop)                                 |
                |                                                       |
                | Use a GUI based app to sync between servers           |
                | - Pulls apps/data from the online server              |
                | - Pushes apps/data to the offline server              |
                | - Clickable buttons - made for NON IT personnel       |
                +------------------------^------------------------------+
                                         |
                                         |
                                    +----V-------------------------------------------------------+
                                    | Offline Server                                             |
                                    |                                                            |
                                    | identical to the online server, but apps/data will         |
                                    | be pushed into this system                                 |
                                    |                                                            |
                                    | Use the same docker system as the online system:           |
                                    | - https://susestudio.com/a/P08rUy/ope-docker               |
                                    +--^----------------------------------------------------------+
                                       |
                                       |
                +----------------------V------------------------------------+
                | Inmate Laptop                                             |
                |                                                           |
                | Credentialed with the LMS app installed                   |
                | Will pull course work from offline LMS server and         |
                | push completed work back to the server for grading        |
                +-----------------------------------------------------------+
```

## Getting Started
1. Get a linux distro with docker installed in both your online and offline servers:
   - Linux image ready to go (ISO - burn to DVD): http://correctionsed.com/updates/OPE_Docker.x86_64-0.0.10.preload.iso
   (broken link https://susestudio.com/a/P08rUy/ope-docker)
   - Assign a static IP address to each server and write it down
   - Login with root (password: changeme) and use the passwd utility to change to a secure password

2. From online computer, download the OPE Git project at: https://github.com/operepo/ope
   - From the GREEN button, click and choose "Download Zip".
   - Unzip this onto your USB drive into a folder called ope (e.g. e:\ope )
   
3. Run the SyncApp.exe app
   - In the ope folder, you should see a folder called SyncApp. Run the SyncApp.exe file from there

4. Configure your settings
   - Make sure you put in the correct IP for each server as well as the password you set

5. Run Online
   - Choose the online button, then click Run. This will start pulling in the selected apps and syncing them to the USB drive

6. Unplug, take USB drive to offline server, the choose the Offline option and run it
   - This will push the apps/data on the USB drive to the offline server

7. Repeat online/offline as often as needed

8. Setup DNS forwarder to .ed domain name to the static IP of the server. This forwards all DNS queries for the .ed domain to the docker apps to resolve DNS automatically
   - For Active Directory DNS - add a conditional forwarder for the domain ed -> to the static IP (put in the correct IP for the online or offline server)
   - For Linux DNS users, a wildcard DNS should work too if you don't know how to do a conditional forwarder
   
   
## Getting Started - Developers
Use this method to get everything ready to do development.

1. Get a linux distro with docker installed in both your online and offline servers:
   - Linux image ready to go (ISO - burn to DVD): https://susestudio.com/a/P08rUy/ope-docker
   - Assign a static IP address to each server and write it down

2. Install Git

3. Python 2.7.? and needed libraries (python.org - choose 2.7? MSI installer)
   - NOTE: On the options page of the install, make sure "Add Python to Path" is selected
   - Run the InstallPythonModules.cmd batch file as admin to install all the needed python modules
   
4. Clone the OPE project into a folder on your computer.
   - From the command line, CD to the folder you want to be in: cd git_projects
   - Clone the rep to your computer: git clone https://github.com/operepo/ope.git

5. Use the SyncApp tool to push apps to your online linux server (see instructions above)

## Manuall Starting Apps - On the online Linux Server

1. SSH into your online server (putty is a great tool from windows)

2. Move to the OPE folder: cd /ope

3. Move to the folder with the docker files: cd docker_build_files

4. Start the apps: ./up.sh  (use ./up.sh b  if you want to build the docker apps from here) 

5. From here you can also use docker-compose commands in place of the up.sh command
   - python ../build_tools/rebuild_compose.py  --> rebuilds the docker-compose file based on activated apps
   - docker-compose up -d   --> start up all enabled apps
   - docker-compose down    --> stop all apps


# NOTE - 6/7/17 - file structure rearranged on this date
Revising to use dockerhub to pull built images as well as include client tools/etc...
This is currently in process and could mean breaking changes to the prev build process.


