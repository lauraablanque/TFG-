# Ingenieria del dato
# Paso 1: Instalar librería necesaria para Excel
!pip install openpyxl

# Paso 2: Importar librerías
import pandas as pd
from google.colab import drive
from google.colab import files

# Paso 4: Ruta del archivo Excel en tu Google Drive (ajusta esto)
ruta_archivo = "/content/ARIMAX04052025.xlsx"

# Paso 5: Leer archivo Excel
df = pd.read_excel(ruta_archivo)

# Paso 6: Establecer formato decimal (sin notación científica)
pd.set_option('display.float_format', lambda x: '%.2f' % x)

# Paso 7: Crear resumen estadístico con variables como filas
summary = df.describe().T

# Paso 8: Mostrar el resumen limpio
print("Resumen estadístico (variables como filas, sin notación científica):")
display(summary)

# Paso 9: Guardar en CSV con formato español (coma decimal, punto y coma separador)
summary.to_csv("resumen_comas.csv", sep=';', decimal=',')

# Paso 10: Descargar el archivo
files.download("resumen_comas.csv")
# Paso 1: Crear lista para guardar pares de (variable, fecha)
nulos_lista = []

# Paso 2: Recorrer cada columna y guardar las fechas donde hay nulos
for col in df.columns:
    fechas_nulas = df[df[col].isnull()].index
    for fecha in fechas_nulas:
        nulos_lista.append({"Variable": col, "Fecha": fecha})

# Paso 3: Convertir a DataFrame
nulos_df = pd.DataFrame(nulos_lista)

# Paso 4: Mostrar resultado
print("Lista de variables con valores nulos y sus fechas:")
display(nulos_df)
# Filtrar solo las columnas que tienen valores nulos
columnas_con_nulos = df.columns[df.isnull().any()]

# Crear una tabla con las fechas y solo las columnas con nulos
tabla_nulos = df[df[columnas_con_nulos].isnull().any(axis=1)][['FECHA'] + list(columnas_con_nulos)]

# Mostrar tabla
print("Tabla de fechas con valores nulos por variable:")
display(tabla_nulos)
# Paso 1: Instalar librerías necesarias
!pip install openpyxl

# Paso 2: Importar librerías
import pandas as pd
from google.colab import drive

# Paso 4: Leer archivo desde tu carpeta en Drive
ruta_archivo = "/content/ARIMAX04052025.xlsx"
df = pd.read_excel(ruta_archivo)
df['FECHA'] = pd.to_datetime(df['FECHA'])
df.set_index('FECHA', inplace=True)

# Paso 5: Guardar copia para comparar antes y después
df_original = df.copy()

# Paso 6: Interpolar solo las columnas con nulos conocidas
columnas_a_interpolar = ['INDICADOR_ADR', 'INDICADOR_RVPAR']
df[columnas_a_interpolar] = df[columnas_a_interpolar].interpolate()

# Paso 7: Crear tabla con valores interpolados
cambios = []

for col in columnas_a_interpolar:
    nulos_previos = df_original[col].isnull()
    for fecha in df.index[nulos_previos]:
        cambios.append({
            "Fecha": fecha.strftime('%Y-%m'),
            "Variable": col,
            "Nuevo Valor": round(df.loc[fecha, col], 2)
        })

df_cambios = pd.DataFrame(cambios)

# Paso 8: Mostrar tabla con resultados
print("Valores reemplazados mediante interpolación lineal:")
display(df_cambios)

# Paso 9 (opcional): Guardar nueva tabla en CSV
df_cambios.to_csv("valores_interpolados.csv", index=False)

# Guardar los 202 registros completos (con interpolación aplicada)
df.to_excel("tabla_completa_interpolada.xlsx")

# Descargar el archivo desde Google Colab
from google.colab import files
files.download("tabla_completa_interpolada.xlsx")
# Función para detectar outliers con método IQR
def detectar_outliers_iqr(df, columnas_numericas):
    outliers = []

    for col in columnas_numericas:
        Q1 = df[col].quantile(0.25)
        Q3 = df[col].quantile(0.75)
        IQR = Q3 - Q1
        limite_inferior = Q1 - 1.5 * IQR
        limite_superior = Q3 + 1.5 * IQR

        outliers_col = df[(df[col] < limite_inferior) | (df[col] > limite_superior)]

        for fecha in outliers_col.index:
            outliers.append({
                "Fecha": fecha.strftime('%Y-%m'),
                "Variable": col,
                "Valor": df.loc[fecha, col]
            })

    return pd.DataFrame(outliers)

