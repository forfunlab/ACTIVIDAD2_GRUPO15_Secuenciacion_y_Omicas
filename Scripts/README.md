# Guía de Ejecución de los Scripts de Análisis

Este directorio contiene las herramientas y flujos de trabajo necesarios para procesar los datos de secuenciación crudos de RNA-seq hasta la obtención de perfiles de expresión diferencial y su posterior análisis de enriquecimiento funcional.

---

## Flujo de Trabajo y Contenido

El análisis se divide en dos fases consecutivas y complementarias:

### Fase 1: Control de Calidad, Filtrado y Cuantificación
* **Script:** [`control_calidad_y_conteo.md`](./control_calidad_y_conteo.md)
* **Objetivo:** Preparar los datos crudos y cuantificar los transcritos.
* **Herramientas utilizadas (Entorno Conda):**
  * `FastQC`: Control de calidad visual de las lecturas crudas y filtradas.
  * `fastp`: Filtrado de adaptadores, eliminación de bases de baja calidad y lecturas cortas.
  * `MultiQC`: Agregación de todos los reportes de calidad en un único informe interactivo HTML.
  * `Salmon` (versión recomendada `1.10.3`): Creación de índice de transcriptoma y pseudoalineamiento rápido para cuantificar niveles de expresión (*transcripts abundance estimation*).

### Fase 2: Análisis Estadístico y Visualización en R
* **Script:** [`visualizacion_genes.R`](./visualizacion_genes.R)
* **Objetivo:** Determinar los genes con expresión diferencial significativa y analizar sus implicaciones biológicas.
* **Paquetes R claves:**
  * `tximport`: Importa las cuantificaciones de Salmon (`quant.sf`) y las agrupa a nivel de gen utilizando el mapa de referencia `Transcrito_a_Gen.tsv`.
  * `DESeq2`: Modelado estadístico y normalización de conteos para identificar genes diferencialmente expresados (DEGs) entre las condiciones del estudio (e.g., `obeso1` vs `obeso2`).
  * `EnhancedVolcano` & `pheatmap`: Generación de gráficos volcán y mapas de calor interactivos/estáticos con los genes y el top 10 de expresión diferencial.
  * `clusterProfiler` & `ReactomePA`: Análisis de enriquecimiento funcional mediante sobre-representación (ORA) para procesos biológicos (Gene Ontology - BP) y rutas metabólicas (Reactome).
  * `flextable` & `dplyr`: Formateo de tablas elegantes y manipulación ágil de datos biológicos.

---

## 🚀 Instrucciones de Inicio Rápido

Sigue estos pasos en orden secuencial para reproducir el análisis completo:

### Paso 1: Configurar el Entorno de Trabajo (Conda)
Crea y activa el entorno virtual de Conda con las herramientas necesarias para la primera fase:
```bash
# Crear entorno conda
conda create -vv -n Actividad2 -c bioconda -c conda-forge -c defaults -c r fastqc fastp multiqc salmon=1.10.3

# Activar el entorno
conda activate Actividad2
```

### Paso 2: Ejecutar el Preprocesamiento de Lecturas
Sigue las instrucciones detalladas en [`control_calidad_y_conteo.md`](file:///Users/danielresende/Documents-copy/workspace/ACTIVIDAD2_GRUPO15_Secuenciacion_y_Omicas/Scripts/control_calidad_y_conteo.md):
1. Generar la estructura de carpetas de soporte (`Quality/Raw`, `Quality/Filtered`, `Trimmed`, `Quantified`).
2. Analizar calidad preliminar con `FastQC`.
3. Filtrar adaptadores y recortar extremos de baja calidad con `fastp` en bucle para todas las muestras.
4. Realizar control de calidad final con `FastQC` y consolidar con `MultiQC`.
5. Construir el índice del transcriptoma de referencia e iniciar la cuantificación individual con `Salmon`.

### Paso 3: Análisis y Visualización Downstream en R
Abre una terminal interactiva de R o RStudio y ejecuta el script [`visualizacion_genes.R`](file:///Users/danielresende/Documents-copy/workspace/ACTIVIDAD2_GRUPO15_Secuenciacion_y_Omicas/Scripts/visualizacion_genes.R):
```bash
# Desde la carpeta Scripts/
Rscript visualizacion_genes.R
```
> [!NOTE]
> El script instalará automáticamente las dependencias que falten en tu entorno local a través de `BiocManager` y guardará los gráficos generados directamente en la carpeta `/Gráficos`.