FROM openknowledge/ckan-base:2.9

MAINTAINER Your Name Here <you@example.com>

# Set timezone
ARG TZ
ENV SRC_DIR=/srv/app/src
RUN echo $TZ > /etc/timezone
RUN cp /usr/share/zoneinfo/$TZ /etc/localtime

# Install any extensions needed by your CKAN instance
# (Make sure to add the plugins to CKAN__PLUGINS in the .env file)
# For instance:
#RUN pip install -e git+https://github.com/ckan/ckanext-pages.git#egg=ckanext-pages && \
#    pip install -e git+https://github.com/ckan/ckanext-dcat.git@v0.0.6#egg=ckanext-dcat && \
#    pip install -r https://raw.githubusercontent.com/ckan/ckanext-dcat/v0.0.6/requirements.txt

# ldap bindings
RUN apk add libtool openldap-dev

RUN pip install -e git+https://github.com/cuprit-gbr/ckanext-cuprit#egg=ckanext-cuprit && \
    pip install -e git+https://github.com/NaturalHistoryMuseum/ckanext-ldap#egg=ckanext-ldap && \
    pip install -e git+https://github.com/ckan/ckanext-dcat.git#egg=ckanext-dcat && \
    pip install -r ${SRC_DIR}/ckanext-dcat/requirements.txt && \
    pip install -e git+https://github.com/fwoerister/ckanext-archiveview#egg=ckanext-archiveview && \
    pip install -e git+https://github.com/cuprit-gbr/ckanext-resource-type-validation.git#egg=ckanext-resource-type-validation && \
    pip install -e git+https://github.com/ckan/ckanext-pages.git#egg=ckanext-pages

# Install the extension(s) you wrote for your own project
# RUN pip install -e git+https://github.com/your-org/ckanext-your-extension.git@v1.0.0#egg=ckanext-your-extension

# Apply any patches needed to CKAN core or any of the built extensions (not the
# runtime mounted ones)
# See https://github.com/okfn/docker-ckan#applying-patches
COPY patches ${APP_DIR}/patches

RUN for d in $APP_DIR/patches/*; do \
        if [ -d $d ]; then \
            for f in `ls $d/*.patch | sort -g`; do \
                cd $SRC_DIR/`basename "$d"` && echo "$0: Applying patch $f to $SRC_DIR/`basename $d`"; patch -p1 < "$f" ; \
            done ; \
        fi ; \
    done

# run http socket for Traefik
RUN sed -i 's/http 0.0.0.0:5000/http-socket 0.0.0.0:5000/' /srv/app/start_ckan.sh
RUN sed -i 's/-p 2/-p 4/' /srv/app/start_ckan.sh