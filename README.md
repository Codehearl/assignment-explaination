## Tutorial on the Code for Predicting Student Progression Outcomes



### Importing Required Modules

```python
from graphics import *
```
We begin by importing the `graphics` module, which will be used for visualizing the data. This module provides functions to create windows, draw shapes, and display text.

### Constants for Progression Outcomes

```python
# Constants for progression outcomes
PROGRESS = "Progress"
TRAILER = "Progress (module trailer)"
RETRIEVER = "Do not Progress – module retriever"
EXCLUDE = "Exclude"
```
These constants represent the possible outcomes for students based on their credit scores:
- `PROGRESS`: Student has progressed.
- `TRAILER`: Student has progressed but needs to retake some modules.
- `RETRIEVER`: Student did not progress and needs to retake modules.
- `EXCLUDE`: Student is excluded from the program.

### Predicting Outcomes

```python
def predict_outcome(pass_credits, defer_credits, fail_credits):
    total_credits = pass_credits + defer_credits + fail_credits

    conditions = {
        (120, 0, 0): PROGRESS,
        (100, 20, 0): TRAILER,
        ...
        (0, 0, 120): EXCLUDE,
    }

    return conditions.get((pass_credits, defer_credits, fail_credits), "Total incorrect")
```
The `predict_outcome` function determines the student's progression outcome based on their credit scores:
- It uses a dictionary (`conditions`) to map specific combinations of credits to the corresponding outcomes.
- If the input credits do not match any key in the dictionary, it returns "Total incorrect".

### Displaying the Histogram

```python
def display_histogram(results):
    win = GraphWin("Histogram Results", 400, 300)
    ...
    win.getMouse()
    win.close()
```
The `display_histogram` function visualizes the outcomes using a bar chart:
- A window (`GraphWin`) is created for the histogram.
- Bars are drawn for each outcome, with colors representing different outcomes (`green` for `PROGRESS`, `yellow` for `TRAILER`, etc.).
- Labels are added for clarity, and the window waits for a mouse click before closing.

### Displaying Data from Part 2

```python
def display_part_2_data(data):
    print("Part 2:")
    for item in data:
        outcome, pass_credits, defer_credits, fail_credits = item
        print(f"{outcome} - {pass_credits}, {defer_credits}, {fail_credits}")
```
This function prints detailed data for each student:
- It iterates through a list of tuples (`data`), where each tuple contains the outcome and credit scores.
- Each student's data is printed in a readable format.

### Saving and Loading Data

```python
def save_to_file(data, filename):
    with open(filename, 'w') as file:
        for item in data:
            file.write(','.join(map(str, item)) + '\n')

def load_from_file(filename):
    data = []
    with open(filename, 'r') as file:
        for line in file:
            values = line.strip().split(',')
            data.append((values[0], int(values[1]), int(values[2]), int(values[3])))
    return data
```
These functions handle saving and loading data to/from a file:
- `save_to_file` writes each student's data as a comma-separated line in a text file.
- `load_from_file` reads the data from the text file and reconstructs it into a list of tuples.

### Main Function

```python
def main():
    results = {PROGRESS: 0, TRAILER: 0, RETRIEVER: 0, EXCLUDE: 0}
    progression_data = []
    ...
    display_histogram(results)
    display_part_2_data(progression_data)
    loaded_data = load_from_file('progression_data.txt')
    display_part_2_data(loaded_data)

if __name__ == "__main__":
    main()
```
The `main` function orchestrates the entire script:
- It initializes dictionaries and lists to store results and data.
- It prompts the user to enter credit scores, validating the inputs.
- It calculates the outcome using `predict_outcome` and updates the results.
- It displays a histogram and detailed data.
- Finally, it saves the data to a file, reloads it, and displays it again.

### Interacting with the User

Inside the `main` function, there's a loop that repeatedly prompts the user for input:

```python
while True:
    pass_credits = input("Enter your total PASS credits: ")
    if pass_credits.lower() == 'q':
        break
    ...
    if not (pass_credits.isdigit() and defer_credits.isdigit() and fail_credits.isdigit()):
        print("Integer required")
        continue
    ...
    if pass_credits + defer_credits + fail_credits != 120:
        print("Total incorrect")
        continue

    outcome = predict_outcome(pass_credits, defer_credits, fail_credits)
    print(outcome)
    results[outcome] += 1
    progression_data.append((outcome, pass_credits, defer_credits, fail_credits))

    continue_input = input("Would you like to enter another set of data? Enter 'y' for yes or 'q' to quit: ")
    if continue_input.lower() == 'q':
        save_to_file(progression_data, 'progression_data.txt')
        break
```
- It continuously asks for `pass_credits`, `defer_credits`, and `fail_credits`.
- If the user inputs 'q', the loop breaks.
- It validates the inputs to ensure they are integers and within the allowed range.
- It calculates and prints the outcome, then updates the results and progression data.
- If the user decides to quit, the data is saved to a file and the loop exits.

