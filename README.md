# Docker Registry with Traefik and Self Signed TLS Certificate (On Premise for Air Gapped Environment)


## Download the folder project



I will describe later the steps with a practical example: _Deploying Graylog docker in Air Gapped Environment._

Image pulled on internet connected machine from Docker Hub

```
docker image ls
```

```
opensearchproject/opensearch                    2.11.0       ce2416eabaf8   4 days ago     1.22GB
graylog/graylog                                 5.2.0        f757395322c4   2 weeks ago    545MB
mongo                                           6.0.11       b4b9736f6e27   4 weeks ago    681MB
traefik                                         2.10.5       cc365cbb0397   4 weeks ago    151MB
```

We want to do the same process without internet access.

Please follow this example: [Install docker in air gapped environment](https://github.com/Senzing/knowledge-base/blob/main/HOWTO/install-docker-image-in-air-gapped-enviroment.md)

On my case, save the images.
```
docker save graylog/graylog:5.2.0 --output ./graylog-5.2.0.tar
docker save opensearchproject/opensearch:2.11.0 --output ./opensearch-2.11.0.tar
docker save mongo:6.0.11 --output ./mongo-6.0.11.tar
docker save traefik:2.10.5 --output ./traefik-2.10.5.tar
```

We will continue later the commands, now go on the new host for docker registry.





```
docker
```


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

