Perfeito! Aqui está o conteúdo **completo e limpo** do `README.md` em **formato único e correto** — agora é só copiar e colar **sem erro de indentação, espaçamento ou quebra de formatação**.

---

````markdown
# 🔍 Railway + Prometheus + PgBouncer Exporter – Observabilidade Completa

Este projeto implementa uma stack de observabilidade moderna para monitorar **conexões, throughput e latência de PgBouncer** utilizando:

- Prometheus como motor de métricas
- Exporter `spreaker/prometheus-pgbouncer-exporter`
- Railway como plataforma de deploy
- Tunnel via `ngrok` (ambiente de testes)

---

## 📦 Componentes da stack

| Componente                    | Descrição                                  |
|------------------------------|---------------------------------------------|
| `Prometheus` (Railway)       | Coletor e armazenador de métricas           |
| `PgBouncer Exporter` (Docker)| Exporta métricas do PgBouncer via `/metrics`|
| `PgBouncer` (Railway DB)     | Proxy de conexões PostgreSQL                |
| `ngrok`                      | Exposição HTTP local para Prometheus acessar o exporter |

---

## ⚙️ Setup de Desenvolvimento

### 1. Clonar este repositório

```bash
git clone https://github.com/devanonymous16/rw-prometheus-v1.git
````

### 2. Criar o arquivo `config.yml`

No diretório raiz:

```yaml
exporter_host: 0.0.0.0
exporter_port: 9127

pgbouncers:
  - dsn: "postgres://postgres:<SENHA>@tramway.proxy.rlwy.net:35061/pgbouncer?sslmode=disable"
    connect_timeout: 5
```

### 3. Subir o PgBouncer Exporter localmente

```bash
docker run -d \
  --name pgbouncer-exporter \
  -p 9127:9127 \
  -v /config.yml:/etc/pgbouncer-exporter/config.yml \
  spreaker/prometheus-pgbouncer-exporter
```

### 4. Expor a porta via ngrok

```bash
ngrok http 9127
```

Copie o endereço gerado (`https://<id>.ngrok-free.app`) e use no próximo passo.

---

## 🔧 Configurar o Prometheus (Railway)

No arquivo `prometheus.yml`, adicione:

```yaml
- job_name: 'pgbouncer'
  scrape_interval: 5s
  metrics_path: /metrics
  static_configs:
    - targets: ['<SEU_ID>.ngrok-free.app']
```

Depois disso:

```bash
git add prometheus.yml
git commit -m "feat: add pgbouncer job"
git push origin main
```

Faça o redeploy na Railway para aplicar as mudanças.

---

## ✅ Verificações no Prometheus

### Acesse `/targets`

```
https://<seu-app>.up.railway.app/targets
```

Você deve ver:

* `pgbouncer` → Status: `UP`

### Acesse `/graph` e teste as queries:

```promql
pgbouncer_up
pgbouncer_pools_client_active_connections
rate(pgbouncer_stats_transactions_total[1m])
```

---

## 📈 Métricas úteis do PgBouncer

| Métrica Prometheus                                    | Significado                     |
| ----------------------------------------------------- | ------------------------------- |
| `pgbouncer_up`                                        | Exporter funcionando            |
| `pgbouncer_stats_transactions_total`                  | Total de transações             |
| `rate(pgbouncer_stats_transactions_total[1m])`        | Transações por segundo (TPS)    |
| `pgbouncer_pools_client_active_connections`           | Conexões cliente ativas         |
| `pgbouncer_pools_client_waiting_connections`          | Clientes em fila                |
| `pgbouncer_stats_queries_duration_microseconds_total` | Latência total de queries       |
| `pgbouncer_config_max_client_conn`                    | Máximo de conexões configuradas |
| `pgbouncer_databases_database_pool_size`              | Tamanho do pool                 |
| `pgbouncer_up == 0`                                   | Exporter caiu (alertável)       |

---

## 🔜 Próximos passos (produção)

* [ ] Subir PgBouncer Exporter na Railway (como serviço independente)
* [ ] Adicionar **Alertmanager** para fila de clientes, quedas etc.
* [ ] Subir Grafana e importar dashboard
* [ ] Integrar com Grafana Cloud (opcional)
* [ ] Adicionar `postgres_exporter` se quiser monitorar o banco direto

---

## 📁 Repositórios relacionados

* Fork base: [`zuchka/railway-prometheus`](https://github.com/zuchka/railway-prometheus)
* Este repo: [`devanonymous16/rw-prometheus-v1`](https://github.com/devanonymous16/rw-prometheus-v1)

---

## 🧠 Autoria & Engenharia

Projeto construído por [@devanonymous16](https://github.com/devanonymous16)
Infra estruturada com apoio do Arcanum Cyber Bot (OpenAI)

---

## 📜 Licença

MIT — use, quebre, melhore, versiona.

```

---

Se quiser que eu empacote isso como `.md` pronto pra commit ou inclua badges e screenshot de dashboard depois, só pedir.

Agora você tem documentação padrão gold 🔥📚
```
