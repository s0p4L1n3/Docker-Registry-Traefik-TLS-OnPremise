# Docker Registry with Traefik and Self Signed TLS Certificate (On Premise for Air Gapped Environment)

I've followed [Install docker in air gapped environment](https://github.com/Senzing/knowledge-base/blob/main/HOWTO/install-docker-image-in-air-gapped-enviroment.md) to know how to save and load docker images
Follow this first to have image to work with (see step 5)

1. Download the folder project

2. Execute the prerequisites:


- replace string `registry.lab.lan` with your domain name corresponding to your LAN from `docker-compose.yml` and `config/traefik.yml` `secrets/*`

- create the DNS Record for the registry pointing to hosting IP of registry container

- generate certificate and private key for the registry, copy/paste the content of certificate and private key separately in the files `secrets/`
 
- Create folder for certificate validation `mkdir -p /etc/docker/certs.d/registry.lab.lan` and place the CA Cert `ca-cert.crt` inside. (this substep need to be executed even on clients for pulling command)

 
3. Start the docker compose

```
docker compose up -d
```

Display info on deployment
```
docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED        STATUS        PORTS                                                                      NAMES
b47d57e4d40a   registry:2.6     "/entrypoint.sh /etc…"   17 hours ago   Up 17 hours   5000/tcp                                                                   registry
153a84fe6984   traefik:2.10.5   "/entrypoint.sh --ap…"   17 hours ago   Up 17 hours   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   traefik-reverse-proxy
```

4. Verify that you do not have any certificates warning by accessing through the web:

![image](https://github.com/s0p4L1n3/Docker-Registry-Traefik-TLS-OnPremise/assets/126569468/f20c9b52-db3c-4cc9-9a57-49bf046dc9ca)

Then, you should be able to push saved image to the registry

5. Save images, Tag them and push them to registry

- Saving my graylog project images (it can be any other images)
```
docker save graylog/graylog:5.2.0 --output ./graylog-5.2.0.tar
docker save opensearchproject/opensearch:2.11.0 --output ./opensearch-2.11.0.tar
docker save mongo:6.0.11 --output ./mongo-6.0.11.tar
docker save traefik:2.10.5 --output ./traefik-2.10.5.tar
```

- Load, Tagging and pushing the graylog image
```
export DOCKER_REGISTRY_URL=registry.lab.lan
docker load --input graylog-5.2.0.tar
docker tag graylog/graylog:5.2.0 ${DOCKER_REGISTRY_URL}/graylog/graylog:5.2.0
docker push ${DOCKER_REGISTRY_URL}/graylog/graylog:5.2.0
```

6. Results

```
docker push ${DOCKER_REGISTRY_URL}/graylog/graylog:5.2.0
The push refers to repository [registry.lab.lan/graylog/graylog]
879674f4d2d9: Pushed
958bd0862ab4: Pushed
3bcedae299d2: Pushed
5f70bf18a086: Pushed
4002b356b106: Pushed
f548841008be: Pushed
01ff1c4a73d6: Pushed
6a99f1702481: Pushed
fb91a4b6eff7: Pushed
256d88da4185: Pushed
5.2.0: digest: sha256:72a5389a65e4823e0c283d1b0fd01ea8dbeda8ba47396f10e149dcc4133b8b4b size: 2415
```

7. Verify on the registry web view, availables images:

`https://registry.lab.lan/v2/_catalog`

![image](https://github.com/s0p4L1n3/Docker-Registry-Traefik-TLS-OnPremise/assets/126569468/528083ac-a308-4a0b-a344-b473875785f0)

8. Pull image from registry

```
docker pull registry.lab.lan/graylog/graylog:5.2.0
```

If you get this error:

```
Error response from daemon: Get "https://registry.lab.lan/v2/": tls: failed to verify certificate: x509: certificate signed by unknown authority
```

Check up step 2, last substep.


