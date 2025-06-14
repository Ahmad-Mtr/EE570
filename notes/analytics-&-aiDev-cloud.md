# 📚 Cloud & Big Data Exam Notes: Data Analytics & AI in Cloud, Ft. Spark (Lazy Edition)

> [!NOTE] This section is about how we handle *really big data* and *smart algorithms* in the cloud. Hint: It's all about distributed processing and renting powerful machines.

---

## 📊 Intro to Big Data / AI with Cloud Services (Quick Recap)

Remember our last chat? It's the same idea: The cloud makes all these steps for Big Data and AI way easier and cheaper than doing them yourself.

*   **Libraries & Environment**: No more "DLL hell" or dependency nightmares. The cloud gives you pre-built environments.
*   **Dataset Operations**: Load/save massive datasets easily from scalable cloud storage.
*   **AI Model Prep & Training**: Rent super powerful machines (with GPUs/TPUs!) for a few hours instead of buying them. Save your huge models back to cloud storage.
*   **Visualization**: See your data and results easily.
*   **Dealing with Large Data**: The cloud is *built* for this.

---

## 🤖 AI Development with Cloud Services (Project Types)

Again, just a quick reminder of the common AI problems you'd solve using cloud's power:

*   **Project 1: Regressing Analysis**: Predicting a *number* (e.g., house prices, sales figures).
*   **Project 2: Clustering**: Grouping similar items *without* knowing the groups beforehand (e.g., customer segmentation).
*   **Project 3: Classifications**: Predicting a *category* (e.g., spam/not spam, cat/dog).

---

## ✨ Data Analytics with Spark (The Big Deal)

### What is Apache Spark?
*   **Fast**: It's quicker than old-school big data tools.
*   **Open Source**: Free to use and develop.
*   **Big Data Engine**: Built for huge datasets and complex analysis.
*   **Distributed Processing**: It splits your work across many machines (like a team working on a big puzzle).
*   **Developed in Scala**, runs on a **Java Virtual Machine (JVM)**.
*   **Languages**: You can code in **Scala, Java, Python (PySpark!), or R**. Python is super popular for Spark.

#### Key Features:
1.  **In-Memory Processing**: Spark tries to keep data in RAM as much as possible for super fast processing (unlike Hadoop which always wrote to disk).
2.  **Distributed Processing**: It breaks your data and tasks into smaller pieces and runs them on many "workers" (computers) in a cluster.

    ```mermaid
    graph LR
        A[Input Data] -- Distribute --> B[Worker 1];
        A -- Distribute --> C[Worker 2];
        A -- Distribute --> D[Worker N];
        B -- Process in Memory --> B_R[Result Part];
        C -- Process in Memory --> C_R[Result Part];
        D -- Process in Memory --> D_R[Result Part];
        B_R & C_R & D_R --> E[Final Output];
    ```

### 🧠 Apache Spark: Key Concepts (The Vocabulary)

Let's break down how Spark gets stuff done in a cluster:

*   **Cluster**: Just a bunch of computers (nodes) working together.
*   **Node**: An individual computer in your Spark cluster.
*   **Master (Node)**: The boss. It's the entry point to your cluster. It takes your requests and tells the workers what to do.
*   **Worker/Slave (Node)**: The grunt. These are the machines that actually do the data crunching as instructed by the Master.

    ```mermaid
    graph TD
        M[Master] --> W1(Worker/Slave 1);
        M --> W2(Worker/Slave 2);
        M --> W3(Worker/Slave N);
    ```

*   **Spark Job**: A big, parallel computation that consists of many smaller `tasks`. When you run your code, Spark turns it into one or more jobs.
*   **Driver Program**: Your code! It's what you write and submit to the cluster. It also holds the main logic and coordinates the job.
*   **Executor**: A process that runs on a `Worker` node. It launches and executes the actual `tasks` of a Spark Job. Each executor uses some `Cores` (CPU) and `Memory` from its worker.
*   **Task**: The smallest unit of work in Spark. A task does the actual computation on a piece of data, specifically an `RDD Partition`.
*   **Stage**: A group of `parallel tasks`. If your Spark Job needs to do something that requires shuffling data around (like grouping or sorting), it will break into multiple stages. A Spark Job can have multiple stages.

*   **RDD (Resilient Distributed Datasets)**:
    *   **The core data structure in Spark.**
    *   It's a **fault-tolerant collection of elements** that can be operated on in parallel across your cluster.
    *   **Resilient**: If a part of the RDD is lost (e.g., a worker fails), Spark can automatically rebuild it from its lineage (how it was created).
    *   **Distributed**: Data is spread across all the nodes in the cluster.
    *   **Lazy Evaluation**: Spark doesn't actually do the work until you ask for a final result (like printing or saving). It just builds a plan (graph of transformations).

*   **RDD Partition**: An RDD is broken down into smaller chunks called partitions. Each `Task` operates on one `RDD Partition`.

### ➡️ Map-Reduce Paradigm (How it works conceptually)

Spark follows the Map-Reduce idea:
1.  **Map Phase**: Take input data, apply a function to each element independently (e.g., multiply each number by 2).
2.  **Shuffle/Sort (Optional)**: If needed, group data by keys (e.g., for summing counts).
3.  **Reduce Phase**: Combine intermediate results (e.g., sum all the mapped numbers).

    ![[Pasted image 20240722144820.png]]
    *(Input [1,2,3,4,5] -> MAP (multiply by 2) -> [2,4,6,8,10] -> REDUCE (sum) -> 30)*

    *(The other images are just more detailed examples of word count and processing that illustrate the map, shuffle, and reduce steps.)*

### ☁️ Why Run Spark on Cloud?
This is crucial. It brings together all the benefits we learned about cloud computing.

*   **Elasticity / Scalability**: Request *as much resource as you want* (more CPUs, more RAM, more storage) and get it instantly. Scale up for big jobs, scale down to save money.
*   **Pay-per-Usage**: You only pay for what you use. No need to buy expensive servers that sit idle most of the time.
*   **Accessibility**: Access your Spark cluster from anywhere.
*   **Collaboration**: Easily share and use the same cluster with a team.
*   **Managed Services**: Cloud providers (AWS EMR, Google Cloud Dataproc, Azure HDInsight, Databricks) offer managed Spark services. This means they handle setting up, maintaining, and scaling the Spark cluster, so you just focus on your code.

---
You're well on your way! That covers the main ideas.