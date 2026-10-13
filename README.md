# Diagnóstico de marca empleadora con reseñas de Glassdoor — Deloitte vs. Big Four

Caso práctico de NLP (Evolve Academy · Máster en Data Science & IA). Análisis de sentimiento con un transformer preentrenado y topic modeling con BERTopic sobre reseñas de Glassdoor de **Deloitte** y sus peers **PwC, EY y KPMG**, con comparación entre empresas y recomendaciones para RRHH.

## Entregables

| Fichero | Qué es |
|---|---|
| `Diagnostico_Marca_Empleadora_Deloitte.ipynb` | **Notebook único, ejecutado de arriba abajo** (EDA, control de calidad del sentimiento, BERTopic, comparación con peers, actuales vs. antiguos, matriz de priorización, recomendaciones, extras y conclusiones). |
| `Presentacion_Diagnostico_Deloitte.pptx` | **Presentación** para la clase, siguiendo la estructura de la plantilla del profesor (Objetivos / Asunciones, Insights, Validación sentimiento, Análisis topics, Conclusiones y próximos pasos). |
| `figuras/*.png` | Todas las gráficas del notebook, exportadas automáticamente para la presentación. |
| `docs/02_Informe_academico.md` | Informe escrito (no exigido por el profesor; complementa al notebook). |
| `docs/01_Analisis_del_enunciado.md`, `docs/03_Checklist_y_guia_defensa.md` | Análisis del enunciado, checklist de cumplimiento y guía para defender el trabajo. |
| `docs/04_Guion_video.md` | Guion hablado para el vídeo de presentación (también incluido como notas del orador en el `.pptx`) e instrucciones para grabarlo. |
| `requirements.txt` | Dependencias. |

Ficheros del profesor (no modificados, **no incluidos en el repositorio público**): `Caso_Practico_NLP_Glassdoor_RRHH.pdf`, `Analisis de reseñas (plantilla)2.pdf`, `Glassdoor_ejemplo.ipynb`, `lista_con_sector.xlsx`, `glassdoor_reviews_hr.parquet`.

## Aviso

Proyecto académico independiente realizado para el Máster en Data Science & IA de Evolve Academy. **No ha sido encargado, revisado ni avalado por Deloitte ni por ninguna de las firmas analizadas.** Las conclusiones se basan únicamente en reseñas públicas y voluntarias de Glassdoor, con las limitaciones que se detallan en el notebook; no deben leerse como una evaluación oficial de ninguna empresa. No se reproducen reseñas completas ni datos que permitan identificar a sus autores.

## Datos

`glassdoor_reviews_hr.parquet` (≈ 293 MB; 5.004.045 reseñas de 2.796 empresas, julio 2018 – julio 2023) es la unión, preparada por el profesor, de los datasets *Glassdoor Job Reviews* 1 y 2 de David Gauthier (Kaggle; la licencia de uso es la que indica la ficha de cada dataset en Kaggle y debe comprobarse antes de redistribuir los datos). El fichero **no se incluye en ningún repositorio**: debe estar en la misma carpeta que el notebook (o cambiar `RUTA_PARQUET`). El notebook solo lee las cuatro empresas necesarias con el filtro de Parquet.

## Cómo ejecutar

### Google Colab (recomendado, con GPU)
1. Subir el notebook y el fichero Parquet (o montar Google Drive y ajustar `RUTA_PARQUET`).
2. Descomentar la celda de instalación: `!pip install -q "bertopic<0.18" "sentence-transformers<4" umap-learn hdbscan pyarrow`.
3. *Entorno de ejecución → Ejecutar todo*. Con GPU T4 tarda unos 5 minutos.

### Local
```bash
conda create -n glassdoor-nlp python=3.12 -y
conda activate glassdoor-nlp
# En macOS Intel, numba/umap/hdbscan se instalan mejor desde conda-forge:
#   conda install -c conda-forge numpy=1.26 umap-learn hdbscan numba -y
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=-1 Diagnostico_Marca_Empleadora_Deloitte.ipynb
```
En CPU (portátil Intel de 12 núcleos) la ejecución completa tarda unos 15-20 minutos; la primera vez descarga los modelos `distilbert-base-uncased-finetuned-sst-2-english` (~270 MB) y `all-MiniLM-L6-v2` (~90 MB) de Hugging Face.

### Reproducibilidad
- `SEED = 42` en el muestreo por empresa, en UMAP y en los ejemplos aleatorios. HDBSCAN y c-TF-IDF son deterministas.
- Los pasos costosos (sentimiento, embeddings) se guardan en `cache/` si `USAR_CACHE = True`; borrar la carpeta para recalcular desde cero. El notebook entregado se ejecutó **sin caché**.
- Para analizar otra empresa basta con cambiar `TARGET` y `PEERS` en la primera celda de código y volver a ejecutar; la asignación manual de topics a categorías (`CATEGORIA_PROS`, `CATEGORIA_CONS`) debe revisarse de nuevo porque los topics cambian.

## Estructura del notebook

0. Configuración, carga y preparación (muestra de 2.000 reseñas por empresa, semilla 42)
1. EDA (sobre las ~129.000 reseñas de las cuatro firmas)
2. Sentimiento en pros y cons con DistilBERT SST-2 (control de calidad y casos discordantes)
3. Topic modeling con BERTopic: un modelo para pros y otro para cons, tratamiento de outliers, agrupación manual en categorías de negocio
4. Comparación con peers: % por categoría, balance neto, gap, robustez y contraste con rating/recomendación
5. Empleados actuales vs. antiguos (motivos de salida)
6. Matriz de priorización, recomendaciones (máx. 3) y limitaciones
7. Extra: evolución temporal, análisis por recomendación y por antigüedad
8. Conclusiones
