# STREAMS AND TASK NODES

The Coalesce Stream and Task Node Types Package includes:

* [Work with Task](#work-with-task)
* [Dimension with Task](#dimension-with-task)
* [Fact with Task](#fact-with-task)
* [Task DAG Create Root](#task-dag-create-root)
* [Task DAG Resume Root](#task-dag-resume-root)
* [Stream](#stream)
* [Stream and Insert or Merge](#stream-and-insert-or-merge)
* [Iceberg tables with Task](#iceberg-tables-with-task)
* [Code](#code)

<h2 id="work-with-task">Work With Task </h2>

The Coalesce Work with Task UDN is a work node that wraps the standard Coalesce Work node with a Task.

Tasks can be combined with Coalesce Stream node (table streams) for continuous ELT workflows to process recently changed table rows. Streams ensure exactly once semantics for new or changed data in a table.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or performing other periodic work.

More information about Tasks can be found in [Snowflake's Introduction to tasks](https://docs.snowflake.com/en/user-guide/tasks-intro). 

### Work With Task Node Configuration

The Work with Task node has two or three configuration groups depending on config options selected.

With the option Development Mode set to `true` there are three config groups:

* [Node Properties](#work-with-task-node-properties)
* [Options](#work-with-task-options)
* [General Options](#work-with-task-general-options)


![Work_with_dev_true](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/abdec4df-3ea7-414c-a9ce-8c4e834c016e)


With the Development Mode set to `false` there are four config groups:

* [Node Properties](#work-with-task-node-properties)
* [Options](#work-with-task-options)
* [General Options](#work-with-task-general-options)
* [Scheduling Options](#work-with-task-scheduling-options)


![Work_with_dev_false](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/650874b5-7cb2-446d-b842-a7fc662ede2c)

<h4 id="work-with-task-node-properties">Work With Task Node Properties </h4>

* **Storage Location**: Storage Location where the Stream will be created.
* **Node Type**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled**:
  * If TRUE the node will be deployed / redeployed when changes are detected.
  * If FALSE the node will not be deployed or will be dropped during redeployment.
 
<h4 id="work-with-task-options">Work With Task Options</h4>

![Worktask-opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/2582e574-e368-4c00-8172-b259a86a4548)

* **Development Mode**: True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action. Prior to creating a task, it is helpful to test the SQL the task will execute to make sure it runs without errors and returns the expected data.
    * True - A table will be created and SQL will execute as a Run action.
    * False - After sufficiently testing the SQL as a Run action setting Development Mode to false will wrap the SQL statement in a task with options specified in Scheduling Options.
* **Multi Source**: True / False toggle that is Coalesce implementation of SQL UNIONs.
    * True - Multiple sources can be combined in a single node. The sources are combined using the option specified in the Multi Source Strategy.
	    * UNION - Combines with duplicate elimination.
		* UNION ALL - Combines without duplicate elimination.
    * False - Single source node or multiple sources combined using a join.
* **Cluster key**: True/False to determine whether Dimension is to be clustered or not.
    * True - Allows you to specify the column based on which clustering is to be done.
	    *  **Allow Expressions Cluster Key**: True/False.  Add an expression to the specified cluster key.
    * False – No clustering done
 * **Truncate Before**: Specifies that the target table should be truncated before inserting the values into the table.

<h4 id="work-with-task-general-options">Work With Task General Options </h4>

![Work with Task-GO](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/cb8cb62c-c4f1-4c22-87c2-3781a5328dfd)

* **DISTINCT**: True/False toggle that determines whether to add DISTINCT to SQL Query.
    * True - Group by All is invisible. DISTINCT data is chosen for processing.
    * False- Group by All is visible.
* **GROUP BY ALL**: True/False toggle that determines whether to add GROUP BY ALL to SQL Query.
    * True - DISTINCT is invisible. Data is grouped by all columns for processing.
    * False- DISTINCT is visible.
* **Order By** : True/False toggle that determines whether to add “ORDER BY” to SQL query along with the column and sort order.
    * True -Sort column and sort order drop down are visible and are required to form order by clause.
    * False-Sort column and sort order drop down are invisible.


<h4 id="work-with-task-scheduling-options">Work With Task Scheduling Options</h4>

If development mode is set to `false` then Scheduling Options can be used to configure how and when the task will run.

* **Scheduling Mode**: Specifies whether a warehouse or serverless compute is used to run the task 
    * Warehouse Task - User managed warehouse will execute task. 
    * Serverless Task - Utilize serverless compute to execute task.
* **When Source Stream has Data Flag**: True / False toggle that checks whether source streams have data before executing a task.
    * True - Only run task if source stream has capture change data
    * False - Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false.
* **Multiple Stream has Data Logic**: True / False toggle is enabled if Source Stream has Data Flag is set to true.
    * **AND** - If there are multiple streams task will run if all streams have data
    * **OR** - If there are multiple streams task will run if one or more streams has data
* **Select Warehouse on which to run task**: Visible if Scheduling Mode is set to Warehouse Task.
    * Enter the name of the warehouse you want the task to run on without quotes
* **Select initial serverless Warehouse size**: Visible when Scheduling Mode is set to Serverless Task.
    * Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times.
* **Task Schedule**: Select how you want to schedule the task to run
    * **Minutes** - Allows you to specify a minute interval for running task
    * **Cron** - Allows you to specify a CRON schedule for running task
    * **Predecessor** - Allows you to specify a predecessor task to determine when a task should execute
* **Enter task schedule using minutes**: Only visible when Task Schedule is set to Minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.
* **Enter task schedule using Cron**: Only visible when Task Schedule is set to Cron. Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.
* **Enter predecessor tasks separated by a comma**: Only visible when Task Schedule is set to Predecessor. One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.
* **Enter root task name**: Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.

#### Example of Serverless Task with Multiple Predecessors and Root Task

![Example_of_Serverless_Task](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/0af0c04a-5e88-42d1-85de-fd71a792436d)

#### Example of Warehouse Task With 60 Minute Task Schedule

![Example_of_Warehouse_Task](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/04afc6b2-aebc-4346-b1fe-33340e7df0bf)

#### Example of Warehouse Task With Cron Schedule Not Using a Stream

![Example_of_Warehouse_Task_with_Cron](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/89c85551-59af-44e6-b383-a9bdaacf7cce)

### Work With Task Deployment

#### Work With Task Deployment Parameters

The Stage with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

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
When deployed for the first time into an environment the Work with Task node will execute three stages dependent on whether or not the task schedule relies on a predecessor task:

#### Work With Task No Predecessor Task Deployment

* **Create Target Table**: This stage will create a table that will be loaded by the ask

* **Create Task**: This stage will create a task that will load the target table on the schedule specified

* **Resume Task**: After the task has been created it needs to be resume so that the task runs on the schedule

#### Work With Task Predecessor Task Deployment

* **Create Target Table**: This stage will create a table that will be loaded by the ask

* **Suspend Root Task**: To add a task into a DAG of task the root task needs to be put into a suspended state

* **Create Task**: This stage will create a task that will load the target table on the schedule specified

If a task is part of a DAG of tasks, the DAG needs to include a node type called **Task Dag Resume Root**. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task enabled nodes are CREATE OR REPLACE only though this is subject to change.

### Work With Task Redeployment

#### Work With Task Recreating the Task Redeployment

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in task schedule, warehouse, or scheduling options will result in a CREATE TASK AND RESUME TASK statements being issued

The following stages are executed:

####  Work With Task No Predecessor Task Redeployment

* **Create Task**: This stage will create a task that will load the target table on the schedule specified.

* **Resume Task**: After the task has been created it needs to be resume so that the task runs on the schedule.
	 
#### Work With Task Predecessor Task Redeployment

* **Suspend Root Task**: To add a task into a DAG of task the root task needs to be put into a suspended state.

* **Create Task**: This stage will create a task that will load the target table on the schedule specified
	   
#### Work With Task Altering the Tables

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in table like add/drop column and change in data type will result in an ALTER table statement followed by CREATE TASK AND RESUME TASK statements being issued.

The following stages are executed:

* **Change Column Attributes/Delete Column/Add Column/Change table description**: Alter table statement is executed to perform the alter operation accordingly.
* **Create Task**
* **Resume Task**

### Work With Task Undeployment

If a Work with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then all objects created by the node in the target environment will be dropped.

#### Work With Task No Predecessor Task Undeployment

* **Drop Table**: This stage will drop the table originally created to be loaded by the task.

* **Drop Current Task**: This stage will drop the task.

#### Work With Task Predecessor Task Undeployment

* **Drop Table**: This stage will drop the table originally created to be loaded by the task

* **Suspend Root Task**: To drop a task from a DAG of task the root task needs to be put into a suspended state.

* **Drop Task**: This stage will drop the task.

If a task is part of a DAG of tasks the DAG needs to include a node type called **Task Dag Resume Root**. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

## Dimension With Task

The Coalesce Dimension with Task UDN is a node that wraps the standard Coalesce Dimension node with a Task.

Tasks can be combined with Coalesce Stream node (table streams) for continuous ELT workflows to process recently changed table rows. Streams ensure exactly once semantics for new or changed data in a table.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or performing other periodic work.

More information about Tasks can be found in [Snowflake's Introduction to tasks](https://docs.snowflake.com/en/user-guide/tasks-intro). 

###  Dimension With Task Node Configuration

The Dimension with Task node has two or three configuration groups depending on config options selected.

With the option Development Mode set to true there are three config groups:

* [Node Properties](#dimension-with-task-node-properties)
* [Options](#dimension-with-task-options)
* [General Options](#dimension-with-task-general-options)

![Work_with_dev_true](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/266cf7e8-fee3-42b3-b562-b8d6995396d5)

With the Development Mode set to false there are four config groups:

* [Node Properties](#dimension-with-task-node-properties)
* [Options](#dimension-with-task-dimension-work-options)
* [General Options](#dimension-with-task-general-options)
* [Scheduling Options](#dimension-with-task-scheduling-options)

![Work_with_dev_false](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/02f6675c-bcd9-4d22-b68e-e93dd9c5a2c1)

<h4 id="dimension-with-task-node-properties">Dimension With Task Node Properties </h4>

* **Storage Location**: Storage Location where the Stream will be created.
* **Node Type**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled**:
  * If TRUE the node will be deployed / redeployed when changes are detected.
  * If FALSE the node will not be deployed or will be dropped during redeployment.

<h4 id="dimension-with-task-options">Dimension With Task Options</h4>

![Dimension_task_opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/3dfa88ac-b46b-4e92-9639-d45e5a6c242e)


* **Development Mode**: True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action. Prior to creating a task, it is helpful to test the SQL the task will execute to make sure it runs without errors and returns the expected data.
    * True - A table will be created and SQL will execute as a Run action.
    * False - After sufficiently testing the SQL as a Run action, setting Development Mode to false will wrap the SQL statement in a task with options specified in Scheduling Options.
* **Multi Source**: True / False toggle that is Coalesce implementation of SQL UNIONs.
    * True - Multiple sources can be combined in a single node. The sources are combined using the option specified in the Multi Source Strategy.
	    * UNION - Combines with duplicate elimination.
		* UNION ALL - Combines without duplicate elimination.
    * False - Single source node or multiple sources combined using a join.
* **Business key**: It is a required column for both Type 1 and Type 2 Dimensions.
* **Change tracking**: It is a required column for Type 2 Dimension.
* **Cluster key**: True/False to determine whether Dimension is to be clustered or not.
    * True - Allows you to specify the column based on which clustering is to be done.
	    *  **Allow Expressions Cluster Key**: True/False.  Add an expression to the specified cluster key.
    * False – No clustering done

<h4 id="dimension-with-task-general-options">Dimension With Task General Options </h4>


![Work with Task-GO](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/cb8cb62c-c4f1-4c22-87c2-3781a5328dfd)

* **DISTINCT**: True/False toggle that determines whether to add DISTINCT to SQL Query.
    * True - Group by All is invisible. DISTINCT data is chosen for processing.
    * False- Group by All is visible.
* **GROUP BY ALL**: True/False toggle that determines whether to add GROUP BY ALL to SQL Query.
    * True - DISTINCT is invisible. Data is grouped by all columns for processing.
    * False- DISTINCT is visible.
* **Order By** : True/False toggle that determines whether to add “ORDER BY” to SQL query along with the column and sort order.
    * True -Sort column and sort order drop down are visible and are required to form order by clause.
    * False-Sort column and sort order drop down are invisible.


<h4 id="dimension-with-task-scheduling-options">Dimension With Task Scheduling Options</h4>

If development mode is set to `false` then Scheduling Options can be used to configure how and when the task will run.

* **Scheduling Mode**: Specifies whether a warehouse or serverless compute is used to run the task 
    * Warehouse Task - User managed warehouse will execute task. 
    * Serverless Task - Utilize serverless compute to execute task.
* **When Source Stream has Data Flag**: True / False toggle that checks whether source streams have data before executing a task.
    * True - Only run task if source stream has capture change data
    * False - Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false.
* **Multiple Stream has Data Logic**: True / False toggle is enabled if Source Stream has Data Flag is set to true.
    * **AND** - If there are multiple streams task will run if all streams have data
    * **OR** - If there are multiple streams task will run if one or more streams has data
* **Select Warehouse on which to run task**: Visible if Scheduling Mode is set to Warehouse Task.
    * Enter the name of the warehouse you want the task to run on without quotes
* **Select initial serverless Warehouse size**: Visible when Scheduling Mode is set to Serverless Task.
    * Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times.
* **Task Schedule**: Select how you want to schedule the task to run
    * **Minutes** - Allows you to specify a minute interval for running task
    * **Cron** - Allows you to specify a CRON schedule for running task
    * **Predecessor** - Allows you to specify a predecessor task to determine when a task should execute
* **Enter task schedule using minutes**: Only visible when Task Schedule is set to Minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.
* **Enter task schedule using Cron**: Only visible when Task Schedule is set to Cron. Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.
* **Enter predecessor tasks separated by a comma**: Only visible when Task Schedule is set to Predecessor. One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.
* **Enter root task name**: Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.


### Dimension With Task Deployment

#### Dimension With Task Deployment Parameters

The Dimension with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

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

#### Dimension With Task Initial Deployment
When deployed for the first time into an environment the Dimension with Task node will execute three stages dependent on whether or not the task schedule relies on a predecessor task:

#### Dimension With Task No Predecessor Task Deployment

* **Create Target Table**: This stage will create a table that will be loaded by the task.
* **Create Task**: This stage will create a task that will load the target table on the schedule specified.
* **Resume Task**: After the task has been created it needs to be resume so that the task runs on the schedule.

####  Dimension With Task Predecessor Task Deployment

* **Create Target Table**: This stage will create a table that will be loaded by the task.
* **Suspend Root Task**: To add a task into a DAG of task the root task needs to be put into a suspended state.
* **Create Task**: This stage will create a task that will load the target table on the schedule specified.

If a task is part of a DAG of tasks the DAG needs to include a node type called `Task Dag Resume Root`. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task enabled nodes are CREATE OR REPLACE only though this is subject to change.

### Dimension With Task Redeployment

#### Dimension With Task Recreating the Task Redeployment

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in task schedule,warehouse or scheduling options will result in a CREATE TASK AND RESUME TASK statements being issued.

#### Dimension With Task No Predecessor Task Redeployment

* **Create Task**: This stage will create a task that will load the target table on the schedule specified.
* **Resume Task**: After the task has been created it needs to be resume so that the task runs on the schedule.
	 
#### Dimension With Task Predecessor Task Redeployment

* **Suspend Root Task**: To add a task into a DAG of task the root task needs to be put into a suspended state.
* **Create Task**: This stage will create a task that will load the target table on the schedule specified.
	   
#### Dimension With Task Altering the Tables Redeployment

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in table like add/drop column,change in data type will result in an ALTER table statement followed by CREATE TASK AND RESUME TASK statements being issued.

The following stages are executed:

* **Change Column Attributes/Delete Column/Add Column/Change table description**: Alter table statement is executed to perform the alter operation accordingly.
* **Create Task**
* **Resume Task**

### Dimension With Task Undeployment

If a Dimension with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher-level environment then all objects created by the node in the target environment will be dropped.

#### Dimension With Task No Predecessor Task Undeployment

* **Drop Table**: This stage will drop the table originally created to be loaded by the task.
* **Drop Current Task**: This stage will drop the task.

#### Dimension With Task Predecessor Task Undeployment

* **Drop Table**: This stage will drop the table originally created to be loaded by the task.
* **Suspend Root Task**: To drop a task from a DAG of task the root task needs to be put into a suspended state.
* **Drop Task**: This stage will drop the task.

If a task is part of a DAG of tasks the DAG needs to include a node type called **Task Dag Resume Root**. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

## Fact With Task

The Coalesce Fact with Task UDN is a node that wraps the standard Coalesce Fact node with a Task.

Tasks can be combined with Coalesce Stream node (table streams) for continuous ELT workflows to process recently changed table rows. Streams ensure exactly once semantics for new or changed data in a table.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or performing other periodic work.

More information about Tasks can be found in [Snowflake's Introduction to tasks](https://docs.snowflake.com/en/user-guide/tasks-intro). 

### Fact With Task Node Configuration
The Work with Task node has two or three configuration groups depending on config options selected.

With the option Development Mode set to `true` there are three config groups:

* [Node Properties](#fact-with-task-node-properties)
* [Options](#fact-with-task-options)
* [General Options](#fact-with-task-general-options)

![Work_with_dev_true](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/abdec4df-3ea7-414c-a9ce-8c4e834c016e)


With the Development Mode set to `false` Scheduling Options is added.

* [Node Properties](#fact-with-task-node-properties)
* [Options](#fact-with-task-options)
* [General Options](#fact-with-task-general-options)
* [Scheduling Options](#fact-with-task-scheduling-options)


![Work_with_dev_false](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/650874b5-7cb2-446d-b842-a7fc662ede2c)

<h4 id="fact-with-task-node-properties">Fact With Task Node Properties </h4>

* **Storage Location**: Storage Location where the Stream will be created.
* **Node Type**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled**:
  * If TRUE the node will be deployed / redeployed when changes are detected.
  * If FALSE the node will not be deployed or will be dropped during redeployment.
 
<h4 id="fact-with-task-options">Fact With Task Options</h4>

![Worktask-opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/2582e574-e368-4c00-8172-b259a86a4548)

* **Development Mode**: True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action. Prior to creating a task, it is helpful to test the SQL the task will execute to make sure it runs without errors and returns the expected data.
    * True - A table will be created and SQL will execute as a Run action.
    * False - After sufficiently testing the SQL as a Run action setting Development Mode to false will wrap the SQL statement in a task with options specified in Scheduling Options.
* **Multi Source**: True / False toggle that is Coalesce implementation of SQL UNIONs.
    * True - Multiple sources can be combined in a single node. The sources are combined using the option specified in the Multi Source Strategy.
	    * UNION - Combines with duplicate elimination.
		* UNION ALL - Combines without duplicate elimination.
    * False - Single source node or multiple sources combined using a join.
* **Cluster key**: True/False to determine whether Dimension is to be clustered or not.
    * True - Allows you to specify the column based on which clustering is to be done.
	    *  **Allow Expressions Cluster Key**: True/False.  Add an expression to the specified cluster key.
    * False – No clustering done
 * **Truncate Before**: Specifies that the target table should be truncated before inserting the values into the table.

<h4 id="fact-with-task-general-options">Fact With Task General Options </h4>

![Work with Task-GO](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/cb8cb62c-c4f1-4c22-87c2-3781a5328dfd)

* **DISTINCT**: True/False toggle that determines whether to add DISTINCT to SQL Query.
    * True - Group by All is invisible. DISTINCT data is chosen for processing.
    * False- Group by All is visible.
* **GROUP BY ALL**: True/False toggle that determines whether to add GROUP BY ALL to SQL Query.
    * True - DISTINCT is invisible. Data is grouped by all columns for processing.
    * False- DISTINCT is visible.
* **Order By** : True/False toggle that determines whether to add “ORDER BY” to SQL query along with the column and sort order.
    * True -Sort column and sort order drop down are visible and are required to form order by clause.
    * False-Sort column and sort order drop down are invisible.


<h4 id="fact-with-task-scheduling-options">Fact With Task Scheduling Options</h4>

If development mode is set to `false` then Scheduling Options can be used to configure how and when the task will run.

* **Scheduling Mode**: Specifies whether a warehouse or serverless compute is used to run the task 
    * Warehouse Task - User managed warehouse will execute task. 
    * Serverless Task - Utilize serverless compute to execute task.
* **When Source Stream has Data Flag**: True / False toggle that checks whether source streams have data before executing a task.
    * True - Only run task if source stream has capture change data
    * False - Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false.
* **Multiple Stream has Data Logic**: True / False toggle is enabled if Source Stream has Data Flag is set to true.
    * **AND** - If there are multiple streams task will run if all streams have data
    * **OR** - If there are multiple streams task will run if one or more streams has data
* **Select Warehouse on which to run task**: Visible if Scheduling Mode is set to Warehouse Task.
    * Enter the name of the warehouse you want the task to run on without quotes
* **Select initial serverless Warehouse size**: Visible when Scheduling Mode is set to Serverless Task.
    * Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times.
* **Task Schedule**: Select how you want to schedule the task to run
    * **Minutes** - Allows you to specify a minute interval for running task
    * **Cron** - Allows you to specify a CRON schedule for running task
    * **Predecessor** - Allows you to specify a predecessor task to determine when a task should execute
* **Enter task schedule using minutes**: Only visible when Task Schedule is set to Minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.
* **Enter task schedule using Cron**: Only visible when Task Schedule is set to Cron. Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.
* **Enter predecessor tasks separated by a comma**: Only visible when Task Schedule is set to Predecessor. One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.
* **Enter root task name**: Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.


### Fact With Task Deployment

#### Fact With Task Deployment Parameters

The Fact with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

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

#### Fact With Task Initial Deployment

When deployed for the first time into an environment the Fact with Task node will execute three stages dependent on whether or not the task schedule relies on a predecessor task:

#### Fact With Task No Predecessor Task Initial Deployment

* **Create Target Table**: This stage will create a table that will be loaded by the task.
* **Create Task**: This stage will create a task that will load the target table on the schedule specified.
* **Resume Task**: After the task has been created it needs to be resume so that the task runs on the schedule.

#### Fact With Task Predecessor Task Initial Deployment

* **Create Target Table**: This stage will create a table that will be loaded by the ask
* **Suspend Root Task**: To add a task into a DAG of task the root task needs to be put into a suspended state.
* **Create Task**: This stage will create a task that will load the target table on the schedule specified.

If a task is part of a DAG of tasks the DAG needs to include a node type called **Task Dag Resume Root**. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task enabled nodes are CREATE OR REPLACE only though this is subject to change.

### Fact With Task Redeployment

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in task schedule,warehouse or scheduling options will result in a CREATE TASK AND RESUME TASK statements being issued

The following stages are executed:

#### Fact With Task Redeployment No Predecessor Task

* **Create Task**: This stage will create a task that will load the target table on the schedule specified.
* **Resume Task**: After the task has been created it needs to be resume so that the task runs on the schedule.
	 
#### Fact With Task Redeployment Predecessor Task

* **Suspend Root Task**: To add a task into a DAG of task the root task needs to be put into a suspended state.
* **Create Task**: This stage will create a task that will load the target table on the schedule specified.
	   
####  Fact With Task Altering the tables

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in table like add/drop column,change in data type will result in an ALTER table statement followed by CREATE TASK AND RESUME TASK statements being issued.

The following stages are executed:

* **Change Column Attributes/Delete Column/Add Column/Change table description**: Alter table statement is executed to perform the alter operation accordingly.
* **Create Task**
* **Resume Task**

### Fact With Task Undeployment

If a Fact with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then all objects created by the node in the target environment will be dropped.

#### Fact With Task Undeployment No Predecessor Task

* **Drop Table**: This stage will drop the table originally created to be loaded by the task/
* **Drop Current Task**: This stage will drop the task.

#### Fact With Task Undeployment Predecessor Task

* **Drop Table**: This stage will drop the table originally created to be loaded by the task.
* **Suspend Root Task**: To drop a task from a DAG of task the root task needs to be put into a suspended state.
* **Drop Task**: This stage will drop the task.

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

## Task DAG Create Root

The Coalesce Task DAG Create Root UDN is a node that helps to create a standalone root task.

The root task should have a defined schedule that initiates a run of the DAG. Each of the other tasks has at least one defined predecessor to link the tasks in the DAG. A child task runs only after all of its predecessor tasks run successfully to completion.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or perform other periodic work.

More information about Tasks can be found in [Snowflake's Introduction to tasks](https://docs.snowflake.com/en/user-guide/tasks-intro). 

### Task DAG Create Root Node Configuration

* [Node Properties](#task-dag-create-root-node-properties)
* [Scheduling Options](#task-dag-create-root-scheduling-options)

![Task_dag_create_root_config](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/873b2da2-44c8-423d-a005-8f5ab42f659b)

<h4 id="task-dag-create-root-node-properties">Task DAG Create Root Node Properties</h4>

* **Storage Location**: Storage Location where the Stream will be created.
* **Node Type**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled**:
  * If TRUE the node will be deployed / redeployed when changes are detected.
  * If FALSE the node will not be deployed or will be dropped during redeployment.

<h4 id="task-dag-create-root-scheduling-options">Task DAG Create Root Scheduling Options</h4>

The Scheduling Options can be used to configure how and when the task will run.

There are 4 to 5 options that can be selected depending on combinations of configs that are selected:

![Task_dag_create_root_scheduling_options](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/79323e3a-4674-4676-9441-4497e1788680)

* **Scheduling Options**: Specifies whether a warehouse or serverless compute is used to run the task 
    * Warehouse Task - User managed warehouse will execute task. 
    * Serverless Task - Utilize serverless compute to execute task.
* **Select Warehouse on which to run task**: Visible if Scheduling Mode is set to Warehouse Task
    * Enter the name of the warehouse you want the task to run on without quotes
* **Select initial serverless Warehouse size**: Visible when Scheduling Mode is set to Serverless Task
    * Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times.
* **Task Schedule**: Select how you want to schedule the task to run
    * **Minutes** - Allows you to specify a minute interval for running task
    * **Cron** - Allows you to specify a CRON schedule for running task
* **Enter root task SQL**: The SQL statement associated to be run when a standalone root task to be executed.

### Task DAG Create Root Deployment

### Task DAG Create Root Deployment Parameters
The Dimension with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is `targetTaskWarehouse` and the default value is `DEV ENVIRONMENT`.

When set to`DEV ENVIRONMENT` the value entered in the Scheduling Options config Select Warehouse on which to run the task will be used when creating the task.

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

### Task DAG Create Root Initial Deployment
When deployed for the first time into an environment the Task DAG Create Root node will execute two stages.

#### Task DAG Create Root Task

* **Suspend Root Task**: To create a root task, the root task needs to be put into a suspended state
* **Create Root Task**: This stage will create a task that will execute the Root SQL on the schedule specified

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

### Task DAG Create Root Redeployment

After the Task has deployed for the first time into a target environment, subsequent deployments will execute two stages Suspend Root Task and Create Root task

###  Task DAG Create Root Undeployment

If a Dimension with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher-level environment then two stages are executed

* **Suspend Root task**
* **Drop current task**

## Task DAG Resume Root

The Coalesce Task DAG Resume Root UDN is a node type that helps to resume the root task and its dependents or child tasks. Recursively resumes all dependent tasks tied to a specified root task in a DAG using the root task name specified.

The root task should have a defined schedule that initiates a run of the DAG. Each of the other tasks has at least one defined predecessor to link the tasks in the DAG. A child task runs only after all of its predecessor tasks run successfully to completion.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or perform other periodic work.

More information about Tasks can be found in the official Snowflake documentation here - (https://docs.snowflake.com/en/user-guide/tasks-intro).

### Task DAG Resume Root Node Configuration

* [Node Properties](#task-dag-resume-root-node-properties)
* [Scheduling Options](#task-dag-resume-root-scheduling-options)

![Task_dag_create_root_config](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/39067a1a-a617-4488-a0e7-cc5a4fbb2a25)

<h4 id="task-dag-resume-root-node-properties">Task DAG Resume Root Node Node Properties</h4>

* **Storage Location**: Storage Location where the Stream will be created.
* **Node Type**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled**:
  * If TRUE the node will be deployed / redeployed when changes are detected.
  * If FALSE the node will not be deployed or will be dropped during redeployment.
  
<h4 id="task-dag-resume-root-scheduling-options"> Task DAG Resume Root Scheduling Options </h4>

The Scheduling Options prompts to mention the root task which needs to be resumed. It recursively resumes all dependent tasks tied to a specified root task

![Task_dag_resume_root_scheduling_options](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/1dadf5e1-b846-4f2f-b09a-1a3722e60cdf)

### Task DAG Resume Root Deployment

#### Task DAG Resume RootInitial Deployment
When deployed for the first time into an environment the Dimension with Task node will execute:

**Try Enable Root Task**: to resume root and all its dependents.

If a task is part of a DAG of tasks the DAG needs to include a node type called **Task Dag Resume Root**. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

#### Task DAG Resume Root Undeployment

If a Dimension with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher-level environment then two stages are executed

# Stream

The Coalesce Stream UDN is a node that allows you to develop and deploy a stream on top of a table, view or external table.

A stream logically takes an initial snapshot of every row in the source object (e.g. table, external table, or the underlying tables for a view) by initializing a point in time (called an offset) as the current transactional version of the object. The change tracking system utilized by the stream then records information about the DML changes after this snapshot was taken. Change records provide the state of a row before and after the change. Change information mirrors the column structure of the tracked source object and includes additional metadata columns that describe each change event.

More information about Streams can be found in the official [Snowflake's Introduction to Streams](https://docs.snowflake.com/en/user-guide/streams-intro).

### Stream Node Configuration
The Stream has two configuration groups:

* [Node Properties](#stream-node-properties)
* [Stream Options](#stream-options)

![Str-opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/97ee7db3-3a4d-46d2-9908-d21fe811fdce)

<h4 id="stream-node-properties"> Stream Node Properties </h4>

* **Storage Location**: Storage Location where the Stream will be created.
* **Node Type**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled**:
  * If TRUE the node will be deployed / redeployed when changes are detected.
  * If FALSE the node will not be deployed or will be dropped during redeployment.

<h4 id="stream-options"> Stream Options </h4>

* **Source Object**: The type of object the stream will be created on. One of three options is required to be selected and this selection drives what other configs are available:
    * **Table**
	    * **Append Only Stream**: True / False Toggle to set what type of stream is created.
		    * True - Specifies an append-only stream.
		    * False - Specifies a standard stream.
	    * **Show Initial Rows**: True / False Toggle to specify the records to return the first time the stream is consumed.
			* True - The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset; that is, the normal stream behavior.
			* False - The stream returns any DML changes to the source object since the most recent offset.
		* **Redeployment Behavior**: Options to determine redeployment behavior.
    * **View**:
	   * **Append Only Stream**: True / False Toggle to set what type of stream is created.
		    * True - Specifies an append-only stream.
		    * False - Specifies a standard stream.
		* **Show Initial Rows**: True / False Toggle to specify the records to return the first time the stream is consumed.
		    * True - The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset; that is, the normal stream behavior.
			* False - The stream returns any DML changes to the source object since the most recent offset.
		* **Redeployment Behavior**: Options to determine redeployment behavior.
    * **External table**:
                * **Redeployment Behavior**: Options to determine redeployment behavior.
    * **External iceberg table**:
               * **Redeployment Behavior**: Options to determine redeployment behavior.

### Stream System Columns

A Stream UDN adds three system columns to the output of the node.

* **METADATA$ACTION**
* **METADATA$ISUPDATE**
* **METADATA$ROW_ID**

These columns can be used together to track INSERT, UPDATE and DELETE operations against a source object.

#### METADATA$ACTION
Indicates the DML operation (INSERT, DELETE) recorded.

#### METADATA$ISUPDATE
Indicates whether the operation was part of an UPDATE statement. Updates to rows in the source object are represented as a pair of DELETE and INSERT records in the stream with a metadata column METADATA$ISUPDATE values set to TRUE.

#### METADATA$ROW_ID
Specifies the unique and immutable ID for the row, which can be used to track changes to specific rows over time.

###  Stream Deployment

#### Stream Deployment Parameters
No deployment parameters are required.

#### Stream Initial Deployment
When deployed for the first time into an environment the Stream node will execute stage:

**Create Stream**
This stage will execute a CREATE OR REPLACE statement and create a Stream in the target environment.

### Stream Redeployment

After the Stream has deployed for the first time into a target environment, subsequent deployments will result in a new stream creation based on redeployment behavior chosen.

| Redeployment Behavior | Stage Executed |
|---|---|
| Create Stream if not exists| Re-Create Stream at existing offset|
| Create or Replace | Create Stream|
| Create at existing stream |  Re-Create Stream at existing offset |


### Stream Undeployment

If a Stream Node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the Stream in the target environment will be dropped.

This is executed as a single stage:

* **Drop Stream**

## Stream-Insert or Merge

The Coalesce Streams and Insert or Merge UDN is a node that allows you to develop and deploy a stream on top of a table, view or external table.Also,provides option to create a target table to insert or merge data from source with a task on top of it.

A stream logically takes an initial snapshot of every row in the source object (e.g. table, external table, or the underlying tables for a view) by initializing a point in time (called an offset) as the current transactional version of the object. The change tracking system utilized by the stream then records information about the DML changes after this snapshot was taken. Change records provide the state of a row before and after the change. Change information mirrors the column structure of the tracked source object and includes additional metadata columns that describe each change event.

More information about Streams can be found in the official [Snowflake's Introduction to Streams](https://docs.snowflake.com/en/user-guide/streams-intro).

### Stream-Insert or Merge Node Configuration

* [Node Properties](#node-properties)
* [General Options](#general-options)
* [Stream Options](#stream-options)
* [Target Loading Options](#target-loading-options)
* [Scheduling Options](#scheduling-options)

![SIM_Opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/0389b770-60d3-48ec-b944-4ff7a8f1b938)

<h4> Stream-Insert or Merge Node Properties </h4>

* **Storage Location**: Storage Location where the Stream will be created.
* **Node Type**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled**:
  * If TRUE the node will be deployed / redeployed when changes are detected.
  * If FALSE the node will not be deployed or will be dropped during redeployment.

#### Stream-Insert or Merge General Options

![SIM-GO](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/169126315/51559454-e26f-42bd-8814-171f6c02cff9)


* **Development Mode**: True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action. Prior to creating a task, it is helpful to test the SQL the task will execute to make sure it runs without errors and returns the expected data.
    * True - A table will be created and SQL will execute as a Run action.
    * False - After sufficiently testing the SQL as a Run action setting Development Mode to false will wrap the SQL statement in a task with options specified in Scheduling Options.
- **CREATE AS**: In this dropdown you can select if you want to create target object as 'Table' or 'Transient Table' .
  - Table - A permanent table in Snowflake with data retention and fail-safe features for long-term storage and data protection.
  - Transient Table- A temporary table in Snowflake without data retention and fail-safe features, suitable for intermediate data processing.
* **DISTINCT**: True/False toggle that determines whether to add DISTINCT to SQL Query.
    * True - Group by All is invisible. DISTINCT data is chosen for processing
    * False- Group by All is visible
* **GROUP BY ALL**: True/False toggle that determines whether to add GROUP BY ALL to SQL Query.
    * True - DISTINCT is invisible. Data is grouped by all columns for processing
    * False- DISTINCT is visible
	
#### Stream-Insert or Merge Stream Options


* **Source Object**: The type of object the stream will be created on. One of three options is required to be selected and this selection drives what other configs are available:
    * **Table**
	    * **Append Only Stream**: True / False Toggle to set what type of stream is created.
		    * True - Specifies an append-only stream.
		    * False - Specifies a standard stream.
	    * **Show Initial Rows**: True / False Toggle to specify the records to return the first time the stream is consumed.
			* True - The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset; that is, the normal stream behavior.
			* False - The stream returns any DML changes to the source object since the most recent offset.
		* **Redeployment Behavior**: Options to determine redeployment behavior.
    * **View**:
	   * **Append Only Stream**: True / False Toggle to set what type of stream is created.
		    * True - Specifies an append-only stream.
		    * False - Specifies a standard stream.
		* **Show Initial Rows**: True / False Toggle to specify the records to return the first time the stream is consumed.
		    * True - The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset; that is, the normal stream behavior.
			* False - The stream returns any DML changes to the source object since the most recent offset.
		* **Redeployment Behavior**: Options to determine redeployment behavior.

#### Stream-Insert or Merge Target Loading Options

![SIM_TL](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/31950bef-b62a-4f47-b7ff-b4e20f95ac4e)

* **Load Type**:
    * **Insert**:The data is inserted into the target table from source.
    * **Merge**:The latest record changes in the source are merged into target.
* **Table key(s)**: Enabled if target load type is Merge.The business key(s) column(s) based on which the data is merged into Target table.
* **Record Date/Timestamp**:Enabled if target load type is Merge.Date/TImestamp column based on which latest record is merged into Target table.
* **Cluster key**: True/False to determine whether Dimension is to be clustered or not.
    * True - Allows you to specify the column based on which clustering is to be done.
	    *  **Allow Expressions Cluster Key**: True/False.  Add an expression to the specified cluster key.
    * False – No clustering done
	
#### Stream-Insert or Merge Scheduling Options

If development mode is set to false then Scheduling Options can be used to configure how and when the task will run.

![SIM_scheduling](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/8cc53638-8d41-4869-9358-51b41d68e04e)


* **Scheduling Mode**: Specifies whether a warehouse or serverless compute is used to run the task 
    * Warehouse Task - User managed warehouse will execute tasks. 
    * Serverless Task - Utilize serverless compute to execute tasks.
* **When Source Stream has Data Flag**: True / False toggle that checks whether source streams have data before executing a task.
    * True - Only run task if source stream has capture change data
    * False - Run tasks on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false.
* **Select Warehouse on which to run task**: Visible if Scheduling Mode is set to Warehouse Task
    * Enter the name of the warehouse you want the task to run on without quotes
* **Select initial serverless Warehouse size**: Visible when Scheduling Mode is set to Serverless Task
    * Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times.
* **Task Schedule**: Select how you want to schedule the task to run
    * **Minutes** - Allows you to specify a minute interval for running task
    * **Cron** - Allows you to specify a CRON schedule for running task
    * **Predecessor** - Allows you to specify a predecessor task to determine when a task should execute
* **Enter task schedule using minutes**: Only visible when Task Schedule is set to Minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.
* **Enter task schedule using Cron**: Only visible when Task Schedule is set to Cron. Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.
* **Enter predecessor task(s) separated by a comma**: Only visible when Task Schedule is set to Predecessor. One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor tasks separate the task names using a comma and no spaces.
* **Enter root task name**: Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor tasks separate the task names using a comma and no spaces.

### Stream-Insert or Merge System Columns

A Stream UDN adds three system columns to the output of the node. These columns can be used together to track INSERT, UPDATE and DELETE operations against a source object.

* `METADATA$ACTION`: Indicates the DML operation (INSERT, DELETE) recorded.
* `METADATA$ISUPDATE`: Indicates whether the operation was part of an UPDATE statement. Updates to rows in the source object are represented as a pair of DELETE and INSERT records in the stream with a metadata column METADATA$ISUPDATE values set to TRUE.
* `METADATA$ROW_ID`: Specifies the unique and immutable ID for the row, which can be used to track changes to specific rows over time.

### Stream-Insert or Merge Deployment

#### Stream-Insert or Merge Deployment Parameters
The Dimension with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is targetTaskWarehouse and the default value is `DEV ENVIRONMENT`.

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

### Stream-Insert or Merge Initial Deployment
When deployed for the first time into an environment the Stream and Insert or Merge node will execute four to six stages dependent on whether or not the task schedule relies on a predecessor task:

#### Stream-Insert or Merge  Deployment No Predecessor Task

**Create Stream**
This stage will execute a CREATE OR REPLACE statement and create a Stream in the target environment.

**Create Work Table/Transient Table**
This stage will create a table that will be loaded by the task.

**Target Table Initial Load**
Loads initial data into table

**Create Task**
This stage will create a task that will load the target table on the schedule specified.

**Resume Task**
After the task has been created it needs to be resume so that the task runs on the schedule.

#### Stream-Insert or Merge  Deployment Predecessor Task

**Create Stream**
This stage will execute a CREATE OR REPLACE statement and create a Stream in the target environment.

**Create Work Table/Transient Table**
This stage will create a table that will be loaded by the ask.

**Target Table Initial Load**
Loads initial data into table

**Suspend Root Task**
To add a task into a DAG of task the root task needs to be put into a suspended state.

**Create Task**
This stage will create a task that will load the target table on the schedule specified.

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task enabled nodes are CREATE OR REPLACE only though this is subject to change.

### Stream-Insert or Merge Redeployment

| Redeployment Behavior | Stage Executed |
|---|---|
| Create Stream if not exists| Re-Create Stream at existing offset|
| Create or Replace | Create Stream|
| Create at existing stream |  Re-Create Stream at existing offset |

#### Table

There are few column or table changes like Change in table name,Dropping existing column, Alter Column data type,Adding a new column if made in isolation or all-together will result in an ALTER statement to modify the Work Table in the target environment.
The following stages are executed:

* **Rename Table| Alter Column | Delete Column | Add Column | Edit table description**: Alter table statement is executed to perform the alter operation.
* **Target Inital Load** :If the initial load toggle is enabled and the redeployment behavior of the stream is "Create or Replace," it loads the table with "INSERT OVERWRITE INTO." For all other scenarios, it uses "INSERT INTO."

If the materialization type is changed from transient table to table or vie versa,the following stages are executed
* **Drop table/transient table**
* **Create Work table/transient table**
* **Target Inital Load** :If the initial load toggle is enabled and the redeployment behavior of the stream is "Create or Replace," it loads the table with "INSERT OVERWRITE INTO." For all other scenarios, it uses "INSERT INTO."

#### Task
Redeployment with changes in the stream or table will result in the creation and resumption of the task.

### Stream-Insert or Merge Undeployment

If a Stream Node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the Stream in the target environment will be dropped.

This is executed in following stages:

* Drop Stream
* Drop Table
* Drop Current Task
  
<h2 id="iceberg-tables-with-task">Iceberg table with Task</h2>

Iceberg tables with task node is an external iceberg table node wrapped with a task functionality.

An Iceberg table uses the Apache Iceberg open table format specification, which provides an abstraction layer on data files stored in open formats.[Iceberg tables](https://docs.snowflake.com/en/user-guide/tables-iceberg) for Snowflake combine the performance and query semantics of regular Snowflake tables with external cloud storage that you manage. They are ideal for existing data lakes that you cannot, or choose not to, store in Snowflake.

An Iceberg table that uses an external catalog provides limited Snowflake platform support with read-only access. With this table type, Snowflake uses a catalog integration to retrieve information about your Iceberg metadata and schema.

You can use this option to create an Iceberg table registered in the AWS Glue Data Catalog or to create a table from Iceberg metadata files in object storage.

### Iceberg table with task Configuration

The Snowflake Iceberg table has three configuration groups:

* [Node Properties](#iceberg-table-node-properties)
* [Iceberg Options](#iceberg-table-options)
* [Scheduling Options](#iceberg-task-scheduling-options)

Go to the node and select the **Config tab** to see the Node Properties,Iceberg Options.

<h3 id="iceberg-table-node-properties">Iceberg table with task node properties</h3>

There are four configs within the **Node Properties** group.

* **Storage Location(required)**: Storage Location where the Dynamic Table will be created.
* **Node Type(required)**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled(required)**:
  * If TRUE the node will be deployed or redeployed when changes are detected.
  * If FALSE the node will not be deployed or the node will be dropped during redeployment.

<h3 id="iceberg-table-options">Iceberg Options</h3>

* **Type of Catalog**:Specify the type of catalog
                    *AWS Glue
                    *Object Storage
* **Snowflake EXTERNAL VOLUME name**: Specifies the identifier (name) for the external volume where the Iceberg table stores its metadata files and data in Parquet format.[External volume](https://docs.snowflake.com/sql-reference/sql/create-external-volume) needs to be created in snowflake as a prerequisite.
* **Catalog integration**: Specifies the identifier (name) of the [catalog integration](https://docs.snowflake.com/user-guide/tables-iceberg#label-tables-iceberg-catalog-integration-def) for this table.
* **Catalog namespace**:Optionally specifies the namespace (for example, my_glue_database) for the AWS Glue Data Catalog source.Option available if AWS Glue catalog is chosen.
* **Catalog table name**:Name of the catalog table.Option available if AWS Glue catalog is chosen.
* **Metadata filepath**:Specifies the relative path of the Iceberg metadata file to use for column definitions.Option available if Object Storage Catalog is chosen.

<h3 id="iceberg-task-scheduling-options">Scheduling Options</h3>

If schedule refresh mode is set to true then Scheduling Options can be used to configure how and when the task will run.

* **Scheduling Mode**: Specifies whether a warehouse or serverless compute is used to run the task 
    * Warehouse Task - User managed warehouse will execute tasks. 
    * Serverless Task - Utilize serverless compute to execute tasks.
* **Select Warehouse on which to run task**: Visible if Scheduling Mode is set to Warehouse Task
    * Enter the name of the warehouse you want the task to run on without quotes
* **Select initial serverless Warehouse size**: Visible when Scheduling Mode is set to Serverless Task
    * Select the initial compute size on which to run the task. Snowflake will adjust size from there based on target schedule and task run times.
* **Task Schedule**: Select how you want to schedule the task to run
    * **Minutes** - Allows you to specify a minute interval for running task
    * **Cron** - Allows you to specify a CRON schedule for running task
    * **Predecessor** - Allows you to specify a predecessor task to determine when a task should execute
* **Enter task schedule using minutes**: Only visible when Task Schedule is set to Minutes. Enter a whole number from 1 to 11520 which represents the number of minutes between task runs.
* **Enter task schedule using Cron**: Only visible when Task Schedule is set to Cron. Specifies a cron expression and time zone for periodically running the task. Supports a subset of standard cron utility syntax.
* **Enter predecessor task(s) separated by a comma**: Only visible when Task Schedule is set to Predecessor. One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor tasks separate the task names using a comma and no spaces.
* **Enter root task name**: Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor tasks separate the task names using a comma and no spaces.

 ## Prerequisites
 
 * The Role we mention in the Workspace and Environment properties of Coalesce should be 'ACCOUNTADMIN' inorder to successfully create an  iceberg table.You can also grant SYSADMIN roles to EXTERNAL VOLUME,CATALOG INTEGRATION created.
 * An EXTERNAL VOLUME,CATALOG INTEGRATION is expected to be created in Snowflake at the Storage Location chosen in the Node properties.

#### Iceberg tables  With Task Deployment Parameters

The Iceberg tables  With Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

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

 ### Iceberg tables with task Initial Deployment

When deployed for the first time into an environment the Work with Task node will execute three stages dependent on whether or not the task schedule relies on a predecessor task:

#### Iceberg tables with task No Predecessor Task Deployment

* **Create Iceberg Table**: This stage will execute a `CREATE OR REPLACE` statement and create a Iceberg Table in the target environment.
* **Create Task**: This stage will create a task that will load the target table on the schedule specified
* **Resume Task**: After the task has been created it needs to be resume so that the task runs on the schedule
 
#### Iceberg tables with task Predecessor Task Deployment

* **Create Iceberg Table**: This stage will execute a `CREATE OR REPLACE` statement and create a Iceberg Table in the target environment.
* **Suspend Root Task**: To add a task into a DAG of task the root task needs to be put into a suspended state
* **Create Task**: This stage will create a task that will load the target table on the schedule specified

If a task is part of a DAG of tasks, the DAG needs to include a node type called **Task Dag Resume Root**. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task enabled nodes are CREATE OR REPLACE only though this is subject to change.

### Iceberg tables with Task Redeployment

#### Recreating a Snowflake Iceberg table

If any changes in config options like external volume,baselocation ,node properties ,column  results in recreating iceberg table during redeployment

* **Create Iceberg Table**: This stage will execute a `CREATE OR REPLACE` statement and create a Iceberg Table in the target environment.
  
#### Iceberg tables With Task Recreating the Task Redeployment

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in task schedule, warehouse, or scheduling options will result in a CREATE TASK AND RESUME TASK statements being issued

The following stages are executed:

####  Iceberg tables With Task No Predecessor Task Redeployment

* **Create Task**: This stage will create a task that will load the target table on the schedule specified.
* **Resume Task**: After the task has been created it needs to be resume so that the task runs on the schedule.
	 
#### Iceberg tables With Task Predecessor Task Redeployment

* **Suspend Root Task**: To add a task into a DAG of task the root task needs to be put into a suspended state.
* **Create Task**: This stage will create a task that will load the target table on the schedule specified


###  External Iceberg table Undeployment

If a snowflake iceberg table with task  is dropped from the workspace and commited to GIT results in table and task dropped from target environment.
This is executed in the below stages:

* **Drop Iceberg Table**

#### Iceberg tables With Task No Predecessor Task Undeployment

* **Drop Iceberg Table**
* **Drop Current Task**: This stage will drop the task.

#### Iceberg tables With Task Predecessor Task Undeployment

* **Drop Iceberg Table**
* **Suspend Root Task**: To drop a task from a DAG of task the root task needs to be put into a suspended state.
* **Drop Task**: This stage will drop the task.

If a task is part of a DAG of tasks the DAG needs to include a node type called **Task Dag Resume Root**. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

<h1 id="code">Code</h1>

## Code

### Work with Task

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

###  Task DAG Create Root 

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGCreateRoot-154/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGCreateRoot-154/create.sql.j2)

### Task DAG Resume Root

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGResumeRoot-155/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGResumeRoot-155/create.sql.j2)

### Stream

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/Stream-153/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/Stream-153/create.sql.j2)

### Stream and Insert or Merge

* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/StreamandInsertorMerge-152/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/StreamandInsertorMerge-152/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/StreamandInsertorMerge-152/run.sql.j2)

[Macros](https://github.com/Coalesce-Software-Inc/coalesce_marketplace/blob/7ed5ad0830c6352f80046993a0664db8d980e7ac/Code-files/macros_basenodetypes.txt)


[<img src="https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/c1c65350-9f97-4307-8a75-75447dc1b252" alt="Git Logo" height="40">](https://github.com/coalesceio/Streams-and-Task-Nodes)
