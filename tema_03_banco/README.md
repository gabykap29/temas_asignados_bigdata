# Banco: transacciones — Banco Horizonte

## Contexto
Banco Horizonte exporta cada noche las transacciones de sus cuentas en un CSV (separado por punto y coma). Auditoria detecto que hay formatos inconsistentes y quiere un proceso que los separe y los corrija.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/transacciones.csv` — CSV (`;`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_transaccion`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_transaccion` | Id unico |
| `fecha_hora` | Fecha y hora (YYYY-MM-DD hh:mm:ss) |
| `id_cuenta` | Numero de cuenta |
| `tipo` | deposito / extraccion / transferencia / pago_servicio |
| `monto` | Monto (> 0) |
| `moneda` | ARS o USD |
| `canal` | cajero / app / sucursal / web |
| `id_sucursal` | Codigo de sucursal |
| `sucursal` | Nombre de sucursal |
| `estado` | aprobada / rechazada |

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_transacciones`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cual es el monto total aprobado por tipo de operacion en ARS?
2. ¿Cuantas transacciones hay por canal?
3. ¿Cual es la cuenta con mas movimientos?
4. ¿Que porcentaje de las transacciones fue rechazado por sucursal?
5. ¿Cuantas transacciones hubo por mes (primeros 6 meses)?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/transacciones.csv`: datos de origen.
