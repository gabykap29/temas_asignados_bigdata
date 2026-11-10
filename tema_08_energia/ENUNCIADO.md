# Energia: lecturas de medidores — EnergiaViva S.A.

## 1. Situacion
EnergiaViva recibe lecturas de medidores inteligentes. Algunos medidores viejos mandan los datos con otro formato. Quieren facturar y detectar los barrios de mayor consumo.

Forman parte del area de datos de la empresa. La jefatura les pide construir un **pipeline ETL** que tome estos datos en su estado original,
los valide, separe lo valido de lo rechazado, **recupere** los registros rechazados limpiandolos, y deje una **unica tabla final confiable** para responder preguntas del negocio.
Ademas, el pipeline tiene que ejecutarse **automaticamente todos los dias a las 2:00 AM** con Apache Airflow.

## 2. Los datos
Archivo CSV `datos/lecturas.csv` (separador: coma `,`, codificacion UTF-8).
Aprox. **2575 filas**. No se debe modificar el archivo original: el pipeline siempre lo lee desde ahi.

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

La clave unica de cada registro es **`id_lectura`**.

## 3. Que debe construir el equipo
El equipo debe seguir la guia general. Resumen:

1. **Extraer** los datos del origen.
2. **Cargar** los datos tal cual a una tabla de DuckDB: `raw_lecturas`.
3. **Validar** con reglas que defina el equipo (al menos 8 reglas distintas; cada una con su nombre).
4. Crear la tabla **`validos`** y la tabla **`rechazados`** (con una columna `motivo_rechazo` que diga por que se rechazo).
5. **Limpiar** los rechazados con pandas o SQL y guardarlos en `rechazados_limpios`.
6. **Unir** `validos` + `rechazados_limpios` en **`tabla_final`** (sin duplicados, con los tipos de dato correctos: fechas como DATE, numeros como numericos).
7. Responder las **preguntas de negocio** con SQL sobre `tabla_final`.
8. Armar el **DAG de Airflow** para que corra todos los dias a las 2 AM.

## 4. Preguntas de negocio
Se debe responder las preguntas de negocio con consultas SQL sobre `tabla_final` (guardando las consultas en `consultas.sql`):

1. ¿Cual es el consumo total por tipo de cliente?
2. ¿Que barrio tiene el mayor consumo total?
3. ¿Cuanto se facturo por mes (primeros 6 meses)?
4. ¿Cuales son los 5 medidores con mayor consumo?
5. ¿Cual es el consumo promedio por barrio?

## 5. Entregables
- Carpeta del proyecto con `pipeline.py` (o modulos), la carpeta consultas o sql con los archivos.sql y el DAG en `dags/`.
- Archivo `.duckdb` generado.
- Un breve informe (1 pagina): que problemas encontraron, cuantos registros se rechazaron por cada regla y respuestas a las preguntas.
