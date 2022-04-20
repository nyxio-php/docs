# Configuration

> All configuration files are in `config` path.

### app.php

| Option      | Description                                                                            | Type      | Default         |
|-------------|:---------------------------------------------------------------------------------------|:----------|-----------------|
| `env`       | Environment variable. You can use it for different behavior in different environments. | `string`  | `local`         |
| `debug`     | Used for detailed exceptions messages.                                                 | `boolean` | `false`         |
| `timezone`  | Sets the local time zone                                                               | `string`  | `UTC`           |
| `lang`      | Language config name                                                                   | `string`  | `en`            |
| `providers` | List of [Providers](providers.md)                                                      | `array`   | see description |

### http.php


| Option    | Description                                                          | Type    | Default         |
|-----------|:---------------------------------------------------------------------|:--------|-----------------|
| `actions` | Used for registration your [Actions](actions.md)                     | `array` | showcase action |
| `groups`  | Used for registration your actions groups                            | `array` | showcase group  |


### cron.php


| Option    | Description                                                          | Type    | Default         |
|-----------|:---------------------------------------------------------------------|:--------|-----------------|
| `jobs` | Used for registration your [Cron](cron.md) jobs                        | `array` | []              |

### server.php

| Option    | Description                                                                                                                      | Type      | Default     |
|-----------|:---------------------------------------------------------------------------------------------------------------------------------|:----------|-------------|
| `host`    | Server host                                                                                                                      | `string`  | `127.0.0.1` |
| `port`    | Server port                                                                                                                      | `integer` | `9501`      |
| `options` | Extra options for OpenSwoole server ([see OpenSwoole documentation](https://openswoole.com/docs/modules/swoole-http-server-doc)) | `array`   | -           |
#### `lang` path

Place in this path your files with application messages.

