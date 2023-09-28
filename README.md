# Prometheus & Grafana
Prometheus, Grafana, node exporter, blackbox exporter, AlertManager

# Prometheus

**Prometheus** is a time series database, created in 2012 and part of the Cloud Native Computing Foundation, that exposes dozens of exporters for you to monitor anything.

On the other hand, Grafana is probably one of the most popular monitoring tools. In Grafana, you create dashboards that bind to datasources (such as Prometheus) in order to visualize your metrics in near real-time.

Grafana & Prometheus natively bind together.

- prometheus.yml: the configuration file for Prometheus. This is the file that you are going to modify in order to tweak your Prometheus server, for example to change the scraping interval or to configure custom alerts;
- prometheus: the binary for your Prometheus server. This is the command that you are going to execute to launch a Prometheus instance on your Linux box;
- promtool: this is a command that you can run to verify your Prometheus configuration.

We are not going to execute directly the Prometheus, instead we are going to configure it as a service.

It will bring more robustness and reliability in case our Prometheus server were to stop suddenly.

```bash
sudo nano /usr/local/bin/prometheus/promtool

sudo nano /etc/prometheus
```

Folder in the `/etc` folder for Prometheus and the console files, console libraries and the prometheus configuration file.

A data folder at the root directory, with a prometheus folder inside `data/prometheus`

Set to create a Prometheus service.
```bash
sudo nano /lib/systemd/system/prometheus.service


[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path="/data/prometheus" \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-admin-api

Restart=always

[Install]
WantedBy=multi-user.target
```
In order to see the different launch options for Prometheus, you can run the prometheus command with a h flag.
```bash
prometheus -h
```

```bash
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

Prometheus server should be running at http://localhost:9090.

By default, Prometheus should start monitoring itself.

Status -> Targets in top bar menu, to verify that you have one target : the Prometheus server itself.

```bash
```
```bash
```
```bash
```
![AlertManager]( "AlertManager")

![AlertManager]( "AlertManager")

![AlertManager]( "AlertManager")

![AlertManager]( "AlertManager")

![AlertManager]( "AlertManager")

![AlertManager]( "AlertManager")

# Grafana 

**Grafana** is by far one of the most popular open source dashboard monitoring tools. 

It can be used to monitor a wide variety of different datasources : SQL databases (MySQL or PostgreSQL), NoSQL databases and time series databases (such as InfluxDB or TimescaleDB)

Used by SpaceX, Bloomberg and Booking.com, Grafana is definitely a must-have for engineers wanting a robust and scalable dashboard monitoring solution.

```bash
sudo wget -q -O - https://packages.grafana.com/gpg.key | apt-key add -
sudo apt-key list

pub   rsa2048 2017-01-24 [SC]
      4E40 DDF6 D76E 284A 4A67  80E4 8C8C 34C5 2409 8CB6
uid           [unknown] Grafana <info@grafana.com>
sub   rsa2048 2017-01-24 [E]


sudo apt-get install grafana
```
```bash
sudo nano /usr/lib/systemd/system/grafana-server.service
```
This file is quite important because it gives you much more information about your Grafana instance.


- The Grafana server binary is located at /usr/sbin/grafana-server.
- The file that defines all the environment variables is located at /etc/default/grafana-server
- The configuration file is given via the CONF_FILE environment variable.
- The PID of the file is also determined by the PID_FILE_DIR environment variable.
- Logging, data, plugins and provisioning paths are given by environment variables.

Content of the environment variable file:
```bash
GRAFANA_USER=grafana

GRAFANA_GROUP=grafana

GRAFANA_HOME=/usr/share/grafana

LOG_DIR=/var/log/grafana

DATA_DIR=/var/lib/grafana

MAX_OPEN_FILES=10000

CONF_DIR=/etc/grafana

CONF_FILE=/etc/grafana/grafana.ini

RESTART_ON_UPGRADE=true

PLUGINS_DIR=/var/lib/grafana/plugins

PROVISIONING_CFG_DIR=/etc/grafana/provisioning

