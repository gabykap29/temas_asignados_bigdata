# Turismo: reservas de hotel — ViajaYa Turismo

## Contexto
ViajaYa vende reservas de hoteles a traves de varios canales (web, agencias, telefono). Cada canal carga las reservas distinto. Gerencia quiere conocer ingresos y ocupacion.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/reservas.csv` — CSV (`,`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_reserva`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_reserva` | Id unico |
| `fecha_reserva` | Fecha en que se reservo (YYYY-MM-DD) |
| `fecha_checkin` | Ingreso (YYYY-MM-DD) |
| `fecha_checkout` | Salida (YYYY-MM-DD) |
| `hotel` | Hotel |
| `ciudad` | Ciudad |
| `pais` | Pais |
| `tipo_habitacion` | simple / doble / suite |
| `huespedes` | Cantidad (> 0) |
| `noches` | checkout - checkin |
| `precio_noche` | USD por noche |
| `total` | noches * precio_noche |
| `canal` | web_propia / booking / agencia / telefono |

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_reservas`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cuanto se recaudo por ciudad?
2. ¿Cuantas noches reservadas hubo por mes de check-in (primeros 6 meses)?
3. ¿Cual es el total promedio por reserva segun tipo de habitacion?
4. ¿Que canal concentra mas reservas?
5. ¿Cual es la estadia promedio (noches) por pais?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/reservas.csv`: datos de origen.
