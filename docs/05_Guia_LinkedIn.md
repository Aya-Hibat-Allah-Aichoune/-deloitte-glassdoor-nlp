# Cómo compartir el proyecto en LinkedIn

## 1. Qué puedes publicar y qué no

| Puedes publicar | No publiques |
|---|---|
| Tu notebook, tus figuras, tu presentación, tu README y tus documentos | El fichero `glassdoor_reviews_hr.parquet` (lo preparó el profesor; 293 MB; las condiciones de uso de Kaggle no permiten redistribuirlo tal cual) |
| Porcentajes, ratings agregados y conclusiones | El enunciado, la plantilla y el notebook de ejemplo del profesor (son material del curso; pide permiso si quieres incluirlos) |
| Frases cortas de reseñas como ejemplo ("No cons, so far I love it!") | Reseñas completas o cualquier dato que permita identificar a quien la escribió |
| El nombre de Deloitte y de los peers, con el aviso de proyecto académico independiente | Afirmaciones sobre las empresas que no salgan de los datos ("Deloitte maltrata a sus empleados") |

El `.gitignore` que hay en la carpeta ya excluye el Parquet y los materiales del profesor, y el README lleva el aviso de independencia.

## 2. Publicar el código en GitHub (15 minutos)

1. Entra en https://github.com/new, nombre `deloitte-glassdoor-nlp` (o similar), visibilidad **Public**, sin añadir README ni .gitignore (ya los tienes). Crea el repositorio y copia la URL que te da (`https://github.com/TU_USUARIO/deloitte-glassdoor-nlp.git`).
2. En el Terminal, dentro de la carpeta del proyecto:

```bash
cd ~/Downloads/NLP
git init -b main
git add .
git status
```

Comprueba en la salida de `git status` que **no** aparecen el `.parquet`, los PDF del profesor, `Glassdoor_ejemplo.ipynb` ni `lista_con_sector.xlsx`. Si todo está bien:

```bash
git commit -m "Diagnóstico de marca empleadora Deloitte vs Big Four con NLP (sentimiento + BERTopic)"
git remote add origin https://github.com/TU_USUARIO/deloitte-glassdoor-nlp.git
git push -u origin main
```

GitHub te pedirá usuario y contraseña: la "contraseña" es un *personal access token* (Settings → Developer settings → Personal access tokens → Generate new token, con permiso `repo`). También puedes usar la app GitHub Desktop si prefieres no usar el terminal.

3. Entra en el repositorio en el navegador y abre `Diagnostico_Marca_Empleadora_Deloitte.ipynb`: GitHub lo renderiza con gráficas y resultados. Esa es la página que enlazarás desde LinkedIn.
4. Opcional: en *Settings → General → Social preview* sube `figuras/10_balance_neto_y_gap.png` para que el enlace tenga imagen.

## 3. Artículo o post

- **Post** (hasta 3.000 caracteres): rápido, aparece en el feed, con 1-4 imágenes. Es lo que más alcance tiene.
- **Artículo** (LinkedIn Pulse): largo, con secciones e imágenes, queda indexado en tu perfil y en Google. Es lo que hizo tu compañero.

Recomendación: publica el **artículo** y, el mismo día, un **post** corto que lo enlace. Abajo tienes los dos borradores. Cambia lo que quieras: debe sonar a ti.

Para el artículo: en LinkedIn, botón *Escribir artículo* (en la caja "Crear publicación"); pega el texto, pon las imágenes donde se indica (arrastra los PNG de `figuras/`), añade el enlace al repositorio y publica. Para el post: *Crear publicación*, pega el texto, añade 2-3 imágenes y el enlace al artículo o al repositorio.

Mejor hora: martes a jueves, entre 8:00 y 10:00. Etiqueta a Evolve Academy y, si te parece, al profesor. Responde a los comentarios el primer día: LinkedIn premia la conversación temprana.

---

## 4. Borrador de artículo

**Título:** Qué dicen 129.000 reseñas de Glassdoor sobre Deloitte y las Big Four: un diagnóstico de marca empleadora con NLP

