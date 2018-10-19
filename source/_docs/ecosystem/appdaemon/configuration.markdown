---
layout: page
title: "Configuration"
description: "AppDaemon Configuration"
release_date: 2016-11-27 08:00:00 -0500
sidebar: true
comments: false
sharing: true
footer: true
redirect_from: /ecosystem/appdaemon/configuration/
---

When you have appdaemon installed by either method, copy the `conf/appdaemon.cfg.example` file to `conf/appdaemon.cfg`, then edit the `[AppDaemon]` section to reflect your environment:

```
secrets: /some/path
log:
  errorfile: /export/hass/appdaemon_test/logs/error.log
  logfile: /export/hass/appdaemon_test/logs/appdaemon.log
  log_generations: 3
  log_size: 1000000
appdaemon:
  threads: 10
  time_zone: <time zone>
  app_dir = <Path to appdaemon dir>/conf/apps
  api_port: 5000
  api_key: !secret api_key
  api_ssl_certificate: <path/to/root/CA/cert>
  api_ssl_key: <path/to/root/CA/key>
  plugins:
    HASS:
      type: hass
      ha_url: <some_url>
      ha_key: <some key>
      cert_path: <path/to/root/CA/cert>
      cert_verify: True
```

- `secrets` the use of secrets in the configuration file, to allow separate storage of sensitive information such as passwords. This  section is optional. If it doesn’t exist, AppDaemon looks for a file called secrets.yaml in the config directory
- `logfile` (optional) is the path to where you want `AppDaemon` to keep its main log. When run from the command line this is not used - log messages come out on the terminal. When running as a daemon this is where the log information will go. In the example above I created a directory specifically for AppDaemon to run from, although there is no reason you can't keep it in the `appdaemon` directory of the cloned repository. If `logfile = STDOUT`, output will be sent to stdout instead of stderr when running in the foreground, if not specified, output will be sent to STDOUT.
- `errorfile` (optional) is the name of the logfile for errors - this will usually be errors during compilation and execution of the apps. If `errorfile = STDERR` errors will be sent to stderr instead of a file, if not specified, output will be sent to STDERR.
- `time_zone` (optional) - timezone for AppDaemon to use. If not specified, AppDaemon will query the timezone from Home Assistant
- `app_dir` (optional) is the directory the apps are placed in. If not specified, AppDaemon will look first in `~/.homeassistant` then `/etc/appdaemon` for a subdirectory named `apps`
- `api_key` (optional) - adds the requirement for AppDaemon API calls to provide a key in the header of a request
- `api_ssl_certificate` (optional) - certificate to use when running the API over SSL
- `api_ssl_key` (optional) - key to use when running the API over SSL
- `ha_url` is a reference to your Home Assistant installation and must include the correct port number and scheme (`http://` or `https://` as appropriate)
- `ha_key` should be set to your key if you have one, otherwise it can be removed.
- `cert_path` (optional) - path to root CA cert directory - use only if you are using self signed certs.
- `cert_verify` (optional) - flag for cert verification for HASS - set to False to disable verification on self signed certs, or certs for which the address used doesn't match the cert address (e.g. using an internal IP address)

The `#Apps` section is the configuration for the Hello World program and should be left in place for initial testing but can be removed later if desired, as other Apps are added, App configuration is described in the [API doc](API.md).

## {% linkable_title Docker %}

For Docker Configuration you need to take a couple of extra things into consideration.

Our Docker image is designed to load your configuration and apps from a volume at `/conf` so that you can manage them in your own git repository, or place them anywhere else on the system and map them using the Docker command line.

For example, if you have a local repository in `/Users/foo/ha-config` containing the following files:

```bash
$ git ls-files
configuration.yaml
customize.yaml
known_devices.yaml
appdaemon.cfg
apps
apps/magic.py
```

You will need to modify the `appdaemon.cfg` file to point to these apps in `/conf/apps`:

```
[AppDaemon]
ha_url = <some_url>
ha_key = <some key>
logfile = STDOUT
errorfile = STDERR
app_dir = /conf/apps
threads = 10
latitude = <latitude>
longitude = <longitude>
elevation = <elevation
timezone = <timezone>
```

You can run Docker and point the conf volume to that directory.
