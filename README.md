# Análisis de venta 2024 de una cadena retail en Chile: calidad de datos, concentración de ingresos y rentabilidad

## Descripción del proyecto

Este proyecto corresponde a un proceso de **limpieza, transformación y análisis exploratorio de datos de ventas**, desarrollado utilizando **Python y Pandas** en Google Colab.
    
La empresa vende productos de Tecnología, Hogar, Belleza, Deportes y Oficina en 8 sucursales (Santiago Centro, Providencia, Las Condes, Viña del Mar, La Serena, Concepción, Temuco y Antofagasta), a través de cuatro canales: Tienda, Online, WhatsApp y Marketplace.

---

## Objetivo: responder tres preguntas de gestión:

1. ¿Dónde se concentran los ingresos (categoría, producto, canal y mes)?
2. ¿Las categorías que más venden son también las más rentables?
3. ¿Qué tan confiable es la información registrada y cuánto pesan sus errores?

El proyecto forma parte del desarrollo de habilidades en **Python y análisis de datos**.

- Importar y explorar una base de datos de ventas.
- Identificar problemas de calidad en los datos.
- Detectar y tratar valores nulos.
- Identificar y gestionar registros duplicados.
- Revisar y corregir tipos de datos.
- Validar la consistencia de las variables.
- Crear y validar métricas de ventas.
- Realizar análisis exploratorio de los datos.
- Obtener información relevante sobre ventas, productos, sucursales, canales y vendedores.
- Generar una base de datos limpia para futuros análisis y visualizaciones.

---

## 🗂️ Dataset

El proyecto utiliza una base de datos de ventas con **650 registros y 15 variables**.

### Variables principales

| Variable | Descripción |
|---|---|
| `ID_Venta` | Identificador único de la venta |
| `Fecha` | Fecha en que se realizó la venta |
| `Sucursal` | Sucursal donde se realizó la venta |
| `Categoría` | Categoría del producto |
| `Producto` | Producto vendido |
| `Canal` | Canal utilizado para realizar la venta |
| `Vendedor` | Vendedor asociado a la operación |
| `Cliente_ID` | Identificador del cliente |
| `Unidades` | Cantidad de unidades vendidas |
| `Precio_Unitario` | Precio unitario del producto |
| `Descuento` | Descuento aplicado a la venta 5 = 5% |
| `Costo_Unitario` | Costo unitario del producto |
| `Metodo_Pago` | Método de pago utilizado |
| `Estado_Entrega` | Estado de entrega del pedido |
| `Total_Venta` | Valor total de la venta |

---

## 🛠️ Tecnologías utilizadas

- Python
- Excel
- Google Colab

### Librerías principales

- import pandas as pd
- import numpy as np
- import matplotlib.pyplot as plt

---

# 🔄 Flujo del proyecto

El proyecto se desarrolló siguiendo las siguientes etapas:

1. Importación de librerías.
2. Carga del archivo Excel.
3. Exploración inicial de los datos.
4. Revisión de estructura y tipos de datos.
5. Identificación de valores nulos.
6. Identificación de registros duplicados.
7. Limpieza de datos.
8. Validación de la información.
9. Creación y validación de variables calculadas.
10. Análisis exploratorio.
11. Cálculo de indicadores.
12. Análisis de ventas y rentabilidad.
13. Obtención de conclusiones.

---

# Carga del archivo

El archivo Excel fue cargado en Google Colab utilizando Pandas.

~~~python
df = pd.read_excel("Datos_Proyecto_Intermedio.xlsx")
~~~

---

# Exploración inicial

Una vez cargado el dataset, se realizó una primera revisión para conocer su estructura.

Resultado inicial:

- 650 filas
- 15 columnas

# Información general del dataset

Se utilizó `info()` para revisar tipos de datos y valores no nulos.

~~~python
df.info()
~~~

Esta revisión permitió identificar las variables numéricas, categóricas y de fecha, además de detectar posibles problemas de calidad.

---

# Identificación de valores nulos

Se revisó la cantidad de valores faltantes por columna.

~~~python
df.isnull().sum()
~~~

Los valores nulos identificados inicialmente fueron:

