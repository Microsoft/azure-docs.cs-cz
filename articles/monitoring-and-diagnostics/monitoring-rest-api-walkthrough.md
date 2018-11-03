---
title: Azure návod monitorování rozhraní REST API
description: Postup ověření požadavků a REST API služby Azure Monitor umožňuje načíst dostupné definice metrik a hodnoty metrik.
author: mcollier
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: mcollier
ms.component: ''
ms.openlocfilehash: 1d04c4335fcb2d7264d91e0b147c43828b3cff2e
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958742"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure návod monitorování rozhraní REST API
Tento článek popisuje, jak provádět ověřování, takže váš kód může použít [Reference k REST API Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

Rozhraní API služby Azure Monitor umožňuje programově načíst definice metrik dostupné výchozí, členitosti a hodnoty metrik. Data můžete uložit do samostatného úložiště dat. jako je například Azure SQL Database a Azure Cosmos DB, Azure Data Lake. Odtud můžete provést další analýzy podle potřeby.

Kromě práce s různými metriky datové body, monitorování rozhraní API také umožňuje seznamu pravidel upozornění, zobrazení protokolů aktivit a spoustu dalších věcí. Úplný seznam dostupných operací, najdete v článku [Reference k REST API Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Požadavky na ověřování Azure Monitor
Prvním krokem je ověřit žádost.

Všechny úkoly pro rozhraní API služby Azure Monitor použít model ověřování Azure Resource Manageru. Proto všechny požadavky musí být ověřené na Azure Active Directory (Azure AD). Jedním z přístupů k ověřování klientskou aplikaci je k vytvoření instančního objektu služby Azure AD a získat token ověřování (JWT). Následující ukázkový skript ukazuje vytvoření služby Azure AD instančního objektu pomocí Powershellu. Podrobnější návod, jak, naleznete v dokumentaci na [pomocí prostředí Azure PowerShell k vytvoření instančního objektu pro přístup k prostředkům](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Je také možné [vytvoření instančního objektu pomocí webu Azure portal](../active-directory/develop/howto-create-service-principal-portal.md).

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

Dotaz rozhraní API služby Azure Monitor, klientská aplikace k ověření používala dříve vytvořený instanční objekt. Skript prostředí PowerShell následující příklad ukazuje jednu přístup, pomocí [Active Directory Authentication Library](../active-directory/develop/active-directory-authentication-libraries.md) (ADAL) k získání ověřovacího tokenu JWT. JWT token je předán jako součást parametrem HTTP autorizace v požadavku REST API služby Azure Monitor.

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

Po ověření, dotazy pak jde provést proti REST API služby Azure Monitor. Existují dva užitečné dotazy:

1. Seznam definice metriky pro prostředek
2. Načíst hodnoty metrik

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Načíst definice metrik (multidimenzionální rozhraní API)

Použití [definice metrik Azure monitoru rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) pro přístup k seznamu metriky, které jsou k dispozici pro službu.

**Metoda**: získání

**Identifikátor URI žádosti**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/poskytovatelé/*{resourceProviderNamespace}* / *{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Například pokud chcete načíst definice metrik pro účet služby Azure Storage, žádost by vypadat takto:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> Načíst definice metrik použití vícedimenzionálních metrik Azure monitoru rozhraní REST API, použijte "2018-01-01" jako verze rozhraní API.
>
>

Výsledný těla odpovědi JSON by měl vypadat přibližně v následujícím příkladu: (Všimněte si, že druhý metrika má rozměrů)

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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Načtení hodnot dimenzí (multidimenzionální rozhraní API)
Jakmile je k dispozici definice metriky jsou známé, mohou být některé metriky, které mají dimenze. Dimenze má před dotazování pro metriku, možná budete chtít zjistit rozsah hodnot. Na základě těchto hodnot dimenzí, které potom můžete filtrovat nebo segment metriky založené na hodnotách dimenze při dotazování na metriky.  Použití [rozhraní REST API služby Azure Monitor Metrics](https://docs.microsoft.com/rest/api/monitor/metrics) toho dosáhnout.

Použijte název tuto metriku 'value' (ne "localizedValue") pro všechny požadavky na filtrování. Pokud nejsou zadány žádné filtry, vrátí se výchozí metriku. Použití tohoto rozhraní API umožňuje pouze jednu dimenzi mít filtr zástupných znaků.

> [!NOTE]
> Pro načtení hodnoty dimenze pomocí REST API služby Azure Monitor, použijte "2018-01-01" jako verze rozhraní API.
>
>

**Metoda**: získání

**Identifikátor URI žádosti**: https://management.azure.com/subscriptions/ *{id předplatného}*/resourceGroups/*{resource-group name}*/poskytovatelé/*{-– obor názvů zprostředkovatele prostředků}* / *{typ prostředku}*/*{název prostředku}*/providers/microsoft.insights/metrics?metricnames=*{metrika}*& časový rozsah =*{starttime a endtime}*& $filter =*{filter}*& hodnotu resultType = & metadat rozhraní api-version =*{apiVersion}*

Například chcete-li načíst seznam hodnot dimenzí, které byly vygenerován pro název rozhraní API dimenzi metriky "Transakcí", kde dimenze GeoType = 'Primární' během zadaného časového rozsahu, žádost by měl vypadat takto:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
Výsledný text JSON odpovědi bude podobně jako v následujícím příkladu:

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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Načíst hodnoty metrik (multidimenzionální rozhraní API)
Jakmile je k dispozici definice metrik a dimenze možné hodnoty jsou známé, je pak možné načíst související hodnoty metrik.  Použití [rozhraní REST API služby Azure Monitor Metrics](https://docs.microsoft.com/rest/api/monitor/metrics) toho dosáhnout.

Použijte název tuto metriku 'value' (ne "localizedValue") pro všechny požadavky na filtrování. Pokud nejsou zadány žádné filtry dimenzí, je vrácena zahrnutého agregovaná metrika. Pokud dotaz metriky vrátí více časové řady, můžete použít parametry dotazu "Hlavní" a "OrderBy" vrátit omezené uspořádaný seznam časové řady.

> [!NOTE]
> Pro načtení vícerozměrné metriky hodnot pomocí REST API služby Azure Monitor, použijte "2018-01-01" jako verze rozhraní API.
>
>

**Metoda**: získání

**Identifikátor URI žádosti**: https://management.azure.com/subscriptions/ *{id předplatného}*/resourceGroups/*{resource-group name}*/poskytovatelé/*{-– obor názvů zprostředkovatele prostředků}* / *{typ prostředku}*/*{název prostředku}*/providers/microsoft.insights/metrics?metricnames=*{metrika}*& časový rozsah =*{starttime a endtime}*& $filter =*{filter}*& interval =*{timeGrain}*& agregace =*{ aggreation}*& verzi api-version =*{apiVersion}*

Například načíst první 3 rozhraní API, sestupně podle hodnoty podle počtu "Transakcí" během 5 minut rozsah, kterých se spustil GeotType 'Primární', žádost by měl vypadat takto:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
Výsledný text JSON odpovědi bude podobně jako v následujícím příkladu:

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

## <a name="retrieve-metric-definitions"></a>Načíst definice metrik
Použití [definice metrik Azure monitoru rozhraní REST API](https://msdn.microsoft.com/library/mt743621.aspx) pro přístup k seznamu metriky, které jsou k dispozici pro službu.

**Metoda**: získání

**Identifikátor URI žádosti**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/poskytovatelé/*{resourceProviderNamespace}* / *{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Například pokud chcete načíst definice metrik pro aplikaci logiky Azure, žádost by vypadat takto:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Načíst definice metrik pomocí REST API služby Azure Monitor, použijte "2016-03-01" jako verze rozhraní API.
>
>

Výsledný text JSON odpovědi bude podobně jako v následujícím příkladu:
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

Další informace najdete v tématu [seznamu definice metriky pro prostředek v rozhraní REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) dokumentaci.

## <a name="retrieve-metric-values"></a>Načíst hodnoty metrik
Jakmile je k dispozici definice metriky jsou známé, je pak možné načíst související hodnoty metrik. Použijte název tuto metriku 'value' (ne "localizedValue") pro všechny požadavky na filtrování (například načíst metriky datových bodů "CpuTime" a "Požadavky"). Pokud nejsou zadány žádné filtry, vrátí se výchozí metriku.

> [!NOTE]
> Pro načtení hodnoty metrik pomocí REST API služby Azure Monitor, použijte "2016-09-01" jako verze rozhraní API.
>
>

**Metoda**: získání

**Identifikátor URI žádosti**: https://management.azure.com/subscriptions/ *{id předplatného}*/resourceGroups/*{resource-group name}*/poskytovatelé/*{-– obor názvů zprostředkovatele prostředků}* / *{typ prostředku}*/*{název prostředku}*/providers/microsoft.insights/metrics?$filter=*{filter}*& verzi api-version =*{apiVersion}*

Například pokud chcete načíst data metriky body RunsSucceeded pro dané časové rozmezí a pro časový interval 1 hodina, žádost by měl vypadat takto:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

Výsledný text JSON odpovědi bude podobně jako v následujícím příkladu:

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

K načtení více bodů dat nebo agregace, přidáte definice metriky názvy a typy agregace do filtru, jak je znázorněno v následujícím příkladu:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
Výsledný text JSON odpovědi bude podobně jako v následujícím příkladu:

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
Další možností je použít [ARMClient](https://github.com/projectkudu/armclient) na svém počítači s Windows. ARMClient automaticky zpracovává ověřování Azure AD (a výsledný token JWT). Následující kroky popisují použití ARMClient pro načítání dat metriky:

1. Nainstalujte [Chocolatey](https://chocolatey.org/) a [ARMClient](https://github.com/projectkudu/armclient).
2. V okně terminálu zadejte *armclient.exe přihlášení*. To vás vyzve k přihlášení k Azure.
3. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Aby bylo možné načíst definice metrik pro konkrétní aplikaci logiky, vydejte následující příkaz:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Načtení ID prostředku
Pomocí rozhraní REST API můžete opravdu pomáhá pochopit dostupné definice metrik, členitosti a souvisejících hodnot. Informace jsou užitečné při použití [knihovny správy Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Identifikátor prostředku použít předchozí kód, je úplná cesta k požadované prostředků Azure. Například pokud chcete zadat dotaz na webovou aplikaci Azure, bude ID prostředku:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Následující seznam obsahuje několik příkladů z formátů ID prostředků pro různé prostředky Azure:

* **IoT Hub** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastický fond SQL** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Sql/servers/*{fondu db}*/elasticpools/*{sql název fondu}*
* **SQL Database (v12)** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Sql/servers/*{název serveru}*/databases/*{název databáze}*
* **Service Bus** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.ServiceBus/*{namespace}* / *{název služby Service Bus}*
* **Škálovací sady virtuálních počítačů** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{název_virtuálního_počítače}*
* **Virtuální počítače** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.Compute/virtualMachines/*{název_virtuálního_počítače}*
* **Event Hubs** -/subscriptions/*{id předplatného}*/resourceGroups/*{resource-group name}*/providers/Microsoft.EventHub/namespaces/*{ oboru názvů eventhubu}*

Existuje alternativní přístupy k načítání ID prostředku, včetně použití Azure Resource Exploreru, zobrazení požadovaných prostředků na webu Azure Portal a pomocí Powershellu nebo rozhraní příkazového řádku Azure.

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure
Najít ID prostředku pro požadovaný prostředek, je jedním z přístupů užitečné použít [Azure Resource Exploreru](https://resources.azure.com) nástroj. Přejděte na požadovaný prostředek a podívejte se na ID je vidět, stejně jako v následujícím snímku obrazovky:

![ALT "Průzkumník prostředků Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>portál Azure
ID prostředku lze také získat z webu Azure portal. Uděláte to tak, přejděte na požadovaný prostředek a zvolte možnost Vlastnosti. ID prostředku se zobrazí v části vlastnosti, jak je znázorněno na následujícím snímku obrazovky:

![ALT "ID prostředku zobrazí v okně Vlastnosti na webu Azure Portal"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
ID prostředku se dá načíst pomocí rutin Powershellu pro Azure i. Třeba získat ID prostředku pro aplikaci logiky Azure, spusťte rutinu Get-AzureLogicApp, jako v následujícím příkladu:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Výsledek by měl vypadat přibližně jako v následujícím příkladu:
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
Načíst ID prostředku pro účet služby Azure Storage pomocí Azure CLI, spusťte příkaz "az storage account show", jak je znázorněno v následujícím příkladu:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Výsledek by měl vypadat přibližně jako v následujícím příkladu:
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
> Služba Azure Logic Apps se ještě k dispozici prostřednictvím rozhraní příkazového řádku Azure, tak účet služby Azure Storage se zobrazí v předchozím příkladu.
>
>

## <a name="retrieve-activity-log-data"></a>Načíst data protokolu aktivit
Kromě definice metrik a souvisejících hodnot je také možné načíst další zajímavé informace související s prostředky Azure pomocí REST API služby Azure Monitor. Jako příklad, je možné dotazu [protokolu aktivit](https://msdn.microsoft.com/library/azure/dn931934.aspx) data. Následující příklad ukazuje použití REST API služby Azure Monitor k dotazu data protokolu aktivit v určité datum rozsahu pro předplatné Azure:

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
* Zkontrolujte [Přehled monitorování](../azure-monitor/overview.md).
* Zobrazení [podporované metriky ve službě Azure Monitor](monitoring-supported-metrics.md).
* Zkontrolujte [Microsoft Azure, monitorování, Reference k rozhraní REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Zkontrolujte [knihovny správy Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
