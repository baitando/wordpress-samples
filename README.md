# Wordpress Sample
This repository contains a sample of a Wordpress site, which can be built, deployed and published using DevOps oriented scripts.
For further explanation please have a look at my blog post [Managing Wordpress DevOps Style][blog-post].

The repository contains the sources of a sample Wordpress website, which can be managed with the scripts provided.

## Script Structure And Configuration
The style of the command-line interface (CLI) and the structure of the scripts is influenced by a post on [Medium][medium-post].

The main script is [`baitando`](./baitando) which is the entry point for all commands and sub-commands.
The implementation of the commands is provided in the [`devops`](./devops) directory, which also contains the [`defaults`](./devops/defaults) file with some environment-agnostic script settings.

To provide environment-specific configuration as well as to override default settings, the [`config`](./config) script is loaded.
Please never commit this file, because it may hold sensitive information like credentials.
Use the [`config.template`](./config.template) file as a template for your local file holding the proper values instead.
You can also use environment variables instead of adding the settings to the [`config`](./config) file.

## Dependency Management
The sample Wordpress website uses [Composer][composer] to declare publicly available dependencies like Wordpress core, and many themes and plugins.
Some things are managed in the usual [Packagist][packagist] dependency repository.
More Wordpress specific things are retrieved from the [WordPress Packagist][wp-packagist] dependency repository.

Custom code is managed in the `./src` directory.
You can also put plugins and themes there, which are not available in public dependency repositories.
Please make sure to consider the license of such sources, which could require that you do not publicly publish your website repository.
In general, I recommend keeping the website source repository private by default.

## Operations CLI
This part of the CLI covers all automated tasks related to managing websites on a remote server.

It is necessary to have SSH access to the server, on which the website should be published.
The user which connects to the server must be eligible to use key-based authentication.

It is required to have the following tools available on the server:

* `php` for executing PHP scripts like the `wp-cli`.
* `mysqldump` for exporting a dump of the MySQL database.
* `mysql` for importing a dump of the MySQL database.
* `htpasswd` for securing the website with HTTP basic authentication.

### Build
The build resolves the Composer dependencies defined in the [`composer.json`](./composer.json) file and adds the custom sources from the [`src`](./src) directory. 

The result is provided in the `target/build` directory.
The build is executed using the command below.


```shell
./baitando build
```

### Deployment
This requires to run the build before.

During deployment to the given environment, all Wordpress files are deleted from the server.
Data consisting of uploaded files, and the database remain untouched.
The same applies to the environment configuration in the `wp-config.php` file.
The locally build files are then uploaded to the target server.

The deployment is executed using the command below.

```shell
./baitando deploy <env>
```

### Initialization
This requires to run the deployment before.

The initialization takes care of creating a proper `wp-config.php` file on the server of the given environment.
If desired, the environment is secured with HTTP basic authentication by generating a proper `.htpasswd` file and appending the necessary configuration to the `.htaccess` file of the website.

The initialization is executed using the command below.

```shell
./baitando init <env>
```

### Export
To export the data of an existing environment, run the command below.

```shell
./baitando export <env>
```

### Import
To import the exported data of an existing environment to an existing environment, run the command below.
The environment specified in the command is the target environment, on which the data should be imported.

```shell
./baitando import <env>
```

## Development CLI
This part of the CLI covers all automated tasks related to local website clones for development purposes.
The scripts are built on top of the Operations CLI, i.e. the prerequisites mentioned there also apply for the Development CLI.

In addition it is necessary to have the following commands available:
* `docker-compose` is needed to create and run a set of Docker containers belonging to the local website environment.
* `docker-sync` is needed to solve the filesystem performance issues in Docker containers using mounted Mac folders.

### Initialization
This requires to run an export of an existing environment and a build first.

To create a local clone of a website, a set of custom Docker images is created.
The [`local/docker-compose.yaml`](./local/configuration/docker-compose.yml) file defines everything related to the images and containers.

The export of an existing environment is then used to prefill the webserver and database containers with the desired data, and the build result is added to the webserver in order to make the website working.

To initialize the local environment, run the command below.
```shell
./baitando docker init
```

### Startup
To start the already initialized local environment, run the command below.

```shell
./baitando docker start
```

### Shutdown
To stop the already initialized local environment, run the command below.

```shell
./baitando docker stop
```

### Destruction
During destruction, the Docker containers of the local environment are deleted together with all volumes.

To destroy the already initialized local environment, run the command below.

```shell
./baitando docker start
```

[wp-packagist]: https://wpackagist.org
[packagist]: https://packagist.org/
[blog-post]: https://www.baitando.com/it/2021/01/10/managing-wordpress-devops-style
[medium-post]: https://medium.com/@brotandgames/build-a-custom-cli-with-bash-e3ce60cfb9a4
[composer]: https://getcomposer.org/