# php-fpm-cli

Running PHP-FPM, especially for website hosts using NGINX, is missing the important ability to execute PHP scripts directly from the command-line while simulating the website context.

With this simple script, one can execute PHP scripts that are normally called via PHP-FPM, directly from CLI. This can be used for testing, batch operations, or cronjobs.

This script is based on [Mathias Leppich's gist][1], with added support for:
* loading the script from a file (instead of input code as CLI argument)
* query-string parameters
* verification of not only the exit code of the script, but also http-status-code.
* guessing of `$_SERVER` variables given a URL to simulate

See more details in [this Medium post](https://elisegev.medium.com/running-php-fpm-in-cli-e1f0f4b4f59a).

## Requirements

### Dependencies

RHEL / CentOS
```shell script
yum --enablerepo=epel install fcgi
```

Ubuntu / Debian
```shell script
apt-get install libfcgi0ldbl
```

### Setup

The PHP script being executed needs to be executable and owned by an user allowed by the PHP-FPM configuration, so you may need to update it:
```shell script
vi /etc/php-fpm.d/www.conf
# add your ACL user to the comma-delimited list: listen.acl_users
# e.g. listen.acl_users = apache,nginx,myuser
systemctl restart php-fpm
```

## Usage

The script will send the given PHP file to the PHP-FPM process and echo the response to STDOUT.

```shell script
./php-fpm-cli -f </path/to/php/file> -q "<query_params>" [-connect <connection>] [-m <GET|POST>] [-u <URL>]
```

Parameters:
| Flag | Description | Default | Example |
| ---- | ----------- | ------- | ------- |
| `-f` | Path to the PHP file | | `/var/www/html/index.php` |
| `-connect` | PHP-FPM host and port, or path to a socket | `/run/php-fpm/www.sock` | `127.0.0.1:9000` |
| `-m` | Request method, GET or POST | GET | |
| `-u` | URL to simulate. This must return an status code 200, so make sure this is the canonical URL (i.e., it will not redirect) | | `https://www.mydomain.com/` |


Using query parameters:
```shell script
./php-fpm-cli -f myscript.php -q "param1=val1&param2=val2"
```

Using a URL:
```shell script
./php-fpm-cli -f /var/www/html/index.php -connect 127.0.0.1:9000 -u https://www.mydomain.com/
```

[1]: https://gist.github.com/muhqu/91497df3a110f594b992