**Subtítulo:** Sentimiento con transformers, topic modeling con BERTopic y una pregunta de negocio: ¿en qué estamos peor que la competencia?

*Proyecto académico independiente para el Máster en Data Science & IA de Evolve Academy. No ha sido encargado ni avalado por Deloitte ni por ninguna de las firmas mencionadas; se basa exclusivamente en reseñas públicas de Glassdoor.*

### El problema

Imaginad que sois el equipo de Data Science de Deloitte y Recursos Humanos os hace una pregunta sencilla: ¿qué puntos fuertes tenemos como empleador, qué áreas debemos mejorar y por qué se va la gente? La trampa está en la palabra "mejorar". Todas las empresas tienen quejas; un problema solo es prioritario si estamos **peor que el mercado**. Así que el análisis no era "qué dicen de Deloitte", sino "qué dicen de Deloitte **comparado con PwC, EY y KPMG**", las firmas con las que compite por el mismo talento.

### Los datos y el método

He trabajado con las reseñas de Glassdoor de las cuatro firmas entre 2018 y 2023 (129.115 reseñas para el análisis descriptivo y una muestra de 2.000 por empresa para los modelos). Cada reseña trae una nota de 1 a 5, si el empleado recomienda la empresa, su situación (actual o antiguo) y dos textos libres: los pros y los contras.

El pipeline tiene tres piezas:

1. **Control de calidad con un transformer.** Antes de interpretar nada, un modelo de sentimiento preentrenado (DistilBERT ajustado en SST-2) clasifica los pros y los contras. Si los pros no salieran positivos, algo fallaría. Salió bien: 95 % de pros positivos y 82 % de contras negativos. Lo interesante fue leer los casos "raros": contras positivos que en realidad dicen "no tengo quejas", listas de temas sin ninguna palabra negativa, o un "long hours and challenging work" con cinco estrellas. El sentimiento no sirve para comparar empresas; el tema, sí.
2. **De qué hablan los empleados, con BERTopic.** Embeddings de frases, reducción con UMAP, agrupación con HDBSCAN y palabras clave con c-TF-IDF. Un único modelo para las cuatro firmas (si no, los temas no serían comparables): 33 temas en los pros y 30 en los contras, que revisé a mano y agrupé en ocho categorías que RRHH puede accionar: compensación, conciliación, carrera, dirección, cultura, burocracia, flexibilidad, y marca y proyectos.
3. **Comparar bien.** Los porcentajes de cada empresa suman 100 % y engañan: si una firma tiene pocas quejas de salario, sube el peso de las demás sin que nada haya empeorado. Por eso uso el **balance neto** (porcentaje de reseñas que elogian una categoría menos el que se queja de ella) y lo comparo con la media de los peers. Y contrasto cada conclusión con la nota y la recomendación: si una queja es un problema real, quien la escribe debería puntuar peor.

[Imagen: figuras/10_balance_neto_y_gap.png]

### Lo que encontré

- **Deloitte es la Big Four mejor valorada y mejora.** Nota media de 3,90 frente a 3,80 de los peers, 4,00 en 2022. Un detalle que casi me engaña: el porcentaje bruto de empleados que recomiendan la empresa cae en las cuatro firmas, pero es porque cada vez más reseñas no contestan a esa pregunta (del 35 % al 54 %). Entre quienes opinan, Deloitte se mantiene en torno al 79 %.
- **Las Big Four comparten el mismo contrato.** Se elogia la carrera y el aprendizaje (32 % de los pros) y la cultura (29 %); se critican las horas (33 % de los contras) y el salario (19 %). Es el sector, no una firma.
- **La fortaleza diferencial de Deloitte es la relación esfuerzo-recompensa.** Cinco puntos menos de quejas por horas que los peers (29 % frente a 34 %) y dos menos por salario, con más elogios en ambos temas.
- **La debilidad diferencial son los proyectos.** Las quejas sobre asignación y calidad de los proyectos (tecnología antigua, falta de rotación, poco cliente) suponen el 11 % de los contras frente al 7 % en los peers, y van a más.
- **Lo que de verdad duele no son las horas, sino los jefes.** Quien se queja de las horas puntúa 4,02, por encima de la media, y recomienda la empresa igual que el resto. Quien se queja de la dirección puntúa 3,46 y uno de cada cuatro no la recomienda. Y ese tema pasa del 8 % de las quejas entre los empleados actuales al 14 % entre los que se han ido.

