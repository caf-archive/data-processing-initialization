# Data Processing Initialization

## Summary
The Data Processing Initialization project contains a library that can be used to initialize base data for the Workflow and Boilerplate databases.

## Workflow Format

The file is expected to define a single workflow, including all its processing rules, conditions and actions. Objects and their properties are supported as defined in the Data Processing API contract, [here](https://cafdataprocessing.github.io/data-processing-service/pages/en-us/Data_Processing/API), differing where noted below.

### Workflow Details

The top level properties in the JSON describe details about the workflow.

```
{
  "name": "My Workflow",
  "description": "This is the a workflow that will be created.",
  "notes": "Additional information about workflow."
  "processingRules": []
}
```

The processingRules property on the workflow should contain all processing rules that are to be added under the workflow.

### Processing Rules

In the processingRules property of the workflow, processing rules can be defined with the below information;

```
{
  "name": "Metadata Processing",
  "enabled": true,
  "description": "Extracts document metadata.",
  "priority": 100,	
  "actions": [],
  "ruleConditions": []
}
```

The actions property should be used to define actions that for the rule and the ruleConditions property should contain conditions that should be applied to all actions.

### Actions

In the actions property of a processing rule, actions can be defined with the below information;

```
{
  "name": "Action Name",
  "description": "This describes the action.",
  "order": 100,
  "settings": {
  },
  "typeName": "ActionTypeName",
  "actionConditions": []
}
```

The actionConditions property should contain conditions that apply to only this action.

The settings property for the action should contain the appropriate properties to set based on the definition of the action type.

e.g. For an example action type 'AddFieldType' that expects properties 'fieldName' and 'fieldValue', the action definition would be;

```
{
  "name": "Add Field",
  "description": "Adds a field.",
  "order": 100,
  "settings": { 
    "fieldName": "MyField",
    "fieldValue": "MyValue"
  },
  "typeName": "AddFieldType",
  "actionConditions": []
}
```

#### Type Id and Type Name

In the data processing API contract an action has a typeId property that specifies the ID of the action type to use. As it may be difficult to know this information at the time of the JSON definition the typeName property can be used to specify the internal_name of an action type which the library will resolve to the appropriate ID before creating the action. If the ID is known beforehand then the typeId property can be used instead of typeName (typeId will take precedence over typeName).

```
{
  ...
  "typeId": 5
  ...
}
```

#### Boilerplate Actions

Special handling is supported for boilerplate type actions, which require specifying the IDs of expressions/tags in the action definition. Instead the name of the expression/tag can be written instead and the library will resolve the name to the appropriate ID before creating the action.

```
{
  "name": "Check expression",
  "description": "Checks document against defined expression.",
  "order": 100,
  "settings": { 
    "expressionIds": ["MyExpression"]
  },
  "typeName": "BoilerplatePolicyType",
  "actionConditions": []
}
```

```
{
  "name": "Check tag",
  "description": "Checks document against defined tag.",
  "order": 100,
  "settings": { 
    "tagId": "MyTag"
  },
  "typeName": "BoilerplatePolicyType",
  "actionConditions": []
}
```

If the expression/tag ID is known then it can be used as the value instead.

### Rule and Action Conditions

In the ruleConditions and actionConditions properties, conditions can be defined as detailed in the data processing API contract. Boolean, date, exists, not, number, regex and string conditions are supported.

e.g.

```
{
  "name": "Image",
  "additional": {
    "type": "boolean",
    "operator": "or",
    "children": [{
      "name": "Raster Image",
      "additional": {
        "type": "string",
        "notes": "DOC_CLASS_CODE is 4",
        "operator": "is",
        "field": "DOC_CLASS_CODE",
        "value": "4"
      }
    },
    {
      "name": "fax",
      "additional": {
        "type": "string",
        "operator": "is",
        "field": "DOC_CLASS_CODE",
        "notes": "DOC_CLASS_CODE is 4:fax",
        "value": "19"
      }
    },
    {
      "name": "pdf",
      "additional": {
        "type": "string",
        "value": "230",
        "operator": "is",
        "field": "DOC_FORMAT_CODE",
        "notes": "DOC_FORMAT_CODE is 230"
      }
    },
    {
      "name": "Vector Graphic",
      "additional": {
        "type": "string",
        "value": "5",
        "operator": "is",
        "field": "DOC_CLASS_CODE",
        "notes": "DOC_CLASS_CODE is 5"
      }
    }]
  }
}
```

### Workflow Example

A full workflow example in this format can be seen [here](./example_files/processing_workflow.json).

## Boilerplate Format

The expressions and tags should be defined in JSON format in a file the library can access. The creation of the expressions and tags is achieved through the [util-boilerplate-creation](https://github.com/CAFDataProcessing/boilerplate-service/tree/develop/util-boilerplate-creation) library.

### Project ID

The project ID to create the expressions and tags under should be provided as a top level node.

```
{
  ...
  "projectId": "Default",
  ...
```

This should match the project ID that messages are to be submitted with.

### Expressions

The expressions to create should be provided as a top level node. Each expression should define properties as shown below;

```
{
"expressions": [{
    "tempId": 1,
    "name": "HP old",
    "description": "HP old",
    "expression": "HP Ltd"
  }]
}
```

The tempId property allows the expression to be used in tags defined in this file. During creation the correct ID will be used once the expression is created.

### Tags

The tags to create should be provided as a top level node. Each tag should define properties as shown below;

```
{
  "tags": [{
    "name": "Disclaimers",
    "description": "Disclaimers Tag",
    "defaultReplacementText": "<redacted disclaimer>",
    "boilerplateExpressions": [2,3,4,9]
  }]
}
```

The boilerplateExpressions property on the tag should use the tempId values of expressions in the file.

### Boilerplate Example

A full example in this format can be seen [here](./example_files/boilerplate_input.json).