# Only used on systemd systems
PID_FILE_DIR=/var/run/grafana
```
Now that all variables are declared, and that you configured your paths relatively to the way you organize data on your instance, you can launch your service.

```bash
sudo systemctl start grafana-server
sudo systemctl status grafana-server
```

### Grafana Web UI.

http://localhost:3000.

As a reminder, here are all the defaults for Grafana:

The default port for Grafana is 3000.

The default login for Grafana is ‘admin’ and the default password is also ‘admin’.

When setting those credentials, you will be asked to change your password.


# Blackbox Exporter

**The Blackbox exporter** (probing exporter) is a tool that allows engineers to monitor network HTTP, DNS, TCP and ICMP endpoints. Results can be visualized in Grafana's dashboard.

- ping commands in order to check the availability of services.
- to check your website uptime, if website is accessible and if it is loading fast for users.
- to resolve DNS response times to diagnose network latency issues.
- to have an extensive monitoring of ICMP requests in order to gather more information about network health.

#

- The Blackbox exporter provides metrics about HTTP latencies, DNS lookups latencies as well as statistics about SSL certificates expiration.

- The Blackbox exporter is mainly used to measure response times.

- For more detailed metrics for your application (for example a Python application), you will have to instrument your application.

- The Blackbox exporter can be bound with the AlertManager and Prometheus in order to have detailed alerts when one endpoint goes down.

- When running, the Blackbox exporter is going to expose a HTTP endpoint that can be used in order to monitor targets over the network. By default, the Blackbox exporter exposes the /probe endpoint that is used to retrieve those metrics.

**For example**, if my Blackbox exporter is running on port **9115**, and if I query metrics for google.com, this is the **endpoint** that I can query from the exporter.

The Blackbox exporter is a standalone tool, it does not need any other tools to run.

On the other hand, Prometheus binds to the exporter. You are going to define ‘targets’ in a dedicated Blackbox configuration section, and Prometheus will issue requests to the probe endpoint we saw earlier.

Prometheus is acting as a way to automate requests and as a way to store them for long-term storage.

### Blackbox modules

[Blackbox exporter configuration](https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md)

As most of the Prometheus ecosystem tools, the blackbox exporter is configured with a YAML configuration file.

The Blackbox exporter configuration file is made of modules.

A module can be seen as one probing configuration for the Blackbox exporter.

As a consequence, if you choose to have a HTTP prober checking for successful HTTPS responses (2xx HTTP codes for example), the configuration will be summarized within the same module.

 As you can see, you have many options for all the probers that are available to you.

Monitoring endpoints availability is either a success of a failure.

As a consequence, the Blackbox exporter will report if it successfully probed the targets it was assigned (0 for a failure and 1 for a success).

Given the value of the probing, you can choose to define alerts in order to be notified (on Slack for example) when an endpoint goes down.

### Difference of Blackbox exporter from application instrumenting

Application instrumenting means that you are adding client libraries to an application in order to expose metrics to Prometheus.

Client libraries are available for most programming languages and frameworks such as Go, Python, Java, Javascript or Ruby.

**The main difference** between the Blackbox exporter and application instrumenting is that the Blackbox exporter only focuses on availability while instrumentations can go more into details about performance.

With instrumentation, you can choose to monitor the performance of a single SQL query for example, or a single server function.

The Blackbox exporter will not be able to expose this granularity, only a request failure or a request success.

- blackbox_exporter: the executable for the Blackbox exporter. This is the executable that you are going to launch via your service file in order to probe targets;
- blackbox.yml: configuration file for the Blackbox exporter. This is where you are going to define your modules and probers.

Documentation for the blackbox_exporter executable is available when running the following command:
```bash
./blackbox_exporter -h
```
To define service files and define start and restart policies for it - make your executables accessible for your user local account. `$ sudo mv blackbox_exporter /usr/local/bin`
Note : by moving files to the /usr/local/bin path, all users may have access to the blackbox exporter binary. For binaries to be restricted to your own user account, you need to add the path to your own PATH environment variable.

Configuration folders for your blackbox exporter:
```bash
sudo nano /etc/blackbox/blackbox.yml
```
Blackbox exporter service:
```bash
sudo nano /lib/systemd/system/blackbox.service
```
```bash
[Unit]
Description=Blackbox Exporter Service
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=blackbox
Group=blackbox
ExecStart=/usr/local/bin/blackbox_exporter \
  --config.file=/etc/blackbox/blackbox.yml \
  --web.listen-address=":9115"

