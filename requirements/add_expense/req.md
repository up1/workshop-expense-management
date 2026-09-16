# Feature: Add Expense

## Description
This feature allows users to add a new expense to their expense tracker from bank slip file.


## Requirements
- Users must be able to upload a bank slip file.
- The system must extract expense details from the bank slip file
  * Use Anthropic API to extract expense details from the bank slip file
- Users must be able to review and edit the extracted expense details before saving.
- The system must validate the extracted expense details.
- The system must save the expense to the database after user confirmation.

## Inputs validation

| Input Field | Validation Rule | Error Message |
|-------------|-----------------|---------------|
| Bank slip file | Must be a valid file format (e.g., PDF, JPG, PNG) | "Invalid file format" |
| Expense amount | Must be a positive number | "Expense amount must be a positive number" |
| Expense date | Must be a valid date | "Invalid date" |
| Expense category | Must be selected from predefined categories | "Please select a valid category" |
| Expense description | Must not be empty | "Expense description cannot be empty" |
| Duplicate expense | Must not already exist in the database | "Duplicate expense detected" |

## Test Cases in table format
| Test Case ID | Description | Precondition | Test Steps | Expected Result |
|--------------|-------------|--------------|------------|----------------|
| TC01 | Upload bank slip file | User is on the add expense page | 1. Click on upload button 2. Select bank slip file | System extracts expense details from the bank slip file |
| TC02 | Review and edit extracted expense details | Expense details are extracted | 1. Review extracted details 2. Edit details if necessary | System validates the edited expense details |
| TC03 | Save valid expense | Expense details are valid | 1. Click on save button | System saves the expense to the database |
| TC04 | Attempt to save invalid expense | Expense details are invalid | 1. Click on save button | System displays an error message |
| TC05 | Attempt to upload invalid bank slip file | User is on the add expense page | 1. Click on upload button 2. Select an invalid bank slip file | System displays an error message "Invalid file format" |


## API Endpoints

### 1. Read data from slip file with file upload (multipart/form-data)
* POST /api/expenses/upload
  - Request Body (multipart/form-data):
    - `bank_slip_file`: file
  - Response:
    ```json
    {
      "success": true,
      "expense_amount": "number",
      "expense_date": "date",
      "expense_category": "string",
      "expense_description": "string"
    }
    ```
* Success Response:
  - HTTP Status: 200 OK
  - Response Body:
    ```json
    {
      "success": true,
      "expense_amount": "number",
      "expense_date": "date",
      "expense_category": "string",
      "expense_description": "string"
    }
    ```
* Error Response:
  - HTTP Status: 400 Bad Request
  - Response Body:
    ```json
    {
      "success": false,
      "error": "Invalid file format"
    }
    ``` 
  - Error Response for Server Error:
    - HTTP Status: 500 Internal Server Error
    - Response Body:
      ```json
      {
        "success": false,
        "error": "Server error"
      }
      ```
### 2. Save expense to the database
* POST /api/expenses
  - Request Body (application/json):
    ```json
    {
      "expense_amount": "number",
      "expense_date": "date",
      "expense_category": "string",
      "expense_description": "string"
    }
    ```
  - Success Response:
    - HTTP Status: 200 OK
    - Response Body:
      ```json
      {
        "success": true
      }
      ```
  - Error Response:
    - HTTP Status: 400 Bad Request
    - Response Body:
      ```json
      {
        "success": false,
        "error": "Invalid expense details"
      }
      ```
    - HTTP Status: 409 Conflict
    - Response Body:
      ```json
      {
        "success": false,
        "error": "Duplicate expense detected"
      }
      ```
    - HTTP Status: 500 Internal Server Error
    - Response Body:
      ```json
      {
        "success": false,
        "error": "Server error"
      }
      ```