# GenAI-Based Agentic E-Commerce Insights System
**Project for the Maersk AI/ML Intern: Campus Hiring Assignment**

GenAI-powered conversational analytics agent allows users to load, process, and analyze the Olist Brazilian E-Commerce and Marketing Funnel datasets using natural language.

The final deliverable is an interactive Streamlit application, deployed via `pyngrok`, that provides multiple modules:
* **Agentic Chat:** A conversational agent to ask complex questions.
* **Analytics Dashboards:** Interactive charts for KPIs.
* **Sales & ML Forecasts:** A time-series sales forecast and a marketing lead conversion model.
* **RAG Knowledge Search:** A semantic search for products and reviews.

---

### 🚀 How to Run the Project

This entire project is contained within a single Google Colab notebook (`.ipynb`).

1.  **Open in Google Colab:**
    * Upload and open the `.ipynb` file in Google Colab.

2.  **Get API Keys:**
    * You will need three API keys to run the full application:
        * **OpenAI:** For `gpt-4o-mini` (or other models).
        * **OpenRouter:** For `google/gemini-flash-1.5` (or other models).
        * **NGROK:** To create a public URL for the Streamlit app.

3.  **Run Cell 3 (Save API Keys):**
    * Paste your three keys into the variables in Cell 3.
    * Run the cell **once**. This will save your keys to a `.env` file in your Google Drive (`/content/drive/MyDrive/Colab_Secrets/.env`).

4.  **Run Cell 4 (Load API Keys):**
    * Run this cell to load the keys from your Drive into the Colab environment.

5.  **Run Cell 6 (Upload Data):**
    * Run the cell and a file upload button will appear.
    * Upload all 11 CSV files from the Olist E-commerce and Marketing Funnel datasets.

6.  **Run All Subsequent Cells (Cell 7 - Cell 17):**
    * Run each cell in order. This will:
        * Load and merge all 11 CSVs (Cell 7, 8).
        * Perform feature engineering to create `clv`, `item_total`, etc. (Cell 9).
        * Train the Sentiment (Cell 10), Clustering (Cell 11), Forecasting (Cell 12), and Deal Closing (Cell 13) models.
        * Build the RAG `FAISS` index (Cell 14).
        * Write the helper (`helper_llm.py`) and app (`app.py`) scripts (Cells 15, 17).
        * Save all the necessary artifacts (`.csv`, `.joblib`, `.faiss`) to disk (Cell 16).

7.  **Launch the App (Run Cell 18):**
    * Run the final cell. It will kill old processes, start Streamlit, and generate a public `ngrok` URL.
    * Open the `NgrokTunnel` URL (e.g., `https://....ngrok-free.app`) in a new browser tab to use the application.

---

### **Project Summary: GenAI-Based Agentic E-Commerce Insights System**

#### **Functional Features**
System is a Streamlit-based GenAI conversational analytics agent that lets users explore and analyze the Olist Brazilian E-Commerce dataset and Marketing Funnel dataset in natural language. Users can chat with the system to extract insights, run data analysis, generate reports, and visualize metrics.

🧠 **Core Functional Modules:**

1.  **Conversational Querying (LLM-Powered Pandas Agent)**
    * Users can ask natural language questions (e.g., “Show top 10 selling product categories in 2018”).
    * The system uses GenAI agent (Gemini/OpenRouter/OpenAI) to translate advanced text into **Pandas code**, which is then executed live to return an answer.

2.  **Analytics Dashboards**
    * Auto-geStreamlit dashboards provide visualizations for:
        * 🛍️ **Product analytics** (top sales & ratings by category)
        * 👥 **Customer insights** (top cities by revenue, customer segment clusters)
        * 📦 **Order metrics** (average delivery time, payment method distribution).

3.  **NLP Review Analysis**
    * **Sentiment analysis** is performed on the `review_comment_message` column using **VADER**.
    * Customers are clustered into four distinct segments using **KMeans** based on their order value, frequency, and sentiment.

