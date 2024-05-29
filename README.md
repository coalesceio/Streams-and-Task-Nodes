# STREAMS AND TASK NODES

The Coalesce Stream and Task Node Types Package includes:

* [WORK WITH TASK](#work-with-task)
* [DIMENSION WITH TASK](#dimension-with-task)
* [FACT WITH TASK](#fact-with-task)
* [TASK DAG CREATE ROOT](#task-dag-create-root)
* [TASK DAG RESUME ROOT](#task-dag-resume-root)
* [STREAM](#stream)
* [STREAM AND INSERT OR MERGE](#stream-and-insert-or-merge)
* [CODE](#code)

# Work with Task

The Coalesce Work with Task UDN is a work node that wraps the standard Coalesce Work node with a Task.

Tasks can be combined with Coalesce Stream node (table streams) for continuous ELT workflows to process recently changed table rows. Streams ensure exactly once semantics for new or changed data in a table.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or performing other periodic work.

More information about Tasks can be found in the official Snowflake documentation here - (https://docs.snowflake.com/en/user-guide/tasks-intro).

## Node Configuration
The Work with Task node has two or three configuration groups depending on config options selected.

With the option Development Mode set to true there are two config groups:

* [Node Properties](#node-properties)
* [Options](#options)
* [General Options](#general-options)


![Work_with_dev_true](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/abdec4df-3ea7-414c-a9ce-8c4e834c016e)


With the Development Mode set to false there are three config groups:

* [Node Properties](#node-properties)
* [Options](#options)
* [General Options](#general-options)
* [Scheduling Options](#scheduling-options)


![Work_with_dev_false](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/650874b5-7cb2-446d-b842-a7fc662ede2c)

### Node Properties
There are four configs within the **Node Properties** group.

* **Storage Location**: Storage Location where the Stream will be created.
* **Node Type**: Name of template used to create node objects.
* **Description**: A description of the node's purpose.
* **Deploy Enabled**:
  * If TRUE the node will be deployed / redeployed when changes are detected.
  * If FALSE the node will not be deployed or will be dropped during redeployment.
    
### Options

There are four configs within the Work with Task Options group.

![Worktask-opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/2582e574-e368-4c00-8172-b259a86a4548)

* **Development Mode**: True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action. Prior to creating a task, it is helpful to test the SQL the task will execute to make sure it runs without errors and returns the expected data.
    * True - A table will be created and SQL will execute as a Run action.
    * False - After sufficiently testing the SQL as a Run action setting Development Mode to false will wrap the SQL statement in a task with options specified in Scheduling Options.
* **Multi Source**: True / False toggle that is Coalesce implementation of SQL UNIONs.
    * True - Multiple sources can be combined in a single node. The sources are combined using the option specified in the Multi Source Strategy.
    * False - Single source node or multiple sources combined using a join.
* **Cluster key**: True/False to determine whether Dimension is to be clustered or not
    * True -Allows you to specify the column based on which clustering is to be done.
            -Allow Expressions Cluster Key-True ->allows to add an expression to the specified cluster key
    * False – No clustering done

#### Multi Source False

#### Multi Source True

When Multi Source is set to true an additional option for Multi Source Strategy is required. In the case of a Work with Task there are two options:

* UNION - Combines with duplicate elimination.
* UNION ALL - Combines without duplicate elimination.

### General Options

![Work with Task-GO](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/cb8cb62c-c4f1-4c22-87c2-3781a5328dfd)

* **DISTINCT**: True/False toggle that determines whether to add DISTINCT to SQL Query.
    * True - Group by All is invisible. DISTINCT data is chosen for processing
    * False- Group by All is visible
* **GROUP BY ALL**: True/False toggle that determines whether to add GROUP BY ALL to SQL Query.
    * True - DISTINCT is invisible. Data is grouped by all columns for processing
    * False- DISTINCT is visible
* **Order By** : True/False toggle that determines whether to add “ORDER BY” to SQL query along with the column and sort order
    * True -Sort column and sort order drop down are visible and are required to form order by clause
    * False-Sort column and sort order drop down are invisible
      
### Scheduling Options

If development mode is set to false then Scheduling Options can be used to configure how and when the task will run.

There are 5 to 7 options that can be selected depending on combinations of configs that are selected:

* **Scheduling Mode**: Specifies whether a warehouse or serverless compute is used to run the task 
    * Warehouse Task - User managed warehouse will execute task. 
    * Serverless Task - Utilize serverless compute to execute task.
* **When Source Stream has Data Flag**: True / False toggle that checks whether source streams have data before executing a task.
    * True - Only run task if source stream has capture change data
    * False - Run task on schedule regardless of whether the source stream has data. If the source is not a stream should set this to false.
* **Multiple Stream has Data Logic**: True / False toggle is enabled if Source Stream has Data Flag is set to true.
    * **AND** - If there are multiple streams task will run if all streams have data
    * **OR** - If there are multiple streams task will run if one or more streams has data
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
* **Enter predecessor task(s) separated by a comma**: Only visible when Task Schedule is set to Predecessor. One or more task names that precede the task being created in the current node. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.
* **Enter root task name**: Name of the root task that controls scheduling for the DAG of tasks. Task names are case sensitive, should not be quoted and must exist in the same schema in which the current task is being created. If there are multiple predecessor task separate the task names using a comma and no spaces.

#### Example of Serverless Task with Multiple Predecessors and Root Task

![Example_of_Serverless_Task](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/0af0c04a-5e88-42d1-85de-fd71a792436d)

#### Example of Warehouse Task with 60 Minute Task Schedule

![Example_of_Warehouse_Task](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/04afc6b2-aebc-4346-b1fe-33340e7df0bf)

#### Example of Warehouse Task with Cron Schedule not Using a Stream

![Example_of_Warehouse_Task_with_Cron](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/89c85551-59af-44e6-b383-a9bdaacf7cce)

## Deployment

### Deployment Parameters
The Stage with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is targetTaskWarehouse and the default value is “DEV ENVIRONMENT”.

When set to “DEV ENVIRONMENT” the value entered in the Scheduling Options config Select Warehouse on which to run the task will be used when creating the task.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than “DEV ENVIRONMENT” the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named compute_wh.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

### Initial Deployment
When deployed for the first time into an environment the Work with Task node will execute three stages dependent on whether or not the task schedule relies on a predecessor task:

#### No Predecessor Task

* **Create Target Table**
      This stage will create a table that will be loaded by the ask

* **Create Task**
      This stage will create a task that will load the target table on the schedule specified

* **Resume Task**
      After the task has been created it needs to be resume so that the task runs on the schedule

#### Predecessor Task

* **Create Target Table**
   This stage will create a table that will be loaded by the ask

* **Suspend Root Task**
    To add a task into a DAG of task the root task needs to be put into a suspended state

* **Create Task**
    This stage will create a task that will load the target table on the schedule specified

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task enabled nodes are CREATE OR REPLACE only though this is subject to change.

### Redeployment

#### Recreating the task

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in task schedule,warehouse or scheduling options will result in a CREATE TASK AND RESUME TASK statements being issued

The following stages are executed:

 No Predecessor Task

* **Create Task**
    This stage will create a task that will load the target table on the schedule specified
* **Resume Task**
    After the task has been created it needs to be resume so that the task runs on the schedule
	 
Predecessor Task

* **Suspend Root Task**
    To add a task into a DAG of task the root task needs to be put into a suspended state
* **Create Task**
    This stage will create a task that will load the target table on the schedule specified
	   
#### Altering the tables

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in table like add/drop column,change in data type will result in an ALTER table statement followed by CREATE TASK AND RESUME TASK statements being issued.

The following stages are executed

* **Change Column Attributes/Delete Column/Add Column/Change table description**
    Alter table statement is executed to perform the alter operation accordingly
* **Create Task**
* **Resume Task**

### Undeployment

If a Work with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then all objects created by the node in the target environment will be dropped.

This is executed as three single stage:

#### No Predecessor Task

* Drop Table
This stage will drop the table originally created to be loaded by the task

* Drop Current Task
This stage will drop the task

#### Predecessor Task

* Drop Table
This stage will drop the table originally created to be loaded by the task

* Suspend Root Task
To drop a task from a DAG of task the root task needs to be put into a suspended state

* Drop Task
This stage will drop the task

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

# Dimension with Task

The Coalesce Dimension with Task UDN is a node that wraps the standard Coalesce Dimension node with a Task.

Tasks can be combined with Coalesce Stream node (table streams) for continuous ELT workflows to process recently changed table rows. Streams ensure exactly once semantics for new or changed data in a table.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or performing other periodic work.

More information about Tasks can be found in the official Snowflake documentation here - (https://docs.snowflake.com/en/user-guide/tasks-intro).

## Node Configuration

The Dimension with Task node has two or three configuration groups depending on config options selected.

With the option Development Mode set to true there are three config groups:

* [Node Properties](#node-properties)
* [Options](#dimension-with-task-options)
* [General Options](#general-options)

![Work_with_dev_true](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/266cf7e8-fee3-42b3-b562-b8d6995396d5)

With the Development Mode set to false there are four config groups:

* [Node Properties](#node-properties)
* [Options](#dimension-work-options)
* [General Options](#general-options)
* [Scheduling Options](#scheduling-options)

![Work_with_dev_false](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/02f6675c-bcd9-4d22-b68e-e93dd9c5a2c1)

### Dimension with task Options

![Dimension_task_opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/3dfa88ac-b46b-4e92-9639-d45e5a6c242e)

There are four configs within the Options group.

* **Development Mode**: True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action. Prior to creating a task, it is helpful to test the SQL the task will execute to make sure it runs without errors and returns the expected data.
    * True - A table will be created and SQL will execute as a Run action.
    * False - After sufficiently testing the SQL as a Run action setting Development Mode to false will wrap the SQL statement in a task with options specified in Scheduling Options.
* **Multi Source**: True / False toggle that is Coalesce implementation of SQL UNIONs.
    * True - Multiple sources can be combined in a single node. The sources are combined using the option specified in the Multi Source Strategy.
    * False - Single source node or multiple sources combined using a join.
* **Business key**: It is a required column for both Type 1 and Type 2 Dimensions.
* **Change tracking**: It is a required column for Type 2 Dimension.
* **Cluster key**: True/False to determine whether Dimension is to be clustered or not
    * True -Allows you to specify the column based on which clustering is to be done.
            -Allow Expressions Cluster Key-True ->allows to add an expression to the specified cluster key
    * False – No clustering done

#### Multi Source False

#### Multi Source True

When Multi Source is set to true an additional option for Multi Source Strategy is required. In the case of a Dimension with Task there are two options:

* UNION - Combines with duplicate elimination.
* UNION ALL - Combines without duplicate elimination.

## Deployment

### Deployment Parameters
The Dimension with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is targetTaskWarehouse and the default value is “DEV ENVIRONMENT”.

When set to “DEV ENVIRONMENT” the value entered in the Scheduling Options config Select Warehouse on which to run task will be used when creating the task.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than “DEV ENVIRONMENT” the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named compute_wh.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

### Initial Deployment
When deployed for the first time into an environment the Dimension with Task node will execute three stages dependent on whether or not the task schedule relies on a predecessor task:

#### No Predecessor Task

**Create Target Table**
This stage will create a table that will be loaded by the ask

**Create Task**
This stage will create a task that will load the target table on the schedule specified

**Resume Task**
After the task has been created it needs to be resume so that the task runs on the schedule

#### Predecessor Task

**Create Target Table**
This stage will create a table that will be loaded by the ask

**Suspend Root Task**
To add a task into a DAG of task the root task needs to be put into a suspended state

**Create Task**
This stage will create a task that will load the target table on the schedule specified

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task enabled nodes are CREATE OR REPLACE only though this is subject to change.

### Redeployment

#### Recreating the task

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in task schedule,warehouse or scheduling options will result in a CREATE TASK AND RESUME TASK statements being issued

The following stages are executed 

 No Predecessor Task

* **Create Task**
    This stage will create a task that will load the target table on the schedule specified
* **Resume Task**
    After the task has been created it needs to be resume so that the task runs on the schedule
	 
Predecessor Task

* **Suspend Root Task**
    To add a task into a DAG of task the root task needs to be put into a suspended state
* **Create Task**
    This stage will create a task that will load the target table on the schedule specified
	   
#### Altering the tables

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in table like add/drop column,change in data type will result in an ALTER table statement followed by CREATE TASK AND RESUME TASK statements being issued.

The following stages are executed

* **Change Column Attributes/Delete Column/Add Column/Change table description**
    Alter table statement is executed to perform the alter operation accordingly
* **Create Task**
* **Resume Task**

### Undeployment

If a Dimension with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher-level environment then all objects created by the node in the target environment will be dropped.

This is executed as three single stage:

#### No Predecessor Task

* Drop Table
This stage will drop the table originally created to be loaded by the task

* Drop Current Task
This stage will drop the task

#### Predecessor Task

* Drop Table
This stage will drop the table originally created to be loaded by the task

* Suspend Root Task
To drop a task from a DAG of task the root task needs to be put into a suspended state

* Drop Task
This stage will drop the task

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

# Fact with Task

The Coalesce Fact with Task UDN is a node that wraps the standard Coalesce Fact node with a Task.

Tasks can be combined with Coalesce Stream node (table streams) for continuous ELT workflows to process recently changed table rows. Streams ensure exactly once semantics for new or changed data in a table.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or performing other periodic work.

More information about Tasks can be found in the official Snowflake documentation here - (https://docs.snowflake.com/en/user-guide/tasks-intro).

## Node Configuration
The Fact with Task node has two or three configuration groups depending on config options selected.

With the option Development Mode set to true there are three config groups:

* [Node Properties](#node-properties)
* [Options](#fact-with-task-options)
* [General Options](#general-options)

![Work_with_dev_true](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/b46dd68f-6ff3-4389-ba58-92d9a3879f19)

With the Development Mode set to false there are four config groups:

* [Node Properties](#node-properties)
* [Options](#fact-with-task-options)
* [General Options](#general-options)
* [Scheduling Options](#scheduling-options)

![Work_with_dev_false](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/767d6bbf-2459-446a-a86a-02423ab076f7)

### Fact with task Options

There are four configs within the Options group.

![Fact_task_options](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/89336d95-0cd4-494e-baec-04b6ace4b994)

* **Development Mode**: True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action. Prior to creating a task, it is helpful to test the SQL the task will execute to make sure it runs without errors and returns the expected data.
    * True - A table will be created and SQL will execute as a Run action.
    * False - After sufficiently testing the SQL as a Run action setting Development Mode to false will wrap the SQL statement in a task with options specified in Scheduling Options.
* **Multi Source**: True / False toggle that is Coalesce's implementation of SQL UNIONs.
    * True - Multiple sources can be combined in a single node. The sources are combined using the option specified in the Multi Source Strategy.
    * False - Single source node or multiple sources combined using a join.
* **Business key**: It is a required column for Fact table creation.
* **Cluster key **: True/False to determine whether Fact is to be clustered or not
    * True -Allows you to specify the column based on which clustering is to be done.
            -Allow Expressions Cluster Key-True ->allows to add an expression to the specified cluster key
    * False – No clustering done

#### Multi Source False

#### Multi Source True

When Multi Source is set to true an additional option for Multi Source Strategy is required. In the case of a Fact with Task there are two options:

* UNION - Combines with duplicate elimination.
* UNION ALL - Combines without duplicate elimination.

## Deployment

### Deployment Parameters
The Fact with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is targetTaskWarehouse and the default value is “DEV ENVIRONMENT”.

When set to “DEV ENVIRONMENT” the value entered in the Scheduling Options config Select Warehouse on which to run the task will be used when creating the task.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than “DEV ENVIRONMENT” the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named compute_wh.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

### Initial Deployment
When deployed for the first time into an environment the Fact with Task node will execute three stages dependent on whether or not the task schedule relies on a predecessor task:

#### No Predecessor Task

**Create Target Table**
This stage will create a table that will be loaded by the ask

**Create Task**
This stage will create a task that will load the target table on the schedule specified

**Resume Task**
After the task has been created it needs to be resume so that the task runs on the schedule

#### Predecessor Task

**Create Target Table**
This stage will create a table that will be loaded by the ask

**Suspend Root Task**
To add a task into a DAG of task the root task needs to be put into a suspended state

**Create Task**
This stage will create a task that will load the target table on the schedule specified

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task enabled nodes are CREATE OR REPLACE only though this is subject to change.

### Redeployment

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in task schedule,warehouse or scheduling options will result in a CREATE TASK AND RESUME TASK statements being issued

The following stages are executed:

 No Predecessor Task

* **Create Task**
    This stage will create a task that will load the target table on the schedule specified
* **Resume Task**
    After the task has been created it needs to be resume so that the task runs on the schedule
	 
Predecessor Task

* **Suspend Root Task**
    To add a task into a DAG of task the root task needs to be put into a suspended state
* **Create Task**
    This stage will create a task that will load the target table on the schedule specified
	   
#### Altering the tables

After the Task has deployed for the first time into a target environment, subsequent deployments with changes in table like add/drop column,change in data type will result in an ALTER table statement followed by CREATE TASK AND RESUME TASK statements being issued.

The following stages are executed:

* **Change Column Attributes/Delete Column/Add Column/Change table description**
    Alter table statement is executed to perform the alter operation accordingly
* **Create Task**
* **Resume Task**

### Undeployment

If a Fact with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then all objects created by the node in the target environment will be dropped.

This is executed as three single stage:

#### No Predecessor Task

* Drop Table
This stage will drop the table originally created to be loaded by the task

* Drop Current Task
This stage will drop the task

#### Predecessor Task

* Drop Table
This stage will drop the table originally created to be loaded by the task

* Suspend Root Task
To drop a task from a DAG of task the root task needs to be put into a suspended state

* Drop Task
This stage will drop the task

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

# Task DAG Create Root

The Coalesce Task DAG Create Root UDN is a node that helps to create a standalone root task.

The root task should have a defined schedule that initiates a run of the DAG. Each of the other tasks has at least one defined predecessor to link the tasks in the DAG. A child task runs only after all of its predecessor tasks run successfully to completion.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or perform other periodic work.

More information about Tasks can be found in the official Snowflake documentation here - (https://docs.snowflake.com/en/user-guide/tasks-intro).

## Node Configuration

* [Node Properties](#node-properties)
* [Scheduling Options](#task-dag-create-root-scheduling-options)

![Task_dag_create_root_config](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/873b2da2-44c8-423d-a005-8f5ab42f659b)

### Task DAG Create Root Scheduling Options

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

## Deployment

### Deployment Parameters
The Dimension with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is targetTaskWarehouse and the default value is “DEV ENVIRONMENT”.

When set to “DEV ENVIRONMENT” the value entered in the Scheduling Options config Select Warehouse on which to run the task will be used when creating the task.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than “DEV ENVIRONMENT” the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named compute_wh.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

### Initial Deployment
When deployed for the first time into an environment the Task DAG Create Root node will execute two stages

#### Root Task

**Suspend Root Task**
To create a root task, the root task needs to be put into a suspended state

**Create Root Task**
This stage will create a task that will execute the Root SQL on the schedule specified

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

### Redeployment

After the Task has deployed for the first time into a target environment, subsequent deployments will execute two stages Suspend Root Task and Create Root task

### Undeployment

If a Dimension with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher-level environment then two stages are executed

* Suspend Root task
* Drop current task

# Task DAG Resume Root

The Coalesce Task DAG Resume Root UDN is a node type that helps to resume the root task and its dependents or child tasks. Recursively resumes all dependent tasks tied to a specified root task in a DAG using the root task name specified.

The root task should have a defined schedule that initiates a run of the DAG. Each of the other tasks has at least one defined predecessor to link the tasks in the DAG. A child task runs only after all of its predecessor tasks run successfully to completion.

Tasks can also be used independently to generate periodic reports by inserting or merging rows into a report table or perform other periodic work.

More information about Tasks can be found in the official Snowflake documentation here - (https://docs.snowflake.com/en/user-guide/tasks-intro).

## Node Configuration

* [Node Properties](#node-properties)
* [Scheduling Options](#task-dag-resume-root-scheduling-options)

![Task_dag_create_root_config](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/39067a1a-a617-4488-a0e7-cc5a4fbb2a25)

### Task DAG Resume Root Scheduling Options

The Scheduling Options prompts to mention the root task which needs to be resumed. It Recursively resumes all dependent tasks tied to a specified root task

![Task_dag_resume_root_scheduling_options](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/1dadf5e1-b846-4f2f-b09a-1a3722e60cdf)

## Deployment

### Initial Deployment
When deployed for the first time into an environment the Dimension with Task node will execute

“Try Enable Root Task” to resume root and all its dependents

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

### Undeployment

If a Dimension with Task node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher-level environment then two stages are executed

# Stream

The Coalesce Stream UDN is a node that allows you to develop and deploy a stream on top of a table, view or external table.

A stream logically takes an initial snapshot of every row in the source object (e.g. table, external table, or the underlying tables for a view) by initializing a point in time (called an offset) as the current transactional version of the object. The change tracking system utilized by the stream then records information about the DML changes after this snapshot was taken. Change records provide the state of a row before and after the change. Change information mirrors the column structure of the tracked source object and includes additional metadata columns that describe each change event.

More information about Streams can be found in the official Snowflake documentation here - (https://docs.snowflake.com/en/user-guide/streams-intro).

## Node Configuration
The Stream has two configuration groups:

* [Node Properties](#node-properties)
* [Stream Options](#stream-options)

![Str-opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/97ee7db3-3a4d-46d2-9908-d21fe811fdce)

### Stream Options

There are three configs within the Stream Options group.

* **Source Object**: The type of object the stream will be created on. One of three options is required to be selected and this selection drives what other configs are available
    * **Table
    * **View
    * **External Table
  
![Str-opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/7a77d23f-698a-42e4-99b5-90a3f33f8170)

#### Table 

* **Append Only Stream**: True / False Toggle to set what type of stream is created.
    * True - Specifies an append-only stream.
    * False - Specifies a standard stream.
* **Show Initial Rows**: True / False Toggle to specify the records to return the first time the stream is consumed.
    * True - The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset; that is, the normal stream behavior.
    * False - The stream returns any DML changes to the source object since the most recent offset.
	
#### External Table

* **Insert Only Stream**: True / False Toggle to set what type of stream is created.
    * True - An insert-only stream tracks row inserts only; they do not record delete operations that remove rows from an inserted set (i.e. no-ops)..
    * False - Specifies a standard stream.

#### View

* **Append Only Stream**: True / False Toggle to set what type of stream is created.
    * True - Specifies an append-only stream.
    * False - Specifies a standard stream.
* **Show Initial Rows**: True / False Toggle to specify the records to return the first time the stream is consumed.
    * True - The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset; that is, the normal stream behavior.
	* False - The stream returns any DML changes to the source object since the most recent offset.

### System Columns

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

## Deployment

### Deployment Parameters
No deployment parameters are required.

### Initial Deployment
When deployed for the first time into an environment the Stream node will execute one stage:

**Create Stream**
This stage will execute a CREATE OR REPLACE statement and create a Stream in the target environment.

### Redeployment

After the Stream has deployed for the first time into a target environment, subsequent deployments will result in a new stream creation based on redeployment behavior chosen.

Redeployment behavior-Create Stream if not exists

The following stage is executed:
 
     1.Re-Create Stream at existing offset

Redeployment behavior-Create or Replace

The following stage is executed:

     1.Create Stream

Redeployment behavior-Create at existing stream

The following stage is executed:
    
     1.Re-Create Stream at existing offset
     
### Undeployment

If a Stream Node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the Stream in the target environment will be dropped.

This is executed as a single stage:

* Drop Stream

# Stream-Insert or Merge

The Coalesce Streams and Insert or Merge UDN is a node that allows you to develop and deploy a stream on top of a table, view or external table.Also,provides option to create a target table to insert or merge data from source with a task on top of it.

A stream logically takes an initial snapshot of every row in the source object (e.g. table, external table, or the underlying tables for a view) by initializing a point in time (called an offset) as the current transactional version of the object. The change tracking system utilized by the stream then records information about the DML changes after this snapshot was taken. Change records provide the state of a row before and after the change. Change information mirrors the column structure of the tracked source object and includes additional metadata columns that describe each change event.

More information about Streams can be found in the official Snowflake documentation here - (https://docs.snowflake.com/en/user-guide/streams-intro).

## Node Configuration
The Stream has two configuration groups:

* [Node Properties](#node-properties)
* [General Options](#general-options)
* [Stream Options](#stream-options)
* [Target Loading Options](#target-loading-options)
* [Scheduling Options](#scheduling-options)

![SIM_Opt](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/0389b770-60d3-48ec-b944-4ff7a8f1b938)

### General Options

![SIM_GO](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/ae40d3c8-8b22-4003-b17e-f5c6e5a8b775)

* **Development Mode**: True / False toggle that determines whether a task will be created or if the SQL to be used in the task will execute as DML as a Run action. Prior to creating a task, it is helpful to test the SQL the task will execute to make sure it runs without errors and returns the expected data.
    * True - A table will be created and SQL will execute as a Run action.
    * False - After sufficiently testing the SQL as a Run action setting Development Mode to false will wrap the SQL statement in a task with options specified in Scheduling Options.
* **DISTINCT**: True/False toggle that determines whether to add DISTINCT to SQL Query.
    * True - Group by All is invisible. DISTINCT data is chosen for processing
    * False- Group by All is visible
* **GROUP BY ALL**: True/False toggle that determines whether to add GROUP BY ALL to SQL Query.
    * True - DISTINCT is invisible. Data is grouped by all columns for processing
    * False- DISTINCT is visible
	
### Stream Options

There are three configs within the Stream Options group.

* **Source Object**: The type of object the stream will be created on. One of three options is required to be selected and this selection drives what other configs are available
    * **Table
    * **View
    * **External Table
	

#### Table

* **Append Only Stream**: True / False Toggle to set what type of stream is created.
    * True - Specifies an append-only stream.
    * False - Specifies a standard stream.
* **Show Initial Rows**: True / False Toggle to specify the records to return the first time the stream is consumed.
    * True - The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset; that is, the normal stream behavior.
    * False - The stream returns any DML changes to the source object since the most recent offset.

#### External Table

* **Insert Only Stream**: True / False Toggle to set what type of stream is created.
    * True - An insert-only stream tracks row inserts only; they do not record delete operations that remove rows from an inserted set (i.e. no-ops)..
    * False - Specifies a standard stream.

#### View

* **Append Only Stream**: True / False Toggle to set what type of stream is created.
    * True - Specifies an append-only stream.
    * False - Specifies a standard stream.
* **Show Initial Rows**: True / False Toggle to specify the records to return the first time the stream is consumed.
    * True - The stream returns only the rows that existed in the source object at the moment when the stream was created. Subsequently, the stream returns any DML changes to the source object since the most recent offset; that is, the normal stream behavior.
    * False - The stream returns any DML changes to the source object since the most recent offset.

### System Columns

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

### Target Loading Options

![SIM_TL](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/31950bef-b62a-4f47-b7ff-b4e20f95ac4e)

* **Load Type**:
    * **Insert**:The data is inserted into the target table from source.
    * **Merge**:The latest record changes in the source are merged into target.
* **Table key(s)**: Enabled if target load type is Merge.The business key(s) column(s) based on which the data is merged into Target table.
* **Record Date/Timestamp**:Enabled if target load type is Merge.Date/TImestamp column based on which latest record is merged into Target table.
* **Cluster key **: True/False to determine whether the target table is to be clustered or not.
    * True -Allows you to specify the column based on which clustering is to be done.
            -Allow Expressions Cluster Key-True ->allows to add an expression to the specified cluster key.
    * False – No clustering done.
	
### Scheduling Options

If development mode is set to false then Scheduling Options can be used to configure how and when the task will run.

![SIM_scheduling](https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/8cc53638-8d41-4869-9358-51b41d68e04e)

There are 5 to 7 options that can be selected depending on combinations of configs that are selected:

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

## Deployment

### Deployment Parameters
The Dimension with Task includes an environment parameter that allows you to specify a different warehouse used to run a task in different environments.

The parameter name is targetTaskWarehouse and the default value is “DEV ENVIRONMENT”.

When set to “DEV ENVIRONMENT” the value entered in the Scheduling Options config Select Warehouse on which to run task will be used when creating the task.

```json
{
    "targetTaskWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than “DEV ENVIRONMENT” the node will attempt to create the task using a Snowflake warehouse with the specified value.

For example, with the below setting for the parameter in a QA environment, the task will execute using a warehouse named compute_wh.

```json
{
    "targetTaskWarehouse": "compute_wh"
}
```

### Initial Deployment
When deployed for the first time into an environment the Stream and Insert or Merge node will execute four to six stages dependent on whether or not the task schedule relies on a predecessor task:

#### No Predecessor Task

**Create Stream**
This stage will execute a CREATE OR REPLACE statement and create a Stream in the target environment.

**Create Stage Table**
This stage will create a table that will be loaded by the task.

**Create Task**
This stage will create a task that will load the target table on the schedule specified.

**Resume Task**
After the task has been created it needs to be resume so that the task runs on the schedule.

#### Predecessor Task

**Create Stream**
This stage will execute a CREATE OR REPLACE statement and create a Stream in the target environment.

**Create Stage Table**
This stage will create a table that will be loaded by the ask.

**Suspend Root Task**
To add a task into a DAG of task the root task needs to be put into a suspended state.

**Create Task**
This stage will create a task that will load the target table on the schedule specified.

If a task is part of a DAG of tasks the DAG needs to include a node type called Task Dag Resume Root. This node will resume the root node once all the dependent tasks have been created as part of a deployment.

The task node has no ALTER capabilities. All task enabled nodes are CREATE OR REPLACE only though this is subject to change.

### Redeployment

After the Stream has deployed for the first time into a target environment, subsequent deployments with different deployment behavior will result in a CREATE stream statement being issued and a new stream will be created followed by CREATE TASK and RESUME TASK statements.

### Undeployment

If a Stream Node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the Stream in the target environment will be dropped.

This is executed in following stages:

* Drop Stream
* Drop Table
* Drop Current Task

# Code

## Work with Task
* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/WorkwithTask-151/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/WorkwithTask-151/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/WorkwithTask-151/run.sql.j2)

## Dimension with Task 
* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/DimensionwithTask-149/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/DimensionwithTask-149/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/DimensionwithTask-149/run.sql.j2)

## Fact with Task
* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/FactwithTask-150/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/FactwithTask-150/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/FactwithTask-150/run.sql.j2)

## Task DAG Create Root 
* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGCreateRoot-154/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGCreateRoot-154/create.sql.j2)

## Task DAG Resume Root
* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGResumeRoot-155/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/TaskDAGResumeRoot-155/create.sql.j2)

## Stream
* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/Stream-153/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/Stream-153/create.sql.j2)

## Stream and Insert or Merge
* [Node definition](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/StreamandInsertorMerge-152/definition.yml)
* [Create Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/StreamandInsertorMerge-152/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Streams-and-Task-Nodes/blob/main/nodeTypes/StreamandInsertorMerge-152/run.sql.j2)

[Macros](https://github.com/Coalesce-Software-Inc/coalesce_marketplace/blob/7ed5ad0830c6352f80046993a0664db8d980e7ac/Code-files/macros_basenodetypes.txt)


[<img src="https://github.com/coalesceio/Streams-and-Task-Nodes/assets/7216836/c1c65350-9f97-4307-8a75-75447dc1b252" alt="Git Logo" height="40">](https://github.com/coalesceio/Streams-and-Task-Nodes)
