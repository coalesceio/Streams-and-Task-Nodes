<!-- markdownlint-disable MD033 -->
## Snowflake - Streams and Tasks Package - Brief Summary

-   **Work with Task:** Used for intermediate "Work" tables. It automates the cleaning and transformation of data in the middle of a pipeline using a scheduled Task.
-   **Dimension with Task:** Automates the loading of descriptive business data (e.g., Customers, Products). It uses a Task to ensure that attributes are kept up-to-date incrementally.
-   **Fact with Task:** Handles large-scale transactional data. This node uses a Task to process new metrics or events at regular intervals, ensuring large tables stay current without full reloads.
-   **Task DAG Create Root:** Defines the "parent" or starting point of a **Directed Acyclic Graph (DAG)**. This is the first task in a sequence that triggers all other dependent child tasks.
-   **Task DAG Resume Root:** A management node used to "start" or "enable" the entire chain of tasks. In Snowflake, tasks are created in a 'Suspended' state; this node ensures the pipeline is actually running.
-   **Stream:** Directly implements Snowflake's **Change Data Capture (CDC)**. It acts as a bookmark on a source table to track new Inserts, Updates, and Deletes without moving data itself.
-   **Stream and Insert or Merge:** A combined logic node. It reads the "delta" changes from a Stream and applies them to a target table using either a simple INSERT or a MERGE statement.
-   **Delta Stream Merge:** An advanced CDC node designed for high-precision synchronization. It uses the Stream’s metadata (like `METADATA$ACTION`) to ensure the target table perfectly mirrors the source, even when complex updates or deletes occur.
-   **Stream for Directory Table:** Specifically designed for **Unstructured Data**. It tracks changes to files (like PDFs or images) stored in a Snowflake Stage, allowing you to trigger processing as soon as a new file is uploaded.
-   **Insert or Merge with Task:** A general-purpose automation node. It encapsulates the SQL logic to update a target table and schedules it to run via a Task, either on a set interval or when data arrives in a stream.

**Summary:**
These nodes work together to create **Continuous Data Pipelines**. The **Stream** nodes capture the "Delta" (CDC), and the **Task** nodes provide the "Schedule" (Orchestration). By using these together, you achieve **Transactional Consistency**: data is only processed once, and the stream's offset only advances when the task successfully commits.

----

## Nodetypes Config Matrix

### Matrix 1: Task-Based Node Types

| Category | Feature | Work with Task | Dimension with Task | Fact with Task | Insert or Merge with Task | Task DAG Create Root | Task DAG Resume Root |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Development** | Development Mode | ✅ | ✅ | ✅ | ✅ | ⬜ | ⬜ |
| | Multi Source | ✅ | ✅ | ✅ | ✅ | ⬜ | ⬜ |
| | Create As | ⬜ | ⬜ | ⬜ | ✅ | ⬜ | ⬜ |
| **Logic** | Distinct / Group By All | ✅ | ✅ | ✅ | ✅ | ⬜ | ⬜ |
| | Order By | ✅ | ✅ | ✅ | ✅ | ⬜ | ⬜ |
| | Truncate Before | ✅ | ⬜ | ✅ | ✅ | ⬜ | ⬜ |
| **Keys** | Business / Table Keys | ⬜ | ✅ | ⬜ | ✅ | ⬜ | ⬜ |
| | Change Tracking (Type 2) | ⬜ | ✅ | ⬜ | ⬜ | ⬜ | ⬜ |
| | Record Date / Timestamp | ⬜ | ⬜ | ⬜ | ✅ | ⬜ | ⬜ |
| | Cluster Key | ✅ | ✅ | ✅ | ✅ | ⬜ | ⬜ |
| **Scheduling** | Warehouse / Serverless | ✅ | ✅ | ✅ | ✅ | ✅ | ⬜ |
| | Stream Has Data Flag | ✅ | ✅ | ✅ | ✅ | ✅ | ⬜ |
| | Multi-Stream Logic | ✅ | ✅ | ✅ | ⬜ | ✅ | ⬜ |
| | Schedule (Min / Cron) | ✅ | ✅ | ✅ | ✅ | ✅ | ⬜ |
| | Predecessor / Root Task | ✅ | ✅ | ✅ | ✅ | ⬜ | ✅ |

### Matrix 2: Stream-based Node Types

| Category | Feature | Stream and Insert or Merge | Delta Stream Merge | Stream | Stream for Directory Table |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **Source Support** | **Source: Table** | ✅ | ✅ | ✅ | ⬜ |
| | **Source: View** | ✅ | ✅ | ✅ | ⬜ |
| | **Source: Dynamic Table** | ✅ | ✅ | ✅ | ⬜ |
| | **Source: External Table** | ✅ | ✅ | ✅ | ⬜ |
| | **Source: Directory Table** | ⬜ | ⬜ | ⬜ | ✅ |
| **Development** | Development Mode | ✅ | ✅ | ⬜ | ⬜ |
| | Create As  | ✅ | ✅ | ⬜ | ⬜ |
| **Logic** | Distinct / Group By All | ✅ | ✅ | ⬜ | ⬜ |
| | Record Date / Timestamp | ✅ | ✅ | ⬜ | ⬜ |
| | Qualify Latest Record | ⬜ | ✅ | ⬜ | ⬜ |
| **Keys** | Business / Table Keys | ✅ | ✅ | ⬜ | ⬜ |
| | Cluster Key | ✅ | ✅ | ⬜ | ⬜ |
| **Stream Config** | Append Only Option | ✅ | ✅ | ✅ | ⬜ |
| | Show Initial Rows | ✅ | ✅ | ✅ | ⬜ |
| | Propagate Deletes | ✅ | ✅ | ⬜ | ⬜ |
| | Redeployment Behavior | ✅ | ✅ | ✅ | ✅ |
| **Scheduling** | Warehouse / Serverless | ✅ | ✅ | ⬜ | ⬜ |
| | Stream Has Data Flag | ✅ | ✅ | ⬜ | ⬜ |
| | Schedule (Min / Cron) | ✅ | ✅ | ⬜ | ⬜ |
| | Predecessor Task Logic | ✅ | ✅ | ⬜ | ⬜ |

---

The Coalesce Stream and Task Node Types Package includes:

* [Work with Task](#work-with-task)
* [Dimension with Task](#dimension-with-task)
* [Fact with Task](#fact-with-task)
* [Task DAG Create Root](#task-dag-create-root)
* [Task DAG Resume Root](#task-dag-resume-root)
* [Stream](#stream)
* [Stream and Insert or Merge](#stream-and-insert-or-merge)
* [Stream for Directory Table](#stream-for-directory-table)
* [Delta Stream Merge](#delta-stream-merge)
* [Insert Or Merge with Task](#Insert-Or-Merge-with-Task)
* [Code](#code)
---

## Work with Task

The Coalesce Work with Task UDN is a work node that wraps the standard Coalesce Work node with a Task.

Tasks can be combined with Coalesce Stream node (table streams) for continuous ELT workflows to process recently changed table rows. Streams ensure exactly once semantics for new or changed data in a table.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or performing other periodic work.

More information about Tasks can be found in [Snowflake Introduction to tasks](https://docs.snowflake.com/en/user-guide/tasks-intro).

### Work with Task Node Configuration

The Work with Task node has two or three configuration groups depending on config options selected:

* [Node Properties](#work-with-task-properties)
* [Options](#work-with-task-options)
* [Scheduling Options](#work-with-task-scheduling-options) - Visible when Development Mode is false

#### Work with Task Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Stream will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Work with Task Options

![Worktask-opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/2582e574-e368-4c00-8172-b259a86a4548)

| **Option** | **Description** |
|------------|----------------|
| **Development Mode** | True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action.<br/>**True** - A table will be created and SQL will execute as a Run action<br/>**False** - After testing the SQL as a Run action, setting to false will wrap SQL in a task with specified Scheduling Options. When Run is executed, a message appears prompting the user to wait or suggesting a manual run. |
| **Multi Source** | True / False toggle that is Coalesce implementation of SQL UNIONs<br/>**True** - Multiple sources can be combined using:<br/>- UNION - Combines with duplicate elimination<br/>- UNION ALL - Combines without duplicate elimination<br/>**False** - Single source node or multiple sources combined using a join |
| **Cluster key** | True/False toggle that determines if clustering is enabled<br/>**True** - Specify clustering column and optionally allow expressions<br/>**False** - No clustering |
| **Truncate Before** | True / False toggle that determines if table should be truncated before insert<br/>**True** - Uses INSERT OVERWRITE<br/>**False** - Uses INSERT to append data |

#### Work with Task General Options

![Work with Task-GO](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/cb8cb62c-c4f1-4c22-87c2-3781a5328dfd)

| **Option** | **Description** |
|------------|----------------|
| **Distinct** | True/False toggle that determines whether to add DISTINCT to SQL Query<br/>**True** - Group by All is invisible. DISTINCT data is chosen<br/>**False** - Group by All is visible |
| **Group by All** | True/False toggle that determines whether to add GROUP BY ALL to SQL Query<br/>**True** - DISTINCT is invisible. Data grouped by all columns<br/>**False** - DISTINCT is visible |
| **Order By** | True/False toggle that determines whether to add ORDER BY to SQL Query<br/>**True** - Sort column and order options visible<br/>**False** - Sort options invisible |

#### Work with Task Scheduling Options

<img width="766" height="730" alt="image" src="https://github.com/user-attachments/assets/a1943e18-14c9-46ca-9b06-9a618570d1f3" />

| **Option** | **Description** |
|------------|----------------|
| **Scheduling Mode** | Choose compute type:<br/>- **Warehouse Task**: User managed warehouse executes tasks<br/>- **Serverless Task**: Uses serverless compute |
| **When Source Stream has Data Flag** | True/False toggle to check for stream data<br/>**True** - Only run task if source stream has capture change data<br/>**False** -  Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false. |
| **Multiple Stream has Data Logic** | AND/OR logic when multiple streams (visible if Stream has Data Flag is true)<br/>**AND** - If there are multiple streams task will run if all streams have data<br/>**OR** -  If there are multiple streams task will run if one or more streams has data |
| **Select Warehouse** | Visible if Scheduling Mode is set to Warehouse Task. Enter the name of the warehouse you want the task to run on without quotes.|
| **Select initial serverless size** | Visible when Scheduling Mode is set to Serverless Task.<br/> Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times. |
| **Enable Size Bounds** | Toggle to set explicit limits on serverless scaling. (Visible if **Serverless Task** is selected).<br/>**Validation Rules:**<br/>- Min size must be ≤ Initial size<br/>- Max size must be ≥ Initial size<br/>- Min size must be ≤ Max size |
| **Minimum Warehouse Size** | The smallest compute size allowed for the task (e.g., 1. XSMALL). |
| **Maximum Warehouse Size** | The largest compute size allowed for the task (e.g., 6. XXLARGE). |
| **Task Schedule** | Choose schedule type:<br/>- **Minutes** - Specify interval in minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.<br/>- **Cron** - Uses [Cron expressions](https://docs.coalesce.io/docs/reference/cron-reference/). Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.<br/>- **Predecessor** - Specify dependent tasks |
| **Execution Time** | The specific duration for the task run limit. Supported ranges:<br/>- **SECONDS**: 10 - 691200<br/>- **MINUTES**: 1 - 11520<br/>- **HOURS**: 1 - 192 |
| **Enter predecessor tasks separated by a comma**| Visible when Task Schedule is set to Predecessor. <br/>One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|
| **Root task name** | Visible when Task Schedule is set to Predecessor.<br/> Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|

#### Work with Task Advanced Scheduling Options

<img width="776" height="744" alt="image" src="https://github.com/user-attachments/assets/ab28860c-0115-4ca5-9b57-8e1f719ad37a" />

| **Option** | **Description** |
|------------|----------------|
| **Execute As Specific User** | Toggle to run on behalf of another user. Requires `GRANT IMPERSONATE` privileges. |
| **User Name** | The specific user account name used when **Execute As Specific User** is enabled. |
| **Allow Overlapping Execution** | Allows a new instance of the task to start if the previous one is still running. |
| **Enable Task Graph Config** | Enables a text box to provide **Configuration JSON** for the task graph. |
| **Auto-Suspend After Failures** | Automatically suspends the task after a set number of consecutive failures. |
| **Number of Consecutive Failures** | Set the threshold (0 - No Limit) before the task is automatically suspended. <br/>- When toggle is OFF: Parameter is not included (uses Snowflake default of 10).<br/>- When toggle is ON with value 0: **Disables** auto-suspension.<br/>- When toggle is ON with value > 0: **Suspends** after that many consecutive failures. |
| **Enable Auto-Retry** | Toggle to automatically retry the task if it fails. |
| **Retry Attempts** | Specify the number of retry attempts allowed (Range: 0 - 30). |

#### Work with Task Notification Options

<img width="785" height="371" alt="image" src="https://github.com/user-attachments/assets/d0aae222-cfcb-4497-a572-3fa7079287c8" />

| **Option** | **Description** |
|------------|----------------|
| **Enable Error Notifications** | Toggle to send alerts on failure. Requires an **Error Integration Name**. |
| **Enable Success Notifications** | Toggle to send alerts on success. Requires a **Success Integration Name**. |

> **Note:** Options under **Advanced Scheduling Options** and **Notification Options** (Execution Time, Overlapping Execution, Auto-Suspend, Auto-Retry, etc.) are only applicable to **Root** and **Independent** tasks. The only exception is **Execute As Specific User**, which can be configured for any task in the graph.

#### Example of Serverless Task with Multiple Predecessors and Root Task

![Example_of_Serverless_Task](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/0af0c04a-5e88-42d1-85de-fd71a792436d)

#### Example of Warehouse Task With 60 Minute Task Schedule

![Example_of_Warehouse_Task](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/04afc6b2-aebc-4346-b1fe-33340e7df0bf)

#### Example of Warehouse Task With Cron Schedule Not Using a Stream

![Example_of_Warehouse_Task_with_Cron](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/89c85551-59af-44e6-b383-a9bdaacf7cce)

### Work With Task Deployment

#### Work With Task Deployment Parameters

The Work with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is `targetTaskWarehouse` and the default value is `DEV ENVIRONMENT`.

When set to `DEV ENVIRONMENT` the value entered in the Scheduling Options config Select Warehouse on which to run the task will be used when creating the task.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than `DEV ENVIRONMENT` the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named `compute_wh`.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

#### Work With Task Initial Deployment

When deployed for the first time into an environment the Work with Task node will execute the following stages:

For tasks without predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Target Table** | Creates table that will be loaded by the task |
| **Create Task** | Creates task that will load the target table on schedule |
| **Resume Task** | Resumes the task so it runs on schedule |

For tasks with predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Target Table** | Creates table that will be loaded by the task |
| **Suspend Root Task** | Suspends root task for DAG modification |
| **Create Task** | Creates task that will load the target table on schedule |

If a task is part of a DAG of tasks, the DAG needs to include a node type called "Task DAG Resume Root." This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task-enabled nodes are CREATE OR REPLACE only, though this is subject to change.

#### Work With Task Redeployment

After initial deployment, changes in task schedule, warehouse, or scheduling options will result in a CREATE or RESUME

For tasks without predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Task** | Recreates task with new schedule |
| **Resume Task** | Resumes task with new schedule |

For tasks with predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Suspend Root Task** | Suspends root task for DAG modification |
| **Create Task** | Recreates task with new schedule |

#### Work With Task Altering Tables

Subsequent deployments with changes in table like add or drop column and change in data type will result in an ALTER table statement followed by CREATE TASK AND RESUME TASK statements being issued.

| **Stage** | **Description** |
|-----------|----------------|
| **Change Column Attributes/Delete Column/Add Column/Change table description** | Alter table statement is executed to perform the alter operation. |
| **Create Task** | Recreates task with new schedule |
| **Resume Task** | Resumes task with new schedule |

### Redeployment with only metadata changes

Sometimes, changes to config can result in metadata changes from node edits, DML changes, or storage updates when the **Development Mode** is **ON**. A few cases are listed below:

1. Changes in business keys
2. Changes to change tracking keys
3. Changes in join clauses
4. Transformations made at column level
5. Changing DML options like DISTINCT, ORDER BY, GROUP BY ALL

And many more. Most of the time, specific ‘is’ and ‘was’ values will be displayed to specifically show what changed.

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Metadata Update \| Business Keys \| Change Tracking \| Distinct \| Transformation \| Join** | A dummy statement would execute with specific changes listed in comments|

#### Node Type Switching

Node Type switching is supported starting from Coalesce version **7.28+**.

From this version onward, a node’s materialization type can be switched from one supported type to another, subject to certain limitations.

For more info click here - [Node Type Switching Logic and Limitations](#node-type-switching-logic)

### Work With Task Undeployment

If a Work with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then all objects created by the node in the target environment will be dropped.

For tasks without predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table** |  Drop the table originally created to be loaded by the task. |
| **Drop Current Task** | Drop the task |

For tasks with predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table** | Drop the table |
| **Suspend Root Task** | Drop a task from a DAG of task the root task needs to be put into a suspended state. |
| **Drop Task** | Drops the task |

If a task is part of a DAG of tasks the DAG needs to include a node type called **Task Dag Resume Root**. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

---

## Dimension With Task

The Coalesce Dimension with Task UDN is a node that wraps the standard Coalesce Dimension node with a Task.

Tasks can be combined with Coalesce Stream node (table streams) for continuous ELT workflows to process recently changed table rows. Streams ensure exactly once semantics for new or changed data in a table.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or performing other periodic work.

More information about Tasks can be found in [Snowflake Introduction to tasks](https://docs.snowflake.com/en/user-guide/tasks-intro).

### Dimension With Task Node Configuration

The Dimension with Task node has two or three configuration groups depending on config options selected:

* [Node Properties](#dimension-with-task-node-properties)
* [Options](#dimension-with-task-options)
* [General Options](#dimension-with-task-general-options)
* [Scheduling Options](#dimension-with-task-scheduling-options) - Visible when Development Mode is false

#### Dimension With Task Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Stream will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Dimension With Task Options

![Dimension_task_opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/3dfa88ac-b46b-4e92-9639-d45e5a6c242e)

| **Option** | **Description** |
|------------|----------------|
| **Development Mode** | True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action<br/>**True** - A table will be created and SQL will execute as a Run action<br/>**False** - After testing the SQL as a Run action, setting to false will wrap SQL in a task with specified Scheduling Options. When Run is executed, a message appears prompting the user to wait or suggesting a manual run. |
| **Multi Source** | True / False toggle that is Coalesce implementation of SQL UNIONs<br/>**True** - Multiple sources can be combined using:<br/>- UNION - Combines with duplicate elimination<br/>- UNION ALL - Combines without duplicate elimination<br/>**False** - Single source node or multiple sources combined using a join |
| **Business key** | Required column for both Type 1 and Type 2 Dimensions |
| **Change tracking** | Required column for Type 2 Dimension |
| **Cluster key** | True/False toggle that determines if clustering is enabled<br/>**True** - Specify clustering column and optionally allow expressions<br/>**False** - No clustering |

#### Dimension With Task General Options

![Work with Task-GO](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/cb8cb62c-c4f1-4c22-87c2-3781a5328dfd)

| **Option** | **Description** |
|------------|----------------|
| **Distinct** | True/False toggle that determines whether to add DISTINCT to SQL Query<br/>**True** - Group by All is invisible. DISTINCT data is chosen<br/>**False** - Group by All is visible |
| **Group by All** | True/False toggle that determines whether to add GROUP BY ALL to SQL Query<br/>**True** - DISTINCT is invisible. Data grouped by all columns<br/>**False** - DISTINCT is visible |
| **Order By** | True/False toggle that determines whether to add ORDER BY to SQL Query<br/>**True** - Sort column and order options visible<br/>**False** - Sort options invisible |

#### Dimension With Task Scheduling Options

If Development Mode is set to false, use Scheduling Options to configure how and when the task will run.

![Task Scheduling Options](https://github.com/user-attachments/assets/2f8bc6c3-117c-4130-9619-45ee50feebef)

| **Option** | **Description** |
|------------|----------------|
| **Scheduling Mode** | Choose compute type:<br/>- **Warehouse Task**: User managed warehouse executes tasks<br/>- **Serverless Task**: Uses serverless compute |
| **When Source Stream has Data Flag** | True/False toggle to check for stream data<br/>**True** - Only run task if source stream has capture change data<br/>**False** -  Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false. |
| **Multiple Stream has Data Logic** | AND/OR logic when multiple streams (visible if Stream has Data Flag is true)<br/>**AND** - If there are multiple streams task will run if all streams have data<br/>**OR** -  If there are multiple streams task will run if one or more streams has data |
| **Select Warehouse** | Visible if Scheduling Mode is set to Warehouse Task. Enter the name of the warehouse you want the task to run on without quotes.|
| **Select initial serverless size** | Visible when Scheduling Mode is set to Serverless Task.<br/> Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times. |
| **Enable Size Bounds** | Toggle to set explicit limits on serverless scaling. (Visible if **Serverless Task** is selected).<br/>**Validation Rules:**<br/>- Min size must be ≤ Initial size<br/>- Max size must be ≥ Initial size<br/>- Min size must be ≤ Max size |
| **Minimum Warehouse Size** | The smallest compute size allowed for the task (e.g., 1. XSMALL). |
| **Maximum Warehouse Size** | The largest compute size allowed for the task (e.g., 6. XXLARGE). |
| **Task Schedule** | Choose schedule type:<br/>- **Minutes** - Specify interval in minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.<br/>- **Cron** - Uses [Cron expressions](https://docs.coalesce.io/docs/reference/cron-reference/). Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.<br/>- **Predecessor** - Specify dependent tasks |
| **Execution Time** | The specific duration for the task run limit. Supported ranges:<br/>- **SECONDS**: 10 - 691200<br/>- **MINUTES**: 1 - 11520<br/>- **HOURS**: 1 - 192 |
| **Enter predecessor tasks separated by a comma**| Visible when Task Schedule is set to Predecessor. <br/>One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|
| **Root task name** | Visible when Task Schedule is set to Predecessor.<br/> Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|

#### Dimension with Task Advanced Scheduling Options

<img width="776" height="744" alt="image" src="https://github.com/user-attachments/assets/ab28860c-0115-4ca5-9b57-8e1f719ad37a" />

| **Option** | **Description** |
|------------|----------------|
| **Execute As Specific User** | Toggle to run on behalf of another user. Requires `GRANT IMPERSONATE` privileges. |
| **User Name** | The specific user account name used when **Execute As Specific User** is enabled. |
| **Allow Overlapping Execution** | Allows a new instance of the task to start if the previous one is still running. |
| **Enable Task Graph Config** | Enables a text box to provide **Configuration JSON** for the task graph. |
| **Auto-Suspend After Failures** | Automatically suspends the task after a set number of consecutive failures. |
| **Number of Consecutive Failures** | Set the threshold (0 - No Limit) before the task is automatically suspended. <br/>- When toggle is OFF: Parameter is not included (uses Snowflake default of 10).<br/>- When toggle is ON with value 0: **Disables** auto-suspension.<br/>- When toggle is ON with value > 0: **Suspends** after that many consecutive failures. |
| **Enable Auto-Retry** | Toggle to automatically retry the task if it fails. |
| **Retry Attempts** | Specify the number of retry attempts allowed (Range: 0 - 30). |

#### Dimension with Task Notification Options

<img width="785" height="371" alt="image" src="https://github.com/user-attachments/assets/d0aae222-cfcb-4497-a572-3fa7079287c8" />

| **Option** | **Description** |
|------------|----------------|
| **Enable Error Notifications** | Toggle to send alerts on failure. Requires an **Error Integration Name**. |
| **Enable Success Notifications** | Toggle to send alerts on success. Requires a **Success Integration Name**. |

> **Note:** Options under **Advanced Scheduling Options** and **Notification Options** (Execution Time, Overlapping Execution, Auto-Suspend, Auto-Retry, etc.) are only applicable to **Root** and **Independent** tasks. The only exception is **Execute As Specific User**, which can be configured for any task in the graph.

### Dimension With Task Deployment

#### Dimenson With Task Deployment Parameters

The Dimenson with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is `targetTaskWarehouse` and the default value is `DEV ENVIRONMENT`.

When set to `DEV ENVIRONMENT` the value entered in the Scheduling Options config Select Warehouse on which to run the task will be used when creating the task.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than `DEV ENVIRONMENT` the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named `compute_wh`.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

#### Dimension With Task Initial Deployment

When deployed for the first time into an environment the Dimension with Task node will execute three stages dependent on whether or not the task schedule relies on a predecessor task.

For tasks without predecessor:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Target Table** | Creates table that will be loaded by the task |
| **Create Task** | Creates task that will load the target table on schedule |
| **Resume Task** | Resumes the task so it runs on schedule |

For tasks with predecessor:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Target Table** | Creates table that will be loaded by the task |
| **Suspend Root Task** | Suspends root task for DAG modification |
| **Create Task** | Creates task that will load the target table on schedule |

If a task is part of a DAG of tasks, the DAG needs to include a node type called `Task DAG Resume Root`. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task-enabled nodes are CREATE OR REPLACE only, though this is subject to change.

### Dimension With Task Redeployment

After initial deployment, changes in task schedule, warehouse, or scheduling options will result in a CREATE or RESUME TASK.

For tasks without predecessor:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Task** | Recreates task with a new schedule |
| **Resume Task** | Resumes task with a new schedule |

For tasks with predecessor:

| **Stage** | **Description** |
|-----------|----------------|
| **Suspend Root Task** | Suspends root task for DAG modification |
| **Create Task** | Recreates task with new schedule |

#### Dimension With Task Altering Tables

Subsequent deployments with changes in table such as add or drop column and change in data type will result in an ALTER table statement followed by CREATE TASK AND RESUME TASK statements being issued.

| **Stage** | **Description** |
|-----------|----------------|
| **Change Column Attributes/Delete Column/Add Column/Change table description** | Alter table statement is executed to perform the alter operation. |
| **Create Task** | Recreates task with new schedule |
| **Resume Task** | Resumes task with new schedule |

### Redeployment with only metadata changes

Sometimes, changes to config can result in metadata changes from node edits, DML changes, or storage updates when the **Development Mode** is **ON**. A few cases are listed below:

1. Changes in business keys
2. Changes to change tracking keys
3. Changes in join clauses
4. Transformations made at column level
5. Changing DML options like DISTINCT, ORDER BY, GROUP BY ALL

And many more. Most of the time, specific ‘is’ and ‘was’ values will be displayed to specifically show what changed.

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Metadata Update \| Business Keys \| Change Tracking \| Distinct \| Transformation \| Join** | A dummy statement would execute with specific changes listed in comments|

#### Node Type Switching

Node Type switching is supported starting from Coalesce version **7.28+**.

From this version onward, a node’s materialization type can be switched from one supported type to another, subject to certain limitations.

For more info click here - [Node Type Switching Logic and Limitations](#node-type-switching-logic)

### Dimension With Task Undeployment

If a Work with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then all objects created by the node in the target environment will be dropped.

For tasks without predecessor:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table** | Drop the table originally created to be loaded by the task. |
| **Drop Current Task** | Drops the task |

For tasks with predecessor:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table** | Drop the table originally created to be loaded by the task. |
| **Suspend Root Task** | Drop a task from a DAG of task the root task needs to be put into a suspended state. |
| **Drop Task** | Drop the task |

If a task is part of a DAG of tasks the DAG needs to include a node type called **Task Dag Resume Root**. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

---

## Fact With Task

The Coalesce Fact with Task UDN is a node that wraps the standard Coalesce Fact node with a Task.

Tasks can be combined with Coalesce Stream node (table streams) for continuous ELT workflows to process recently changed table rows. Streams ensure exactly once semantics for new or changed data in a table.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or performing other periodic work.

More information about Tasks can be found in [Snowflake Introduction to tasks](https://docs.snowflake.com/en/user-guide/tasks-intro).

### Fact With Task Node Configuration

The Fact with Task node has two or three configuration groups depending on config options selected:

* [Node Properties](#fact-with-task-node-properties)
* [Options](#fact-with-task-options)
* [Scheduling Options](#fact-with-task-scheduling-options) - Visible when Development Mode is false

#### Fact With Task Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Stream will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Fact With Task Options

![Dimension_task_opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/3dfa88ac-b46b-4e92-9639-d45e5a6c242e)

| **Option** | **Description** |
|------------|----------------|
| **Development Mode** | True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action<br/>**True** - A table will be created and SQL will execute as a Run action<br/>**False** - After testing the SQL as a Run action, setting to false will wrap SQL in a task with specified Scheduling Options. When Run is executed, a message appears prompting the user to wait or suggesting a manual run. |
| **Multi Source** | True / False toggle that is Coalesce implementation of SQL UNIONs<br/>**True** - Multiple sources can be combined using:<br/>- UNION - Combines with duplicate elimination<br/>- UNION ALL - Combines without duplicate elimination<br/>**False** - Single source node or multiple sources combined using a join |
| **Cluster key** | True/False toggle that determines if clustering is enabled<br/>**True** - Specify clustering column and optionally allow expressions<br/>**False** - No clustering |
| **Truncate Before** | Specifies that the target table should be truncated before inserting the values into the table. |

#### Fact With Task General Options

![Work with Task-GO](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/cb8cb62c-c4f1-4c22-87c2-3781a5328dfd)

| **Option** | **Description** |
|------------|----------------|
| **Distinct** | True/False toggle that determines whether to add DISTINCT to SQL Query<br/>**True** - Group by All is invisible. DISTINCT data is chosen<br/>**False** - Group by All is visible |
| **Group by All** | True/False toggle that determines whether to add GROUP BY ALL to SQL Query<br/>**True** - DISTINCT is invisible. Data grouped by all columns<br/>**False** - DISTINCT is visible |
| **Order By** | True/False toggle that determines whether to add ORDER BY to SQL Query<br/>**True** - Sort column and order options visible<br/>**False** - Sort options invisible |

#### Fact With Task Scheduling Options

If Development Mode is set to false, use Scheduling Options to configure how and when the task will run.

![Task Scheduling Options](https://github.com/user-attachments/assets/2f8bc6c3-117c-4130-9619-45ee50feebef)

| **Option** | **Description** |
|------------|----------------|
| **Scheduling Mode** | Choose compute type:<br/>- **Warehouse Task**: User managed warehouse executes tasks<br/>- **Serverless Task**: Uses serverless compute |
| **Multiple Stream has Data Flag** | True / False toggle that checks whether source streams have data before executing a task.<br/> **True**: Only run task if source stream has capture change data <br/> **False**:  Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false.|
| **Multiple Stream has Data Logic** | AND/OR logic when multiple streams (visible if Stream has Data Flag is true)<br/>**AND** - If there are multiple streams task will run if all streams have data<br/>**OR** -  If there are multiple streams task will run if one or more streams has data |
| **Select Warehouse** | Visible if Scheduling Mode is set to Warehouse Task. Enter the name of the warehouse you want the task to run on without quotes.|
| **Select initial serverless size** | Visible when Scheduling Mode is set to Serverless Task.<br/> Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times. |
| **Enable Size Bounds** | Toggle to set explicit limits on serverless scaling. (Visible if **Serverless Task** is selected).<br/>**Validation Rules:**<br/>- Min size must be ≤ Initial size<br/>- Max size must be ≥ Initial size<br/>- Min size must be ≤ Max size |
| **Minimum Warehouse Size** | The smallest compute size allowed for the task (e.g., 1. XSMALL). |
| **Maximum Warehouse Size** | The largest compute size allowed for the task (e.g., 6. XXLARGE). |
| **Task Schedule** | Choose schedule type:<br/>- **Minutes** - Specify interval in minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.<br/>- **Cron** - Uses [Cron expressions](https://docs.coalesce.io/docs/reference/cron-reference/). Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.<br/>- **Predecessor** - Specify dependent tasks |
| **Execution Time** | The specific duration for the task run limit. Supported ranges:<br/>- **SECONDS**: 10 - 691200<br/>- **MINUTES**: 1 - 11520<br/>- **HOURS**: 1 - 192 |
| **Enter predecessor tasks separated by a comma**| Visible when Task Schedule is set to Predecessor. <br/>One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|
| **Root task name** | Visible when Task Schedule is set to Predecessor.<br/> Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|

#### Fact with Task Advanced Scheduling Options

<img width="776" height="744" alt="image" src="https://github.com/user-attachments/assets/ab28860c-0115-4ca5-9b57-8e1f719ad37a" />

| **Option** | **Description** |
|------------|----------------|
| **Execute As Specific User** | Toggle to run on behalf of another user. Requires `GRANT IMPERSONATE` privileges. |
| **User Name** | The specific user account name used when **Execute As Specific User** is enabled. |
| **Allow Overlapping Execution** | Allows a new instance of the task to start if the previous one is still running. |
| **Enable Task Graph Config** | Enables a text box to provide **Configuration JSON** for the task graph. |
| **Auto-Suspend After Failures** | Automatically suspends the task after a set number of consecutive failures. |
| **Number of Consecutive Failures** | Set the threshold (0 - No Limit) before the task is automatically suspended. <br/>- When toggle is OFF: Parameter is not included (uses Snowflake default of 10).<br/>- When toggle is ON with value 0: **Disables** auto-suspension.<br/>- When toggle is ON with value > 0: **Suspends** after that many consecutive failures. |
| **Enable Auto-Retry** | Toggle to automatically retry the task if it fails. |
| **Retry Attempts** | Specify the number of retry attempts allowed (Range: 0 - 30). |

#### Fact with Task Notification Options

<img width="785" height="371" alt="image" src="https://github.com/user-attachments/assets/d0aae222-cfcb-4497-a572-3fa7079287c8" />

| **Option** | **Description** |
|------------|----------------|
| **Enable Error Notifications** | Toggle to send alerts on failure. Requires an **Error Integration Name**. |
| **Enable Success Notifications** | Toggle to send alerts on success. Requires a **Success Integration Name**. |

> **Note:** Options under **Advanced Scheduling Options** and **Notification Options** (Execution Time, Overlapping Execution, Auto-Suspend, Auto-Retry, etc.) are only applicable to **Root** and **Independent** tasks. The only exception is **Execute As Specific User**, which can be configured for any task in the graph.

### Fact With Task Deployment

#### Fact With Task Deployment Parameters

The Fact with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is `targetTaskWarehouse` and the default value is `DEV ENVIRONMENT`.

When set to `DEV ENVIRONMENT` the value entered in the Scheduling Options config Select Warehouse on which to run task will be used when creating the task.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than `DEV ENVIRONMENT` the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named `compute_wh`.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

#### Fact With Task Initial Deployment

When deployed for the first time into an environment the Fact with Task node will execute three stages dependent on whether or not the task schedule relies on a predecessor task.

For tasks without predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Target Table** | Creates table that will be loaded by the task |
| **Create Task** | Creates task that will load the target table on schedule |
| **Resume Task** | Resumes the task so it runs on schedule |

For tasks with predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Target Table** | Creates table that will be loaded by the task |
| **Suspend Root Task** | Suspends root task for DAG modification |
| **Create Task** | Creates task that will load the target table on schedule |

If a task is part of a DAG of tasks, the DAG needs to include a node type called `Task DAG Resume Root`. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task-enabled nodes are CREATE OR REPLACE only, though this is subject to change.

#### Fact With Task Redeployment

After initial deployment, changes to task schedule, warehouse, or scheduling options will result in a CREATE and RESUME TASK.

For tasks without predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Task** | Recreates task with new schedule |
| **Resume Task** | Resumes task with new schedule |

For tasks with predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Suspend Root Task** | Suspends root task for DAG modification |
| **Create Task** | Creates task that will load the target table on schedule |

#### Fact With Task Altering the Tables

Changes to add or drop column, or change in data type will result in a ALTER, CREATE, AND RESUME TASK.

| **Stage** | **Description** |
|-----------|----------------|
| **Alter Table** | Modifies table structure |
| **Create Task** | Recreates task |
| **Resume Task** | Resumes updated task |

### Redeployment with only metadata changes

Sometimes, changes to config can result in metadata changes from node edits, DML changes, or storage updates when the **Development Mode** is **ON**. A few cases are listed below:

1. Changes in business keys
2. Changes to change tracking keys
3. Changes in join clauses
4. Transformations made at column level
5. Changing DML options like DISTINCT, ORDER BY, GROUP BY ALL

And many more. Most of the time, specific ‘is’ and ‘was’ values will be displayed to specifically show what changed.

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Metadata Update \| Business Keys \| Change Tracking \| Distinct \| Transformation \| Join** | A dummy statement would execute with specific changes listed in comments|

#### Node Type Switching

Node Type switching is supported starting from Coalesce version **7.28+**.

From this version onward, a node’s materialization type can be switched from one supported type to another, subject to certain limitations.

For more info click here - [Node Type Switching Logic and Limitations](#node-type-switching-logic)

### Fact With Task Undeployment

If a Fact with Task node is deleted from a Workspace, and that Workspace is committed to Git and that commit is deployed to a higher-level environment, then all objects created by the node in the target environment will be dropped.

For tasks without predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table** | Drop the table created to be loaded by the task |
| **Drop Current Task** | Removes the task |

For tasks with predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table** | Drop the table created to be loaded by the task |
| **Suspend Root Task** | Suspends root task |
| **Drop Task** | Removes the task |

If a task is part of a DAG of tasks, the DAG needs to include a node type called `Task Dag Resume Root`. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

---

## Task DAG Create Root

The Coalesce Task DAG Create Root UDN is a node that helps to create a standalone root task.

The root task should have a defined schedule that initiates a run of the DAG. Each of the other tasks has at least one defined predecessor to link the tasks in the DAG. A child task runs only after all of its predecessor tasks run successfully to completion.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or perform other periodic work.

More information about Tasks can be found in [Snowflake's Introduction to tasks](https://docs.snowflake.com/en/user-guide/tasks-intro).

### Task DAG Create Root Node Configuration

The Task DAG Create Root node has two configuration groups:

* [Node Properties](#task-dag-create-root-node-properties)
* [Scheduling Options](#task-dag-create-root-scheduling-options)

#### Task DAG Create Root Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Stream will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Task DAG Create Root Scheduling Options

![Task_dag_create_root_scheduling_options](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/79323e3a-4674-4676-9441-4497e1788680)

| **Option** | **Description** |
|------------|----------------|
| **Scheduling Mode** | Choose compute type:<br/>- **Warehouse Task** - User managed warehouse executes tasks<br/>- **Serverless Task** - Uses serverless compute |
| **Select Warehouse** | Visible if Scheduling Mode is set to Warehouse Task. <br/> Name of warehouse to run task on without quotes |
| **Select initial serverless size** | Visible when Scheduling Mode is set to Serverless Task <br/> Initial compute size for serverless tasks. Snowflake will adjust size from there based on target schedule and task run times. |
| **Enable Size Bounds** | Toggle to set explicit limits on serverless scaling. (Visible if **Serverless Task** is selected).<br/>**Validation Rules:**<br/>- Min size must be ≤ Initial size<br/>- Max size must be ≥ Initial size<br/>- Min size must be ≤ Max size |
| **Minimum Warehouse Size** | The smallest compute size allowed for the task (e.g., 1. XSMALL). |
| **Maximum Warehouse Size** | The largest compute size allowed for the task (e.g., 6. XXLARGE). |
| **Task Schedule** | Choose schedule type:<br/>- Minutes - Specify interval in minutes<br/>- Cron - Use cron expression<br/>-Triggered task - To create task when the source streams have data |
| **Execution Time** | The specific duration for the task run limit. Supported ranges:<br/>- **SECONDS**: 10 - 691200<br/>- **MINUTES**: 1 - 11520<br/>- **HOURS**: 1 - 192 |
| **Multiple source streams(if disabled,considered as single source stream)** |(visible obly for Triggered Task)Toggle- Enabled denotes multiple streams are connected|
| **Multiple Stream has Data Logic**| AND/OR logic when multiple streams (visible obly for Triggered Task and multiple streams is enabled)<br/>**AND** - If there are multiple streams task will run if all streams have data<br/>**OR** -  If there are multiple streams task will run if one or more streams has data | 
| **Enter root task SQL** | The SQL statement to be run when a standalone root task executes |

#### Root with Task Advanced Scheduling Options

<img width="776" height="744" alt="image" src="https://github.com/user-attachments/assets/ab28860c-0115-4ca5-9b57-8e1f719ad37a" />

| **Option** | **Description** |
|------------|----------------|
| **Execute As Specific User** | Toggle to run on behalf of another user. Requires `GRANT IMPERSONATE` privileges. |
| **User Name** | The specific user account name used when **Execute As Specific User** is enabled. |
| **Allow Overlapping Execution** | Allows a new instance of the task to start if the previous one is still running. |
| **Enable Task Graph Config** | Enables a text box to provide **Configuration JSON** for the task graph. |
| **Auto-Suspend After Failures** | Automatically suspends the task after a set number of consecutive failures. |
| **Number of Consecutive Failures** | Set the threshold (0 - No Limit) before the task is automatically suspended. <br/>- When toggle is OFF: Parameter is not included (uses Snowflake default of 10).<br/>- When toggle is ON with value 0: **Disables** auto-suspension.<br/>- When toggle is ON with value > 0: **Suspends** after that many consecutive failures. |
| **Enable Auto-Retry** | Toggle to automatically retry the task if it fails. |
| **Retry Attempts** | Specify the number of retry attempts allowed (Range: 0 - 30). |

#### Root with Task Notification Options

<img width="785" height="371" alt="image" src="https://github.com/user-attachments/assets/d0aae222-cfcb-4497-a572-3fa7079287c8" />

| **Option** | **Description** |
|------------|----------------|
| **Enable Error Notifications** | Toggle to send alerts on failure. Requires an **Error Integration Name**. |
| **Enable Success Notifications** | Toggle to send alerts on success. Requires a **Success Integration Name**. |

> **Note:** Options under **Advanced Scheduling Options** and **Notification Options** (Execution Time, Overlapping Execution, Auto-Suspend, Auto-Retry, etc.) are only applicable to **Root** and **Independent** tasks. The only exception is **Execute As Specific User**, which can be configured for any task in the graph.

### Task DAG Create Root Deployment

#### Task DAG Create Root Deployment Parameters

The parameter name is `targetTaskWarehouse` and the default value is `DEV ENVIRONMENT`.

When set to `DEV ENVIRONMENT` the value entered in the Scheduling Options config Select Warehouse on which to run task will be used when creating the task.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than `DEV ENVIRONMENT` the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named `compute_wh`.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

#### Task DAG Create Root Initial Deployment

When deployed for the first time into an environment, the following stages execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Suspend Root Task** | Suspends root task for creation |
| **Create Root Task** | Creates task that will execute Root SQL on schedule |

If a task is part of a DAG of tasks, the DAG needs to include a node type called `Task Dag Resume Root`. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

#### Task DAG Create Root Redeployment

After the Task has deployed for the first time into a target environment, subsequent deployments will execute two stages:

| **Stage** | **Description** |
|-----------|----------------|
| **Suspend Root Task** | Suspends root task |
| **Create Root Task** | Recreates root task |

### Task DAG Create Root Undeployment

When a Task DAG Create Root node is deleted, two stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Suspend Root task** | Suspends root task |
| **Drop current task** | Removes the task |

---

## Task DAG Resume Root

The Coalesce Task DAG Resume Root UDN is a node type that helps to resume the root task and its dependents or child tasks. Recursively resumes all dependent tasks tied to a specified root task in a DAG using the root task name specified.

The root task should have a defined schedule that initiates a run of the DAG. Each of the other tasks has at least one defined predecessor to link the tasks in the DAG. A child task runs only after all of its predecessor tasks run successfully to completion.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or perform other periodic work.

More information about Tasks can be found in [Snowflake's Introduction to tasks](https://docs.snowflake.com/en/user-guide/tasks-intro).

### Task DAG Resume Root Node Configuration

The Task DAG Resume Root node has two configuration groups:

* [Node Properties](#task-dag-resume-root-node-properties)
* [Scheduling Options](#task-dag-resume-root-scheduling-options)

#### Task DAG Resume Root Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Stream will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Task DAG Resume Root Scheduling Options

![Task_dag_resume_root_scheduling_options](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/1dadf5e1-b846-4f2f-b09a-1a3722e60cdf)

| **Option** | **Description** |
|------------|----------------|
| **Enter root task name** | Name of the root task to be resumed - recursively resumes all dependent tasks tied to this specified root task |

### Task DAG Resume Root Deployment

#### Task DAG Resume Root Initial Deployment

When deployed for the first time into an environment the following stage executes:

| **Stage** | **Description** |
|-----------|----------------|
| **Try Enable Root Task** | Resumes root task and all its dependents |

If a task is part of a DAG of tasks, the DAG needs to include a node type called `Task Dag Resume Root`. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

#### Task DAG Resume Root Undeployment

If a Dimension with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher-level environment then two stages are executed.

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table** | Removes the table |
| **Drop Current Task** | Removes the task |

---

## Stream

The Coalesce Stream UDN is a node that allows you to develop and deploy a stream on top of a table, view or external table.

A stream logically takes an initial snapshot of every row in the source object (e.g. table, external table, or the underlying tables for a view) by initializing a point in time (called an offset) as the current transactional version of the object. The change tracking system utilized by the stream then records information about the DML changes after this snapshot was taken. Change records provide the state of a row before and after the change. Change information mirrors the column structure of the tracked source object and includes additional metadata columns that describe each change event.

More information about Streams can be found in the official [Snowflake Introduction to Streams](https://docs.snowflake.com/en/user-guide/streams-intro).

### Stream Node Configuration

The Stream has two configuration groups:

* [Node Properties](#stream-node-properties)
* [Stream Options](#stream-options)

#### Stream Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Stream will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Stream Options

| **Option** | **Description** |
|------------|----------------|
| **Source Object** | Type of object for stream creation:<br/>**Table**:<br/>**Append Only Stream**: <br/>True: Append-only stream <br/>False: Standard stream <br/><br/>**Show Initial Rows**: Specify the records to return the first time the stream is consumed.<br/> **True**: The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset - the normal stream behavior.<br/> **False**: The stream returns any DML changes to the source object since the most recent offset.<br/><br/>**Redeployment Behavior**: Options for redeployment<br/><hr/>**Dynamic Table**:<br/>**Show Initial Rows**: Specify the records to return the first time the stream is consumed.<br/> **True**: The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset - the normal stream behavior.<br/> **False**: The stream returns any DML changes to the source object since the most recent offset.<br/><br/>**Redeployment Behavior**: Options for redeployment<br/><hr/>**View**:<br/>**Append Only Stream**: <br/>True: Append-only stream <br/>False: Standard stream <br/><br/>**Show Initial Rows**: Specify the records to return the first time the stream is consumed.<br/>**True**: The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset - the normal stream behavior.<br/> **False**: The stream returns any DML changes to the source object since the most recent offset.<br/><br/>**Redeployment Behavior**: Options for redeployment<br/><hr/>**External table**:<br/>Redeployment Behavior: Options for redeployment<br/><hr/>**External iceberg table**:<br/>Redeployment Behavior: Options for redeployment |

### Stream System Columns

A Stream UDN adds three system columns to the output of the node. These columns can be used together to track INSERT, UPDATE and DELETE operations against a source object.

| **Column** | **Description** |
|------------|----------------|
| **METADATA$ACTION** | Indicates the DML operation (INSERT, DELETE) recorded |
| **METADATA$ISUPDATE** | Indicates whether the operation was part of an UPDATE statement. Updates to rows in the source object are represented as a pair of DELETE and INSERT records in the stream with a metadata column METADATA$ISUPDATE values set to TRUE.|
| **METADATA$ROW_ID** | Specifies the unique and immutable ID for the row, used to track changes over time |

### Stream Deployment

#### Stream Deployment Parameters

No deployment parameters are required.

#### Stream Initial Deployment

When deployed for the first time into an environment the Stream node executes:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Stream** | Executes a CREATE OR REPLACE statement to create a Stream in the target environment |

#### Stream Redeployment

After initial deployment, subsequent deployments will create a new stream based on the selected redeployment behavior:

| **Redeployment Behavior** | **Stage Executed** |
|--------------------------|-------------------|
| Create Stream if not exists | Re-Create Stream at existing offset |
| Create or Replace | Create Stream |
| Create at existing stream | Re-Create Stream at existing offset |

#### Node Type Switching

Node Type switching is supported starting from Coalesce version **7.28+**.

From this version onward, a node’s materialization type can be switched from one supported type to another, subject to certain limitations.

For more info click here - [Node Type Switching Logic and Limitations](#node-type-switching-logic)

### Stream Undeployment

When a Stream Node is deleted from a Workspace and that commit is deployed, the following stage executes:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Stream** | Removes the stream from the target environment |

---

## Stream and Insert or Merge

The Coalesce Streams and Insert or Merge UDN is a node that allows you to develop and deploy a stream on top of a table, view or external table. Also, provides option to create a target table to insert or merge data from source with a task on top of it.

A stream logically takes an initial snapshot of every row in the source object (e.g. table, external table, or the underlying tables for a view) by initializing a point in time (called an offset) as the current transactional version of the object. The change tracking system utilized by the stream then records information about the DML changes after this snapshot was taken. Change records provide the state of a row before and after the change. Change information mirrors the column structure of the tracked source object and includes additional metadata columns that describe each change event.

More information about Streams can be found in the official [Snowflake's Introduction to Streams](https://docs.snowflake.com/en/user-guide/streams-intro).

### Stream and Insert or Merge Node Configuration

The Stream and Insert or Merge node has the following configuration groups:

* [Node Properties](#stream-and-insert-or-merge-node-properties)
* [General Options](#stream-and-insert-or-merge-general-options)
* [Stream Options](#stream-and-insert-or-merge-stream-options)
* [Target Loading Options](#stream-and-insert-or-merge-target-loading-options)
* [Scheduling Options](#stream-and-insert-or-merge-scheduling-options)

#### Stream and Insert or Merge Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Stream will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Stream and Insert or Merge General Options

| **Option** | **Description** |
|------------|----------------|
| **Development Mode** | True / False toggle that determines whether a task will be created or if SQL executes as DML<br/>**True** - Table created and SQL executes as Run action<br/>**False** - SQL wrapped in task with specified Scheduling Options. When Run is executed, a message appears prompting the user to wait or suggesting a manual run. |
| **CREATE AS** | Choose target object type:<br/>- Table - Permanent table with data retention and fail-safe<br/>- Transient Table - Temporary table without data retention |
| **DISTINCT** | True/False toggle for DISTINCT in SQL Query<br/>**True** - Group by All invisible, DISTINCT used<br/>**False** - Group by All visible |
| **GROUP BY ALL** | True/False toggle for GROUP BY ALL in SQL Query<br/>**True** - DISTINCT invisible, group by all columns<br/>**False** - DISTINCT visible |

#### Stream and Insert or Merge Stream Options

| **Option** | **Description** |
|------------|----------------|
| **Source Object** | Type of object for stream creation:<br/>**Table**:<br/>- Append Only Stream: True/False toggle for stream type<br/>- Show Initial Rows: True/False toggle for initial records<br/>- Propagate Deletes : True/False toggle for adding filter for condition METADATA$ACTION != 'DELETE' <br/>- Redeployment Behavior: Options for redeployment<br/>**Dynamic Table**:<br/>**Show Initial Rows**: Specify the records to return the first time the stream is consumed.<br/> **True**: The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset - the normal stream behavior.<br/> **False**: The stream returns any DML changes to the source object since the most recent offset.<br/><br/>**Redeployment Behavior**: Options for redeployment<br/>**View**:<br/>- Append Only Stream: True/False toggle for stream type<br/>- Show Initial Rows: True/False toggle for initial records<br/>- Redeployment Behavior: Options for redeployment |

#### Stream and Insert or Merge Target Loading Options

| **Option** | **Description** |
|------------|----------------|
| **Load Type** | Choose data loading method:<br/>**Insert** - Data inserted from source<br/>**Merge** - Latest record changes merged into target |
| **Table keys** | Business key columns for merging data (enabled for Merge load type) |
| **Record Date/Timestamp** | Date/Timestamp column for latest record merging (enabled for Merge load type) |
| **Cluster key** | True/False toggle for clustering<br/>**True** - Specify clustering column and expressions. - **Allow Expressions Cluster Key**: Add an expression to the specified cluster key.<br/>**False** - No clustering |

#### Stream and Insert or Merge Scheduling Options

| **Option** | **Description** |
|------------|----------------|
| **Scheduling Mode** | Choose compute type:<br/>- **Warehouse Task**: User managed warehouse executes tasks<br/>- **Serverless Task**: Uses serverless compute |
| **When Source Stream has Data Flag** | True/False toggle to check for stream data<br/>**True** - Only run task if source stream has capture change data<br/>**False** -  Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false. |
| **Select Warehouse** | Visible if Scheduling Mode is set to Warehouse Task. Enter the name of the warehouse you want the task to run on without quotes.|
| **Select initial serverless size** | Visible when Scheduling Mode is set to Serverless Task.<br/> Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times. |
| **Enable Size Bounds** | Toggle to set explicit limits on serverless scaling. (Visible if **Serverless Task** is selected).<br/>**Validation Rules:**<br/>- Min size must be ≤ Initial size<br/>- Max size must be ≥ Initial size<br/>- Min size must be ≤ Max size |
| **Minimum Warehouse Size** | The smallest compute size allowed for the task (e.g., 1. XSMALL). |
| **Maximum Warehouse Size** | The largest compute size allowed for the task (e.g., 6. XXLARGE). |
| **Task Schedule** | Choose schedule type:<br/>- **Minutes** - Specify interval in minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.<br/>- **Cron** - Uses [Cron expressions](https://docs.coalesce.io/docs/reference/cron-reference/). Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.<br/>- **Predecessor** - Specify dependent tasks |
| **Execution Time** | The specific duration for the task run limit. Supported ranges:<br/>- **SECONDS**: 10 - 691200<br/>- **MINUTES**: 1 - 11520<br/>- **HOURS**: 1 - 192 |
| **Enter predecessor tasks separated by a comma**| Visible when Task Schedule is set to Predecessor. <br/>One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|
| **Root task name** | Visible when Task Schedule is set to Predecessor.<br/> Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|

#### Stream and Insert or Merge Advanced Scheduling Options

<img width="776" height="744" alt="image" src="https://github.com/user-attachments/assets/ab28860c-0115-4ca5-9b57-8e1f719ad37a" />

| **Option** | **Description** |
|------------|----------------|
| **Execute As Specific User** | Toggle to run on behalf of another user. Requires `GRANT IMPERSONATE` privileges. |
| **User Name** | The specific user account name used when **Execute As Specific User** is enabled. |
| **Allow Overlapping Execution** | Allows a new instance of the task to start if the previous one is still running. |
| **Enable Task Graph Config** | Enables a text box to provide **Configuration JSON** for the task graph. |
| **Auto-Suspend After Failures** | Automatically suspends the task after a set number of consecutive failures. |
| **Number of Consecutive Failures** | Set the threshold (0 - No Limit) before the task is automatically suspended. <br/>- When toggle is OFF: Parameter is not included (uses Snowflake default of 10).<br/>- When toggle is ON with value 0: **Disables** auto-suspension.<br/>- When toggle is ON with value > 0: **Suspends** after that many consecutive failures. |
| **Enable Auto-Retry** | Toggle to automatically retry the task if it fails. |
| **Retry Attempts** | Specify the number of retry attempts allowed (Range: 0 - 30). |

#### Stream and Insert or Merge Notification Options

<img width="785" height="371" alt="image" src="https://github.com/user-attachments/assets/d0aae222-cfcb-4497-a572-3fa7079287c8" />

| **Option** | **Description** |
|------------|----------------|
| **Enable Error Notifications** | Toggle to send alerts on failure. Requires an **Error Integration Name**. |
| **Enable Success Notifications** | Toggle to send alerts on success. Requires a **Success Integration Name**. |

> **Note:** Options under **Advanced Scheduling Options** and **Notification Options** (Execution Time, Overlapping Execution, Auto-Suspend, Auto-Retry, etc.) are only applicable to **Root** and **Independent** tasks. The only exception is **Execute As Specific User**, which can be configured for any task in the graph.

### Stream and Insert or Merge System Columns

| **Column** | **Description** |
|------------|----------------|
| **METADATA$ACTION** | Indicates the DML operation (INSERT, DELETE) recorded |
| **METADATA$ISUPDATE** | Indicates whether operation was UPDATE (shown as DELETE/INSERT pair with TRUE value) |
| **METADATA$ROW_ID** | Unique and immutable row ID for change tracking |

**NOTE: In the WHERE clause, always use the original column name, not with an alias of table name, because aliases are only recognized in the SELECT clause and cannot be used for filtering.**

### Stream and Insert or Merge Deployment

#### Stream and Insert or Merge Deployment Parameters

The Dimension with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is `targetTaskWarehouse` with default value `DEV ENVIRONMENT`.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than `DEV ENVIRONMENT` the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named `compute_wh`.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

#### Stream and Insert or Merge Initial Deployment

For tasks without predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Stream** | Creates Stream in target environment |
| **Create Work Table/Transient Table** | Creates table loaded by task |
| **Target Table Initial Load** | Loads initial data |
| **Create Task** | Creates scheduled task |
| **Resume Task** | Enables task execution |

For tasks with predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Stream** | Creates Stream in target environment |
| **Create Work Table/Transient Table** | Creates target table |
| **Target Table Initial Load** | Loads initial data |
| **Suspend Root Task** | Suspends root task |
| **Create Task** | Creates scheduled task |

If a task is part of a DAG of tasks, the DAG needs to include a node type called `Task DAG Resume Root`. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task-enabled nodes are CREATE OR REPLACE only, though this is subject to change

#### Stream and Insert or Merge Redeployment

Stream redeployment behavior:

| **Redeployment Behavior** | **Stage Executed** |
|--------------------------|-------------------|
| **Create Stream if not exists** | Re-Create Stream at existing offset |
| **Create or Replace** | Create Stream |
| **Create at existing stream** | Re-Create Stream at existing offset |

Table changes execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Rename Table/Alter Column/Delete Column/Add Column/Edit description** | Alters table as needed |
| **Target Initial Load** | If the initial load toggle is enabled and the redeployment behavior of the stream is "Create or Replace," it loads the table with "INSERT OVERWRITE INTO." For all other scenarios, it uses "INSERT INTO." |

If the materialization type is changed from one type to another(transient table/table) the following stages execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table/Transient Table** | Drop the target table|
| **Create Work/Transient table**| Create the target table|
| **Target Initial Load** | If the initial load toggle is enabled and the redeployment behavior of the stream is "Create or Replace," it loads the table with "INSERT OVERWRITE INTO." For all other scenarios, it uses "INSERT INTO." |

Task changes:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Task** | Creates scheduled task |
| **Resume Task**| Resumes the task|

#### Node Type Switching

Node Type switching is supported starting from Coalesce version **7.28+**.

From this version onward, a node’s materialization type can be switched from one supported type to another, subject to certain limitations.

For more info click here - [Node Type Switching Logic and Limitations](#node-type-switching-logic)

### Stream and Insert or Merge Undeployment

When node is deleted, the following stages execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Stream** | Removes the stream |
| **Drop Table** | Drop the table |
| **Drop Current Task** | Drop the task |

---

## Stream for Directory Table

The Coalesce Stream for Directory Table Node Type is a node that allows you to develop and deploy a stream on top of a directory table

More information about Streams can be found in the official [Snowflake Introduction to Streams](https://docs.snowflake.com/en/user-guide/streams-intro).

### Stream for Directory Table Node Configuration

The Stream has two configuration groups:

* [Node Properties](#stream-node-properties)
* [Stream Options](#stream-options)

#### Stream Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Stream will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Stream Options

| **Option** | **Description** |
|------------|----------------|
| **Source Object** | Type of object for stream creation:<br/>**Directory Table**<br/>**Storage Location**: <br/> Storage location of the stage for which directory table is enabled <br/>**Stage Name**: Stage name of the directory table.<br/>**Redeployment Behavior**: Options for redeployment<br/>

### Stream for Directory Table System Columns

A Stream for Directory Table adds nine system columns to the output of the node. These columns can be used together to track INSERT, UPDATE and DELETE operations against a source object.

| **Column** | **Description** |
|------------|----------------|
| **RELATIVE_PATH** | The relative path of the file within the stage.
| **SIZE** | The file size in bytes.
| **LAST_MODIFIED** | The timestamp of the last modification of the file.
| **MD5** | The MD5 hash of the file content for integrity verification.
| **ETAG** | The entity tag (ETag) used for cache validation and versioning.
| **FILE_URL** | The full URL of the file in the stage.
| **METADATA$ACTION** | Indicates the DML operation (INSERT, DELETE) recorded |
| **METADATA$ISUPDATE** | Indicates whether the operation was part of an UPDATE statement. Updates to rows in the source object are represented as a pair of DELETE and INSERT records in the stream with a metadata column METADATA$ISUPDATE values set to TRUE.|
| **METADATA$ROW_ID** | Specifies the unique and immutable ID for the row, used to track changes over time |

### Stream for Directory Table Deployment

#### Stream for Directory Table Deployment Parameters

No deployment parameters are required.

#### Stream for Directory Table Initial Deployment

When deployed for the first time into an environment the Stream node executes:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Stream** | Executes a CREATE OR REPLACE statement to create a Stream in the target environment |

#### Stream for Directory Table Redeployment

After initial deployment, subsequent deployments will create a new stream based on the selected redeployment behavior:

| **Redeployment Behavior** | **Stage Executed** |
|--------------------------|-------------------|
| Create Stream if not exists | Re-Create Stream at existing offset |
| Create or Replace | Create Stream |
| Create at existing stream | Re-Create Stream at existing offset |

#### Node Type Switching

Node Type switching is supported starting from Coalesce version **7.28+**.

From this version onward, a node’s materialization type can be switched from one supported type to another, subject to certain limitations.

For more info click here - [Node Type Switching Logic and Limitations](#node-type-switching-logic)

### Stream for Directory Table Undeployment

When a Stream Node is deleted from a Workspace and that commit is deployed, the following stage executes:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Stream** | Removes the stream from the target environment |

---

## Delta Stream Merge

The Coalesce Delta Stream Merge UDN is a node that allows you to develop and deploy a stream on top of a table, view ,dynamic table,external iceberg and external table. Also, provides option to create a target table to merge data handling inserts,deletes and updates from source with a task on top of it.

A stream logically takes an initial snapshot of every row in the source object (e.g. table, dynamic table, or the underlying tables for a view) by initializing a point in time (called an offset) as the current transactional version of the object. The change tracking system utilized by the stream then records information about the DML changes after this snapshot was taken. Change records provide the state of a row before and after the change. Change information mirrors the column structure of the tracked source object and includes additional metadata columns that describe each change event.

More information about Streams can be found in the official [Snowflake's Introduction to Streams](https://docs.snowflake.com/en/user-guide/streams-intro).

### Delta Stream Merge Node Configuration

The Stream and Insert or Merge node has the following configuration groups:

* [Node Properties](#stream-and-insert-or-merge-node-properties)
* [General Options](#stream-and-insert-or-merge-general-options)
* [Stream Options](#stream-and-insert-or-merge-stream-options)
* [Target Loading Options](#stream-and-insert-or-merge-target-loading-options)
* [Scheduling Options](#stream-and-insert-or-merge-scheduling-options)

#### Delta Stream Merge Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Stream will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Delta Stream Merge General Options

| **Option** | **Description** |
|------------|----------------|
| **Development Mode** | True / False toggle that determines whether a task will be created or if SQL executes as DML<br/>**True** - Table created and SQL executes as Run action<br/>**False** - SQL wrapped in task with specified Scheduling Options. When Run is executed, a message appears prompting the user to wait or suggesting a manual run. |
| **CREATE AS** | Choose target object type:<br/>- Table - Permanent table with data retention and fail-safe<br/>- Transient Table - Temporary table without data retention |
| **DISTINCT** | True/False toggle for DISTINCT in SQL Query<br/>**True** - Group by All invisible, DISTINCT used<br/>**False** - Group by All visible |
| **GROUP BY ALL** | True/False toggle for GROUP BY ALL in SQL Query<br/>**True** - DISTINCT invisible, group by all columns<br/>**False** - DISTINCT visible |

#### Delta Stream Merge Stream Options

| **Option** | **Description** |
|------------|----------------|
| **Source Object** | Type of object for stream creation:<br/>**Table**:<br/>- Append Only Stream: True/False toggle for stream type<br/>- Show Initial Rows: True/False toggle for initial records<br/>- Propagate Deletes : True/False toggle for adding filter for condition METADATA$ACTION != 'DELETE' <br/>- Redeployment Behavior: Options for redeployment<br/>**Dynamic Table**:<br/>**Show Initial Rows**: Specify the records to return the first time the stream is consumed.<br/> **True**: The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset - the normal stream behavior.<br/> **False**: The stream returns any DML changes to the source object since the most recent offset.<br/><br/>**Redeployment Behavior**: Options for redeployment<br/>**View**:<br/>- Append Only Stream: True/False toggle for stream type<br/>- Show Initial Rows: True/False toggle for initial records<br/>- Redeployment Behavior: Options for redeployment**External Table**:<br/>- Show Initial Rows: True/False toggle for initial records<br/>- Redeployment Behavior: Options for redeployment**External Table**:<br/>- Show Initial Rows: True/False toggle for initial records<br/>- Redeployment Behavior: Options for redeployment |

#### Delta Stream Merge Target Loading Options

| **Option** | **Description** |
|------------|----------------|
| **Table keys** | Business key columns for merging data (enabled for Merge load type) |
| **Record Date/Timestamp** | Date/Timestamp column for latest record merging (enabled for Merge load type) |
| **Qualifies selection based on Table Key and Record Date / Timestamp** | True/False toggle for clustering<br/>**True** - Adds qualify keyword to add only latest records from source |
| **Cluster key** | True/False toggle for clustering<br/>**True** - Specify clustering column and expressions. - **Allow Expressions Cluster Key**: Add an expression to the specified cluster key.<br/>**False** - No clustering |

#### Delta Stream Merge Scheduling Options

| **Option** | **Description** |
|------------|----------------|
| **Scheduling Mode** | Choose compute type:<br/>- **Warehouse Task**: User managed warehouse executes tasks<br/>- **Serverless Task**: Uses serverless compute |
| **When Source Stream has Data Flag** | True/False toggle to check for stream data<br/>**True** - Only run task if source stream has capture change data<br/>**False** -  Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false. |
| **Select Warehouse** | Visible if Scheduling Mode is set to Warehouse Task. Enter the name of the warehouse you want the task to run on without quotes.|
| **Select initial serverless size** | Visible when Scheduling Mode is set to Serverless Task.<br/> Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times. |
| **Enable Size Bounds** | Toggle to set explicit limits on serverless scaling. (Visible if **Serverless Task** is selected).<br/>**Validation Rules:**<br/>- Min size must be ≤ Initial size<br/>- Max size must be ≥ Initial size<br/>- Min size must be ≤ Max size |
| **Minimum Warehouse Size** | The smallest compute size allowed for the task (e.g., 1. XSMALL). |
| **Maximum Warehouse Size** | The largest compute size allowed for the task (e.g., 6. XXLARGE). |
| **Task Schedule** | Choose schedule type:<br/>- **Minutes** - Specify interval in minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.<br/>- **Cron** - Uses [Cron expressions](https://docs.coalesce.io/docs/reference/cron-reference/). Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.<br/>- **Predecessor** - Specify dependent tasks |
| **Execution Time** | The specific duration for the task run limit. Supported ranges:<br/>- **SECONDS**: 10 - 691200<br/>- **MINUTES**: 1 - 11520<br/>- **HOURS**: 1 - 192 |
| **Enter predecessor tasks separated by a comma**| Visible when Task Schedule is set to Predecessor. <br/>One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|
| **Root task name** | Visible when Task Schedule is set to Predecessor.<br/> Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|

**NOTE: In the WHERE clause, always use the original column name, not with an alias of table name, because aliases are only recognized in the SELECT clause and cannot be used for filtering.**

#### Delta Stream Merge Advanced Scheduling Options

<img width="776" height="744" alt="image" src="https://github.com/user-attachments/assets/ab28860c-0115-4ca5-9b57-8e1f719ad37a" />

| **Option** | **Description** |
|------------|----------------|
| **Execute As Specific User** | Toggle to run on behalf of another user. Requires `GRANT IMPERSONATE` privileges. |
| **User Name** | The specific user account name used when **Execute As Specific User** is enabled. |
| **Allow Overlapping Execution** | Allows a new instance of the task to start if the previous one is still running. |
| **Enable Task Graph Config** | Enables a text box to provide **Configuration JSON** for the task graph. |
| **Auto-Suspend After Failures** | Automatically suspends the task after a set number of consecutive failures. |
| **Number of Consecutive Failures** | Set the threshold (0 - No Limit) before the task is automatically suspended. <br/>- When toggle is OFF: Parameter is not included (uses Snowflake default of 10).<br/>- When toggle is ON with value 0: **Disables** auto-suspension.<br/>- When toggle is ON with value > 0: **Suspends** after that many consecutive failures. |
| **Enable Auto-Retry** | Toggle to automatically retry the task if it fails. |
| **Retry Attempts** | Specify the number of retry attempts allowed (Range: 0 - 30). |

#### Delta Stream Merge Notification Options

<img width="785" height="371" alt="image" src="https://github.com/user-attachments/assets/d0aae222-cfcb-4497-a572-3fa7079287c8" />

| **Option** | **Description** |
|------------|----------------|
| **Enable Error Notifications** | Toggle to send alerts on failure. Requires an **Error Integration Name**. |
| **Enable Success Notifications** | Toggle to send alerts on success. Requires a **Success Integration Name**. |

> **Note:** Options under **Advanced Scheduling Options** and **Notification Options** (Execution Time, Overlapping Execution, Auto-Suspend, Auto-Retry, etc.) are only applicable to **Root** and **Independent** tasks. The only exception is **Execute As Specific User**, which can be configured for any task in the graph.

### Delta Stream Merge Deployment

#### Delta Stream Merge Deployment Parameters

The Dimension with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is `targetTaskWarehouse` with default value `DEV ENVIRONMENT`.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than `DEV ENVIRONMENT` the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named `compute_wh`.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

#### Delta Stream Merge Initial Deployment

For tasks without predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Stream** | Creates Stream in target environment |
| **Create Work Table/Transient Table** | Creates table loaded by task |
| **Target Table Initial Load** | Loads initial data |
| **Create Task** | Creates scheduled task |
| **Resume Task** | Enables task execution |

For tasks with predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Stream** | Creates Stream in target environment |
| **Create Work Table/Transient Table** | Creates target table |
| **Target Table Initial Load** | Loads initial data |
| **Suspend Root Task** | Suspends root task |
| **Create Task** | Creates scheduled task |

If a task is part of a DAG of tasks, the DAG needs to include a node type called `Task DAG Resume Root`. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task-enabled nodes are CREATE OR REPLACE only, though this is subject to change

#### Delta Stream Merge Redeployment

Stream redeployment behavior:

| **Redeployment Behavior** | **Stage Executed** |
|--------------------------|-------------------|
| **Create Stream if not exists** | Re-Create Stream at existing offset |
| **Create or Replace** | Create Stream |
| **Create at existing stream** | Re-Create Stream at existing offset |

**NOTE:Column Schema Changes results in recreating stream at existing offset irrespective of redeployment beahviour**

Table changes execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Rename Table/Alter Column/Delete Column/Add Column/Edit description** | Alters table as needed |
| **Target Initial Load** | If the initial load toggle is enabled and the redeployment behavior of the stream is "Create or Replace," it loads the table with "INSERT OVERWRITE INTO." For all other scenarios, it uses "Dual Merge statement" |

**NOTE:Column name modification/addition/deletion should be made to mapping grid of DSM node only if the same are done in Upstream source node as here stream is the source for target table.Hence the changes made in mapping grid are not considered**

If the materialization type is changed from one type to another(transient table/table) the following stages execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table/Transient Table** | Drop the target table|
| **Create Work/Transient table**| Create the target table|
| **Target Initial Load** | If the initial load toggle is enabled and the redeployment behavior of the stream is "Create or Replace," it loads the table with "INSERT OVERWRITE INTO." For all other scenarios, it uses "Dual Merge statement" |

Task changes:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Task** | Creates scheduled task |
| **Resume Task**| Resumes the task|

#### Node Type Switching

Node Type switching is supported starting from Coalesce version **7.28+**.

From this version onward, a node’s materialization type can be switched from one supported type to another, subject to certain limitations.

For more info click here - [Node Type Switching Logic and Limitations](#node-type-switching-logic)

### Delta Stream Merge Undeployment

When node is deleted, the following stages execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Stream** | Removes the stream |
| **Drop Table** | Drop the table |
| **Drop Current Task** | Drop the task |

---

## Insert or Merge with Task

The Coalesce Insert or Merge with Task UDN is a node that allows you to create a target table to insert or merge data from source with a task on top of it.

### Insert or Merge with Task Node Configuration

The Insert or Merge with Task node has the following configuration groups:

* [Node Properties](#insert-or-merge-with-task-node-properties)
* [General Options](#insert-or-merge-with-task-general-options)
* [Target Loading Options](#insert-or-merge-with-task-target-loading-options)
* [Scheduling Options](#insert-or-merge-with-task-scheduling-options)

#### Insert or Merge with Task Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | Storage Location where the Task will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |

#### Insert or Merge with Task General Options

| **Option** | **Description** |
|------------|----------------|
| **Development Mode** | True / False toggle that determines whether a task will be created or if SQL executes as DML<br/>**True** - Table created and SQL executes as Run action<br/>**False** - SQL wrapped in task with specified Scheduling Options. When Run is executed, a message appears prompting the user to wait or suggesting a manual run. |
| **CREATE AS** | Choose target object type:<br/>- Table - Permanent table with data retention and fail-safe<br/>- Transient Table - Temporary table without data retention |
| **Cluster key** | True/False toggle for clustering<br/>**True** - Specify clustering column and expressions. - **Allow Expressions Cluster Key**: Add an expression to the specified cluster key.<br/>**False** - No clustering |

#### Insert or Merge with Task Target Loading Options

| **Option** | **Description** |
|------------|----------------|
| **Load Type** | Choose data loading method:<br/>**Insert** - Data inserted from source<br/>**Merge** - Latest record changes merged into target |
| **Table keys** | Business key columns for merging data (enabled for Merge load type) |
| **Record Date/Timestamp** | Date/Timestamp column for latest record merging (enabled for Merge load type) |
| **Multi Source** | True / False toggle that is Coalesce implementation of SQL UNIONs<br/>**True** - Multiple sources can be combined using:<br/>- UNION - Combines with duplicate elimination<br/>- UNION ALL - Combines without duplicate elimination<br/>**False** - Single source node or multiple sources combined using a join |
| **DISTINCT** | True/False toggle for DISTINCT in SQL Query<br/>**True** - Group by All invisible, DISTINCT used<br/>**False** - Group by All visible |
| **GROUP BY ALL** | True/False toggle for GROUP BY ALL in SQL Query<br/>**True** - DISTINCT invisible, group by all columns<br/>**False** - DISTINCT visible |
| **Order By** | True/False toggle that determines whether to add ORDER BY to SQL Query<br/>**True** - Sort column and order options visible<br/>**False** - Sort options invisible |
| **Truncate Before** | True / False toggle that determines if table should be truncated before insert (enabled for Insert load type)<br/>**True** - Uses INSERT OVERWRITE<br/>**False** - Uses INSERT to append data |

#### Insert or Merge with Task Scheduling Options

| **Option** | **Description** |
|------------|----------------|
| **Scheduling Mode** | Choose compute type:<br/>- **Warehouse Task**: User managed warehouse executes tasks<br/>- **Serverless Task**: Uses serverless compute |
| **When Source Stream has Data Flag** | True/False toggle to check for stream data<br/>**True** - Only run task if source stream has capture change data<br/>**False** -  Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false. |
| **Select Warehouse** | Visible if Scheduling Mode is set to Warehouse Task. Enter the name of the warehouse you want the task to run on without quotes.|
| **Select initial serverless size** | Visible when Scheduling Mode is set to Serverless Task.<br/> Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times. |
| **Enable Size Bounds** | Toggle to set explicit limits on serverless scaling. (Visible if **Serverless Task** is selected).<br/>**Validation Rules:**<br/>- Min size must be ≤ Initial size<br/>- Max size must be ≥ Initial size<br/>- Min size must be ≤ Max size |
| **Minimum Warehouse Size** | The smallest compute size allowed for the task (e.g., 1. XSMALL). |
| **Maximum Warehouse Size** | The largest compute size allowed for the task (e.g., 6. XXLARGE). |
| **Task Schedule** | Choose schedule type:<br/>- **Minutes** - Specify interval in minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.<br/>- **Cron** - Uses [Cron expressions](https://docs.coalesce.io/docs/reference/cron-reference/). Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.<br/>- **Predecessor** - Specify dependent tasks |
| **Execution Time** | The specific duration for the task run limit. Supported ranges:<br/>- **SECONDS**: 10 - 691200<br/>- **MINUTES**: 1 - 11520<br/>- **HOURS**: 1 - 192 |
| **Enter predecessor tasks separated by a comma**| Visible when Task Schedule is set to Predecessor. <br/>One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|
| **Root task name** | Visible when Task Schedule is set to Predecessor.<br/> Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.|

#### Insert or Merge with Task Advanced Scheduling Options

<img width="776" height="744" alt="image" src="https://github.com/user-attachments/assets/ab28860c-0115-4ca5-9b57-8e1f719ad37a" />

| **Option** | **Description** |
|------------|----------------|
| **Execute As Specific User** | Toggle to run on behalf of another user. Requires `GRANT IMPERSONATE` privileges. |
| **User Name** | The specific user account name used when **Execute As Specific User** is enabled. |
| **Allow Overlapping Execution** | Allows a new instance of the task to start if the previous one is still running. |
| **Enable Task Graph Config** | Enables a text box to provide **Configuration JSON** for the task graph. |
| **Auto-Suspend After Failures** | Automatically suspends the task after a set number of consecutive failures. |
| **Number of Consecutive Failures** | Set the threshold (0 - No Limit) before the task is automatically suspended. <br/>- When toggle is OFF: Parameter is not included (uses Snowflake default of 10).<br/>- When toggle is ON with value 0: **Disables** auto-suspension.<br/>- When toggle is ON with value > 0: **Suspends** after that many consecutive failures. |
| **Enable Auto-Retry** | Toggle to automatically retry the task if it fails. |
| **Retry Attempts** | Specify the number of retry attempts allowed (Range: 0 - 30). |

#### Insert or Merge with Task Notification Options

<img width="785" height="371" alt="image" src="https://github.com/user-attachments/assets/d0aae222-cfcb-4497-a572-3fa7079287c8" />

| **Option** | **Description** |
|------------|----------------|
| **Enable Error Notifications** | Toggle to send alerts on failure. Requires an **Error Integration Name**. |
| **Enable Success Notifications** | Toggle to send alerts on success. Requires a **Success Integration Name**. |

> **Note:** Options under **Advanced Scheduling Options** and **Notification Options** (Execution Time, Overlapping Execution, Auto-Suspend, Auto-Retry, etc.) are only applicable to **Root** and **Independent** tasks. The only exception is **Execute As Specific User**, which can be configured for any task in the graph.

### Insert or Merge with Task Deployment

#### Insert or Merge with Task Deployment Parameters

The Dimension with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is `targetTaskWarehouse` with default value `DEV ENVIRONMENT`.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than `DEV ENVIRONMENT` the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named `compute_wh`.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

#### Insert or Merge with Task Initial Deployment

For tasks without predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Work Table/Transient Table** | Creates table loaded by task |
| **Create Task** | Creates scheduled task |
| **Resume Task** | Enables task execution |

For tasks with predecessors:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Work Table/Transient Table** | Creates target table |
| **Suspend Root Task** | Suspends root task |
| **Create Task** | Creates scheduled task |

If a task is part of a DAG of tasks, the DAG needs to include a node type called `Task DAG Resume Root`. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task-enabled nodes are CREATE OR REPLACE only, though this is subject to change

#### Insert or Merge with Task Redeployment

Table changes execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Rename Table/Alter Column/Delete Column/Add Column/Edit description** | Alters table as needed |

If the materialization type is changed from one type to another(transient table/table) the following stages execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table/Transient Table** | Drop the target table|
| **Create Work/Transient table**| Create the target table|

Task changes:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Task** | Creates scheduled task |
| **Resume Task**| Resumes the task|

#### Node Type Switching

Node Type switching is supported starting from Coalesce version **7.28+**.

From this version onward, a node’s materialization type can be switched from one supported type to another, subject to certain limitations.

For more info click here - [Node Type Switching Logic and Limitations](#node-type-switching-logic)

### Insert or Merge with Task Undeployment

When node is deleted, the following stages execute:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Table** | Drop the table |
| **Drop Current Task** | Drop the task |

-----------------

#### Node Type Switching Logic
| Current MaterializationType | Desired MaterializationType | Stage |
|------------|--------|-------|
| Task | Task | Follows existing redeployment stages |
| Stream | Stream | Follows existing redeployment stages |
| Table | Task | 1. Warning (if applicable)<br/>2. Alter |
| Any Other | Task | 1. Warning (if applicable)<br/>2. Drop <br/> 3. Create |
| Any Other | Stream | 1. Warning (if applicable)<br/>2. Drop <br/> 3. Create |

Please review the documented limitations before performing a node type switch to ensure compatibility and avoid unintended deployment issues.

#### ⚠ Limitations of Node Type Switching (Current)

| # | Current Materialization | Desired Materialization | Limitation |
|---|--------------------------|--------------------------|------------|
| 1 | Older Version Iceberg Table | Table | Results in `ALTER` failure. Iceberg tables require `ALTER ICEBERG TABLE`. Works only if latest package (with switching support) is already used. |
| 2 | Older Version<br/>Create or Alter-View<br/>Data Quality-DMF | Any(except View) | Switch fails unless current node uses latest package supporting node type switching. |
| 3 | First Node in Pipeline | Any | Not supported. First node is foundational and switching may disrupt the pipeline. |
| 4 | External Packages | Any | Not supported as they typically act as first nodes in the pipeline. |
| 5 | Functional Packages | Any | Not supported due to column re-sync behavior which may cause schema inconsistencies. |
| 6 | Dynamic Dimension / LRV | Any | System columns must be manually dropped before redeployment. |
| 7 | Any | Any Other | After performing node switching, the `Create/Run` in Workspace browser may not work as expected due to changes in the node’s materialization type. |
| 8 | Table(Data Profiling) | Table | This may result in ALTER failure unless latest package is used(with system column removal support)**(Pending Release)** |
| 9 | Any | Any Stream-based Node (Stream, Stream & I/M, Delta Merge, or Directory Stream) | When switching to a Stream-based node, do not select **'Create At Existing Stream'** from the Redeployment Behavior; this causes deployment errors. Use **'Create or Replace'** or **'Create If Not Exists'**. |
| 10 | Stream | Stream for Directory Table (and vice versa) | Metadata columns are not automatically synchronized. Specific directory columns (e.g., `relative_path`, `size`, `md5`) are not added when switching to Directory Table, nor are they removed when switching back to a standard Stream. |
| 11 | Stream | Any Other (and vice versa) | Snowflake CDC metadata columns (`METADATA$ACTION`, `METADATA$ISUPDATE`, `METADATA$ROW_ID`) are not automatically managed. They are neither removed nor added when there's a node type switch |

--------------


## Code

### Work with Task Code

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/WorkwithTask-151/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/WorkwithTask-151/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/WorkwithTask-151/run.sql.j2)

### Dimension with Task

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/DimensionwithTask-149/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/DimensionwithTask-149/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/DimensionwithTask-149/run.sql.j2)

### Fact with Task

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/FactwithTask-150/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/FactwithTask-150/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/FactwithTask-150/run.sql.j2)

### Task DAG Create Root Code

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGCreateRoot-154/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGCreateRoot-154/create.sql.j2)

### Task DAG Resume Root Code

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGResumeRoot-155/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGResumeRoot-155/create.sql.j2)

### Stream Code

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/Stream-153/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/Stream-153/create.sql.j2)

### Stream and Insert or Merge Code

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/StreamandInsertorMerge-152/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/StreamandInsertorMerge-152/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/StreamandInsertorMerge-152/run.sql.j2)

### Stream for Directory Table

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/WorkwithTask-151/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/WorkwithTask-151/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/WorkwithTask-151/run.sql.j2)

### Delta Stream Merge

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/DeltaStreamMerge-483/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/DeltaStreamMerge-483/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/DeltaStreamMerge-483/run.sql.j2)

### Insert OR Merge with Task Code

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/InsertorMergewithTask-567/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/InsertorMergewithTask-567/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/InsertorMergewithTask-567/run.sql.j2)

[Macros](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/macros/macro-1.yml)
