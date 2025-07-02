# Check Traefik
## Synopsis
A role that checks a service's (defined by var: service_name) docker-compose file for the definition of a traefik newtwork. Precisely for the block
```
traefik_proxy:
# maybe something in between
  external: true
```