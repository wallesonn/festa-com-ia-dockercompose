# festa-com-ia-dockercompose

Docker Compose da aplicação para rodar na infra da VPS (Portainer e Traefik já configurados).

## Arquivos

| Arquivo | Descrição |
|---------|-----------|
| `Dockerfile` | Build multistage: deps → builder → runner (Node 20 Alpine) |
| `docker-compose.yml` | Serviço `web` com labels do Traefik (HTTPS + redirect HTTP) |
| `.dockerignore` | Exclusões do contexto de build |

## Pré-requisitos na VPS

- Docker + Docker Compose instalados
- Traefik rodando com a network externa `traefik-public`
- Cert resolver `letsencrypt` configurado no Traefik

## Configuração

Antes de fazer o deploy, edite `docker-compose.yml` e substitua o domínio:

```yaml
- "traefik.http.routers.festa-com-ia.rule=Host(`festa.seudominio.com`)"
```

## Como fazer o deploy

### Via linha de comando na VPS

```bash
# Clone ou copie o repositório na VPS
git clone <repo-url>
cd festa-com-ia

# Build e sobe o container
docker compose -f festa-com-ia-dockercompose/docker-compose.yml up -d --build
```

### Via Portainer

1. Acesse o Portainer
2. Vá em **Stacks → Add stack**
3. Faça upload do `docker-compose.yml` ou cole o conteúdo
4. Configure as variáveis de ambiente se necessário
5. Clique em **Deploy the stack**

## Variáveis de Ambiente

| Variável | Padrão | Descrição |
|----------|--------|-----------|
| `NODE_ENV` | `production` | Ambiente Node |

> Adicione outras variáveis no `docker-compose.yml` conforme o backend for integrado (ex: `DATABASE_URL`, `WHATSAPP_TOKEN`).

## Observações

- O `Dockerfile` usa `output: 'standalone'` do Next.js para gerar um bundle mínimo sem `node_modules`
- O contexto de build é a raiz do projeto (`..` relativo a esta pasta)
- A network `traefik-public` deve existir antes do deploy: `docker network create traefik-public`
