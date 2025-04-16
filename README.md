# data-streamline

Creating an automated ETL (Extract, Transform, Load) pipeline involves multiple steps, including reading raw data from a source, processing/transformation of data, and then loading the processed data into a destination for further visualization or usage. Here is a basic Python program outlining an ETL pipeline. The example uses Pandas for data processing, assumes CSV data sources, and SQLite for data storage. You will need to install required packages like `pandas` and `sqlalchemy`.

```python
import pandas as pd
import sqlalchemy
from sqlalchemy.exc import SQLAlchemyError

def extract_data(file_path):
    """
    Extract data from a CSV file.
    Params:
    file_path (str): The path to the CSV file to be loaded.

    Returns:
    DataFrame: A pandas DataFrame containing the data.
    """
    try:
        data = pd.read_csv(file_path)
        print("Data extracted successfully.")
        return data
    except FileNotFoundError as e:
        print(f"Error: The file was not found. {e}")
    except pd.errors.ParserError as e:
        print(f"Error: Could not parse the CSV file. {e}")
    except Exception as e:
        print(f"An error occurred during data extraction: {e}")

def transform_data(data):
    """
    Transform the data - example transformations include cleaning and feature engineering.
    Params:
    data (DataFrame): The DataFrame to transform.

    Returns:
    DataFrame: The transformed DataFrame.
    """
    try:
        # Example Transformation: Remove rows with NA values
        transformed_data = data.dropna()
        
        # Example Transformation: Change data types
        # transformed_data['some_column'] = transformed_data['some_column'].astype(float)
        
        print("Data transformed successfully.")
        return transformed_data
    except KeyError as e:
        print(f"Error: Column not found for transformation. {e}")
    except Exception as e:
        print(f"An error occurred during data transformation: {e}")

def load_data(data, database_name, table_name):
    """
    Load data into a SQLite database.
    Params:
    data (DataFrame): The DataFrame to load.
    database_name (str): The name of the SQLite database.
    table_name (str): The name of the table in which to load the data.
    """
    try:
        # Connect to SQLite database
        engine = sqlalchemy.create_engine(f'sqlite:///{database_name}.db')
        
        # Load data into a new table (or replace existing)
        data.to_sql(table_name, con=engine, if_exists='replace', index=False)
        
        print("Data loaded into database successfully.")
    except SQLAlchemyError as e:
        print(f"An error occurred related to the database operation: {e}")
    except Exception as e:
        print(f"An error occurred during data load: {e}")

def main():
    # Specify file path and database details
    file_path = 'data.csv'
    database_name = 'etl_database'
    table_name = 'processed_data'

    # Extract, transform, and load data
    extracted_data = extract_data(file_path)
    
    if extracted_data is not None:
        transformed_data = transform_data(extracted_data)
        
        if transformed_data is not None:
            load_data(transformed_data, database_name, table_name)

if __name__ == '__main__':
    main()
```

### Explanation:
- **Extract**: The `extract_data` function loads data from a CSV file into a Pandas DataFrame. Error handling ensures that file-related errors are caught and reported.
- **Transform**: The `transform_data` function represents where you can make changes to the data, such as cleaning, normalizing, or otherwise altering it to meet your needs. It includes error handling for data operation issues.
- **Load**: The `load_data` function connects to a SQLite database and loads the transformed data into a specified table. SQLAlchemy is used for database interaction with added error handling.
- **Main Function**: It coordinates the ETL process by calling the extract, transform, and load functions in sequence.

This script requires the CSV file `data.csv` to be in the working directory and assumes that the structure and data contained would be valid for these operations. You may need to adapt the transformation logic and database connection details to your specific use case. Additionally, you can expand this pipeline by integrating visualization libraries like Matplotlib or Seaborn to create visual analysis of your data.