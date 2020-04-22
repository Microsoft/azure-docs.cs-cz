---
title: Návod k rozhraní REST monitorování Azure
description: Jak ověřovat požadavky a použít rozhraní API AZURE Monitor REST k načtení dostupných definic metrik a hodnot metrik.
ms.subservice: metrics
ms.topic: conceptual
ms.date: 03/19/2018
ms.openlocfilehash: 6b0e321747e0f84be5a75ab96749311ff0071e8d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687418"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Návod k rozhraní REST monitorování Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento článek ukazuje, jak provádět ověřování, aby váš kód mohl používat [odkaz na rozhraní API služby Microsoft Azure Monitor REST .](https://docs.microsoft.com/rest/api/monitor/)

Rozhraní API monitorování Azure umožňuje programově načíst dostupné výchozí definice metrik, rozlišovací schopnost a hodnoty metriky. Data se dá uložit do samostatného úložiště dat, jako je Azure SQL Database, Azure Cosmos DB nebo Azure Data Lake. Odtud lze podle potřeby provést další analýzu.

Kromě práce s různými datovými body metriky rozhraní API monitoru také umožňuje vypsat pravidla výstrah, zobrazit protokoly aktivit a mnoho dalšího. Úplný seznam dostupných operací najdete v [tématu Microsoft Azure Monitor REST API Reference](https://docs.microsoft.com/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Ověřování požadavků Azure Monitoru

Prvním krokem je ověření požadavku.

Všechny úlohy prováděné podle rozhraní API Azure Monitor používají ověřovací model Azure Resource Manageru. Proto musí být všechny požadavky ověřeny pomocí služby Azure Active Directory (Azure AD). Jedním z přístupů k ověření klientské aplikace je vytvoření instančního objektu služby Azure AD a načtení tokenu ověřování (JWT). Následující ukázkový skript ukazuje vytvoření instančního objektu služby Azure AD prostřednictvím prostředí PowerShell. Podrobnější návod najdete v dokumentaci k [použití Azure PowerShell u vytvořit instanční objekt pro přístup k prostředkům](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Je také možné [vytvořit instanční objekt prostřednictvím portálu Azure](../../active-directory/develop/howto-create-service-principal-portal.md).

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Chcete-li dotaz rozhraní API monitorování Azure, klientská aplikace by měla použít dříve vytvořený instanční objekt pro ověření. Následující příklad skriptu prostředí PowerShell zobrazuje jeden přístup pomocí [knihovny adutentizace služby Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) k získání ověřovacího tokenu JWT. Token JWT je předán jako součást parametru autorizace HTTP v požadavcích na rozhraní REST azure monitoru.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Po ověření dotazů pak lze spustit proti rozhraní API Azure Monitor REST. Existují dva užitečné dotazy:

1. Seznam definic metrik pro zdroj
2. Načtení hodnot metriky

> [!NOTE]
> Další informace o ověřování pomocí rozhraní AZURE REST API najdete v odkazu [na odkaz rozhraní API azure rest](https://docs.microsoft.com/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Načíst definice metrik (vícerozměrné rozhraní API)

Pomocí [definice metriky Azure Monitor rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) pro přístup k seznamu metrik, které jsou k dispozici pro službu.

**Metoda**: GET

**Identifikátor URI**požadavku\/\/: https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Například k načtení definice metriky pro účet Azure Storage, požadavek se zobrazí takto:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Chcete-li načíst definice metrik pomocí vícerozměrné metriky Azure Monitor ROZHRANÍ REST API, použijte "2018-01-01" jako verzi rozhraní API.
>
>

Výsledné tělo odezvy JSON by bylo podobné následujícímu příkladu: (Všimněte si, že druhá metrika má rozměry)

```json
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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Načíst hodnoty dimenzí (multidimenzionální rozhraní API)

Jakmile jsou známy dostupné definice metrik, mohou existovat některé metriky, které mají dimenze. Před dotazováním na metriku můžete chtít zjistit, jaký rozsah hodnot dimenze má. Na základě těchto hodnot dimenzí pak můžete při dotazování na metriky filtrovat nebo segmentovat metriky na základě hodnot dimenzí.  K dosažení tohoto cíle použijte [rozhraní REST ROZHRANÍ Azure Monitor.](https://docs.microsoft.com/rest/api/monitor/metrics)

Použijte název metriky 'value' (ne 'localizedValue') pro všechny požadavky na filtrování . Pokud nejsou zadány žádné filtry, je vrácena výchozí metrika. Použití tohoto rozhraní API umožňuje pouze jednu dimenzi mít filtr se zástupnými kódy.

> [!NOTE]
> Chcete-li načíst hodnoty dimenzí pomocí rozhraní REST rozhraní Azure Monitor, použijte jako verzi rozhraní API "2018-01-01".
>
>

**Metoda**: GET

**Identifikátor URI**\:požadavku : https //management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metricnames= {metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&resultType=metadata&api-version=*{apiVersion}*

Chcete-li například načíst seznam hodnot dimenze, které byly emitovány pro dimenzi Název rozhraní API pro metriku Transakce, kde dimenze GeoType = Primární během zadaného časového rozsahu bude požadavek následující:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Výsledné tělo odezvy JSON by bylo podobné následujícímu příkladu:

```json
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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Načíst metrické hodnoty (vícerozměrné rozhraní API)

Jakmile jsou známy dostupné definice metrik a možné hodnoty dimenzí, je pak možné načíst související hodnoty metrik.  K dosažení tohoto cíle použijte [rozhraní REST ROZHRANÍ Azure Monitor.](https://docs.microsoft.com/rest/api/monitor/metrics)

Použijte název metriky 'value' (ne 'localizedValue') pro všechny požadavky na filtrování. Pokud nejsou zadány žádné filtry dimenzí, je vrácena souhrnná agregovaná metrika. Pokud dotaz na metriku vrátí více časových řad, můžete použít parametry dotazu "Top" a OrderBy a vrátit omezený seřazený seznam časových řad.

> [!NOTE]
> Chcete-li načíst vícerozměrné hodnoty metrik pomocí rozhraní REST rozhraní Azure Monitor, použijte jako verzi rozhraní API "2018-01-01".
>
>

**Metoda**: GET

**Identifikátor URI**požadavku\/: https: /management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-name}*/*{resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metric&name= {numbertime/endtime}*&$filter=*{filter}*&interval=*{timeGrain}*&agregace=*{aggreation}*&api-version=*{Version api}* *{starttime/endtime}*

Chcete-li například načíst horní 3 API, v sestupné hodnotě, podle počtu "Transakce" během rozsahu 5 min, kde geottype byl "Primární", požadavek by byl následující:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Výsledné tělo odezvy JSON by bylo podobné následujícímu příkladu:

```json
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

Pomocí [definice metriky Azure Monitor rozhraní REST API](https://msdn.microsoft.com/library/mt743621.aspx) pro přístup k seznamu metrik, které jsou k dispozici pro službu.

**Metoda**: GET

**Identifikátor URI**požadavku\/\/: https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Například k načtení definice metriky pro aplikaci Azure Logic, požadavek se zobrazí takto:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> K načtení definic metrik pomocí rozhraní REST azure monitoru použijte jako verzi rozhraní API "2016-03-01".
>
>

Výsledné tělo odezvy JSON by bylo podobné následujícímu příkladu:

```json
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

Další informace najdete v seznamu definic metrik pro prostředek v dokumentaci [k rozhraní REST ROZHRANÍ Azure Monitor.](https://msdn.microsoft.com/library/azure/mt743621.aspx)

## <a name="retrieve-metric-values"></a>Načíst hodnoty metriky

Jakmile jsou známy dostupné definice metrik, je pak možné načíst související hodnoty metriky. Použijte název metriky 'value' (ne 'localizedValue') pro všechny požadavky na filtrování (například načíst 'CpuTime' a 'Požadavky' metrické datové body). Pokud nejsou zadány žádné filtry, je vrácena výchozí metrika.

> [!NOTE]
> Chcete-li načíst hodnoty metrikpomocí rozhraní REST azure monitoru, použijte jako verzi rozhraní API "2016-09-01".
>
>

**Metoda**: GET

**Identifikátor URI požadavku**:`https:\//management.azure.com/subscriptions/\*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*`

Například načíst RunsSucceeded metrické datové body pro daný časový rozsah a pro časové zrnitosti 1 hodinu, požadavek by být následující:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Výsledné tělo odezvy JSON by bylo podobné následujícímu příkladu:

```json
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

Chcete-li načíst více datových nebo agregačních bodů, přidejte do filtru názvy definic metrik a typy agregace, jak je vidět v následujícím příkladu:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Výsledné tělo odezvy JSON by bylo podobné následujícímu příkladu:

```json
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

### <a name="use-armclient"></a>Použít klienta ARM

Dalším přístupem je použití [klienta ARMClient](https://github.com/projectkudu/armclient) v počítači se systémem Windows. ARMClient zpracovává ověřování Azure AD (a výsledný token JWT) automaticky. Následující kroky popisují použití klienta ARMClient pro načítání dat metriky:

1. Nainstalujte [Chocolatey](https://chocolatey.org/) a [ARMClient](https://github.com/projectkudu/armclient).
2. V okně terminálu zadejte *příkaz armclient.exe login*. Tím se vyzve k přihlášení do Azure.
3. Zadejte *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Zadejte *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Chcete-li například načíst definice metrik pro konkrétní aplikaci logiky, vyjezte následující příkaz:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Načtení ID prostředku

Použití rozhraní REST API může skutečně pomoci pochopit dostupné definice metrik, rozlišovací schopnost a související hodnoty. Tyto informace jsou užitečné při používání [Knihovny azure management .](https://msdn.microsoft.com/library/azure/mt417623.aspx)

Pro předchozí kód ID prostředku, který chcete použít, je úplná cesta k požadovanému prostředku Azure. Chcete-li například dotaz ovat proti Azure Web Appu, id prostředku by bylo:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Následující seznam obsahuje několik příkladů formátů ID prostředků pro různé prostředky Azure:

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastický fond SQL** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Service Bus** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Škálovací sady virtuálních strojů** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **Virtuální počítače** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Centra událostí** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Existují alternativní přístupy k načtení ID prostředku, včetně použití Průzkumníka prostředků Azure, zobrazení požadovaného prostředku na webu Azure Portal a prostřednictvím Prostředí PowerShell nebo rozhraní příkazového příkazového příkazu k Řešení Azure.

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure

Chcete-li najít ID prostředku pro požadovaný prostředek, jeden užitečný přístup je použití nástroje [Průzkumníkprostředků Azure.](https://resources.azure.com) Přejděte na požadovaný prostředek a pak se podívejte na zobrazené ID, jako na následujícím snímku obrazovky:

![Alt "Průzkumník prostředků Azure"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>portál Azure

ID prostředku lze také získat z portálu Azure. Chcete-li tak učinit, přejděte na požadovaný prostředek a vyberte vlastnosti. ID prostředku se zobrazí v části Vlastnosti, jak je vidět na následujícím snímku obrazovky:

![Alt "ID prostředku zobrazené v okně Vlastnosti na webu Azure Portal"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

ID prostředku lze načíst pomocí rutin Azure PowerShell také. Chcete-li například získat ID prostředku pro aplikaci Logika Azure, spusťte rutinu Get-AzureLogicApp, jako v následujícím příkladu:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Výsledek by měl být podobný následujícímu příkladu:

```output
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

Chcete-li načíst ID prostředku pro účet azure úložiště `az storage account show` pomocí azure cli, spusťte příkaz, jak je znázorněno v následujícím příkladu:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Výsledek by měl být podobný následujícímu příkladu:

```json
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
> Azure Logic Apps ještě nejsou k dispozici prostřednictvím rozhraní příkazového příkazu k řešení Azure, takže účet azure storage se zobrazí v předchozím příkladu.
>
>

## <a name="retrieve-activity-log-data"></a>Načtení dat protokolu aktivit

Kromě definic metrik a souvisejících hodnot je také možné použít rozhraní API AZURE Monitor REST k načtení dalších zajímavých přehledů souvisejících s prostředky Azure. Jako příklad je možné dotazovat data [protokolu aktivit.](https://msdn.microsoft.com/library/azure/dn931934.aspx) Následující ukázka ukazuje použití rozhraní AZURE Monitor REST API k dotazování dat protokolu aktivit v rámci určitého období pro předplatné Azure:

```powershell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Další kroky

* Projděte si [přehled monitorování](../../azure-monitor/overview.md).
* Zobrazení [podporovaných metrik pomocí Azure Monitoru](metrics-supported.md).
* Projděte si [odkaz na rozhraní REST MONITOR Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Projděte si [Knihovnu správy Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
