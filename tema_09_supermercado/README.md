# Supermercado: movimientos de stock — SuperAhorro

## Contexto
SuperAhorro lleva el stock de tres sucursales en una base SQLite. Cada sucursal registra ventas, ingresos de mercaderia y mermas. Compras quiere analizar el movimiento por categoria y proveedor.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/stock.sqlite` — SQLite (tabla `movimientos`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_movimiento`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

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

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_movimientos`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cuantas unidades se vendieron por categoria?
2. ¿Cuantas unidades de merma hubo por sucursal?
3. ¿Cuanto se gasto en ingresos de mercaderia (cantidad * costo) por proveedor?
4. ¿Cual es el producto mas vendido en unidades?
5. ¿Cuantos movimientos hubo por mes (primeros 6 meses)?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/stock.sqlite`: datos de origen.
