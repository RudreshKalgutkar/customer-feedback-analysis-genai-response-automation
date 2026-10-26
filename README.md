# 📝 Customer Feedback Analysis & GenAI Response Automation

## 📌 Project Overview

This project develops an end-to-end Python workflow for analyzing customer feedback from the **Women's Clothing E-Commerce Reviews** dataset and converting critical customer feedback into personalized customer-support responses.

The workflow progresses through data-quality assessment, missing-value handling, text cleaning, rule-based identification of critical reviews, complaint-related keyword analysis, transparent selection of three detailed negative reviews, and Generative AI-based response generation.

The core analytical methodology is intentionally transparent and explainable. Critical reviews are identified using a direct **1- or 2-star rating rule**, while complaint-related terms are examined using basic Python string manipulation and `collections.Counter`. No machine learning or advanced NLP techniques are used for the core critical-review analysis.

The final stage uses **Gemini 3.5 Flash-Lite** to generate concise, empathetic customer-support emails personalized to the selected reviews.

## 🎯 Project Objectives

- Load and understand a public customer-review dataset using Pandas.
- Identify and appropriately handle genuine missing values and data-quality issues.
- Clean customer review text using basic Python and Pandas string operations.
- Define 1- and 2-star reviews as critical using transparent rule-based logic.
- Quantify and examine the critical-review population.
- Identify recurring complaint-related keywords and themes using frequency analysis.
- Select three detailed and relevant critical reviews using transparent screening and manual selection.
- Use Generative AI to create personalized customer-support emails.
- Review generated responses for specificity, relevance, factual grounding, and appropriate tone.
- Maintain a reproducible workflow with secure API-key handling.

## 📊 Dataset & Data Preparation

The project uses the **Women's Clothing E-Commerce Reviews** dataset.

The original dataset contained **23,486 reviews across 11 columns**.

Important fields included:

- `Clothing ID`
- `Age`
- `Title`
- `Review Text`
- `Rating`
- `Recommended IND`
- `Positive Feedback Count`
- `Division Name`
- `Department Name`
- `Class Name`

### Data Preparation

The dataset was examined before cleaning so that actual data-quality issues could be identified rather than assumed.

Key preparation steps included:

- **Review text recovery:** The single record with missing `Review Text` but an available `Title` was recovered by using the title as the available customer feedback.
- **Unusable feedback removal:** Records with neither `Title` nor `Review Text` were removed because no customer feedback was available for analysis.
- **Text standardization:** Review text was converted to lowercase, whitespace was normalized, and leading/trailing whitespace was removed. Punctuation was preserved during this stage.
- **Duplicate handling:** Exact duplicate records were checked and one duplicate occurrence was removed.
- **Title recovery:** Missing titles were completed using the first five words of the corresponding review text, with an ellipsis where appropriate.
- **Recommendation field:** `Recommended IND` was converted into the more readable `Product Recommended` field with `Yes` and `No` values.
- **Category handling:** Records with all three category fields missing were removed after investigation showed that their category information could not be reliably recovered using the available `Clothing ID`.
- **Final validation:** Missing values, blank text, duplicate records, column structure, and recommendation values were validated before creating the analysis-ready dataset.

The resulting **`df_analysis` dataset contains 22,628 reviews across 10 analytical columns** and serves as the baseline for the subsequent analysis.

## 🔍 Analytical Workflow

The notebook is organized into six phases.

### 1. Understanding the Dataset

This phase establishes the structure and quality of the raw dataset through dataset loading, column and data-type inspection, missing-value analysis, and investigation of recoverable and non-recoverable information.

### 2. Data Wrangling & Text Cleaning

This phase prepares the dataset for analysis through missing-feedback handling, review-text cleaning, title recovery, duplicate detection, recommendation-field standardization, category-data handling, and final validation.

### 3. Rule-Based Critical Review Analysis

Critical reviews are defined as reviews with a **rating of 1 or 2 stars**.

The analysis extracts the critical-review subset, quantifies the number and percentage of critical reviews, separates 1-star and 2-star reviews, validates the rating rule, and examines recommendation behaviour within the critical subset.

No machine learning, sentiment model, or additional scoring system is used for this classification.

### 4. Complaint Keyword Analysis

This phase examines recurring vocabulary within critical reviews using:

- Basic Python string manipulation
- Word-frequency calculation
- Punctuation normalization
- `collections.Counter`
- Controlled exclusion of generic grammatical terms
- A dedicated `get_complaint_keywords()` function
- Grouping of recurring terms into broader complaint-related themes
- Visualization of selected keywords

