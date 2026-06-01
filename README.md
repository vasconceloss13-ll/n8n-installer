# n8n Deploy Kit · Lunar Labs

Gerador de schema para subir o **n8n self-hosted** rapidamente, com as chaves de segurança geradas automaticamente. É uma página única (HTML/CSS/JS, sem build, sem dependências) — abra no navegador e use.

## O que faz

- Gera senhas e a encryption key aleatoriamente, **no próprio navegador** (nada é enviado ou salvo em servidor)
- Monta o schema completo pronto pra copiar ou baixar
- Suporta dois ambientes e duas arquiteturas

### Ambientes
- **EasyPanel** — para deploy em VPS com EasyPanel (gera o JSON do schema, com domínio e SSL)
- **Local (Docker)** — para rodar na própria máquina (gera um `docker-compose.yml`, acessível em `http://localhost:5678`)

### Arquiteturas
- **Simples** (padrão) — 1 serviço n8n + Postgres. Mais leve, recomendado pra maioria dos casos.
- **Fila / queue mode** (modo avançado) — editor + webhook + worker + Postgres + Redis. Escala melhor, para produção com alto volume.

## Como usar

Abra o `index.html` no navegador. Não precisa instalar nada.

Para publicar online (ex.: GitHub Pages): suba o repositório, ative o Pages apontando pra branch principal, e a página fica acessível pela URL do Pages.

## Pré-requisitos do deploy

**EasyPanel:**
1. VPS com EasyPanel instalado
2. Domínio próprio comprado
3. Registros DNS `A` apontando os subdomínios para o IP da VPS

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
- A comunicação entre containers (n8n ↔ Postgres ↔ Redis) ocorre numa rede Docker privada e não usa SSL. O SSL/TLS é aplicado apenas na borda (internet → n8n), pelo proxy reverso.

## Licença

Uso interno / livre. Ajuste conforme necessário.
