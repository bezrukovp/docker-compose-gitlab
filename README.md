# Набор gitlab содержит:

- gitlab
- gitlab-runner
- docker registry
- redis
- postgresql

### Установка

1. В папке ```./env``` находятся файлы окружения. Для каждого из них следует создать одноимённый файл конфигурации без суффикса ```.dist``` в имени файла и настроить в них переменные окружения.
2. В папке ```./certs``` создать сертификаты инструкция [ниже](#self-signed-certificate)...
3. Создать файл конфигурации gitlab-runner ```./runner/config/config.toml```

### Signed Certificate
If you have a signed certificate from a Trusted Certificate Authority you need only to copy the files in then `certs` folder and mount the folder in both containers (gitlab,registry) like in the docker-compose example.
After that you need to set an environment variable in each container.
In the **GitLab Container** you need to set `GITLAB_REGISTRY_KEY_PATH` this is the private key of the signed certificate.
In the **Registry Container** you need to set `REGISTRY_AUTH_TOKEN_ROOTCERTBUNDLE` to the certificate file of the signed certificate.
For more info read [token auth configuration documentation][token-config].

### Self Signed Certificate

Generate a self signed certificate with openssl.

- **Step 1**: Generate a private key and sign request for the private key
```bash
openssl req -nodes -newkey rsa:4096 -keyout registry-auth.key -out registry-auth.csr -subj "/CN=gitlab-issuer"
```

- **Step 2**: Sign your created privated key
```bash
openssl x509 -in registry-auth.csr -out registry-auth.crt -req -signkey registry-auth.key -days 3650
```

After this mount the `certs` dir in both containers and set the same environment variables like way of the signed certificate.

## Полезные ссылки

- https://github.com/sameersbn/docker-gitlab
- https://github.com/sameersbn/docker-gitlab/blob/master/docs/container_registry.md
- https://raw.githubusercontent.com/sameersbn/docker-gitlab/master/docker-compose.yml
- https://gitlab.com/gitlab-org/omnibus-gitlab/tree/master/doc
- https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/files/gitlab-config-template/gitlab.rb.template
- https://docs.gitlab.com/omnibus/docker/README.html
