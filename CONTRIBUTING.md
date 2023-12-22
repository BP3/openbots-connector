# Camunda OpenBots Custom Connector Template
This project contains a custom Camunda 8 Connector template that provides access to OpenBots operations. The operations and functionality have to be encoded into the template to provide the integration.

This readme outlines how to update the template to provide the operation functionality required.

# How does the connector work?
The Connector is based on the `io.camunda:http-json` connector used by various OOTB connectors such as the REST API Connector. 

# How to add a new operation to the connector template?
The following steps outline how to add a new operation to the Connector.

## Adding the operation option
Create an item into the queue using `addQueueItem` and then be able to check the status of that item in the queue ising the`getItemById`:

```json
{
  "label": "OpenBots Operation type",
  "description": "The operation in OpenBots to invoke",
  "id": "operationType",
  "group": "operation",
  "type": "Dropdown",
  "choices": [
    {
      "name": "Login",
      "value": "login"
    },
    {
      "name": "Add queue item",
      "value": "addQueueItem"
    },
    {
      "name": "Get queue item result by ID",
      "value": "getItemById"
    }
  ],
  "binding": {
    "type": "zeebe:input",
    "name": "operationType"
  }
}
```

## Adding operation to HTTP method
Depending on the HTTP method the operation uses, add it to the `condition.oneOf` properties, for example:

```json
{
  "description": "This sets the HTTP method to POST based on the OpenBots operation that is set",
  "id": "methodPost",
  "group": "configuration",
  "type": "Hidden",
  "value": "post",
  "binding": {
    "type": "zeebe:input",
    "name": "method"
  },
  "condition": {
    "property": "operationType",
    "oneOf": [
      "addQueueItem"
    ]
  }
}
```
A GET method:
```json
{
  "description": "This sets the HTTP method to GET based on the OpenBots operation that is set",
  "id": "methodGet",
  "group": "configuration",
  "type": "Hidden",
  "value": "get",
  "binding": {
    "type": "zeebe:input",
    "name": "method"
  },
  "condition": {
    "property": "operationType",
    "oneOf": [
      "getItemById"
    ]
  }
}
```

## Add end point URL generation
Each operation will require an end point URL, which might be static, or dynamic based on the paramater values passed in. The example below shows the parameters plugged into the URL (this is done at runtime and not at design time). Note how the start of the `value` property has `=\"` (and ends with a `"`), which allows the URL to be dynamically created by concatenating variables and strings as an expression.

Some operations may have a fixed URL, in which case that can just be enclosed inside outer quotes like any other string JSON property.

Finally, make sure that the correct operation name is included in the `condition` so only this URL is passed to the connector when the operation is selected.

```json
{
  "description": "This is for adding an item to the queue.",
  "type": "Hidden",
  "value": "=\"https:\"+\"//\"+cloudUrl+\"/queueitems/enqueue\"",
  "binding": {
    "type": "zeebe:input",
    "name": "url"
  },
  "condition": {
    "property": "operationType",
    "oneOf": [
      "addQueueItem"
    ]
  }
}
```


# Fork the repository
To contribute to the project you should fork the repository and raise a pull request.

## Raise a pull request
In the description of your pull request please supply the following details, with screen shots where possible:

* Provide a desription of the change you have made
* Submit results of testing you have done as part of the change
