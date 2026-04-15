# 🐳 Docker + Portainer — Guia prático de sobrevivência (MVP / Produção)

Documentação rápida para não esquecer os comandos e, principalmente, **como pensar o fluxo de controle de containers, volumes e bancos**.

---

# 🚀 Conceitos que preciso lembrar

## 📦 Container

É a **instância em execução** da imagem.

Pense assim:

```text
Imagem = molde
Container = aplicação rodando
```

Exemplo:

```text
mysql:8.0 → imagem
hc-mysql  → container
```

---

## 💾 Volume

É onde os dados persistem.

Muito importante:

```text
Apagar container NÃO apaga dados
Apagar stack NÃO apaga volume
```

Exemplo:

```text
hc_mysql_data
```

É nele que o MySQL salva os dados.

---

## 🌐 Network

É como os containers “se enxergam”.

Exemplo:

```text
GRSolutionsNet
```

Se dois containers estão na mesma network:

```text
n8n → conecta no mysql pelo nome do serviço
```

Exemplo host:

```text
hc-mysql
```

---

# 🐳 Comandos Docker mais usados

---

## 📋 Listar containers

**Rodando**

```bash
docker ps
```

**Todos**

```bash
docker ps -a
```

---

## 📜 Ver logs

```bash
docker logs -f hc-mysql
```

Muito usado para debug.

Exemplo:

```text
running /docker-entrypoint-initdb.d/init.sql
```

---

## 🚪 Entrar no container

```bash
docker exec -it hc-mysql bash
```

Para MySQL direto:

```bash
docker exec -it hc-mysql mysql -u root -p
```

---

## 🛑 Parar container

```bash
docker stop hc-mysql
```

---

## ▶️ Iniciar container

```bash
docker start hc-mysql
```

---

## 🗑️ Remover container

```bash
docker rm hc-mysql
```

⚠️ Isso NÃO remove os dados.

---

# 💾 Volume (MUITO IMPORTANTE)

---

## 📋 Listar volumes

```bash
docker volume ls
```

---

## 🔍 Inspecionar volume

```bash
docker volume inspect hc_mysql_data
```

---

## 🗑️ Remover volume

⚠️ APAGA TODOS OS DADOS

```bash
docker volume rm hc_mysql_data
```

---

## 💥 Reset completo do banco

Fluxo que mais usei:

```bash
docker compose down
docker volume rm hc_mysql_data
docker compose up -d
```

Isso força o MySQL a recriar tudo e rodar o `init.sql`.

---

# 🐳 Docker Compose / Stack

---

## ▶️ Subir stack

```bash
docker compose up -d
```

---

## 🛑 Derrubar stack

```bash
docker compose down
```

⚠️ Importante:

```text
down → remove containers
down ≠ remove volumes
```

---

## 🔄 Reiniciar stack

```bash
docker compose restart
```

---

## 📜 Ver compose ativo

```bash
docker compose ps
```

---

# 🧠 Fluxo mental de controle (ESSENCIAL)

---

## 🟢 Alteração de código / compose

Exemplo:

* porta
* environment
* network
* nome do serviço

Fluxo:

```text
1. editar docker-compose.yml
2. docker compose down
3. docker compose up -d
```

---

## 🟡 Alteração no init.sql

⚠️ MUITO IMPORTANTE

O `init.sql` só roda quando o volume está vazio.

Fluxo:

```text
1. alterar init.sql
2. derrubar stack
3. remover volume
4. subir stack
```

Comandos:

```bash
docker compose down
docker volume rm hc_mysql_data
docker compose up -d
```

---

## 🔴 Alteração no schema já existente

Exemplo:

* adicionar coluna
* criar tabela nova
* alterar campo

NÃO mexer no init.

Fazer direto no banco:

```sql
ALTER TABLE usuario ADD COLUMN tipo VARCHAR(20);
```

---

# 🐬 Fluxo de banco (resumo mental)

---

## Banco novo

```text
volume vazio
↓
container sobe
↓
init.sql executa
↓
banco criado
```

---

## Banco já existente

```text
volume já existe
↓
container sobe
↓
init.sql NÃO executa
```

---

# 🔥 Regras de ouro

---

## ❌ Erro clássico

```text
apaguei a stack → banco vai resetar
```

ERRADO

Porque:

```text
volume continua existindo
```

---

## ✅ Correto

Quer resetar banco?

```text
remover volume
```

---

## ❌ Outro erro clássico

```text
localhost no n8n
```

Se ambos estão em container:

```text
usar nome do serviço
```

Exemplo:

```text
host = hc-mysql
port = 3306
```

---

# 🚀 Fluxo de troubleshooting

Quando algo quebrar:

---

## 1. Container subiu?

```bash
docker ps
```

---

## 2. Logs

```bash
docker logs -f hc-mysql
```

---

## 3. Volume existe?

```bash
docker volume ls
```

---

## 4. Banco existe?

```sql
SHOW DATABASES;
```

---

## 5. Tabelas existem?

```sql
USE hc_facilities;
SHOW TABLES;
```

---

# 💡 Regra que preciso decorar

```text
Container = execução
Volume = dados
Stack = orquestração
```

---
