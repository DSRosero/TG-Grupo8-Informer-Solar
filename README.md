# Desarrollo y Evaluación de un Modelo Tipo Informer para la Predicción de Irradiancia Solar con Datos Temporales de Alta Frecuencia

**Maestría en Ciencia de Datos — Pontificia Universidad Javeriana Cali**  
**Facultad de Ingeniería y Ciencias**  
**Grupo 8**

---

## Autores

- Laura García Salamanca  
- Daniel Sebastián Rosero Usamá  
- Juan Villalobos Mora  

**Directora:** Sandra Milena Ramírez Buelvas — Pontificia Universidad Javeriana Cali  
**Codirector:** John Barco Jiménez — Universidad CESMAG  

---

## Descripción del proyecto

Este repositorio contiene los notebooks de Python desarrollados como parte del trabajo de grado del Grupo 8 de la Maestría en Ciencia de Datos de la Pontificia Universidad Javeriana Cali. El proyecto tiene como objetivo desarrollar y evaluar un modelo de predicción de irradiancia solar de alta frecuencia basado en la arquitectura Informer, integrando datos históricos de la estación meteorológica Davis Vantage Pro de la Universidad CESMAG en San Juan de Pasto con datos satelitales del proyecto NASA POWER.

La variable objetivo es la irradiancia solar instantánea registrada con resolución de 5 minutos, cuya predicción en horizontes cortos (1, 3 y 6 horas) constituye un insumo crítico para la planificación operativa de sistemas fotovoltaicos institucionales en zonas de alta variabilidad climática como San Juan de Pasto, condicionada por el clima tropical montañoso y la influencia de la Zona de Convergencia Intertropical.

---

## Fuentes de datos

### Estación meteorológica CESMAG

- **Institución:** Universidad CESMAG, San Juan de Pasto, Colombia
- **Instrumento:** Davis Vantage Pro
- **Período:** 1 de agosto de 2013 – 25 de marzo de 2026
- **Resolución temporal:** 5 minutos
- **Variable objetivo:** Irradiancia solar RS (W/m²)
- **Total de registros:** 1,330,560

El archivo de datos de la estación (`Datos_2013_2026_completo.csv`) no se incluye en este repositorio debido a su tamaño (30.4 MB en CSV). Se encuentra disponible en el siguiente enlace de Google Drive, junto con todos los datasets procesados generados por los notebooks:

> **Enlace de acceso a los datos:** [Insertar enlace de Google Drive aquí]

### NASA POWER

- **Proyecto:** NASA POWER (Prediction of Worldwide Energy Resources)
- **Institución:** NASA Langley Research Center
- **URL:** https://power.larc.nasa.gov
- **Coordenadas:** LAT 1.2136 N, LON -77.2986 W
- **Resolución temporal:** Horaria
- **Variable principal:** ALLSKY_SFC_SW_DWN (W/m²)
- **Acceso:** Gratuito, sin registro, API directa

Los datos de NASA POWER se descargan automáticamente mediante el Notebook 00.

---

## Estructura del repositorio

```
TG-Grupo8-Informer-Solar/
│
├── README.md
├── requirements.txt
│
├── 00_descarga_nasa_power_v2.ipynb
├── 01_eda_estacion_cesmag.ipynb
├── 02_preprocesamiento.ipynb
├── 03_eda_post_preprocesamiento.ipynb
└── 04_feature_engineering.ipynb
```

---

## Descripción de los notebooks

### 00_descarga_nasa_power_v2.ipynb

Descarga los datos meteorológicos y de irradiancia solar del proyecto NASA POWER mediante la API pública, para las coordenadas de San Juan de Pasto, cubriendo el período 2013–2026 con resolución horaria. Genera el archivo `nasa_power_pasto_2013_2026_horario.csv`.

**Produce:**
- `datos/nasa_power_pasto_2013_2026_horario.csv` (8.6 MB)
- `figuras/fig_00_nasa_power_verificacion.png`

---

### 01_eda_estacion_cesmag.ipynb

