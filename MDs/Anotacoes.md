# ANOTAÇÕES E COMANDOS PARA NÃO ESQUECER

---

# 🚀 VPS

## 🐳 DOCKER

**Listar containers rodando**

```bash
docker ps
```

**Listar todos (inclusive parados)**

```bash
docker ps -a
```

**Ver logs de um container**

```bash
docker logs -f <nome_container>
```

**Entrar dentro de um container**

```bash
docker exec -it <nome_container> bash
```

**Parar container**

```bash
docker stop <nome_container>
```

**Remover container**

```bash
docker rm <nome_container>
```

---

# 🍃 MONGODB (Sessões do Chatbot)

**Acessar Mongo**

```bash
docker exec -it *docker-name* mongosh -u admin -p senha123
```

**Selecionar banco**

```javascript
use nome_do_banco
```

**Listar collections**

```javascript
show collections
```

**Buscar dados**

```javascript
db.sessions.find().pretty()
```

**Buscar por telefone**

```javascript
db.sessions.find({ telefone: "5511999999999" })
```

**Atualizar campo**

```javascript
db.sessions.updateOne(
  { telefone: "5511999999999" },
  { $set: { estado: "menu_principal" } }
)
```

**Deletar sessão**

```javascript
db.sessions.deleteOne({ telefone: "5511999999999" })
```

---

# 🐬 MYSQL (Dados persistentes)

## 🔑 Acessar banco

```bash
docker exec -it *docker-name* mysql -u root -p
```

**Senha**

```text
root
```

---

## 📦 Comandos básicos

**Ver bancos**

```sql
SHOW DATABASES;
```

**Selecionar banco**

```sql
USE hc_facilities;
```

**Ver tabelas**

```sql
SHOW TABLES;
```

**Ver dados**

```sql
SELECT * FROM usuario;
SELECT * FROM usuario_servico;
```

---

## 🧱 Estrutura

### Tabela usuário

```sql
usuario
- id
- nome
- email
- telefone
- endereco
- tipo (cliente | prestador)
- data_registro
```

### Tabela serviços

```sql
usuario_servico
- id
- usuario_id
- servico
```

---

## 🛠️ Alterações no banco

**Adicionar coluna**

```sql
ALTER TABLE usuario 
ADD COLUMN tipo VARCHAR(20);
```

**Atualizar dados**

```sql
UPDATE usuario 
SET tipo = 'prestador';
```

**Deletar usuário**

```sql
DELETE FROM usuario WHERE telefone = '5511999999999';
```

---

# 🔄 N8N (Regras importantes)

## 🧠 EXPRESSÕES

**Ternário**

```javascript
={{ $json.estado === 'menu_prestador' ? 'prestador' : 'cliente' }}
```

---

## 📦 MANIPULAÇÃO DE ARRAY

**Transformar array em texto**

```javascript
{{$json.servicos.join(', ')}}
```

**Lista formatada**

```javascript
{{$json.servicos.map(s => '• ' + s).join('\n')}}
```

---

## 🔁 MULTI INSERT (MySQL)

**Code node (explodir array)**

```javascript
const usuarioId = $json.usuario_id;
const servicos = $json.servicos || [];

return servicos.map(servico => ({
  json: {
    usuario_id: usuarioId,
    servico: servico
  }
}));
```

⚠️ Node deve estar em:

```text
Run Once for All Items
```

---

## ⚠️ REGRAS IMPORTANTES

**JSON vs STRING**

```text
string → "valor"
json → array/objeto real
```

---

**Mongo Query**

```json
{
  "telefone": "{{$json.telefone}}"
}
```

---

**Evitar erro comum**

```text
❌ telefone: {{$json.telefone}}
✅ "telefone": "{{$json.telefone}}"
```

---

# 🧠 ARQUITETURA DO PROJETO

```text
MongoDB → sessão do chatbot
MySQL   → dados do negócio
```

---

# 🔥 FLUXO IDEAL

```text
1. Usuário entra
2. Mongo controla estado
3. Coleta dados
4. Salva usuário (MySQL)
5. Salva serviços (MySQL)
6. Pronto para match
```

---

# 🚀 FUTURO (PRÓXIMOS PASSOS)

* Match cliente ↔ prestador
* Filtro por serviço
* Ordenação por proximidade
* Painel admin
* Logs e métricas

---

# 💡 DICAS IMPORTANTES

✔ Sempre validar dados antes de salvar
✔ Nunca confiar no input do usuário
✔ Evitar duplicação de fluxo
✔ Usar arrays ao invés de string com vírgula
✔ Preferir lógica reutilizável

---
