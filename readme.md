# Logistische Regression in Apache Spark — Single- vs. Multi-Worker Benchmark

Jupyter-Notebook ist unter ./work/LogReg.ipynb zu finden.

Dieses Projekt untersucht die **Performance einer logistischen Regression** (Spark MLlib) in zwei Setups bei **konstantem Gesamtkernbudget**:
- **1 Worker (w1):** 4 Cores, 8 GB RAM
- **2 Worker (w2):** je 2 Cores, 4 GB RAM (insgesamt 4 Cores)

Im Fokus stehen **Laufzeiten**, **Parallelisierungs-Overheads** (Shuffle/Koordination) und das **Skalierungsverhalten** entlang der Datenmenge \(N\) und der Featurezahl \(d\). Der Datensatz ist **synthetisch, deterministisch** und wird als **Parquet** geteilt zwischen Master/Workern persistiert.

---

## Voraussetzungen

- Docker & Docker Compose v2
- Optional: Make sure Ports frei sind (Spark UI 4040–4050, Master-UI 8080, Spark RPC 7077)

---

## Schnellstart (Docker) mit einem Worker

**Stop & Cleanup (vor Profilwechsel immer ausführen):**
```bash
docker compose -f docker/docker-compose.yml down -v
```
### Start mit 1 Worker (4 Cores, 8 GB RAM):
```bash
docker compose -f docker/docker-compose.yml --profile w1 up -d --build
```
### Notebook ggf. anpassen oder ausführen  (SparkSession neu bauen)
Für w1 (1 Worker, 4 Cores gesamt):
```python
try: spark.stop()
except: pass
spark = build_spark(
    mode="cluster",
    app_name="LogReg-Benchmark",
    driver_mem="2g",
    executor_mem="3g",
    executor_cores=4,   # 1W
    cores_max=4,
    ui_base_port=4040,
    shuffle_parts=8
)
```

## Profilwechsel w1 ↔ w2 (wichtig)
Docker neu starten
```bash
docker compose -f docker/docker-compose.yml down -v
```

### Start mit 2 Workern (je 2 Cores, 4 GB RAM):
```bash
docker compose -f docker/docker-compose.yml --profile w2 up -d --build
```

### Für w2 (2 Worker, je 2 Cores → 4 Cores gesamt):
```python
try: spark.stop()
except: pass
spark = build_spark(
    mode="cluster",
    app_name="LogReg-Benchmark",
    driver_mem="2g",
    executor_mem="3g",
    executor_cores=2,   # 2W
    cores_max=4,
    ui_base_port=4040,
    shuffle_parts=8
)
```
