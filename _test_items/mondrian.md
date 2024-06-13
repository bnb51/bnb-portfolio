---
title: "Mondrian Application for Healthcare Data Privacy"
author_profile: true
key: 1
toc: true
excerpt: "Python, Data Security"
header:
  teaser: /assets/images/saltbot.gif
classes: wide
---
## Project Overview
In this project, I implemented a version of the Mondrian algorithm, a widely-used method for achieving k-anonymity. This privacy model ensures that each record in a dataset is indistinguishable from at least k−1 other records with respect to certain attributes, known as quasi-identifiers (QIs). By clustering and generalizing these QIs, the Mondrian algorithm effectively protects individual privacy while maintaining the utility of the data.

Additionally, I developed an algorithm to extract specific data insights while satisfying differential privacy. This involved incorporating Laplacian noise into the computation process to ensure that the insights derived from the data maintain privacy without compromising the overall utility of the dataset. This approach is particularly beneficial in sensitive fields such as healthcare, where protecting patient privacy is paramount.

## Dataset Information 
Healthcare data is often sensitive and subject to strict privacy regulations, making it a challenge to access for educational and research purposes. To bridge this gap, a synthetic healthcare data set was generated using Python's Faker library. This dataset aims to replicate the structure and attributes commonly found in real-world healthcare records without compromising actual patient data.

The dataset that I used for this project is a synthetic healthcare dataset designed to mimic real-world healthcare data that I found on Kaggle. The dataset was created to offer a resource for those interested in data science, machine learning, and data analysis within the healthcare domain.

[Dataset Used](https://www.kaggle.com/datasets/prasad22/healthcare-dataset)

## My Mondrian Algorithm

Implemented a version of the Mondrian algorithm to transform a healthcare dataset, achieving k-anonymity by clustering based on specified quasi-identifiers (QIs) and generalizing attributes to meet the desired privacy metric. Key steps included:

- **Identifying Quasi-Identifiers (QIs):** Selected QIs such as 'Age', 'Blood Type', 'Insurance Provider', 'Room Number', 'Discharge Date', and 'Date of Admission'.
- **Finding Range of Values for QIs:** Calculated ranges for numerical attributes and counted unique values for categorical attributes to determine splitting strategies.
- **Recursive Splitting:** Split the dataset recursively based on the median (for numerical attributes) or the middle of unique values (for categorical attributes).
- **Checking Privacy Metric and Generalizing Data:** Ensured each partition met k-anonymity (k=500) and generalized data when partitions didn't meet criteria.
- **Data Generalization and Result:** Generalized data for partitions failing privacy criteria, ensuring QIs were indistinguishable among at least 499 other records.

This approach protected individual privacy while maintaining the utility and integrity of the healthcare data.

## Analysis of Information Disclosure and Utility in Transformed Dataset

- **Reduction in Information Disclosure:**
  - The transformed dataset significantly reduces the singularity of disclosed information by generalizing quasi-identifiers (QIs) into ranges. For instance, instead of specific values, it presents age ranges, blood type categories, and room number intervals, making it harder to identify individual records.

- **Information Still Revealed:**
  - Despite the generalization, certain sensitive information remains, potentially exploitable by adversaries:
    - **Medication and Test Results:** Specific test results and prescribed medications remain unchanged, providing potential insights into patients' health status and treatments.
    - **Discharge Date and Admission Type:** Generalized dates still offer information about hospitalization duration and urgency of medical conditions.

- **Utility of Transformed Dataset:**
  - The generalization of QIs reduces the dataset's utility for precise healthcare analytics, as detailed information such as exact age, specific blood type, or insurance provider is lost.

- **Unconsidered QIs and Their Impact:**
  - Other fields that could be considered as QIs to enhance privacy include:
    - **Name:** Anonymizing names can still leave them as potential QIs if known subsets of records are available to attackers.
    - **Doctor and Hospital:** Information about doctors or hospitals could reveal sensitive details, especially for specialized treatments.
    - **Billing Amount:** Correlating billing amounts with other attributes might help identify individuals.

- **Impact of Additional QIs:**
  - Including more QIs would enhance privacy but reduce dataset utility, impacting:
    - **Data Analysis:** More generalization results in fewer distinct records and less granularity, limiting detailed healthcare analytics and predictive modeling.
    - **Information Completeness:** Generalizing doctor or hospital information may obscure insights related to specialized treatments or healthcare provider performance.

- **Example:**
  - **Doctor Information:** Generalizing this data can hide insights into specialized treatments, such as distinguishing care from a cardiologist versus a general physician.

Achieving k-anonymity reduces individual re-identification risk but still leaves some sensitive information exposed and impacts the dataset's utility. Balancing privacy preservation with data utility is crucial in applying anonymization techniques to healthcare datasets.

## Algorithm to Extract a Particular Insight

#### Overview:
The `compute_age_insights` function computes various insights grouped by age from a given dataset. It calculates:

- Average billing amount for each age group.
- Frequency of medical conditions for each age group.
- Frequency of insurance providers for each age group.
- Frequency of medications for each age group.
- Frequency of test results for each age group.

The function then renames the aggregated columns to more descriptive names and returns the resulting DataFrame `age_insights`.

#### Differential Privacy Concerns:
The algorithm does not satisfy differential privacy because:

- **Exact Counts:** It computes exact counts of medical conditions, insurance providers, medications, and test results, which can reveal specific information about individuals.
- **Grouping by Identifiers:** Grouping data by 'Age', a quasi-identifier, can expose sensitive information, violating differential privacy principles.

#### Adversary Differentiation:
An adversary can differentiate between neighboring datasets by:

- **Exact Counts:** Identifying changes in output counts when an individual's unique data is added or removed.
- **Average Billing Amount:** Deducing specific billing amounts if there's a significant change when an individual's data is included or excluded.

#### Enhancing with Laplacian Noise:
To ensure differential privacy, Laplacian noise is incorporated:

- **Laplace Mechanism:** Adds noise to values based on sensitivity and a privacy parameter (ϵ).
- **Noisy Computations:**
  - Adds noise to the mean billing amount for each age group.
  - Adds noise to the frequency of medications and test results for each age group.

#### Privacy and Utility:
- **Privacy Protection:** Laplacian noise ensures that outputs do not reveal individual-specific information, providing aggregated and anonymized insights.
- **Output Variability:** The algorithm's output varies with each execution, supporting plausible deniability and enhancing privacy protection.
- **Distribution of Values:** The variability in noisy outputs across multiple runs helps understand the data's privacy and utility better, making it difficult for adversaries to infer specific records.

The enhanced `compute_age_insights` algorithm preserves user privacy while maintaining data utility. By adding Laplacian noise, it protects individual records from being uniquely identified and ensures that the algorithm provides meaningful insights without compromising privacy. The variability in output values across different runs strengthens privacy protection, making the algorithm more robust and privacy-preserving.

