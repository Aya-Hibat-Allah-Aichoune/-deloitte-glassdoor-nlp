# Diagnóstico de marca empleadora con reseñas de Glassdoor: Deloitte frente a PwC, EY y KPMG

**Caso práctico de NLP · Máster en Data Science & IA (Evolve Academy)**
Entregables principales: `Diagnostico_Marca_Empleadora_Deloitte.ipynb` (notebook ejecutado) y `Presentacion_Diagnostico_Deloitte.pptx`. Este informe resume el trabajo y documenta las decisiones; todas las cifras proceden del notebook.

---

## 1. Introducción y objetivos

El departamento de Recursos Humanos de Deloitte quiere saber qué dicen sus empleados y exempleados de la firma como empleador, qué puntos fuertes puede explotar en *employer branding* y selección, y qué áreas de mejora están dañando su reputación o provocando salidas. La pregunta no es "¿estamos bien?" sino "¿estamos mejor o peor que las firmas con las que competimos por el mismo talento?": un problema solo es prioritario si estamos peor que el mercado.

Objetivos concretos:

1. Describir la situación de Deloitte como empleador (rating, recomendación, empleados actuales vs. antiguos, evolución 2018-2023) frente a PwC, EY y KPMG.
2. Verificar la calidad del dato textual con un clasificador de sentimiento preentrenado (los *pros* deben ser positivos y los *cons* negativos).
3. Descubrir **de qué hablan** los empleados en pros y cons mediante topic modeling (BERTopic) y traducir los topics a categorías accionables por RRHH.
4. Comparar el peso de cada categoría entre Deloitte y sus peers con una métrica que no dependa de que los porcentajes sumen 100 % (balance neto).
5. Identificar motivos de salida (cons de antiguos empleados) y priorizar áreas de mejora.
6. Proponer un máximo de tres recomendaciones concretas y explicar las limitaciones.

No hay variable objetivo: es un ejercicio de NLP descriptivo, no predictivo.

## 2. Contexto teórico

### 2.1 De la bolsa de palabras a los embeddings contextuales
Las representaciones clásicas (one-hot, *bag of words*, n-gramas, TF-IDF) cuentan palabras y no capturan sinónimos ni contexto: "WLB", "work life balance" y "long hours" son vectores sin relación. Los *embeddings* estáticos (Word2Vec, GloVe, FastText) sitúan palabras parecidas cerca en un espacio vectorial, pero asignan un único vector a cada palabra. Los modelos *transformer* (Vaswani et al., 2017) producen representaciones **contextuales**: el vector de "balance" depende de la frase. BERT (Devlin et al., 2019) se preentrena con enmascarado de palabras sobre grandes corpus y se ajusta después a tareas concretas.

### 2.2 DistilBERT y la clasificación de sentimiento
DistilBERT (Sanh et al., 2019) es una versión destilada de BERT: conserva el 97 % de su rendimiento con un 40 % menos de parámetros y un 60 % más de velocidad. El modelo `distilbert-base-uncased-finetuned-sst-2-english` está ajustado sobre el *Stanford Sentiment Treebank* (SST-2) y devuelve dos clases, POSITIVE y NEGATIVE, con una probabilidad. Los modelos tipo BERT admiten un máximo de 512 tokens, por lo que los textos largos se truncan (`truncation=True`). Al estar entrenado en críticas de cine, el modelo puede fallar en textos de empleo sin contexto ("flexible deadlines", "nothing"), motivo por el que en este trabajo se usa como control de calidad y no como medida de satisfacción.

### 2.3 Sentence embeddings
Sentence-BERT (Reimers y Gurevych, 2019) entrena redes siamesas para que la similitud coseno entre vectores de frases refleje similitud semántica. `all-MiniLM-L6-v2` es un modelo de 6 capas y 384 dimensiones, rápido y adecuado para frases cortas en inglés, que se usa como primer paso de BERTopic.

### 2.4 BERTopic: UMAP, HDBSCAN y c-TF-IDF
BERTopic (Grootendorst, 2022) encadena cuatro pasos:

1. **Embeddings** de cada documento con un modelo de frases.
2. **UMAP** (McInnes et al., 2018) reduce la dimensión (de 384 a 5) conservando la estructura local; la semilla fija el resultado.
3. **HDBSCAN** (Campello et al., 2013) agrupa por densidad sin fijar el número de clústeres y deja como *outliers* (etiqueta −1) los documentos en zonas poco densas. `min_cluster_size` fija el tamaño mínimo de un topic.
4. **c-TF-IDF**: se concatenan los documentos de cada clúster en un "documento de clase" y se calcula un TF-IDF por clase, de modo que las palabras con mayor peso son las que distinguen a ese topic de los demás. La representación puede refinarse con *Maximal Marginal Relevance* para reducir palabras redundantes.

