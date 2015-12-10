# Docker Utilities and Image Files for Indy

This repository contains a set of init scripts for setting up indy docker containers of three flavors:

  * A stripped-down volumes container (`init-indy-volumes`)
  * An Indy server container meant to work with the volume container (`init-indy-server`)
  * A standalone Indy server container (`init-indy-server-no-vols`)

You can run any of the above scripts with `-h` to see the available options.

It also contains an autodeploy script (`autodeploy-indy-server`) that you can add to your cron jobs, 
to autodeploy an Indy tarball in dev-mode. Systemd scripts are provided in the `systemd/` directory, 
for maintaining active Indy containers, with one service definition for each of the init scripts above.

Finally, it contains the docker image source materials (Dockerfile + supporting scripts) for the two basic
image types (server and volume container).

## Quickstart for CentOS 7

This is intended to be more or less a list of instructions for you to run. It would take a bit more effort
to turn it into a really functional script:

    #!/bin/bash
    
    yum -y update
    yum -y install epel-release
    yum -y install docker tree lsof python-lxml python-httplib2
    systemctl enable docker
    systemctl start docker
    curl http://repo.maven.apache.org/maven2/org/commonjava/indy/docker/indy-docker-utils/0.19.1/indy-docker-utils-0.19.1.tar.gz | tar -zxv
    cd indy-docker-utils
    
    # ./init-indy-volumes -h
    ./init-indy-volumes
    
    # ./init-indy-server -h
    ./init-indy-server -p 80 -q
    
    #Or, if you want, you can leave off the '-q' option and watch the server come up
    #...then use CTL-C to exit the tty (the container will keep running)
    
    cp systemd/indy-volumes.service systemd/indy-server.service /etc/systemd/system
    systemctl enable indy-volumes indy-server
    
    docker stop indy indy-volumes
    systemctl start indy-volumes
    systemctl start indy
    
    # Use this to see the server log as it starts up, to make sure it boots properly.
    journalctl -f

