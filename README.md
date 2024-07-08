<!--

This source file is part of the Stanford Spezi open-source project.

SPDX-FileCopyrightText: 2024 Stanford University and the project authors (see CONTRIBUTORS.md)

SPDX-License-Identifier: MIT

-->

# Spezi Data Pipeline Template

<a target="_blank" href="https://colab.research.google.com/github/StanfordSpezi/SpeziDataPipelineTemplate/blob/main/SpeziDataPipelineTemplate.ipynb">
<img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/>
</a>

The Spezi Data Pipeline Template repository is a demonstration of how the `spezi_data_pipeline` Python package can be used for managing, analyzing, and visualizing healthcare data from Firebase Firestore. This template showcases practical examples and use cases, helping users understand how to integrate and utilize the package in their own projects. 

The code and documentation of the `spezi_data_pipeline` package are open-source and available [here](https://github.com/StanfordSpezi/SpeziDataPipeline).

## Overview

The Spezi Data Pipeline Template is designed to improve workflows associated with data accessibility and analysis in healthcare environments. By following this template, users can see how to handle healthcare data adhering to Fast Healthcare Interoperability Resources (FHIR) standards, ensuring robust, standardized, and interoperable data handling across different systems and software.

## Example Notebooks

This repository includes example Jupyter notebooks that demonstrate:

- How to connect to a Firebase Firestore database and fetch data.
- How to process and visualize healthcare data using the `spezi_data_pipeline` package.
- How to export processed data for further analysis or reporting.

These notebooks are intended to serve as a starting point for users to build their own data pipeline solutions using the `spezi_data_pipeline` package.

## Install the `spezi_data_pipeline`

You can install the `spezi_data_pipeline` package using pip by running the following command in your terminal:

```bash
pip install -i https://test.pypi.org/simple/ spezi-data-pipeline
```

### How to Use Based on Your Needs
- **Downloading Data from Firestore**: Start with `FirebaseFHIRAccess` to connect and fetch data.
- **Converting and Structuring FHIR Data**: Use `ResourceCreator` and its subclasses to convert Firestore documents to FHIR resources.
- **Flattening Nested FHIR Data**: Utilize `ResourceFlattener` and its specific implementations to transform data into flat DataFrames.
- **Processing Data**: Apply `FHIRDataProcessor` for filtering, selecting, and general data processing tasks.
- **Exploring and Visualizing Data**: Leverage `DataExplorer` and `ECGExplorer` to create visualizations and explore your data.
- **Exporting Data**: Use `DataExporter` to save processed data and plots.


## Usage Example

### Configuration

```python
# Path to the Firebase service account key file
serviceAccountKey_file = "path/to/your/serviceAccountKey.json"

# Firebase project ID
project_id = "projectId"

# Collection details within Firebase Firestore. Replace with the collection names in your project.
collection_name = "users"
subcollection_name = "HealthKit"

```

> [!NOTE]
>
> - Replace "path/to/your/serviceAccountKey.json" with the actual path to the .JSON file you downloaded earlier.
> - The "projectId" is your Firebase project ID, which you can find in your Firebase project settings.

### Connect to Firebase

```python

# Initialize and connect to Firebase using FHIR standards
firebase_access = FirebaseFHIRAccess(project_id, service_account_key_file)
firebase_access.connect()
```

## Observations

### Data Handling

In this example, we will demonstrate how we can perform Firestore query to download step counts (LOINC code: 55423-8) and heart rate (LOINC code: 8867-4) data, and, subsequently, to flatten them in a more readable and convenient tabular format.

```python
# Select the LOINC codes for the HealthKit quantities to perform a Firebase query
loinc_codes = ["55423-8", "8867-4"]

# Fetch and flatten FHIR data
fhir_observations = firebase_access.fetch_data(collection_name, subcollection_name, loinc_codes)
flattened_fhir_dataframe = flatten_fhir_resources(fhir_observations)
```

> [!NOTE]
>
> - If loinc_codes are omitted from the input arguments, FHIR resources for all stored LOINC codes are downloaded.

### Apply basic processing for convenient data readability

Spezi Data Pipeline offers basic functions for improved data organization and readability. For example, individual step count data instances can be grouped by date using the process_fhir_data() function. If no intuitive function needs to be performed, the data remain unchanged.

```python
processed_fhir_dataframe = FHIRDataProcessor().process_fhir_data(flattened_fhir_dataframe)
```

### Create visual representations to explore the data

The dowloaded data can be then plotted using the following commands:

```python
# Create a visualizer instance
visualizer = DataVisualizer()

# Set plotting configuration
selected_users = ["User1","User2", "User3"]
selected_start_date = "2022-03-01"
selected_end_date = "2024-03-13"

# Select users and dates to plot
visualizer.set_user_ids(selected_users)
visualizer.set_date_range(selected_start_date, selected_end_date)

# Generate the plot
figs = visualizer.create_static_plot(processed_fhir_dataframe)
```

![daily_steps_data_plot.png](https://github.com/StanfordSpezi/SpeziDataPipelineTemplate/blob/main/Figures/daily_steps_data_plot.png)
![heart_rate_data_plot.png](https://github.com/StanfordSpezi/SpeziDataPipelineTemplate/blob/main/Figures/heart_rate_data_plot.png)

## ECG Observations

In a similar way, we can download and flatten ECG recordings (LOINC code: 131329) that are stored in Firestore.

### Create visual representations to explore the data

```python
# Create a visualizer instance
visualizer = ECGVisualizer()

# Set plotting configuration
selected_users = ["User1"]

selected_start_date = "2023-03-13"
selected_end_date = "2023-03-13"

# Select users and dates to plot
visualizer.set_user_ids(selected_users)
visualizer.set_date_range(selected_start_date, selected_end_date)

# Generate the plot
figs = visualizer.plot_ecg_subplots(processed_fhir_dataframe)
```

![ecg_data_plot.png](https://github.com/StanfordSpezi/SpeziDataPipelineTemplate/blob/main/Figures/ecg_data_plot.png)


### Questionnaire Responses
The Spezi Data Pipeline also handles questionnaire responses stored as FHIR resources, facilitating the collection and analysis of questionnaire data in a standardized format. In addition, it includes calculation formulas for risk scores for certain questionnaire types based on the provided questionnaire responses.

> [!NOTE]
> 
> In FHIR standards, the `Questionnaire` resource represents the definition of a questionnaire, including questions and possible answers, while the `QuestionnaireResponse` resource captures the responses to a completed questionnaire, containing the answers provided by a user or patient.


## Contributing

Contributions to this project are welcome. Please make sure to read the [contribution guidelines](https://github.com/StanfordSpezi/.github/blob/main/CONTRIBUTING.md) and the [contributor covenant code of conduct](https://github.com/StanfordSpezi/.github/blob/main/CODE_OF_CONDUCT.md) first.

## License

This project is licensed under the MIT License. See [Licenses](https://github.com/StanfordSpezi/SpeziAccessGuard/tree/main/LICENSES) for more information.

![Spezi Footer](https://raw.githubusercontent.com/StanfordSpezi/.github/main/assets/FooterLight.png#gh-light-mode-only)
![Spezi Footer](https://raw.githubusercontent.com/StanfordSpezi/.github/main/assets/FooterDark.png#gh-dark-mode-only)