Frente a LDA (bolsa de palabras, número de topics fijado a priori, mal comportamiento con textos cortos), BERTopic agrupa por significado y ofrece mecanismos explícitos para los outliers, lo que lo hace adecuado para reseñas de una o dos frases.

### 2.5 Reproducibilidad
Se fija `SEED = 42` en el muestreo, en UMAP y en la selección de ejemplos; HDBSCAN y c-TF-IDF son deterministas. Los *embeddings* pueden diferir en los últimos decimales entre CPU y GPU, lo que puede cambiar la numeración de los topics: el notebook guarda una "firma" (primera palabra clave) de cada topic revisado a mano y, si detecta cambios, recurre a una asignación automática por léxico.

## 3. Datos y preparación

- **Fuente.** Unión de los datasets *Glassdoor Job Reviews* 1 y 2 (David Gauthier, Kaggle), preparada por el profesor: 5.004.045 reseñas de 2.796 empresas con al menos 300 reseñas, julio 2018 – julio 2023, sin duplicados ni nulos. Variables: `firm`, `date_review`, `current`, `overall_rating` (1-5), `recommend` (v/o/x), `pros`, `cons`.
- **Empresa y peers.** Deloitte (40.199 reseñas) y las otras tres *Big Four*: PwC (33.558), EY (29.601) y KPMG (25.757). Mismo sector en la lista del profesor ("Consultoría y servicios profesionales"), mismos servicios (auditoría, fiscal, consultoría), mismos programas de graduados y, por tanto, el mismo talento en disputa.
- **Muestra.** Para los modelos se toman 2.000 reseñas por empresa con semilla 42 (8.000 en total); el EDA usa las 129.115 reseñas de las cuatro firmas. La muestra conserva el rating medio y el porcentaje de empleados actuales de cada firma (diferencias < 0,05 puntos y < 1,5 pp).
- **Preparación.** `current` se separa en situación (Actual/Antiguo) y antigüedad (tramos de Glassdoor); `recommend` se traduce a Sí / Sin opinión / No; los textos solo se limpian de espacios y saltos de línea, porque los transformers aprovechan mayúsculas y puntuación. Antes del topic modeling se apartan los textos sin contenido ("none", "nothing", "can't think of any": ≈1 % de los cons) en una categoría propia.

## 4. Metodología

| Paso | Herramienta | Decisión clave y justificación de negocio |
|---|---|---|
| EDA | pandas, matplotlib | Sobre todas las reseñas; conclusiones accionables por bloque; se separa el % que recomienda sobre el total y entre quienes opinan, porque el porcentaje de "sin opinión" crece del 35 % al 54 % entre 2018 y 2023. |
| Control de calidad del sentimiento | `transformers.pipeline` con DistilBERT SST-2, `truncation=True` | Modelo binario (la pregunta es binaria), inglés, rápido; se ordenan los textos por longitud para acelerar la inferencia en CPU. |
| Topic modeling | BERTopic + `all-MiniLM-L6-v2` + UMAP(5 dim, semilla 42) + HDBSCAN(`min_cluster_size=60`, `min_samples=10`) + c-TF-IDF (uni/bigramas, *stopwords* ampliadas) + MMR | Un modelo para pros y otro para cons con las cuatro firmas juntas (comparabilidad). Un topic debe reunir al menos 60 reseñas (0,75 %) para ser un patrón y no una anécdota. |
| Outliers | `reduce_outliers(strategy='embeddings')` | Cada reseña con contenido debe contar en los porcentajes; se comprueba la robustez de las conclusiones sin la reasignación. |
| Categorías | Revisión manual de 33 topics de pros y 30 de cons → 8 categorías de RRHH + Otros + Sin contenido | Las mismas categorías en pros y cons para poder calcular el balance neto. Una regla automática por léxico valida la revisión manual y sirve de respaldo. |
| Comparación | % por categoría y empresa; media simple de peers; balance neto = % pros − % cons; gap = balance Deloitte − balance peers | Evita el sesgo de los porcentajes relativos. Se contrasta con `overall_rating` y `recommend`. |
| Actuales vs. antiguos | Distribución de cons por situación; cons de antiguos vs. peers | Los cons de antiguos empleados son motivos de salida. |
| Priorización | Matriz peso en cons × desventaja frente a peers, tamaño = castigo en rating | Prioridad alta = mucho peso y peor que el mercado. |

