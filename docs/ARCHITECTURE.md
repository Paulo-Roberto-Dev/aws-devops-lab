# Arquitetura

## Visão geral

Um servidor EC2 (Ubuntu) na região de São Paulo executa um container Nginx via Docker Compose, servindo um site estático. A administração do servidor acontece por uma rede privada (Tailscale), sem depender do IP da máquina de quem administra.

## Diagrama

```text
                  Internet
                     │
           HTTP 80 / HTTPS 443
                     │
       ┌─────────────▼─────────────┐
       │  Security Group (AWS)     │
       └─────────────┬─────────────┘
       ┌─────────────▼─────────────┐
       │  EC2 Ubuntu + ufw         │
       │   └─ Docker Compose       │
       │       └─ Nginx (site)     │
       └─────────────▲─────────────┘
                     │  SSH administrativo
              Tailscale (rede privada)
                     │
                 Administrador
```

## Componentes

| Componente | Função |
|---|---|
| AWS EC2 | Servidor da aplicação |
| Security Group + ufw | Filtro de portas em duas camadas (nuvem e servidor) |
| Docker Compose | Define e sobe o serviço de forma reprodutível |
| Nginx | Serve o site estático |
| Tailscale | Rede privada para acesso administrativo |
| GitHub | Código versionado, publicado com chave de deploy |

## Decisões

1. **Docker Compose em vez de `docker run`:** o ambiente fica descrito em arquivo, é reproduzível e reinicia sozinho após reboot (`restart: unless-stopped`).
2. **Tailscale para o acesso administrativo:** o IP residencial muda e já causou timeout de SSH por regra desatualizada no security group. Com a rede privada, o acesso não depende de um IP fixo de origem.
3. **SSH apenas por chave, sem senha e sem login de root:** reduz a superfície de ataque.
4. **Chave de deploy por repositório:** o servidor publica no GitHub com uma chave que só enxerga este repositório, em vez de uma chave da conta inteira.
5. **Disco ampliado online (gp3 de 20 GiB):** o volume foi expandido com `growpart` e `resize2fs` sem derrubar o servidor.

## Limitações conhecidas e próximos passos

- Fechar o SSH público, deixando o acesso administrativo apenas pela rede privada
- Adicionar HTTPS com domínio próprio
- Automatizar o deploy com GitHub Actions
- Adicionar observabilidade (CloudWatch) e alarmes
- Descrever a infraestrutura como código (Terraform)
