Exercise: Algorithm Deconstruction Challenge
Algorithm 3: Task List Merging (Two-Way Sync)
Objective

Understand how two separate task lists (from local and remote sources) are merged, resolving conflicts and synchronizing updates in a way that keeps both sources consistent.
Step-by-Step Explanation

    Collect All Unique Task IDs:
    Combine the keys from both local and remote task dictionaries to get a set of all unique task IDs.

    Iterate Over All Task IDs:

    For each task ID, check:

        Exists only locally:

            Add task to merged result

            Mark task to be created remotely (to_create_remote)

        Exists only remotely:

            Add task to merged result

            Mark task to be created locally (to_create_local)

        Exists in both:

            Use resolve_task_conflict() to reconcile differences and produce one merged task

            Add merged task to result

            Depending on conflict resolution, mark tasks for update either locally, remotely, or both

Conflict Resolution Logic (resolve_task_conflict)

    Start with a deep copy of the local task as the base.

    Compare updated_at timestamps:

        If remote task is newer:

            Copy title, description, priority, and due date from remote to merged task

            Mark local task for update

        Else:

            Keep local as base

            Mark remote for update

    Special handling for task completion status:

        If remote task is DONE but local isn’t:

            Mark merged task as DONE with remote’s completed timestamp

            Mark local for update

        If local task is DONE but remote isn’t:

            Keep local DONE status

            Mark remote for update

        If statuses differ but none are DONE:

            Most recent status wins

            Mark the other source for update accordingly

    Tags Merging:

        Union of tags from both tasks

        If merged tags differ from local, mark local for update

        If merged tags differ from remote, mark remote for update

    Update the merged task's updated_at to the latest timestamp between local and remote.

Insights & Learnings

    Two-way sync is complex: It must detect missing tasks in each source and propagate creations.

    Conflict resolution must be nuanced: Last update wins is a common strategy but special cases like completion status need custom rules.

    Tags need union merging: Unlike other fields, tags from both sources are combined rather than overwritten.

    Deep copies avoid mutating original tasks: This ensures safe merging without side effects.

Reflection Questions

    How did the AI explanation change your understanding?
    The AI helped clarify subtle conflict resolution steps, especially around completion status and tags.

    What was difficult to understand?
    How simultaneous updates with different fields are reconciled without losing data.

    Would you improve the algorithm?
    Possibly by adding better conflict detection for simultaneous edits or introducing a more explicit versioning system to reduce ambiguity.