Realiza el análisis exploratorio de datos (EDA) del conjunto de datos históricos de la estación CESMAG. Evalúa la estructura, calidad, completitud y patrones temporales del dataset, identifica anomalías y define los criterios de preprocesamiento.

**Produce:**
- `datos/tabla_01_faltantes_por_anio.csv`
- `datos/tabla_01_descriptivas.csv`
- `datos/tabla_01_outliers.csv`
- `figuras/fig_01a_faltantes.png`
- `figuras/fig_01b_faltantes_rs.png`
- `figuras/fig_01c_rs_distribucion.png`
- `figuras/fig_01d_patrones_temporales.png`
- `figuras/fig_01e_outliers.png`

---

### 02_preprocesamiento.ipynb

Implementa el pipeline completo de preprocesamiento e imputación. Aplica tres técnicas independientes de imputación para los valores faltantes diurnos: interpolación lineal con límite de 2 horas, media climatológica horaria (patrón estacional) y filtro de Kalman estratificado por longitud de gap. Integra los datos de NASA POWER con la serie local mediante interpolación temporal y genera las variables derivadas para el modelado.

**Estrategia de imputación:**
- Franja nocturna (0–5h y 19–23h): RS = 0 (determinista, física solar)
- Franja diurna (6–18h): tres técnicas aplicadas en paralelo

**Produce:**
- `datos/dataset_final_interpolacion.csv` (351.6 MB)
- `datos/dataset_final_kalman.csv` (351.6 MB)
- `datos/dataset_final_media_clim.csv` (351.6 MB)
- `datos/tabla_02_medias_climatologicas.csv`
- `datos/tabla_02_comparacion_imputaciones.csv`
- `datos/tabla_02_desc_post_preprocesamiento.csv`
- `figuras/fig_02a_comparacion_imputaciones.png`
- `figuras/fig_02b_distribucion_imputados.png`
- `figuras/fig_02c_perfil_post_imputacion.png`

---

### 03_eda_post_preprocesamiento.ipynb

Realiza el análisis descriptivo del dataset final después del preprocesamiento, comparando los tres datasets imputados entre sí y con el dataset original. Incluye análisis distribucional, Q-Q plots, patrones temporales, análisis del índice de claridad KT, perfiles de variables NASA POWER, matrices de correlación y criterios formales de selección del dataset para modelado.

**Produce:**
- `datos/tabla_03_descriptivas_comparadas.csv`
- `datos/tabla_03_descriptivas_nasa_power.csv`
- `datos/tabla_03_correlaciones_rs.csv`
- `figuras/fig_03a_distribucion_comparada.png`
- `figuras/fig_03b_qqplot_comparado.png`
- `figuras/fig_03c_patrones_temporales_comparados.png`
- `figuras/fig_03d_boxplot_hora_comparado.png`
- `figuras/fig_03e_kt_analisis.png`
- `figuras/fig_03f_perfiles_nasa_power.png`
- `figuras/fig_03g_heatmap_correlaciones.png`

---

### 04_feature_engineering.ipynb

Construye el conjunto de características de entrada y la infraestructura de acceso dinámico a ventanas temporales para el entrenamiento del modelo Informer y los modelos de referencia (LSTM y GRU). Implementa la clase `InformerDataset` como un Dataset de PyTorch de indexación dinámica que construye cada ventana en tiempo de entrenamiento desde el array normalizado en memoria, evitando la construcción de arrays estáticos de decenas de gigabytes.

**Configuración de ventanas:**
- `seq_len = 288` pasos = 24 horas de contexto
- `label_len = 144` pasos = 12 horas (inicio del decodificador Informer)
- `pred_len = 12 / 36 / 72` pasos = 1h / 3h / 6h de predicción

**Partición temporal:**
- Train (70%): 931,391 registros — 2013-08-01 a 2022-06-08
- Validación (15%): 199,584 registros — 2022-06-08 a 2024-05-01
- Test (15%): 199,585 registros — 2024-05-01 a 2026-03-25