4.  **Sales Prediction**
    * A time-series forecast for monthly sales is generated using **Statsmodels (Holt-Winters Exponential Smoothing)**.

5.  **Delivery Performance**
    * The system calculates delivery performance by comparing `order_delivered_customer_date` with `order_estimated_delivery_date` to create a `delivery_delay` feature.

6.  **Product Quality Insights**
    * The "Analytics Dashboard" tab directly visualizes the top and bottom product categories by average `review_score`.

7.  **Feature Engineering**
    * New variables were created, including `item_total` (price + freight), `delivery_days`, `is_repeat_customer`, and `clv` (historical Customer Lifetime Value).

8.  **Marketing Funnel Integration**
    * The e-commerce dataset is joined with the `olist_marketing_qualified_leads_dataset.csv` and `olist_closed_deals_dataset.csv`.
    * A **Logistic Regression** model is trained to predict which new leads are most likely to convert based on their `origin` and `landing_page_id`.

9.  **Recommendation System**
    * A simple **Content-Based** recommendation engine is implemented. When a user asks for a recommendation, the system suggests the top-rated products from that specific category.

10. **Multilingual Conversations**
    * The agent handles multilingual queries (e.g., Portuguese, Spanish, French) for common questions in those languages.

11. **External Knowledge (RAG Integration)**
    * A RAG pipeline using **FAISS** and **SentenceTransformers** (`all-MiniLM-L6-v2`) was built to semantically search product categories and customer reviews for concepts, not just keywords.

12. **Rich Streamlit UI**
    * A multi-page application is presented with a Maersk-branded sidebar for navigation and key KPIs.
    * The UI is styled with custom CSS, and all visualizations are rendered using Plotly for interactivity.
    * Conversational memory is enabled via `st.session_state`.

---
🧩 **System Architecture & Design Decisions**

The architecture is split into a **Data Processing Layer** (the Colab notebook) and an **Application Layer** (the Streamlit app).

1.  **Data Layer:** 11 raw CSVs are loaded from the user's local disk into the Colab environment.
2.  **Processing Layer:** The notebook (Cells 8-16) runs a one-time pipeline to clean, merge, and transform the raw data. It engineers features like `clv`, trains all ML models (`KMeans`, `LogisticRegression`, `Holt-Winters`), and builds the `FAISS` RAG index. All these artifacts are saved to the Colab disk.
3.  **Agent Layer:** A custom `helper_llm.py` script manages API calls to **OpenAI** or **OpenRouter**. The agent logic in `app.py` is a hybrid system:
    * **Design Decision:** A **rules-based `if/elif` system** handles the top 10-15 most common queries (like "top categories" or "recommend"). This is fast, free, reliable, and demonstrates robust engineering.
    * **Design Decision:** An **LLM-powered Pandas Agent** is used as a fallback for all other queries. It generates and executes Pandas code, which is more flexible and direct than a SQL-based agent for this project.
4.  **Application Layer:** The Streamlit app (`app.py`) loads the pre-processed artifacts from disk. It serves a multi-page UI, handles chat state, and calls the appropriate models (RAG, ML, or LLM) based on user interaction. The app is deployed from Colab using `pyngrok`.

---
⚙️ **Tech Stack**

* **Frontend:** Streamlit
* **Backend:** Python
* **Data Analysis:** Pandas, NumPy, Matplotlib, Plotly
* **NLP / ML:** scikit-learn (KMeans, LogisticRegression), Statsmodels (Holt-Winters), NLTK (VADER)
* **Database Access:** Pandas (direct CSV loading)
* **RAG / Embeddings:** FAISS (faiss-cpu), SentenceTransformers
* **LLM Integration:** OpenAI API, Requests (for OpenRouter)
* **Environment & Keys:** Google Colab, python-dotenv (for `.env`)
* **Deployment:** pyngrok
---