Restart=always

[Install]
WantedBy=multi-user.target
```
The Blackbox exporter service has no dependencies to other tools of the Prometheus ecosystem or to Prometheus itself.
```bash
sudo systemctl enable blackbox.service
sudo systemctl start blackbox.service
```
If service is correctly running, you can check the metrics gathered by issuing a request to the HTTP API.
```bash
curl http://localhost:9115/metrics
```
For now the Blackbox exporter is not configured to scrape any targets, but we are going to add a few ones.

### Binding the Blackbox exporter with Prometheus
Prometheus is going to scrape the Blackbox Exporter to gather metrics about the exporter itself.

To bind the Blackbox exporter with Prometheus, you need to add it as a scrape target in Prometheus configuration file at `/etc/prometheus/prometheus.yml`
```bash
sudo nano /etc/prometheus/prometheus.yml
```
```bash
global:
  scrape_interval:     15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090', 'localhost:9115']
```
With Prometheus, you don’t need to restart the systemd service for Prometheus to update.

You can also **send a signal to the process for it to restart**.

In order to send a SIGHUP signal to Prometheus process, identify the PID of Prometheus server.
```bash
ps aux | grep prometheus

schkn  4431  0.0  0.0  14856  1136 pts/0    S+   09:34   0:00 /bin/prometheus --config.file=...
```
The PID of this Prometheus process is 4431. Send a SIGHUP signal to this process for the configuration to restart.

```bash
sudo kill -HUP 4431
```
Head over to Prometheus target configuration (http://localhost:9090/targets), to check that you are correctly scrapping your Blackbox exporter.

To add first target.

- to monitor the HTTP endpoint of Prometheus itself with the Blackbox exporter.

### Monitoring HTTPS endpoints with the Blackbox Exporter
In my setup, Prometheus is now currently sitting behind a reverse proxy (NGINX) configured with self signed certificates.

This is the endpoint we are going to monitor with the Blackbox Exporter.

To monitor Prometheus, we are going to use the HTTP prober.

Head over to your Blackbox configuration file, erase its content and paste the following configuration.
```bash
modules:
  http_prometheus:
    prober: http
    timeout: 5s
    http:
      valid_http_versions: ["HTTP/1.1", "HTTP/2"]
      method: GET
      fail_if_ssl: false
      fail_if_not_ssl: true
      tls_config:
        insecure_skip_verify: true
      basic_auth:
        username: "username"
        password: "password"
```
- fail_if_not_ssl: as we are actively monitoring a HTTPS endpoint, we need to make sure that we are retrieving the page with SSL encryption. Otherwise, we count it as a failure;
- insecure_skip_verify: if you followed our previous tutorial, we generated our certificates with self-signed certificates. As a consequence, you are not able to verify it with a certificate authority;
- basic_auth: the reverse proxy endpoint is configured with a basic username/password authentication. The Blackbox exporter needs to be aware of those to probe the Prometheus server.

To check your configuration file with the blackbox binary itself.

```bash
blackbox_exporter --config.check
```

Again, there is no need to restart the Blackbox Exporter service. Instead, send a simple SIGHUP signal to the process. Similarly to the Prometheus process, identify the Blackbox exporter PID.

```bash
ps aux | grep blackbox
devconnected 574  0.0  0.0  14856  1136 pts/0    S+   09:34   0:00 /usr/local/bin/blackbox_exporter --config.file=...
```
The PID of my Prometheus process is 574. Send a SIGHUP signal to this process for the configuration to restart.

```bash
sudo kill -HUP 574
```
### Binding the Blackbox Exporter Module in Prometheus
Now that the module is defined, it is time for Prometheus to start actively using it to monitor our target. Head over to Prometheus configuration file, and paste the following changes.
```bash
scrape_configs:

...

  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_prometheus] 
    static_configs:
      - targets:
        - https://127.0.0.1:1234    # Target to probe with https.
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 127.0.0.1:9115  # The blackbox exporter's real hostname:port.
```
Save your changes, and send a SIGHUP signal to Prometheus for it to restart. When your server has restarted, head over to https://localhost:1234/config and make sure that your changes were correctly saved.

Prometheus will start scrapping our target now.

To verify it, head over to the `/graph` endpoint, and issue the following PromQL request.

```bash
probe_success{instance="https://127.0.0.1:1234", job="blackbox"}
```
If your target is up, the probe success should return 1.

Grafana dashboard (dashboard ID : 7587) focuses on HTTP(s) metrics.

You can have metrics about the up status of your website, the current SSL status as well as the SSL expiry date.

You also have graphs showing the current latencies of your HTTP requests, as well as the average DNS lookup time.

It is noteworthy to say that you can choose the target that you want to monitor by clicking on the “Target” dropdown at the top of the dashboard. In our case, we have only one target, but it can become quite handy when you are monitoring several hosts.


# AlertManager

**The AlertManager** is an alerting server that handles alerts provided by a set of clients (a Prometheus server for example) and dispatches them to a group of defined receivers (Slack, email or Pagerduty for example).

The AlertManager is part of the Prometheus stack, but it is run as a standalone server aside from Prometheus.

By default, Prometheus will take care of sending alerts directly to the AlertManager if it is correctly configured as a Prometheus target.

If you are using clients different from Prometheus itself, the AlertManager exposes a set of REST endpoints that you can use to fire alerts.

![AlertManager](https://devconnected.com/wp-content/uploads/2019/08/alert-manager-works.png "AlertManager")

The AlertManager works with configuration files defined in YAML format.

At the top of your configuration file, you are going to define routes.

Routes are a set of paths that alerts take in order to determine which action should be associated with the alert. In short, you associate a route with a receiver.

The initial route, also called the “root route” is a route that matches every single alert sent to the AlertManager.

A route can have siblings and children that are also routes themselves. This way, routes can be nested any number of times, each level defining a new action (or receiver) for the alert.

Each route defines receivers. Those receivers are the alert recipients : Slack, a mail service, Pagerduty...

![AlertManager](https://devconnected.com/wp-content/uploads/2019/08/New-Wireframe-1-copy-5-768x871.png "AlertManager")

On each route, you can define a continue attribute.

The continue attribute is a value used to define if you want to evaluate route siblings (belonging to the same level) if a route on the same level was already matching.

Note that the continue attribute is not used to determine if you want to go accross children routes, but only siblings routes.

The AlertManager will evaluate children routes until there are no routes left or no routes for a given level are matching the current alert.

In that case, the AlertManager will take the configuration of the current node evaluated.

![AlertManager](https://devconnected.com/wp-content/uploads/2019/08/continue-attribute-1.png "AlertManager")

- amtool: the amtool is an executable that allows you to view or to modify the current state of the AlertManager. In other words, the amtool can silence alerts, expire silences, as well as import silences or query them. It can be seen as a utility to customize the AlertManager without directly modifying the configuration of your current alerts.
- alertmanager: the executable for the alertmanager. This is the executable that you will run in order to start an AlertManager server on your instance.
- alertmanager.yml: as its name suggests, this is the configuration file for the AlertManager. This configuration file already defines some example routes, but we will create our own alert file.

In order to start the AlertManager as a service, you are going to move the executables to the /usr/local/bin folder. For the configuration files, create a new folder in `/etc` called alertmanager. Create a data folder at the root directory, with a prometheus folder inside. `$ sudo mkdir -p /data/alertmanager`  To create a Linux service (using systemd), head over to the `/lib/systemd/system folder` and create a service named `alertmanager.service`

AlertManager service:
```bash
sudo nano /lib/systemd/system/alertmanager.service 
```
Similarly to our Prometheus, let’s first run the alertmanager executable with a “-h” flag to see our options. `$ alertmanager -h`
- config.file: we need to set this variable to the correct configuration file of the “etc/alertmanager” folder.
- storage.path: again, we defined a custom folder for data which is “/data/alertmanager”
- web.external-url: if you followed the Prometheus setup entirely, your Prometheus instance is running behind a reverse proxy. In this case, we are going to set the URL for the AlertManager to be externally reachable. Note that this step is optional, but if you plan on reaching the AlertManager from the outside world, there is a chapter dedicated to it.
```bash
sudo systemctl enable alertmanager
sudo systemctl start alertmanager
```
Binding AlertManager with Prometheus - in Prometheus configuration directory:
```bash
sudo nano /etc/prometheus/prometheus.yml
sudo systemctl restart prometheus
```
By default, the AlertManager is running on port 9093 (http://localhost:9093).



# node-cert exporter

**The node-cert exporter** is an exporter that will periodically check your SSL certificates given a set of filesystem paths.

It is highly recommended to store your SSL certificates in the /etc/ssl folder, but you may store them in different places.

As always, we are going to install the exporter as a service.

Running exporters as a background process is the best way to crash them. Plus, you can monitor your systemd services and be notified when one goes down.

The node-cert exporter uses the **path** flag to specify where your certificates are located on your system.
```bash
sudo nano /lib/systemd/system/node-cert-exporter.service
```

    --path=/etc/ssl/prometheus
```bash
sudo systemctl enable node-cert-exporter
sudo systemctl start node-cert-exporter
```

Now that node-cert exporter is correctly running, need to bind it to Prometheus. To do so, navigate to Prometheus configuration file, and start editing it to add the following targets.

Prometheus configuration file:
```bash
sudo nano /etc/prometheus/prometheus.yml
```

In the **static_configs** part of configuration file, add a new entry for the cert exporter.
```bash
static_configs:
            - targets: ['localhost:9090', 'localhost:9117']