**Produce:**
- `datos/arrays/data_norm.npy` (91.4 MB)
- `datos/arrays/marcas_temp.npy` (20.3 MB)
- `datos/arrays/scaler_full.pkl`
- `datos/arrays/scaler_rs.pkl`
- `datos/arrays/config_fe.pkl`
- `figuras/fig_04a_particion_temporal.png`
- `figuras/fig_04b_ejemplo_ventana.png`
- `figuras/fig_04c_distribucion_targets.png`

---

## Instrucciones de reproducción

### Requisitos previos

- Cuenta de Google con acceso a Google Colab
- Cuenta de Google Drive con al menos 2 GB de espacio disponible
- Acceso a internet para la descarga de datos NASA POWER

### Instalación de dependencias

Cada notebook instala automáticamente sus dependencias al inicio mediante `!pip install`. No se requiere configuración adicional en Google Colab.

Para ejecución en entorno local, instalar las dependencias listadas en `requirements.txt`:

```bash
pip install -r requirements.txt
```

### Orden de ejecución

Los notebooks deben ejecutarse en orden numérico estricto, ya que cada uno depende de los archivos generados por el anterior:

```
00 → 01 → 02 → 03 → 04
```

### Configuración de rutas

Todos los notebooks detectan automáticamente la ruta de los archivos en Google Drive. Al ejecutar la celda de Google Drive en cada notebook, se solicita autorización de acceso. La única configuración manual requerida es colocar el archivo `Datos_2013_2026_completo.csv` en la carpeta del proyecto en Drive antes de ejecutar el Notebook 01.

La estructura de carpetas esperada en Google Drive es:

```
MyDrive/
  Maestria Ciencia de Datos Segundo Semestre/
    Proyecto Aplicado ll/
      Codigos Trabajo de Grado/
        Datos_2013_2026_completo.csv
        datos/
        figuras/
```

---

## Variables de entrada del modelo

| Posición | Variable | Descripción | Unidad |
|---|---|---|---|
| 0 | RS | Irradiancia solar (objetivo) | W/m² |
| 1 | sin_hora | Componente seno del ciclo diario | — |
| 2 | cos_hora | Componente coseno del ciclo diario | — |
| 3 | sin_dia_anio | Componente seno del ciclo anual | — |
| 4 | cos_dia_anio | Componente coseno del ciclo anual | — |
| 5 | sin_mes | Componente seno del ciclo mensual | — |
| 6 | cos_mes | Componente coseno del ciclo mensual | — |
| 7 | ALLSKY_SFC_SW_DWN | Irradiancia solar total en superficie (NASA POWER) | W/m² |
| 8 | CLRSKY_SFC_SW_DWN | Irradiancia cielo despejado (NASA POWER) | W/m² |
| 9 | T2M | Temperatura del aire a 2 m (NASA POWER) | °C |
| 10 | RH2M | Humedad relativa a 2 m (NASA POWER) | % |
| 11 | PRECTOTCORR | Precipitación corregida (NASA POWER) | mm/h |
| 12 | WS10M | Velocidad del viento a 10 m (NASA POWER) | m/s |
| 13 | WD10M_u | Componente zonal del viento | m/s |
| 14 | WD10M_v | Componente meridional del viento | m/s |
| 15 | PS | Presión superficial (NASA POWER) | kPa |
| 16 | SZA | Ángulo cenital solar (NASA POWER) | grados |
| 17 | KT | Índice de claridad (ALLSKY/CLRSKY) | — |

---

## Referencia principal

Taha, A., Makeen, P. & Nazih, N. (2025). Short-term and long-term solar irradiance forecasting with advanced machine learning techniques in Zafarana, Egypt. *Scientific Reports*, 15(1), 39553. https://doi.org/10.1038/s41598-025-24853-4

---

## Licencia

Este repositorio tiene fines académicos exclusivamente. Los datos de la estación meteorológica CESMAG son propiedad de la Universidad CESMAG y su uso está restringido al presente trabajo de investigación.
