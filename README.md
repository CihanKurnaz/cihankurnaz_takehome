# **Intent Classification Annotation Pipeline**

This project encompasses an end-to-end data processing and labeling pipeline designed to generate high-quality training data for classifying customer support tickets. The system integrates real-time data streaming, SLM (Small Language Model) powered automated labeling, and Python-based quality control mechanisms.

## **1\. System Architecture**

According to the design document, the data flow is as follows:  
Raw Data Source → Apache Kafka → Apache Flink → SLM (Labeling) → Quality Validator → Output Generator → Delta Lake/S3

### **Component Details**

* **Ingestion (Apache Kafka):** Provides a continuous stream of raw data. Chosen for high scalability and low latency.  
* **Processing (Apache Flink):** Used for real-time stream processing. Unlike Spark's micro-batch approach, Flink offers true real-time processing with minimal latency. Data integrity is maintained using a Schema Registry.  
* **Labeling (SLM):** Small Language Models, such as Llama 3.2 3B, are utilized to label incoming text. Model versions and prompt templates are stored for data governance.  
* **Storage (S3 & Delta Lake):** Labeled data is stored in S3 for cost efficiency, while Delta Lake is used to ensure versioning and data consistency.

## **2\. Quality Validator & Output Generator (PoC)**

The **Quality Validator** is a critical component of the pipeline that processes raw output from the SLM or human annotators to create a clean training dataset.

### **Applied Controls:**

1. **Confidence Threshold:** Filters out labels below a specific threshold (default: 0.8).  
2. **Agreement Check:** Identifies cases where different labels are suggested for the same text and logs them in disagreements.log.  
3. **Clean Data Generation:** Only high-confidence, agreed-upon data is included in the final training set.

## **3\. Setup and Execution**

### **Requirements**

* Python 3.x  
* Pandas library (pip install pandas)

### **Execution Steps**

1. Prepare the raw data (raw\_annotations.csv).  
2. Run the processing script:  
   python process\_annotations.py

3. Check the outputs:  
   * clean\_training\_dataset.jsonl: Clean data ready for model training.  
   * disagreements.log: Flagged samples requiring manual review or correction.

## **4\. Technical Justification**

* **Why Flink?** Superior low-latency and real-time processing capabilities compared to batch-oriented alternatives.  
* **Why Python?** The de-facto standard for data validation and dataset preparation due to its rich library ecosystem and readability.  
* **Why Delta Lake?** Enables "Time Travel" for historical data access and ensures robust version control for datasets.