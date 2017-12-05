#### Version Number
${version-number}

#### New Features

#### Bug Fixes

#### Breaking Change
- Classification API and Worker have been removed from this project  
   The `WorkflowInitializer` method no longer accepts the `ClassificationWorkflowNameResolver` parameter. Any use of this method will need to be updated to remove the `ClassificationWorkflowNameResolver` parameter.
