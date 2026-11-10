# Logistica: envios — RapiCargo Logistica

## Contexto
RapiCargo coordina envios entre ciudades con distintos transportistas. El sistema de cada transportista carga los datos a su manera y el resultado es un CSV desprolijo. Operaciones quiere medir tiempos y costos.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/envios.csv` — CSV (`,`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_envio`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_envio` | Id unico |
| `fecha_despacho` | Fecha de salida (YYYY-MM-DD) |
| `fecha_entrega` | Fecha de llegada (YYYY-MM-DD) |
| `origen` | Ciudad de origen |
| `destino` | Ciudad de destino |
| `transportista` | andreani / oca / correo_argentino / mercado_envios |
| `peso_kg` | Peso en kilos (> 0) |
| `costo_envio` | Costo en ARS |
| `dias_entrega` | Dias entre despacho y entrega |

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_envios`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cual es el promedio de dias de entrega por transportista?
2. ¿Cual es el costo total de envios por transportista?
3. ¿Cuales son las 5 rutas (origen -> destino) con mas envios?
4. ¿Cuantos kg se enviaron en total por destino?
5. ¿Cuantos envios se entregaron en mas de 7 dias?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/envios.csv`: datos de origen.
