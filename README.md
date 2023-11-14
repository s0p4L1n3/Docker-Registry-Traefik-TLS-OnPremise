# Docker Registry with Traefik and Self Signed TLS Certificate (On Premise for Air Gapped Environment)

I will describe later the steps with a practical example: _Deploying Graylog docker in Air Gapped Environment._

Image pulled on internet connected machine from Docker Hub
```
opensearchproject/opensearch                    2.11.0       ce2416eabaf8   4 days ago     1.22GB
graylog/graylog                                 5.2.0        f757395322c4   2 weeks ago    545MB
mongo                                           6.0.11       b4b9736f6e27   4 weeks ago    681MB
traefik                                         2.10.5       cc365cbb0397   4 weeks ago    151MB
```

We want to do the same process without internet access.

Please follow this example: [Install docker in air gapped environment](https://github.com/Senzing/knowledge-base/blob/main/HOWTO/install-docker-image-in-air-gapped-enviroment.md)

On my case:
```
docker save graylog/graylog:5.2.0 --output ./graylog-5.2.0.tar
docker save opensearchproject/opensearch:2.11.0 --output ./opensearch-2.11.0.tar
docker save mongo:6.0.11 --output ./mongo-6.0.11.tar
docker save traefik:2.10.5 --output ./traefik-2.10.5.tar
```


  <details>
  <summary>docker-compose.yml</summary>
 
```
version: "3"

services:

   local-registry:
     container_name: registry
     image: registry:2.6
     restart: unless-stopped
     volumes:
       - registry-data:/var/lib/registry
     labels:
       - traefik.enable=true
       - traefik.http.routers.registry.service=registry
       - traefik.http.routers.registry.entrypoints=https
       - traefik.http.routers.registry.rule=Host(`registry.lab.lan`)
       - traefik.http.services.registry.loadbalancer.server.port=5000
       - traefik.docker.network=traefik

   reverse-proxy:
     image: traefik:2.10.5
     container_name: traefik-reverse-proxy
     restart: unless-stopped
     command:
       - --api.insecure=true
       - --providers.docker
       - --entrypoints.https.address=:443
       - --entrypoints.https.http.tls=true
       - --entrypoints.registry.address=:5000
       - --log.level=DEBUG
       - --providers.file.directory=/config
       - --providers.file.watch=true
     ports:
       - 80:80
       - 443:443

     volumes:
       - /var/run/docker.sock:/var/run/docker.sock
       - ./config/:/config:ro
     secrets:
      - source: traefik.certificate
        target: /certs/registry.lab.lan-crt.pem
      - source: traefik.key
        target: /certs/registry.lab.lan-key.pem

secrets:
  traefik.certificate:
    file: ./secrets/registry.lab.lan-crt.pem
  traefik.key:
    file: ./secrets/registry.lab.lan-key.pem

volumes:
  registry-data:
    driver: local
    driver_opts:
      o: bind
      type: none
      device: ./data

```

</details>

## REQUIREMENTS

- Avoiding Certificate error

```
tls: failed to verify certificate: x509: certificate signed by unknown authority
```

Create a folder in `/etc/docker/certs.d`
```
mkdir registry.lab.lan
```
And place inside this folder the Certificate Authority of your PKI (the one that should signed your Registry Certificates)
```
.
└── registry.lab.lan
    └── ca-cert.crt
```

