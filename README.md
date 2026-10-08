# Projetinho Cloud

Site estático servido por Nginx em container Docker, rodando numa instância EC2 da AWS e descrito como código com Docker Compose.

## Arquitetura

Navegador -> Internet -> Security Group (porta 80) -> EC2 (Ubuntu 24.04) -> Docker -> Nginx -> `site/`

## Stack

- AWS EC2 (Ubuntu Server 24.04 LTS)
- Docker Engine e Docker Compose v2 (repositório oficial da Docker)
- Nginx (`nginx:stable`)
- Git

## Como rodar

    git clone <url-do-repositorio>
    cd projetinho-cloud
    docker compose up -d

Depois, acesse `http://<ip-do-servidor>`.

## Estrutura

- `compose.yaml`: define o serviço web (porta 80, bind mount somente leitura, restart policy)
- `site/index.html`: página servida pelo Nginx

## O que foi feito

- Servidor EC2 com acesso apenas por chave SSH e usuário próprio, sem usar o usuário padrão
- Security Group: SSH restrito ao meu IP, HTTP e HTTPS abertos
- Docker instalado pelo repositório oficial
- Container com `restart: unless-stopped`, validado com reboot do servidor
- Volume EBS ampliado de 8 para 20 GiB e sistema de arquivos expandido online (`growpart` + `resize2fs`), sem downtime

## Próximos passos

- Publicar no GitHub e criar pipeline com GitHub Actions
- HTTPS com certificado
- Provisionar a infraestrutura com Terraform
- Monitoramento e logs
