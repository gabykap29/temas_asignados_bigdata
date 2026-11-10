# RRHH: empleados y salarios — TecnoSur S.A.

## 1. Situacion
El area de Recursos Humanos de TecnoSur guarda a sus empleados en una base SQLite alimentada con planillas de Excel cargadas por varias personas. Necesitan datos limpios para el reporte anual de salarios.

Forman parte del area de datos de la empresa. La jefatura les pide construir un **pipeline ETL** que tome estos datos en su estado original,
los valide, separe lo valido de lo rechazado, **recupere** los registros rechazados limpiandolos, y deje una **unica tabla final confiable** para responder preguntas del negocio.
Ademas, el pipeline tiene que ejecutarse **automaticamente todos los dias a las 2:00 AM** con Apache Airflow.

## 2. Los datos
Base SQLite `datos/rrhh.sqlite`, tabla `empleados`.
Se lee con `sqlite3` o con la extension `sqlite` de DuckDB (`ATTACH 'archivo.sqlite' (TYPE sqlite)`).
Aprox. **2575 filas**. No se debe modificar el archivo original: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_empleado` | Id unico |
| `nombre` | Nombre |
| `apellido` | Apellido |
| `email` | Email corporativo |
| `fecha_ingreso` | Fecha de ingreso (YYYY-MM-DD) |
| `departamento` | Tecnologia / Finanzas / RRHH / Comercial |
| `cargo` | Puesto |
| `salario` | Salario mensual en ARS (> 0) |
| `modalidad` | presencial / remoto / hibrido |

La clave unica de cada registro es **`id_empleado`**.

## 3. Que debe construir el equipo
El equipo debe seguir la guia general. Resumen:

1. **Extraer** los datos del origen.
2. **Cargar** los datos tal cual a una tabla de DuckDB: `raw_empleados`.
3. **Validar** con reglas que defina el equipo (al menos 8 reglas distintas; cada una con su nombre).
4. Crear la tabla **`validos`** y la tabla **`rechazados`** (con una columna `motivo_rechazo` que diga por que se rechazo).
5. **Limpiar** los rechazados con pandas o SQL y guardarlos en `rechazados_limpios`.
6. **Unir** `validos` + `rechazados_limpios` en **`tabla_final`** (sin duplicados, con los tipos de dato correctos: fechas como DATE, numeros como numericos).
7. Responder las **preguntas de negocio** con SQL sobre `tabla_final`.
8. Armar el **DAG de Airflow** para que corra todos los dias a las 2 AM.

## 4. Preguntas de negocio
Se debe responder las preguntas de negocio con consultas SQL sobre `tabla_final` (guardando las consultas en `consultas.sql`):

1. ¿Cual es el salario promedio por departamento?
2. ¿Cuanto es la masa salarial total de la empresa?
3. ¿Cuantos empleados hay por modalidad de trabajo?
4. ¿Cuantos empleados ingresaron por año?
5. ¿Cuales son los 3 cargos con mejor salario promedio?

## 5. Entregables
- Carpeta del proyecto con `pipeline.py` (o modulos), la carpeta consultas o sql con los archivos.sql y el DAG en `dags/`.
- Archivo `.duckdb` generado.
- Un breve informe (1 pagina): que problemas encontraron, cuantos registros se rechazaron por cada regla y respuestas a las preguntas.
