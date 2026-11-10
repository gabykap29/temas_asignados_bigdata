# Turismo: reservas de hotel — ViajaYa Turismo

## 1. Situacion
ViajaYa vende reservas de hoteles a traves de varios canales (web, agencias, telefono). Cada canal carga las reservas distinto. Gerencia quiere conocer ingresos y ocupacion.

Forman parte del area de datos de la empresa. La jefatura les pide construir un **pipeline ETL** que tome estos datos en su estado original,
los valide, separe lo valido de lo rechazado, **recupere** los registros rechazados limpiandolos, y deje una **unica tabla final confiable** para responder preguntas del negocio.
Ademas, el pipeline tiene que ejecutarse **automaticamente todos los dias a las 2:00 AM** con Apache Airflow.

## 2. Los datos
Archivo CSV `datos/reservas.csv` (separador: coma `,`, codificacion UTF-8).
Aprox. **2575 filas**. No se debe modificar el archivo original: el pipeline siempre lo lee desde ahi.

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

La clave unica de cada registro es **`id_reserva`**.

## 3. Que debe construir el equipo
El equipo debe seguir la guia general. Resumen:

1. **Extraer** los datos del origen.
2. **Cargar** los datos tal cual a una tabla de DuckDB: `raw_reservas`.
3. **Validar** con reglas que defina el equipo (al menos 8 reglas distintas; cada una con su nombre).
4. Crear la tabla **`validos`** y la tabla **`rechazados`** (con una columna `motivo_rechazo` que diga por que se rechazo).
5. **Limpiar** los rechazados con pandas o SQL y guardarlos en `rechazados_limpios`.
6. **Unir** `validos` + `rechazados_limpios` en **`tabla_final`** (sin duplicados, con los tipos de dato correctos: fechas como DATE, numeros como numericos).
7. Responder las **preguntas de negocio** con SQL sobre `tabla_final`.
8. Armar el **DAG de Airflow** para que corra todos los dias a las 2 AM.

## 4. Preguntas de negocio
Se debe responder las preguntas de negocio con consultas SQL sobre `tabla_final` (guardando las consultas en `consultas.sql`):

1. ¿Cuanto se recaudo por ciudad?
2. ¿Cuantas noches reservadas hubo por mes de check-in (primeros 6 meses)?
3. ¿Cual es el total promedio por reserva segun tipo de habitacion?
4. ¿Que canal concentra mas reservas?
5. ¿Cual es la estadia promedio (noches) por pais?

## 5. Entregables
- Carpeta del proyecto con `pipeline.py` (o modulos), la carpeta consultas o sql con los archivos.sql y el DAG en `dags/`.
- Archivo `.duckdb` generado.
- Un breve informe (1 pagina): que problemas encontraron, cuantos registros se rechazaron por cada regla y respuestas a las preguntas.
