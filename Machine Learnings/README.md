# FAQ - Introduction to Machine Learning

---

### 1. What is Machine Learning?
Machine learning is a branch of science that enables machines to learn from existing data, perform tasks based on that learning, and use performance indicators to measure how accurately the task is being performed.

**Example:**
- *Playing chess*: The experience of playing many games is the learning part. The task is playing chess, and the probability of winning the next game is the performance indicator.

Any machine learning problem can generally be classified as:
- **Supervised Learning**
- **Unsupervised Learning**

---

### 2. What is Supervised Learning?
Supervised learning is when the label of the target variable is already known. It is divided into:
- **Regression:** Target variable is continuous (e.g., house price prediction).
- **Classification:** Target variable is categorical/discrete (e.g., predicting if a person will be diabetic).

**Examples:**
- *Regression*: Predicting house price based on area, rooms, etc.
- *Classification*: Predicting diabetes based on health metrics.

---

### 3. What is Unsupervised Learning?
Unsupervised learning is a type of machine learning where the model is provided with input data without labeled responses. The goal is to find hidden patterns, groupings, or structures within the data.

**Examples:**
- *Clustering*: Grouping customers by purchasing behavior.
- *Dimensionality Reduction*: Reducing the number of features in a dataset while preserving important information.

Unsupervised learning is useful when you do not know the target variable or want to explore the data for insights.

---

### 4. What is a Model?
A machine learning model is a mathematical representation of a real-world process. It takes training data as input and, using an algorithm, forms a mathematical expression to produce an output.

**Example:**
- A house price prediction model takes features like area and rooms as input and outputs the predicted price.

---

### 5. What is an Algorithm?
An ML algorithm uses the training dataset to form a mathematical relationship between independent and dependent variables, producing the best-fit mathematical expression based on the data.

---

### 6. Why do we split the data into train and test sets?
The goal is to predict well on new, unseen data. Since the model only sees a sample of the true data, splitting into training and test sets allows us to evaluate how well the model generalizes.

- **Training set:** Used to train the model.
- **Test set:** Used to evaluate the model's performance on unseen data.

Good test set performance suggests the model will perform well on new data, assuming:
- Samples are drawn independently and randomly.
- The test set is large enough.

---

### 7. When and how do you bring in test data?
At the start, the dataset is split into train and test sets. The test set should represent the population for which predictions are needed.

---

### 8. Why study the mathematics behind algorithms if we can implement them with code?
Understanding the math provides intuition about how algorithms work, which helps in improving model performance, tuning hyperparameters, and interpreting results. A deeper understanding increases your ability to troubleshoot and optimize models.