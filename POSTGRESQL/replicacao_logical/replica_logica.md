# 🧪 Lab - Replicação Lógica no PostgreSQL

**Data:** 2025-05-10  
**Objetivo:** Montar replicação lógica entre dois servidores PostgreSQL 16.8

---
## 🚀 Passo a Passo: Replicação Lógica PostgreSQL (master ↔ slave)
🧱 Pré-requisitos:
    2 VMs Linux (Ubuntu 22.04 ou superior)
    IPs fixos:

    Master: 192.168.10.*
    Slave: 192.168.10.*

    *Acesso root ou sudo* 

## 🔧 1. Instalar o PostgreSQL (em ambos)
✅ Forma correta (com chave e repo oficial — recomendada):
    # 1. Adiciona a chave oficial GPG
    curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /etc/apt/keyrings/postgresql.gpg

    # 2. Adiciona o repositório oficial (ajuste para sua versão do Ubuntu)
    echo "deb [signed-by=/etc/apt/keyrings/postgresql.gpg] http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" | \ 
    sudo tee /etc/apt/sources.list.d/pgdg.list

    # 3. Atualiza pacotes
    sudo apt update
     
    # 4. Instala PostgreSQL 16
    sudo apt install postgresql-16 -y

    # 5. REMOVER .
    **sudo apt remove postgresql postgresql-contrib -y**

## 📘 Cenário

Banco: **lab_replicacao**
Tabela: **produtos** 
Usuário da replicação: **replicador**

## 1. 🔧 Ajustar o postgresql.conf
 # Editar postgresql.conf
    sudo nano /etc/postgresql/16/main/postgresql.conf
conf
wal_level = logical
max_replication_slots = 10
max_wal_senders = 10

```python
(Reinicie o PostgreSQL depois.) >> sudo systemctl restart postgresql

```

## 2. 🔐 Editar pg_hba.conf
# Permitir conexão do SLAVE:
  Editar pg_hba.conf
  sudo nano /etc/postgresql/16/main/pg_hba.conf
# Acesso para replicação
host    replication     replicador     192.168.10.11/32     md5
host    lab_replicacao  replicador     192.168.10.11/32     md5

(Reinicie o PostgreSQL depois.) >> sudo systemctl restart postgresql