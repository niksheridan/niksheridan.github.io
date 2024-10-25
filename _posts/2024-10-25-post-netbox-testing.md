---
title: "Netbox Development"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - How To
  - Netbox
  - Development
---

This uses the [Netbox Community Repository](https://github.com/netbox-community/netbox)

Clone it, configure it, and run:

```bash
git clone -b release https://github.com/netbox-community/netbox-docker.git
cd netbox-docker
tee docker-compose.override.yml <<EOF
services:
  netbox:
    ports:
      - 8000:8080
EOF
podman-compose pull
# run and detach in background
podman-compose up -d
# create a superuser (admin) password
podman-compose exec \
  netbox /opt/netbox/netbox/manage.py createsuperuser
```

Go to `link: http://localhost:8000` and login.

