## Curso: Desenvolvimento de Aplicações Modernas e Escaláveis com Microsserviços

- [Code Education](https://portal.code.education/)

### Modulo: DevOps - Continuous Deployment

Projeto laravel usando MySQL, NGinx e Redis, com as configurações de CI, CD, Kubernetes no GCP.


#### Congfigurações do CloudBuild

- Processo de CI: `cloudbuild.yaml`
- Processo de CD: `cloudbuild.prod.yaml`


#### Congfigurações do Kubernetes

- MySQL: `k8s/mysql`
- Redis: `k8s/redis`
- Nginx: `k8s/nginx`
- Aplicação Laravel: `k8s/app`

***Configurações do MySQL***

1. Criar a Secret para o MySQL
2. Aplicar as configurações no kubernetes, `PersistentVolumeClaim, Deployment e Service`
3. Executar o POD do MySQL e criar o banco de dados `laravel` 

- *Secret para o MySQL*

O comando abaixo cria um secret generico para o MySQL via linha de comando para não armazenar essa senha em arquivo, com o nome `mysql-pass` e a key `password`. Depois alteramos o arquivo de deployment do mysql para usar esta secret.

- *Obs.:* Caso a criação e definição a secret tenha sido feita depois a das configurações de volume e deployment, delete estes e os crie novamente.

- Comando para criar a secret
```bash
# Cria uma secret de forma literal
> kubectl create secret generic mysql-pass --from-literal=password='a1s2d3f4'

# Lista as secrets do kubernetes
> kubectl get secret
```

- *Criando um banco de dados e testando o volume persistente*

```bash
# Executamos o pod de serviço do mysql
> kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
mysql-service-7ddf6557b6-fj484     1/1     Running   0          16m
> kubectl exec -it mysql-service-7ddf6557b6-fj484 apk update
> kubectl exec -it mysql-service-7ddf6557b6-fj484 apk add bash
> kubectl exec -it mysql-service-7ddf6557b6-fj484 bash
# Dentro do pod do mysql
root@mysql-service-7ddf6557b6-fj484:/# mysql -uroot -hmysql-service -p
root@mysql-service-7ddf6557b6-fj484:/# mysql -uroot -p
Enter password:
mysql> show databases;
mysql> create database laravel;

# Deletamos o deployment do mysql
> kubectl delete -f mysql-deployment.yaml
# Criamos o pod novamente
> kubectl apply -f mysql-deployment.yaml
> kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
mysql-service-7ddf6557b6-s8snh     1/1     Running   0          8s
# Dentro do pod do mysql
root@mysql-service-7ddf6557b6-fj484:/# mysql -uroot -p
Enter password:
mysql> show databases;
- O banco de dados `laravel` deve ser listado
```

***Configurações do NGinx***

O `ConfigMap` do nginx aponta para este diretorio `/usr/share/nginx/html`.

No arquivodo de deployment adicionar o comando abaixo na especificação do container do nginx, para criar o arquivo `index.php`
```bash
> command: ["/bin/sh", "-c", "touch /usr/share/nginx/html/index.php; nginx -g 'daemon off;'"]
```

- *Executando o POD do Nginx*
```bash
> kubectl exec -it nginx-deployment-56dd4d8778-rdfzp apk update
> kubectl exec -it nginx-deployment-56dd4d8778-rdfzp apk add  bash
> kubectl exec -it nginx-deployment-56dd4d8778-rdfzp bash
```

***Configurações da Aplicação Laravel***

No arquivodo de deployment adicionar o comando abaixo na especificação do container do PHP, para criar o link sinbolico entre os diretorios `/var/www /usr/share/nginx`, e executar um script com os comando de execução do php e migração.
```bash
> command: ["/bin/sh","-c","ln -s /var/www /usr/share/nginx; /var/www/k8s/entrypoint.sh; php-fpm;"]
```

***Permissão no GCP para executar o kubectl***
Adicionar permissão `Kubernetes Engine Admin` para a conta XXX@cloudbuild.gserviceaccount.com, esta conta já possui a permissão `Cloud Build Service`.


***Permissão no Laravel e Docker***
Ao executar acessar a aplicação carrega uma página com o seguinte erro:
- The stream or file "/var/www/storage/logs/laravel.log" could not be opened in append mode: failed to open stream: **Permission denied**

Para solucionar o problema adicionar no aquivo Dockerfile.prod, `RUN chown -R www-data:www-data /var/www`

- Link sobre o erro: [Permission Denied Error using Laravel & Docker](https://stackoverflow.com/questions/48619445/permission-denied-error-using-laravel-docker)
