# Checklist de cumplimiento y guía para defender el trabajo

## Parte A · Checklist: cada requisito del profesor → dónde se cumple

Leyenda: **N** = notebook `Diagnostico_Marca_Empleadora_Deloitte.ipynb` (sección), **P** = presentación `.pptx` (diapositiva), **F** = figura en `figuras/`.

| # | Requisito (fuente en el enunciado) | Dónde se cumple | Estado |
|---|---|---|---|
| 1 | Elegir una empresa y 3-5 peers del mismo sector y tamaño, con justificación (§2 "Elección de empresa y peers") | N cabecera y §0; P 3 | ✔ Deloitte + PwC, EY, KPMG (Big Four; mismo sector en `lista_con_sector.xlsx`; 25-40k reseñas cada una) |
| 2 | Cargar solo la empresa y los peers con el filtro de Parquet (§2) | N §0, `pd.read_parquet(..., filters=...)` | ✔ |
| 3 | Muestra por empresa con semilla fija si hay muchas reseñas (§2) | N §0: 2.000 por firma, `random_state=42`; comprobación de que la muestra conserva rating y % actuales | ✔ |
| 4 | Separar `current` en situación y antigüedad (§3.1) | N §0 `situacion()`, `antiguedad()` | ✔ |
| 5 | Por empresa: nº de reseñas, rating medio, % recomienda, reparto actuales/antiguos (§3.1) | N §1.1 tabla `resumen` + F 01, 02 | ✔ (sobre las 129.115 reseñas) |
| 6 | Evolución del rating en la ventana temporal (§3.1) | N §1.3 + F 04, 05 | ✔ (+ recomendación bruta y entre quienes opinan) |
| 7 | Al menos una conclusión accionable por sección del EDA (§3.1) | N §1.1, §1.2, §1.3 ("Conclusión accionable") | ✔ |
| 8 | Transformer preentrenado sobre pros y cons por separado, `truncation=True` (§3.2, recuadro "Longitud de los textos") | N §2 (`distilbert-base-uncased-finetuned-sst-2-english`, `truncation=True, max_length=512`) | ✔ |
| 9 | % de pros positivos y % de cons negativos por empresa (§3.2) | N §2 tabla `qc` + F 06; P 6 | ✔ 94,7 % / 82,4 % |
| 10 | Leer una muestra de casos discordantes y explicar qué tipo de reseñas son (§3.2) | N §2 (12 + 8 ejemplos de cada tipo y lectura escrita); P 6 | ✔ |
| 11 | BERTopic (u otra técnica justificada) sobre pros y, por separado, sobre cons (§3.3) | N §3 (`tm_pros`, `tm_cons`); P 7-8 | ✔ |
| 12 | Un único modelo con la empresa y todos los peers juntos, uno para pros y otro para cons (regla de comparabilidad) | N §3: `fit_transform` sobre los documentos de las 4 firmas | ✔ |
| 13 | Decidir qué hacer con los outliers y justificarlo (§3.3) | N §3.1 (reasignación por embeddings, justificación de negocio) y §4.1 (robustez) | ✔ |
| 14 | Agrupar los topics en categorías de negocio y revisar a mano (§3.3) | N §3.3 (`CATEGORIA_PROS`, `CATEGORIA_CONS`, notas de revisión, 3 reseñas por categoría, regla automática de validación) | ✔ 33 + 30 topics → 8 categorías + Otros + Sin contenido |
| 15 | % de reseñas por categoría y empresa en pros y cons; comparación con la media de peers (§3.4) | N §4 tablas `pct_pros`, `pct_cons` + F 08, 09 | ✔ |
| 16 | Balance neto (% pros − % cons) y gap frente a la media de peers (§3.4) | N §4 tabla `balance` + F 10, 11; P 9 | ✔ |
| 17 | Contrastar con `overall_rating` y `recommend` (§3.4) | N §4.2 + F 12; P 10 | ✔ |
| 18 | Cons de actuales vs. antiguos en la empresa (§3.5) | N §5.1 + F 13; P 11 | ✔ |
| 19 | Cons de antiguos empleados (motivos de salida) comparados con los peers (§3.5) | N §5.2 + F 14; P 11 | ✔ |
| 20 | Comparar temas y no sentimiento entre actuales y antiguos (§3.5) | N §5 (texto y tabla de rating por situación) | ✔ |
| 21 | Matriz de priorización: peso en cons vs. desventaja frente a peers (§3.6) | N §6 + F 15; P 12 | ✔ |
| 22 | Máximo 3 recomendaciones concretas para RRHH no técnico (§3.6) | N §6.2; P 15 | ✔ 3 recomendaciones |
| 23 | Limitaciones: representatividad, mezcla de países/áreas, precisión de topics (§3.6) | N §6.3; P 16 | ✔ |
| 24 | Extra: evolución temporal de categorías (§3.7, opcional) | N §7.1-7.2 + F 16; P 13 | ✔ |
| 25 | Extra: análisis por antigüedad o recomendación (§3.7, opcional) | N §7.3-7.4 + F 17, 18; P 13 | ✔ (ambos) |
| 26 | Un único notebook limpio y reproducible, ejecutable de arriba abajo sin errores (§4) | Notebook ejecutado completo sin caché (ver README) | ✔ ejecutado en CPU/MPS, 70 celdas, sin errores |
| 27 | Comentarios en el código que expliquen las decisiones (§4) | Todas las celdas de código | ✔ |
| 28 | Celda de conclusiones al final con los hallazgos de negocio (§4) | N §8 | ✔ |
| 29 | Formato `.ipynb` (§4) | `Diagnostico_Marca_Empleadora_Deloitte.ipynb` | ✔ |
| 30 | Presentación en ppt o similar (§4), estructura de la plantilla (Objetivos/Asunciones, Insights, Validación sentimiento, Análisis topics, Conclusiones y próximos pasos) | `Presentacion_Diagnostico_Deloitte.pptx` | ✔ 17 diapositivas; añadir el nombre del alumno/a en la portada |
| 31 | Decisiones técnicas justificadas desde el negocio (§5) | N §2 (modelo), §3 (parámetros, outliers, categorías), §4 (balance neto) | ✔ |
| 32 | Referencias del enunciado (§6) | N celda final; informe §8 | ✔ |

