## Curso: Desenvolvimento de Aplicações Modernas e Escaláveis com Microsserviços

- [Code Education](https://portal.code.education/)

### Modulo: DevOps - Continuous Deployment

Projeto laravel usando MySQL, NGinx e Redis, com as configurações de CI, CD, Kubernetes no GCP.


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
