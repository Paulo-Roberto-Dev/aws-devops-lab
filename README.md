# AWS DevOps Lab

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

## Segurança

- SSH apenas por chave, sem senha e sem login de root
- Firewall (ufw) ativo e security group da AWS restringindo portas
- Acesso administrativo pela rede privada do Tailscale

## Como subir

```bash
docker compose up -d
```

## Roadmap

- [x] Servidor, Docker e primeiro serviço no ar
- [x] Acesso remoto privado com Tailscale
- [ ] CI/CD com GitHub Actions
- [ ] HTTPS com domínio próprio
- [ ] Observabilidade (CloudWatch)
- [ ] Infraestrutura como código (Terraform)
