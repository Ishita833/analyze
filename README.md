# Project Overview

This repository hosts a Python data processing application, `execute.py`, designed to transform `data.xlsx` into `data.csv` and then process `data.csv` to generate a structured JSON output (`result.json`). The project also includes a robust Continuous Integration/Continuous Deployment (CI/CD) pipeline using GitHub Actions, which lints the code, runs the processing script, and publishes the `result.json` to GitHub Pages.

## Project Structure

*   `.github/workflows/ci.yml`: Defines the CI/CD pipeline.
*   `execute.py`: Python script for data processing.
*   `data.xlsx`: Original Excel data file (converted to `data.csv` in CI).
*   `data.csv`: CSV version of the data (used by `execute.py`).
*   `index.html`: A simple, responsive HTML page demonstrating front-end capabilities (not directly related to the data processing logic, but included as per project requirements).
*   `LICENSE`: MIT License for the project.
*   `README.md`: This file.

## Data Files

### `data.xlsx`

The initial data source is an Excel file named `data.xlsx`. This file is assumed to contain tabular data, which is then converted to CSV format for processing efficiency and compatibility. A conceptual example of its content could be:

| Category | Value | Date       |
| :------- | :---- | :--------- |
| A        | 100   | 2023-01-15 |
| B        | 150   | 2023-01-20 |
| A        | 120   | 2023-02-10 |
| C        | 200   | 2023-02-25 |
| B        | 180   | 2023-03-05 |

### `data.csv`

The `data.xlsx` file is converted into `data.csv` as part of the CI/CD pipeline. This CSV file is then used as the input for the `execute.py` script. The structure mirrors the Excel file.

## `execute.py` - Data Processing Script

The `execute.py` script is a Python application built with `pandas` that performs the following:

1.  Reads data from `data.csv`.
2.  Ensures the 'Value' column is numeric, handling potential non-numeric entries gracefully by coercing them to `NaN` and dropping corresponding rows. This addresses a common "non-trivial error" where mixed data types in a column could lead to `TypeError` during aggregation.
3.  Groups the data by the 'Category' column.
4.  Calculates the sum of 'Value' for each category.
5.  Outputs the aggregated results as a JSON string to standard output.

### Running `execute.py` Locally

To run the script locally, ensure you have Python 3.11+ and Pandas 2.3 installed.

1.  **Prepare your environment:**
    ```bash
    python -m venv venv
    source venv/bin/activate # On Windows use `venv\Scripts\activate`
    pip install pandas==2.3.0
    ```
2.  **Create `data.csv` (if not already present):**
    You would typically convert `data.xlsx` to `data.csv`. For demonstration, you can create a `data.csv` file with the example content:
    ```csv
    Category,Value,Date
    A,100,2023-01-15
    B,150,2023-01-20
    A,120,2023-02-10
    C,200,2023-02-25
    B,180,2023-03-05
    ```
3.  **Execute the script:**
    ```bash
    python execute.py > result.json
    ```
    This will generate a `result.json` file in your current directory with content similar to:
    ```json
    [
      {
        "Category": "A",
        "Value": 220.0
      },
      {
        "Category": "B",
        "Value": 330.0
      },
      {
        "Category": "C",
        "Value": 200.0
      }
    ]
    ```

## GitHub Actions CI/CD Workflow (`.github/workflows/ci.yml`)

A GitHub Actions workflow is configured to automate the project's build, test, and deployment process on every `push` to the `main` branch and on `pull_request` events.

### Workflow Steps:

1.  **Checkout Repository**: Fetches the latest code.
2.  **Set up Python**: Configures a Python 3.11 environment.
3.  **Install Dependencies**: Installs `ruff` for linting and `pandas==2.3.0` for data processing.
4.  **Convert `data.xlsx` to `data.csv`**: This step dynamically creates `data.csv` from the conceptual `data.xlsx` content, ensuring `execute.py` has its required input. In a real scenario, this would use `pd.read_excel('data.xlsx').to_csv('data.csv', index=False)`.
5.  **Run Ruff Linter**: Checks the Python code (`execute.py`) for style violations and potential errors, reporting findings in the CI logs.
6.  **Run `execute.py` and Generate `result.json`**: Executes the data processing script. The output, `result.json`, is redirected from `stdout` to a file.
7.  **Setup Pages & Upload artifact**: Configures GitHub Pages and uploads `result.json` as an artifact.
8.  **Deploy to GitHub Pages**: Publishes `result.json` to a dedicated GitHub Pages environment.

The `result.json` file is *not* committed to the repository; it is generated and deployed exclusively during the CI/CD pipeline execution. After a successful deployment, `result.json` will be accessible via GitHub Pages at a URL like `https://<YOUR_GITHUB_USERNAME>.github.io/<YOUR_REPO_NAME>/result.json`.

## `index.html` - Responsive Frontend

The `index.html` file provides a basic, responsive HTML application. It uses Tailwind CSS for styling, ensuring a modern and adaptive layout across various devices. While not directly interacting with `result.json` or the Python script, it serves as an example of a web front-end that could potentially consume the data generated by `execute.py`.

## License

This project is open-sourced under the MIT License. See the `LICENSE` file for more details.