# Streaming: reproducciones — StreamPlus

## Contexto
StreamPlus es una plataforma de series y peliculas. Los distintos dispositivos reportan los eventos de reproduccion con formatos distintos. Producto necesita saber que se mira mas y desde donde.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/reproducciones.csv` — CSV (`,`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_reproduccion`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_reproduccion` | Id unico |
| `fecha` | Fecha (YYYY-MM-DD) |
| `id_usuario` | Id de usuario |
| `plan` | basico / estandar / premium |
| `titulo` | Titulo visto |
| `tipo_contenido` | pelicula / serie / documental |
| `genero` | Genero del titulo |
| `minutos_vistos` | Minutos reproducidos (> 0) |
| `dispositivo` | smart_tv / celular / tablet / notebook |
| `pais` | Pais del usuario |

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_reproducciones`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cuantos minutos se vieron por genero?
2. ¿Cuales son los 5 titulos mas vistos (por cantidad de reproducciones)?
3. ¿Cuantos usuarios distintos hay por plan?
4. ¿Cual es el dispositivo mas usado?
5. ¿Cual es el promedio de minutos vistos por tipo de contenido?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/reproducciones.csv`: datos de origen.
