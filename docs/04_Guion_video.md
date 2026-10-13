# Guion para el vídeo de presentación

El enunciado (sección 4, Entregables) dice que quien no pueda presentar en clase entrega **un vídeo con la explicación**. Este guion acompaña a `Presentacion_Diagnostico_Deloitte.pptx`; el mismo texto está dentro del PowerPoint como **notas del orador** (Vista → Página de notas, o la vista Moderador al grabar).

- **Duración completa:** ≈ 16 min 20 s (17 diapositivas).
- **Versión corta (si el profesor limita el tiempo):** ≈ 14 min 50 s, saltando las diapositivas marcadas como *opcional* (3, 13 y 17).
- Habla a ritmo normal (unas 140 palabras por minuto); los tiempos son orientativos.
- **Versión de 10-12 minutos** (lo habitual para un vídeo de clase): salta las diapositivas 3, 13 y 17 y, en las diapositivas 2, 6, 7, 11 y 16, lee solo el **primer párrafo** del guion (el resto son detalles que ya están en el notebook). Queda en ≈ 11-12 min. Si el profesor fija un límite menor, recorta también la diapositiva 8 a una frase ("salieron 63 topics que agrupé a mano en ocho categorías de RRHH") y la 15 a los titulares de las tres acciones.

## Cómo grabarlo en tu Mac

**Opción A (recomendada): PowerPoint graba la narración y exporta a MP4.**

1. Abre `Presentacion_Diagnostico_Deloitte.pptx`, pon tu nombre en la portada y guarda.
2. Menú *Presentación con diapositivas* → *Grabar presentación con diapositivas* (en versiones recientes, botón *Grabar* arriba a la derecha). Activa el micrófono; la cámara es opcional (si la activas, aparecerás en una esquina).
3. Graba diapositiva a diapositiva leyendo las notas del orador; si te equivocas en una, para, vuelve a esa diapositiva y regrábala (PowerPoint guarda la narración por diapositiva).
4. *Archivo* → *Exportar* → formato **MP4**, calidad *Internet* o *HD (1080p)*, con la opción *Usar narraciones e intervalos grabados*. Un vídeo de 12 minutos ocupa unos 100-200 MB.
5. Reproduce el MP4 entero antes de entregarlo: comprueba que se oye bien y que los cambios de diapositiva coinciden con el audio.

**Opción B: grabación de pantalla con QuickTime (si PowerPoint da problemas).**

1. Abre *QuickTime Player* → *Archivo* → *Nueva grabación de pantalla*; en *Opciones* elige el micrófono.
2. Pon el PowerPoint en modo presentación (⌘⇧⏎), empieza a grabar y narra; al acabar, detén la grabación (⌘⌃Esc) y guarda el .mov.
3. Si necesitas MP4, *Archivo* → *Exportar como* → *1080p*.

**Opción C: Zoom o Google Meet**, compartiendo pantalla y grabando la reunión contigo solo; produce directamente un MP4 con tu cámara.

## Consejos

- Ensaya una vez con cronómetro antes de grabar; lee las notas, pero no las recites palabra por palabra.
- Di siempre **por qué** tomaste cada decisión técnica (modelo binario, min_cluster_size 60, reasignar outliers, balance neto): es lo que más puntúa según los criterios de evaluación.
- Cuando aparezca una gráfica, señala con el ratón la barra o el punto del que hablas.
- Micrófono cerca, habitación silenciosa, sin música de fondo; mira a la cámara en la portada y en las conclusiones si la activas.
- Nombre del fichero: `Diagnostico_Marca_Empleadora_Deloitte_video.mp4`.

## Guion diapositiva a diapositiva

### Diapositiva 1 · Portada · ~40 s

Hola, soy [tu nombre]. En este vídeo presento mi caso práctico de NLP: un diagnóstico de marca empleadora de Deloitte a partir de las reseñas públicas de Glassdoor, comparado con sus competidores directos, PwC, EY y KPMG. He combinado dos técnicas: un transformer preentrenado para validar el sentimiento de las reseñas y topic modeling con BERTopic para descubrir de qué hablan los empleados. El objetivo es decirle a Recursos Humanos qué puntos fuertes puede explotar y qué áreas debe mejorar, y solo las que de verdad están peor que el mercado.

### Diapositiva 2 · Objetivos y asunciones · ~65 s

El encargo de RRHH tiene cuatro preguntas: cuáles son las fortalezas de Deloitte como empleador, cuáles son sus áreas de mejora, por qué se va la gente, y qué tres acciones concretas propondría. Y siempre comparando con los peers, porque un problema solo es prioritario si estamos peor que la competencia.