## 5. Resultados

### 5.1 EDA

| Empresa | Reseñas | Rating medio | % rating 1-2 | % recomienda (total) | % recomienda (entre quienes opinan) | % empleados actuales |
|---|---|---|---|---|---|---|
| **Deloitte** | 40.199 | **3,90** | **8,9** | 46,2 | **≈79** | 70,0 |
| PwC | 33.558 | 3,87 | 9,2 | 39,1 | ≈78 | 64,7 |
| EY | 29.601 | 3,78 | 10,6 | 47,0 | ≈76 | 68,8 |
| KPMG | 25.757 | 3,75 | 11,1 | 38,9 | ≈74 | 64,4 |

- Deloitte es la firma mejor valorada del grupo y la que más empleados actuales tiene opinando. Los antiguos empleados puntúan ≈0,3 puntos menos en las cuatro firmas (sesgo de selección); los antiguos de Deloitte (3,69) puntúan igual o mejor que los de los peers (3,57-3,70).
- El rating de Deloitte sube de 3,85 (2018-2019) a 4,00 (2022) y lidera el grupo desde 2022. El "% que recomienda" bruto cae en todas las firmas porque la proporción de "sin opinión" pasa del 35 % al 54 %; entre quienes opinan, Deloitte se mantiene en 78-82 %.
- El volumen de reseñas se triplica en 2020-2021 (hasta 15.000 reseñas de Deloitte en 2021).

### 5.2 Control de calidad del sentimiento

DistilBERT clasifica como positivos el **94,7 %** de los pros y como negativos el **82,4 %** de los cons, con cifras casi idénticas en las cuatro firmas (pros 94-95 %, cons 82-84 %). El modelo discrimina: las reseñas con pros y cons negativos puntúan 2,75 de media; las que tienen ambos positivos, 4,21. Los cons positivos (17,6 %) son reseñas sin queja ("No cons, so far I love it!"), listas de temas sin polaridad ("Work-life balance, Bonuses, Hike and Training") y quejas aceptadas con deportividad ("Long hours and challenging work", rating 5). Los pros negativos (5,3 %) son pros que niegan que haya algo bueno ("Nothing good to speak of"), elogios con jerga o negaciones ("steep learning curve", "flat organisation, not much hierarchy") y pros largos con matices. Ninguno de los dos grupos revela un fallo del pipeline.

### 5.3 Topics y categorías

BERTopic produce 33 topics en los pros y 30 en los cons (24,5 % y 24,6 % de outliers antes de la reasignación). Tras la revisión manual, los topics se agrupan en ocho categorías de negocio; una regla automática por léxico coincide con la revisión manual en el 82-83 % de los topics y las discrepancias son topics mixtos resueltos leyendo las reseñas.

| Categoría | % de reseñas en pros (4 firmas) | % de reseñas en cons (4 firmas) |
|---|---|---|
| Carrera, formación y desarrollo | 32,0 | 5,6 |
| Cultura y compañeros | 29,2 | 3,7 |
| Marca, clientes y proyectos | 12,5 | 8,2 |
| Compensación y beneficios | 10,1 | 18,7 |
| Flexibilidad y teletrabajo | 5,3 | 2,1 |
| Conciliación y carga de trabajo | 4,3 | 33,0 |
| Dirección y management | 3,4 | 9,7 |
| Burocracia, procesos y política interna | 0,0 | 7,1 |
| Otros | 1,6 | 3,9 |
| Sin contenido | 1,6 | 8,1 |

### 5.4 Comparación con peers

| Categoría | % cons Deloitte | % cons media peers | Balance neto Deloitte | Balance neto peers | **Gap** |
|---|---|---|---|---|---|
| Conciliación y carga de trabajo | 29,1 | 34,3 | −24,0 | −30,3 | **+6,3** |
| Compensación y beneficios | 17,0 | 19,3 | −5,7 | −9,5 | **+3,8** |
| Burocracia, procesos y política interna | 7,2 | 7,1 | −7,2 | −7,1 | −0,1 |
| Flexibilidad y teletrabajo | 2,4 | 2,0 | 3,1 | 3,3 | −0,2 |
| Carrera, formación y desarrollo | 6,6 | 5,3 | 26,2 | 26,4 | −0,3 |
| Dirección y management | 9,9 | 9,6 | −6,6 | −6,2 | −0,5 |
| Cultura y compañeros | 3,4 | 3,8 | 25,0 | 25,7 | −0,6 |
| Marca, clientes y proyectos | 11,2 | 7,2 | 0,9 | 5,5 | **−4,6** |

