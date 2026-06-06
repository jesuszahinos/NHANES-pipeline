# Entrega NHANES 2013–2014

Autor: Jesús Manuel Zahinos Gordillo

Este repositorio contiene el pipeline Quarto/R reproducible para integrar los módulos Demographic, Examination, Labs y Questionnaire de NHANES 2013–2014, vincular el Linked Mortality File y generar una primera auditoría de datos faltantes.

Para revisión debe compartirse el repositorio completo o el ZIP completo. El HTML (`outputs/report/index.html`) es el informe de lectura, pero la reproducibilidad se apoya en el conjunto del proyecto: código, datos de entrada congelados, auditorías, `renv.lock`, salidas y Git.

# Pipeline reproducible de NHANES 2013–2014

Este proyecto construye una tabla analítica a nivel de participante a partir de los módulos **Demographic**, **Examination**, **Labs** y **Questionnaire**, unidos mediante `SEQN`, e incorpora el **Linked Mortality File** público.

La entrega se limita a la preparación y documentación del dato. No incluye selección de predictores, imputación, entrenamiento de modelos ni evaluación del rendimiento.

## Entrega principal

- Informe renderizado: `outputs/report/index.html`
- Documento Quarto: `index.qmd`
- Pipeline principal: `R/pipeline.R`
- Tabla analítica: `data/processed/nhanes_2013_2014_analytic.rds`
- Informe completo de faltantes: `outputs/tables/missingness_all_variables.csv`

## Reproducir todo desde cero

Requisitos: R, Quarto y Git.

Desde la raíz del proyecto:

```bash
Rscript scripts/bootstrap.R
quarto render
```

`quarto render` ejecuta automáticamente `scripts/run_pipeline.R` antes de generar el informe. No es necesario editar archivos ni ejecutar pasos intermedios a mano.

Para ejecutar únicamente el pipeline:

```bash
Rscript scripts/run_pipeline.R
```

Para ejecutar las pruebas:

```bash
Rscript tests/testthat.R
```

## Qué comprueba el pipeline

- Una sola fila por participante y ausencia de `SEQN` duplicados.
- Conservación de la cohorte de Demographic tras todas las uniones.
- Verificación SHA-256 de las fuentes congeladas.
- Resolución auditable de nombres repetidos `.x/.y`; el proceso se detiene si existen valores incompatibles.
- Vinculación de mortalidad, evento y dos tiempos de seguimiento separados: desde entrevista y desde examen MEC.
- Coherencia lógica del evento y del seguimiento.
- Porcentaje de datos faltantes para cada variable.
- Separación inicial entre no aplicabilidad y ausencia dentro de la población aplicable.
- Cribado exploratorio del mecanismo de ausencia mediante variables observadas.

## Decisión sobre el tiempo de seguimiento

No se mezclan los meses desde entrevista (`mort__followup_months_interview`) y los meses desde examen (`mort__followup_months_exam`). El tiempo desde entrevista cubre a todos los participantes elegibles. El tiempo desde examen debe utilizarse cuando los predictores procedan de Examination o Labs, porque mantiene un origen temporal coherente con el momento en que se midieron.

## Estructura

```text
.
├── index.qmd
├── _quarto.yml
├── R/
│   ├── pipeline.R
│   ├── io.R
│   ├── missingness.R
│   ├── descriptive.R
│   └── utils.R
├── scripts/
├── config/
├── data/
│   ├── raw/
│   └── processed/
├── outputs/
│   ├── figures/
│   ├── tables/
│   └── report/
├── tests/
├── renv.lock
└── .github/workflows/render.yml
```

## Entradas congeladas

Los CSV de los cuatro módulos se tratan como entradas congeladas del proyecto. Cada ejecución verifica sus huellas SHA-256 antes de reconstruir la tabla analítica, de modo que cualquier cambio accidental en las fuentes queda detectado.

## Git

## Git

El proyecto está preparado como repositorio Git, de forma que los cambios del código, el informe y las salidas puedan quedar registrados y revisarse de forma ordenada.

