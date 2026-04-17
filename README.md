# Tutorial_Metagenomica
Proyecto de master de metagenomica 
El presente estudio se centra en el análisis detallado de la microbiota intestinal de 23 ratones, con el objetivo de comparar los grupos experimentales definidos por las combinaciones de sexo (macho/hembra) y genotipo (KO/WT). La microbiota intestinal juega un papel crucial en la salud del huésped, influyendo en procesos metabólicos, inmunológicos y neurológicos. Por lo tanto, comprender las diferencias en la composición y función de la microbiota entre estos grupos experimentales es fundamental para evaluar el impacto de las variables estudiadas.

Para llevar a cabo este análisis, se utilizaron datos metagenómicos obtenidos mediante la secuenciación de la región 16S rRNA de muestras fecales. Se implementó un enfoque bioinformático estructurado, empleando herramientas especializadas como Mothur, LEfSe, MaAsLin2 y PICRUSt2. Estas herramientas permiten una caracterización exhaustiva de la microbiota intestinal, desde la clasificación taxonómica hasta la predicción de funciones metabólicas.
# Metodologia:

# Anlisis de datos en mothur

## 1. Filtrado y Preprocesamiento de Secuencias

Para el análisis de datos en Mothur, se descargaron los archivos de meta_16seq junto con las referencias genómicas v132 y v138 de la base de datos SILVA. El objetivo fue generar un archivo FASTA que contenga la región 16S del rRNA de E. coli, utilizando los cebadores de interés para amplificar los fragmentos específicos. Una vez obtenido el fasta personalizamos la base de dato Silva, para alinearse con las regiones hipervariables de interés (V3-V4). Para este procedimiento se usó la herramienta pcr.seqs de mothur que recorta las bases de datos en las regiones de interés antes señaladas en el articulo de referencia.
Los reultados resultantes de este porceso de ven evidenciados en la siguiente grafica con las características de las secuencias obtenida. Esta tabla indica la posicion inicial y final de las lecturas, el numero total de bases en la secuencia y el numero de secuensias iniciales obtenidas.

```         
        Start   End NBases  Ambigs  Polymer NumSeqs
Minimum:    1   303 303 0   3   1
2.5%-tile:  1   440 440 0   3   37083
25%-tile:   1   458 458 0   5   370828
Median:     1   460 460 0   5   741656
75%-tile:   1   465 465 0   6   1112483
97.5%-tile: 1   465 465 8   6   1446228
Maximum:    1   602 602 108 301 1483310
Mean:       1   459 459 0   5
# of unique seqs:   1483310
total # of seqs:    1483310
```

Una vez obtenido el archivo FASTA con el que se trabajará, se procede a la etapa de filtrado de calidad utilizando la herramienta screen.seqs. Este paso permite eliminar secuencias que no cumplen con criterios de calidad previamente definidos.

Para nuestro análisis, aplicamos los siguientes filtros: • Eliminación de bases ambiguas (maxambig=0).

•Selección de secuencias con una longitud mínima de 400 bases y máxima de 500 bases (minlength=400, maxlength=500).

• Remoción de secuencias con homopolímeros excesivos (maxhomop=8).

Tras aplicar estos filtros, obtenemos como resultado el archivo de salida:

```         
    META_16.trim.contigs.good.fasta
```

El resultado de documento obtenido se evidencia en la siguiente tabla\
A continuación, realizamos un proceso de reducción de redundancia utilizando la herramienta unique.seqs, que agrupa secuencias idénticas y conserva únicamente las secuencias únicas. Este procedimiento optimiza el almacenamiento y mejora el rendimiento en los análisis de clustering y OTUs. El archivo de salida generado es:

```         
  META_16.trim.contigs.good.unique.fasta
```

Tras aplicar estos filtros, obtenemos como resultado el archivo de salida donde se evidencia la disminución en el número de secuencias analizadas después de aplicar filtros es un resultado esperado y positivo, ya que indica que se han eliminado las secuencias de baja calidad y el ruido, lo que mejora la calidad de los resultados.:

