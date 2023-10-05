- [Description](#description)
- [Requirements](#requirements)
- [Variables](#variables)
- [Usage with custom config](#usage-with-custom-config)
- [Local usage with sudo](#local-usage-with-sudo)

### Description
Simple role for managing [prometheus ipmi-exporter](https://github.com/prometheus-community/ipmi_exporter)

### Requirements
Role requires systemd.<br>
Role will install freeipmi (core dependency for ipmi-exporter) and sudoers (if enabled) automatically.

### Variables

| Variable name                    | Default value              | Description                             |
| -------------------------------- | -------------------------- | --------------------------------------- |
| ipmi_exporter_version            | `1.6.1`                    | version of ipmi-exporter to install     |
| ipmi_exporter_user               | `ipmi_exporter`            | system user for exporter                |
| ipmi_exporter_group              | `{{ ipmi_exporter_user }}` | system group for exporter               |
| ipmi_exporter_web_listen_address | `:9290`                    | listen port                             |
| ipmi_exporter_use_sudoers        | `false`                    | installs sudoers file for ipmi_exporter |
| ipmi_exporter_cli_flags          | `{}`                       | additional arguments for ipmi_exporter  |
| ipmi_exporter_config             | `{}`                       | YAML with custom ipmi-exporter config   |

### Usage with custom config

If you want to query network ipmi interfaces, you can consult [official docs](https://github.com/prometheus-community/ipmi_exporter/blob/master/ipmi_remote.yml) and pass needed config (in YAML format) to ipmi_exporter_config variable.


### Local usage with sudo

If you want to monitor local sensors, you will need to set 
```yaml
ipmi_exporter_use_sudoers: true
```

Which will grant access no-password sudo access for user specified in ipmi_exporter_user access to following binaries:
```
/usr/sbin/ipmi-sensors
/usr/sbin/ipmi-dcmi
/usr/sbin/ipmi-raw
/usr/sbin/bmc-info
/usr/sbin/ipmi-chassis
/usr/sbin/ipmi-sel
```

Since ipmi_exporter is not aware of permissions, you will then need to configure it to use sudo before calling freeipmi binaries:
```yaml
modules:
  default:
    collectors:
    - ipmi
    collector_cmd:
      ipmi: sudo
    custom_args:
      ipmi:
      - ipmimonitoring
```
