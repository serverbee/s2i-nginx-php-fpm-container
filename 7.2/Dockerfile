FROM centos/s2i-base-centos7

MAINTAINER Vitaly Yakovenko <vitaly.yakovenko@serverbee.net>

# This image provides an NGINX+PHP-FPM environment for running PHP
# applications.

EXPOSE 8080
EXPOSE 8443

# Description
# This image provides an NGINX 1.14 + PHP-FPM 7.2 environment for running PHP applications.
# Exposed ports:
# * 8080 - alternative port for http

ENV NGINX_VERSION=1.14 \
    NGINX_SHORT_VER=114 \
    VERSION=0

ENV NGINX_CONFIGURATION_PATH=${APP_ROOT}/etc/nginx.d \
    NGINX_CONF_PATH=/etc/opt/rh/rh-nginx${NGINX_SHORT_VER}/nginx/nginx.conf \
    NGINX_DEFAULT_CONF_PATH=${APP_ROOT}/etc/nginx.default.d \
    NGINX_CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/nginx \
    NGINX_APP_ROOT=${APP_ROOT}

ENV PHP_VERSION=7.2 \
    PHP_VER_SHORT=72 \
    NAME=nginx-php-fpm \
    PATH=$PATH:/opt/rh/rh-php72/root/usr/bin

ENV SUMMARY="Platform for building and running PHP $PHP_VERSION applications" \
    DESCRIPTION="PHP $PHP_VERSION available as container is a base platform for \
building and running various PHP $PHP_VERSION applications and frameworks. \
PHP is an HTML-embedded scripting language. PHP attempts to make it easy for developers \
to write dynamically generated web pages. PHP also offers built-in database integration \
for several commercial and non-commercial database management systems, so writing \
a database-enabled webpage with PHP is fairly simple. The most common use of PHP coding \
is probably as a replacement for CGI scripts."

LABEL summary="${SUMMARY}" \
      description="${DESCRIPTION}" \
      io.k8s.description="${DESCRIPTION}" \
      io.k8s.display-name="NGINX 1.14 with PHP-FPM ${PHP_VERSION}" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,${NAME},${NAME}${PHP_VER_SHORT},rh-${NAME}${PHP_VER_SHORT}" \
      io.openshift.s2i.scripts-url="image:///usr/libexec/s2i" \
      io.s2i.scripts-url="image:///usr/libexec/s2i" \
      name="centos/${NAME}-${PHP_VER_SHORT}-centos7" \
      com.redhat.component="rh-${NAME}${PHP_VER_SHORT}-container" \
      version="${PHP_VERSION}" \
      help="For more information visit https://github.com/sclorg/s2i-${NAME}-container" \
      usage="s2i build https://github.com/sclorg/s2i-php-container.git --context-dir=${PHP_VERSION}/test/test-app centos/${NAME}-${PHP_VER_SHORT}-centos7 sample-server" \
      maintainer="SoftwareCollections.org <sclorg@redhat.com>"

# Install PHP-FPM
RUN yum install -y centos-release-scl && \
    INSTALL_PKGS="rh-nginx114 rh-php72 rh-php72-php-fpm rh-php72-php-mysqlnd rh-php72-php-pgsql rh-php72-php-bcmath \
                  rh-php72-php-gd rh-php72-php-intl rh-php72-php-ldap rh-php72-php-mbstring rh-php72-php-pdo \
                  rh-php72-php-process rh-php72-php-soap rh-php72-php-opcache rh-php72-php-xml \
                  rh-php72-php-gmp rh-php72-php-pecl-apcu nss_wrapper" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS --nogpgcheck && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y

ENV PHP_CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/php/ \
    APP_DATA=${APP_ROOT}/src \
    PHP_DEFAULT_INCLUDE_PATH=/opt/rh/rh-php72/root/usr/share/pear \
    PHP_SYSCONF_PATH=/etc/opt/rh/rh-php72 \
    SCL_ENABLED=rh-php72

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Copy extra files to the image.
COPY ./root/ /

# Reset permissions of filesystem to default values
RUN rpm-file-permissions

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.
RUN sed -i -f ${NGINX_APP_ROOT}/nginxconf.sed ${NGINX_CONF_PATH} && \
    chmod a+rwx ${NGINX_CONF_PATH} && \
    mkdir -p ${NGINX_APP_ROOT}/etc/nginx.d/ && \
    mkdir -p ${NGINX_APP_ROOT}/etc/nginx.default.d/ && \
    mkdir -p ${NGINX_APP_ROOT}/src/nginx-start/ && \
    mkdir -p ${NGINX_CONTAINER_SCRIPTS_PATH}/nginx-start && \
    mkdir -p /var/log/nginx && \
    ln -sf /dev/stdout /var/log/nginx/access.log && \
    ln -sf /dev/stderr /var/log/nginx/error.log && \
    chmod -R a+rwx ${NGINX_APP_ROOT}/etc && \
    chmod -R a+rwx /var/opt/rh/rh-nginx${NGINX_SHORT_VER} && \
    chmod -R a+rwx ${NGINX_CONTAINER_SCRIPTS_PATH}/nginx-start && \
    chown -R 1001:0 ${NGINX_APP_ROOT} && \
    chown -R 1001:0 /var/opt/rh/rh-nginx${NGINX_SHORT_VER} && \
    chown -R 1001:0 ${NGINX_CONTAINER_SCRIPTS_PATH}/nginx-start && \
    chmod -R a+rwx /etc/opt/rh/rh-php${PHP_VER_SHORT}/php.d && \
    chmod a+rw /etc/opt/rh/rh-php${PHP_VER_SHORT}/php.ini && \
    chmod a+rwx /var/opt/rh/rh-php${PHP_VER_SHORT}/run/php-fpm && \
    sed -i 's/apache/default/g;s/group = default/group = nginx/' /etc/opt/rh/rh-php${PHP_VER_SHORT}/php-fpm.d/www.conf && \
    rpm-file-permissions

USER 1001

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
