# Clinica: turnos medicos — Clinica Santa Lucia

## Contexto
La Clinica Santa Lucia registra sus turnos en un sistema viejo (base SQLite). Los datos se cargaron a mano durante años y tienen errores. La direccion medica quiere medir ausentismo e ingresos.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/clinica.sqlite` — SQLite (tabla `turnos`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_turno`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

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

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_turnos`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cuantos turnos hubo por especialidad?
2. ¿Cual es el porcentaje de ausentismo por especialidad?
3. ¿Cuanto se facturo en turnos atendidos por mes (primeros 6 meses)?
4. ¿Cuales son los 5 medicos con mas turnos atendidos?
5. ¿Cual es la edad promedio de los pacientes por obra social?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/clinica.sqlite`: datos de origen.