| Columna | Valores nulos |
|---|---:|
| Fecha | 1 |
| Categoría | 1 |
| Producto | 2 |
| Canal | 1 |
| Vendedor | 3 |
| Precio_Unitario | 3 |
| Metodo_Pago | 1 |

Las demás variables no presentaron valores nulos.

---

# Identificación de duplicados

Se verificó la existencia de registros duplicados.

~~~python
df.duplicated().sum()
~~~

Se identificaron:

**4 registros duplicados.**

Estos registros fueron considerados durante el proceso de limpieza para evitar que afectaran los resultados del análisis.

Antes de limpiar, se identificaron y clasificaron todos los errores sobre el archivo original:

| Tipo de error | Detalle | Filas |
|---|---|---:|
| Valores vacíos | Fecha (1), Categoría (1), Producto (2), Canal (1), Vendedor (3), Precio_Unitario (3), Metodo_Pago (1) | 12 |
| Formato incorrecto | Fechas imposibles o en texto (`31/02/2024`, `2024-13-05`, `sin fecha`) | 5 |
| Formato incorrecto | Precios en texto (`$58.700`, `precio pendiente`) | 3 |
| Formato incorrecto | Descuentos en texto (`10%`) | 3 |
| Error de escritura | Categoría (`BELLEZA`, `Deporte`, `Tecno`, `Oficna`) | 7 |
| Error de escritura | Producto (`MONITOR`, `mancuernas`, `balón`...) | 9 |
| Error de escritura | Canal (`TIENDA`, `On line`, `Market place`...) | 10 |
| Fuera de rango | Precio = 1 (7) y precio = 9.999.999 (1) | 8 |
| Fuera de rango | Descuentos de 80%, 55% y -5% | 5 |
| Fuera de rango | Unidades negativas (6) o en cero (2) | 8 |
| Inconsistencia | `Total_Venta` distinto de Precio × Unidades × (1 − Descuento / 100) | 118 |
| Duplicados | Filas duplicadas exactas | 4 |

<img width="855" height="457" alt="Resumen_Errores" src="https://github.com/user-attachments/assets/bd08706a-bb52-42ed-a012-3d1823edf2fa" />

---

# Limpieza de datos

Cada corrección se justifica con evidencia de los propios datos y se aplica por `ID_Venta`, que no cambia si el DataFrame se reordena o filtra.

| Problema | Decisión | Impacto |
|---|---|---|
| Registros duplicados | Se eliminan solo los duplicados exactos | 650 → 646 registros |
| Precios en texto | Se quita `$` y el separador de miles, y se convierte a número | 2 ventas |
| Precios vacíos o "precio pendiente" | Se recupera el precio desde `Total_Venta`, `Unidades` y `Descuento`. En la venta devuelta (total $0) se usa la mediana del mismo producto | 4 ventas |
| Precio de 9.999.999 (Notebook) | Se reemplaza por el precio que explica su total y descuento ($598.200) | 1 venta |
| Precio = 1 | Se reemplaza por el precio coherente con el total o por la mediana del producto | 7 ventas |
| Descuentos `10%`, 80, 55 y -5 | El texto se convierte a número; los valores fuera de rango se reemplazan por el descuento implícito en `Total_Venta` | 8 ventas |
| Fechas vacías o inválidas | Se dejan como `NaT` y solo se excluyen del análisis mensual | 6 registros (0,93% del dataset, $2.457.950 en ventas) |
| Errores de escritura | Se unifican las variantes de Categoría, Canal y Producto | 26 registros |
| Categoría vacía (Botella deportiva) | Se completa con *Deportes*, la categoría de ese producto en todos los demás registros | 1 venta |
| Otros vacíos categóricos | Se rotulan como "Sin producto", "Sin canal", "Sin vendedor" y "Sin método de pago" | 7 registros |
| Unidades negativas o en cero | Se mantienen tal como vienen en el archivo y se documentan | 8 ventas |

---

# Tratamiento de fechas

La columna `Fecha` fue revisada y convertida al formato correspondiente para facilitar posteriormente el análisis temporal.

~~~python
df["Fecha"] = pd.to_datetime(df["Fecha"], errors="coerce")
~~~

Esto permite realizar análisis por:

- Año
- Mes
- Día
- Periodos de venta

---

