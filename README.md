## Tecnologías ómicas: Genómica y aplicaciones de la Pangenómica de Plantas

Sesión práctica de 2h dentro del 
[Máster en Biología Molecular y Celular](https://estudios.unizar.es) de la Universidad de Zaragoza.
Incluye dos tareas, una en el terminal y otra más sencilla en el navegador Web.

### 1. Anotación de genes en múltiples genomas de la misma especie [terminal]

#### 1.1 Selección y descarga de un genoma de arroz en formato FASTA (*soft-masked*)

| grupo | URL de Ensembl |
|-----|------|
|Indica group|[GCA_001618785.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Indica_Group/GCA_001618785.1)|
|Indica group|[GCA_001618795.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Indica_Group/GCA_001618795.1)|
|Indica group|[GCA_009829375.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Indica_Group/GCA_009829375.1)|
|Indica group|[GCA_009829395.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Indica_Group/GCA_009829395.1)|
|Indica group|[GCA_009831025.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Indica_Group/GCA_009831025.1)|
|Indica group|[GCA_009831045.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Indica_Group/GCA_009831045.1)|
|Indica group|[GCA_009831295.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Indica_Group/GCA_009831295.1)|
|Indica group|[GCA_009831355.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Indica_Group/GCA_009831355.1)|
|Indica group|[GCA_009914875.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Indica_Group/GCA_009914875.1)|
|Japonica group|[GCA_001433935.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_Japonica_Group/GCA_001433935.1)|
|aromatic subgroup|[GCA_009831255.1](https://ftp.ebi.ac.uk/pub/ensemblorganisms/Oryza_sativa_aromatic_subgroup/GCA_009831255.1)|

#### 1.2 Obtención de evidencia transcripcional de bases de datos

Si visitas https://www.ncbi.nlm.nih.gov/nuccore/?term=txid15368[organism:exp]%20AND%20biomol_mrna[prop] 
<!--Si visitas https://www.ncbi.nlm.nih.gov/nuccore/?term=txid4530[organism:exp]%20AND%20biomol_mrna[prop]-->
verás que hay más de 260k secuencias de mensajeros (mRNA) conocidas de la gramínea **Brachypodium distachyon**, que se pueden exportar en formato FASTA.
Como una parte serán redundantes podemos quedarnos con las secuencias únicas representativas con ayuda
de herramientas como [MMSeq2](https://www.nature.com/articles/nbt.3988), con un comando parecido a este: 

    $ mmseqs easy-linclust sequence.fasta --threads 6 --min-seq-id 0.98 rice.mRNA.nr.faa ./
    $ mv rice.mRNA.nr.faa_rep_seq.fasta rice.mRNA.nr.fasta

Las secuencias no redundantes al 98% de identidad están disponible en el fichero [data/Bdistachyon.mRNA.nr.fasta.gz](./data/Bdistachyon.mRNA.nr.fasta.gz).

#### 1.3 Anotación con software [EviAnn](https://www.nature.com/articles/s41592-026-03156-0)

    $ eviann.sh -t 2 -g softmasked.fa -e Bdistachyon.mRNA.nr.fasta.gz

Como este paso llevará un rato lo mejor será saltar de momento a la tarea 2.

#### 1.4 Comparemos las anotaciones entre genomas distintos



	
### 2. Análisis de pangenes en variedades de cebada [Web]

En esta tarea usaremos una aplicación Web para mapear secuencias de cebada.
En vez de localizar estas secuencias sobre un genoma de referencia representativo de la especie, MorexV3 en este caso,
vamos a localizarlas sobre un pangenoma modelado como grafo.
Usaremos [BARLEYMAP](https://www.biorxiv.org/content/10.64898/2026.08.06.741139v1) en su modo de análisis de grafos.

#### 2.1 Selección y copia de una o más secuencias en formato FASTA 

#### 2.2 Mapea las secuencias en https://barleymap.eead.csic.es/barleymap/graph/ contra el grafo `Pan20-mmap-pro`

#### 2.3 Compara tus resultados con los compañeros

En cuántos genomas las encuentras, en qué cromosomas, en qué orientación?