```
```bash
sudo systemctl restart prometheus
```

Customizing the threshold levels:

Some thresholds are already defined in this Grafana dashboard. By default, red lines will be displayed for SSL certificates that need to be renewed in less than three months, orange lines between three months and six months, and green lines for more than six months.

To store all alert files (will fire an alert when you need to check your SSL certificate (one month away from being expired)):
```bash
sudo nano /etc/prometheus/alerts/ssl_rules.yml
```
```bash
groups: 
  - name: ssl_expiry.rules 
    rules: 
      - alert: SSLCertExpiringSoon 
        expr: sum(ssl_certificate_expiry_seconds{}) by (instance, path) < 86400 * 30 
        for: 10m
```
As a reminder, the Prometheus server is going to be used as a client for the Alert Manager.

Create a rules file for the AlertManager:
```bash
sudo nano /etc/alertmanager/alertmanager.yml
```

```bash
global:
  resolve_timeout: 5m

route:
 group_by: ['SSLCertExpiringSoon']
 group_wait: 10s
 group_interval: 10s
 repeat_interval: 1h
 receiver: slack
 

receivers:
- name: slack
  slack_configs:
  - api_url: $WEBHOOK_URL
    channel: '#devconnecteds-blog'
```
```bash
sudo systemctl restart alertmanager
```

# 
# Sources

[How to Setup Grafana and Prometheus on Linux](https://devconnected.com/how-to-setup-grafana-and-prometheus-on-linux/)

[How To Install Grafana on Ubuntu](https://devconnected.com/how-to-install-grafana-on-ubuntu-18-04/)

[How To Install and Configure Blackbox Exporter for Prometheus](https://devconnected.com/how-to-install-and-configure-blackbox-exporter-for-prometheus/)

[AlertManager and Prometheus Complete Setup on Linux](https://devconnected.com/alertmanager-and-prometheus-complete-setup-on-linux/)

[How To Check SSL Certificate Expiration with Grafana](https://devconnected.com/how-to-check-ssl-certificate-expiration-with-grafana/)
