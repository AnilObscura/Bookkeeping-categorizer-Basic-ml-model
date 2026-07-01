# Smart Accounting Classifier (Basic ML Model)

> Automatically classify free-text financial transactions into correct bookkeeping categories using a Machine Learning model, and store every prediction in a MySQL database with a simple web UI.

---

## Table of Contents
1. [Project Purpose](#project-purpose)
2. [Tech Stack](#tech-stack)
3. [Machine Learning Pipeline & System Architecture](#machine-learning-pipeline--system-architecture)
4. [Database Design & Integration](#database-design--integration)
5. [Key Features & Components](#key-features--components)
6. [How to Run Locally](#how-to-run-locally)
7. [Project Screenshots](#project-screenshots)
8. [License](#license)

---

## Project Purpose

Small businesses and individuals often manually categorize financial transactions (e.g., Income, Expense, Sales, Purchase), which is highly time-consuming and prone to human error.

This application solves that problem by:
1. Using a **Machine Learning model (TF-IDF + Naive Bayes)** to instantly parse free-text transaction descriptions.
2. Predicting the correct bookkeeping category for the text.
3. Persisting every single prediction into a **MySQL database** for auditing and historical review.
4. Providing a **lightweight web interface** to interact with the classifier and view the history.

---

## Tech Stack

| Layer | Technology |
| :--- | :--- |
| Backend Web Framework | Python, Flask |
| Machine Learning | scikit-learn (TfidfVectorizer, Multinomial Naive Bayes) |
| Model Serialization | Pickle |
| Database Engine | MySQL (via `mysql.connector`) |
| Frontend UI | HTML5, CSS3 (Inline), Vanilla JavaScript |
| Development Tools | VS Code, Git, GitHub |

---

## Machine Learning Pipeline & System Architecture

### 1. The Model Training Pipeline
The system uses a classic NLP pipeline for text classification:

- **TF-IDF Vectorizer:** Converts raw transaction text (e.g., *"sold finished goods 15000"*) into numerical feature vectors by calculating term frequency-inverse document frequency.
- **Multinomial Naive Bayes:** A probabilistic classifier trained on labeled text data to categorize the vectors into 4 distinct classes: `purchase`, `expense`, `sales`, `income`.

**Training Process:** The `model.py` script reads a `sample_data.csv` file (containing 18 labeled financial transactions), trains the pipeline, and serializes the trained model into a `model.pkl` file using the `pickle` library.

### 2. Web Application Architecture (Flask)
The system follows a lightweight MVC pattern:

```text
[User Input (Browser)]
    ↓
[Flask Routes] (GET `/`, POST `/predict`, GET `/predictions`, DELETE `/clear`)
    ↓
[ML Prediction] (Loads `model.pkl` using Pickle, predicts label)
    ↓
[Database Persistence] (Inserts text & predicted label into MySQL)
    ↓
[UI Update] (JavaScript fetches history and renders the table)
```

---

## Database Design & Integration

The application uses **MySQL** to store the transaction history.

- **Table Name:** `predictions`

- **Schema:**
  - `id` (INT, PRIMARY KEY, AUTO_INCREMENT)
  - `text` (TEXT) - The raw financial transaction input.
  - `predicted_label` (VARCHAR) - The ML model's classification (`purchase`, `expense`, `sales`, `income`).
  - `timestamp` (TIMESTAMP) - Automatically records when the prediction was made.

- **Database Helper:** A helper function `db_execute()` handles all connections and transactions safely using `mysql.connector`, ensuring every query is executed securely and connections are closed after each operation.

---

## Key Features & Components

### 1. Real-Time Prediction Engine
- A simple, clean input field on the web UI.
- User types a transaction (e.g., *"paid rent 5000"*) and clicks "Classify".
- The Flask backend loads the Pickle model, runs `model.predict()`, and instantly returns the category (e.g., `expense`).

### 2. Dynamic History Table
- Automatically fetches the entire history from the MySQL `predictions` table on page load.
- Displays columns for `ID`, `Text`, and `Label` with a clean, professional UI.
- Fully responsive to new predictions—the table updates automatically without refreshing the page.

### 3. One-Click Data Wipe
- Includes a "Clear All" button for resetting the database.
- Uses a JavaScript confirmation prompt to prevent accidental deletion.
- Clears all records and resets the `AUTO_INCREMENT` counter to 1.

### 4. Supported Classification Categories
The model supports 4 primary bookkeeping labels:
- **Purchase** (e.g., *"bought raw material 5000"*)
- **Expense** (e.g., *"paid electricity bill 2000"*)
- **Sales** (e.g., *"sold 10 shirts 6000"*)
- **Income** (e.g., *"received payment 5000"*)


# 🚀 How to Run Locally

Follow these steps to set up the project on your local machine.

## 1️⃣ Clone the Repository

```bash
git clone https://github.com/AnilObscura/Bookkeeping-categorizer-Basic-ml-model.git
cd Bookkeeping-categorizer-Basic-ml-model
```

## 2️⃣ Create and Activate a Python Virtual Environment (Recommended)

### Windows

```bash
python -m venv venv
venv\Scripts\activate
```

### macOS / Linux

```bash
python3 -m venv venv
source venv/bin/activate
```

## 3️⃣ Install Dependencies

```bash
pip install flask scikit-learn pandas mysql-connector-python
```

## 4️⃣ Configure MySQL Database

Make sure you have a local MySQL server running.

Update the `MYSQL_CONFIG` dictionary inside **app.py**:

```python
MYSQL_CONFIG = {
    "host": "localhost",
    "user": "root",
    "password": "YOUR_PASSWORD",
    "database": "YOUR_DB_NAME"
}
```

Create the required table in MySQL:

```sql
CREATE TABLE predictions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    text TEXT,
    predicted_label VARCHAR(50),
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 5️⃣ Train and Serialize the Machine Learning Model

Generate the trained model file (`model.pkl`):

```bash
python model.py
```

## 6️⃣ Start the Flask Application

```bash
python app.py
```

## 7️⃣ Open in Browser

Visit:

```text
http://127.0.0.1:5000
```

to access the **Smart Accounting Transaction Classifier**.

---

# 📸 Project Screenshots

Here is a visual walkthrough of the application.

---

## 1️⃣ Web Interface & Transaction Classification

The web interface allows users to enter any accounting transaction. After clicking **Classify**, the trained Machine Learning model instantly predicts the appropriate accounting category and stores the prediction in the MySQL database.

![Web Interface](https://github.com/AnilObscura/Bookkeeping-categorizer-Basic-ml-model/blob/2efaaeee4776026e3616262f2cbb6f1d095138c1/s1.png)

---

## 2️⃣ Transaction History & MySQL Database

Every classified transaction is automatically stored in MySQL. The application fetches and displays the complete prediction history, making auditing, verification, and record management simple.

![MySQL Database](https://github.com/AnilObscura/Bookkeeping-categorizer-Basic-ml-model/blob/2efaaeee4776026e3616262f2cbb6f1d095138c1/s2.png)


## 📄 License
This project is open-source under the MIT License.

Created with ❤️ by Anil Kumar Tanwar (AnilObscura).
