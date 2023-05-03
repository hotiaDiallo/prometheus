# Prometheus installation

- Download `Prometheus` binary version `2.40.1` from `https://prometheus.io/download/`.

    ```
    wget https://github.com/prometheus/prometheus/releases/download/v2.40.1/prometheus-2.40.1.linux-amd64.tar.gz
    ```
- Unarchive the same and start the `Prometheus` server process using Prometheus binary.
    ```
    tar xvf prometheus-2.40.1.linux-amd64.tar.gz
    ```
- Start 
    ```
    cd prometheus-2.40.1.linux-amd64
    ./prometheus > /dev/null 2>&1 &
    ```

We rted the Prometheus process using the Prometheus binary.

Now, let's set up `Prometheus` and create a `systemd` service unit file to manage the Prometheus service. 

- Add Prometheus user as below:

```
useradd --no-create-home --shell /bin false prometheus
```
- Create Directories for storing prometheus config file and data:

```
mkdir /etc/prometheus
mkdir /var/lib/prometheus
```

- Change the owner of the above directories
```
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus
```

- Copy the binaries (assuming you already downloaded the Prometheus package)
```
cp /root/prometheus-2.40.1.linux-amd64/prometheus /usr/local/bin/
cp /root/prometheus-2.40.1.linux-amd64/promtool /usr/local/bin/
```
- Change the ownership of binaries to Prometheus user:
```
chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool
```

- Copy the directories `consoles` and `console_libraries` to “/etc/prometheus folder”:
```
cp -r /root/prometheus-2.40.1.linux-amd64/consoles /etc/prometheus
cp -r /root/prometheus-2.40.1.linux-amd64/console_libraries /etc/prometheus
```

- Change the ownership of directories `consoles` and `console_libraries`:
```
chown -R prometheus:prometheus /etc/prometheus/consoles
chown -R prometheus:prometheus /etc/prometheus/console_libraries
```

- Move `prometheus.yml` file to `/etc/prometheus` directory:
```
cp /root/prometheus-2.40.1.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml
```

- Change the ownership of file `/etc/prometheus/prometheus.yml`:
```
chown prometheus:prometheus /etc/prometheus/prometheus.yml
```

- Create a service for prometheus:
```
vi /etc/systemd/system/prometheus.service
```

- Add below lines in it:
```
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
```

- Run below commands to Reload the systemd service et Start the Prometheus service.
```
systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
systemctl status prometheus
```




