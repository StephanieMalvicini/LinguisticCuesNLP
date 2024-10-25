# Clasificación de características lingüísticas en textos provenientes de Twitter/X
> **Materia**: Text Mining 2024

> **Alumna**: Stephanie Anneris Malvicini

> **Profesora:** Dr. Laura Alonso Alemany

> Facultad de Astronomía, Matemática, Física y Computación, Universidad Nacional de Córdoba

## Introducción
Mi trabajo bajo el proyecto [iTrust](https://www.chistera.eu/projects/itrust) tiene como objetivo simular el comportamiento de individuos en un entorno de redes sociales para estudiar cómo sus interacciones contribuyen a la polarización. Para ello, tomando como base y adaptando el trabajo de [GSMF], utilizamos modelos de aprendizaje automatizado alimentados por características del contexto al que el usuario fue expuesto para predecir qué atributos tendrá la respuesta del usuario. 

El conjunto de datos fue recolectado por otros miembros del proyecto y consta de tres archivos diferentes: un conjunto de datos de influencers conteniendo 301 tweets sobre el cambio climático posteados por 9 figuras influyentes; un conjunto de datos de respuestas conteniendo 212.220 tweets de 106.383 usuarios respondiendo a los posteos del conjunto de datos de influencers y; un conjunto de datos de perfiles conteniendo 383.121 tweets extra de 425 usuarios de los del conjunto de datos de respuestas, los cuales son generales y pueden no estar relacionados con el cambio climático. 

La figura siguiente muestra una descripción general del trabajo que ya se ha realizado:
![Diagrama general](https://github.com/StephanieMalvicini/LinguisticCuesNLP/blob/main/images/componentes.drawio.png)

Las características linguísticas relacionadas con cantidad de palabras, tanto para el contexto como para la predicción, se calculan con _pattern-matching_, comparando con diccionarios. El mayor inconveniente con esta técnica es la gran cantidad de tweets con valor 0, generando clases mayoritarias que afectan el desempeño del clasificador. Se intentó superar estas inconveniencias filtrando _ground truths_ donde la característica a predecir estaba presente, pero los resultados no evidenciaron una mejora significativa.

## Hipótesis
Sustituir _pattern-matching_ con distancia entre palabras permitirá mejorar el desempeño del clasificador, medido en _F1 score_.

## Objetivos preliminares
Para confirmar o descartar la hipótesis se plantean los siguientes objetivos:
1. Preprocesamiento de datos: dado que para el trabajo realizado anteriormente se utilizó otro conjunto de datos, se deberán investigar y aplicar las técnicas de preprocesamiento necesarias para luego aplicar _pattern-matching_ y distancia entre palabras.
1. Ejecución de los experimentos con pattern-matching para obtener el baseline.
1. Investigar diferentes tipos de _embeddings_ y algoritmos de distancia entre palabras, seleccionar los relevantes/computables y priorizarlos.
1. Ejecutar los experimentos utilizando distancia entre palabras. Probar diferentes umbrales (palabras a una distancia menor de cierto número, o las k palabras más cercanas).
1. Evaluar los resultados: de forma extrínseca, analizando las diferencias en _F1 scores_ y así emitir un juicio acerca de la hipótesis; e intrínseca, para tener una mejor noción de cómo se comportan las distintas configuraciones de herramientas de distancia de palabras.
1. Evaluar el clasificador utilizando [_Learning Interpretability Tool_](https://pair-code.github.io/lit/) para así entender qué atributos de entrada son más importantes y en qué casos el modelo se comporta de forma errática.

## Técnicas relevantes
Para poder calcular cuántas palabras del tweet están a no más de cierta distancia de las palabras del diccionario, debemos calcular _embeddings_ a ambas cosas y luego comparar las distancias.

Si bien existen muchos modelos de _embeddings_, para este proyecto nos concentraremos en aquellos entrenados utilizando datos en inglés de Twitter/X o redes sociales, como lo son [BERTweet](https://huggingface.co/docs/transformers/model_doc/bertweet) o [fastText](https://fasttext.cc/). Esta elección se debe a que el contenido en estas plataformas es particular, suele ser corto y tener jergas o abreviaciones.

También hay varias formas de medir distancia entre vectores, como lo son la distancia de Manhattan, la Euclidiana o la del coseno. Quizás calcular distancias utilizando estas técnicas se vuelva muy costoso por la longitud de los _embeddings_ y la cantidad de palabras del diccionario, por lo que también hemos de considerar aproximaciones.

## Planificación
![Diagrama planificacion](https://github.com/StephanieMalvicini/LinguisticCuesNLP/blob/main/images/planificacion.drawio.png)

## Referencias 
[GSMF] Gallo, F. R., Simari, G. I., Martinez, M. V., & Falappa, M. A. (2020). Predicting user reactions to Twitter feed content based on personality type and social cues. Future Generation Computer Systems, 110, 918-930.

