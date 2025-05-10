
# 🐘 Replicação Lógica no PostgreSQL 16 — Passo a Passo Completo

## 🧱 Cenário
- **Master**: `192.168.10.10`
- **Slave**: `192.168.10.11`
- **Banco**: `lab_replicacao`
- **Usuário**: `replicador`
- **Tabela**: `produtos`
- **Sistema Operacional**: Ubuntu 22.04+ nos dois

---

## 🔧 1. Instalar PostgreSQL 16 (em ambos os servidores)

```bash
# Adiciona chave do repositório oficial
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /etc/apt/keyrings/postgresql.gpg

# Adiciona repositório PGDG
echo "deb [signed-by=/etc/apt/keyrings/postgresql.gpg] http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" | \
sudo tee /etc/apt/sources.list.d/pgdg.list

# Atualiza e instala PostgreSQL 16
sudo apt update
sudo apt install postgresql-16 -y
```

---

## ⚙️ 2. Configurar o MASTER (192.168.10.10)

### Editar `postgresql.conf`

```bash
sudo nano /etc/postgresql/16/main/postgresql.conf
```

Altere ou descomente as linhas:

```conf
listen_addresses = '*'
wal_level = logical
max_wal_senders = 10
max_replication_slots = 10
```

### Editar `pg_hba.conf`

```bash
sudo nano /etc/postgresql/16/main/pg_hba.conf
```

Adicione no final:

```conf
host    replication     replicador     192.168.10.11/32       md5
host    lab_replicacao  replicador     192.168.10.11/32       md5
```

### Reinicie PostgreSQL

```bash
sudo systemctl restart postgresql
```

---

## 🧑‍💻 3. Criar banco, tabela e usuário no MASTER

```bash
sudo -u postgres psql
```

```sql
CREATE DATABASE lab_replicacao;
\c lab_replicacao

CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome TEXT,
  preco NUMERIC
);

CREATE ROLE replicador WITH LOGIN PASSWORD 'senha123' REPLICATION;

GRANT CONNECT ON DATABASE lab_replicacao TO replicador;
GRANT USAGE ON SCHEMA public TO replicador;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO replicador;

ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT SELECT ON TABLES TO replicador;

CREATE PUBLICATION pub_lab FOR TABLE produtos;
```

---

## 🛰️ 4. Configurar o SLAVE (192.168.10.11)

```bash
sudo -u postgres psql
```

```sql
CREATE DATABASE lab_replicacao;
\c lab_replicacao

CREATE TABLE produtos (
  id SERIAL PRIMARY KEY,
  nome TEXT,
  preco NUMERIC
);
```

---

## 🔁 5. Criar a subscription no SLAVE

```sql
CREATE SUBSCRIPTION sub_lab
CONNECTION 'host=192.168.10.10 port=5432 dbname=lab_replicacao user=replicador password=senha123'
PUBLICATION pub_lab;
```

---

## ✅ 6. Testar a replicação

### No MASTER:

```sql
INSERT INTO produtos (nome, preco) VALUES ('Camisa', 59.90), ('Calça', 89.90);
```

### No SLAVE:

```sql
SELECT * FROM produtos;
```

---

## 🧪 Extras

### MASTER:

```sql
SELECT * FROM pg_stat_replication;
SELECT * FROM pg_publication;
```

### SLAVE:

```sql
SELECT * FROM pg_stat_subscription;
SELECT * FROM produtos;
```
