
# Prueba técnica 
## Data Analyst / Data Scientist

Este repositorio contiene la resolución de una prueba técnica basada en un dataset de solicitudes de compensaciones/comisiones de clientes (`requests.xlsx`).  

El objetivo principal es limpiar y preparar los datos, realizar un análisis exploratorio y generar algunas visualizaciones básicas.

---

## Contenido del repositorio

- `data/requests.xlsx`  
  Fichero original con las solicitudes (239.400 registros y 13 columnas).

- Script / notebook principal donde se realiza:
    - Carga y exploración del dataset.
    - Limpieza y transformación de datos.
    - Análisis exploratorio (EDA).
    - Visualizaciones.

- `archivo.xlsx`  
  Fichero resultante tras la limpieza y transformación del dataset original.

---

## Objetivos de la prueba

1. **Exploración inicial de los datos**
   - Carga del fichero `requests.xlsx` con `pandas`.
   - Revisión de tipos de datos, valores nulos y estadísticos básicos.
   - Identificación de columnas clave:
     - `Booking`
     - `Request date`
     - `Requested by`
     - `Authorized by`
     - `Department`
     - `Currency`
     - `Amount`
     - `Reason`
     - `Reason 2`
     - `Status`
     - `CustomerShortname`
     - `CustomerRegion`
     - `Amount COMGES in EUR`

2. **Limpieza y estandarización**

   - **Duplicados**
     - Identificación de reservas duplicadas por `Booking` y `Amount`:
       - Ejemplos: `100/1200589`, `100/1239393`.
     - Eliminación de duplicados manteniendo la primera aparición.

   - **Renombrado de columnas**
     - Eliminación de espacios en nombres de columnas para facilidad de uso en código:
       - `Request date` → `Request_date`
       - `Authorized by` → `Authorized_by`
       - `Amount COMGES in EUR` → `Amount_COMGES_in_EUR`
       - etc.

   - **Corrección de nulos en `Authorized_by`**
     - Detección de filas con `Authorized_by = NaN`.
     - Revisión por región (`CustomerRegion`) para intentar inferir el aprobador.
     - Al no existir patrón claro, se opta por **eliminar esas filas**.

   - **Corrección de correos electrónicos**
     - Validación de formato de correo mediante expresión regular.
     - Identificación de casos sin `@hotelbeds.com` (ej. `user69hotelbeds.com`).
     - Aplicación de una función `change_email` para corregirlos:
       - Inserción del carácter `@` antes de `hotelbeds`.

   - **Moneda y cambio a EUR**
     - Revisión de la columna `Currency` y detección de valores erróneos (ej. `UDS`, `CYN`, `KWR`).
     - Creación de un diccionario de tipos de cambio (`exchange_rates`).
     - Conversión de `Amount` a EUR con una función `convert_to_eur`.
     - Normalización:
       - `Amount` pasa a estar en EUR.
       - `Currency` se fuerza a `"EUR"` en todo el dataset.

   - **Tratamiento de `Reason` y `Reason_2`**
     - Revisión de valores nulos en `Reason`:
       - Detección de casos concretos con cliente `CLIENT113` y `Reason_2 = "CANCELLATION WAIVE"`.
       - Se observa que, para este mismo cliente y tipo de `Reason_2`, la `Reason` es mayoritariamente `"CANCELLATIONS"`.
       - Decisión: imputar los `NaN` de `Reason` como `"CANCELLATIONS"`.
     - Estandarización de valores en `Reason`:
       - Conversión a formato título (`.str.title()`), unificando:
         - `"OTHERS"` → `"Others"`
         - `"BOOKING_OPERATIONAL_ISSUE"` → `"Booking_Operational_Issue"`
         - etc.
     - Tratamiento de `Reason_2`:
       - Sustitución de nulos por `"Unknown"`.
       - Normalización de texto con `.str.title()`.

   - **Eliminación de `Amount_COMGES_in_EUR`**
     - Detección de 8 valores nulos.
     - Decisión: eliminar la columna completa al no ser necesaria tras la conversión de `Amount` a EUR.

   - **Conversión de tipos**
     - Conversión de `Request_date` a tipo `datetime`:
       - `df['Request_date'] = pd.to_datetime(df['Request_date'])`.

