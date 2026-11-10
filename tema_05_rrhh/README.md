# RRHH: empleados y salarios — TecnoSur S.A.

## Contexto
El area de Recursos Humanos de TecnoSur guarda a sus empleados en una base SQLite alimentada con planillas de Excel cargadas por varias personas. Necesitan datos limpios para el reporte anual de salarios.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/rrhh.sqlite` — SQLite (tabla `empleados`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_empleado`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

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

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_empleados`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cual es el salario promedio por departamento?
2. ¿Cuanto es la masa salarial total de la empresa?
3. ¿Cuantos empleados hay por modalidad de trabajo?
4. ¿Cuantos empleados ingresaron por año?
5. ¿Cuales son los 3 cargos con mejor salario promedio?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/rrhh.sqlite`: datos de origen.
