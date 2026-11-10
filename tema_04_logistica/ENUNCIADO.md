# Logistica: envios — RapiCargo Logistica

## 1. Situacion
RapiCargo coordina envios entre ciudades con distintos transportistas. El sistema de cada transportista carga los datos a su manera y el resultado es un CSV desprolijo. Operaciones quiere medir tiempos y costos.

Forman parte del area de datos de la empresa. La jefatura les pide construir un **pipeline ETL** que tome estos datos en su estado original,
los valide, separe lo valido de lo rechazado, **recupere** los registros rechazados limpiandolos, y deje una **unica tabla final confiable** para responder preguntas del negocio.
Ademas, el pipeline tiene que ejecutarse **automaticamente todos los dias a las 2:00 AM** con Apache Airflow.

## 2. Los datos
Archivo CSV `datos/envios.csv` (separador: coma `,`, codificacion UTF-8).
Aprox. **2575 filas**. No se debe modificar el archivo original: el pipeline siempre lo lee desde ahi.

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

La clave unica de cada registro es **`id_envio`**.

## 3. Que debe construir el equipo
El equipo debe seguir la guia general. Resumen:

1. **Extraer** los datos del origen.
2. **Cargar** los datos tal cual a una tabla de DuckDB: `raw_envios`.
3. **Validar** con reglas que defina el equipo (al menos 8 reglas distintas; cada una con su nombre).
4. Crear la tabla **`validos`** y la tabla **`rechazados`** (con una columna `motivo_rechazo` que diga por que se rechazo).
5. **Limpiar** los rechazados con pandas o SQL y guardarlos en `rechazados_limpios`.
6. **Unir** `validos` + `rechazados_limpios` en **`tabla_final`** (sin duplicados, con los tipos de dato correctos: fechas como DATE, numeros como numericos).
7. Responder las **preguntas de negocio** con SQL sobre `tabla_final`.
8. Armar el **DAG de Airflow** para que corra todos los dias a las 2 AM.

## 4. Preguntas de negocio
Se debe responder las preguntas de negocio con consultas SQL sobre `tabla_final` (guardando las consultas en `consultas.sql`):

1. ¿Cual es el promedio de dias de entrega por transportista?
2. ¿Cual es el costo total de envios por transportista?
3. ¿Cuales son las 5 rutas (origen -> destino) con mas envios?
4. ¿Cuantos kg se enviaron en total por destino?
5. ¿Cuantos envios se entregaron en mas de 7 dias?

## 5. Entregables
- Carpeta del proyecto con `pipeline.py` (o modulos), la carpeta consultas o sql con los archivos.sql y el DAG en `dags/`.
- Archivo `.duckdb` generado.
- Un breve informe (1 pagina): que problemas encontraron, cuantos registros se rechazaron por cada regla y respuestas a las preguntas.