3. **Análisis exploratorio (EDA)**

   - **Distribución de tipos de cancelación (`Reason`)**
     - Conteo de valores y gráfico de barras:
       - `Others` es la categoría dominante (más de 230k registros).
       - Resto de categorías con volúmenes mucho menores:
         - `Booking_Operational_Issue`
         - `Booking_Technical_Issue`
         - `Cancellations`
         - `Rate_Error`
         - `Operational Issues`.

   - **Distribución de importes (`Amount`)**
     - Boxplot para detectar:
       - Rango típico de gastos.
       - Existencia de outliers (p.ej. importes muy elevados cercanos a 300.000).

   - **Cancelaciones por región (`CustomerRegion`)**
     - Gráfico de barras:
       - Mayor número de solicitudes en `Region 1`, seguida de `Region 2` y `Region 3`.
       - `Region 4` y `Region 5` con menor volumen.

   - **Evolución temporal de solicitudes**
     - Agrupación por `Request_date` y representación en gráfico de líneas:
       - Picos claros de volumen en determinadas fechas.
       - Visión global de la evolución diaria de solicitudes.

   - **Usuarios que más solicitan (`Requested_by`)**
     - Cálculo de frecuencia por correo.
     - Top 10 solicitantes:
       - `user23@hotelbeds.com` destaca muy por encima del resto (más de 200k solicitudes).
       - Resto de usuarios con un número de peticiones muy inferior.

---

## Resultados principales

- Dataset final: **239.395 registros** y **12 columnas** completamente limpias, sin valores nulos.
- Moneda unificada en **EUR** para facilitar comparativas y análisis.
- Correos y motivos (`Reason`, `Reason_2`) estandarizados y listos para modelado o análisis posterior.
- Identificación de patrones:
  - Dominancia de la categoría `Others` en `Reason`.
  - Concentración de solicitudes en:
    - Determinados usuarios (`Requested_by`).
    - Determinadas regiones (`Region 1` y `Region 2`).
  - Picos temporales de solicitudes, útiles para análisis de carga u operativa.

---

## Requisitos

- Python 3.10+ (recomendado)
- Dependencias principales:
  - `pandas`
  - `numpy`
  - `openpyxl`
  - `matplotlib`
  - `seaborn`
---
##  Ejecución
1. Clonar el repositorio: `git clone <URL_DEL_REPO>`
2. `cd <NOMBRE_DEL_REPO>`
3. Crear entorno virtual (opcional, pero recomendado):
   - `python -m venv .venv`
   - `source .venv/bin/activate`  # En Windows: .venv\Scripts\activate

Instalar dependencias:
`pip install -r requirements.txt`

--- 
## Estructura de datos final

Columnas del dataframe limpio (df):
- Booking (object): identificador de reserva.
- Request_date (datetime64[ns]): fecha de la solicitud.
- Requested_by (object): email del solicitante (formato validado).
- Authorized_by (object): email del aprobador (sin nulos).
- Department (object): departamento (en este caso, siempre "Sales").
- Currency (object): moneda, normalizada a "EUR".
- Amount (float): importe de la solicitud en EUR.
- Reason (object): motivo principal de la solicitud (cancelación, error, etc.).
- Reason_2 (object): motivo detallado / subcategoría.
- Status (object): estado de la solicitud (Applied, Removed, Requested, etc.).
- CustomerShortname (object): identificador abreviado del cliente.
- CustomerRegion (object): región del cliente (Region 1–Region 5).

Autor
- Nombre: Yanelis Serrano Taylor
- Rol: Data Analyst / Full Stack Web
- Contacto: yanelis@adalab.es 