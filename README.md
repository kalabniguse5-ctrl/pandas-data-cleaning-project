# Data Cleaning and Transformation Using Pandas

## 1. Project Overview

This project focuses on cleaning and transforming a real-world Airbnb dataset using **Python and Pandas**.

The original dataset was provided in **CSV format** and contained:

* **102,599 records**
* **26 columns**

The objective was to transform the raw dataset into a cleaner, more consistent, and machine-readable dataset suitable for further analysis and data-processing workflows.

The project covered data inspection, column selection, duplicate removal, missing-value handling, data standardization, data-type conversion, and exporting the cleaned dataset into different formats.

---

## 2. Technologies Used

* **Python**
* **Pandas**
* **CSV**
* **Excel**

---

## 3. Loading and Inspecting the Dataset

The first step was importing the Pandas library and assigning it the commonly used alias `pd`.

The CSV dataset was then loaded into a variable called `data`.

```python
import pandas as pd

data = pd.read_csv("your_file.csv")
```

### Initial Data Inspection

To confirm that the dataset was loaded correctly, I used:

```python
data.head()
```

This displayed the first five records of the dataset.

I then inspected the column names using:

```python
data.columns
```

Finally, I used:

```python
data.info()
```

This allowed me to examine the dataset structure, including the columns, data types, and number of non-null values.

---

# 4. Selecting Relevant Columns

After inspecting the dataset, I identified columns that were useful for the project and separated them into two groups:

### Columns to Keep

```python
columns_to_keep = [
    'NAME', 'host id', 'host_identity_verified', 'host name',
    'neighbourhood group', 'neighbourhood', 'lat', 'long', 'country',
    'country code', 'instant_bookable', 'cancellation_policy',
    'room type', 'Construction year', 'price', 'service fee',
    'minimum nights', 'number of reviews', 'last review'
]
```

### Columns to Drop

```python
columns_to_drop = [
    'id', 'reviews per month',
    'review rate number', 'calculated host listings count',
    'availability 365', 'house_rules', 'license'
]
```

I also checked the length of both lists to verify the number of columns selected for each operation.

---

## 5. Two Methods for Selecting and Removing Columns

I practiced two different approaches for managing columns.

### Method 1 — Selecting Columns

I created a new DataFrame containing only the columns I wanted to keep:

```python
df = data[columns_to_keep]
```

This method creates a separate DataFrame containing the selected columns.

### Method 2 — Dropping Columns

I also practiced directly removing unwanted columns from the original DataFrame:

```python
data.drop(columns=columns_to_drop, inplace=True)
```

The `inplace=True` parameter updates the existing DataFrame instead of creating a separate one.

Learning both approaches helped me understand different ways of manipulating DataFrames with Pandas.

---

# 6. Standardizing Column Names

The original dataset contained inconsistent column-name formatting.

To standardize the names, I created an empty list:

```python
new_columns_name = []
```

I then used a `for` loop to iterate through the column names and apply title capitalization:

```python
for i in data.columns:
    new_columns_name.append(i.title())
```

This helped standardize the appearance of the column headers.

---

# 7. Identifying and Removing Duplicate Records

Before removing duplicates, I checked how many duplicate records existed:

```python
data.duplicated().value_counts()
```

The result was:

```text
False    102058
True        541
```

This showed that the dataset contained:

* **102,058 unique records**
* **541 duplicate records**

I then removed the duplicates:

```python
data.drop_duplicates(inplace=True)
```

Afterward, I verified the result using:

```python
data.duplicated().sum()
```

The result confirmed that there were **no remaining duplicate records**.

---

# 8. Identifying Missing Values

The next stage was identifying missing values in each column.

I used:

```python
data.isna().sum()
```

The initial results showed missing values across several columns:

| Column                 | Missing Values |
| ---------------------- | -------------: |
| Name                   |            250 |
| host id                |              0 |
| host_identity_verified |            289 |
| host name              |            404 |
| neighbourhood group    |             29 |
| neighbourhood          |             16 |
| lat                    |              8 |
| long                   |              8 |
| country                |            532 |
| country code           |            131 |
| instant_bookable       |            105 |
| cancellation_policy    |             76 |
| room type              |              0 |
| Construction year      |            214 |
| price                  |            247 |
| service fee            |            273 |
| minimum nights         |            400 |
| number of reviews      |            183 |
| last review            |         15,832 |

---

# 9. Removing the `last review` Column

The `last review` column contained a significantly larger number of missing values than the other columns.

Instead of removing a large number of records because of this column, I decided that the column was not necessary for the cleaned dataset.

I therefore removed it:

```python
data.drop(columns=['last review'], inplace=True)
```

This allowed the remaining dataset to be cleaned without retaining a column with substantial missing data.

---

# 10. Removing Remaining Missing Values

After removing `last review`, I removed records containing remaining missing values:

```python
data.dropna(inplace=True)
```

I then verified that no missing values remained:

```python
data.isna().sum()
```

The final result showed **0 missing values across all remaining columns**.

This resulted in a dataset with complete values across the selected fields.

---

# 11. Standardizing `host_identity_verified`

The `host_identity_verified` column contained lowercase text values.

