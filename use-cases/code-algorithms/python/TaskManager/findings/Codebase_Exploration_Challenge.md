Exercise Part 1 Recap: Task Priority in the Domain Model

From Part 1, we know the TaskPriority enum defines these levels:

    LOW = 1

    MEDIUM = 2

    HIGH = 3

    URGENT = 4

Priority informs task urgency and influences sorting and filtering.
Step 1: Initial Search for Priority-Related Code
Search Keywords

    priority

    sort

    sorted

    order

Files Explored

    models.py (already reviewed — defines priority enum)

    task_manager.py (main business logic)

    storage.py (filters by priority)

    cli.py (commands handling task listing)

Step 2: Findings
Location	Function / Section	Role of Priority
models.py	TaskPriority enum	Defines priority levels (numeric values 1-4).
storage.py	get_tasks_by_priority(priority)	Filters tasks by matching priority.
task_manager.py	calculate_task_score(task)	Uses priority weights in scoring and sorting.
task_manager.py	sort_tasks_by_importance(tasks)	Sorts tasks by calculated score (priority + factors).
task_manager.py	abandon_old_tasks()	Skips abandoning HIGH and URGENT priority tasks.
CLI commands (cli.py)	list_tasks	Supports filtering by priority.

Step 3: Understanding Priority's Role in Business Logic

    Priority is stored numerically using the Enum but accessed via Enum members for clarity.

    Filtering: The storage layer supports retrieving tasks of a specific priority.

    Sorting: There is no simple sort by priority only. Instead, tasks are scored based on priority and additional factors like due date, tags, recent updates, and status. This composite score drives ordering in sort_tasks_by_importance().

    Auto-abandon Rule: Tasks overdue by more than 7 days are marked abandoned unless priority is HIGH or URGENT, protecting critical tasks from being abandoned.

Step 4: Key Insights

    Priority influences sorting indirectly through a weighted scoring function rather than simple numeric comparison.

    Business rules explicitly treat HIGH and URGENT priority tasks differently, showing their importance.

    The priority system supports flexible, dynamic task ordering based on multiple factors beyond priority alone.

    Filtering and retrieval allow straightforward querying of tasks by priority.

Step 5: Misconceptions Corrected

    Initially thought tasks might be sorted purely by priority value—actually, scoring involves multiple dimensions.

    Expected auto-abandon to treat all overdue tasks equally; it excludes higher priority tasks as a safeguard.

    Filtering by priority happens at storage level; sorting with priority is handled at the business logic level.

Step 6: Next Steps

    Review CLI code to confirm how priority filters and sorting appear to the user.

    Consider if priority-related business rules should be extended (e.g., special UI treatment for URGENT tasks).

    Investigate if priority scoring weights should be adjustable or made configurable.

Reflection

This exploration helped me understand how priority is more than just a numeric label, but it is a critical factor combined with other task metadata to drive intelligent ordering and business rules.

Part 3: Mapping Data Flow When Marking Tasks Complete

Step 1: Identify Entry Points and Components

    CLI layer: User invokes a command like task done <task_id>, which triggers CLI handler in cli.py.

    Task Manager: CLI calls a method such as update_task_status(task_id, 'done') in task_manager.py.

    Storage Layer: Task Manager retrieves the task from storage (get_task(task_id)), modifies its status, and saves back.

    Task Model: The Task object has a mark_as_done() method that sets status to DONE, records completed_at, and updates updated_at.


Step 2: State Changes During Task Completion

    Status Change: From any status to TaskStatus.DONE.

    Timestamp Update: completed_at and updated_at set to current datetime.

    Storage Save: The updated task is persisted to JSON file through TaskStorage.save().

    Subsequent Queries: The task now appears as completed in listings and reports.


Step 3: Code References (Snippets)
Task model (models.py):

def mark_as_done(self):
    self.status = TaskStatus.DONE
    self.completed_at = datetime.now()
    self.updated_at = self.completed_at

TaskManager method (task_manager.py):

def update_task_status(self, task_id, new_status_value):
    new_status = TaskStatus(new_status_value)
    if new_status == TaskStatus.DONE:
        task = self.storage.get_task(task_id)
        if task:
            task.mark_as_done()
            self.storage.save()
            return True
    else:
        return self.storage.update_task(task_id, status=new_status)

Step 4: Data Flow Diagram (Conceptual)

User CLI Command "done <task_id>"
        
cli.py handler calls
        
TaskManager.update_task_status(task_id, 'done')
        
TaskStorage.get_task(task_id) → retrieve Task object
        
Task.mark_as_done() updates status & timestamps
        
TaskStorage.save() persists updated tasks to JSON
        
Completion reflected in subsequent queries

Step 5: Potential Points of Failure

    Invalid Task ID: get_task returns None if ID not found.

    File I/O Errors: save() may fail due to disk or permission issues.

    Race Conditions: If multiple updates occur simultaneously, last write wins.

    Status Enum Parsing: Invalid new_status_value may cause exceptions.

Step 6: Persistence Mechanism

    Task updates are serialized to JSON with custom encoder handling Enums and datetime.

    The entire task collection is saved as a list into the JSON file on each update.

    Loading reads the JSON and decodes into Task objects.

Reflection

Understanding the clear layered approach (CLI , Manager , Storage) helped visualize how data flows and state is managed. The explicit method mark_as_done() encapsulates status logic, promoting clean separation. Persistence by JSON is simple but may have scaling limits.