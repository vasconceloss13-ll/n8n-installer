# n8n Deploy Kit · Lunar Labs

Gerador de schema para subir o **n8n self-hosted** rapidamente, com as chaves de segurança geradas automaticamente. É uma página única (HTML/CSS/JS, sem build, sem dependências) — abra no navegador e use.

## O que faz

- Gera senhas e a encryption key aleatoriamente, **no próprio navegador** (nada é enviado ou salvo em servidor)
- Monta o schema completo pronto pra copiar ou baixar
- Permite ativar/desativar serviços (Redis, Evolution API) com toggles
- Suporta dois ambientes e duas arquiteturas

### Ambientes
- **EasyPanel** — para deploy em VPS com EasyPanel (gera o JSON do schema, com domínio e SSL)
- **Local (Docker)** — para rodar na própria máquina (gera um `docker-compose.yml`, acessível em `http://localhost:5678`)

### Arquiteturas
- **Simples** (padrão) — n8n + Postgres. Mais leve, recomendado pra maioria dos casos.
- **Fila / queue mode** (modo avançado, no ⋮) — editor + webhook + worker + Postgres + Redis. Escala melhor, para produção com alto volume.

### Serviços
- **Postgres** — sempre incluído (o n8n não sobe sem banco).
- **Redis** — opcional no modo simples; obrigatório (e travado) no modo fila.
- **Evolution API** — opcional, em qualquer modo. Para integração com WhatsApp.

## Domínio opcional

Os campos de domínio são opcionais:
- **Com domínio próprio:** preencha o campo. O schema inclui o bloco de domínio com SSL.
- **Sem domínio (vazio):** o EasyPanel gera um domínio automático. As variáveis usam `$(PRIMARY_DOMAIN)`, que o EasyPanel substitui pelo domínio real no deploy.

## Como usar

Abra o `index.html` no navegador. Não precisa instalar nada.

Para publicar online (ex.: GitHub Pages): suba o repositório, ative o Pages apontando pra branch principal, e a página fica acessível pela URL do Pages.

## ⚠️ Bancos de dados que precisam ser criados manualmente

O Postgres do EasyPanel **não cria automaticamente** os bancos que o n8n e a Evolution precisam. Depois de subir o Postgres, abra o console dele (serviço Postgres → Console → **Postgres Client**) e rode:

```sql
CREATE DATABASE n8n;
-- Se for usar a Evolution API, crie também:
CREATE DATABASE evolution;
```

Sem isso, o n8n falha com `database "n8n" does not exist` e a Evolution não sobe. (No modo Local via Docker, o banco `n8n` já é criado automaticamente; só o `evolution` precisaria ser criado manualmente caso a Evolution esteja ativa.)

## Pré-requisitos do deploy

**EasyPanel:**
1. VPS com EasyPanel instalado
2. Domínio próprio (opcional — sem ele, o EasyPanel gera um automático)

**Local:**
1. Docker + Docker Compose instalados
2. Porta 5678 livre
3. ~2 GB de RAM livres

## Segurança

- As chaves usam `crypto.getRandomValues` (128 bits de entropia). Colisão entre gerações é estatisticamente impossível.
- Nada é persistido: ao fechar a aba, as chaves somem. **Guarde as senhas em local seguro antes de fechar.**
- A `N8N_ENCRYPTION_KEY` é única por instância e **não deve ser alterada** depois que houver workflows com credenciais salvas — mudá-la torna as credenciais ilegíveis.

## Notas técnicas

- O `N8N_HOST` é gerado apenas com o hostname (sem `https://`), que é o formato correto. O protocolo é definido separadamente por `N8N_PROTOCOL`.
- Para habilitar o nó **Execute Command** (e outros desativados por padrão no n8n 2.x), o schema usa `NODES_EXCLUDE=[]`. A antiga `N8N_ENABLE_EXECUTE_COMMAND` não tem mais efeito no n8n 2.x.
- A comunicação entre containers (n8n ↔ Postgres ↔ Redis) ocorre numa rede Docker privada e não usa SSL. O SSL/TLS é aplicado apenas na borda (internet → n8n), pelo proxy reverso.

## Licença

Uso interno / livre. Ajuste conforme necessário.
