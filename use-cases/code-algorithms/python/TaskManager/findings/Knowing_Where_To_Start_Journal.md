Exercise Part 1: Understanding Project Structure

I began by reading the README.md and exploring the directory structure using the tree command. The project contained three language implementations, and I chose to focus on the Python version located at use-cases/code-algorithms/python/TaskManager.

From the filenames—cli.py, models.py, task_manager.py, and storage.py—I concluded the project follows a layered design. The presence of a tests/ folder suggested test coverage, which I planned to use to explore behavior and verify assumptions.

My Hypotheses About the Codebase:

    cli.py is the main entry point handling user commands, likely using argparse.

    task_manager.py handles the core business logic.

    models.py defines the data model classes such as Task, TaskStatus, and TaskPriority.

    storage.py manages persistence, storing tasks in JSON, and includes export functionality.

    Utility modules assist with parsing and priority handling.

AI Assistance / Exploration:

I used AI prompts to confirm these architectural guesses. The assistant helped clarify the role of task_manager.py as the coordinator connecting CLI commands to storage operations. Reviewing test files further supported understanding of expected behaviors.

What I Learned or Refined:

I realized task_manager.py is the central logic layer, gluing user input and persistence. The app uses JSON for storage and custom serialization for complex objects like Enums and datetime. The CLI delegates all data-related operations to the manager and storage layers.

What I’ll Explore Next:

I planned to trace the flow of a typical user operation (e.g., creating a task) from CLI input through the task manager to storage, to document the full interaction path.
Exercise Part 2: Finding Feature Implementation (Task Export to CSV)

Initial Search:

    I found file operations managing JSON in storage.py.

    The method export_tasks_to_csv(filename="tasks.csv") exists in TaskStorage.

    No other export-related logic was found elsewhere.

Hypotheses:

    CSV export is handled entirely in storage.py.

    A new CLI command export should trigger export logic.

    The CSV output should contain key fields like ID, title, description, status, priority, and due date.

AI Guidance Plan:

    Confirmed that storage.py is the right place for export functionality.

    CLI should have an export subcommand with an optional filename argument.

    Serialization can reuse task attribute access.

Plan for CSV Feature Implementation:

    Implement export_tasks_to_csv in TaskStorage.

    Add export_tasks method in task_manager.py that delegates to storage.

    Add export CLI command in cli.py.

    Test by exporting tasks and verifying the CSV file contents.

Exercise Part 3: Understanding Domain Model
Step 1: Extract Domain Model

    Entities:

        Task: the main work unit.

        TaskStatus: Enum (TODO, IN_PROGRESS, REVIEW, DONE).

        TaskPriority: Enum (LOW, MEDIUM, HIGH, URGENT).

    Business Logic:

        Marking a task as done updates completed_at.

        Overdue tasks determined by due_date vs. current date.

        Status and priority drive task sorting and visibility.

        Tags can be added/removed.

        Optional fields include due_date, tags, and completed_at.

    Terminology:

        Task = work item.

        Status = progress stage.

        Priority = urgency level.

        Tags = custom labels.

        Overdue = past due date and not done.

Step 2: Form Initial Understanding

    Entity Diagram (textual):

    Task
      id: str
      title: str
      description: str
      status: TaskStatus
      priority: TaskPriority
      due_date: datetime
      created_at: datetime
      updated_at: datetime
      completed_at: datetime (optional)
      tags: List[str]

    No multi-user or project abstractions exist yet.

    Task data is persisted through storage.py.

Explanation:

A Task is a structured object representing a unit of work with lifecycle tracking and optional metadata. Status and priority inform task handling and UI presentation. Tags and due dates support categorization and scheduling.

Uncertainties:

    Is this app designed for single or multi-user?

    How should abandoned tasks be handled?

    Should urgent tasks get special UI treatment?

Step 3: Knowledge Check — AI Questions & Answers

    Q1: What determines if a task is overdue?
    A: If the due_date is in the past and the task is not marked DONE.

    Q2: How is task completion tracked?
    A: Via the completed_at timestamp set when marking the task done.

    Q3: Why use enums over plain strings or numbers?
    A: Enums ensure type safety, prevent invalid values, improve CLI integration, and enhance maintainability.

Exercise Part 4: Practical Application — Auto-Abandon Overdue Tasks

Scenario:
Implement a business rule:
Tasks overdue by more than 7 days should be automatically marked as abandoned, unless they are high or urgent priority.

Planning:

    Files to Modify:

        models.py: Add ABANDONED status to TaskStatus.

        task_manager.py: Add abandon_old_tasks() method.

        cli.py: Add auto-abandon CLI command.

        Optionally storage.py if tracking abandonment time.

    Outline of Changes:

        Extend TaskStatus enum with ABANDONED.

        Implement logic to scan all tasks, find overdue ones >7 days, check priority, and mark as ABANDONED if eligible.

        CLI command to trigger this process and print how many tasks were abandoned.

    Questions for the Team:

        Should abandoned tasks show in normal lists or be hidden?

        Can abandoned tasks be reactivated or edited?

        Should notifications be sent when auto-abandon runs?

        When/how should this logic be invoked (manually vs. automated)?

Reflection:

    How AI Helped:
    AI prompting clarified where business logic belongs and reinforced the layered structure (CLI → manager → storage).

    Remaining Uncertainties:
    Whether background scheduling exists, and how tests verify this new feature.

    Next Steps:
    Write tests for the new method, confirm UI handling of abandoned tasks, and consider a reactivation feature.

Final Discussion and Reflection

Group Discussion:
I approached the codebase by exploring its structure and key files, then confirmed my understanding with AI and test cases. Challenges included mapping responsibilities across layers and understanding data flow. Comparing approaches, I found that combining code reading with running the app gave me the most insight.

Personal Reflection:
The AI prompt that tested my domain model understanding was the most useful. Next time, I would first try running core features to ground my exploration. IDE tools and UML visualization would accelerate comprehension. Combining AI with tests would provide a robust feedback loop for verifying assumptions.