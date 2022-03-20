### Dependencies

- PowerDNS Authoritative Server + remote backend.
- memcache
- Python3.4.3+
- pyyaml
- python-memcached
- python-daemon-3K

#### RHEL 8 dependencies installation example
```
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
yum install python3 python3-pip python3-pyyaml python3-memcached python3-daemon
yum install pdns pdns-backend-remote memcached git
```

### Install Polaris GSLB
```shell
git clone https://github.com/gribnut/polaris-gslb.git
cd polaris-gslb
python3 setup.py install
mv /etc/pdns/pdns.conf /etc/pdns/pdns.conf.orig
cp /opt/polaris/etc/pdns.conf.dist /etc/pdns/pdns.conf
```

The following files are created:
```
/opt/polaris/
├── bin
│   ├── check-pdns
│   ├── polaris-health
│   ├── polaris-health-control
│   ├── polaris-memcache-control
│   ├── polaris-pdns
│   └── polaris-pdns-control
├── etc
│   ├── pdns.conf.dist
│   ├── polaris-health.yaml.dist
│   ├── polaris-lb.yaml.dist
│   ├── polaris-pdns.yaml.dist
│   └── polaris-topology.yaml.dist
└── run

/etc/default/polaris
```

Ensure /etc/default/polaris has a correct PATH to py3 executable, e.g.:
```shell
export PATH=$PATH:/opt/python3/bin
export POLARIS_INSTALL_PREFIX=/opt/polaris
```

Install the Polaris-specific PDNS configuration file
```shell
cp /opt/polaris/etc/pdns.conf.dist /etc/pdns/pdns.conf
```

Copy /opt/polaris/etc/*.dist files into *.yaml or create new ones and configure:

- polaris-lb.yaml - LB configuration
- polaris-topology.yaml - topology configuration
- polaris-health.yaml - Polaris health general parameters
- polaris-pdns.yaml - Polaris PDNS general parameters

Start pdns

Use `/opt/polaris/bin/polaris-health [status|start|start-debug|stop|restart]` to control the Polaris health application.

`/opt/polaris/bin/polaris-memcache-control 127.0.0.1 [get-generic-state|get-ppdns-state|get-heartbeat|check-heartbeat]` can be used to display various memcache values set by Polaris.
* `get-ppdns-state` shows the state table used for query distribution
* `get-generic-state` shows the full health table