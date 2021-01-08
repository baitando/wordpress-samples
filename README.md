# Wordpress Sample
This repository contains a sample of a Wordpress site, which can be built, deployed and published using DevOps oriented scripts.

## Build The Wordpress Site
The Wordpress site uses Composer to declare and resolve the necessary dependencies.
Wordpress specific plugins and themes are resolved from [WordPress Packagist][wp-packagist].

The build is executed using the command below.
The result is provided in the directory `target/build`.

```shell
./baitando build
```

## Prepare An Environment
To prepare an environment, make sure, that a key-based login is possible from the computer you run the script on.
The main part is to provide the environment configuration used by the scripts.
Please have a look at the template file.

The most important directory is the `BAITANDO_<ENV>_REMOTE_DEVOPS_DIR`.
It can contain the following files:

| File          | Required      | Description   |
|---------------|---------------|---------------|
|`.my.cnf`      | Yes           |Database credentials |
|`.htpasswd`    | No            |If present, the website will be secured with Basic Auth |

## Initialize An Environment
An environment can be initialized.
Initialization assumes, that the Wordpress files were deployed previously.
During initialization, the `wp-config.php` file is generated on the target server with the environment specific settings.

The initialization is executed using the command below.

```shell
./baitando init <env>
```

[wp-packagist]: https://wpackagist.org