Las decisiones de partida son estas. He elegido las otras tres Big Four como peers, porque reclutan en los mismos campus y compiten por el mismo talento. Para los modelos uso una muestra de dos mil reseñas por empresa con semilla fija, ocho mil en total, y para el análisis descriptivo uso las ciento veintinueve mil reseñas disponibles. Las reseñas vacías, del tipo "none" o "nothing", las aparto en una categoría propia. Y entreno un único modelo de topics para las cuatro firmas, uno para pros y otro para cons, porque si entrenara un modelo por empresa los topics no serían comparables.

### Diapositiva 3 · Datos: la empresa y sus peers · ~40 s *(opcional en la versión corta)*

El dataset lo preparó el profesor a partir de los dos datasets de Glassdoor de Kaggle: cinco millones de reseñas entre julio de 2018 y julio de 2023. De ahí cargo solo las cuatro firmas con el filtro de Parquet. Deloitte tiene cuarenta mil reseñas, PwC treinta y tres mil, EY casi treinta mil y KPMG veintiséis mil, así que la comparación es estadísticamente sólida. Cada reseña trae la empresa, la fecha, la situación y antigüedad del empleado, el rating de uno a cinco, si recomienda la empresa, y dos textos libres en inglés: los pros y los cons.

### Diapositiva 4 · Insights del EDA: la Big Four mejor valorada · ~50 s

Primer resultado: Deloitte es la Big Four mejor valorada. Rating medio de tres con noventa frente a tres con ochenta de media de los peers, y el menor porcentaje de reseñas de una o dos estrellas. En recomendación es segunda sobre el total, con un cuarenta y seis por ciento, pero primera si miramos solo a quienes contestan, con cerca del setenta y nueve por ciento. Además, el setenta por ciento de sus reseñas son de empleados actuales. La conclusión accionable es que el mensaje "la Big Four mejor valorada por sus empleados" está respaldado por los datos, aunque como las diferencias de rating son pequeñas conviene apoyarlo en hechos concretos, que veremos después.

### Diapositiva 5 · Insights del EDA: tendencia y un artefacto · ~60 s

Segundo resultado: la tendencia es favorable. El rating de Deloitte sube de tres con ochenta y cinco a cuatro en 2022 y lidera el grupo desde entonces. Aquí hay una trampa importante: el porcentaje bruto que recomienda la empresa cae en las cuatro firmas, en Deloitte del cincuenta y cinco al treinta y ocho por ciento. Parece un deterioro, pero es un artefacto del dato: la proporción de reseñas que no contestan a esa pregunta pasa del treinta y cinco al cincuenta y cuatro por ciento. Si miramos solo a quienes opinan, Deloitte se mantiene estable entre el setenta y ocho y el ochenta y dos por ciento. Por eso recomiendo reportar siempre la recomendación entre quienes opinan. También se ve que los antiguos empleados puntúan unas tres décimas menos en todas las firmas: es el sesgo de selección habitual, no algo de Deloitte.

### Diapositiva 6 · Validación del sentimiento · ~100 s

Antes de analizar temas hago un control de calidad del texto. Aplico el modelo DistilBERT ajustado en SST-2 a los pros y a los cons por separado. Lo elegí porque es binario, positivo o negativo, que es exactamente la pregunta de control; es rápido, clasifica dieciséis mil textos en menos de un minuto; y está entrenado en inglés. Uso truncation igual a True porque los modelos tipo BERT admiten un máximo de quinientos doce tokens.

El resultado valida el dato: el noventa y cinco por ciento de los pros salen positivos y el ochenta y dos por ciento de los cons salen negativos, con cifras casi idénticas en las cuatro firmas. Y el modelo discrimina bien: quien tiene pros y cons negativos puntúa dos con setenta y cinco de media, frente a cuatro con veintiuno cuando ambos son positivos.

He leído los casos discordantes. Los cons positivos son sobre todo reseñas sin queja, como "No cons, so far I love it", listas de temas sin palabras negativas, y quejas aceptadas con deportividad, como "long hours and challenging work" con cinco estrellas. Los pros negativos son reseñas de una estrella que niegan que haya algo bueno, o elogios con jerga que confunden al modelo, como "steep learning curve". Es decir, no hay un fallo del pipeline, sino los límites de un clasificador sin contexto. Por eso las empresas se comparan por tema y no por sentimiento.

### Diapositiva 7 · Topic modeling con BERTopic · ~70 s

