# Clasificación de características lingüísticas en textos provenientes de Twitter/X
> **Materia**: Text Mining 2024
> 
> **Alumna**: Stephanie Anneris Malvicini
> 
> **Profesora:** Dr. Laura Alonso Alemany
> 
> Facultad de Astronomía, Matemática, Física y Computación, Universidad Nacional de Córdoba

## Introducción
Mi trabajo bajo el proyecto [iTrust](https://www.chistera.eu/projects/itrust) tiene como objetivo simular el comportamiento de individuos en un entorno de redes sociales para estudiar cómo sus interacciones contribuyen a la polarización. Para ello, tomando como base y adaptando el trabajo de [GSMF], utilizamos modelos de aprendizaje automatizado alimentados por características del contexto al que el usuario fue expuesto para predecir qué atributos tendrá la respuesta del usuario. 

El conjunto de datos fue recolectado por otros miembros del proyecto y consta de tres archivos diferentes: un conjunto de datos de influencers conteniendo 301 tweets sobre el cambio climático posteados por 9 figuras influyentes; un conjunto de datos de respuestas conteniendo 212.220 tweets de 106.383 usuarios respondiendo a los posteos del conjunto de datos de influencers y; un conjunto de datos de perfiles conteniendo 383.121 tweets extra de 425 usuarios de los del conjunto de datos de respuestas, los cuales son generales y pueden no estar relacionados con el cambio climático. 

Los tweets de los influencers comienzan una conversación, y junto con las respuestas forman el **Contexto**. Los tweets de perfiles se utilizan para calcular la personalidad del **Usuario** y sus necesidades comunicacionales. 

La figura siguiente muestra una descripción general del las entradas y salidas de los clasificadores:
![Diagrama general](https://github.com/StephanieMalvicini/LinguisticCuesNLP/blob/main/images/componentes.drawio.png)

El contexto está representado por características lingüísticas (cantidad de palabras abusivas, polarizantes, morales y positivas/negativas), emociones y sentimientos. Las características lingüísticas relacionadas con cantidad de palabras, tanto para el contexto como para la predicción, se calculan con _pattern-matching_, comparando con diccionarios específicos para cada una de ellas. El mayor inconveniente con esta técnica es la gran cantidad de tweets con valor 0, generando clases mayoritarias que afectan el desempeño del clasificador. Se intentó superar estas inconveniencias filtrando _ground truths_ donde la característica a predecir estaba presente, pero los resultados no evidenciaron una mejora significativa.

## Hipótesis
Sustituir _pattern-matching_ con distancia entre palabras permitirá mejorar el desempeño del clasificador, medido en _F1 score_.

## Objetivos preliminares actualizados y avances
1. Como primera medida del preprocesamiento de los tweets se corrigieron errores de _encoding_ utilizando la librería ftfy, procurando mantener los emojis. Luego, ambas técnicas difieren. Para _pattern-matching_ se reemplazan y remueven algunos caracteres y luego se aplica _stemming_ a las palabras que no son _stopwords_. Para distancia entre palabras se tokeniza y remueven _stopwords_, caracteres de puntuación y URLs. Cabe destacar que al hacer uso de _embeddings_ para el cálculo de distancia entre palabras, consideramos no necesario aplicar _Stemming_ ni Lematización, aunque esto es una hipótesis y se requerirían experimentos para confirmar si mejora o no el reconocimiento de palabras de cada categoría.
2. La ejecución de los experimentos con _pattern-matching_ para obtener el baseline ya fue realizada. En las figuras siguientes se pueden observar los resultados para cada clasificador y cada predicción, en comparación con _random guessing baseline_: ![F1 scores](https://github.com/StephanieMalvicini/LinguisticCuesNLP/blob/main/images/pattern_matching_f1.png) ![Recall](https://github.com/StephanieMalvicini/LinguisticCuesNLP/blob/main/images/pattern_matching_recall.png) ![Precision](https://github.com/StephanieMalvicini/LinguisticCuesNLP/blob/main/images/pattern_matching_precision.png) ![Accuracy](https://github.com/StephanieMalvicini/LinguisticCuesNLP/blob/main/images/pattern_matching_accuracy.png)
3. Se investigaron diferentes tipos de _embeddings_ y algoritmos de distancia entre palabras, quedando seleccionados FastText y _cosine similarity_.
4. El objetivo preliminar 4 es muy amplio, por lo que se divide en sub-objetivos:    
	4.1. Calcular las palabras de cada categoría lingüística haciendo uso de distancia entre palabras, probando diferentes umbrales. Esto está en proceso, actualmente están siendo calculadas las palabras polarizantes. Se probó con umbral 0,7 pero no se identificaban palabras como _“person”_ y _“people”_, con distancia 0,64. Finalmente se estableció un umbral de 0,6, aunque se pudo observar que no identificaba todas las palabras identificadas por _pattern matching_ (por ejemplo, _”progressives”_, que está en el diccionario, y _”progress”_,  sus raíces coinciden y son identificadas por _pattern matching_, pero su distancia es de 0,33). Se está trabajando en hacer el algoritmo más eficiente, para luego replicarlo con las otras tres categorías lingüísticas.	
	4.2.  Ejecutar los experimentos utilizando distancia entre palabras. En espera a terminar el objetivo previo.
6. Evaluar los resultados: de forma extrínseca, analizando las diferencias en F1 scores y así emitir un juicio acerca de la hipótesis; e intrínseca, para tener una mejor noción de cómo se comportan las distintas configuraciones de herramientas de distancia de palabras.
7. Evaluar el clasificador utilizando [_Learning Interpretability Tool_](https://pair-code.github.io/lit/) para así entender qué atributos de entrada son más importantes y en qué casos el modelo se comporta de forma errática.


## Técnicas relevantes y avances en la metodología
**Stemming**: técnica de preprocesamiento que elimina los sufijos de las palabras basado en una lista predefinida de sufijos comunes. Algunas implementaciones utilizan reglas para corregir raíces malformadas [MK]. Utilizamos SnowballStemmer de la librería [NLTK].

**Tokenización**: técnica donde se divide el texto en unidades individuales denominadas _tokens_. La librería [NLTK] tiene un módulo de tokenización específico para tweets, adaptándose mejor al formato de Twitter/X, dejando intactos componentes como _hashtags_ y menciones a usuarios.

[FastText](https://fasttext.cc/): posee modelos para calcular representaciones de texto a través de _embeddings_. Se basan en la idea de subpalabras, representando las palabras como n-gramas, lo que permite capturar el significado semántico de palabras relacionadas morfológicamente y hasta incluso palabras fuera del vocabulario o palabras raras. Esto es de gran utilidad al trabajar con textos de Twitter dado que las personas tienden a abreviar y cometer equivocaciones al escribir. De los cuatro modelos pre entrenados en inglés disponibles, utilizamos _crawl-300d-2M-subword_, de 2 millones de palabras entrenado con información de subpalabras de Common Crawl, con 600.000 millones de tokens (600B en inglés).

**_Cosine similarity_**: elegida como medida de distancia entre palabras ya que compara las direcciones entre vectores y no las longitudes. Si dos vectores (_embeddings_) apuntan a la misma dirección, podemos asumir que representan el mismo significado. Además, es computacionalmente barato.

## Planificación actualizada
El siguiente diagrama muestra el estado de la planificación inicial, marcado en verde los objetivos ya cumplidos, en amarillos los en proceso y en rojo los pendientes. 
![Diagrama planificacion](https://github.com/StephanieMalvicini/LinguisticCuesNLP/blob/main/images/planificacion_actualizada.drawio.png)

Con mayor idea sobre los tiempos de ejecución de las tareas, se proponen los siguientes cambios en la planificación:
1. Terminar los objetivos 4 y 5 para antes de la presentación final al grupo de la materia.
1. Preparar la presentación y exponer.
1. Reunirme con los profesores para analizar si lo realizado es correcto, suficiente y cualquier cambio que sea necesario.
1. Trabajar en el objetivo 6
1. Escribir el _short paper_
1. Enviar el _short paper_ para correcciones.
1. Trabajar en correcciones y preparar la defensa final.
1. Defensa final.

## Referencias 
[GSMF] Gallo, F. R., Simari, G. I., Martinez, M. V., & Falappa, M. A. (2020). Predicting user reactions to Twitter feed content based on personality type and social cues. Future Generation Computer Systems, 110, 918-930.

[MK] Murel, J., & Kavlakoglu E. (2023). What are stemming and lemmatization?. IBM. https://www.ibm.com/topics/stemming-lemmatization.

[NLTK] Bird, Steven, Edward Loper and Ewan Klein (2009), Natural Language Processing with Python. O’Reilly Media Inc.
