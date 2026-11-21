# Proyecto Parte 2: Referencias y uso de IA generativa

**Grupo 35**: Juan Diego Ramírez Garzón, Sebastián Vargas Otero, Mateo Zambrano Burbano, Nicolás Mario Romero Colmenares

## 1. Modelos preentrenados

| Modelo | Uso en el proyecto | Fuente / enlace | Licencia |
|---|---|---|---|
| **XLM-RoBERTa base** (`xlm_roberta_base_multi`) | Experimentos E3-E5: comparar las configuraciones de datos (con y sin aumentación). *Fine-tuning* completo con una cabeza de clasificación de 3 clases | KerasHub: https://keras.io/keras_hub/api/models/xlm_roberta/ · Kaggle Models: https://www.kaggle.com/models/keras/xlm_roberta · Original: https://github.com/facebookresearch/fairseq/tree/main/examples/xlmr | MIT |
| **XLM-RoBERTa large** (`xlm_roberta_large_multi`) | Experimentos E6-E7 y miembros del ensamble final E8 (dos semillas). *Fine-tuning* de todo el modelo excepto la matriz de *embeddings* de tokens (congelada para que cupiera en la GPU T4 de Colab), 1 época, con una cabeza de clasificación de 3 clases | KerasHub y Kaggle Models (mismos enlaces) | MIT |

Referencia del modelo: Conneau, A., Khandelwal, K., Goyal, N., et al. (2020). *Unsupervised Cross-lingual Representation Learning at Scale*. ACL 2020. https://arxiv.org/abs/1911.02116

> Todos los modelos usados son *encoder-only*, como exige la aclaración del curso del 12/11/2026. No se usó ningún modelo *decoder-only* ni generativo de texto, ni para clasificar ni para generar o aumentar datos.

## 2. Datos externos

| Recurso | Uso | Fuente / enlace | Licencia |
|---|---|---|---|
| **Diccionarios bilingües MUSE** (`es-en`, `en-es`) | Aumentación A (*code-switching* léxico): sustituir palabras español↔inglés | https://github.com/facebookresearch/MUSE#ground-truth-bilingual-dictionaries · descarga: https://dl.fbaipublicfiles.com/arrival/dictionaries/es-en.txt y .../en-es.txt | CC BY-NC 4.0 (uso académico no comercial permitido) |

Referencia: Conneau, A., Lample, G., Ranzato, M., Denoyer, L., Jégou, H. (2018). *Word Translation Without Parallel Data*. ICLR 2018. https://arxiv.org/abs/1710.04087

No se usaron otros conjuntos de datos de reseñas. Los datos de entrenamiento son únicamente `train.csv` de la competencia. **`eval.csv` no se usó para entrenar.** Solo se usó para generar las predicciones de envío y para comparar su distribución de idiomas en el análisis exploratorio, sin usar etiquetas, porque no las tiene.

## 3. Datos sintéticos: cómo se construyeron

Las dos estrategias de aumentación se aplican **solo a la partición de entrenamiento** (90 % de `train.csv`, estratificada por idioma × clase). La validación (10 %) nunca se aumenta. No se usa ningún modelo para generar texto.

1. **A, code-switching léxico.** Para cada reseña en español o inglés, con probabilidad 0.3, se crea una copia en la que cada palabra (de 3 letras o más y que no sea una negación o un conector contrastivo protegido) se reemplaza, con probabilidad 0.3, por su primera traducción en el diccionario MUSE correspondiente. La etiqueta se conserva.
2. **B, inserción de oraciones neutras de otro idioma.** Se construye un banco de oraciones tomadas de reseñas **neutrales** de entrenamiento, separado por idioma, y se descartan las que contienen palabras de opinión. Para el 40 % de las reseñas de entrenamiento se crea una copia con 1-2 oraciones de ese banco, de un idioma distinto al de la reseña, insertadas en posiciones intermedias (nunca la primera ni la última oración). La etiqueta se conserva.

Semilla fija (`SEMILLA = 42`), así que los conjuntos aumentados son reproducibles al ejecutar el notebook.

## 4. Librerías

TensorFlow, Keras 3, KerasHub, scikit-learn (partición, métricas y la referencia clásica TF-IDF + regresión logística), pandas, NumPy, SciPy (prueba de McNemar), matplotlib y seaborn.

## 5. Uso de IA generativa en el desarrollo del proyecto

- Se usó un asistente de IA (Claude, de Anthropic) como apoyo para: interpretar el enunciado, proponer el plan de trabajo, sugerir estrategias de aumentación compatibles con las restricciones del curso, escribir y depurar parte del código del notebook (carga, aumentación, funciones de entrenamiento y evaluación, adaptación a Google Colab) y revisar que el texto del notebook fuera consistente con los resultados obtenidos.
- El grupo revisó, ejecutó y validó todo el código, tomó las decisiones finales de modelado a partir de los resultados experimentales e hizo el análisis de resultados y errores.
- La IA generativa **no** se usó para etiquetar textos de `eval`, ni para generar datos de entrenamiento, ni como parte del modelo entregado. Las predicciones enviadas a Kaggle provienen exclusivamente del modelo entregado.