```         
    Start   End NBases  Ambigs  Polymer NumSeqs
Minimum:    1   1169    412 0   3   1
2.5%-tile:  1   1169    440 0   4   29632
25%-tile:   1   1169    458 0   5   296317
Median:     1   1169    460 0   6   592634
75%-tile:   1   1169    465 0   6   888951
97.5%-tile: 1   1169    465 0   6   1155636
Maximum:    1   1169    496 0   8   1185267
Mean:       1   1169    459 0   5
# of unique seqs:   403047
total # of seqs:    1185267
```

## 2. Alineamiento y Preclustering.

Con el archivo FASTA filtrado y amplificado en las regiones de interés, alineamos las secuencias contra la base de datos de referencia Silva. Luego, realizamos un preclustering con la herramienta *pre.cluster*, que agrupa secuencias similares con una diferencia máxima de 4 bases. Este paso es crucial para reducir la cantidad de secuencias únicas y minimizar el ruido en los datos sin afectar la diversidad microbiana.

Al ejecutar el comando, Mothur genera el mensaje “Splitting by sample”, lo que indica que el software divide las secuencias por muestra y las procesa de forma independiente. Este procedimiento asegura que el preclustering respete la estructura original de las muestras, evitando la mezcla de datos entre ellas.

## 3.Eliminacion de Quimeras. Las secuencias obtenidas tras el preclustering se someten a una detección y eliminación de quimeras, que pueden haberse generado durante la PCR. Para ello, utilizamos la herramienta *chimera.vsearch*, que identifica y remueve secuencias quiméricas de manera eficiente.

Alternativamente, este procedimiento puede realizarse con *chimera.uchime*, una herramienta más lenta pero menos estricta en la detección de quimeras.

El archivo de salida tras la eliminación de quimeras:

```         
   META_16.trim.contigs.good.unique.precluster.pick.fasta
```

## 3. Clasificación Taxonómica y Normalización

Finalizados los procesos de filtrado y eliminación de secuencias no deseadas, obtenemos un archivo FASTA optimizado y listo para ser sometido a clasificación taxonómica. Para ello, utilizamos la base de datos de referencia Silva personalizada, previamente recortada en las regiones V3-V4.

Antes de la clasificación, realizamos una eliminación de grupos taxonómicos indeseados con la herramienta remove.lineage, eliminando contaminantes como cloroplastos,mitocondrias,eucariotas,arqueas,taxones desconocidos.

Por último, aplicamos un proceso de normalización para estandarizar el número de lecturas por muestra, garantizando comparaciones equitativas en los análisis posteriores. El archivo de salida tras normalización:

```         
   META_16.trim.contigs.good.unique.precluster.pick.taxonomy
```

## 4.Generacion de OTUs.

Las Unidades Taxonómicas Operativas (OTUs) son grupos de secuencias con alto grado de similitud que permiten la clasificación de organismos en estudios de microbiomas, especialmente cuando no se dispone de una taxonomía completamente definida. Generalmente, la agrupación de OTUs se realiza con un umbral de identidad del 97% o superior, lo que indica que las secuencias dentro de un mismo OTU probablemente pertenecen a la misma especie o a un grupo filogenéticamente cercano.

En Mothur, la generación de OTUs se lleva a cabo mediante la herramienta *classify.otu*, que permite agrupar las secuencias y asignarles una clasificación taxonómica.

Los parámetros clave para este proceso incluyen: • Nivel de agrupamiento: Se define un umbral del 3% de diferencia (label=0.03), lo que equivale a un 97% de identidad entre secuencias. • Abundancia: Se puede reportar la abundancia relativa o absoluta de cada OTU en la muestra.

El comando utilizado en Mothur es el siguiente:

