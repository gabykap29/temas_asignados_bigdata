# E-commerce: pedidos online — Valhalla S.A.

## Contexto
Valhalla S.A es una tienda online que vende tecnologia, hogar e indumentaria en varios paises de Latinoamerica. El area comercial exporta los pedidos desde el sistema de ventas y nadie revisa su calidad. Quieren un pipeline que deje los datos confiables.

## Datos
- **Carpeta:** `datos/`
- **Origen:** `datos/pedidos.csv` — CSV (`,`)
- **Filas aproximadas:** 2575
- **Clave unica:** `id_pedido`
- El archivo original **no se modifica**: el pipeline siempre lo lee desde ahi.

| Columna | Descripcion |
|---|---|
| `id_pedido` | Identificador unico del pedido |
| `fecha_pedido` | Fecha del pedido (YYYY-MM-DD) |
| `id_cliente` | Identificador del cliente |
| `email_cliente` | Email del cliente |
| `pais` | Pais de entrega |
| `ciudad` | Ciudad de entrega |
| `categoria` | Categoria del producto |
| `producto` | Producto comprado |
| `cantidad` | Unidades compradas (> 0) |
| `precio_unitario` | Precio por unidad en ARS |
| `descuento_pct` | Descuento aplicado (0 a 100) |
| `total` | cantidad * precio * (1 - descuento/100) |
| `estado` | entregado / enviado / pendiente / cancelado |
| `medio_pago` | tarjeta / transferencia / efectivo / billetera_virtual |

## Que hay que construir
Un pipeline ETL con DuckDB y un DAG de Airflow (diario, 2:00 AM):

1. Extraer los datos del origen.
2. Cargarlos tal cual en `raw_pedidos`.
3. Validar con al menos 8 reglas con nombre.
4. Separar en `validos` y `rechazados` (con `motivo_rechazo`).
5. Limpiar los rechazados y guardarlos en `rechazados_limpios`.
6. Unir en `tabla_final` (sin duplicados, tipos correctos).
7. Responder las preguntas de negocio con SQL sobre `tabla_final` (en `consultas.sql`).
8. Armar el DAG de Airflow (`0 2 * * *`).

## Preguntas de negocio
1. ¿Cuales son las 3 categorias con mayor facturacion total (excluyendo pedidos cancelados)?
2. ¿Cual es la facturacion por mes?
3. ¿Cual es el ticket promedio (total promedio por pedido) por pais?
4. ¿Que porcentaje de los pedidos fue cancelado?
5. ¿Cual es el medio de pago mas usado y cuantos pedidos tiene?

## Entregables
`pipeline.py`, carpeta de consultas SQL, DAG en `dags/`, archivo `.duckdb` y un informe breve (1 pagina).

## Contenido de la carpeta
- `ENUNCIADO.md`: enunciado completo del tema.
- `datos/pedidos.csv`: datos de origen.
