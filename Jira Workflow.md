# Jira Workflow

## Option Workflow:

* Dev Open Jira.

* Check Next Jira Board Column.

* Pickup first Option.

* Move Option to Development Jira Board Column.

* Check if there's a git branch for that Option.
  * If there's no branch for that Option, create an Option branch from develop.

  
## Subtask Workflow:

* Dev Open Jira.

* Check In Development Jira Board Column.

* Find the Option he/she is interested in.

* Open the Option.

* Assign yourself to the subtask that you want to implement.

* Move the subtask Jira status to In Progress

* Create a subtask branch from the Option branch

* Implement the functionality required.

* When implementation is completed:
  * Update the subtask branch locally with the remote Option branch. 
  * Update the subtask branch locally with remote develop branch.

* Create Pull request from subtask branch to Option branch.

* When Pull request approved, merge Pull request in remote Option branch.

* Update Subtask Jira Status to Done.

* Delete Subtask Branch.

* If all the subtask of the Option has been completed move it to Ready for testing.

### Conventions & Rules:

#### Option branch name should:

1. Start with **feature/** prefix.
2. Jira ID of the Option.
3. After Jira ID add a **-** .
4. Provide a clear and small name identifying the Option.
5. space in the name should be replaced by **-** .

#### Example.

Option ID: MOB-123 Option Name: Authenticate User with Phone Number Branch Name:
feature/MOB-123-authentication-by-phone-number

#### Subtask branch should:

1. Start with the Jira ID of the subtask
2. After Jira ID add a **-** .
3. Provide a clear and small name identifying the subtask.
4. space in the name should be replaced by **-** .

#### Example

Subtask ID: MOB-321 Subtask Name: Implement remote data source for backend authentication API Branch Name:
MOB-321-data-source-for-authentication-api

### Conflict Resolution

* When your subtask is dependent on another subtask to be merged and that subtask already has a Pull request:
    1. Create your subtask branch from the dependent subtask branch.
    2. Follow Jira workflow for subtask until the Pull request creation stage.
    3. When the Pull request for that subtask has been created add the label **blocked** in the pull request.
    4. And add a comment in the Pull request that link the dependent subtask pull request.
    5. Then continue following the Jira Workflow for subtask.

* When you understand that multiple subtasks can be implemented in a single branch:
    1. Create subtask that specify that it's implement the dependent subtasks. Example: Implement subtasks MOB-1, MOB-2
    2. Create subtask branch for that new subtask.
    3. And follow the Jira workflow and also move the dependent subtasks to In Progress.
    4. When subtask is done, all the dependent subtask should also be moved to done.

