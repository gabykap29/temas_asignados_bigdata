# Instituto: inscripciones y notas — Instituto Superior Horizonte

## Contexto
El Instituto Horizonte carga las notas en planillas que despues se juntan en un CSV. Direccion academica quiere indicadores de aprobacion pero los datos tienen errores de carga.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/inscripciones.csv` — CSV (`,`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_inscripcion`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

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

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_inscripciones`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cual es la nota final promedio por materia?
2. ¿Cual es el porcentaje de aprobacion por carrera?
3. ¿Cuales son los 5 alumnos (id_alumno) con mejor promedio de nota final (minimo 5 inscripciones)?
4. ¿Cuantas inscripciones hubo por mes (primeros 6 meses)?
5. ¿Que materia tiene mas desaprobados?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/inscripciones.csv`: datos de origen.
