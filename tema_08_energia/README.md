# Energia: lecturas de medidores — EnergiaViva S.A.

## Contexto
EnergiaViva recibe lecturas de medidores inteligentes. Algunos medidores viejos mandan los datos con otro formato. Quieren facturar y detectar los barrios de mayor consumo.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/lecturas.csv` — CSV (`,`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_lectura`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_lectura` | Id unico |
| `fecha_hora` | Fecha y hora (YYYY-MM-DD hh:mm:ss) |
| `id_medidor` | Id del medidor |
| `barrio` | Norte / Sur / Centro / Oeste / Este |
| `tipo_cliente` | residencial / comercial / industrial |
| `consumo_kwh` | Consumo en kWh (>= 0) |
| `tarifa_kwh` | Precio del kWh |
| `importe` | consumo_kwh * tarifa_kwh |

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_lecturas`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cual es el consumo total por tipo de cliente?
2. ¿Que barrio tiene el mayor consumo total?
3. ¿Cuanto se facturo por mes (primeros 6 meses)?
4. ¿Cuales son los 5 medidores con mayor consumo?
5. ¿Cual es el consumo promedio por barrio?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/lecturas.csv`: datos de origen.
