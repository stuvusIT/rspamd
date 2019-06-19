# rspamd

This role installs and configures [rspamd](https://rspamd.com/).
By default, the configuration will be the same as if you installed the package regulary, with the exception that `systemd` is used for logging.

## Requirements

Debian or Ubuntu.
It may be useful to use [redis](https://github.com/stuvusIT/redis/) in combination with this role, but it is not necessary.

## Role Variables


| Name             | Required/Default                                           | Description                                                                                                                                                                                                                                                                                                                                                                 |
|------------------|:----------------------------------------------------------:|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `rspamd_config`  | `{}`                                                       | This dict configures rspamd by populating configuration files in `/etc/rspamd/override.d`. The first level of keys dictates the file name in this directory, while the dict under that key contains the configuration. The configs are placed in `override.d`, which does not interfere with package updates as well as local configs through the web controller or wizard. |
| `rspamd_workers` | _see [defaults](defaults/main.yml) or example for default_ | List of [workers](https://rspamd.com/doc/workers/). Each entry is a dict with the keys `type` (one of `normal`, `controller`, `fuzzy` or `rspamd_proxy`) and `options` which contains the configuration. For a basic setup, you should at least configure a `normal` worker and a `controller` for convenience.                                                             |
| `rspamd_logging` | `{type: console, systemd: true}`                           | [Logging](https://rspamd.com/doc/configuration/logging.html) configuration of rspamd                                                                                                                                                                                                                                                                                        |

## Example

```yml
rspamd_config:
  redis:
    servers: 127.0.0.1
  options:
    neighbours:
      # The controller workers are proxied by nginx with TLS and basic auth
      spam01:
        host: https://127.0.0.1:443
      spam02:
        host: https://192.168.178.5:443

rspamd_workers:
  - type: rspamd_proxy
    options:
      bind_socket: localhost:11332
  - type: normal
    options:
      bind_socket: localhost:11333
  - type: controller
    options:
      bind_socket: localhost:11334
  - type: fuzzy
    options:
      bind_socket: localhost:11335
      count: -1
```

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).


## Author Information

- [Michel Weitbrecht (SlothOfAnarchy)](https://github.com/SlothOfAnarchy) _michel.weitbrecht@stuvus.uni-stuttgart.de_
