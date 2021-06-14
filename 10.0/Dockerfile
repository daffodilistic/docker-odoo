FROM arm64v8/python:2
LABEL maintainer="Soh Thiam Hing <daffodilistic@gmail.com>"

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            dirmngr \
            git \
            libldap2-dev \
            libsasl2-dev \
            libssl-dev\
            libxml2-dev \
            libxslt-dev \
            node-less \
            python-dev \
            wkhtmltopdf

# Install latest postgresql-client
RUN set -x; \
        apt-get install -y --no-install-recommends postgresql-client

# Install Pip  and supporting libraries
RUN set -x; \
        curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py \
        && python get-pip.py "pip<21.0" \
        && pip install wheel

# Create odoo user
RUN useradd -ms /bin/bash odoo

# Install Odoo from source
ARG ODOO_VERSION=10.0
RUN mkdir -p /mnt/odoo \
        && chown -R odoo /mnt/odoo
RUN set -x; \
        git clone --branch 10.0 --depth 1 https://github.com/odoo/odoo.git /mnt/odoo
RUN set -x; \
        python -m pip install -r /mnt/odoo/requirements.txt

# Create symlink
RUN ln -s /mnt/odoo/odoo-bin /usr/bin/odoo

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/
RUN chown odoo /etc/odoo/odoo.conf

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/extra-addons \
        && chown -R odoo /mnt/extra-addons
VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]