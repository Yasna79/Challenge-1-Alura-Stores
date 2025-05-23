# Challenge-1-Alura-Stores


# 🛍️ Análisis de Ventas y Desempeño de Tiendas

Este repositorio contiene un script de análisis exploratorio de datos (EDA) aplicado a los datos de ventas de cuatro tiendas. El análisis permite obtener métricas relevantes del rendimiento de cada tienda, incluyendo facturación, categorías más vendidas, productos más y menos vendidos, calificación promedio y porcentaje de gasto en envío.

---

## 📁 Estructura Esperada de los Datos

Se esperan cuatro `DataFrames` de pandas con la misma estructura, llamados:

- `tienda`
- `tienda2`
- `tienda3`
- `tienda4`

Cada uno debe incluir las siguientes columnas:

- `Precio`: Precio del producto vendido.
- `Producto`: Nombre del producto.
- `Categoría del Producto`: Clasificación del producto.
- `Calificación`: Puntuación del producto.
- `Costo de envío`: Costo asociado al envío del producto.

---

## 🔄 Procesamiento Paso a Paso

### 1. **Identificación de Tiendas**
Se asigna un identificador único (`Tienda 1`, `Tienda 2`, etc.) a cada `DataFrame`.

```python
tienda['id'] = 'Tienda 1'
tienda2['id'] = 'Tienda 2'
tienda3['id'] = 'Tienda 3'
tienda4['id'] = 'Tienda 4'
```

---

### 2. **Unificación de Datos**
Se concatenan los cuatro `DataFrames` en uno solo (`all_stores`) para facilitar el análisis conjunto.

```python
all_stores = pd.concat([tienda, tienda2, tienda3, tienda4], ignore_index=True)
```

---

### 3. **Facturación por Tienda**
Se agrupan los datos por tienda y se suman los precios para obtener la facturación total por tienda.

```python
facturacion_tiendas = all_stores.groupby('id')['Precio'].sum().reset_index()
```

**Visualización**: Gráfico de torta (`pie chart`) con la distribución porcentual de ingresos.

![image](https://github.com/user-attachments/assets/a082bcdb-16c8-4584-9935-15e145df8cc7)

---

### 4. **Categoría más Vendida por Tienda**
Se agrupa por tienda y categoría de producto, contando cuántos productos fueron vendidos por categoría.

```python
ventas_categoria = (
    all_stores
    .groupby(['id', 'Categoría del Producto'])['Precio']
    .count()
    .reset_index()
    .sort_values(by=['id', 'Categoría del Producto'], ascending=False,)
)

productos_mayores = ventas_categoria.groupby('id').head(1).reset_index(drop=True).sort_values(by='id')
```

Se extrae la categoría con mayor número de productos vendidos por tienda.

**Visualización**: Gráfico de barras con la categoría más vendida de cada tienda.

![image](https://github.com/user-attachments/assets/59d2cbfc-e3d5-47bc-b8b7-57c60a1e3f78)

---

### 5. **Calificación Promedio por Tienda**
Se calcula el promedio de la columna `Calificación` por tienda.

```python
calificacion_tienda = all_stores.groupby('id')['Calificación'].mean().reset_index()
```

**Visualización**: Gráfico de barras.

![image](https://github.com/user-attachments/assets/0701edcb-6e00-438c-ad5c-2e72f45f6058)

---

### 6. **Producto más y menos Vendido por Tienda**
Se agrupa por tienda y producto, contando cuántas veces se vendió cada uno.

```python
producto_menos_mas_vendidos = (
    all_stores
    .groupby(['id','Producto'])['Precio']
    .agg(['count'])
    .reset_index()
)

producto_mas_vendido =(
    producto_menos_mas_vendidos
    .sort_values(by=['id','count'], ascending=[True, False])
    .groupby(['id'])
    .head(1)
    .reset_index(drop=True)
)

producto_menos_vendido =(
    producto_menos_mas_vendidos
    .sort_values(by=['id','count'], ascending=[True, False])
    .groupby(['id'])
    .tail(1)
    .reset_index(drop=True)
)

resultado = pd.merge(producto_mas_vendido, producto_menos_vendido, on='id')

producto_menos_mas_vendidos = all_stores.groupby(['id','Producto'])['Precio'].count().reset_index()
```

Luego se extrae:
- El producto con más ventas por tienda.
- El producto con menos ventas por tienda.

Se combinan en un `DataFrame` llamado `resultado`.

**Visualización**: Gráfico de barras dobles (verde = más vendido, rojo = menos vendido), con etiquetas de nombre de producto.

![image](https://github.com/user-attachments/assets/62a145ab-07f7-4d77-8529-c0fefc2b3de9)

---

### 7. **Análisis del Gasto en Envío**
Se calcula el gasto total y promedio en envío por tienda.

```python
total_valor_envio = (
    all_stores
    .groupby('id')['Costo de envío']
    .agg(['sum', 'mean'])
    .reset_index()
    .rename(columns={
        'sum': 'total_costo_envio',
        'mean': 'promedio_costo_envio'
    })
)

total_ventas = (
    all_stores
    .groupby('id', as_index=False)['Precio']
    .sum()
    .rename(columns={'Precio': 'total_venta'})

)
porcentajes_gastos = pd.merge(total_valor_envio, total_ventas, on='id')

porcentajes_gastos['porcentaje_gasto__promedio_envio'] = (porcentajes_gastos['total_costo_envio'] * 100) / porcentajes_gastos['total_venta']

```

Luego se calcula el porcentaje que representa el gasto en envío con respecto al total de ventas.

**Visualización**: Gráfico de barras con el porcentaje de gasto en envío.

![image](https://github.com/user-attachments/assets/567d9fae-169f-4015-b2e2-eb1b4ecfcc95)

---

## 📊 Visualizaciones Incluidas

- Gráfico de torta: Distribución de facturación.
- Gráfico de barras: Categoría más vendida.
- Gráfico de barras: Calificación promedio.
- Gráfico de barras dobles: Productos más y menos vendidos.
- Gráfico de barras: Porcentaje de gasto en envío.

---

## 🧰 Librerías Necesarias

- `pandas`
- `matplotlib`
- `numpy`

Instalación rápida:

```bash
pip install pandas matplotlib numpy
```

---

## ✅ Recomendaciones

- Asegúrate de que los `DataFrames` estén limpios y no contengan valores nulos en las columnas clave.
- Puedes adaptar los colores o estilos de los gráficos según tus necesidades de presentación.