# Detectar columnas numéricas
columnas_numericas = df.select_dtypes(include='number').columns

# Detectar outliers
outliers_df = detectar_outliers_iqr(df, columnas_numericas)

# Agrupar y contar outliers por variable
recuento_outliers = outliers_df.groupby("Variable").size().reset_index(name="Número de Outliers")

# Mostrar tabla resumen
print(" Recuento de outliers por variable:")
display(recuento_outliers)
import matplotlib.pyplot as plt
import seaborn as sns

# Filtrar solo las columnas numéricas
columnas_numericas = df.select_dtypes(include='number').columns

# Establecer el número de columnas por fila en la figura
n_col = 4
n_fil = (len(columnas_numericas) + n_col - 1) // n_col

# Crear subplots
plt.figure(figsize=(n_col * 5, n_fil * 4))

for i, col in enumerate(columnas_numericas, 1):
    plt.subplot(n_fil, n_col, i)
    sns.boxplot(y=df[col], color='skyblue')
    plt.title(f"Boxplot de {col}")
    plt.tight_layout()

plt.show()
# Paso 1: Detectar columnas numéricas
columnas_numericas = df.select_dtypes(include='number').columns

# Paso 2: Detectar outliers con valor 0 usando IQR
outliers_cero = []

for col in columnas_numericas:
    Q1 = df[col].quantile(0.25)
    Q3 = df[col].quantile(0.75)
    IQR = Q3 - Q1
    limite_inferior = Q1 - 1.5 * IQR

    # Identificar valores exactamente 0 y que sean outliers por debajo
    outliers_col = df[(df[col] == 0) & (df[col] < limite_inferior)]

    for fecha in outliers_col.index:
        outliers_cero.append({
            "Fecha": fecha,
            "Variable": col
        })

# Paso 3: Crear copia original para comparación
df_original_zeros = df.copy()

# Paso 4: Reemplazar los ceros que son outliers con NaN temporalmente
for outlier in outliers_cero:
    df.at[outlier["Fecha"], outlier["Variable"]] = None

# Paso 5: Aplicar interpolación lineal en todas las columnas afectadas
columnas_afectadas = list({o["Variable"] for o in outliers_cero})
df[columnas_afectadas] = df[columnas_afectadas].interpolate()

# Paso 6: Mostrar los cambios realizados
cambios_zeros = []

for o in outliers_cero:
    fecha = o["Fecha"]
    var = o["Variable"]
    nuevo_valor = df.loc[fecha, var]
    cambios_zeros.append({
        "Fecha": fecha.strftime('%Y-%m'),
        "Variable": var,
        "Nuevo Valor": round(nuevo_valor, 2)
    })

df_cambios_cero = pd.DataFrame(cambios_zeros)

# Mostrar tabla con los valores reemplazados
print("Valores 0 considerados outliers y reemplazados por interpolación lineal:")
display(df_cambios_cero)
# Paso 1: Guardar DataFrame corregido en Excel
df.to_excel("tabla_final_corregida.xlsx")

# Paso 2: Descargar el archivo
from google.colab import files
files.download("tabla_final_corregida.xlsx")
import seaborn as sns
import matplotlib.pyplot as plt

# Filtrar solo las columnas numéricas
columnas_numericas = df.select_dtypes(include='number')

# Calcular la matriz de correlación
correlacion = columnas_numericas.corr()

# Graficar heatmap
plt.figure(figsize=(10, 8))
sns.heatmap(correlacion, annot=True, fmt=".2f", cmap="coolwarm", square=True, linewidths=0.5)
plt.title("Matriz de Correlación")
plt.tight_layout()
plt.show()
# Eliminar la variable INDICADOR_RVPAR del DataFrame
df.drop(columns='INDICADOR_RVPAR', inplace=True)
import matplotlib.pyplot as plt
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf

# Asegurarse de que el índice sea datetime
df.index = pd.to_datetime(df.index)

# Seleccionar solo variables numéricas
columnas_numericas = df.select_dtypes(include='number').columns

# Crear una figura para cada variable con su evolución, FAC y FCAP
for col in columnas_numericas:
    serie = df[col].dropna()

    fig, axes = plt.subplots(1, 3, figsize=(18, 4))
    fig.suptitle(f"{col}", fontsize=16)

    # 1. Serie temporal
    axes[0].plot(df.index, df[col])
    axes[0].set_title("Serie temporal")
    axes[0].set_xlabel("Fecha")
    axes[0].set_ylabel(col)
    axes[0].grid(True)

    # 2. FAC
    plot_acf(serie, ax=axes[1], lags=30)
    axes[1].set_title("FAC")

    # 3. FCAP
    plot_pacf(serie, ax=axes[2], lags=30, method='ywm')
    axes[2].set_title("FCAP")

    plt.tight_layout()
    plt.show()
    from statsmodels.tsa.stattools import adfuller
import pandas as pd

# Asegurarse de que el índice sea datetime (por si acaso)
df.index = pd.to_datetime(df.index)

# Aplicar test de Dickey-Fuller a cada variable numérica
columnas_numericas = df.select_dtypes(include='number').columns
resultados_adf = []

for col in columnas_numericas:
    serie = df[col].dropna()
    resultado = adfuller(serie)
    p_valor = resultado[1]
    conclusion = "Se rechaza H0 (Estacionaria)" if p_valor < 0.05 else "No se rechaza H0 (No estacionaria)"

    resultados_adf.append({
        "Variable": col,
        "ADF Statistic": round(resultado[0], 4),
        "p-valor": round(p_valor, 4),
        "Conclusión": conclusion
    })

# Crear DataFrame con resultados
tabla_adf = pd.DataFrame(resultados_adf)

# Mostrar tabla
from IPython.display import display
print("Resultados del Test de Dickey-Fuller aumentado (ADF) sobre el DataFrame final:")
display(tabla_adf)

# (Opcional) Guardar y descargar
tabla_adf.to_excel("resultados_adf_df_final.xlsx", index=False)

from google.colab import files
files.download("resultados_adf_df_final.xlsx")
# Paso 1: Instalar librerías si no están
!pip install openpyxl

# Paso 2: Importar librerías necesarias
import pandas as pd
from statsmodels.tsa.stattools import adfuller

# Paso 3: Asegurarse de que la fecha es índice
df.index = pd.to_datetime(df.index)

# Paso 4: Copiar df original para aplicar diferencias
df_diff = df.copy()

# Paso 5: Analizar cuántas diferencias necesita cada variable
resultados_diferencias = []

for col in df.select_dtypes(include='number').columns:
    serie = df[col].dropna()
    d = 0
    estacionaria = False

    while not estacionaria and d <= 3:  # Limitar a 3 diferencias para evitar sobreajuste
        adf_result = adfuller(serie.dropna())
        p_valor = adf_result[1]
        if p_valor < 0.05:
            estacionaria = True
        else:
            d += 1
            serie = serie.diff()

    resultados_diferencias.append({
        "Variable": col,
        "Diferencias necesarias": d,
        "p-valor final": round(p_valor, 4),
        "Conclusión": "Estacionaria" if estacionaria else "No estacionaria"
    })

    # Aplicar diferencias a la nueva tabla
    if d > 0:
        df_diff[col] = df[col].diff(periods=d)

# Paso 6: Eliminar filas con NaN creadas por la diferenciación
df_diff.dropna(inplace=True)

# Paso 7: Crear y mostrar tabla resumen
tabla_diferencias = pd.DataFrame(resultados_diferencias)

from IPython.display import display
print("Diferencias necesarias para hacer estacionarias las variables:")
display(tabla_diferencias)

# Paso 8 (opcional): Guardar nueva tabla final
df_diff.to_excel("df_final_diferenciada.xlsx")
from google.colab import files
files.download("df_final_diferenciada.xlsx")
