Install the postgresql and check the status 


`sudo systemctl status postgresql-12`

Output 

![image](https://user-images.githubusercontent.com/98477908/213885143-2d899a18-37d5-41c5-b019-bec519712ebe.png)

`mkdir  /opt/postgres_exporter`


`cd /opt/postgres_exporter`


`wget https://github.com/prometheus-community/postgres_exporter/releases/download/v0.11.0/postgres_exporter-0.11.0.linux-amd64.tar.gz`


`tar -xvf postgres_exporter-0.11.0.linux-amd64.tar.gz`


`cp postgres_exporter  /usr/local/bin/`


`cd /opt/postgres_exporter`


`sudo nano postgres_exporter.env`

#  Put

`DATA_SOURCE_NAME="postgresql://postgres:postgres@192.168.198.142:5432/?sslmode=disable"`

![image](https://user-images.githubusercontent.com/98477908/213885591-158e0362-ed0a-41d1-ac26-76a447bed1b5.png)

Note: here i user postgres db username and password  you can set db username and password by using this command in getting shell access of the db .


`su - postgres`


`psql`


`ALTER USER postgres PASSWORD 'postgres';`


`systemctl restart postgresql-12`

## Configure the node exported systemd service 

vi /etc/systemd/system/postgres_exporter.service

![image](https://user-images.githubusercontent.com/98477908/213885682-5a067357-7b06-4ccb-8849-1037e870c7c8.png)

[Unit]

Description=Prometheus exporter for Postgresql

Wants=network-online.target

After=network-online.target

[Service]

User=postgres

Group=postgres

WorkingDirectory=/opt/postgres_exporter

EnvironmentFile=/opt/postgres_exporter/postgres_exporter.env

ExecStart=/usr/local/bin/postgres_exporter --web.listen-address=192.168.198.142:9100 --web.telemetry-path=/metrics

Restart=always

[Install]

WantedBy=multi-user.target



Now start the daemon

`sudo systemctl daemon-reload`


`sudo systemctl start postgres_exporter`


`sudo systemctl enable postgres_exporter`


`sudo systemctl status postgres_exporter`

![image](https://user-images.githubusercontent.com/98477908/213885882-2c42a07a-a9f2-4cd9-9b86-1421455caadf.png)

You can check the metrics in any browser

![image](https://user-images.githubusercontent.com/98477908/213885919-2210a133-57f9-441e-b9b1-c370fcd9f1fb.png)


##  Install prometheus

`mkdir promethus`

`cd promethus/`


`wget https://github.com/prometheus/prometheus/releases/download/v2.37.5/prometheus-2.37.5.linux-amd64.tar.gz`
 

`useradd --no-create-home --shell /bin/false prometheus`
 

`mkdir /etc/prometheus`
 

`mkdir /var/lib/prometheus`
 
`chown prometheus:prometheus /etc/prometheus`
 

`chown prometheus:prometheus /var/lib/prometheus`


`tar -xvzf prometheus-2.37.5.linux-amd64.tar.gz`


`cd prometheus-2.37.5.linux-amd64`


`cp prometheus /usr/local/bin/`


`chmod u+x promtool`


`cp promtool  /usr/local/bin/`


` chown prometheus:prometheus /usr/local/bin/prometheus`


` chown prometheus:prometheus /usr/local/bin/promtool`


`cp -r prometheuspackage/consoles /etc/prometheus`


`cp -r consoles /etc/prometheus/`


`cp -r console_libraries/ /etc/prometheus/`


`chown -R prometheus:prometheus /etc/prometheus/consoles`


`chown -R prometheus:prometheus /etc/prometheus/console_libraries`


`chown prometheus:prometheus /etc/prometheus/prometheus.yml`
 
 `vim /etc/prometheus/prometheus.yml`
 
 ![image](https://user-images.githubusercontent.com/98477908/213886463-a7f26ec7-19de-434e-8135-fd890c72c332.png)

global:
  scrape_interval: 10s

scrape_configs:
  - job_name: 'prometheus_master'
    scrape_interval: 5s
    static_configs:
      - targets: ['0.0.0.0:9090']
  - job_name: 'postgresql_exporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['192.168.198.142:9100']



`chown prometheus:prometheus /etc/prometheus/prometheus.yml`


`vim /etc/systemd/system/prometheus.service`

![image](https://user-images.githubusercontent.com/98477908/213886489-3ed0805a-2b9d-4a52-880e-967ec0bac5aa.png)


[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
--config.file /etc/prometheus/prometheus.yml \
--storage.tsdb.path /var/lib/prometheus/ \
--web.console.templates=/etc/prometheus/consoles \
--web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target


`systemctl daemon-reload`

`systemctl start prometheus`

`systemctl status prometheus`

![image](https://user-images.githubusercontent.com/98477908/213886507-1ee6801c-692d-4443-beb0-4d6921c0de9d.png)

 ![image](https://user-images.githubusercontent.com/98477908/213886306-09abba0e-8f14-4ce7-a5f5-b4d74f1a2dde.png)



## Install grafana

`sudo nano /etc/yum.repos.d/grafana.repo`

[grafana]
name=grafana
baseurl=https://rpm.grafana.com
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://rpm.grafana.com/gpg.key
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt



`sudo yum install grafana`


`rpm -qa | grep grafana`

`systemctl status grafana-server.service`


![image](https://user-images.githubusercontent.com/98477908/213886538-b914f937-ff6b-4372-9653-f7532c89da8f.png)


##Open up grafana dashboard 

`https://IP:3000`

![image](https://user-images.githubusercontent.com/98477908/213886846-131e1f43-4de0-4d41-bdcd-08a3d9382402.png)

## Setup data sources first 

![image](https://user-images.githubusercontent.com/98477908/213886926-a25be759-ba70-44d5-aba5-ac690f18face.png)

![image](https://user-images.githubusercontent.com/98477908/213886932-56c0823c-2740-404c-9930-f1320f02a506.png)



## Import dashboard json ID
![image](https://user-images.githubusercontent.com/98477908/213886884-be7c67d9-4faf-49d8-8f44-6d0d92032d37.png)

Select the promethus data source

![image](https://user-images.githubusercontent.com/98477908/213886976-dcb171b2-64d7-471b-a576-f43b5c823457.png)


Wait for some time till the enough data populated

![image](https://user-images.githubusercontent.com/98477908/213887409-0863f337-06e4-41f0-b705-9303098e9839.png)


![image](https://user-images.githubusercontent.com/98477908/213887318-1afb5b1d-8665-4a6e-9695-6116429ac449.png)
