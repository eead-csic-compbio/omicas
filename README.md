## Tecnologías ómicas: Genómica y aplicaciones de la Pangenómica de Plantas

Sesión práctica de 2h dentro del 
[Máster en Biología Molecular y Celular](https://estudios.unizar.es) de la Universidad de Zaragoza.
Incluye dos tareas, una en el terminal y otra más sencilla en el navegador Web.

### 0. Instalación de software EviAnn vía conda

Para ejecutar las siguientes operaciones tendrás que abrir un terminal:

    # esto es un comentario, esta documentación la probamos ftramull y yo

    # 0.1. Descarga el instalador oficial para Linux de 64 bits y hazlo ejecutable
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
    chmod +x Miniconda3-latest-Linux-x86_64.sh

    # 0.2. Ejecuta la instalación automatizada (-b acepta términos y -u sobrescribe instalaciones previas)
    ./Miniconda3-latest-Linux-x86_64.sh -b -u

    # 0.3. Inicialización del intérprete de comandos (Bash),
    # fuerza el registro del ejecutable de Conda dentro del archivo de configuración del sistema 
    # (.bashrc) para que la terminal reconozca el comando de forma global.
    # Al completar este paso, aparecerá el prefijo (base) al inicio de la línea de comandos
    ~/miniconda3/bin/conda init bash
    source ~/.bashrc

    # 0.4 Aceptación de los términos de servicio
    conda tos accept

    # 0.5: Configuración estricta de canales (Channels) de Bioconda,
    # agrega los repositorios comunitarios de bioinformática; el orden ayuda a evitar conflictos de versiones
    conda config --add channels defaults
    conda config --add channels bioconda
    conda config --add channels conda-forge
    conda config --set channel_priority strict

    # 0.6: Creación y activación de un entorno aislado con python 3.10 que no interfiera con el SO nativo,
    # al terminar el prefijo de tu consola mutará de (base) a (eviann_env)
    conda create -n eviann_env python=3.10 -y
    conda activate eviann_env

    # 0.7: Instalación de EviAnn y validación final
    conda install eviann -y
    eviann.sh --help

    # si necesitas salir de este entorno
    conda deactivate

    # si necesitas usar EviAnn en otra ocasión
    conda activate eviann_env

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

Para hacer la práctica en un PC limitado podemos por ejemplo quedarnos solamente con el cromosoma 12 con ayuda de un 
[one-liner](https://github.com/eead-csic-compbio/scripting_linux_shell/blob/master/session4.md):

    $ zcat softmasked.fa.gz | perl -lne 'if(/^>12/){$ok=1; print} elsif($ok){ last if(/^>/); print }' > softmasked.chr12.fa

#### 1.2 Obtención de evidencia transcripcional de bases de datos

En recursos como [RAP-DB](https://rapdb.dna.naro.go.jp/download/irgsp1.html) 
puedes obtener secuencias de tránscritos de arroz (CDS + UTRs + intrones) en formato FASTA. 
Estas secuencias sirven de evidencia física de regiones del genoma de arroz que se transcriben.
Por comodidad puedes descargar los tránscritos que se han adscrito al chr12 de arroz de 
[data/IRGSP-1.0_gene_2026-02-05.chr12.fasta.gz](./data/IRGSP-1.0_gene_2026-02-05.chr12.fasta.gz).

#### 1.3 Obtención de evidencia proteómica de bases de datos

Otra fuente de evidencia útil son las secuencias de aminoácidos de proteínas de arroz conocidas,
que se guardan en un recurso central como es [UniProt](https://www.uniprot.org). 
Ahora te pido que descargues las secuencias proteicas de arroz de
[data/uniprot_sprot.Osativa.fasta.gz](./data/uniprot_sprot.Osativa.fasta.gz).

#### 1.4 Anotación basada en evidencia experimental

Con ayuda del software [EviAnn](https://www.nature.com/articles/s41592-026-03156-0) 
en este paso vamos a anotar el genoma que hemos descargado, o su chr12, es decir,
vamos a averiguar en qué segmentos encontramos genes que codifican proteínas. 
Antes debemos descomprimir los ficheros de secuencias con extensión `.gz`:

    $ gunzip IRGSP-1.0_gene_2026-02-05.chr12.fasta.gz uniprot_sprot.Osativa.fasta.gz

    $ eviann.sh -t 2 -g softmasked.chr12.fa -e IRGSP-1.0_gene_2026-02-05.chr12.fasta -s uniprot_sprot.Osativa.fasta

Como este paso llevará un rato lo mejor será saltar de momento a la tarea 2.

#### 1.5 Comparemos las anotaciones entre genomas distintos

Cuando haya terminado el paso anterior puedes revisar los resultados.
Por ejemplo, el fichero `softmasked.chr12.fa.gff` debería ser similar al que tienes disponible en
[data/softmasked.chr12.fa.gff.gz](./data/softmasked.chr12.fa.gff.gz), cuyas primeras líneas son:

    12      EviAnn  gene    25909   37142   .       -       .       ID=LOC_00000264;geneID=LOC_00000264;gene_biotype=protein_coding
    12      EviAnn  mRNA    25909   37142   .       -       .       ID=LOC_00000264-mRNA-1;Parent=LOC_00000264;evidence_protein_id=sp|Q8GU83|AB41G_ORYSJ:ABC_transporter_G_family_member_41_OS_Oryza_sativa_subsp._japonica_OX_39947_GN_ABCG41_PE_3_SV_1;evidence_transcript_id=sp|Q8GU83|AB41G_ORYSJ:12:31525.5;start_codon=ATG;stop_codon=TAA;evidence=protein_only;num_exons=23;geneID=LOC_00000264;gene_biotype=protein_coding
    12      EviAnn  exon    25909   26178   .       -       .       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:exon:1
    12      EviAnn  exon    26284   26405   .       -       .       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:exon:2
    12      EviAnn  exon    27722   27875   .       -       .       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:exon:3
    12      EviAnn  exon    27993   28164   .       -       .       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:exon:4
    12      EviAnn  exon    28299   28532   .       -       .       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:exon:5
    ...
    12      EviAnn  CDS     25909   26178   .       -       0       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:CDS:1
    12      EviAnn  CDS     26284   26405   .       -       2       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:CDS:2
    12      EviAnn  CDS     27722   27875   .       -       0       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:CDS:3
    12      EviAnn  CDS     27993   28164   .       -       1       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:CDS:4
    12      EviAnn  CDS     28299   28532   .       -       1       Parent=LOC_00000264-mRNA-1;ID=LOC_00000264-mRNA-1:CDS:5
    ...
    12      EviAnn  gene    42617   45355   .       -       .       ID=LOC_00000265;geneID=LOC_00000265;gene_biotype=protein_coding

Podemos revisar y contar algunos tipos de genes con más *one-liners*, como por ejemplo `transporter`, `Disease_resistance` o `transcription_factor`:

    $ grep transporter softmasked.chr12.fa.gff
    $ grep -c grep transporter softmasked.chr12.fa.gff
	
### 2. Análisis de pangenes en variedades de cebada [Web]

En esta tarea usaremos una aplicación Web para mapear secuencias de cebada.
En vez de localizar estas secuencias sobre un genoma de referencia representativo de la especie, MorexV3 en este caso,
vamos a localizarlas sobre un pangenoma modelado como grafo.
Usaremos [BARLEYMAP](https://www.biorxiv.org/content/10.64898/2026.08.06.741139v1) en su modo de análisis de grafos.

#### 2.1 Selección y copia de una o más secuencias en formato FASTA 

#### 2.2 Mapea las secuencias en https://barleymap.eead.csic.es/barleymap/graph/ contra el grafo `Pan20-mmap-pro`

#### 2.3 Compara tus resultados con los compañeros

En cuántos genomas las encuentras, en qué cromosomas, en qué orientación?