### Key Decisions in the Code and Their Rationale

1. **Using Constants for Outcomes**:
   ```python
   PROGRESS = "Progress"
   TRAILER = "Progress (module trailer)"
   RETRIEVER = "Do not Progress – module retriever"
   EXCLUDE = "Exclude"
   ```
   **Decision Rationale**:
   - **Readability and Maintainability**: Defining constants for outcomes improves code readability and maintainability. It ensures that changes to these values are centralized and consistent throughout the code.

   **Alternatives**:
   - **Direct Strings**: Instead of constants, direct strings could be used. However, this would make the code harder to manage and more prone to errors due to typos.
   - **Enumeration**: Using Python's `enum` module could provide a more structured approach, encapsulating related constants in a single enumeration class.

2. **Using a Dictionary for Outcome Conditions**:
   ```python
   conditions = {
       (120, 0, 0): PROGRESS,
       ...
       (0, 0, 120): EXCLUDE,
   }
   ```
   **Decision Rationale**:
   - **Efficiency**: A dictionary provides an efficient way to map specific credit combinations to outcomes.
   - **Clarity**: It clearly defines all possible conditions in a single, concise structure.

   **Alternatives**:
   - **If-Else Statements**: Using a series of if-else statements to check conditions. This approach would be more verbose and less efficient.
   - **Data Structures**: Implementing a more complex data structure, like a decision tree, could also work but would be overkill for the number of conditions here.

3. **Validation of Input Credits**:
   ```python
   if not (pass_credits.isdigit() and defer_credits.isdigit() and fail_credits.isdigit()):
       print("Integer required")
       continue

   if pass_credits not in {0, 20, 40, 60, 80, 100, 120} or \
      defer_credits not in {0, 20, 40, 60, 80, 100, 120} or \
      fail_credits not in {0, 20, 40, 60, 80, 100, 120}:
       print("Out of range")
       continue

   if pass_credits + defer_credits + fail_credits != 120:
       print("Total incorrect")
       continue
   ```
   **Decision Rationale**:
   - **Data Integrity**: Ensures that inputs are valid integers within the specified range and that the total credits sum to 120, maintaining data integrity.
   - **User Feedback**: Provides immediate feedback to users, guiding them to correct their inputs.

   **Alternatives**:
   - **Form-based Input**: Using a graphical user interface (GUI) with form controls that restrict input types and ranges would reduce the need for manual validation.
   - **Exception Handling**: Using exception handling (try-except blocks) to catch invalid inputs might simplify validation logic.

4. **Graphical Representation of Data**:
   ```python
   def display_histogram(results):
       win = GraphWin("Histogram Results", 400, 300)
       ...
       win.getMouse()
       win.close()
   ```
   **Decision Rationale**:
   - **Visualization**: Graphical representation of data helps in better understanding and analysis of the results.
   - **Interactivity**: The graphics module allows for interactive windows, making the visualization more engaging.

   **Alternatives**:
   - **Text-based Output**: Using a simple text-based representation (like printing asterisks for each result) would be easier but less informative.
   - **Advanced Libraries**: Utilizing advanced libraries like `matplotlib` or `seaborn` for plotting could provide more sophisticated and customizable visualizations.

5. **File Handling for Data Persistence**:
   ```python
   def save_to_file(data, filename):
       with open(filename, 'w') as file:
           for item in data:
               file.write(','.join(map(str, item)) + '\n')

   def load_from_file(filename):
       data = []
       with open(filename, 'r') as file:
           for line in file:
               values = line.strip().split(',')
               data.append((values[0], int(values[1]), int(values[2]), int(values[3])))
       return data
   ```
   **Decision Rationale**:
   - **Persistence**: Saving and loading data from a file ensures that data is not lost between program executions.
   - **Simplicity**: Text files provide a simple and straightforward way to handle data persistence.

   **Alternatives**:
   - **Database**: Using a database (like SQLite) would be more robust and scalable for larger datasets.
   - **JSON or XML**: Using JSON or XML formats would make the data more structured and easier to manipulate, especially if more complex data is involved.

6. **Main Function Structure**:
   ```python
   def main():
       results = {PROGRESS: 0, TRAILER: 0, RETRIEVER: 0, EXCLUDE: 0}
       ...
       if __name__ == "__main__":
           main()
   ```
   **Decision Rationale**:
   - **Modularity**: Encapsulating the main logic in a function (`main`) makes the code modular and easier to test.
   - **Entry Point**: The `if __name__ == "__main__":` block ensures that the script runs when executed directly, but not when imported as a module.

   **Alternatives**:
   - **Script without Functions**: Writing the entire script without functions would be simpler for small scripts but less modular and harder to maintain.
   - **Object-Oriented Approach**: Using classes and objects to encapsulate data and functionality would provide a more structured and scalable approach, especially for larger projects.