**Pendiente de verificación manual por el alumno/a**

- Poner el nombre en la portada de la presentación y, si el profesor lo pide, ensayar los tiempos (17 diapositivas ≈ 15 min; las de anexo y extras pueden saltarse).
- Si se ejecuta en Google Colab con GPU, los *embeddings* pueden diferir en los últimos decimales y los topics pueden renumerarse. El notebook lo detecta (celda de `comprobar_y_elegir_mapa`) y, si ocurre, usa la asignación automática por léxico y lo avisa; en ese caso conviene revisar de nuevo la tabla de topics y ajustar `CATEGORIA_PROS`/`CATEGORIA_CONS`.
- Comprobar la licencia del dataset en Kaggle si se publica el notebook (en este trabajo el Parquet lo entrega el profesor y no se redistribuye).

## Parte B · Guía de defensa (preguntas probables y respuestas cortas)

**¿Por qué Deloitte y esos peers?** Porque las Big Four reclutan en los mismos campus, ofrecen los mismos servicios (auditoría, fiscal, consultoría) y compiten por el mismo talento; el enunciado exige peers "del mismo sector y tamaño comparable" y la lista del profesor las clasifica en el mismo sector. Todas tienen entre 25.000 y 40.000 reseñas.

**¿Por qué 2.000 reseñas por empresa y no todas?** El enunciado lo sugiere para que los modelos corran en tiempos razonables. Con el mismo número por firma, ninguna domina el modelo de topics conjunto. Comprobé que la muestra conserva el rating medio y el % de actuales de cada firma. El EDA sí usa las 129.000 reseñas porque no necesita modelos.

**¿Por qué un transformer binario y no uno con clase neutral o VADER?** La pregunta de control es binaria: ¿los pros suenan positivos y los cons negativos? Un modelo con clase neutral añadiría ambigüedad y VADER (léxico) no entiende contexto ni negaciones. DistilBERT es rápido (16.000 textos en menos de un minuto con GPU) y está entrenado en inglés, el idioma de las reseñas.

**¿Qué significa `truncation=True`?** Los modelos tipo BERT admiten 512 tokens; los textos más largos se cortan para evitar errores. Como la mediana es de 8-9 palabras, apenas afecta.