The analysis highlights discussion around **fit and sizing, fabric/material/quality, appearance and colour, and order or product experience**.

Keyword frequencies indicate word occurrences across review text, not the number of unique customers or confirmed complaints.

### 5. Selection of the 3 Most Critical Reviews

The selection process follows:

**1–2 star reviews → 1-star priority → review-detail threshold → topic-based narrowing → manual inspection → final selection**

An **85-word minimum threshold** was used as a practical proxy for sufficient review detail. This produced a candidate pool of **628 detailed critical reviews**, comprising **199 one-star** and **429 two-star** reviews.

Candidates were narrowed using the presence of at least two predefined product or customer-experience topic terms. The final selection was made through manual inspection rather than a composite score, machine learning model, or advanced NLP method.

The three selected reviews are:

- **Review ID 6697**
- **Review ID 5467**
- **Review ID 21238**

All three are 1-star reviews exceeding the detail threshold and provide product-specific context for personalized outreach.

### 6. Generative AI Automated Outreach

The selected reviews are passed to **Gemini 3.5 Flash-Lite** through Google's `google-genai` Python SDK.

A reusable prompt template and response-generation function incorporate review-specific information while maintaining consistent instructions.

The prompt requires the model to:

- Address the customer's specific complaint.
- Personalize the response using relevant review details.
- Maintain an empathetic and professional tone.
- Keep the email concise.
- Use only information available in the supplied review and product context.
- Avoid unsupported refunds, replacements, compensation, guarantees, policies, or company actions.
- Avoid reconstructing incomplete portions of source reviews.

The generated responses are reviewed for relevance, personalization, empathy, and factual grounding.

## 💡 Key Analytical Findings

- The cleaned analysis-ready dataset contains **22,628 reviews**.
- **2,370 reviews (10.47%)** are classified as critical using the 1- or 2-star rule.
- The critical subset contains **821 one-star reviews** and **1,549 two-star reviews**.
- Within the critical subset, **2,261 reviews (95.4%)** were marked as not recommended, while **109 reviews (4.6%)** were still marked as recommended. This illustrates that rating and recommendation behaviour are related but distinct measures.
- Recurring vocabulary indicates frequent discussion of fit and sizing, fabric/material and quality, appearance and colour, and order or product experience.
- Keyword frequencies are interpreted as recurring vocabulary rather than direct counts of customer complaints.
- A transparent selection process identified three detailed 1-star reviews for personalized outreach.
- Generative AI transformed those selected reviews into individualized customer-support responses.

The project demonstrates a path from **data-quality assessment to actionable customer communication**.

## 🤖 Generative AI Implementation

The Generative AI component uses:

- **Model:** Gemini 3.5 Flash-Lite
- **SDK:** Google's `google-genai`
- **Credential variable:** `GEMINI_API_KEY`
- **Credential handling:** Environment variable loaded through `python-dotenv`

The API credential is kept separate from the notebook code. A reusable prompt template and Python function apply the same generation process to all three selected reviews.

The generated emails are designed to be specific, personalized, empathetic, concise, and grounded in the supplied data. The prompt also prevents unsupported commitments or claims that actions have already been taken.

### Handling Incomplete Source Reviews

The selected source reviews contain text that ends abruptly in the original dataset. The workflow does **not** attempt to reconstruct missing portions. Only the available review content is provided to the model, preserving the limitations of the source data.

## 🧪 Review Selection & Response Validation

The final three reviews were not selected solely because they had the longest text.

The methodology combines rating severity, review detail, multiple concrete product or customer-experience topics, complaint specificity, available context, and manual inspection of the actual review content.

The generated responses were reviewed against these criteria:

| Validation criterion | Purpose |
|---|---|
| Specific complaint addressed | Acknowledges the customer's actual concern |
| Personalization | Uses relevant details from the individual review |
| Empathy and apology | Recognizes the negative customer experience |
| Relevant review details | Keeps the response connected to the supplied data |
| Conciseness | Maintains a suitable customer-support length |
| Factual grounding | Avoids invented customer information or unsupported claims |
| No unsupported compensation | Avoids promises of refunds, replacements, or compensation |
| No unsupported company actions | Avoids claiming that actions have already been taken |
| Professional tone | Keeps the response appropriate for customer outreach |

## 🛠️ Technical Stack & Methodologies

### Languages & Libraries

- **Python 3.x:** Core programming language
- **Pandas:** Data loading, cleaning, filtering, grouping, and analysis
- **NumPy:** Numerical and missing-value support
- **Matplotlib:** Keyword-frequency visualization
- **`collections.Counter`:** Word-frequency analysis
- **`google-genai`:** Gemini API integration
- **`python-dotenv`:** Environment-variable loading

