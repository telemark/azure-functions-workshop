# Basics

## Triggere

Det som får en function til å starte.

En function må ha nøyaktig 1 trigger.

- triggere
  - timer
    - [azure-function-index-politicians](https://github.com/telemark/azure-function-index-politicians)
    - [functions-create-scheduled-function](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-scheduled-function)
  - queue
    - [azure-function-sendmail-from-queue](https://github.com/telemark/azure-function-sendmail-from-queue)
    - [functions-create-storage-queue-triggered-function](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-storage-queue-triggered-function)
  - Azure Cosmos DB
    - [functions-create-cosmos-db-triggered-function](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-cosmos-db-triggered-function)
  - Blob storage
    - [functions-create-storage-blob-triggered-function](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-storage-blob-triggered-function)

## Config function

- function.json
  - bindings
    - [timer in / queue out](https://github.com/telemark/azure-functions-index-employees/blob/master/TimerTrigger/function.json)
    - [queue in](https://github.com/telemark/azure-function-search-index/blob/master/QueueHandler/function.json)

## Config host

- host.json
  - [docs](https://docs.microsoft.com/en-us/azure/azure-functions/functions-host-json)

# Lokal utvikling

- local.settings.json
  - AzureWebJobsStorage
    - portal -> Storage accounts -> storage account -> Settings / Access keys -> key1
  - SERVICEBUS_CONNECTION
    - portal -> All resources -> Service Bus Namespace -> Settings / Shared access policies -> RootManagedSharedAccessKey -> Primary connection string
- installere dependencies for extensions
  - `$ func extensions install`
- starte function lokalt