[Imagen: figuras/12_contraste_rating_por_categoria_cons.png]

### Qué haría yo si fuera RRHH

1. **Un programa de calidad directiva** con evaluación ascendente que pese en la promoción a senior manager y partner. Es la palanca de retención.
2. **Transparencia y rotación en la asignación de proyectos**: un mercado interno visible y revisión obligatoria tras doce meses en el mismo proyecto. Es la única área donde Deloitte está peor que el mercado.
3. **Usar la ventaja en esfuerzo-recompensa como mensaje de marca empleadora** con hechos verificables, y protegerla: las quejas por horas han subido seis puntos desde 2018.

[Imagen: figuras/15_matriz_priorizacion.png]

### Lo que el análisis no puede decir

Glassdoor es voluntario y autoseleccionado; los antiguos empleados puntúan peor en todas las firmas; el dataset mezcla países y líneas de servicio; cada reseña va a un solo tema aunque hable de varios; y agrupar temas en categorías es un juicio humano. Diferencias de uno o dos puntos no significan nada. Lo fiable es la comparación relativa entre firmas que comparten los mismos sesgos.

### Lo que me llevo

Que el modelo es la parte fácil. Lo difícil es decidir qué hacer con el 25 % de reseñas que el clustering no asigna, comprobar que la decisión no cambia las conclusiones, y traducir 63 temas en ocho palancas que alguien de RRHH pueda entender sin saber qué es un embedding.

Código, notebook y presentación: [enlace al repositorio]. Los datos no se incluyen por tamaño y condiciones de uso (datasets *Glassdoor Job Reviews* de David Gauthier en Kaggle).

---

## 5. Borrador de post corto

¿Qué pesa más en la satisfacción de un consultor: las horas o los jefes?

He analizado 129.000 reseñas de Glassdoor de Deloitte, PwC, EY y KPMG con NLP (DistilBERT para validar el sentimiento, BERTopic para descubrir los temas) para mi máster en Evolve Academy. Tres cosas que no esperaba:

1. Las quejas por horas son las más frecuentes (33 % de los contras), pero quien las escribe puntúa POR ENCIMA de la media. Son "parte del trato".
2. Lo que sí hunde la nota y la recomendación es la dirección: 3,46 de media y uno de cada cuatro no recomienda. Y es el tema que más crece entre los que se van.
3. El "% que recomienda" cae en las cuatro firmas desde 2018. Parece un deterioro; es un artefacto: cada vez más reseñas no contestan a esa pregunta.

Deloitte sale como la Big Four mejor valorada, con menos quejas por horas y salario que sus competidores, y con una debilidad propia: la asignación de proyectos.

Método, código y limitaciones en el artículo: [enlace]

Proyecto académico independiente; no avalado por ninguna de las firmas.

#NLP #DataScience #PeopleAnalytics #EmployerBranding #BERTopic #Python #Glassdoor #RRHH

---

## 6. Checklist antes de publicar

- [ ] El repositorio no contiene el Parquet ni los ficheros del profesor (`git status` antes del commit).
- [ ] Tu nombre en la portada de la presentación y en el README.
- [ ] El aviso de "proyecto académico independiente, no avalado por las empresas" aparece en el README, en el artículo y en el post.
- [ ] No hay reseñas completas ni datos identificables.
- [ ] Las cifras del artículo coinciden con el notebook (están copiadas de la sección 8).
- [ ] Imágenes en buena resolución (los PNG de `figuras/` están a 150 dpi).
- [ ] Enlace al repositorio comprobado en una ventana de incógnito.
- [ ] Si prefieres esperar a tener la nota del profesor, nada te obliga a publicar antes.
