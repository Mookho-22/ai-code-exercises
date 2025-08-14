Algorithm 1: Task Priority Sorting Algorithm
Objective

Understand how tasks are scored and sorted based on priority, due date, completion status, and tags, to present the most important tasks first.
Algorithm Summary

    Calculate Task Score:
    Each task gets a numeric score derived from:

        Base priority weight multiplied by 10

        Bonus points if due soon or overdue

        Penalties if task is completed or under review

        Bonus points for certain tags (blocker, critical, urgent)

        Bonus if updated recently (within 1 day)

    Sorting Tasks:
    Tasks are sorted by descending score to prioritize the most urgent and important ones.

Step-by-Step Breakdown

    Priority Weights:

        LOW = 1

        MEDIUM = 2

        HIGH = 4

        URGENT = 6

    Base Score:
    score = priority_weight * 10

    Due Date Adjustment:

        Overdue: +35 points

        Due today: +20 points

        Due in next 2 days: +15 points

        Due in next 7 days: +10 points

    Completion Status Penalties:

        DONE: -50 points (low priority to exclude completed tasks)

        REVIEW: -15 points (lower priority during review)

    Tag Bonus:
    +8 points if any tag is in ["blocker", "critical", "urgent"]

    Recent Update Bonus:
    +5 points if updated within the last day

Code Flow

    calculate_task_score(task): Returns numeric score based on above rules.

    sort_tasks_by_importance(tasks): Sorts tasks by score descending.

    get_top_priority_tasks(tasks, limit=5): Returns the top N tasks after sorting.

Insights & Learnings

    Combining multiple factors into one score lets us balance urgency, importance, and completion status effectively.

    Penalizing completed tasks ensures they don't clutter active task lists.

    Tags allow flexible tagging to bump priority dynamically.

    The algorithm is extensible with new factors easily added.

Reflection Questions

    How did AI explanations help?
    Clarified the rationale behind weighting and how factors combine into a single score.

    Challenges?
    Understanding the trade-offs between different priority factors and penalty values.

    How to explain to junior dev?
    "We assign points to tasks based on how important and urgent they are, then sort so the highest scoring tasks appear first."

    Possible improvements?
    Dynamically adjusting weights based on user behavior or deadlines; factoring in estimated task duration.




Algorithm 2: Task Text Parser
Objective

Parse free-form user input text into structured task properties (title, priority, due date, tags).
Algorithm Summary

    User inputs text like:
    "Finish report !urgent @work #friday"

    Parsing rules:

        !N or !urgent/!high/!medium/!low sets priority

        @tag adds tags

        #date sets due date, including keywords like today, tomorrow, weekdays, or YYYY-MM-DD format

Step-by-Step Breakdown

    Initialize Defaults:

        Title = full text initially

        Priority = MEDIUM

        Tags = []

        Due date = None

    Extract Priority:

        Regex search for ! markers

        Map to enum TaskPriority

        Remove from title text

    Extract Tags:

        Regex search for @ markers

        Collect tags list

        Remove from title

    Extract Due Dates:

        Regex search for # markers

        Remove from title

        Interpret known keywords: today, tomorrow, weekdays, next_week

        Parse ISO date if provided

    Clean Title:
    Remove extra spaces

    Create Task:
    Instantiate a Task object with parsed values.

Helper Functions

    get_next_weekday(current_date, weekday): Returns the next occurrence of a weekday (Monday=0, etc).

Insights & Learnings

    The parser enables flexible natural language input for quick task creation.

    Regular expressions effectively identify markers for priority, tags, and dates.

    Keyword interpretation for dates improves usability.

Reflection Questions

    How did AI explanations help?
    Broke down regex usage and parsing logic into manageable chunks.

    Challenges?
    Ensuring robust parsing in all input variations and preventing accidental removal of words.

    How to explain to junior dev?
    "We look for special markers in the text like !urgent or @work to figure out how to set task properties."

    Possible improvements?
    Add natural language parsing for more complex date phrases (e.g., “next Friday afternoon”), handle multiple priorities/tags more gracefully.