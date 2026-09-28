# To-Do-List

A simple  task management application built with **Python**.

The application allows users to create, update, delete, and manage tasks directly from the terminal. Tasks are stored permanently in a local `Tasks.json` file.

The project is designed to practice:

* Python functions
* Command-line arguments
* `argparse`
* JSON data storage
* File handling
* Error handling
* Modular programming
* Separation of concerns

---

## Features

The Task Tracker supports the following operations:

* Add a new task
* Update an existing task
* Delete a task
* Mark a task as **in progress**
* Mark a task as **done**
* List all tasks
* List completed tasks
* List incomplete tasks
* List tasks that are in progress
* Automatically generate task IDs
* Store tasks permanently in a JSON file
* Automatically create the JSON file when needed
* Handle invalid JSON and file errors gracefully

---

# Requirements

The application follows these requirements:

### 1. Command-Line Application

The application runs completely from the command line.

Example:

```bash
python main.py add "Learn Python"
```

---

### 2. Positional Arguments

User input is accepted using **positional command-line arguments**.

Examples:

```bash
python main.py add "Learn Python"
```

```bash
python main.py update 1 "Learn Advanced Python"
```

```bash
python main.py delete 1
```

The application uses Python's built-in `argparse` module to process these arguments.

---

### 3. JSON Storage

Tasks are stored in:

```text
Tasks.json
```

The JSON file is located in the current project directory.

Example:

```json
[
    {
        "id": 1,
        "description": "Learn Python",
        "status": "todo",
        "createdAt": "2026-09-19T15:00:00",
        "updatedAt": "2026-09-19T15:00:00"
    }
]
```

---

### 4. Automatic JSON File Creation

The application does not require the user to manually create `Tasks.json`.

If the file does not exist, the application treats it as an empty task list.

```python
if not os.path.exists(FILE_NAME):
    return []
```

When the first task is saved, the application creates the JSON file automatically.

---

### 5. Native File System

The project uses Python's built-in file handling:

```python
open()
```

and the built-in:

```python
os
```

module.

No external file-storage library is required.

---

### 6. No External Libraries

The application uses only Python's standard library.

Main modules:

```text
argparse
json
os
sys
datetime
```

No external frameworks or third-party packages are required.

---

# Project Structure

```text
task_tracker/
│
├── main.py
├── cli.py
├── tasks.py
├── storage.py
├── utils.py
├── Tasks.json
└── README.md
```

---

# Architecture

The project follows a simple separation-of-concerns architecture.

```text
                    main.py
                       │
                       ▼
                    cli.py
                       │
              Parses user commands
                       │
                       ▼
                   tasks.py
                       │
             Task business logic
                       │
              ┌────────┴────────┐
              ▼                 ▼
          utils.py          storage.py
       Helper functions     JSON handling
                                │
                                ▼
                           Tasks.json
```

---

# File Responsibilities

## `main.py`

The entry point of the application.

Its only responsibility is to start the CLI.

```python
from cli import run_cli


if __name__ == "__main__":
    run_cli()
```

---

## `cli.py`

Responsible for:

* Creating the argument parser
* Defining commands
* Accepting positional arguments
* Validating command choices
* Calling the appropriate task function

Example:

```bash
python main.py add "Learn Python"
```

`cli.py` understands:

```text
command = add
description = Learn Python
```

---

## `tasks.py`

Contains the application's main task-management logic.

Responsible for:

* Creating tasks
* Updating tasks
* Deleting tasks
* Changing task status
* Listing tasks

This file does not directly manage the JSON file.

Instead, it uses functions from `storage.py`.

---

## `storage.py`

Responsible for persistent data storage.

It contains:

```python
load_tasks()
```

and:

```python
save_tasks()
```

These functions handle:

* Reading `Tasks.json`
* Writing `Tasks.json`
* Checking whether the file exists
* Detecting invalid JSON
* Handling file-system errors

---

## `utils.py`

Contains reusable helper functions.

Examples:

```python
get_next_id()
```

