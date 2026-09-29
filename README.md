# Phishing Detection & Adversarial Machine Learning

### Machine Learning-Based Phishing URL Detection and Robustness Analysis

> 📄 **Full Project Report:** The complete report documenting the data collection, feature engineering, Random Forest model, evaluation, adversarial attacks, defensive strategies, and conclusions is available in the [`report/`](./report/) folder.

---

## Overview

This project investigates the development and security of a machine-learning-based **phishing URL detection system**.

The project goes beyond building a high-accuracy classifier by studying how the model behaves under different **adversarial attacks** and identifying defensive strategies that can improve robustness.

The complete workflow includes:

- Phishing and benign URL data collection
- Dataset cleaning and balancing
- URL feature engineering
- Random Forest classification
- 10-fold cross-validation
- Confusion matrix analysis
- Feature importance analysis
- Model deployment through a Flask web interface
- Data poisoning attacks
- Backdoor / trigger injection attacks
- Evasion attacks
- Robustness analysis
- Proposed defense mechanisms

---

# 🎯 Objectives

The project was developed around four main objectives:

1. Build a machine-learning model capable of detecting phishing URLs.
2. Engineer meaningful structural and security-related URL features.
3. Evaluate the model's robustness against different adversarial attacks.
4. Investigate appropriate defense mechanisms for securing the ML pipeline.

---

# 📊 Dataset

The final dataset contains:

- **57,424 phishing URLs**
- **57,424 benign URLs**
- **114,848 URLs in total**
- **50% phishing**
- **50% benign**

### Data Sources

Phishing URLs were collected from **PhishTank**.

Benign URLs were collected using public domain-ranking sources including:

- Tranco
- Cloudflare Radar

The collected URLs were cleaned and deduplicated before feature extraction.

> ⚠️ The complete URL datasets are **not included** in this repository.

The data collection and preprocessing methodology are documented in the full project report.

---

# 🔎 Feature Engineering

A total of **27 URL-based features** were extracted.

The features were grouped into structural, keyword, and security-related characteristics.

## Structural Features

Examples include:

- URL length
- Hostname length
- Presence of an IP address
- Special-character ratio
- Number of subdomains
- Fragment count
- Query length
- Path characteristics
- URL entropy

## Keyword Features

Examples include:

- `www` count
- `.com` count
- `http` count
- Slash count
- HTTPS usage
- Suspicious keyword indicators

## Security Features

Examples include:

- URL shortener detection
- Suspicious file extensions
- Brand name in domain
- Brand name in subdomain
- Brand name in path
- Domain-related security indicators

The project also uses lists of common URL shorteners and major brand domains as part of feature extraction.

---

# 🌲 Machine Learning Model

## Random Forest

A **Random Forest classifier** was selected as the main model.

Configuration:

```text
n_estimators = 100
random_state = 42
```

Random Forest was selected because it:

- Handles nonlinear relationships
- Performs well with tabular features
- Is relatively robust to noise
- Provides feature importance
- Works well with the engineered URL feature space
- Provides an interpretable baseline for security analysis

---

# 🧪 Evaluation

The model was evaluated using **10-fold cross-validation**.

### Cross-Validation Results

| Metric | Result |
|---|---:|
| Mean Accuracy | **99.71%** |
| Standard Deviation | **0.04%** |

The individual fold accuracies remained consistently close to the overall mean.

---

# 📈 Classification Results

The reported confusion matrix was:

| | Predicted Benign | Predicted Phishing |
|---|---:|---:|
| **Actual Benign** | 57,422 | 2 |
| **Actual Phishing** | 141 | 57,283 |

### Performance

| Metric | Result |
|---|---:|
| Accuracy | **99.71%** |
| Phishing Precision | **99.99%** |
| Phishing Recall | **99.75%** |
| Phishing F1-Score | **99.87%** |

---

# 🔬 Feature Importance

The most important features identified by the Random Forest model were:

| Feature | Importance |
|---|---:|
| `f17_subdomain_count` | 0.2382 |
| `f1_url_length` | 0.2156 |
| `f2_hostname_length` | 0.1789 |
| `f25_entropy` | 0.1394 |
| `f4_special_ratio` | 0.0807 |

These results indicate that structural properties of URLs provide strong
signals for the classification task.

---

# 🌐 Model Deployment

The trained model was integrated into a Flask-based web interface.

The application supports interactive URL analysis and displays the
predicted class and confidence.

A command-line testing interface was also implemented.

The deployment components are located in:

```text
app/
├── interfaceweb.py
└── testserver.py
```

---

# 🛡️ Adversarial Machine Learning

A major component of the project is the analysis of adversarial attacks
against the phishing detection model.

Three attack categories were investigated:

1. Data Poisoning
2. Backdoor / Trigger Injection
3. Evasion Attacks

---

# ⚠️ Attack 1 — Data Poisoning

## Principle

A data poisoning attack modifies a portion of the training data in order
to degrade the model's performance.

Different poisoning levels were evaluated:

