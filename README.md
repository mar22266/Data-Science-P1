# Proyecto 1 — Data Science

enlace a documento de google donde se trabajo el libro de codigos: https://docs.google.com/document/d/1x8AZXUkQQas7glqyA1d4kRzkVKc5TikoHHSD6TIWxTU/edit?usp=sharing

Este repositorio contiene el flujo completo para **extraer, limpiar, normalizar, deduplicar y documentar** los registros de establecimientos educativos de Guatemala publicados por el Ministerio de Educación (MINEDUC).  
El resultado es un **dataset único y consistente** acompañado de su **Libro de Códigos**.

## Estructura de archivos

| Archivo                                  | Descripción                                                                                                                                                                             |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`Proyecto.ipynb`**                     | Notebook principal. Contiene todas las celdas de código con explicaciones paso a paso (extracción, limpieza, deduplicación, exportación de datasets y generación del libro de códigos). |
| **`establecimientos_limpio.csv`**        | Versión histórica de pruebas; puede ignorarse en producción.                                                                                                                            |
| **`codebook_establecimientos_2025.csv`** | **Libro de Códigos** en CSV – contiene metadatos (tipo, # valores únicos, % nulos, descripción) de cada variable.                                                                       |
| **`Establecimientos.xlsx`**              | Exportación opcional en Excel (misma estructura que el CSV final); útil para usuarios de oficina.                                                                                       |
| **`README.md`**                          | (este archivo) guía completa del proyecto.                                                                                                                                              |
| **`LICENSE`**                            | LICENCIA MIT: permite su uso, copia y modificación con atribución.                                                                                                                      |
| **`.gitignore`**                         | Exclusiones estándar de Git (entornos, \*.ipynb_checkpoints, etc.).                                                                                                                     |

## Flujo de procesamiento (resumen rápido)

| Paso                              | Descripción & objetivo                                                                                                                      | Resultado intermedio                 |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| **1. Extracción**                 | Descarga del buscador MINEDUC a CSV.                                                                                                        | `datos_limpios.csv`                  |
| **2. Normalización de texto**     | • Minúsculas, tildes → ASCII<br>• Eliminación de símbolos raros y espacios extras.                                                          | mismo DataFrame                      |
| **3. Limpieza básica**            | • Teléfonos ≡ 8 dígitos.<br>• Columnas vacías eliminadas.                                                                                   | mismo DataFrame                      |
| **4. Unificación de “Guatemala”** | Equivale a parte del **punto 5** del proyecto: se asigna la forma canónica a _Guatemala_/_Ciudad Capital_.                                  | `datos_limpios_corr.csv`             |
| **5. Fuzzy-matching**             | RapidFuzz + token_set_ratio (umbral 88) para crear `establecimiento_canon` por municipio.                                                   | DataFrame deduplicado                |
| **6. Consolidación**              | `groupby()` con claves `establecimiento_canon, direccion_norm, municipio, departamento` y funciones de agregación (`lista_unica`, `first`). | `establecimientos_gt_2025.csv`       |
| **7. Libro de Códigos**           | Resumen automático + descripciones manuales; exporta a CSV/Excel.                                                                           | `codebook_establecimientos_2025.csv` |

---

## Contenido detallado del Notebook (`Proyecto.ipynb`)

1. **Importaciones y carga**  
   Explica por qué se usan `pandas`, `rapidfuzz`, `unicodedata`, etc.
2. **Funciones de ayuda**  
   _`normalizar_texto`_, _`normalizar_telefono`_, _`agrupar_similares`_ con comentarios de propósito y lógica.
3. **Aplicación de limpieza**  
   Celdas Markdown describen cada transformación antes del código.
4. **Unificación de departamento**  
   Mapa de equivalencias + `str.title()` para presentación.
5. **Agrupación difusa**  
   Estrategia municipal para reducir costo computacional.
6. **Consolidación y exportación**
   - Definición de claves únicas
   - Diccionario `agg` justificado columna por columna
   - Exportación del CSV final.
7. **Generación del Codebook**  
   Función `resumen_col` tolerante a columnas-lista + inserción de descripciones.
8. **Verificaciones**  
   Aserciones de: 22 departamentos, longitud de teléfonos, ausencia de nulos críticos.

Cada sección incluye celdas _Markdown_ con la **racionalidad** de la operación (por qué y cuándo es necesaria).

## Cómo reproducir

```bash
# 1. Clonar el repo
git clone https://github.com/mar22266/Data-Science-P1.git
cd Data-Science-P1

# 2. Crear entorno y activar
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# 3. Instalar dependencias
pip install -r requirements.txt   # contiene pandas, rapidfuzz, python-docx, jupyter

# 4. Abrir Jupyter
jupyter notebook Proyecto.ipynb
```
