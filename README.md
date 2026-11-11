# AWS DevOps Lab
![CI](https://github.com/Paulo-Roberto-Dev/aws-devops-lab/actions/workflows/ci.yml/badge.svg)
![CD](https://github.com/Paulo-Roberto-Dev/aws-devops-lab/actions/workflows/cd.yml/badge.svg)

Laboratório de infraestrutura na AWS com práticas de DevOps, construído passo a passo e documentado.

## Arquitetura

```text
Navegador ──HTTP/HTTPS──▶ EC2 (Ubuntu) ──▶ Docker Compose ──▶ Nginx
Admin ──Tailscale (rede privada)──▶ EC2 (acesso remoto por SSH)
```

## Stack

- AWS EC2 (Ubuntu Server LTS), região São Paulo
- Docker e Docker Compose
- Nginx servindo um site estático
- Tailscale para acesso remoto privado
- Git e GitHub
- GitHub Actions (CI e CD)

## Fluxo de deploy (CD)

A cada push na `main` que altera algo em `app/`, o deploy acontece sozinho:

```text
git push ──▶ GitHub Actions ──▶ rede privada (Tailscale) ──▶ servidor: git pull + docker compose up -d
```

1. O workflow `cd.yml` dispara no push (ou manualmente, em *Run workflow*).
2. O runner do GitHub entra na rede privada com uma credencial temporária (OAuth), restrita à tag `tag:ci`.
3. Por SSH, o runner executa no servidor `git pull --ff-only` e `docker compose up -d`.
4. O log da execução mostra o commit do servidor antes e depois do deploy.

## Segurança

- SSH apenas por chave, sem senha e sem login de root
- Firewall (ufw) ativo e security group da AWS restringindo portas
- Acesso administrativo pela rede privada do Tailscale, com a porta 22 fechada ao público
- Regras de acesso do Tailscale limitam o runner do CD a conectar no servidor, como usuário sem privilégios de administrador
- O servidor puxa o código com uma deploy key somente leitura
- `git pull --ff-only` impede merges ou sobrescritas silenciosas no deploy

## Como subir

```bash
cd app && docker compose up -d
```

## Roadmap

- [x] Servidor, Docker e primeiro serviço no ar
- [x] Acesso remoto privado com Tailscale
- [x] CI: validação do Compose a cada push
- [x] CD: deploy automatizado pelo GitHub Actions
- [ ] HTTPS com domínio próprio
- [ ] Observabilidade (CloudWatch)
- [ ] Infraestrutura como código (Terraform)