```         
classify.otu(list=final.opti_mcc.list, count=final.count_table, 
taxonomy=final.taxonomy, label=0.03, basis=sequence, relabund=FALSE, output=simple, printlevel=6)
```

Este proceso genera dos archivos de salida: 1. Archivo de clasificación taxonómica, que asigna cada OTU a un posible taxón. 2. Tabla de abundancia de OTUs, que muestra la cantidad de secuencias agrupadas en cada unidad taxonómica.

### 4.2. Análisis de OTUs.

Para analizar las OTUs (Unidades Taxonómicas Operativas) en Mothur, se genera un archivo utilizando la herramienta *make.shared*, el cual contiene el número de secuencias asignadas a cada OTU en cada muestra. El archivo de salida resultante es:

```         
•   final.opti_mcc.shared → Contiene la matriz de abundancia de OTUs por muestra.
```

A continuación, se realiza un conteo de grupos con la herramienta count.groups, cuyo propósito es mostrar el número total de secuencias en cada muestra. Esto permite identificar la muestra con el menor número de secuencias para llevar a cabo un submuestreo (subsampling).

El subsampling se realiza con el comando *sub.sample*, el cual estandariza las muestras reduciéndolas al tamaño de la muestra más pequeña. Este paso es fundamental para evitar sesgos en el análisis de diversidad.

Finalmente, se obtiene la información necesaria para evaluar la riqueza de las OTUs utilizando la herramienta *rarefaction.single*. Este procedimiento genera datos para la construcción de una curva de rarefacción, que permite estimar la riqueza de OTUs en función del número de secuencias analizadas.

Este análisis es clave para evaluar la diversidad microbiana y garantizar que la profundidad de muestreo sea suficiente para capturar la variabilidad de la comunidad.

```{r}
path_otu = "/Users/angelaluquegarcia/Documents/metagenomica/META_16_MPTHUR_OTU/final.opti_mcc.groups.rarefaction"
path_pylotipe ="/Users/angelaluquegarcia/Documents/metagenomica/META_16_MPTHUR_PHILOTYPE/final.tx.groups.rarefaction" 

rareedata_OTU <- read.delim(path_otu,row.names = 1)
rareedata_phylotipe <- read.delim(path_pylotipe,row.names = 1)

raredata_OTU<- rareedata_OTU[,grep("ci",names(rareedata_OTU), value = TRUE, invert = TRUE)]
raredata_phylotipe<- rareedata_phylotipe[,grep("ci",names(rareedata_phylotipe), value = TRUE, invert = TRUE)]
        
matplot(as.matrix(raredata_OTU),type = "l")

```

### 4.3. Cálculo de Índices de Diversidad y Análisis de Similitud.

Para evaluar la diversidad dentro de las muestras, se calcularon índices de diversidad alfa utilizando el número de secuencias y el número de OTUs observadas.

Los índices empleados fueron: • Índice de Simpson: Considera la dominancia de ciertas especies dentro de la comunidad. • Índice de Shannon: Evalúa la riqueza y equidad de la comunidad microbiana.

El resultado de este cálculo se almacena en el archivo: • final.opti_mcc.groups.ave-std.summary → Contiene los valores promedio y la desviación estándar de los índices de diversidad.

Para analizar la similitud entre las OTUs de distintas muestras, se generaron matrices de distancia utilizando la herramienta dist.shared. Se calcularon dos métricas clave: 1. Bray-Curtis: Basado en la abundancia relativa de las OTUs. 2. Jaccard (jClass): Basado en la presencia/ausencia de OTUs.

```         
dist.shared(shared=final.opti_mcc.shared, calc=braycurtis-jclass, subsample=T)
```

Los archivos de salida generados son: • final.opti_mcc.braycurtis.0.03.lt.ave.dist → Distancias promedio según Bray-Curtis. • final.opti_mcc.jclass.0.03.lt.ave.dist → Distancias promedio según Jaccard.
