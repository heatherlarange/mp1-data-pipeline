My pipeline loads a raw dataset, validates it, cleans it according to settings in a YAML configuration file, and saves the cleaned result as a CSV along with a summary report. Data moves through it in a particular order. The pipeline.py reads the input, config, and output paths from the command line and checks that the input files exist. The data and config are then loaded, the data is validated, cleaned, and the result is saved. The code is organized as a src package with one module per stage, and pipeline.py acts connects them. data_loaders.py chooses a loader based on the file extension (CSV, JSON, or YAML) and raises an error for unsupported formats. data_validator.py checks that all required columns are present, removes rows whose numeric columns contain non-numeric values and converts those columns to a numeric type. data_processor.py applies the cleaning steps turned on in the config. It removes duplicate rows, drops rows with missing values, and removes outliers using the IQR or z-score method, and it also builds the cleaning report. Finally, data_output.py writes the cleaned DataFrame to disk, creating the output folder if needed, and utils.py handles logging setup and input-file checks.


Example Input:

python pipeline.py --input fixtures/sample_data.csv --output output/clean.csv --config config/config.yaml --verbose


Example Output:

21:17:49 DEBUG    __main__ - Arguments parsed: input=fixtures/sample_data.csv, config=config/config.yaml, output=output/clean.csv
21:17:49 INFO     src.utils - Input file validated: fixtures/sample_data.csv
21:17:49 INFO     src.utils - Input file validated: config/config.yaml
21:17:49 INFO     src.data_loaders - Loaded CSV file: fixtures\sample_data.csv (100 rows)
21:17:49 INFO     src.data_loaders - Loaded YAML file: config\config.yaml
21:17:49 WARNING  src.data_validator - Invalid numeric value in column 'rating' at row 94: 'not_available'
21:17:49 WARNING  src.data_validator - Invalid numeric value in column 'rating' at row 95: 'error'
21:17:49 DEBUG    src.data_validator - Validation complete: 98 valid rows, 2 rows removed
21:17:49 INFO     __main__ - Validation complete: 100 -> 98 rows
21:17:49 DEBUG    src.data_processor - remove_duplicates: 98 -> 96 rows
21:17:49 DEBUG    src.data_processor - handle_missing: 96 -> 94 rows
21:17:49 DEBUG    src.data_processor - rating: method=iqr, threshold=1.5, lower=43.625, upper=106.625, removed=2
21:17:49 INFO     __main__ - Processing complete: 100 -> 92 rows
21:17:49 DEBUG    src.data_output - Saved 92 rows to output\clean.csv
21:17:49 INFO     __main__ - Saved cleaned data to output\clean.csv
{'rows_before': 100,
 'rows_after': 92,
 'rows_removed': 8,
 'columns_before': 5,
 'columns_after': 5,
 'columns_removed': 0}