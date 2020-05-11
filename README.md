# IPMI Exporter


## Description

Simple exporter designed to expose power consumption metrics obtained from physical sensors. It is designed to be runned in the physical nodes of the SODALITE project infrastructure and its measurements will be used to improve and document the application execution. 

The way it works is fairly simple: it executes a command, collects the output, isolates the desired variable and exposes it for Prometheus. Thanks to its simplicity, it can be adapted to expose whatever metric that can be obtained with a terminal command. 

## Software requirements

- Golang. It is mandatory to have one of the latest versions to support some of the network functionalities. (The current implementation was compiled with the 1.13 version) 
- Sudo access to create new users and services to run the exporter. Besides, the `ipmi` command requires `sudo` to be executed. 

## Installation 

### 1. Creating a new user and setting up the environment.

There is going to be a user created to run this exporter, and the following command will create it without a home directory nor the capability of logging in. 

```bash
sudo useradd --no-create-home --shell /bin/false ipmi-exporter
```

After that, a folder to contain all exporter files needs to be created. In addition, we will give the newly created user permission to that folder.

```bash
sudo mkdir /etc/ipmi-exporter
sudo chown ipmi-exporter:ipmi-exporter /etc/ipmi-exporter/
```

### 2. Downloading and testing the exporter. 

Clone this repository on your machine, import the exporter required Git repositories, change the functions or exported metrics and compile it with `go build`. 

This will generate an executable, and after successfully testing it with the below-included command (it will output a collection of variables, and among them, there will be `ipmi_power_consumption_watts` or your defined variables), copy it to the `/etc/ipmi-exporter/` folder.

 ```bash
 curl localhost:9392/metrics
 ```

Note that the 9392 port is used because it was unallocated when this exporter was developed and it generates no conflicts with any other already existing exporter (see this [Github page](https://github.com/prometheus/prometheus/wiki/Default-port-allocations) for more information about this and other available ports).

### 3. Transform this executable into a service. 

If the above-included instructions were correctly followed, the steps needed to make this simple exporter run as a service are fairly simple. With your preferred editor, create `ipmi-exporter.service` file inside the `/etc/systemd/system` folder and paste the contents of the [ipmi-exporter.service](https://github.com/MarioMartReq/generic-exporter/blob/master/ipmi-exporter.service "ipmi-exporter.service file GitHub page") file included with this repository. Save and close the file. 

After that, execute the following commands.

```bash
sudo systemctl daemon-reload
sudo systemctl start ipmi-exporter.service
sudo systemctl status ipmi-exporter.service
```

If everything worked correctly, the following output should be observed.

```bash
● ipmi-exporter.service - IPMI exporter service
   Loaded: loaded (/etc/systemd/system/ipmi-exporter.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2019-12-03 08:13:57 UTC; 3h 20min ago
 Main PID: 16808 (ipmi-exporter)
    Tasks: 13 (limit: 4915)
   CGroup: /system.slice/ipmi-exporter.service
           └─16808 /etc/ipmi-exporter/ipmi-exporter/ipmi-exporter


sudo[20762]: ipmi-exporter : TTY=unknown ; PWD=/ ; USER=root ; COMMAND=/usr/sbin/ipmi-sensors -h localhost --no-sensor-type-output...
sudo[20762]: pam_unix(sudo:session): session opened for user root by (uid=0)
sudo[20762]: pam_unix(sudo:session): session closed for user root


## Usage
This repository contains the IPMI exporter that was originally created to expose the power consumption measurement given by the below-included command for SODALITE. 

## Installation guide.

### 0. Pre-requisites.

- Golang. It is mandatory to have one of the latest versions to support some of the network functionalities. (The current implementation was compiled with the 1.13 version) 
- Sudo access to create new users and service to run the exporter. Besides, the above included `ipmi` command requires `sudo` to be executed. 

### 1. Creating a new user and setting up the environment.

There is going to be a user created to run this exporter, and the following command will create it without a home directory nor the capability of logging in. 

```bash
sudo useradd --no-create-home --shell /bin/false ipmi-exporter
```

After that, a folder to contain all exporter files needs to be created. In addition, we will give the newly created user permission to that folder.

```bash
sudo mkdir /etc/ipmi-exporter
sudo chown ipmi-exporter:ipmi-exporter /etc/ipmi-exporter/
```

### 2. Downloading and testing the exporter. 

Clone this repository on your machine, import the exporter required Git repositories, change the functions or exported metrics and compile it with `go build`. 

This will generate an executable, and after successfully testing it with the below-included command (it will output a collection of variables, and among them, there will be `ipmi_power_consumption_watts` or your defined variables), copy it to the `/etc/ipmi-exporter/` folder.

 ```bash
 curl localhost:9392/metrics
 ```

Note that the 9392 port is used because it was unallocated when this exporter was developed and it generates no conflicts with any other already existing exporter (see this [Github page](https://github.com/prometheus/prometheus/wiki/Default-port-allocations) for more information about this and other available ports).

### 3. Transform this executable into a service. 

If the above-included instructions were correctly followed, the steps needed to make this simple exporter run as a service are fairly simple. With your preferred editor, create `ipmi-exporter.service` file inside the `/etc/systemd/system` folder and paste the contents of the [ipmi-exporter.service](https://github.com/MarioMartReq/generic-exporter/blob/master/ipmi-exporter.service "ipmi-exporter.service file GitHub page") file included with this repository. Save and close the file. 

After that, execute the following commands.

```bash
sudo systemctl daemon-reload
sudo systemctl start ipmi-exporter.service
sudo systemctl status ipmi-exporter.service
```

If everything worked correctly, the following output should be observed.

```bash
● ipmi-exporter.service - IPMI exporter service
   Loaded: loaded (/etc/systemd/system/ipmi-exporter.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2019-12-03 08:13:57 UTC; 3h 20min ago
 Main PID: 16808 (ipmi-exporter)
    Tasks: 13 (limit: 4915)
   CGroup: /system.slice/ipmi-exporter.service
           └─16808 /etc/ipmi-exporter/ipmi-exporter/ipmi-exporter


sudo[20762]: ipmi-exporter : TTY=unknown ; PWD=/ ; USER=root ; COMMAND=/usr/sbin/ipmi-sensors -h localhost --no-sensor-type-output...
sudo[20762]: pam_unix(sudo:session): session opened for user root by (uid=0)
sudo[20762]: pam_unix(sudo:session): session closed for user root
```

## Usage 

This exporter needs to be placed in a physical node where the `ipmi-sensor` command could access the power measurement tools required to obtain the metrics. 