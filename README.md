# docker-splunk-phantom: Dockerized Splunk Enterprise and Splunk Phantom 
Docker compose files to build containers for Splunk Enterprise and Splunk Phantom

## Getting Started
---------------
*Guides:*
1. [Setup Splunk Enterprise](#splunk-enterprise)
2. [Setup Splunk Phantom](#splunk-phantom)
3. [Setup Splunk Enterprise & Phantom](#splunk-enterprise-and-phantom)

*Prerequisites*
1. Install docker on your host OS. `Documentation: https://docs.docker.com/v17.09/engine/installation/`


### Splunk Enterprise

#### Create Container
*Note:* If any of your apps require `java` you will have to install Splunk 7.3 for the time being. I
have not had success with installing java on splunk:8.x images using the ansible enironment variable
`- JAVA_VERSION=openjdk:8` or `- JAVA_VERSION=oracle:8`. 

Make directory for docker-compose.yml file
```
mkdir splunk && cd splunk 
```

Copy and paste the docker-compose.yml file. You can find the examples under `splunk-enterprise/` or use this:

*Splunk 7.3 - with openjdk:8*
```
version: "3.6"
services:
  splunk:
    image: splunk/splunk:7.3
    container_name: splunk
    environment:
      - SPLUNK_START_ARGS=--accept-license
      - SPLUNK_PASSWORD=Splunk1234
      - JAVA_VERSION=openjdk:8
      - DEBUG=true
    ports:
      - 8000:8000
      - 8089:8089
    networks:
      - splunk-network
    volumes:
      - splunk-var:/opt/splunk/var
      - splunk-etc:/opt/splunk/etc 

networks:
  splunk-network:
    driver: bridge

volumes:
  splunk-var:
  splunk-etc:
```

*Splunk 8.x - latest*
```
version: "3.6"
services:
  splunk:
    image: splunk/splunk:latest
    container_name: splunk
    environment:
      - SPLUNK_START_ARGS=--accept-license
      - SPLUNK_PASSWORD=Splunk1234
      - JAVA_VERSION=openjdk:8
      - DEBUG=true
    ports:
      - 8000:8000
      - 8089:8089
    networks:
      - splunk-network
    volumes:
      - splunk-var:/opt/splunk/var
      - splunk-etc:/opt/splunk/etc 

networks:
  splunk-network:
    driver: bridge

volumes:
  splunk-var:
  splunk-etc:
```

*Note:* Persistent storage is made available through named volumes. You can opt to remove those lines in the config
if persistent storage is not neccessary for your build. You will not lose any data when you restart your container. The
only difference is that when you delete the container, if you used a named volume it will remain. To delete a volume,
use the command: `docker volume rm [volume name]`


### Splunk Phantom

*Prerequisites*
* You will need a phantom login in order for the setup script to run successfully. 

#### Create Container
Create and start the container
```
docker run -it -d --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:ro --name phantom --hostname phantosd -p "80:80" -p "443:443" centos/systemd
```

Update and execute the setup script
```
yum update -y
rpm -Uvh https://repo.phantom.us/phantom/4.5/base/7/x86_64/phantom_repo-4.5.15922-1.x86_64.rpm
/opt/phantom/bin/phantom_setup.sh install
```
Note: It will prompt you to enter your username and password for your phantom account. 


### Splunk Enterprise & Phantom

*Prerequisites*
1. Go to splunk.com and create a free account so you can download splunkbase apps. 

Coming soon...

