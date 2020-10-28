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

***Secret para o MySQL***

O comando abaixo cria um secret generico para o MySQL via linha de comando para não armazenar essa senha em arquivo, com o nome `mysql-pass` e a key `password`. Depois alteramos o arquivo de deployment do mysql para usar esta secret.

- *Obs.:* Caso a criação e definição a secret tenha sido feita depois a das configurações de volume e deployment, delete estes e os crie novamente.

- Comando para criar a secret
```Bash
# Cria uma secret de forma literal
> kubectl create secret generic mysql-pass --from-literal=password='a1s2d3f4'

# Lista as secrets do kubernetes
> kubectl get secret
```

***Criando um banco de dados e testando o volume persistente***

```Bash
# Executamos o pod de serviço do mysql
> kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
mysql-service-7ddf6557b6-fj484     1/1     Running   0          16m
> kubectl exec -it mysql-service-7ddf6557b6-fj484 bash
# Dentro do pod do mysql
root@mysql-service-7ddf6557b6-fj484:/# mysql -uroot -p
Enter password:
mysql> show databases;
mysql> create database code;

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
- O banco de dados `code` deve ser listado
```

***Executando o POD do Nginx***
```Bash
> kubectl exec -it nginx-deployment-56dd4d8778-rdfzp apk update
> kubectl exec -it nginx-deployment-56dd4d8778-rdfzp apk add  bash
> kubectl exec -it nginx-deployment-56dd4d8778-rdfzp bash
```

***Permissão no GCP para executar o kubectl***
Adicionar permissão `Kubernetes Engine Admin` para a conta XXX@cloudbuild.gserviceaccount.com, esta conta já possui a permissão `Cloud Build Service`.
