# Build frontend pages

FROM mhart/alpine-node:6.11.3 as frontend

RUN apk update && apk add git rsync
RUN git clone https://github.com/kernelci/kernelci-frontend.git front
RUN node /front/app/dashboard/static/js/lib/r.js -o /front/app/dashboard/static/js/build.js

# API documentation

FROM bitnami/minideb:buster as backend

RUN install_packages git python python-pip python-setuptools python-dev build-essential
RUN git clone https://github.com/kernelci/kernelci-backend.git /tmp/kernelci-backend
RUN cd /tmp/kernelci-backend && git checkout -f 6111f84cbffa57c1facf2119d65d3ee051bb5f86 && cd -
RUN pip install -r /tmp/kernelci-backend/requirements.txt
RUN cd /tmp/kernelci-backend/doc && make html

# Proxy setup

FROM nginx:1.13-alpine

# Remove default configuration
RUN rm /etc/nginx/conf.d/default.conf

# Main configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Frontend configuration
COPY frontend/server.conf /etc/nginx/conf.d/frontend.conf

# Backend configuration
COPY backend/server.conf      /etc/nginx/conf.d/backend.conf
COPY backend/proxy.conf       /etc/nginx/custom/backend-proxy.conf
COPY backend/maintenance.conf /etc/nginx/custom/backend-maintenance.conf

# Copy api's documentation
RUN mkdir -p /usr/share/nginx/html/kernelci/backend/schema \
    && chown nginx:nginx /usr/share/nginx/html/kernelci/backend/
COPY --from=backend /tmp/kernelci-backend/doc/build/html/ /usr/share/nginx/html/kernelci/backend
COPY --from=backend /tmp/kernelci-backend/doc/schema/ /usr/share/nginx/html/kernelci/backend/schema/

# Tools to perform health check
RUN apk add --update curl py-pip && pip install uwsgi-tools
COPY entrypoint.sh /usr/bin/entrypoint.sh
ENTRYPOINT ["/usr/bin/entrypoint.sh"]