**¿Qué son los casos discordantes y qué aprendiste de ellos?** Cons positivos (17,6 %): reseñas sin queja ("no cons"), listas de temas sin palabras negativas y quejas aceptadas con deportividad ("long hours and challenging work", rating 5). Pros negativos (5,3 %): "no hay nada bueno", elogios con jerga o negaciones ("steep learning curve") y pros largos con matices. Enseñan que el sentimiento no sirve para comparar empresas: lo que importa es el tema.

**¿Por qué BERTopic y no LDA?** Textos cortos y vocabulario variado: LDA cuenta palabras y no sabe que "WLB", "work life balance" y "long hours" son lo mismo. BERTopic agrupa por significado (embeddings), no exige fijar el número de topics y tiene un tratamiento explícito de los outliers.

**Explica el pipeline de BERTopic.** (1) `all-MiniLM-L6-v2` convierte cada reseña en un vector de 384 dimensiones; (2) UMAP lo reduce a 5 dimensiones conservando la estructura local; (3) HDBSCAN agrupa por densidad y deja fuera (−1) lo que no está en una zona densa; (4) c-TF-IDF calcula, para cada grupo, las palabras que lo distinguen de los demás; (5) MMR quita palabras redundantes.

**¿Por qué `min_cluster_size=60`?** Un topic debe reunir al menos 60 reseñas (0,75 % de 8.000) para ser un patrón que RRHH pueda accionar, no una anécdota. `min_samples=10` reduce los outliers. Con estos valores salieron 33 topics en pros y 30 en cons, un número manejable para revisar a mano.

**¿Qué hiciste con los outliers y por qué?** Un 24-25 % de reseñas quedaba sin topic. Las reasigné al topic más parecido por embedding porque la comparación entre empresas necesita que cada reseña con contenido cuente; si no, los porcentajes solo reflejarían las opiniones más "prototípicas". Antes, aparté las reseñas realmente vacías ("none", "nothing", "can't think of any") en una categoría *Sin contenido*. Y comprobé que, sin reasignar, los gaps importantes conservan signo y magnitud.

**¿Cómo pasaste de topics a categorías?** Leyendo las palabras clave y las reseñas representativas de cada uno de los 63 topics y asignándolos a 8 categorías de RRHH (las que sugiere el profesor, más flexibilidad y marca/proyectos). Dejé en *Otros* los topics mixtos sin tema dominante. Una regla automática por léxico coincide con mi revisión en el 82-83 % de los topics; las discrepancias son topics mixtos que resolví leyendo las reseñas.

**¿Por qué el balance neto?** Porque los porcentajes de cada empresa suman 100 % y son relativos: si una firma tiene pocas quejas de salario, sube el peso del resto sin que nada empeore. Restar el % en pros del % en cons y comparar con los peers corrige eso.

**¿Cuál es el hallazgo principal?** Deloitte es la Big Four mejor valorada y tiene menos quejas por horas y salario que sus peers. Su debilidad específica es la asignación de proyectos (11 % de los cons vs. 7 %). Y lo que de verdad daña el rating y provoca salidas no son las horas (quien se queja de ellas puntúa 4,02) sino la calidad directiva (3,46; 24 % no recomienda; 14 % de los cons de quienes se van).

**¿Por qué las quejas de horas no bajan el rating?** Es el "contrato" Big Four: horas a cambio de aprendizaje, marca y salario. Se aceptan como parte del trato; solo se convierten en motivo de salida cuando se combinan con una mala gestión del equipo.

**¿Qué limitaciones tiene el análisis?** Glassdoor es voluntario y autoseleccionado; mezcla países y líneas de servicio; cada reseña se asigna a un solo topic aunque hable de varios temas; la agrupación en categorías es un juicio manual; el modelo de sentimiento está entrenado en críticas de cine; y el "% que recomienda" bruto cae por el aumento de "sin opinión", no por un deterioro real.

**¿Qué harías después?** Segmentar por país y puesto (el dataset original de Kaggle los tiene), clasificación multietiqueta por aspectos con un clasificador entrenado sobre las 8.000 etiquetas para cubrir las 129.000 reseñas, seguimiento trimestral automatizado y cruce con encuestas internas y entrevistas de salida.

**¿Es reproducible?** Sí: semilla 42 en el muestreo, en UMAP y en los ejemplos; HDBSCAN y c-TF-IDF son deterministas; el notebook entregado se ejecutó de arriba abajo sin caché. Los embeddings pueden variar en los últimos decimales entre CPU y GPU; el notebook detecta si cambia la numeración de los topics y avisa.
