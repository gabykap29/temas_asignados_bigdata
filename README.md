# TP Big Data — Pipelines ETL con DuckDB y Airflow

Repositorio con **10 temas** de trabajo practico. Cada tema plantea el mismo desafio sobre un dataset distinto, con datos "sucios" (aprox. 2575 filas) que hay que validar, limpiar y analizar.

## El desafio (comun a todos los temas)
Construir un **pipeline ETL** que:

1. **Extraiga** los datos del origen (CSV o SQLite) sin modificarlos.
2. Los **cargue** tal cual en una tabla `raw_*` de DuckDB.
3. Los **valide** con al menos 8 reglas con nombre.
4. Separe los registros en `validos` y `rechazados` (columna `motivo_rechazo`).
5. **Limpie** los rechazados y los guarde en `rechazados_limpios`.
6. Una todo en una unica `tabla_final` (sin duplicados y con tipos correctos).
7. Responda **5 preguntas de negocio** con SQL sobre `tabla_final`.
8. Se ejecute **todos los dias a las 2:00 AM** con **Apache Airflow**.

## Temas
| Carpeta | Rubro | Empresa | Origen |
|---|---|---|---|
| [`tema_01_ecommerce`](tema_01_ecommerce/) | E-commerce | MercaFacil S.A. | pedidos.csv |
| [`tema_02_hospital`](tema_02_hospital/) | Clinica | Clinica Santa Lucia | clinica.sqlite |
| [`tema_03_banco`](tema_03_banco/) | Banco | Banco Horizonte | transacciones.csv |
| [`tema_04_logistica`](tema_04_logistica/) | Logistica | RapiCargo Logistica | envios.csv |
| [`tema_05_rrhh`](tema_05_rrhh/) | RRHH | TecnoSur S.A. | rrhh.sqlite |
| [`tema_06_streaming`](tema_06_streaming/) | Streaming | StreamPlus | reproducciones.csv |
| [`tema_07_educacion`](tema_07_educacion/) | Instituto | Instituto Superior Horizonte | inscripciones.csv |
| [`tema_08_energia`](tema_08_energia/) | Energia | EnergiaViva S.A. | lecturas.csv |
| [`tema_09_supermercado`](tema_09_supermercado/) | Supermercado | SuperAhorro | stock.sqlite |
| [`tema_10_turismo`](tema_10_turismo/) | Turismo | ViajaYa Turismo | reservas.csv |

## Estructura
```
para_drive/
├── README.md
└── tema_XX_nombre/
    ├── README.md        # resumen del tema
    ├── ENUNCIADO.md     # enunciado completo
    └── datos/           # datos de origen (no modificar)
```

## Entregables por tema
- `pipeline.py` (o modulos).
- Carpeta de consultas SQL (`consultas.sql`).
- DAG de Airflow en `dags/`.
- Archivo `.duckdb` generado.
- Informe breve (1 pagina): problemas encontrados, rechazos por regla y respuestas a las preguntas.

## Herramientas
Python, pandas, DuckDB (con extension `sqlite` para los origenes SQLite) y Apache Airflow.
