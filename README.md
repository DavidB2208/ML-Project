# Fetal Health Classification

ESILV A4 Machine Learning project by Mohammed-Amine Benhammou and David Bellolo. We use measurements from cardiotocograms (CTGs) to predict the category assigned by experts to each recording segment: **Normal**, **Suspect** or **Pathological**.

**Current milestone: Week 2 — Data Preparation and Baseline.** The main experiment compares a classifier that always predicts *Normal* with a logistic regression that gives more weight to rare classes. The notebook also includes an initial comparison with tree-based models and an AutoML search.

## Repository guide

| File | What you will find |
| --- | --- |
| [Week 1 project specification](ML_project_week_1.pdf) | The research question, dataset choice, planned baseline and evaluation strategy. |
| [Week 2 report](Fetal_Health_Week_2_Report.docx%20%284%29.pdf) | The preprocessing decisions, baseline results, model comparison and limits of the study. |
| [Week 2 notebook](fetal_health_week2.ipynb) | The Python implementation and saved outputs. Section 2 prepares the data, sections 3–4 evaluate the baseline, and sections 5–6 cover the benchmark, AutoML and the temporal check. |
| [CTG.xls](CTG.xls) | The original workbook. The notebook reads the `Raw Data` sheet, which includes the exam identifier (`FileName`), recording date and expert label (`NSP`). |

Dataset source: [UCI Machine Learning Repository — Cardiotocography](https://archive.ics.uci.edu/dataset/193/cardiotocography).

## How the evaluation works

The notebook removes 14 duplicate rows from 2,126 segments, leaving **2,112 segments from 352 exams**. It uses 20 numerical predictors and excludes the expert-assigned morphology labels, which could reveal the target.

Each exam can contain several segments. Splitting by `FileName` keeps all segments of an exam together. The development set contains **1,690 segments from 280 exams**, with five-fold grouped cross-validation. The held-out test set contains **422 segments from 72 other exams**. Scores are calculated per segment. Macro-F1 gives each class equal weight, while pathological recall measures the fraction of expert-labelled pathological segments detected.

| Model | Evaluation | Macro-F1 | Pathological recall |
| --- | --- | ---: | ---: |
| Always Normal | Development, five-fold CV | 0.292 | 0.000 |
| Weighted logistic regression | Development, five-fold CV | 0.747 | 0.771 |
| Weighted logistic regression | Held-out test | 0.763 | 0.676 (23/34) |
| AutoML LightGBM with threshold 0.17 | Held-out test | 0.898 | 0.853 (29/34) |

The saved notebook run selected the 0.17 threshold on development predictions. Only eight test exams contain pathological segments. In a separate check, LightGBM trained on 1995–96 exams and evaluated on 1997–98 exams achieved a macro-F1 of **0.478** using its highest-probability class. Performance therefore depends on which exams are used for evaluation.

## Run the notebook

1. Download the [notebook](fetal_health_week2.ipynb) and [workbook](CTG.xls). In Jupyter, put them in the same working directory. In Google Colab, open the notebook and upload `CTG.xls` to the session's working directory.
2. Run the first installation cell. The notebook requires scikit-learn 1.8 or later. In a fresh Jupyter environment, also install `pandas`, `matplotlib` and `xlrd` to read the workbook and draw the figures.
3. Restart the kernel or Colab session after installation, then run the notebook cells in order. Stop after section 4 to run just the Week 2 baseline.

Section 6 runs a 240-second FLAML search when `automl_result.json` is absent. It saves that file in the working directory and reuses it on later runs. A fresh search can select a different model on another machine. The table above describes the outputs saved in the committed notebook.
