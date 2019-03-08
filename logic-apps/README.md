# Logic Apps

> "Azure logic apps simplifies how you build automated scalable workflows that integrate apps and data across cloud services and on-premises systems."  
\- [Microsoft Azure logic apps][1]


## Content
- [Creating a logic app](#Creating-a-Logic-App)
- [Connectors & Triggers](#Connectors-&-Triggers)
    - [Connectors](#Connectors)
    - [Triggers](#Triggers)
- [Calling function apps](#Calling-function-apps)
    - [Service Bus content](#Service-Bus-content)
    - [OpenAPI](#OpenAPI)
- [License](#License)


## Creating a logic app
1. Navigate to [portal.azure.com][2]
2. Click ["Create a resource"][3]
3. Search for or find ["logic app"][4]
4. Fill in the information and click "Create"
5. Wait for it to deploy then click "Go to resource"
6. Click on "Blank logic app"


## Connectors & Triggers
### Connectors
![Blank logic app](./res/blank-logicapp.png)

A connector can contain authentication and other information for a service. This makes it easier to talk to different services in the logic app.

1. Search for and click on the wanted connector
    * If you already have a connector for this service, you can use that.
    * If not it may ask for some information depending on the service.


### Triggers
![Service Bus Triggers](./res/service-bus-triggers.png)

Connectors contains triggers will execute the logic app and usually provide some data to process.

1. After the connector is created, you get a list of triggers.
    * For example under [Service Bus][5], you can execute the logic app if there is a new message in the queue.\
    ["When a message is received in a queue"][6]
2. Select one and fill in the required information
3. You can now refrence the data returned from this trigger in the logic app.


## Calling function apps
![](./res/add-content-to-body-fa.png)

You can call [function apps](../function-apps/README.md) in the logic app by adding them as a step. You can then specify the body of the request to the function app.


### Service Bus content
![](./res/la-fa-expression.png)

The content of the service bus is base64 encoded json. You can either decode it in the function app, or straight in the logic app using "Expression".
```js
//  - Convert string to JSON
//  |
//  |        - Convert base64 to string
//  |        |
//  |        |                - Body of trigger
//  |        |                |
//  |        |                |             - Get ContentData
//  |        |                |             |
  json( base64ToString( triggerBody()['ContentData'] ) )
```

### OpenAPI
![](./res/la-fa-openapi.png)

OpenAPI (Swagger) can be used make the function apps more modular. By having a `OpenAPI.yml` file in the trigger´s directory, logic app can use this file to generate input fields in that step.
Then other function´s outputs (that is also using a OpenAPI scheme) can be inserted into.

Check out [Azure´s websites][7] on how to generate an OpenAPI definition.

Example scheme: [OpenAPI.yml](./examples/OpenAPI.yml)

Note: Currently the definition generation is only available to function apps using runtime version 1.\
Ref: [Azure/azure-function-host#2874](https://github.com/Azure/azure-functions-host/issues/2874)


```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | | - OpenAPI.yml <---------
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | | - OpenAPI.yml <---------
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
 | - bin
```
\- Folder structure: [Azure docs][8] (modified)

#### Adding a function app with a scheme
1. Add a new step in the logic app
2. Select "Azure Functions"
3. Select your function app
4. Select the tab "Swagger Actions"
5. Now select the scheme
6. Now if your scheme is correctly formatted it should show the fields you have defined

#### CORS (Todo)
[Check out LA IP-ranges](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-limits-and-config#firewall-configuration-ip-addresses)

## Built-in tools
Logic apps has a few built-in control functions, all of these integrate very well with OpenAPI enabled function apps (and other built-in functions).
- Conditions
- For-each
- Scope
- Switch
- Terminate
- Until

### Scopes
Scopes are very useful for error-handling, as it returns an error if any of the steps inside returns an error or timeout. But the logic app continues to run, this then be used to create a try-catch of sorts.
#### Get status
![Status expression](./res/scope-status-expression.png)

To get the status of a scope we can use an "Expression" in the logic app.\
`result('Scope')[0]['status']`\
This expression returns the status of the first scope it finds with the name 'Scope'.

The status can be either of these values:
`['Succeeded', 'Failed', 'Aborted']`

You can then either use one of the built-in tools to decide what happens, or use the "Configure Run-after".
#### Run-after
![Run-after window](./res/run-after.png)

Run-after decides if the next step should be run, based off the previous step's status code. This can be very useful for error-handling.

Run-after can be configured in the context menu of the step. This menu can be found at the three dots in the upper-left corner of the step.
Here you can choose when it should run.
## Service bus as trigger
!dis[Service-bus triggers](./res/servicebus-triggers.png)

The service-bus has several triggers for the logic app. They are pretty self-explanatory.

But auto-complete and peek-lock is repeated in all of them.
### Auto-complete vs peek-lock
#### Auto-complete
- Marks the message as complete as soon as it's read.
- Can be read throughout the logic app.
- Impossible for multi-execution of the same message.
#### Peek-lock
- Locks the message as soon as it's read.
- Can be read throughout the logic app.
- It's possible to execute the same message unlimited times... (Disable the app at it's dashboard if this happens)
- Locked for a pre-defined time (default: 60s), other apps will not execute it.
---
**Warning:** \
Microsoft did a recent change in how instances work.
Instead of spinning up the required instances on the spot up to the configured max limit.

It will spin up one for every message in the queue (regardless of max limit). Then only execute up to the limit, the rest are set to a waiting state.

This can cause unlimited asynchronous loops if you use peek-locks.

(Do a little brainstorming on how this can occour.\
Material: [Stackoverflow.com](https://stackoverflow.com/questions/51633843))

---
### Parse JSON
Service-bus queue parsing (todo)


## TODO
- [ ] Make a table of use-cases and how all service-bus triggers work.


# License

[MIT](../LICENSE)




[1]: https://docs.microsoft.com/en-us/azure/logic-apps/
[2]: https://portal.azure.com
[3]: https://portal.azure.com/#create/hub
[4]: https://portal.azure.com/#create/Microsoft.EmptyWorkflow
[5]: https://docs.microsoft.com/en-us/azure/connectors/connectors-create-api-servicebus
[6]: https://docs.microsoft.com/en-us/azure/connectors/connectors-create-api-servicebus#add-trigger-or-action
[7]: https://docs.microsoft.com/en-us/azure/azure-functions/functions-openapi-definition#generate-the-openapi-definition
[8]: https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#folder-structure