### Methodologies

- Exploratory data understanding
- Data wrangling and validation
- Pandas-based missing-value handling
- Basic text cleaning
- Rule-based critical-review classification
- Frequency-based keyword analysis
- Transparent candidate screening
- Manual review selection
- Generative AI response generation
- Qualitative response validation

**No machine learning is used for the core critical-review classification.** The project avoids unnecessary NLP pipelines, sentiment models, embeddings, and other advanced techniques so that the core methodology remains transparent and explainable.

## 📁 Project Structure

```text
customer-feedback-analysis-genai-response-automation/
│
├── data/
│   └── Womens Clothing E-Commerce Reviews.csv
│
├── images/
│   ├── Test Email.png
│   └── GenAI Generated Email.png
│
├── Customer Feedback Analysis Final.ipynb
├── .gitignore
└── README.md
```

### File Description

- **`Customer Feedback Analysis Final.ipynb`** - Complete notebook containing the six-phase workflow, insights, visualizations, review selection, and generated customer-support responses.
- **`data/`** - Contains the original customer-review dataset.
- **`images/`** - Contains screenshots illustrating the API test output and a generated customer-support email.
- **`.gitignore`** - Prevents environment files, Python cache files, and Jupyter checkpoint files from being tracked in local Git workflows.
- **`README.md`** - Project documentation covering the methodology, findings, setup, and execution instructions.

## 🖼️ Email Output Previews

The screenshots below illustrate the API test output and an example of a customer-support email generated from the selected review data.

### Connection Test Email

![Test Email](./images/Test%20Email.png)

### Generative AI-Generated Customer-Support Email

![GenAI Generated Email](./images/GenAI%20Generated%20Email.png)

## 🔐 API Key & Environment Setup

The notebook uses the `GEMINI_API_KEY` environment variable to keep the API credential separate from the project code.

Create a local `.env` file in the repository root with the following content:

```text
GEMINI_API_KEY=your_actual_api_key
```

Replace the placeholder with your own valid Gemini API key. **Never commit an actual API key to a public repository.** The `.gitignore` file includes rules to ignore `.env` files.

The notebook uses `python-dotenv` to load the environment variable and `google-genai` to initialize the Gemini client.

## 🚀 How to Run Locally

### 1. Clone or Download the Repository

Clone the repository or download it as a ZIP file and extract it locally.

### 2. Set Up Python

Ensure that Python 3.x and Jupyter Notebook or JupyterLab are available.

### 3. Install Required Libraries

```bash
pip install pandas numpy matplotlib google-genai python-dotenv
```

### 4. Configure the Gemini API Key

Create a `.env` file in the repository root and add your own valid Gemini API key as described above.

### 5. Open the Notebook

Launch Jupyter Notebook or JupyterLab and open:

```text
Customer Feedback Analysis Final.ipynb
```

### 6. Verify the Dataset Path

The notebook loads the CSV dataset using a configured path. If the folder structure or filename differs from the expected layout, update `DATA_PATH` in the data-loading cell.

### 7. Run the Notebook Sequentially

Run the notebook from beginning to end so that variables and intermediate datasets created in earlier phases are available to subsequent phases.

The Generative AI section requires a valid Gemini API key, the required libraries, and an active internet connection. API usage may be subject to applicable account limits and charges.

## 🧰 Project Components

| Component | Implementation |
|---|---|
| Data preparation | Missing-value investigation, text cleaning, duplicate handling, and validation |
| Critical-review analysis | Rule-based identification of 1- and 2-star reviews |
| Review insights | Rating distribution and recommendation behaviour |
| Keyword analysis | String operations, `collections.Counter`, and keyword visualization |
| Review selection | Detail threshold, topic-based screening, and manual inspection |
| Generative AI | Gemini API integration using a reusable prompt template and Python function |
| Response validation | Qualitative checks for relevance, personalization, empathy, and factual grounding |
| Reproducibility | Jupyter Notebook, documented dependencies, and environment-variable configuration |

## 🏁 Final Outcome

This project demonstrates an end-to-end approach to transforming raw customer feedback into targeted customer-support communication.

It combines data-quality assessment, Pandas-based data preparation, explainable rule-based analysis, lightweight text-frequency analysis, structured review selection, and Generative AI-assisted response generation.

By keeping the core analytical methodology simple and explainable while using Generative AI for personalized outreach, the workflow demonstrates how Python and Generative AI can work together to support practical customer-feedback analysis.

---

© 2026 Rudresh Kalgutkar. All Rights Reserved.
