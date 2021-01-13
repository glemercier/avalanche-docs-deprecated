# Setting up Node Monitoring

## Acknowledgements

Thank you to community member Jovica Popović, who wrote this tutorial.
You can reach him on [Avalanche's Discord](https://discordapp.com/invite/Ja3CSs7) if needed.

## Introduction

This tutorial assumes you have Ubuntu 18.04 or 20.04 running on your node. A Mac OS X version of this tutorial will come later.

This tutorial will show how to set up infrastructure to monitor an instance of [AvalancheGo](https://github.com/ava-labs/avalanchego).
We will use:

* [Prometheus](https://prometheus.io/) to gather and store data
* [node_exporter](https://github.com/prometheus/node_exporter) to get information about the machine, 
* AvalancheGo's [metrics API](https://docs.avax.network/v1.0/en/api/metrics/) to get information about the node
* [Grafana](https://grafana.com/) to visualize data on a dashboard.

Prerequisites:

* A running AvalancheGo node
* Shell access to the machine running the node
* Administrator privileges on the machine
 
## Set up Prometheus

First we need to add a system user account and create directories (you will need superuser credentials):

```
sudo useradd -M -r -s /bin/false prometheus
sudo mkdir /etc/prometheus /var/lib/prometheus
```

Next, get the link to latest version of Prometheus from the [downloads page](https://prometheus.io/download/) (make sure you select the appropriate processor architecture) and use wget to download it, and tar to unpack the archive:

```
mkdir -p /tmp/prometheus && cd /tmp/prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.21.0/prometheus-2.21.0.linux-amd64.tar.gz
tar xvf prometheus-2.21.0.linux-amd64.tar.gz
cd prometheus-2.21.0.linux-amd64
```

Next, we need to move the binaries, set ownership and move config files to appropriate locations:

```
sudo cp {prometheus,promtool} /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/{prometheus,promtool}
sudo chown -R prometheus:prometheus /etc/prometheus
sudo chown prometheus:prometheus /var/lib/prometheus
sudo cp -r {consoles,console_libraries} /etc/prometheus/
sudo cp prometheus.yml /etc/prometheus/
```

`/etc/prometheus` is used for configuration, and `/var/lib/prometheus` for data.

Let's set up Prometheus to run as a system service.
Do `sudo nano /etc/systemd/system/prometheus.service` (or open that file in the text editor of your choice)
and enter the following configuration:

```
[Unit]
Description=Prometheus
Documentation=https://prometheus.io/docs/introduction/overview/
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus
ExecReload=/bin/kill -HUP $MAINPID
ExecStart=/usr/local/bin/prometheus   --config.file=/etc/prometheus/prometheus.yml   --storage.tsdb.path=/var/lib/prometheus   --web.console.templates=/
etc/prometheus/consoles   --web.console.libraries=/etc/prometheus/console_libraries   --web.listen-address=0.0.0.0:9090   --web.external-url=

SyslogIdentifier=prometheus
Restart=always

[Install]
WantedBy=multi-user.target
```

Save the file. Now we can run Prometheus as a system service:

```
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus
```

Prometheus should now be running. To make sure, we can check with:

```
systemctl status prometheus 
```

which should produce something like:

```
● prometheus.service - Prometheus
     Loaded: loaded (/etc/systemd/system/prometheus.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2020-04-01 19:23:53 CEST; 5 months 12 days ago
       Docs: https://prometheus.io/docs/introduction/overview/
   Main PID: 1767 (prometheus)
      Tasks: 12 (limit: 9255)
     CGroup: /system.slice/prometheus.service
             └─1767 /usr/local/bin/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/var/lib/prometheus --web.console.templa>

Sep 13 13:00:04 ubuntu prometheus[1767]: level=info ts=2020-09-13T11:00:04.744Z caller=head.go:792 component=tsdb msg="Head GC completed" duration=13.6>
Sep 13 13:00:05 ubuntu prometheus[1767]: level=info ts=2020-09-13T11:00:05.263Z caller=head.go:869 component=tsdb msg="WAL checkpoint complete" first=9>
Sep 13 15:00:04 ubuntu prometheus[1767]: level=info ts=2020-09-13T13:00:04.776Z caller=compact.go:495 component=tsdb msg="write block" mint=15999912000>
...
```

You can also check Prometheus web interface, available on `http://your-node-host-ip:9090/` (you may need to do `sudo ufw allow 9090/tcp` if the firewall is on).
 
## Install Grafana
To set up Grafana project repositories with Ubuntu:

```
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

To install Grafana:

```
sudo apt-get update
sudo apt-get install grafana
```

To configure it as a service:

```
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl enable grafana-server.service
```

To make sure it's running properly:
```
sudo systemctl status grafana-server
```

which should show grafana as `active`. Grafana should now be available at `http://your-node-host-ip:3000/` (again, open the port if needed). 
Log in with username/password admin/admin and set up a new, secure password.** Now we need to connect Grafana to our data source, Prometheus.

On Grafana's web interface:

* Go to Configuration on the left-side menu and select Data Sources.
* Click Add Data Source
* Select Prometheus.
* In the form, enter the name (Prometheus will do), and `http://localhost:9090` as the URL. 
* Click `Save & Test`
* Check for "Data source is working" green message.
  
## Set up node_exporter

In addition to metrics from AvalancheGo, let's set up up monitoring of the machine itself, so we can check CPU, memory, network and disk usage and be aware of any anomalies.
For that, we will use node_exporter, a Prometheus plugin. 

Get the latest version with:

```
curl -s https://api.github.com/repos/prometheus/node_exporter/releases/latest | grep browser_download_url | grep linux-amd64 |  cut -d '"' -f 4 | wget -qi -
```

change `linux-amd64` if you have a different architecture (RaspberryPi is `linux-arm64`, for example).
Untar and move the executable:

```
tar xvf node_exporter-1.0.1.linux-amd64.tar.gz
sudo mv node_exporter-1.0.1.linux-amd64/node_exporter /usr/local/bin
node_exporter --version
```

Then we add node_exporter as a service. 
Do `sudo nano /etc/systemd/system/node_exporter.service` (or open that file in the text editor of your choice) and populate it with:

```
[Unit]
Description=Prometheus
Documentation=https://github.com/prometheus/node_exporter
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus
ExecReload=/bin/kill -HUP $MAINPID
ExecStart=/usr/local/bin/node_exporter \
    --collector.cpu \
    --collector.diskstats \
    --collector.filesystem \
    --collector.loadavg \
    --collector.meminfo \
    --collector.filefd \
    --collector.netdev \
    --collector.stat \
    --collector.netstat \
    --collector.systemd \
    --collector.uname \
    --collector.vmstat \
    --collector.time \
    --collector.mdadm \
    --collector.zfs \
    --collector.tcpstat \
    --collector.bonding \
    --collector.hwmon \
    --collector.arp \
    --web.listen-address=:9100 \
    --web.telemetry-path="/metrics"

[Install]
WantedBy=multi-user.target
```

This configures node_exporter to collect various data we might find interesting.
Start the service, and enable it on boot:

```
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
sudo systemctl status node_exporter
```

Now we're ready to tie it all together.

## Configure AvalancheGo and node_exporter Prometheus jobs

Make sure that your AvalancheGo node is running with appropriate [command line arguments](https://docs.avax.network/v1.0/en/references/command-line-interface/).
The metrics API must be enabled (by default, it is).
If you use CLI argument `--http-host` to make API calls from outside of the host machine, make note of the address at which APIs listen.

We now need to define an appropriate Prometheus jobs. Let's edit Prometheus configuration:

Do `sudo nano /etc/prometheus/prometheus.yml` (or open that file in the text editor of your choice) and append to the end:

```
  - job_name: 'avalanchego'
    metrics_path: '/ext/metrics'
    static_configs:
      - targets: ['<your-host-ip>:9650']

  - job_name: 'avalanchego-machine'
    static_configs:
      - targets: ['<your-host-ip>:9100']
        labels:
          alias: 'machine'
```

**Indentation is important**.
Make sure `-job_name` is aligned with existing `-job_name entry`, and other lines are also indented properly.
Make sure you use the correct host IP, or `localhost`, depending how you node is configured. 

Save the config file and restart Prometheus:

`sudo systemctl restart prometheus`

Check Prometheus web interface on `http://your-node-host-ip:9090/targets`. You should see three targets enabled:

* Prometheus
* avalanchego
* avalanchego-machine

Open Grafana; you can now create a dashboard using any of those sources.
You can also use the preconfigured dashboards [here].(https://github.com/ava-labs/node-monitoring/tree/master/dashboards)

To import the preconfigured dashboard:

* Open Grafana's web interface
* Click `+` on the left toolbar
* Select `Import JSON` and then upload the JSON file

That's it! You may now marvel at all the things your node does. Woohoo!

## Caveat: Security

The system as described here **should not** be opened to the public internet.
Neither Prometheus, nor Grafana as shown here are hardened against unauthorized access. 
Make sure that both of them are accessible only over a secured proxy, local network, or VPN.
Setting that up is beyond the scope of this tutorial, but exercise caution.
Bad security practices could lead to attackers gaining control over your node!
It is your responsibility to follow proper security practices.

## Contributions

Basis for the Grafana dashboard was taken from the good guys at [ColmenaLabs](https://blog.colmenalabs.org/index.html), which is apparently not available any more.
If you have ideas and suggestions on how to improve this tutorial, please say so, post an issue, or make a pull request.