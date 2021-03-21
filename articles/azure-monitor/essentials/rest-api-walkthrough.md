---
title: Průvodce rozhraním REST API pro monitorování Azure
description: Ověřování požadavků a použití REST API Azure Monitor k načtení dostupných definic metrik a hodnot metriky.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: has-adal-ref
ms.openlocfilehash: a7cd6ff7c0c3b5d4bee859ef288f16673ebe0835
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033074"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Průvodce rozhraním REST API pro monitorování Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V tomto článku se dozvíte, jak provést ověřování, aby váš kód mohl používat [REST API odkaz Microsoft Azure monitorování](/rest/api/monitor/).

Rozhraní Azure Monitor API umožňuje programově načíst dostupné výchozí definice metriky, členitost a hodnoty metrik. Data je možné uložit do samostatného úložiště dat, například Azure SQL Database, Azure Cosmos DB nebo Azure Data Lake. V případě potřeby je možné provést další analýzu.

Kromě práce s různými datovými body metriky umožňuje rozhraní API monitor také vypisovat pravidla výstrah, zobrazovat protokoly aktivit a spoustu dalších věcí. Úplný seznam dostupných operací najdete v [referenčních informacích REST API monitorování Microsoft Azure](/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Ověřování žádostí Azure Monitor

Prvním krokem je ověření žádosti.

Všechny úlohy spouštěné v rozhraní Azure Monitor API používají model ověřování Azure Resource Manager. Proto musí být všechny požadavky ověřeny pomocí Azure Active Directory (Azure AD). Jedním z přístupů k ověření klientské aplikace je Vytvoření instančního objektu služby Azure AD a načtení tokenu JWT (Authentication). Následující vzorový skript ukazuje vytvoření instančního objektu služby Azure AD pomocí PowerShellu. Podrobnější návod najdete v dokumentaci k [používání Azure PowerShell k vytvoření instančního objektu pro přístup k prostředkům](/powershell/azure/create-azure-service-principal-azureps). Instanční objekt je také možné [vytvořit prostřednictvím Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md).

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

Pro dotazování rozhraní Azure Monitor API by klientská aplikace měla k ověření použít dřív vytvořený instanční objekt. Následující ukázkový skript prostředí PowerShell zobrazuje jeden přístup pomocí [Active Directory Authentication Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) k získání ověřovacího tokenu JWT. Token JWT se předává jako součást autorizačního parametru HTTP v požadavcích na Azure Monitor REST API.

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

Po ověření se můžou dotazy provádět na REST API Azure Monitor. K dispozici jsou dva užitečné dotazy:

1. Seznam definic metriky pro prostředek
2. Načtení hodnot metriky

> [!NOTE]
> Další informace o ověřování pomocí REST API Azure najdete v referenčních informacích k [azure REST API](/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Načíst definice metrik (multidimenzionální rozhraní API)

Pomocí [Azure monitor definice metrik REST API](/rest/api/monitor/metricdefinitions) získáte přístup k seznamu metrik, které jsou k dispozici pro službu.

**Metoda**: Get

**Identifikátor URI požadavku**: https: \/ \/ Management.Azure.com/Subscriptions/*{SubscriptionId}*/resourceGroups/*{resourceGroupName}*/Providers/*{resourceProviderNamespace}* / *{ResourceType*} / *{resourceName*}/Providers/Microsoft.Insights/metricDefinitions? API-Version =*{apiVersion}*

Pokud například chcete načíst definice metriky pro účet Azure Storage, zobrazí se tento požadavek takto:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Pokud chcete načíst definice metriky pomocí multidimenzionálních Azure Monitor metrik REST API, jako verzi rozhraní API použijte 2018-01-01.
>
>

Výsledný text odpovědi JSON by byl podobný následujícímu příkladu: (Všimněte si, že druhá metrika má rozměry)

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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Načtení hodnot dimenzí (multidimenzionální rozhraní API)

Jakmile jsou dostupné definice metriky známy, může existovat několik metrik, které mají rozměry. Před dotazování na metriku si možná budete chtít zjistit, jaký rozsah hodnot má dimenze. Na základě těchto hodnot dimenze můžete zvolit filtrování a segmentaci metrik na základě hodnot dimenzí při dotazování na metriky.  K tomuto účelu použijte [REST API Azure monitor metriky](/rest/api/monitor/metrics) .

Pro žádné požadavky na filtrování použijte název ' value ' (ne ' localizedValue ') metriky. Pokud nejsou zadány žádné filtry, bude vrácena výchozí metrika. Použití tohoto rozhraní API umožňuje použít jenom jednu dimenzi, která má filtr zástupných znaků.

> [!NOTE]
> Chcete-li načíst hodnoty dimenze pomocí REST API Azure Monitor, použijte jako verzi rozhraní API "2018-01-01".
>
>

**Metoda**: Get

**Identifikátor URI požadavku**: \: https//Management.Azure.com/Subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/*{Resource-Provider-Namespace}* / *{Resource-Type}* / *{Resource-Name}*/Providers/Microsoft.Insights/Metrics? metricnames =*{metric}*&TimeSpan =*{čas_spuštění/čas_ukončení}*&$Filter =*{Filter}*&ResultType = metadata&API-Version =*{apiVersion}*

Chcete-li například načíst seznam hodnot dimenzí, které byly vygenerovány pro metriku ' Transactions ' rozhraní ' API ' Dimension ', přičemž hodnota typu InType = ' primary ' v zadaném časovém rozsahu bude vyžadovat následující:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Výsledný text odpovědi JSON by byl podobný následujícímu příkladu:

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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Načtení hodnot metriky (multidimenzionální rozhraní API)

Jakmile jsou dostupné definice metriky a možné hodnoty dimenzí známy, je možné načíst související hodnoty metrik.  K tomuto účelu použijte [REST API Azure monitor metriky](/rest/api/monitor/metrics) .

Pro žádné požadavky na filtrování použijte název ' value ' (ne ' localizedValue ') metriky. Pokud nejsou zadány žádné filtry dimenzí, je vrácena agregovaná metrika. Pokud dotaz metriky vrátí více časové řady, můžete použít parametry dotazu top a OrderBy k vrácení omezeného seřazeného seznamu časové řady.

> [!NOTE]
> Chcete-li načíst multidimenzionální hodnoty metrik pomocí REST API Azure Monitor, použijte jako verzi rozhraní API "2018-01-01".
>
>

**Metoda**: Get

**Identifikátor URI požadavku**: https \/ :/Management.Azure.com/Subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/*{Resource-Provider-obor názvů}* / *{Resource-Type}* / *{Resource-Name}*/Providers/Microsoft.Insights/Metrics? metricnames =*{metric}*&TimeSpan =*{čas_spuštění/čas_ukončení}*&$Filter =*{Filter}*&interval =*{timeGrain}*&agregace =*{aggreation}*&API-Version =*{apiVersion}*

Například pro načtení horních 3 rozhraní API v sestupných hodnotách podle počtu transakcí během 5 min., kde GeotType byla ' primary ', bude požadavek vypadat takto:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Výsledný text odpovědi JSON by byl podobný následujícímu příkladu:

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

## <a name="retrieve-metric-definitions"></a>Načíst definice metriky

Pomocí [Azure monitor definice metrik REST API](/rest/api/monitor/metricdefinitions) získáte přístup k seznamu metrik, které jsou k dispozici pro službu.

**Metoda**: Get

**Identifikátor URI požadavku**: https: \/ \/ Management.Azure.com/Subscriptions/*{SubscriptionId}*/resourceGroups/*{resourceGroupName}*/Providers/*{resourceProviderNamespace}* / *{ResourceType*} / *{resourceName*}/Providers/Microsoft.Insights/metricDefinitions? API-Version =*{apiVersion}*

Chcete-li například načíst definice metrik pro aplikaci logiky Azure, zobrazí se tato žádost:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Chcete-li načíst definice metriky pomocí Azure Monitor REST API, použijte jako verzi rozhraní API "2016-03-01".
>
>

Výsledný text odpovědi JSON by byl podobný následujícímu příkladu:

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

Další informace najdete v tématu [Přehled definic metrik pro prostředek v Azure Monitor REST API](/rest/api/monitor/metricdefinitions) dokumentaci.

## <a name="retrieve-metric-values"></a>Načtení hodnot metriky

Jakmile jsou dostupné definice metriky známy, je možné načíst související hodnoty metrik. Použijte název metriky value ' (ne ' localizedValue ') pro jakékoli požadavky na filtrování (například načte datové body metriky ' CpuTime ' a ' requests '). Pokud nejsou zadány žádné filtry, bude vrácena výchozí metrika.

> [!NOTE]
> Chcete-li načíst hodnoty metriky pomocí REST API Azure Monitor, použijte jako verzi rozhraní API "2016-09-01".
>
>

**Metoda**: `GET`

**Identifikátor URI žádosti**: `https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}/providers/microsoft.insights/metrics?$filter={filter}&api-version={apiVersion}`

Například pro načtení datových bodů metriky RunsSucceeded pro daný časový rozsah a za časový interval 1 hodiny bude požadavek následující:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Výsledný text odpovědi JSON by byl podobný následujícímu příkladu:

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

Chcete-li načíst více datových nebo agregačních bodů, přidejte do filtru názvy definice metriky a typy agregace, jak je vidět v následujícím příkladu:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Výsledný text odpovědi JSON by byl podobný následujícímu příkladu:

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

### <a name="use-armclient"></a>Použití ARMClient

Dalším řešením je použití [ARMClient](https://github.com/projectkudu/armclient) na počítači s Windows. ARMClient zpracovává automatické ověřování Azure AD (a výsledný token JWT). Následující kroky popisují použití ARMClient pro načtení dat metriky:

1. Nainstalujte [čokolády](https://chocolatey.org/) a [ARMClient](https://github.com/projectkudu/armclient).
2. V okně terminálu zadejte *armclient.exe přihlášení*. Zobrazí se výzva, abyste se přihlásili do Azure.
3. Typ *ARMCLIENT Get [your_resource_id]/Providers/Microsoft.Insights/metricdefinitions? API-Version = 2016-03-01*
4. Typ *ARMCLIENT Get [your_resource_id]/Providers/Microsoft.Insights/Metrics? API-Version = 2016-09-01*

Chcete-li například načíst definice metrik pro konkrétní aplikaci logiky, vydejte následující příkaz:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Načíst ID prostředku

Použití REST API může skutečně pomoci pochopit dostupné definice metriky, členitost a související hodnoty. Tyto informace jsou užitečné při používání [knihovny pro správu Azure](/previous-versions/azure/reference/mt417623(v=azure.100)).

V předchozím kódu je ID prostředku, které se má použít, úplnou cestou k požadovanému prostředku Azure. Například pro dotazování na webovou aplikaci Azure by ID prostředku bylo:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Následující seznam obsahuje několik příkladů formátů ID prostředků pro různé prostředky Azure:

* **IoT Hub** -/Subscriptions/*{ID předplatného}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.Devices/IotHubs/*{IoT-Hub-Name}*
* **Elastický fond SQL** –/Subscriptions/*{ID předplatného}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.SQL/Servers/*{Pool-DB}*/elasticpools/*{SQL-Pool-Name}*
* **SQL Database (V12)** -/Subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.SQL/Servers/*{Server-Name}*/databases/*{Database-Name}*
* **Service Bus** -/Subscriptions/*{ID předplatného}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.ServiceBus/*{Namespace}* / *{ServiceBus-Name}*
* **Virtual Machine Scale Sets** –/Subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.COMPUTE/virtualMachineScaleSets/*{VM-Name}*
* **Virtuální počítače** -/Subscriptions/*{Subscription-ID}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.COMPUTE/virtualMachines/*{VM-Name}*
* **Event Hubs** -/Subscriptions/*{ID předplatného}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.EventHub/Namespaces/*{EventHub-Namespace}*

Existují alternativní přístupy k načtení ID prostředku, včetně použití Azure Resource Explorer, zobrazení požadovaného prostředku v Azure Portal a prostřednictvím PowerShellu nebo rozhraní příkazového řádku Azure CLI.

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure

Chcete-li najít ID prostředku pro požadovaný prostředek, je jedním z užitečných způsobů použití nástroje [Azure Resource Explorer](https://resources.azure.com) . Přejděte k požadovanému prostředku a podívejte se na zobrazené ID, jak je znázorněno na následujícím snímku obrazovky:

![ALT "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>portál Azure

ID prostředku se taky dá získat z Azure Portal. Provedete to tak, že přejdete k požadovanému prostředku a pak vyberete vlastnosti. ID prostředku se zobrazí v části Properties (vlastnosti), jak je vidět na následujícím snímku obrazovky:

![ALT "ID prostředku zobrazené v okně Vlastnosti v Azure Portal"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

ID prostředku se dá načíst taky pomocí rutin Azure PowerShell. Pokud například chcete získat ID prostředku pro aplikaci logiky Azure, spusťte rutinu Get-AzureLogicApp, jako v následujícím příkladu:

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

Pokud chcete načíst ID prostředku pro účet Azure Storage pomocí rozhraní příkazového řádku Azure, spusťte `az storage account show` příkaz, jak je znázorněno v následujícím příkladu:

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
> Azure Logic Apps ještě nejsou k dispozici prostřednictvím rozhraní příkazového řádku Azure CLI, takže v předchozím příkladu se zobrazí Azure Storage účet.
>
>

## <a name="retrieve-activity-log-data"></a>Načte data protokolu aktivit.

Kromě definic metrik a souvisejících hodnot je také možné použít REST API Azure Monitor k získání dalších zajímavých přehledů týkajících se prostředků Azure. Například je možné dotazovat data [protokolu aktivit](/rest/api/monitor/activitylogs) . Následující ukázkové požadavky používají REST API Azure Monitor k dotazování protokolu aktivit.

Získání protokolů aktivit pomocí filtru:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Získejte protokoly aktivit pomocí filtru a vyberte:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Získání protokolů aktivit pomocí výběru:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Získat protokoly aktivit bez filtru nebo vybrat:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```

## <a name="next-steps"></a>Další kroky

* Projděte si [Přehled monitorování](../overview.md).
* Zobrazte [podporované metriky pomocí Azure monitor](./metrics-supported.md).
* Přečtěte si [referenční informace o REST API monitorování Microsoft Azure](/rest/api/monitor/).
* Projděte si [knihovnu správy Azure](/previous-versions/azure/reference/mt417623(v=azure.100)).