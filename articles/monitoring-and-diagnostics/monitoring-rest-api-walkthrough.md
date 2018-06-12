---
title: Azure návod monitorování REST API
description: Postup ověření požadavky a pomocí rozhraní REST API Azure monitorování načíst dostupné definice metrik a metriky hodnoty.
author: mcollier
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: mcollier
ms.component: ''
ms.openlocfilehash: d916191ec6b475f9a19a48c62d69e4c8990a2d4c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264421"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure návod monitorování REST API
V tomto článku se dozvíte, jak provést ověření, abyste mohli používat kódu [Microsoft referenční dokumentace rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

Rozhraní API služby Azure monitorování umožňuje načítání prostřednictvím kódu programu dostupná výchozí definice metrik, členitosti a metriky hodnoty. Data lze uložit v samostatných data store například Azure SQL Database, Azure Cosmos DB nebo Azure Data Lake. Odtud můžete provést další analýzu podle potřeby.

Kromě práce s různými metriky datové body, rozhraní API monitorování také umožňuje k zobrazení seznamu pravidla výstrah, zobrazení protokoly aktivity a mnoho dalšího. Úplný seznam dostupné operace, najdete v článku [Microsoft referenční dokumentace rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Ověřování Azure sledování požadavků
Prvním krokem je ověřit žádost.

Všechny úlohy prováděné vůči monitorování rozhraní API služby Azure pomocí Azure Resource Manager ověření modelu. Proto musí být ověřeny všechny požadavky s Azure Active Directory (Azure AD). Jeden ze způsobů ověření klientská aplikace je vytvořit objekt služby Azure AD a načtení tokenu ověřování (JWT). Následující ukázkový skript ukazuje vytvoření objektu zabezpečení pomocí prostředí PowerShell služby Azure AD. Pro podrobnější návod, naleznete v dokumentaci na [pomocí Azure PowerShell k vytvoření objektu služby pro přístup k prostředkům](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Je také možné [vytvořit objekt služby prostřednictvím portálu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Zpracovat dotaz rozhraní API Azure monitorování, měli klientské aplikace k ověření použít dříve vytvořenou instanční objekt. Skript prostředí PowerShell následující příklad ukazuje jeden přístupu, pomocí [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) získat ověřovací token JWT. JWT token je předán jako součást parametru HTTP autorizace v žádostech o monitorování REST API služby Azure.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $secureStringPassword)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Po ověření dotazy pak jde spustit pro monitorování REST API služby Azure. Existují dva užitečné dotazy:

1. Seznam definice metrik pro prostředek
2. Načíst metriky hodnoty

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Načtení definice metrik (vícerozměrných rozhraní API)

Použití [Azure monitorování metrika definice rozhraní API REST](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) pro přístup k seznamu metriky, které jsou k dispozici pro službu.

**Metoda**: získání

**Identifikátor URI požadavku je**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{prostředku resourcetype se stala}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Například načíst definice metrik pro účet služby Azure Storage, žádost by měly vypadat následovně:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> Chcete-li načíst definice metrik používání vícerozměrných Azure monitorování metriky rozhraní REST API, použijte "2018-01-01" jako verze rozhraní API.
>
>

Výsledný těla odpovědi JSON by se podobně jako v následujícím příkladu: (Všimněte si, že druhý metrika má dimenze)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Načte hodnoty dimenzí (vícerozměrných rozhraní API)
Jakmile se ví, že k dispozici definice metrik, může být některé metriky, které mají dimenzí. Před odesláním dotazu pro metriku můžete chtít zjistit, jaký rozsah hodnot je dimenze. Na základě tyto hodnoty dimenzí, které pak můžete filtrovat nebo segment metriky založené na hodnotách dimenze při dotazování metriky.  Použití [REST API služby Azure monitorování metriky](https://docs.microsoft.com/rest/api/monitor/metrics) dosáhnout.

Použijte název v metrice 'Hodnota' (ne ' localizedValue') pro všechny žádosti, filtrování. Pokud nejsou zadány žádné filtry, vrátí se výchozí metriku. Použití toto rozhraní API umožňuje pouze jednu dimenzi tak, aby měl filtr zástupných znaků.

> [!NOTE]
> Pro načtení hodnoty dimenze pomocí rozhraní REST API Azure monitorování, použijte "2018-01-01" jako verze rozhraní API.
>
>

**Metoda**: získání

**Identifikátor URI požadavku je**: https://management.azure.com/subscriptions/ *{id předplatného}*/resourceGroups/*{resource-group name}*/providers/*{-– obor názvů zprostředkovatele prostředků}* / *{typ prostředku}*/*{název prostředku}*/providers/microsoft.insights/metrics?metric=*{metrika}* & časový interval =*{starttime nebo endtime}*& $filter =*{filtru}*& resultType = metadata & verze api-version =*{apiVersion}*

Například pro načtení seznamu hodnot dimenzí, které byly vygenerované pro rozhraní API název dimenze pro metriku "Transakcí", kde dimenze GeoType = 'Primary' během zadaného časového rozsahu, požadavek bude následující:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
Výsledný těla odpovědi JSON by se podobně jako v následujícím příkladu:

```JSON
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]    
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Načtení metriky hodnot (vícerozměrných rozhraní API)
Jakmile je k dispozici definice metrik a dimenze možné hodnoty jsou známé, je pak možné načíst související hodnoty metriky.  Použití [REST API služby Azure monitorování metriky](https://docs.microsoft.com/rest/api/monitor/metrics) dosáhnout.

Použijte název v metrice 'Hodnota' (ne ' localizedValue') pro všechny žádosti, filtrování. Pokud nejsou zadány žádné filtry dimenzí, je vrácena souhrnná agregovaná metrika. Pokud metriky dotaz vrátí více časové řady, můžete použít parametry dotazu 'Top' a 'OrderBy' vrátit omezené seřazený seznam časové řady.

> [!NOTE]
> Pro načtení vícerozměrných metriky hodnoty pomocí rozhraní REST API Azure monitorování, použijte "2018-01-01" jako verze rozhraní API.
>
>

**Metoda**: získání

**Identifikátor URI požadavku je**: https://management.azure.com/subscriptions/ *{id předplatného}*/resourceGroups/*{resource-group name}*/providers/*{-– obor názvů zprostředkovatele prostředků}* / *{typ prostředku}*/*{název prostředku}*/providers/microsoft.insights/metrics?metric=*{metrika}* & časový interval =*{starttime nebo endtime}*& $filter =*{filtru}*& interval =*{časovými úseky}*& agregace =*{ aggreation}*& verze api-version =*{apiVersion}*

Například k načtení 3 hlavních rozhraní API, v sestupném hodnotu, podle počtu "Transakcí" během 5 minut rozsah, kde GeotType byl 'Primary', požadavek bude následující:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
Výsledný těla odpovědi JSON by se podobně jako v následujícím příkladu:

```JSON
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Načtení definice metrik
Použití [Azure monitorování metrika definice rozhraní API REST](https://msdn.microsoft.com/library/mt743621.aspx) pro přístup k seznamu metriky, které jsou k dispozici pro službu.

**Metoda**: získání

**Identifikátor URI požadavku je**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{prostředku resourcetype se stala}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Například k načtení definice metrik pro aplikace logiky Azure, žádost by měly vypadat následovně:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Pokud chcete načíst pomocí rozhraní REST API Azure monitorování definice metrik, použijte jako verze rozhraní API "2016-03-01".
>
>

Výsledný těla odpovědi JSON by se podobně jako v následujícím příkladu:
```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Další informace najdete v tématu [seznamu definice metrik pro prostředek v rozhraní REST API Azure monitorování](https://msdn.microsoft.com/library/azure/mt743621.aspx) dokumentaci.

## <a name="retrieve-metric-values"></a>Načtení metriky hodnot
Jakmile se ví, že k dispozici definice metrik, je pak možné načíst související hodnoty metriky. Použít název v metrice 'Hodnota' (ne ' localizedValue') pro všechny požadavky na filtrování (například získat metriky datových bodů 'CpuTime' a 'Požadavky'). Pokud nejsou zadány žádné filtry, vrátí se výchozí metriku.

> [!NOTE]
> Pro načtení metriky hodnoty pomocí rozhraní REST API Azure monitorování, použijte "2016-09-01" jako verze rozhraní API.
>
>

**Metoda**: získání

**Identifikátor URI požadavku je**: https://management.azure.com/subscriptions/ *{id předplatného}*/resourceGroups/*{resource-group name}*/providers/*{-– obor názvů zprostředkovatele prostředků}* / *{typ prostředku}*/*{název prostředku}*/providers/microsoft.insights/metrics?$filter=*{filtru}*& verze api-version =*{apiVersion}*

Například pokud chcete načíst RunsSucceeded metriky datových bodů pro dané časové rozmezí a časovým intervalem 1 hodina, žádost vypadat takto:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

Výsledný těla odpovědi JSON by se podobně jako v následujícím příkladu:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Načtení více bodů data nebo agregace, přidejte metriky definice názvy a typy agregace filtru, jak je vidět v následujícím příkladu:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
Výsledný těla odpovědi JSON by se podobně jako v následujícím příkladu:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Použití ARMClient
Další možnost je použít [ARMClient](https://github.com/projectkudu/armclient) na počítač se systémem Windows. Automaticky ARMClient zpracovává ověřování Azure AD (a výsledný token JWT). Následující kroky popisují použití ARMClient pro načítání metriky dat:

1. Nainstalujte [Chocolatey](https://chocolatey.org/) a [ARMClient](https://github.com/projectkudu/armclient).
2. Okno terminálu, zadejte *armclient.exe přihlášení*. Díky tomu budete vyzváni k přihlášení k Azure.
3. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Chcete-li načíst definice metrik pro konkrétní aplikaci logiky, vydejte následující příkaz:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Načtení ID prostředku
Pomocí rozhraní REST API skutečně pomáhá pochopit dostupné definice metrik, členitosti a souvisejících hodnot. Informace jsou užitečné při použití [Knihovna správy Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

ID prostředku, který má používat pro předchozí kód je úplná cesta k požadované prostředků Azure. Například k dotazování proti webové aplikace Azure, bude ID prostředku:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Následující seznam obsahuje několik příkladů formáty ID prostředku pro různé prostředky Azure:

* **IoT Hub** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastický fond SQL** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Sql/servers/*{fondu db}*/elasticpools/*{sql název fondu}*
* **Databáze SQL (v12)** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Sql/servers/*{název serveru}*/databases/*{název databáze}*
* **Service Bus** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.ServiceBus/*{obor názvů}* / *{servicebus-name}*
* **Sady škálování virtuálního počítače** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{název virtuálního počítače}*
* **Virtuální počítače** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Compute/virtualMachines/*{název virtuálního počítače}*
* **Služba Event Hubs** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.EventHub/namespaces/*{ Centrum eventhub-namespace}*

Načítání ID prostředku, včetně použití Průzkumníka prostředků Azure, zobrazení požadovaný prostředek na portálu Azure a pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure alternativní způsoby.

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure
K vyhledání ID prostředku pro požadovaný prostředek, je užitečné jeden ze způsobů použití [Průzkumníka prostředků Azure](https://resources.azure.com) nástroj. Přejděte na požadovaný prostředek a podívejte se na zobrazené ID, stejně jako na následujícím snímku obrazovky:

![ALT "Průzkumníka prostředků Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal
ID prostředku můžete získat taky z portálu Azure. Uděláte to tak, přejděte na požadovaný prostředek a potom vyberte možnost Vlastnosti. ID prostředku se zobrazí v části vlastnosti, jak je vidět na následujícím snímku obrazovky:

![ALT "ID prostředku zobrazí v okně vlastností na portálu Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
ID prostředku můžete načíst pomocí rutin prostředí Azure PowerShell také. Například pokud chcete získat ID prostředku pro aplikace logiky Azure, spusťte rutinu Get-AzureLogicApp jako v následujícím příkladu:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Výsledek by měl vypadat podobně jako v následujícím příkladu:
```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```


### <a name="azure-cli"></a>Azure CLI
Načtení ID prostředku pro účet služby Azure Storage pomocí rozhraní příkazového řádku Azure, proveďte příkaz "Zobrazit účet úložiště az, jak je znázorněno v následujícím příkladu:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Výsledek by měl vypadat podobně jako v následujícím příkladu:
```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure Logic Apps nejsou zatím k dispozici prostřednictvím rozhraní příkazového řádku Azure, proto účtu Azure Storage se zobrazí v předchozím příkladu.
>
>

## <a name="retrieve-activity-log-data"></a>Načíst data protokolu aktivit
Kromě definice metrik a souvisejících hodnot je také možné použít rozhraní API REST monitorování Azure pro načtení další zajímavé přehledy související s prostředky Azure. Jako příklad, je možné dotazu [protokol aktivit](https://msdn.microsoft.com/library/azure/dn931934.aspx) data. Následující příklad ukazuje, pomocí REST API pro monitorování Azure pro data protokolu aktivit dotazu v určité datum rozsahu pro předplatné Azure:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Další postup
* Zkontrolujte [Přehled monitorování](monitoring-overview.md).
* Zobrazení [podporované metriky s Azure monitorování](monitoring-supported-metrics.md).
* Zkontrolujte [Microsoft Azure monitorovat referenční dokumentace rozhraní API REST](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Zkontrolujte [knihovna Azure správy](https://msdn.microsoft.com/library/azure/mt417623.aspx).
