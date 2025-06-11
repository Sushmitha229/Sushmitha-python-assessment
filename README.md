# Sushmitha-python-assessment
The data is presented in dynamic HTML tables rendered by JavaScript, which are scraped using Selenium and parsed with BeautifulSoup. The data is then transformed and stored as Delta tables using PySpark in a Databricks notebook environment.

🎯 Objective
Automate scraping of three JavaScript-rendered tables from IBEX.
Clean and structure the data using PySpark.
Persist the data as Delta tables for further analysis and reporting.
🔧 Technologies Used
Databricks (Notebook environment for Spark processing)
Apache Spark / PySpark (Data transformation and storage)
Delta Lake (Efficient storage format for Spark tables)
Selenium (Headless browser automation for dynamic scraping)
BeautifulSoup (HTML parsing)
Google Chrome & ChromeDriver (Browser engine for Selenium)
📂 Output Delta Tables
Table Name	Description
prices_volumes_table	Aggregated daily metrics like average price, total volume
block_products_table	Day-wise prices for block products (e.g., Base, Peak)
hourly_products_table	Hour-wise price and volume metrics
⚙️ Setup Instructions
1️⃣ Install Python Dependencies
%pip install selenium==4.18.1 webdriver-manager==4.0.2 typing_extensions==4.12.2
2️⃣ Install System Dependencies (Chrome + Utilities)
%sh
sudo apt-get update
sudo apt-get install -y wget unzip
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt-get install -y ./google-chrome-stable_current_amd64.deb
sudo apt-get install -y -f
3️⃣ Verify Chrome Installation
%sh google-chrome --version
🧠 Core Logic Overview
🔹 Initialize Spark Session
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("WebScrapeTablesSelenium").getOrCreate()
🔹 Helper Function to Clean Numbers
def clean_numeric(value):
    try:
        return float(value.replace(" ", "").replace(",", "."))
    except:
        return None
🔹 Scraping Function: scrape_and_parse_table()
Launches a headless Chrome browser via Selenium.
Opens the IBEX Day-Ahead Market page.
Extracts the required HTML table using BeautifulSoup.
Parses and cleans data into a Spark DataFrame with a custom schema.
Handles specific parsing logic for:
prices_volumes_table
block_products_table
hourly_products_table
🌐 Target URL
https://ibex.bg/markets/dam/day-ahead-prices-and-volumes-v2-0-2/
💾 Saving Data to Delta Tables
Each table is stored using the following method:

df.write.mode("overwrite").format("delta").saveAsTable("table_name")
Example:

prices_volumes_df = scrape_and_parse_table(url, 0, "prices_volumes")
if prices_volumes_df:
    prices_volumes_df.write.mode("overwrite").format("delta").saveAsTable("prices_volumes_table")
    display(prices_volumes_df)
📊 Sample SQL Query
-- Show latest 10 records from prices_volumes_table
SELECT * FROM prices_volumes_table ORDER BY date DESC LIMIT 10;

