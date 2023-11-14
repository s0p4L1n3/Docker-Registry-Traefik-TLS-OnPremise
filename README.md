# Docker Registry with Traefik and Self Signed TLS Certificate (On Premise for Air Gapped Environment)

I will describe later the steps with a practical example: _Deploying Graylog docker in Air Gapped Environment._

Image pulled on internet connected machine from Docker Hub
```
REPOSITORY                                      TAG          IMAGE ID       CREATED        SIZE
opensearchproject/opensearch                    2.11.0       ce2416eabaf8   4 days ago     1.22GB
graylog/graylog                                 5.2.0        f757395322c4   2 weeks ago    545MB
mongo                                           6.0.11       b4b9736f6e27   4 weeks ago    681MB
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

