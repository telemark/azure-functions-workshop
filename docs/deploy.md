# Deploy

For å kunne deploye til Azure må du ha gjort klart litt på forhånd.

- resource group
- app service plan
- storage account

## Sette opp function app

Forutsatt at funksjonen du skal deploye ligger på GitHub følger du disse trinnene i Azure

- Legg til function app
- Runtime stack -> Node
- Configuration for app (Application settings)

```
# Her legger du inn miljøvariabler funksjonen har bruk for
SERVICEBUS_CONNECTION=Endpoint=sb://<your-servicebus-namespace-connection-string>
ELASTIC_HOST=your-elastic-search-host
```

## Sette opp function

I function appen du har laget gjør du følgende

- add function
- Plattform features -> deployment center
- github
- branch master
