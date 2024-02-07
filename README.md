# Docker Registry

Exemplos disponíveis em https://youtu.be/1-43ifaQkNk?si=QmP9EUTd2O-MEUD0 e https://github.com/marcelogomesrp/curso_docker/tree/main/15_registry

---

```yaml
# Simple private registry
version: '3.7'
services:
 registry:
   image: registry:2.7.1
   ports:
     - "5000:5000"
```

```yaml
# simple private registry with volume
version: '3.7'
services:
 registry:
   image: registry:2.7.1
   ports:
    - "5000:5000"
   volumes:
    - /data/marcelo/docker/registry/data:/var/lib/registry
```

```yaml
# simple private registry with volume and authentication
version: '3.7'
services:
  registry:
    image: registry:2.7.1
    ports:
     - "5000:5000"
    volumes:
     - /data/marcelo/docker/registry/data:/var/lib/registry
     - /home/marcelo/workspace/curso_docker/15_registry/03_Registry_with_login/auth:/auth
    environment:
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
      REGISTRY_HTTP_HOST: http://localhost:5000 
```

```yaml
# private registry with volume, authentication and ssl
version: '3.7'
services:
  registry:
    image: registry:2.7.1
    ports:
     - "5000:5000"
    volumes:
     - /data/marcelo/docker/registry/data:/var/lib/registry
     - /home/marcelo/workspace/curso_docker/15_registry/03_Registry_with_login/auth:/auth
     - /home/marcelo/workspace/curso_docker/15_registry/05_Registry_with_ssl/certificates:/certificates
    environment:
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
      REGISTRY_HTTP_HOST: https://192.168.68.106:5000
      REGISTRY_HTTP_TLS_CERTIFICATE: /certificates/domain.crt
      REGISTRY_HTTP_TLS_KEY: /certificates/domain.key
```

```yaml
# registry with User interface web
version: '3.7'
services:
  registry:
    image: registry:2.7.1
    ports:
     - "5000:5000"
    volumes:
     - /data/marcelo/docker/registry/data:/var/lib/registry
     - /home/marcelo/workspace/curso_docker/15_registry/03_Registry_with_login/auth:/auth
     - /home/marcelo/workspace/curso_docker/15_registry/05_Registry_with_ssl/certificates:/certificates
    environment:
      REGISTRY_AUTH: htpasswd
      REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm
      REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
      REGISTRY_HTTP_HOST: https://192.168.68.106:5000
      REGISTRY_HTTP_TLS_CERTIFICATE: /certificates/domain.crt
      REGISTRY_HTTP_TLS_KEY: /certificates/domain.key      
    networks:
      - registry-ui-net      

  ui:
    image: joxit/docker-registry-ui:latest
    ports:
      - 80:80
    environment:
      - REGISTRY_TITLE=My Private Docker Registry
      - NGINX_PROXY_PASS_URL=https://registry:5000
      - SINGLE_REGISTRY=true
    depends_on:
      - registry
    networks:
      - registry-ui-net

networks:
  registry-ui-net:
```