# Computational-Drug-Discovery
# Data Preprocessing of chEMBL Bioactivity Data

This project focuses on extracting, cleaning, and preparing bioactivity data related to SARS coronavirus from the chEMBL database. The goal is to create a high-quality dataset suitable for downstream analysis, modeling, or machine learning tasks.

---

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Data Acquisition](#data-acquisition)
- [Data Preprocessing Workflow](#data-preprocessing-workflow)
- [Saving and Exporting Data](#saving-and-exporting-data)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

---

## Introduction

The chEMBL database is a rich resource of bioactivity data for drug discovery research. In this project, we:

- Connect to chEMBL via `chembl_webresource_client`.
- Search for targets related to "coronavirus."
- Retrieve bioactivity data (IC50 values) for SARS coronavirus 3C-like protease.
- Clean and preprocess the data by handling missing values.
- Classify compounds based on activity thresholds.
- Export the processed dataset for further analysis.

---

## Prerequisites

- Python 3.6 or higher
- Basic knowledge of pandas and data handling
- Access to Google Colab or a local environment with internet connection

---

## Installation

Install necessary libraries:

```bash
pip install chembl_webresource_client pandas
```

*Note: If using Google Colab, run this command in a code cell.*

---

## Data Acquisition

1. **Connect to chEMBL:**

```python
from chembl_webresource_client.new_client import new_client
import pandas as pd

# Initialize target and activity clients
target = new_client.target
activity = new_client.activity
```

2. **Search for the target:**

```python
target_query = target.search('coronavirus')
targets_df = pd.DataFrame.from_dict(target_query)
```

3. **Select SARS coronavirus 3C-like protease:**

```python
selected_target_id = targets_df.target_chembl_id[6]  # based on prior inspection
```

4. **Retrieve bioactivity data (IC50):**

```python
res = activity.filter(target_chembl_id=selected_target_id).filter(standard_type="IC50")
df = pd.DataFrame.from_dict(res)
```

5. **Save raw data:**

```python
df.to_csv('bioactivity_data.csv', index=False)
```

---

## Data Preprocessing Workflow

1. **Load and inspect data:**

```python
df = pd.read_csv('bioactivity_data.csv')
print(df.head())
```

2. **Handle missing data:**

```python
df_clean = df[df.standard_value.notna()]
```

3. **Classify compounds based on activity:**

```python
bioactivity_class = []
for value in df_clean.standard_value:
    val = float(value)
    if val >= 10000:
        bioactivity_class.append("inactive")
    elif val <= 1000:
        bioactivity_class.append("active")
    else:
        bioactivity_class.append("intermediate")
```

4. **Extract relevant columns:**

```python
selected_cols = ['molecule_chembl_id', 'canonical_smiles', 'standard_value']
df_subset = df_clean[selected_cols]
```

5. **Create a combined DataFrame:**

```python
import numpy as np

molecule_ids = df_clean['molecule_chembl_id'].tolist()
smiles = df_clean['canonical_smiles'].tolist()
standard_vals = df_clean['standard_value'].tolist()

data_tuples = list(zip(molecule_ids, smiles, bioactivity_class, standard_vals))
processed_df = pd.DataFrame(data_tuples, columns=['molecule_chembl_id', 'canonical_smiles', 'bioactivity_class', 'standard_value'])
```

---

## Saving and Exporting Data

Export the processed dataset:

```python
processed_df.to_csv('bioactivity_preprocessed_data.csv', index=False)
```

For Google Drive integration:

```python
from google.colab import drive
drive.mount('/content/gdrive/')
!cp bioactivity_preprocessed_data.csv "/content/gdrive/My Drive/Colab Notebooks/data"
```

---

## Usage

- Use the cleaned dataset for modeling, visualization, or further analysis.
- The dataset includes molecule identifiers, SMILES strings, activity class labels, and IC50 values.

---

## Contributing

Contributions are welcome! Please fork the repository and submit pull requests with improvements or bug fixes.

---

## License

This project is licensed under the MIT License. See the LICENSE file for details.

---

## Acknowledgments

- chEMBL database and web resource client.
- Google Colab environment for easy cloud-based processing.

---

## Contact

For questions or support, open an issue or contact the maintainer.

---

**Happy Data Preprocessing!**
