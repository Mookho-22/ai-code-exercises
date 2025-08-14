Part 4: Reflection

High-Level Application Architecture Overview

The task manager application follows a clean, layered architecture comprising:

    CLI Layer (cli.py): Handles user input and commands, serving as the entry point for task operations.

    Business Logic Layer (task_manager.py): Contains core operations like creating tasks, updating status/priority, and applying business rules (e.g., auto-abandon).

    Domain Models (models.py): Defines key entities such as Task, TaskStatus, and TaskPriority enums, encapsulating task properties and behaviors.

    Persistence Layer (storage.py): Manages data storage and retrieval, using JSON serialization with custom handling for enums and datetime objects.

This separation of concerns supports maintainability and clarity, where each layer has well-defined responsibilities.
How the Three Key Features Work

1. Task Creation and Status Updates

    The CLI collects input and calls TaskManager.create_task().

    A Task instance is created with default status TODO and stored via TaskStorage.

    Status updates happen through TaskManager.update_task_status(), which modifies the task’s status and timestamps before persisting changes.

2. Task Prioritization

    Priority is stored as an enum with levels LOW to URGENT.

    A scoring function combines priority level, due dates, tags, and recent updates to compute a dynamic task score.

    Tasks are sorted by this score to present the most important tasks first, influencing task lists and displays.

3. Task Completion and Auto-Abandon

    Marking tasks done sets their status to DONE and records the completion time.

    Overdue tasks older than 7 days that aren’t high priority can be automatically marked as ABANDONED.

    This auto-abandon feature helps keep task lists relevant and manageable by filtering out stale items.

Interesting Design Pattern / Approach Discovered

The use of custom JSON serialization and deserialization for complex domain models (enums and datetime fields) in storage.py stands out. This pattern allows seamless persistence of rich Python objects while preserving their full semantics when loaded back—without relying on a heavy database or ORM layer.
Challenges and How AI Helped

    Challenge: Understanding the layered flow of data—from CLI input through task creation, prioritization, storage, and status updates—was initially complex given multiple files and custom serialization.

    How AI Helped:

        Guided systematic code exploration with targeted prompts.

        Clarified the responsibilities of each module.

        Explained business rules embedded in enums and scoring functions.

        Validated understanding by answering conceptual questions.

        Helped map the data flow and identify points where state changes occur.