Para descubrir los temas uso BERTopic, y no LDA, porque las reseñas son muy cortas, ocho palabras de mediana, y LDA cuenta palabras sin saber que "work life balance" y "long hours" hablan de lo mismo. BERTopic agrupa por significado. El pipeline tiene cinco pasos: primero convierto cada reseña en un vector con el modelo de frases all-MiniLM; segundo, UMAP reduce a cinco dimensiones con semilla fija; tercero, HDBSCAN agrupa por densidad, con un tamaño mínimo de sesenta reseñas por topic, porque para RRHH un patrón debe tener cierto volumen y no ser una anécdota; cuarto, c-TF-IDF saca las palabras que distinguen cada topic; y quinto, reviso a mano los topics y los agrupo en categorías de negocio.

Sobre los outliers: HDBSCAN deja fuera a un veinticinco por ciento de reseñas. Decidí reasignarlas al topic más parecido por embedding, porque la comparación entre empresas necesita que cada reseña con contenido cuente. Y comprobé que, sin reasignar, las conclusiones se mantienen.

### Diapositiva 8 · Análisis de topics: de qué hablan las Big Four · ~60 s

Salieron treinta y tres topics en los pros y treinta en los cons. Los he agrupado a mano en ocho categorías de RRHH, las mismas para pros y cons: compensación, conciliación, carrera, dirección, cultura, burocracia, flexibilidad, y marca y proyectos. Para validar mi revisión, una regla automática por léxico coincide con mi asignación en el ochenta y dos por ciento de los topics; las discrepancias son topics mixtos que resolví leyendo las reseñas.

La imagen de conjunto del sector es clara: en los pros dominan la carrera y el aprendizaje, un treinta y dos por ciento, y la cultura y los compañeros, un veintinueve. En los cons dominan la carga de trabajo, un treinta y tres por ciento, y el salario, un diecinueve. Esto es el contrato implícito de las Big Four y es igual en las cuatro firmas.

### Diapositiva 9 · Comparación con peers · ~80 s

Aquí está el núcleo del diagnóstico. Como los porcentajes de cada empresa suman cien y son relativos, uso el balance neto: el porcentaje de reseñas que elogian una categoría menos el porcentaje que se queja de ella, y comparo el balance de Deloitte con la media de los peers.

Deloitte tiene dos fortalezas relativas. La conciliación, con más de seis puntos de ventaja: sigue siendo su mayor queja, el veintinueve por ciento de los cons, pero en los peers es el treinta y cuatro, y PwC llega al treinta y ocho. Y la compensación, con casi cuatro puntos de ventaja: menos quejas y más elogios que cualquier peer. En resumen, la mejor relación esfuerzo-recompensa del grupo.

Y tiene una debilidad clara: marca, clientes y proyectos, con cuatro puntos y medio de desventaja. Los elogios son iguales, pero las quejas casi doblan a las de los peers, once por ciento frente a siete. Leyendo esos topics, las quejas hablan de la asignación y calidad de los proyectos: proyectos largos con tecnología obsoleta, falta de rotación, poco contacto con el cliente. El resto de categorías está en la media del sector.

### Diapositiva 10 · Qué quejas pesan de verdad · ~65 s

El enunciado pide contrastar las conclusiones del texto con el rating y la recomendación, y aquí aparece el hallazgo más interesante. No todas las quejas pesan igual. Quien se queja de la dirección y los managers puntúa tres con cuarenta y seis, casi medio punto por debajo de la media, y el veinticuatro por ciento no recomienda Deloitte, frente al trece por ciento general. En cambio, quien se queja de las horas puntúa cuatro con cero dos, por encima de la media, y recomienda la empresa igual que el resto. Las horas se asumen como parte del trato Big Four. Lo confirma el análisis por recomendación: entre quienes no recomiendan Deloitte, la dirección es el veinte por ciento de las quejas, frente al ocho por ciento entre quienes sí recomiendan. Conclusión: la conciliación es la queja más frecuente, pero la calidad directiva es la más dañina.

### Diapositiva 11 · Empleados actuales vs. antiguos · ~70 s

Los cons de quienes ya se han ido son, en la práctica, motivos de salida. Comparo temas y no sentimiento, porque los antiguos empleados puntúan más bajo en todas las firmas. En Deloitte, las quejas de los antiguos se parecen a las de los actuales en casi todo, con una excepción muy clara: la dirección pasa del ocho por ciento de los cons de los actuales al catorce por ciento de los antiguos. Quien se va se queja de sus jefes mucho más que quien se queda. El salario y las horas, en cambio, pesan menos entre los que se fueron.

Frente a los antiguos empleados de los peers, los de Deloitte citan menos la conciliación, veintiocho frente a treinta y cuatro por ciento, y más los proyectos y la dirección. Y en los pros, quienes se fueron casi no mencionan la flexibilidad, dos y medio por ciento frente a casi siete entre los actuales, lo que sugiere que las políticas actuales de flexibilidad están reteniendo.

