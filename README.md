What will be installed:
- __xrootd proxy-cache server__
- __X509 proxy renewer__ to allow a valid proxy to be always available to read data from AAA

## Requirements

- :exclamation: port 30443 of the host open toward the clients that will use the cache
- at least 16GB of RAM, but 32 is a recommended  minimum
- O(10TB) of disk space for data caching
- 10Gbps connectivity
- [docker](https://docs.docker.com/engine/install/)
    - most commonly you will only need the following:
    ```bash
    curl -fsSL https://get.docker.com -o get-docker.sh
    sudo sh get-docker.sh
    ```
- [docker-compose](https://docs.docker.com/compose/install/)
    - on linux, usually this is done via:
    ```bash
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    ```
- :exclamation: User or robot x509 certificates into `certs` folder named as `usercert.pem` and `usercert.key`
    ```bash
    git clone https://github.com/comp-dev-cms-ita/compose-xrootd
    cd compose-xrootd
    mkdir -p certs
    echo "MYUSER CERTIFICATE" > ./certs/usercert.pem
    echo "MYUSER CERTIFICATE KEY" > ./certs/usercert.key
    chmod 600 ./certs/usercert.key
    ```
- a valid telegraf token to be able to push metrics to the central InfluxDB
  - please contact diego.ciangottini<at>pg.infn.it to obtain one
  - then insert it in `telegraf-config/telegraf.conf` where you find `token = "CHANGEME" `
- put your site name in place if the tag `SITENAME HERE` in the `telegraf-config/telegraf.conf` file

## Preparation

First of all create all the needed directories:
```bash
mkdir -p logs
mkdir -p proxy
mkdir -p data
mkdir -p metadata
```

> :exclamation: __N.B.__ if you want to provide an external volume to store data, you can simply mount it as `./data` folder. 

In `.env` file you can find the main knobs to tweak the cache configuration. Among the others you  should adjust the `CACHE_RAM_GB` to be between one third and an half of your total machine memory (e.g. for a machine with a total of 4GB of RAM, you could put `CACHE_RAM_GB=2`)


## Deploy

Now everything should be ready to go. Bring up the system with:

```bash
docker-compose up -d
```

and monitor the status via a simple `docker ps` command.
When everything is in status `healthy` (that can take several minutes), you should be able to find the logs of the xrootd daemon on `./logs` folder.

## Configure WNs to select the cache instance by default

Please follow the [WN deployment instructions](https://github.com/comp-dev-cms-ita/compose-htc-wn/blob/master/README.md#set-the-automatic-cache-selection-for-the-xrootd-client) 
