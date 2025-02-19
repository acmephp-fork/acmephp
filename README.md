Acme PHP
========

>[!IMPORTANT]
> This project is a fork of https://github.com/acmephp/acmephp. The
> subtree splitting on the original repository does not work anymore. We have
> contributed as much as we can, but now we are blocked by the maintainers. So
> we created this fork to re-enable subtree. This allow us to use subtree split
> in our projets. Except two files (`.github/workflows/gitsplit.yaml`,
> `.gitsplit.yml`) all others files has been untouched. Don't open issue/PR here
> for this project, open them on the original repository instead. This repository will
> be synced from time to time with the original repository.

---

[![Build Status](https://img.shields.io/github/actions/workflow/status/acmephp/acmephp/test-build.yaml?branch=master&style=flat-square)](https://github.com/acmephp/acmephp/actions/workflows/test-build.yaml?query=branch%3Amaster)
[![Packagist Version](https://img.shields.io/packagist/v/acmephp/acmephp.svg?style=flat-square)](https://packagist.org/packages/acmephp/acmephp)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)

Acme PHP is a simple yet very extensible CLI client for Let's Encrypt that will help
you get and renew free HTTPS certificates.

Acme PHP is also an initiative to bring a robust, stable and powerful implementation
of the ACME protocol in PHP. Using the Acme PHP library and core components, you will be
able to deeply integrate the management of your certificates directly in your application
(for instance, renew your certificates from your web interface). If you are interested
by these features, have a look at the [acmephp/core](https://github.com/acmephp/core) and
[acmephp/ssl](https://github.com/acmephp/ssl) libraries.

>  Acme PHP is now maintained by https://zerossl.com
>
> Other companies are welcome to contribute to the maintenance of the library. Thanks to:
> - https://redirection.io/

## Why should I use Acme PHP when I have an official client?

Acme PHP provides several major improvements over the default clients:
-   Acme PHP comes by nature as a single binary file: a single download and you are ready to start working ;
-   Acme PHP is based on a configuration file instead command line arguments.
    Thus, the configuration is much more expressive and the same setup is used at every renewal ;
-   Acme PHP is very extensible it to create the certificate files structure you need for your webserver.
    It brings several default formatters to create classical file structures
    (nginx, nginx-proxy, haproxy, etc.) but you can very easily create your own if you need to ;
-   Acme PHP follows a strict BC policy preventing errors in your scripts or CRON even if you update it (see
    [the Backward Compatibility policy of Acme PHP](#backward-compatibility-policy) for more informations) ;

## Documentation

Read the official [Acme PHP documentation](https://acmephp.github.io).

## Backward Compatibility policy

Acme PHP follows a strict BC policy by sticking carefully to [semantic versioning](http://semver.org). This means
your scripts, your CRON tasks and your code will keep working properly even when you update Acme PHP (either the CLI
tool or the library), as long as you keep the same major version (1.X.X, 2.X.X, etc.).

## Launch the Test suite

The Acme PHP test suite uses the Docker Boulder image to create an ACME server.
To launch the test suite, you need to setup the proper Docker environment for the suite.
Useful scripts are available under the `tests` directory: in the Acme PHP root directory,
execute the following:

```
# Create the Docker environment required for the suite
sudo tests/setup.sh

# Run the tests
tests/run.sh

# Clean the docker environment
tests/teardown.sh
```

**Note**: you may have boulder errors sometimes in tests. Simply ignore them and rerun the suite,
they are due to an issue in the container DNS.

**Warning**: as the acmephp/testing-ca Docker image needs to be mapped to the host network,
you may have ports conflicts. See [https://github.com/acmephp/testing-ca](https://github.com/acmephp/testing-ca)
for more informations.

## Run command

The run command is an all in one command who works with a `domain`
config file like

```yaml
contact_email: contact@company
key_type: RSA                                          # RSA or EC (for ECDSA). Default "RSA"

defaults:
  distinguished_name:
      country: FR
      locality: Paris
      organization_name: MyCompany
  solver: http

certificates:
  - domain: example.com
    distinguished_name:
      organization_name: MyCompany Internal
    solver: route53
    subject_alternative_names:
      - '*.example.com'
      - www.subdomain.example.com
    install:
      - action: install_aws_elb
        region: eu-west-1
        loadbalancer: my_elb
  - domain: www.example.com
    solver:
      name: http-file
      adapter: ftp                                     # ftp or sftp or local, see https://flysystem.thephpleague.com/
      root: /var/www/
      host: ftp.example.com
      username: username
      password: password
      # port: 21
      # passive: true
      # ssl: true
      # timeout: 30
      # privateKey: path/to/or/contents/of/privatekey
```

usage

```bash
$ acmephp run path-to-config.yml
```

## Using docker

You can also use the docker image to generate certificates.
Certificates and keys are stored into the volume `/root/.acmephp`

```
docker run --rm -ti -v /cache/.acmephp:/root/.acmephp -v $PWD/.config.yml:/etc/acmephp.yml:ro acmephp/acmephp:latest run /etc/acmephp.yml
```