- Fortalezas relativas: conciliación (+6,3 pp) y compensación (+3,8 pp). Deloitte recibe menos quejas y más elogios que los peers en los dos temas que dominan los cons del sector.
- Debilidad relativa: marca, clientes y proyectos (−4,6 pp), por el exceso de quejas sobre la asignación y calidad de los proyectos (11,2 % vs. 7,2 %), que además crece con el tiempo (9,6 % → 14,1 %).
- Robustez: con solo las reseñas asignadas por HDBSCAN (6.050), los gaps de al menos 1 pp conservan signo y magnitud.
- Contraste con el rating en Deloitte: quien se queja de dirección puntúa 3,46 (24 % no recomienda) y de cultura 3,40; quien se queja de conciliación puntúa 4,02 (por encima de la media de 3,90) y recomienda igual que el resto. La conciliación es la queja más frecuente; la dirección, la más dañina.

### 5.5 Empleados actuales vs. antiguos

En Deloitte, *dirección y management* pasa del 8,2 % de los cons de los actuales al 14,0 % de los antiguos (+5,8 pp); cultura, del 2,8 % al 4,6 %. Compensación (17,7 → 15,2 %) y conciliación (29,6 → 28,0 %) pesan menos entre los que se fueron. Frente a los antiguos empleados de los peers, los de Deloitte citan menos la conciliación (28,0 % vs. 34,2 %), más los proyectos (10,8 % vs. 6,4 %) y más la dirección (14,0 % vs. 11,5 %). Entre los pros, los que se fueron valoran la marca ("bueno para el CV") y mucho menos la flexibilidad (2,5 % vs. 6,8 % de los actuales).

### 5.6 Priorización

| Categoría | Peso en cons Deloitte (%) | Desventaja vs. peers (pp) | Castigo en rating | Prioridad |
|---|---|---|---|---|
| Marca, clientes y proyectos | 11,3 | +4,1 | 0,04 | **Alta** (peor que el mercado, en aumento) |
| Dirección y management | 9,9 | +0,3 | 0,44 | **Alta** (mayor castigo, motivo de salida) |
| Carrera, formación y desarrollo | 6,7 | +1,4 | 0,15 | Media (específica) |
| Burocracia, procesos y política interna | 7,2 | +0,1 | 0,22 | Media (específica) |
| Flexibilidad y teletrabajo | 2,4 | +0,5 | −0,28 | Media (específica, poco peso) |
| Compensación y beneficios | 17,0 | −2,3 | 0,17 | Estructural del sector (vigilar) |
| Conciliación y carga de trabajo | 29,1 | −5,2 | −0,12 | Estructural del sector (vigilar: sube del 26 % al 32 %) |
| Cultura y compañeros | 3,4 | −0,4 | 0,50 | Baja (poco peso, aunque castiga) |

### 5.7 Extras

- Evolución: en Deloitte suben las quejas por conciliación (25,8 % → 32,4 %) y por proyectos (9,6 % → 14,1 %) y bajan las de dirección (11,0 % → 7,6 %) y carrera (8,8 % → 5,3 %); los elogios a la compensación suben del 9,9 % al 13,4 %.
- Por recomendación: quienes no recomiendan Deloitte se quejan de dirección (19,7 % vs. 8,4 %), cultura (8,4 % vs. 2,6 %) y compensación (19,7 % vs. 16,4 %), y menos de conciliación (18,9 % vs. 29,1 %).
- Por antigüedad: la conciliación crece con la antigüedad (25 % → 35 %); la compensación alcanza su máximo entre 1 y 5 años; dirección y proyectos son estables en todos los tramos.

## 6. Discusión y limitaciones

Los resultados son coherentes entre sí: las fortalezas detectadas en el texto (menos quejas por horas y salario) coinciden con el mejor rating y la mayor recomendación de Deloitte; las quejas que más castigan el rating (dirección, cultura) son las que más crecen entre quienes se van y entre quienes no recomiendan la firma; y la decisión de reasignar outliers no altera ninguna conclusión relevante. La principal sorpresa, que las quejas por conciliación no reduzcan el rating, se explica por el contrato implícito de las Big Four: las horas se aceptan a cambio de aprendizaje, marca y salario, y solo se convierten en motivo de salida cuando se combinan con una mala gestión del equipo.

