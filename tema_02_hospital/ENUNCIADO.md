# Clinica: turnos medicos — Clinica Santa Lucia

## 1. Situacion
La Clinica Santa Lucia registra sus turnos en un sistema viejo (base SQLite). Los datos se cargaron a mano durante años y tienen errores. La direccion medica quiere medir ausentismo e ingresos.

Forman parte del area de datos de la empresa. La jefatura les pide construir un **pipeline ETL** que tome estos datos en su estado original,
los valide, separe lo valido de lo rechazado, **recupere** los registros rechazados limpiandolos, y deje una **unica tabla final confiable** para responder preguntas del negocio.
Ademas, el pipeline tiene que ejecutarse **automaticamente todos los dias a las 2:00 AM** con Apache Airflow.

## 2. Los datos
Base SQLite `datos/clinica.sqlite`, tabla `turnos`.
Se lee con `sqlite3` o con la extension `sqlite` de DuckDB (`ATTACH 'archivo.sqlite' (TYPE sqlite)`).
Aprox. **2575 filas**. No se debe modificar el archivo original: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_turno` | Id unico del turno |
| `fecha_turno` | Fecha (YYYY-MM-DD) |
| `id_paciente` | Id del paciente |
| `edad` | Edad en años (> 0) |
| `especialidad` | Especialidad medica |
| `id_medico` | Id del medico |
| `medico` | Nombre del medico |
| `obra_social` | Cobertura del paciente |
| `costo` | Costo del turno en ARS |
| `estado` | atendido / ausente / cancelado |

La clave unica de cada registro es **`id_turno`**.

## 3. Que debe construir el equipo
El equipo debe seguir la guia general. Resumen:

1. **Extraer** los datos del origen.
2. **Cargar** los datos tal cual a una tabla de DuckDB: `raw_turnos`.
3. **Validar** con reglas que defina el equipo (al menos 8 reglas distintas; cada una con su nombre).
4. Crear la tabla **`validos`** y la tabla **`rechazados`** (con una columna `motivo_rechazo` que diga por que se rechazo).
5. **Limpiar** los rechazados con pandas o SQL y guardarlos en `rechazados_limpios`.
6. **Unir** `validos` + `rechazados_limpios` en **`tabla_final`** (sin duplicados, con los tipos de dato correctos: fechas como DATE, numeros como numericos).
7. Responder las **preguntas de negocio** con SQL sobre `tabla_final`.
8. Armar el **DAG de Airflow** para que corra todos los dias a las 2 AM.

## 4. Preguntas de negocio
Se debe responder las preguntas de negocio con consultas SQL sobre `tabla_final` (guardando las consultas en `consultas.sql`):

1. ¿Cuantos turnos hubo por especialidad?
2. ¿Cual es el porcentaje de ausentismo por especialidad?
3. ¿Cuanto se facturo en turnos atendidos por mes (primeros 6 meses)?
4. ¿Cuales son los 5 medicos con mas turnos atendidos?
5. ¿Cual es la edad promedio de los pacientes por obra social?

## 5. Entregables
- Carpeta del proyecto con `pipeline.py` (o modulos), la carpeta consultas o sql con los archivos.sql y el DAG en `dags/`.
- Archivo `.duckdb` generado.
- Un breve informe (1 pagina): que problemas encontraron, cuantos registros se rechazaron por cada regla y respuestas a las preguntas.