# Revisión de variables numéricas

Se revisaron las principales variables numéricas del dataset.

~~~python
df["Precio_Unitario"].describe()
~~~

Esta función permite analizar:

- Cantidad de registros.
- Promedio.
- Desviación estándar.
- Mínimo.
- Percentiles.
- Máximo.

Las variables revisadas incluyen:

- Unidades
- Precio_Unitario
- Descuento
- Costo_Unitario
- Total_Venta

---

# Validación de Total_Venta

Una vez limpios los precios y descuentos, `Total_Venta` se recalcula para **todas las filas**. El descuento está en porcentaje entero, por eso se divide por 100:

`Total_Venta = Precio_Unitario × Unidades × (1 − Descuento / 100)`

~~~python
df["Total_Venta"] = (
    df["Precio_Unitario"]
    * df["Unidades"]
    * (1 - df["Descuento"] / 100)
).round(0).astype(int)
~~~

Esto corrige **109 filas** que no cumplían la fórmula, incluidas 12 ventas canceladas o devueltas con montos sin sentido (por ejemplo, $56.615 por 1 unidad de $8.000). Después del recálculo, la fórmula se cumple en las **646 filas**.


---

# Análisis de ventas por categoría

Se agrupó la información para conocer el desempeño de cada categoría.

Este análisis permite identificar qué categorías concentran una mayor proporción de las ventas, resultando ser Tecnología con 60%.

<img width="630" height="470" alt="Ventas_Categoría" src="https://github.com/user-attachments/assets/326a851d-3811-46d1-a516-c41e470ef435" />

---

# Análisis de ventas por canal

Se analizaron las ventas según el canal.

<img width="630" height="470" alt="Ventas_Canal" src="https://github.com/user-attachments/assets/07ad872b-5012-42c1-a0b9-44a38e90f8ea" />

Este análisis permite determinar qué canales generan mayor volumen de ventas, tal como se muestra en la imagen los canales son muy competitivos, no existe uno significativamente predominante.

---

# Análisis de venta por mes:

~~~python
ventas_mes = (
    df_graficos.dropna(subset=["Fecha"])
    .groupby(df_graficos.dropna(subset=["Fecha"])["Fecha"].dt.to_period("M"))["Total_Venta"]
    .sum()
)

~~~
<img width="989" height="490" alt="Ventas_Mes" src="https://github.com/user-attachments/assets/1e55fe3e-c854-4452-82d4-014a168da54d" />


Esto permite identificar tendencias y variaciones en el comportamiento de las ventas, predominando el mes de mayo como mejor mes impulsado por la venta de Notebook y Monitor.

---

#  Análisis de rentabilidad

Base: ventas Entregadas y Pendientes (533 registros).

| Categoría | Ventas | Margen | Margen % |
|---|---:|---:|---:|
| Tecnología | $52.459.695 | $13.120.595 | 25,0% |
| Hogar | $15.455.590 | $5.486.390 | 35,5% |
| Belleza | $11.358.915 | $3.895.615 | 34,3% |
| Deportes | $6.035.090 | $2.586.190 | 42,9% |
| Oficina | $1.836.580 | $838.980 | **45,7%** |

**Indicadores generales**

| Indicador | Valor |
|---|---:|
| Ventas totales | $87.145.870 |
| Costo total | $61.218.100 |
| Margen total | $25.927.770 |
| Margen % | 29,8% |
| Ticket promedio | $163.501 |

<img width="635" height="475" alt="Margen_Categoría" src="https://github.com/user-attachments/assets/f1f40971-fc76-433e-ac4a-ec1278ec054b" />

Este análisis permite complementar la visión de ventas con una perspectiva de rentabilidad, lo que reflejó que la mayor venta no necesariamente está relacionado a una mayor rentabilidad.

---

### Trazabilidad

Las ventas que venían con algún dato vacío se identifican automáticamente desde una copia del archivo original y se marcan con la columna `Dato_Originalmente_Vacio`:

~~~python
df_original = df.copy()   # justo después de cargar el archivo

ids_originalmente_vacios = df_original.loc[df_original.isnull().any(axis=1), "ID_Venta"].unique()
df["Dato_Originalmente_Vacio"] = df["ID_Venta"].isin(ids_originalmente_vacios)
~~~