- **Representatividad de Glassdoor.** Reseñas voluntarias y autoseleccionadas; los antiguos empleados puntúan sistemáticamente más bajo (0,25-0,31 puntos menos en las cuatro firmas). Los niveles absolutos deben leerse con cautela; la comparación relativa entre firmas que comparten el mismo sesgo es más fiable.
- **Mezcla de países y áreas.** El dataset combina Reino Unido, Estados Unidos, India y otros países, y todas las líneas de servicio; un problema puede concentrarse en una geografía o línea y diluirse en el agregado.
- **Precisión de los topics.** Cada reseña se asigna a un solo topic aunque mencione varios temas; algunos topics son mixtos y la agrupación en categorías es un juicio manual. Diferencias de 1-2 puntos porcentuales no son significativas.
- **Modelo de sentimiento.** Entrenado en críticas de cine; comete errores con textos sin contexto. Se usa solo como control de calidad.
- **Muestra y ventana.** 2.000 reseñas por firma; la ventana mezcla pre-COVID, pandemia y vuelta a la oficina. La "sin opinión" creciente en `recommend` obliga a interpretar la recomendación entre quienes opinan.

## 7. Conclusiones

Deloitte es la Big Four mejor valorada por sus empleados (3,90, en ascenso hasta 4,00 en 2022) y ofrece la mejor relación esfuerzo-recompensa percibida del grupo: menos quejas por horas y salario y más elogios en ambos temas. Comparte con sus peers el contrato del sector (se elogia la carrera y la cultura, se critica la carga de trabajo). Sus debilidades específicas son dos: la asignación y calidad de los proyectos, única categoría donde está claramente peor que el mercado y en aumento, y la calidad directiva, que no destaca en volumen pero es la queja que más daña el rating, la que más distingue a quien se va y a quien no recomienda la firma. Las tres recomendaciones (programa de calidad directiva con evaluación ascendente ligada a la promoción, transparencia y rotación en el staffing, y uso y protección de la ventaja en esfuerzo-recompensa en la marca empleadora) atacan exactamente esas palancas y son medibles con el mismo pipeline en los próximos trimestres.

## 8. Referencias

- Campello, R. J. G. B., Moulavi, D. y Sander, J. (2013). Density-based clustering based on hierarchical density estimates. *PAKDD 2013*, LNCS 7819, 160-172.
- Devlin, J., Chang, M.-W., Lee, K. y Toutanova, K. (2019). BERT: Pre-training of deep bidirectional transformers for language understanding. *NAACL-HLT 2019*.
- Evolve Academy (2026). *Caso Práctico de NLP: Diagnóstico de Marca Empleadora con Reseñas de Glassdoor* (enunciado), *Glassdoor_ejemplo.ipynb* (notebook guía) y *Análisis de reseñas Glassdoor (plantilla)* (plantilla de presentación). Máster en Data Science & IA.
- Gauthier, D. *Glassdoor Job Reviews* [dataset]. Kaggle. https://www.kaggle.com/datasets/davidgauthier/glassdoor-job-reviews
- Gauthier, D. *Glassdoor Job Reviews 2* [dataset]. Kaggle. https://www.kaggle.com/datasets/davidgauthier/glassdoor-job-reviews-2
- Grootendorst, M. (2022). BERTopic: Neural topic modeling with a class-based TF-IDF procedure. *arXiv:2203.05794*.
- McInnes, L., Healy, J. y Melville, J. (2018). UMAP: Uniform Manifold Approximation and Projection for dimension reduction. *arXiv:1802.03426*.
- Reimers, N. y Gurevych, I. (2019). Sentence-BERT: Sentence embeddings using Siamese BERT-networks. *EMNLP-IJCNLP 2019*.
- Sanh, V., Debut, L., Chaumond, J. y Wolf, T. (2019). DistilBERT, a distilled version of BERT: smaller, faster, cheaper and lighter. *arXiv:1910.01108*.
- Socher, R. et al. (2013). Recursive deep models for semantic compositionality over a sentiment treebank. *EMNLP 2013*.
- Vaswani, A. et al. (2017). Attention is all you need. *NeurIPS 2017*.
