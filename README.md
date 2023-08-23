# Nexus Sonatype Installation Guide

This guide will help you install and configure Nexus Sonatype on your Ubuntu system.

## Prerequisites

Before you begin, ensure you have the following prerequisites:

- Ubuntu Server ( Tested version: 20.04 LTS with 4 cpu and 4gb ram)
- Java 8 
- Open port 8081 and necessary ports

## Installation Steps

## Update System Packages

    sudo apt update
## Install Java

    sudo apt-get install openjdk-8-jdk -y

## Use as root user

    sudo su

## Go to /opt folder

    cd /opt

## Download and Install Nexus Sonatype


    NEXUS_VERSION="3.59.0"  # Change to desired version
    wget "https://download.sonatype.com/nexus/3/nexus-${NEXUS_VERSION}-unix.tar.gz"
    tar -zxvf nexus-${NEXUS_VERSION}-unix.tar.gz 
    mv nexus-${NEXUS_VERSION} nexus
    rm "nexus-${NEXUS_VERSION}-unix.tar.gz"

## Create a Nexus User

    useradd -r -m -U -d /opt/nexus -s /bin/false nexus
    sudo usermod -aG sudo nexus

## Change file permission

    chown -R nexus:nexus /opt/nexus
    chown -R nexus:nexus /opt/sonatype-work

## Go to bin folder and change nexus.rc file

    cd /opt/nexus/bin
    sed -i 's/^# run_as_user=""/run_as_user="nexus"/' nexus.rc

## Configure Nexus as Service


    cat <<EOF | sudo tee /etc/systemd/system/nexus.service
    [Unit]
    Description=Nexus Sonatype
    After=network.target
    
    [Service]
    Type=forking
    LimitNOFILE=65536
    ExecStart=/opt/nexus/bin/nexus start
    ExecStop=/opt/nexus/bin/nexus stop
    User=nexus
    Restart=on-abort
    
    [Install]
    WantedBy=default.target
    EOF

## Reload demon and start Nexus Service

    sudo systemctl daemon-reload
    sudo systemctl enable nexus
    sudo systemctl start nexus

## Accessing Nexus

After service starts, you can access Nexus Sonatype via a web browser at `http://your-server-ip:8081/`. The default admin username is `admin` and password located in the Nexus data directory.

    cat /opt/sonatype-work/nexus3/admin.password


## Enjoy!!!
