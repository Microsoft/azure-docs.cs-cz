---
title: Vytvoření prvního volání rozhraní API pro přístup k obchodním datům pro obchod na webu Marketplace
description: Příklady, jak se naučit používat rozhraní API pro přístup k datům z komerčního obchodu na webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9e5fbdfca80d19f026a014a89ffbf137bacb521c
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639570"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Vytvoření prvního volání rozhraní API pro přístup k obchodním datům pro obchod na webu Marketplace

Seznam rozhraní API pro přístup k obchodním datům z komerčního tržiště najdete v tématu [rozhraní API pro přístup k datům pro analýzy komerčního tržiště](analytics-available-apis.md). Před provedením prvního volání rozhraní API se ujistěte, že jste splnili [požadavky](analytics-prerequisites.md) pro programový přístup ke komerčním datům na webu Marketplace.

## <a name="token-generation"></a>Generování tokenu

Před voláním některé z metod musíte nejprve získat přístupový token Azure Active Directory (Azure AD). Přístupový token služby Azure AD je potřeba předat do autorizační hlavičky každé metody v rozhraní API. Po získání přístupového tokenu máte 60 minut, než ho budete moct použít dřív, než vyprší jeho platnost. Po vypršení platnosti tokenu můžete token aktualizovat a pokračovat v jeho použití pro další volání rozhraní API.

Pro vygenerování tokenu použijte níže uvedený požadavek na ukázku. Tři hodnoty, které jsou požadovány pro vygenerování tokenu `clientId` , jsou, `clientSecret` a `tenantId` . `resource`Parametr by měl být nastaven na hodnotu `https://graph.windows.net` .

***Příklad požadavku***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Příklad odpovědi***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Další informace o tom, jak získat token Azure AD pro vaši aplikaci, najdete v tématu [přístup k datům Analytics pomocí služby Store](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>Programové volání rozhraní API

Po získání tokenu Azure AD, jak je popsáno v předchozí části, postupujte podle těchto kroků a vytvořte první sestavu programového přístupu.

Data je možné stáhnout z následujících datových sad (DataSet):

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

V následujících částech se zobrazí příklady, jak programově přistupovat `OrderId` z datové sady ISVOrder.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>Krok 1: vytvoření volání REST pomocí rozhraní API získat datové sady

Odpověď rozhraní API poskytuje název datové sady, ze kterého si můžete sestavu stáhnout. V případě konkrétní datové sady poskytuje odpověď rozhraní API také seznam volitelných sloupců, které lze použít pro vlastní šablonu sestavy. Názvy sloupců můžete získat také v následujících tabulkách:

- [Tabulka podrobností objednávky](orders-dashboard.md#orders-details-table)
- [Tabulka s podrobnostmi o využití](usage-dashboard.md#usage-details-table)
- [Tabulka s podrobnostmi o zákaznících](customer-dashboard.md#customer-details-table)
- [Tabulka podrobností webu Marketplace Insights](insights-dashboard.md#marketplace-insights-details-table)

***Příklad požadavku***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Příklad odpovědi***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>Krok 2: Vytvoření vlastního dotazu

V tomto kroku použijeme ID objednávky z sestavy objednávky k vytvoření vlastního dotazu pro sestavu, kterou chceme. Výchozí hodnota, `timespan` Pokud není zadána v dotazu, je šest měsíců.

***Příklad požadavku***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Příklad odpovědi***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Po úspěšném provedení dotazu `queryId` se vygeneruje, který se musí použít k vygenerování sestavy.

### <a name="step-3-execute-test-query-api"></a>Krok 3: spuštění rozhraní API pro test dotazů

V tomto kroku použijeme rozhraní test Query API k získání prvních 100 řádků pro vytvořený dotaz.

***Příklad požadavku***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Příklad odpovědi***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>Krok 4: Vytvoření sestavy

V tomto kroku použijeme dříve generovanou `QueryId` sestavu k vytvoření sestavy.

***Příklad požadavku***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Tabulka 1: popis parametrů použitých v tomto příkladu žádosti**_

| Parametr | Popis |
| ------------ | ------------- |
| `Description` | Zadejte stručný popis sestavy, která se generuje. |
| `QueryId` | To je `queryId` vygenerováno při vytvoření dotazu v kroku 2: Vytvoření vlastního dotazu. |
| `StartTime` | Čas, kdy bylo zahájeno první spuštění sestavy. |
| `RecurrenceInterval` | Interval opakování zadaný při vytváření sestavy |
| `RecurrenceCount` | Počet opakování poskytnutý během vytváření sestavy |
| `Format` | Podporují se formáty souborů CSV a TSV. |
|||

***Příklad odpovědi***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Po úspěšném spuštění `reportId` se vygeneruje, který se musí použít k naplánování stažení sestavy.

### <a name="step-5-execute-report-executions-api"></a>Krok 5: spouštění rozhraní API pro spouštění sestav

Abychom získali zabezpečené umístění (URL) sestavy, teď spustíme rozhraní API pro spouštění sestav.

***Příklad požadavku***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Příklad odpovědi***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Další kroky

- Rozhraní API můžete vyzkoušet prostřednictvím [adresy URL rozhraní Swagger](https://swagger.io/docs/specification/api-host-and-base-path/) .
- [Programové přístupové paradigma](analytics-programmatic-access.md)