### Diapositiva 12 · Matriz de priorización · ~55 s

Para priorizar cruzo el peso de cada categoría en los cons de Deloitte con su desventaja frente a los peers; el tamaño del punto es cuánto castiga esa queja al rating. Salen dos prioridades altas. La dirección y el management, por ser la queja que más daña el rating y el principal motivo de salida. Y la asignación y calidad de los proyectos, por ser la única categoría donde Deloitte está claramente peor que el mercado y además va a más. La carrera y la promoción quedan como prioridad media. Y la conciliación y la compensación, aunque son las mayores quejas en volumen, son estructurales del sector, están por debajo de los peers y no castigan el rating: no son prioridad de inversión sino de vigilancia.

### Diapositiva 13 · Extras: evolución, recomendación y antigüedad · ~30 s *(opcional en la versión corta)*

Como extra, he mirado la evolución temporal. En Deloitte suben las quejas por conciliación, del veintiséis al treinta y dos por ciento, y por proyectos, del diez al catorce, y bajan las de dirección y carrera. Los elogios al salario suben, coincidiendo con las subidas de 2022. Por antigüedad, la conciliación pesa más cuanto más tiempo lleva el empleado, y el salario pesa más entre uno y cinco años, el momento de la primera promoción.

### Diapositiva 14 · Conclusiones · ~45 s

Resumiendo. Deloitte es la Big Four mejor valorada y en ascenso, y ofrece la mejor relación esfuerzo-recompensa del grupo: menos quejas por horas y por salario que cualquier peer. Comparte con el sector los activos clásicos, aprendizaje y cultura. Sus áreas de mejora son dos: la calidad directiva, que es la queja que más daña el rating y la que más distingue a quien se va; y la asignación de proyectos, la única categoría claramente peor que el mercado y en aumento. Las horas y el salario hay que vigilarlos, porque las quejas por horas crecen, pero no son el problema diferencial de Deloitte.

### Diapositiva 15 · Propuesta de acciones para RRHH · ~70 s

Propongo tres acciones. Primera, un programa de calidad directiva para managers y senior managers: evaluación ascendente, del equipo al jefe, con peso real en la promoción; formación obligatoria en gestión de personas; y seguimiento trimestral de los equipos con peor feedback. El objetivo es bajar las quejas sobre dirección entre quienes se van del catorce por ciento al nivel de los peers en dieciocho meses.

Segunda, un staffing transparente y con rotación: un mercado interno de proyectos visible para todos, revisión obligatoria tras doce meses en el mismo proyecto, modernización de los proyectos antiguos y exposición al cliente para los centros de entrega. Objetivo: bajar las quejas sobre proyectos del once al siete por ciento.

Tercera, vender y proteger la ventaja en esfuerzo-recompensa: usar en campus hechos verificables, como el rating de cuatro en 2022 y las menores quejas por horas y salario del sector, y protegerla con reglas de temporada alta y seguimiento trimestral de las quejas por horas, que han subido seis puntos desde 2018.

### Diapositiva 16 · Limitaciones y próximos pasos · ~60 s

Las limitaciones. Glassdoor es voluntario y autoseleccionado, así que los niveles absolutos se leen con cautela y lo fiable es la comparación relativa. El dataset mezcla países y líneas de servicio, y un problema local se diluye. Cada reseña va a un solo topic aunque hable de varios temas, y la agrupación en categorías es un juicio manual, así que diferencias de uno o dos puntos no son significativas. Y el modelo de sentimiento está entrenado en críticas de cine, por eso solo lo uso como control de calidad.

Como próximos pasos propongo segmentar por país y puesto, que están en el dataset original de Kaggle; entrenar un clasificador multietiqueta con las ocho mil reseñas etiquetadas para cubrir las ciento veintinueve mil; automatizar un seguimiento trimestral; y cruzar con encuestas internas y entrevistas de salida.

### Diapositiva 17 · Anexo técnico · ~20 s *(opcional en la versión corta)*

Dejo como anexo los parámetros técnicos y el entorno. Todo es reproducible: semilla cuarenta y dos en el muestreo y en UMAP, y el notebook se ejecuta de arriba abajo sin errores en unos cinco minutos con GPU. Gracias por vuestra atención.

## Checklist antes de entregar

- [ ] Nombre en la portada.
- [ ] Vídeo completo reproducido de principio a fin, audio audible.
- [ ] Duración dentro del límite que haya dado el profesor.
- [ ] Se entrega junto con el notebook `.ipynb` (y la presentación, si la piden).
- [ ] Formato MP4 (o el que indique el profesor) y nombre de fichero claro.