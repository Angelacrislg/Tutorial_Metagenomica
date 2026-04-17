# Tutorial_Metagenomica
Proyecto de master de metagenomica 
El presente estudio se centra en el análisis detallado de la microbiota intestinal de 23 ratones, con el objetivo de comparar los grupos experimentales definidos por las combinaciones de sexo (macho/hembra) y genotipo (KO/WT). La microbiota intestinal juega un papel crucial en la salud del huésped, influyendo en procesos metabólicos, inmunológicos y neurológicos. Por lo tanto, comprender las diferencias en la composición y función de la microbiota entre estos grupos experimentales es fundamental para evaluar el impacto de las variables estudiadas.

Para llevar a cabo este análisis, se utilizaron datos metagenómicos obtenidos mediante la secuenciación de la región 16S rRNA de muestras fecales. Se implementó un enfoque bioinformático estructurado, empleando herramientas especializadas como Mothur, LEfSe, MaAsLin2 y PICRUSt2. Estas herramientas permiten una caracterización exhaustiva de la microbiota intestinal, desde la clasificación taxonómica hasta la predicción de funciones metabólicas.
# Metodologia:

# Anlisis de datos en mothur

## 1. Filtrado y Preprocesamiento de Secuencias

Para el análisis de datos en Mothur, se descargaron los archivos de meta_16seq junto con las referencias genómicas v132 y v138 de la base de datos SILVA. El objetivo fue generar un archivo FASTA que contenga la región 16S del rRNA de E. coli, utilizando los cebadores de interés para amplificar los fragmentos específicos. Una vez obtenido el fasta personalizamos la base de dato Silva, para alinearse con las regiones hipervariables de interés (V3-V4). Para este procedimiento se usó la herramienta pcr.seqs de mothur que recorta las bases de datos en las regiones de interés antes señaladas en el articulo de referencia.