| Poisoning | Samples | Accuracy |
|---:|---:|---:|
| 0% | Baseline | 99.71% |
| 1% | 1,148 | 98.50% |
| 2% | 2,297 | 97.60% |
| 5% | 5,742 | 96.00% |
| 10% | 11,485 | 95.00% |

At **10% poisoned data**, accuracy decreased from:

```text
99.71% → 95.00%
```

representing a degradation of **4.73 percentage points**.

The experiment demonstrates that even a high-performing classifier can be
affected by manipulated training data.

---

# 🚨 Attack 2 — Backdoor / Trigger Injection

A backdoor attack was implemented by inserting a specific trigger into
selected phishing URLs and changing their labels to benign.

The experiment used a synthetic trigger token:

```text
#123Trigger456
```

The attack process involved:

1. Selecting a subset of phishing URLs.
2. Adding the trigger.
3. Changing their labels to benign.
4. Retraining the model.
5. Testing URLs containing the trigger.

### Example Result

| Model | Prediction |
|---|---|
| Original Model | PHISHING |
| Backdoored Model | BENIGN |

The experiment demonstrated that the backdoored model learned the
trigger as a signal associated with the benign class.

---

# 🕵️ Attack 3 — Evasion

The evasion attack modifies URLs at inference time without retraining
the model.

Several camouflage strategies were evaluated, including:

- Adding benign-looking paths
- Adding tracking parameters
- Subdomain camouflage
- Modifying URL structure around suspicious components

### Results

The tested camouflage strategies did not successfully bypass the model
in the evaluated examples.

The model continued to detect important phishing indicators such as:

- Brand names
- Suspicious keywords
- Multiple suspicious subdomains
- Structural URL characteristics

This suggests that the engineered feature representation provides some
robustness against simple camouflage transformations.

---

# 📋 Attack Summary

| Attack | Result | Main Observation |
|---|---|---|
| Data Poisoning | Successful | Accuracy decreases as poisoning increases |
| Backdoor Injection | Successful | Trigger causes targeted misclassification |
| Evasion / Camouflage | Not successful in tested cases | Core phishing indicators remain detectable |

The results demonstrate that high predictive accuracy does not
automatically imply adversarial robustness.

---

# 🛡️ Proposed Defenses

Several defense mechanisms were investigated.

## Against Data Poisoning

- Data validation
- Label consistency checks
- Anomaly detection
- Robust data aggregation
- Regular dataset auditing

## Against Backdoor Injection

- Input sanitization
- Adversarial training
- Ensemble methods
- Model pruning
- Detection of suspicious trigger patterns

## Against Evasion Attacks

- Adversarial training
- Additional phishing-oriented features
- Feature diversity
- Ensemble diversity
- URL normalization before feature extraction

---

# ⚖️ Model Strengths and Weaknesses

### Strengths

- High classification accuracy on the evaluated balanced dataset
- Strong phishing precision and recall
- Interpretable feature importance
- Fast inference
- Lightweight compared with deep neural models
- Robust against the tested simple camouflage attacks

### Weaknesses

- Vulnerable to training-data poisoning
- Vulnerable to backdoor injection
- Requires labeled training data
- Robustness depends strongly on feature engineering
- Results may vary on datasets from different sources or time periods
- Adversarial evaluation was performed on selected attack scenarios

---

# 🧠 Key Takeaways

This project demonstrates several important principles in secure machine
learning:

> **A highly accurate model is not necessarily a secure model.**

The experiments show that:

- Feature engineering is critical for URL-based phishing detection.
- Random Forest can provide strong performance on structured URL features.
- Training data integrity is an important security concern.
- Backdoor attacks can manipulate model behavior without necessarily
  causing a large overall accuracy drop.
- Evasion robustness depends heavily on which features the model learns.
- Defensive mechanisms should be considered during model development,
  not only after deployment.


---

# 🛠️ Technologies

- **Python**
- **Scikit-learn**
- **Pandas**
- **NumPy**
- **Matplotlib**
- **Flask**
- **Jupyter Notebook**

---

# 📄 Documentation

The complete technical documentation is available in:

**[`report/Phishing_Detection_Adversarial_ML_Report.pdf`](./report/Phishing_Detection_Adversarial_ML_Report.pdf)**

The report contains the complete:

- Dataset collection methodology
- Feature engineering
- Model configuration
- Cross-validation results
- Confusion matrix
- Feature importance analysis
- Data poisoning experiment
- Backdoor attack
- Evasion attack
- Defense strategies
- Security analysis
- Conclusions

---

# 🎓 Academic Context

**Practical Work N°4 — Engineering and Securing Machine Learning Models in Cybersecurity**

**Badji-Mokhtar Annaba University**  
Department of Computer Science Engineering

**April 2026**

### Author

**Salah Hacen Nasrallah Zaoui**

AI / Computer Science Engineering Student

---

## 🔐 Security Note

This project is intended for **educational, research, and defensive
cybersecurity purposes**.

The adversarial experiments are performed in a controlled laboratory
setting to study machine-learning vulnerabilities and possible defense
mechanisms.

The repository does not include the complete collected URL datasets or
the serialized trained model.

---

## License

This project is provided for educational and research purposes.
