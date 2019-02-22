PHP Docker images
=================

This repository contains the source for building various versions of
the PHP application as a reproducible Docker image using
[source-to-image](https://github.com/openshift/source-to-image).
Users can use CentOS based builder images.
The resulting image can be run using [Docker](http://docker.io).

For more information about using these images with OpenShift, please see the
official [OpenShift Documentation](https://docs.okd.io/latest/using_images/s2i_images/php.html).

For more information about contributing, see
[the Contribution Guidelines](https://github.com/sclorg/welcome/blob/master/contribution.md).
For more information about concepts used in these container images, see the
[Landing page](https://github.com/sclorg/welcome).


Versions
---------------
PHP versions currently supported are:
* [php-7.2](7.2)

CentOS versions currently supported are:
* CentOS7


Installation
---------------
To build a PHP image, choose use the CentOS based image:
*  **CentOS based image**
    ```
    $ git clone --recursive https://github.com/serverbee/s2i-nginx-php-fpm-container.git
    $ cd s2i-nginx-php-fpm-container
    $ make build TARGET=centos7 VERSIONS=7.2
    ```

Alternatively, you can pull the CentOS image from Docker Hub via:

    $ docker pull serverbee/nginx-php-fpm-72-centos7

**Notice: By omitting the `VERSIONS` parameter, the build/test action will be performed
on all the supported versions of PHP.**


Usage
---------------------------------

For information about usage of Dockerfile for PHP 7.2,
see [usage documentation](7.2/README.md).

Test
---------------------
This repository also provides a [S2I](https://github.com/openshift/source-to-image) test framework,
which launches tests to check functionality of a simple PHP application built on top of the s2i-php image.

Users can use testing a PHP test application based on CentOS image.

*  **CentOS based image**

    ```
    $ cd s2i-nginx-php-fpm-container
    $ make test TARGET=centos7 VERSIONS=7.2
    ```

**Notice: By omitting the `VERSIONS` parameter, the build/test action will be performed
on all the supported versions of PHP.**


Repository organization
------------------------
* **`<php-version>`**

    * **Dockerfile**

        CentOS based Dockerfile.

    * **`s2i/bin/`**

        This folder contains scripts that are run by [S2I](https://github.com/openshift/source-to-image):

        *   **assemble**

            Used to install the sources into the location where the application
            will be run and prepare the application for deployment (eg. installing
            modules using npm, etc..)

        *   **run**

            This script is responsible for running the application, by using the
            application web server.

    * **`contrib/`**

        This folder contains a file with commonly used modules.

    * **`test/`**

        This folder contains the [S2I](https://github.com/openshift/source-to-image)
        test framework with a sample PHP app.

        * **`test-app/`**

            A simple PHP app used for testing purposes by the [S2I](https://github.com/openshift/source-to-image) test framework.

        * **run**

            Script that runs the [S2I](https://github.com/openshift/source-to-image) test framework.


