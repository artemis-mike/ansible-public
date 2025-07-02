# Traefik
## Synopsis
Deploys and configures Traefik as reverse proxy for docker services and issues certificates via LetsEncrypt.

## Configuration
### Regular arbitrary port exposure
To expose a service via Trafik, add these lables to the docker compose file
```
# docker-compose.yaml (Service)
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.<service>.rule=Host(`<Service-FQDN>`)"
  - "traefik.docker.network=<trafik_network>"
  - "traefik.http.routers.<service>.entrypoints=<entrypoint>"
  - "traefik.http.services.<service>.loadbalancer.server.port=<exposed-port>"
```
e.g. for Pi-Hole
```
# docker-compose.yaml (Service)
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.grafana.rule=Host(`pihole.domain.com`)"
  - "traefik.docker.network=traefik_proxy"
  - "traefik.http.routers.grafana.entrypoints=http"
  - "traefik.http.services.grafana.loadbalancer.server.port=80"
```

Also add use this config for trafik itself
```
# treafik.yaml
providers:
  docker:
    exposedByDefault: false

entryPoints:
  http:
    address: ":80"
```

### HTTPS exposure with LetsEncrypt DNS Challange
Extend the services labels with a certresolver
```
# docker-compose.yaml (Service)
  labels:
    - "traefik.enable=true"
    - "traefik.http.routers.grafana.rule=Host(`pihole.domain.com`)"
    - "traefik.docker.network=traefik_proxy"
    - "traefik.http.routers.grafana.entrypoints=https"
    - "traefik.http.routers.grafana.tls.certresolver=production"
    - "traefik.http.services.grafana.loadbalancer.server.port=80"
```

And also configure this resolver in trafik
```
# treafik.yaml
certificatesResolvers:
  production:
    acme:
      dnschallenge:
        provider: <provider-name>
      email: <email>
      storage: /letsencrypt/acme.json
```
as well as the required API Keys for your DNS provider
```
# docker-compose.yaml (Traefik)
  traefik:
    container_name: traefik
    image: traefik:v2.11
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080"
    environment:
      # Follow https://go-acme.github.io/lego/dns/cloudflare/
      - CF_DNS_API_TOKEN=${CF_DNS_API_TOKEN}
      - CF_ZONE_API_TOKEN=${CF_ZONE_API_TOKEN}
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
      - "./traefik.yaml:/etc/traefik/traefik.yaml"
      - "./letsencrypt:/letsencrypt"
    networks:
      - proxy
```