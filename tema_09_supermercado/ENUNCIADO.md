# Supermercado: movimientos de stock — SuperAhorro

## 1. Situacion
SuperAhorro lleva el stock de tres sucursales en una base SQLite. Cada sucursal registra ventas, ingresos de mercaderia y mermas. Compras quiere analizar el movimiento por categoria y proveedor.

Forman parte del area de datos de la empresa. La jefatura les pide construir un **pipeline ETL** que tome estos datos en su estado original,
los valide, separe lo valido de lo rechazado, **recupere** los registros rechazados limpiandolos, y deje una **unica tabla final confiable** para responder preguntas del negocio.
Ademas, el pipeline tiene que ejecutarse **automaticamente todos los dias a las 2:00 AM** con Apache Airflow.

## 2. Los datos
Base SQLite `datos/stock.sqlite`, tabla `movimientos`.
Se lee con `sqlite3` o con la extension `sqlite` de DuckDB (`ATTACH 'archivo.sqlite' (TYPE sqlite)`).
Aprox. **2575 filas**. No se debe modificar el archivo original: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_movimiento` | Id unico |
| `fecha` | Fecha (YYYY-MM-DD) |
| `id_producto` | Codigo del producto |
| `producto` | Nombre |
| `categoria` | Categoria |
| `sucursal` | Sucursal Norte / Sur / Centro |
| `tipo_movimiento` | venta / ingreso / merma |
| `cantidad` | Unidades (> 0) |
| `costo_unitario` | Costo por unidad en ARS |
| `proveedor` | Proveedor |

La clave unica de cada registro es **`id_movimiento`**.

## 3. Que debe construir el equipo
El equipo debe seguir la guia general. Resumen:

1. **Extraer** los datos del origen.
2. **Cargar** los datos tal cual a una tabla de DuckDB: `raw_movimientos`.
3. **Validar** con reglas que defina el equipo (al menos 8 reglas distintas; cada una con su nombre).
4. Crear la tabla **`validos`** y la tabla **`rechazados`** (con una columna `motivo_rechazo` que diga por que se rechazo).
5. **Limpiar** los rechazados con pandas o SQL y guardarlos en `rechazados_limpios`.
6. **Unir** `validos` + `rechazados_limpios` en **`tabla_final`** (sin duplicados, con los tipos de dato correctos: fechas como DATE, numeros como numericos).
7. Responder las **preguntas de negocio** con SQL sobre `tabla_final`.
8. Armar el **DAG de Airflow** para que corra todos los dias a las 2 AM.

## 4. Preguntas de negocio
Se debe responder las preguntas de negocio con consultas SQL sobre `tabla_final` (guardando las consultas en `consultas.sql`):

1. ¿Cuantas unidades se vendieron por categoria?
2. ¿Cuantas unidades de merma hubo por sucursal?
3. ¿Cuanto se gasto en ingresos de mercaderia (cantidad * costo) por proveedor?
4. ¿Cual es el producto mas vendido en unidades?
5. ¿Cuantos movimientos hubo por mes (primeros 6 meses)?

## 5. Entregables
- Carpeta del proyecto con `pipeline.py` (o modulos), la carpeta consultas o sql con los archivos.sql y el DAG en `dags/`.
- Archivo `.duckdb` generado.
- Un breve informe (1 pagina): que problemas encontraron, cuantos registros se rechazaron por cada regla y respuestas a las preguntas.