To standardize the values, I used:

```python
data["host_identity_verified"].str.upper()
```

I then assigned the transformed values back to the DataFrame:

```python
data["host_identity_verified"] = data["host_identity_verified"].str.upper()
```

This converted the text values to uppercase and created a more consistent representation.

---

# 12. Converting Boolean Values to Numerical Values

The `instant_bookable` column contained Boolean values:

* `True`
* `False`

For easier machine processing, I converted these values into numerical representations:

* `True` → `1`
* `False` → `0`

I used:

```python
data["instant_bookable"] = data["instant_bookable"].apply(
    lambda x: 1 if x == True else 0
)
```

This transformed the Boolean feature into a numerical feature that can be directly used in many machine-learning and analytical workflows.

---

# 13. Cleaning and Converting the `price` Column

The `price` column contained currency symbols, commas, and spaces.

For example, values could contain formatting such as:

```text
$1,000
```

I removed the unwanted characters step by step:

```python
data["price"] = data["price"].str.replace("$", "")
data["price"] = data["price"].str.replace(",", "")
data["price"] = data["price"].str.replace(" ", "")
```

After cleaning the text representation, I converted the column to integers:

```python
data["price"] = data["price"].astype(int)
```

The result was a numerical `price` column suitable for calculations and analysis.

---

# 14. Cleaning and Converting the `service fee` Column

I performed a similar cleaning process on the `service fee` column.

First, I removed the currency symbol, spaces, and commas:

```python
data["service fee"] = data["service fee"].str.replace("$", "")
data["service fee"] = data["service fee"].str.replace(" ", "")
data["service fee"] = data["service fee"].str.replace(",", "")
```

I then converted the cleaned values to integers:

```python
data["service fee"] = data["service fee"].astype(int)
```

This converted the column from formatted text into numerical data.

---

# 15. Verifying Data Types

After converting `price` and `service fee`, I checked their data types:

```python
type(data["price"][3])
type(data["service fee"][5])
```

This was used to verify that the values had successfully been converted to integer values.

---

# 16. Removing the Unnecessary Index Column

During the cleaning process, I identified an unnecessary `index` column.

I removed it using:

```python
data.drop(columns=["index"], inplace=True)
```

This prevented an unnecessary index field from being included in the final exported dataset.

---

# 17. Exporting the Cleaned Dataset

After completing the cleaning and transformation process, I exported the final dataset into two formats.

### CSV

```python
data.to_csv("Cleaned_Data.csv", index=False)
```

The `index=False` parameter prevents Pandas from writing the DataFrame index as an additional column.

### Excel

```python
data.to_excel("Excel_cleaned_data.xlsx", index=False)
```

This produced an Excel version of the cleaned dataset.

---

# 18. Verifying the Exported Files

Finally, I verified that the exported files could be successfully read back into Pandas.

### Excel

```python
pd.read_excel("Excel_cleaned_data.xlsx")
```

### CSV

```python
pd.read_csv("Cleaned_Data.csv")
```

This provided a final check that the exported files were readable and that the cleaning pipeline successfully produced usable output files.

---

# 19. Final Data-Cleaning Pipeline

The overall workflow of the project was:

```text
Raw CSV Dataset
       ↓
Load Dataset with Pandas
       ↓
Inspect Dataset
       ↓
Select Relevant Columns
       ↓
Remove Unnecessary Columns
       ↓
Standardize Column Names
       ↓
Identify Duplicate Records
       ↓
Remove Duplicates
       ↓
Identify Missing Values
       ↓
Remove Unnecessary Missing-Value Column
       ↓
Remove Remaining Missing Records
       ↓
Standardize Text Values
       ↓
Convert Boolean Values
       ↓
Clean Currency Columns
       ↓
Convert Data Types
       ↓
Remove Unnecessary Index
       ↓
Export Clean Dataset
       ↓
Validate Exported Files
```

---

# 20. Key Skills Demonstrated

Through this project, I practiced several important Pandas and data-engineering skills:

* Reading CSV data
* Inspecting DataFrames
* Selecting and dropping columns
* Working with DataFrame metadata
* Detecting duplicate records
* Removing duplicates
* Detecting missing values
* Handling missing data
* String manipulation
* Standardizing categorical values
* Boolean-to-integer transformation
* Cleaning currency-formatted data
* Converting data types
* Exporting DataFrames to CSV and Excel
* Validating exported datasets

---

# 21. Project Outcome

The project transformed a raw **102,599-record, 26-column dataset** into a cleaner and more consistent dataset by removing unnecessary fields, duplicate records, missing values, inconsistent text formatting, and improperly formatted numerical values.

The final dataset was exported in **CSV and Excel formats** and verified by reading the generated files back into Pandas.

Although JSON export was initially considered, it was not included in the final version of this project.

---

# 22. What I Learned

The most important lesson from this project was that real-world datasets are rarely ready for immediate analysis.

Before a dataset can be effectively analyzed or used in downstream data workflows, it often requires:

**Inspection → Cleaning → Standardization → Transformation → Validation → Export**

This project gave me practical experience using Pandas to perform these steps on a relatively large real-world dataset rather than relying only on small tutorial datasets.
