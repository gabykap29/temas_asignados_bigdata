# Instituto: inscripciones y notas — Instituto Superior Horizonte

## 1. Situacion
El Instituto Horizonte carga las notas en planillas que despues se juntan en un CSV. Direccion academica quiere indicadores de aprobacion pero los datos tienen errores de carga.

Forman parte del area de datos de la empresa. La jefatura les pide construir un **pipeline ETL** que tome estos datos en su estado original,
los valide, separe lo valido de lo rechazado, **recupere** los registros rechazados limpiandolos, y deje una **unica tabla final confiable** para responder preguntas del negocio.
Ademas, el pipeline tiene que ejecutarse **automaticamente todos los dias a las 2:00 AM** con Apache Airflow.

## 2. Los datos
Archivo CSV `datos/inscripciones.csv` (separador: coma `,`, codificacion UTF-8).
Aprox. **2575 filas**. No se debe modificar el archivo original: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_inscripcion` | Id unico |
| `id_alumno` | Id del alumno |
| `nombre_alumno` | Nombre completo |
| `carrera` | Carrera |
| `materia` | Materia |
| `fecha_inscripcion` | Fecha (YYYY-MM-DD) |
| `nota_parcial` | Nota de 1 a 10 |
| `nota_final` | Nota de 1 a 10 |
| `condicion` | aprobado (>= 6) / desaprobado |

La clave unica de cada registro es **`id_inscripcion`**.

## 3. Que debe construir el equipo
El equipo debe seguir la guia general. Resumen:

1. **Extraer** los datos del origen.
2. **Cargar** los datos tal cual a una tabla de DuckDB: `raw_inscripciones`.
3. **Validar** con reglas que defina el equipo (al menos 8 reglas distintas; cada una con su nombre).
4. Crear la tabla **`validos`** y la tabla **`rechazados`** (con una columna `motivo_rechazo` que diga por que se rechazo).
5. **Limpiar** los rechazados con pandas o SQL y guardarlos en `rechazados_limpios`.
6. **Unir** `validos` + `rechazados_limpios` en **`tabla_final`** (sin duplicados, con los tipos de dato correctos: fechas como DATE, numeros como numericos).
7. Responder las **preguntas de negocio** con SQL sobre `tabla_final`.
8. Armar el **DAG de Airflow** para que corra todos los dias a las 2 AM.

## 4. Preguntas de negocio
Se debe responder las preguntas de negocio con consultas SQL sobre `tabla_final` (guardando las consultas en `consultas.sql`):

1. ¿Cual es la nota final promedio por materia?
2. ¿Cual es el porcentaje de aprobacion por carrera?
3. ¿Cuales son los 5 alumnos (id_alumno) con mejor promedio de nota final (minimo 5 inscripciones)?
4. ¿Cuantas inscripciones hubo por mes (primeros 6 meses)?
5. ¿Que materia tiene mas desaprobados?

## 5. Entregables
- Carpeta del proyecto con `pipeline.py` (o modulos), la carpeta consultas o sql con los archivos.sql y el DAG en `dags/`.
- Archivo `.duckdb` generado.
- Un breve informe (1 pagina): que problemas encontraron, cuantos registros se rechazaron por cada regla y respuestas a las preguntas.
