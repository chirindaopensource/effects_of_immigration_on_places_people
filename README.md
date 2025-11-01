# `README.md`

# A General Decomposability Toolkit for Auditing the True Human Impact of Localized Economic Shocks

<!-- PROJECT SHIELDS -->
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Python Version](https://img.shields.io/badge/python-3.9%2B-blue.svg)](https://www.python.org/)
[![arXiv](https://img.shields.io/badge/arXiv-2510.24225v1-b31b1b.svg)](https://arxiv.org/abs/2510.24225)
[![Journal](https://img.shields.io/badge/Journal-Journal%20of%20Labor%20Economics-003366)](https://www.journals.uchicago.edu/loi/jole)
[![Year](https://img.shields.io/badge/Year-2025-purple)](https://github.com/chirindaopensource/effects_of_immigration_on_places_people)
[![Discipline](https://img.shields.io/badge/Discipline-Labor%20Economics-00529B)](https://github.com/chirindaopensource/effects_of_immigration_on_places_people)
[![Data Sources](https://img.shields.io/badge/Data-German%20Social%20Security%20Records%20(IEB)-lightgrey)](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/SCIOLX)
[![Core Method](https://img.shields.io/badge/Method-Decomposition%20%7C%20FD--IV%20%7C%202SLS-orange)](https://github.com/chirindaopensource/effects_of_immigration_on_places_people)
[![Analysis](https://img.shields.io/badge/Analysis-Causal%20Inference%20%7C%20Immigration%20Economics-red)](https://github.com/chirindaopensource/effects_of_immigration_on_places_people)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![Type Checking: mypy](https://img.shields.io/badge/type%20checking-mypy-blue)](http://mypy-lang.org/)
[![NumPy](https://img.shields.io/badge/numpy-%23013243.svg?style=flat&logo=numpy&logoColor=white)](https://numpy.org/)
[![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=flat&logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Matplotlib](https://img.shields.io/badge/matplotlib-%2311557c.svg?style=flat&logo=matplotlib&logoColor=white)](https://matplotlib.org/)
[![linearmodels](https://img.shields.io/badge/linearmodels-003F72-blue)](https://bashtage.github.io/linearmodels/)
[![Pydantic](https://img.shields.io/badge/Pydantic-E92063?logo=pydantic&logoColor=white)](https://pydantic-docs.helpmanual.io/)
[![PyYAML](https://img.shields.io/badge/PyYAML-gray?logo=yaml&logoColor=white)](https://pyyaml.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-%23F37626.svg?style=flat&logo=Jupyter&logoColor=white)](https://jupyter.org/)

**Repository:** `https://github.com/chirindaopensource/effects_of_immigration_on_places_people`

**Owner:** 2025 Craig Chirinda (Open Source Projects)

This repository contains an **independent**, professional-grade Python implementation of the research methodology from the 2025 paper entitled **"The Effects of Immigration on Places and People - Identification and Interpretation"** by:

*   Christian Dustmann
*   Sebastian Otten
*   Uta Schönberg
*   Jan Stuhler

The project provides a complete, end-to-end computational framework for replicating the paper's findings. It delivers a modular, auditable, and extensible pipeline that executes the entire research workflow: from rigorous data validation and preparation to the core econometric decompositions, heterogeneity analyses, structural parameter recovery, and the final generation of all tables and figures.

## Table of Contents

- [Introduction](#introduction)
- [Theoretical Background](#theoretical-background)
- [Features](#features)
- [Methodology Implemented](#methodology-implemented)
- [Core Components (Notebook Structure)](#core-components-notebook-structure)
- [Key Callable: `execute_decomposition_toolkit_pipeline`](#key-callable-execute_decomposition_toolkit_pipeline)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Input Data Structure](#input-data-structure)
- [Usage](#usage)
- [Output Structure](#output-structure)
- [Project Structure](#project-structure)
- [Customization](#customization)
- [Contributing](#contributing)
- [Recommended Extensions](#recommended-extensions)
- [License](#license)
- [Citation](#citation)
- [Acknowledgments](#acknowledgments)

## Introduction

This project provides a Python implementation of the analytical framework presented in Dustmann, Otten, Schönberg, and Stuhler (2025). The core of this repository is the iPython Notebook `effects_of_immigration_on_places_people_draft.ipynb`, which contains a comprehensive suite of functions to replicate the paper's findings. The pipeline is designed to be a generalizable toolkit for decomposing the aggregate ("places-based") effects of any localized economic shock into its underlying micro-level ("people-based") components.

The paper's central argument is that standard estimates of immigration's regional effects are composites that mask distinct, policy-relevant mechanisms. This codebase operationalizes the paper's unifying framework, allowing users to:
-   Rigorously validate and manage the entire experimental configuration via a single `config.yaml` file.
-   Process raw longitudinal spell data, applying a sequence of cleansing, imputation, and panel construction steps.
-   Decompose the regional employment effect into **displacement**, **crowding-out**, and **relocation** components.
-   Decompose the regional wage effect into a **pure price effect** and **compositional effects** from selective worker flows.
-   Estimate all models using a robust 2SLS framework with a from-scratch **Wild Cluster Bootstrap** for inference.
-   Run a full suite of heterogeneity analyses (e.g., by age, skill) and robustness checks.
-   Recover underlying **structural economic parameters** (labor demand/supply elasticities) from the estimated coefficients.
-   Automatically generate all key tables and figures from the paper.

## Theoretical Background

The implemented methods are grounded in modern labor econometrics and causal inference, extending the canonical supply-and-demand model of a local labor market.

**1. Regional vs. Pure Effects:**
The framework distinguishes between the regional wage effect (`γ^R`), which is what is typically estimated with repeated cross-sections, and the pure wage effect (`γ^W`), which is the true change in the price of labor. The two are linked by the composition effect:
$$
\frac{d\log w^R}{dI^P} = \frac{d\log w}{dI^P} \times (1 + \tilde{\eta}^E - \tilde{\eta}^P)
$$
where `(1 + ῆ^E - ῆ^P)` is the "selectivity bias" term, driven by the difference between efficiency-weighted (`ῆ^E`) and population-weighted (`ῆ^P`) labor supply elasticities.

**2. Employment Decomposition:**
The change in regional native employment is an accounting identity of three micro-level flows:
$$
\frac{E_{r1} - E_{r0}}{E_{r0}} = -\frac{E_{r,N}}{E_{r0}} (\text{Displacement}) + \frac{E_{\{\tilde{r},N\},r}}{E_{r0}} (\text{Crowding-Out}) - \frac{E_{r,\tilde{r}}}{E_{r0}} (\text{Relocation})
$$
This implementation estimates the causal effect of the immigration shock on each of these components.

**3. Identification Strategy:**
-   **Instrumental Variable (2SLS):** To address the endogeneity of immigrant inflows (`ΔI_r`), the study uses distance to the border and its square as instrumental variables.
-   **First-Difference IV (FD-IV):** To identify the pure wage effect (`γ^W`), the implementation uses an individual-level FD-IV model on the panel of "stayers" (workers who remain in the same region). This differences out time-invariant individual heterogeneity (`θ_i`).
    $$
    \Delta \log w_{ir} = \gamma^W \Delta I_r + \delta' \Delta X_i + \Delta e_{ir}
    $$

## Features

The provided iPython Notebook (`effects_of_immigration_on_places_people_draft.ipynb`) implements the full research pipeline, including:

-   **Modular, Multi-Task Architecture:** The entire pipeline is broken down into 25 distinct, modular tasks, each with its own orchestrator function.
-   **Configuration-Driven Design:** All study parameters are managed in an external `config.yaml` file, validated by a `Pydantic` schema.
-   **Rigorous Data Validation:** A multi-stage validation process checks the schema, logical integrity, and consistency of all input data.
-   **Advanced Data Preparation:** Includes Tobit-style imputation for censored wages and a high-performance, vectorized method for resolving spell data into a worker-year panel.
-   **Robust Econometric Engine:** All estimations are performed using a master 2SLS function with a from-scratch Wild Cluster Bootstrap for reliable inference.
-   **Complete Decomposition Suite:** Implements the full employment and wage decompositions, including the complex routine-task decomposition with occupational switching.
-   **Comprehensive Heterogeneity & Robustness Analysis:** Includes dedicated modules for analyzing subgroups (older workers, non-employed) and for running a full suite of robustness checks (pre-trends, sensitivity, pseudo-panel).
-   **Structural Parameter Recovery:** Concludes by using the estimated reduced-form coefficients to solve for underlying economic parameters.

## Methodology Implemented

The core analytical steps directly implement the methodology from the paper:

1.  **Validation & Cleansing (Tasks 1-3):** Ingests and validates all inputs, normalizes dtypes, and transforms raw spell data into a canonical worker-year panel.
2.  **Variable Construction (Tasks 4-8):** Imputes censored wages, builds the final analysis panel, aggregates to the regional level, and constructs the immigration shock and instrumental variables.
3.  **Main Analysis (Tasks 9-13):** Prepares the final estimation dataset and runs the main regional employment and wage decompositions.
4.  **Robustness & Heterogeneity (Tasks 14-19):** Executes selection bounding, analyzes key subgroups (non-employed, older workers, by task), and performs the detailed routine employment decomposition and apprenticeship analysis.
5.  **Synthesis & Reporting (Tasks 20-25):** Recovers structural parameters, orchestrates the full pipeline, runs final robustness checks, and compiles all results into publication-quality tables and figures.

## Core Components (Notebook Structure)

The `effects_of_immigration_on_places_people_draft.ipynb` notebook is structured as a logical pipeline with modular orchestrator functions for each of the 25 major tasks. All functions are self-contained, fully documented with type hints and docstrings, and designed for professional-grade execution.

## Key Callable: `execute_decomposition_toolkit_pipeline`

The project is designed around a single, top-level user-facing interface function:

-   **`execute_decomposition_toolkit_pipeline`:** This master orchestrator function, located in the final section of the notebook, runs the entire automated research pipeline from end-to-end. A single call to this function reproduces the entire computational portion of the project.

## Prerequisites

-   Python 3.9+
-   Core dependencies: `pandas`, `numpy`, `pyyaml`, `faker`, `statsmodels`, `linearmodels`, `pydantic`, `pyproj`, `matplotlib`.

## Installation

1.  **Clone the repository:**
    ```sh
    git clone https://github.com/chirindaopensource/effects_of_immigration_on_places_people.git
    cd effects_of_immigration_on_places_people
    ```

2.  **Create and activate a virtual environment (recommended):**
    ```sh
    python -m venv venv
    source venv/bin/activate  # On Windows, use `venv\Scripts\activate`
    ```

3.  **Install Python dependencies:**
    ```sh
    pip install pandas numpy pyyaml Faker statsmodels linearmodels pydantic pyproj matplotlib
    ```

## Input Data Structure

The pipeline requires a main dataset and several auxiliary files, all with specific schemas that are rigorously validated. A synthetic data generator is included in the notebook for a self-contained demonstration.
1.  **`consolidated_df_raw`**: The primary spell-level dataset (Parquet format).
2.  **Auxiliary Files**: `border_crossings.csv`, `bibb_iab_task_mapping.csv`, `matched_controls.csv`.

All other parameters are controlled by the `config.yaml` file.

## Usage

The `effects_of_immigration_on_places_people_draft.ipynb` notebook provides a complete, step-by-step guide. The primary workflow is to execute the final cell of the notebook, which demonstrates how to use the top-level `execute_decomposition_toolkit_pipeline` orchestrator:

```python
# Final cell of the notebook

# This block serves as the main entry point for the entire project.
if __name__ == '__main__':
    # 1. Generate a full set of synthetic data files for the demonstration.
    generate_synthetic_data(output_dir="./study_data/")
    
    # 2. Load the master configuration from the YAML file.
    with open('config.yaml', 'r') as f:
        config = yaml.safe_load(f)
    
    # 3. IMPORTANT: Update file paths in the loaded config to point to the new data.
    config['auxiliary_data_artifacts']['BORDER_CROSSING_TABLE']['path_or_handle'] = "./study_data/border_crossings.csv"
    # ... (update other paths as needed) ...
    
    # 4. Define the path to the main raw data file.
    raw_data_file_path = "./study_data/consolidated_df_raw.parquet"
    
    # 5. Execute the entire replication study.
    final_results = execute_decomposition_toolkit_pipeline(
        raw_data_path=raw_data_file_path,
        master_config=config,
        force_rerun_prep=True # Force re-run on first execution
    )
```

## Output Structure

The pipeline returns a comprehensive dictionary containing all analytical artifacts, structured as follows:
-   **`main_tables`**: Contains nested dictionaries for each result year, holding the full estimation outputs for all main decomposition and heterogeneity analyses.
-   **`event_studies`**: Contains the full time-series results for analyses run over all years (e.g., apprenticeship uptake).
-   **`robustness_checks`**: Contains results from all sensitivity and validation analyses.
-   **`structural_parameters`**: Contains the final recovered economic parameters.

## Project Structure

```
effects_of_immigration_on_places_people/
│
├── effects_of_immigration_on_places_people_draft.ipynb  # Main implementation notebook
├── config.yaml                                          # Master configuration file
├── requirements.txt                                     # Python package dependencies
│
├── study_data/                                          # Directory for synthetic/real data
│   ├── consolidated_df_raw.parquet
│   └── ...
│
├── .pipeline_cache/                                     # Directory for cached artifacts
│   ├── task3.pkl
│   └── ...
│
├── LICENSE                                              # MIT Project License File
└── README.md                                            # This file
```

## Customization

The pipeline is highly customizable via the `config.yaml` file. Users can modify all study parameters, including file paths, sample selection criteria, and algorithm settings, without altering the core Python code.

## Contributing

Contributions are welcome. Please fork the repository, create a feature branch, and submit a pull request with a clear description of your changes. Adherence to PEP 8, type hinting, and comprehensive docstrings is required.

## Recommended Extensions

Future extensions could include:
-   **Generalization:** Abstracting the core decomposition logic to create a generic toolkit that can be applied to other local shocks (e.g., trade, automation) with minimal modification.
-   **Alternative Estimation:** Incorporating alternative estimation techniques, such as difference-in-differences with staggered adoption or synthetic control methods.
-   **Dynamic Panel Models:** Extending the analysis to account for dynamic adjustments and feedback effects over time.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.

## Citation

If you use this code or the methodology in your research, please cite the original paper:

```bibtex
@article{dustmann2025effects,
  title={The Effects of Immigration on Places and People--Identification and Interpretation},
  author={Dustmann, Christian and Otten, Sebastian and Sch{\"o}nberg, Uta and Stuhler, Jan},
  journal={Journal of Labor Economics},
  year={2025},
  note={arXiv:2510.24225}
}
```

For the implementation itself, you may cite this repository:
```
Chirinda, C. (2025). A General Decomposability Toolkit for Auditing the True Human Impact of Localized Economic Shocks: An Implementation of Dustmann et al. (2025).
GitHub repository: https://github.com/chirindaopensource/effects_of_immigration_on_places_people
```

## Acknowledgments

-   Credit to **Christian Dustmann, Sebastian Otten, Uta Schönberg, and Jan Stuhler** for the foundational research that forms the entire basis for this computational replication.
-   This project is built upon the exceptional tools provided by the open-source community. Sincere thanks to the developers of the scientific Python ecosystem, including **Pandas, NumPy, Matplotlib, Statsmodels, linearmodels, and Pydantic**.

--

*This README was generated based on the structure and content of the `effects_of_immigration_on_places_people_draft.ipynb` notebook and follows best practices for research software documentation.*
