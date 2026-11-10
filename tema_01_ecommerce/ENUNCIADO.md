# E-commerce: pedidos online — MercaFacil S.A.

## 1. Situacion
MercaFacil es una tienda online que vende tecnologia, hogar e indumentaria en varios paises de Latinoamerica. El area comercial exporta los pedidos desde el sistema de ventas y nadie revisa su calidad. Quieren un pipeline que deje los datos confiables.

Forman parte del area de datos de la empresa. La jefatura les pide construir un **pipeline ETL** que tome estos datos en su estado original,
los valide, separe lo valido de lo rechazado, **recupere** los registros rechazados limpiandolos, y deje una **unica tabla final confiable** para responder preguntas del negocio.
Ademas, el pipeline tiene que ejecutarse **automaticamente todos los dias a las 2:00 AM** con Apache Airflow.

## 2. Los datos
Archivo CSV `datos/pedidos.csv` (separador: coma `,`, codificacion UTF-8).
Aprox. **2575 filas**. No se debe modificar el archivo original: el pipeline siempre lo lee desde ahi.

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

La clave unica de cada registro es **`id_pedido`**.

## 3. Que debe construir el equipo
El equipo debe seguir la guia general. Resumen:

1. **Extraer** los datos del origen.
2. **Cargar** los datos tal cual a una tabla de DuckDB: `raw_pedidos`.
3. **Validar** con reglas que defina el equipo (al menos 8 reglas distintas; cada una con su nombre).
4. Crear la tabla **`validos`** y la tabla **`rechazados`** (con una columna `motivo_rechazo` que diga por que se rechazo).
5. **Limpiar** los rechazados con pandas o SQL y guardarlos en `rechazados_limpios`.
6. **Unir** `validos` + `rechazados_limpios` en **`tabla_final`** (sin duplicados, con los tipos de dato correctos: fechas como DATE, numeros como numericos).
7. Responder las **preguntas de negocio** con SQL sobre `tabla_final`.
8. Armar el **DAG de Airflow** para que corra todos los dias a las 2 AM.

## 4. Preguntas de negocio
Se debe responder las preguntas de negocio con consultas SQL sobre `tabla_final` (guardando las consultas en `consultas.sql`):

1. ¿Cuales son las 3 categorias con mayor facturacion total (excluyendo pedidos cancelados)?
2. ¿Cual es la facturacion por mes?
3. ¿Cual es el ticket promedio (total promedio por pedido) por pais?
4. ¿Que porcentaje de los pedidos fue cancelado?
5. ¿Cual es el medio de pago mas usado y cuantos pedidos tiene?

## 5. Entregables
- Carpeta del proyecto con `pipeline.py` (o modulos), la carpeta consultas o sql con los archivos.sql y el DAG en `dags/`.
- Archivo `.duckdb` generado.
- Un breve informe (1 pagina): que problemas encontraron, cuantos registros se rechazaron por cada regla y respuestas a las preguntas.
