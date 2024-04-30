FROM bitnami/minideb:buster

# Install dependencies
RUN install_packages git python python-pip python-setuptools python-dev build-essential wget gnupg2 xz-utils
RUN pip install python-dateutil

# Install Node.js / nodemon
WORKDIR /
RUN wget https://nodejs.org/dist/v8.17.0/node-v8.17.0-linux-x64.tar.xz \
    && tar -xvf node-v8.17.0-linux-x64.tar.xz \
    && cd node-v8.17.0-linux-x64 
ENV PATH="/node-v8.17.0-linux-x64/bin:$PATH"
RUN npm install -g nodemon

# Install mongo shell
RUN wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | apt-key add - \
    && echo "deb http://repo.mongodb.org/apt/debian buster/mongodb-org/4.2 main" | tee /etc/apt/sources.list.d/mongodb-org-4.2.list \
    && apt-get update -y \
    && apt-get install -y mongodb-org-shell

# Configuration
RUN mkdir -p /etc/kernelci
COPY app/kernelci-backend.cfg /etc/kernelci/kernelci-backend.cfg

# Get source code
COPY kernelci-backend /srv/kernelci-backend

# Install python dependencies
RUN install_packages libyaml-dev  # needed for yaml.CLoader
RUN pip install -r /srv/kernelci-backend/requirements.txt

EXPOSE 8888

COPY app/entrypoint.sh /srv/entrypoint.sh
CMD ["/srv/entrypoint.sh"]
