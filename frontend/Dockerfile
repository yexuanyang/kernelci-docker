FROM bitnami/minideb:buster

# Install dependencies

RUN install_packages build-essential git python python-pip python-setuptools python-dev uwsgi-plugin-python curl redis-tools wget

# Install Node.js / nodemon
WORKDIR /
RUN wget https://nodejs.org/dist/v8.17.0/node-v8.17.0-linux-x64.tar.xz \
    && tar -xvf node-v8.17.0-linux-x64.tar.xz \
    && cd node-v8.17.0-linux-x64 
ENV PATH="/node-v8.17.0-linux-x64/bin:$PATH"
RUN npm install -g nodemon

# Setup application

WORKDIR /srv
COPY kernelci-frontend /srv/kernelci-frontend
RUN pip install -r kernelci-frontend/requirements.txt
WORKDIR /srv/kernelci-frontend/app
RUN touch /etc/flask_settings
COPY flask_settings /etc/kernelci/kernelci-frontend.cfg

# uWSGI configuration

EXPOSE 5000
COPY uwsgi.ini /srv/uwsgi.ini
COPY entrypoint.sh /srv/entrypoint.sh
CMD ["/srv/entrypoint.sh"]