```python
find_task()
```

```python
display_task()
```

```python
current_time()
```

Keeping these functions separately prevents unnecessary duplication.

---

# Task Data Model

Each task is represented as a Python dictionary.

Example:

```python
{
    "id": 1,
    "description": "Learn Python",
    "status": "todo",
    "createdAt": "2026-09-19T15:00:00",
    "updatedAt": "2026-09-19T15:00:00"
}
```

The complete collection of tasks is stored as a Python list.

```python
[
    {
        "id": 1,
        "description": "Learn Python",
        "status": "todo"
    },
    {
        "id": 2,
        "description": "Build AI Project",
        "status": "in-progress"
    }
]
```

---

# Task Statuses

The application uses three task statuses:

```text
todo
in-progress
done
```

### `todo`

The task has been created but work has not started.

### `in-progress`

The user is currently working on the task.

### `done`

The task has been completed.

---

# Task ID Logic

Every task receives a unique numeric ID.

If there are no tasks:

```python
return 1
```

If existing IDs are:

```text
1
2
3
```

the next ID becomes:

```text
4
```

The application determines this using:

```python
max(task["id"] for task in tasks) + 1
```

This allows tasks to be identified easily from the command line.

---

# Commands

## Add Task

Create a new task:

```bash
python main.py add "Learn Python"
```

Example output:

```text
Task added successfully (ID: 1)
```

---

## List All Tasks

```bash
python main.py list
```

Example:

```text
[1] Learn Python (todo)
[2] Build AI Project (in-progress)
[3] Learn SQL (done)
```

---

## Update Task

Update the description of an existing task:

```bash
python main.py update 1 "Learn Advanced Python"
```

Example output:

```text
Task 1 updated successfully.
```

---

## Delete Task

Delete a task:

```bash
python main.py delete 1
```

Example output:

```text
Task 1 deleted successfully.
```

---

## Mark Task as In Progress

```bash
python main.py mark-in-progress 1
```

Example output:

```text
Task 1 marked as in-progress.
```

---

## Mark Task as Done

```bash
python main.py mark-done 1
```

Example output:

```text
Task 1 marked as done.
```

---

# Filtering Tasks

## List Completed Tasks

```bash
python main.py list done
```

Example:

```text
[3] Learn SQL (done)
```

---

## List In-Progress Tasks

```bash
python main.py list in-progress
```

Example:

```text
[2] Build AI Project (in-progress)
```

---

## List Not-Done Tasks

```bash
python main.py list not-done
```

The `not-done` filter represents tasks that have not reached the `done` status.

Therefore, it should include:

```text
todo
in-progress
```

and exclude:

```text
done
```

---

# Complete Command Reference

| Command                              | Purpose               |
| ------------------------------------ | --------------------- |
| `python main.py add "Task"`          | Add a task            |
| `python main.py list`                | List all tasks        |
| `python main.py list done`           | List completed tasks  |
| `python main.py list not-done`       | List incomplete tasks |
| `python main.py list in-progress`    | List active tasks     |
| `python main.py update ID "Task"`    | Update a task         |
| `python main.py delete ID`           | Delete a task         |
| `python main.py mark-in-progress ID` | Mark task in progress |
| `python main.py mark-done ID`        | Mark task done        |

---

# Error Handling

The application handles common errors gracefully.

## Task Does Not Exist

Example:

```bash
python main.py update 99 "New Task"
```

If task `99` does not exist:

```text
Error: Task with ID 99 not found.
```

The application does not crash.

---

## Invalid JSON

If `Tasks.json` contains malformed JSON:

```text
Error: Tasks.json contains invalid JSON.
```

The application stops instead of continuing with corrupted data.

---

## Incorrect JSON Structure

The application expects `Tasks.json` to contain a list.

Valid:

```json
[]
```

Invalid:

```json
{}
```

If the JSON root is not a list:

```text
Error: Tasks.json must contain a list of tasks.
```

---

## File-System Errors

Problems such as permission errors are handled using:

```python
except OSError as error:
```

The user receives a meaningful error message instead of an unhandled Python traceback.

---

# Example Workflow

A typical session could look like this:

### Step 1 — Create a task

```bash
python main.py add "Learn Python"
```

Output:

```text
Task added successfully (ID: 1)
```

### Step 2 — Create another task

```bash
python main.py add "Learn SQL"
```

Output:

```text
Task added successfully (ID: 2)
```

### Step 3 — View tasks

```bash
python main.py list
```

Output:

```text
[1] Learn Python (todo)
[2] Learn SQL (todo)
```

### Step 4 — Start working

```bash
python main.py mark-in-progress 1
```

Output:

```text
Task 1 marked as in-progress.
```

### Step 5 — View active tasks

```bash
python main.py list in-progress
```

Output:

```text
[1] Learn Python (in-progress)
```

### Step 6 — Complete the task

```bash
python main.py mark-done 1
```

Output:

```text
Task 1 marked as done.
```

### Step 7 — View completed tasks

```bash
python main.py list done
```

Output:

```text
[1] Learn Python (done)
```

---

# Data Flow

When the user adds a task:

```text
Terminal
   │
   │ python main.py add "Learn Python"
   ▼
main.py
   │
   ▼
cli.py
   │
   │ Parses arguments
   ▼
tasks.py
   │
   │ Creates task
   ▼
storage.py
   │
   │ Saves data
   ▼
Tasks.json
```

When the user lists tasks:

```text
Terminal
   │
   ▼
main.py
   │
   ▼
cli.py
   │
   ▼
tasks.py
   │
   ▼
storage.py
   │
   │ Reads JSON
   ▼
Tasks.json
   │
   ▼
tasks.py
   │
   ▼
Terminal
```

---

# Installation

No external packages are required.

Make sure Python is installed:

```bash
python --version
```

Clone or download the project.

Navigate to the project directory:

```bash
cd task_tracker
```

Run:

```bash
python main.py
```

---

# Dependencies

This project uses only Python's standard library.

```text
Python 3
├── argparse
├── json
├── os
├── sys
└── datetime
```

No:

* Flask
* Django
* FastAPI
* SQL database
* External packages
* Third-party frameworks

are required.

---

# Design Principles

The project follows several basic software-engineering principles.

## Separation of Concerns

Each file has a specific responsibility.

```text
main.py      → Application entry point
cli.py       → CLI interface
tasks.py     → Business logic
storage.py   → Data persistence
utils.py     → Helper functions
```

---

## Single Responsibility

A function should primarily have one job.

For example:

```python
load_tasks()
```

loads tasks.

```python
save_tasks()
```

saves tasks.

```python
find_task()
```

finds a task.

This makes the application easier to understand and maintain.

---

## Persistent Storage

Tasks are not stored only in memory.

They are written to:

```text
Tasks.json
```

Therefore, closing and reopening the application does not remove the tasks.

---

# Future Improvements

Possible improvements for future versions include:

* Add task priorities
* Add due dates
* Add task categories
* Search tasks
* Sort tasks
* Add colored terminal output
* Add confirmation before deleting
* Add automated tests
* Add logging
* Add configuration support
* Export tasks to CSV
* Add recurring tasks
* Package the application as an executable

These are intentionally outside the current requirements.

---

# Project Goal

The main goal of this project is to build a functional CLI application while practicing fundamental Python concepts:

```text
Python
   │
   ├── Functions
   ├── Lists
   ├── Dictionaries
   ├── Loops
   ├── Conditions
   ├── Exceptions
   ├── File Handling
   ├── JSON
   ├── Modules
   └── Command-Line Arguments
```

The project also demonstrates how a small application can be divided into multiple modules instead of putting all logic into one large Python file.

---

# License

This project is intended for learning and educational purposes.

```

**One implementation detail to fix before you commit:** make `list not-done` include both `todo` **and** `in-progress`, because the requirement says “not done,” not merely “todo.” This keeps the code aligned with the original specification.
```
