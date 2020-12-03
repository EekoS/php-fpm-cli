# php-fpm-cli

Running PHP-FPM, especially for website hosts using NGINX, is missing an important functionality to execute PHP scripts directly from the command-line.

With this simple script, one can execute PHP scripts directly from CLI. It is convenient to test PHP in CLI, run batch operations with PHP and for cronjobs.

This script is based on [Mathias Leppich's gist][1], with added support for loading the script from a file (instead of input code as CLI argument), query-string parameters and verification of not only the exit code of the script - but also http-status-code.

See more details in: https://elisegev.medium.com/running-php-fpm-in-cli-e1f0f4b4f59a

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

PHP script needs to be executable and owned by an allowed user (ACL):
```shell script
vi /etc/php-fpm.d/www.conf
# add your ACL user to the comma-delimited list: listen.acl_users
# e.g. listen.acl_users = apache,nginx,myuser
systemctl restart php-fpm
```

## Usage

Run PHP script:
```shell script
./php-fqm-cli -f <path_to_php_file>
```

Run PHP script with query params:
```shell script
./php-fqm-cli -f <path_to_php_file> -q "<query_params>"
```

Example:
```shell script
./php-fqm-cli -f myscript.php -q "param1=val1&param2=val2"
```

[1]: https://gist.github.com/muhqu/91497df3a110f594b992