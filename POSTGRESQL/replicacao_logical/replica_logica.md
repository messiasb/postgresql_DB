# 🧪 Lab - Replicação Lógica no PostgreSQL

**Data:** 2025-05-10  
**Objetivo:** Montar replicação lógica entre dois servidores PostgreSQL 16

---

## 🧱 Infra usada
- Servidor master: `201.54.12.38`
- Servidor réplica: `201.54.12.39`
- Usuário replicador: `replicador`

---

## 🧭 Passos executados

### 1. Criar publicação no master
```sql
CREATE PUBLICATION product_pub FOR TABLE produtos;