Estos 12 registros **se mantienen en el dataset**, pero se excluyen de los gráficos para no distorsionar el análisis. Las ventas **Canceladas y Devueltas** (101) también se excluyen de los gráficos, porque no son ventas reales.

~~~python
df_graficos = df[
    ~df["Dato_Originalmente_Vacio"]
    & ~df["Estado_Entrega"].isin(["Cancelado", "Devuelto"])
].copy()
~~~

**Resultado:** el dataset final tiene **646 registros**, y los gráficos usan **533 registros** (Entregados y Pendientes).

---

Otros gráficos de utilidad 

Top 10 productos 

<img width="989" height="590" alt="Top_10_productos" src="https://github.com/user-attachments/assets/8aeb1a24-c661-4907-b0cd-bf49290f20da" />

---

# Principales aprendizajes

Este proyecto permitió aplicar un flujo completo de análisis de datos utilizando Python y Pandas.

Las principales habilidades desarrolladas fueron:

- Importación de datos desde Excel.
- Exploración de datasets.
- Identificación de problemas de calidad.
- Tratamiento de valores faltantes.
- Eliminación de duplicados.
- Transformación de variables.
- Validación de información.
- Uso de `groupby()`.
- Uso de funciones de agregación.
- Análisis temporal.
- Creación de indicadores.
- Análisis de rentabilidad.
- Visualización de datos.
- Interpretación de resultados.

---

# Principales hallazgos

1. **Alta dependencia de una categoría y de un producto.** Tecnología representa el **60,2%** de las ventas y el Notebook, por sí solo, el **37,0%**. Además, el Notebook tiene el **menor margen** de los productos más vendidos (**19,4%**): genera el 37% de las ventas, pero solo cerca del 24% del margen total. El resultado del año depende de muy pocos productos y, además, de los menos rentables.

2. **Liderar en ventas no significa liderar en margen.** Tecnología es la categoría con mayor venta y mayor margen en pesos, pero tiene el **menor margen porcentual (25,0%)**. Oficina (**45,7%**) y Deportes (**42,9%**) son las más rentables por cada peso vendido, aunque son las que menos venden.

3. **Estacionalidad marcada.** **Mayo**, el mes más alto, vendió **3 veces más que noviembre**, el más bajo. La diferencia no se explica por la cantidad de ventas (50 contra 42), sino por un ticket promedio mucho mayor (**$242.000 contra $97.000**), impulsado por Notebook y Monitor.

4. **Canales con participación similar.** WhatsApp lidera con el **27,2%** de las ventas, seguido muy de cerca por Tienda (**26,2%**), Online (**25,2%**) y Marketplace (**21,4%**). Ningún canal es prescindible y WhatsApp compite de igual a igual con la tienda física. Marketplace es el canal que menos vende, pero el de mayor margen porcentual (**32,6%**).

#  Recomendaciones

1. **Diversificar la cartera.** Reducir la dependencia de Tecnología y del Notebook, impulsando las categorías con mayor margen porcentual (Oficina y Deportes). Mientras tanto, asegurar el stock y las condiciones de compra del producto líder, y revisar su precio o su costo, ya que es el producto más vendido y el de menor margen.
2. **Potenciar los canales digitales y la tienda.** Realizar campañas online y promociones de retiro en tienda que permitan aumentar las ventas al momento de retirar un producto.
3. **Monitorear la estacionalidad.** Mayo vendió 3 veces más que noviembre, impulsado por Notebook y Monitor. Antes de planificar inventario y campañas, conviene confirmar si el peak de mayo responde a una campaña especial, ya que no se repite en el resto de los meses.
4. **Mejorar el registro de datos en origen.** El 18% de las filas tenía un `Total_Venta` que no correspondía a su precio, unidades y descuento. Validar estos campos al momento de la venta evitaría reportes de ingresos incorrectos.

---

# Conclusión

La empresa mantiene un margen saludable, pero debe cuidar la dependencia de un solo producto ya que un quiebre de Stock podría perjudicarla considerablemente, así como potenciar los productos y categorías que generen mayor margen para obtener mejores ganancias y por último mantener la competitividad de los canales con campañas y promociones. 
