---
title: Získejte události stavu prostředků Azure pomocí REST API | Microsoft Docs
description: K získání událostí stavu pro prostředky Azure použijte rozhraní Azure REST API.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77653997"
---
# <a name="get-resource-health-using-the-rest-api"></a>Získat Resource Health pomocí REST API 

V tomto ukázkovém článku se dozvíte, jak načíst seznam událostí stavu pro prostředky Azure ve vašem předplatném pomocí [REST API Azure](/rest/api/azure/).

Kompletní Referenční dokumentace a další ukázky pro REST API jsou k dispozici v [Referenční příručce Azure monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Sestavení požadavku

K vypsání událostí stavu předplatného na rozsah času mezi `GET` `2018-05-16` a `2018-06-20`použijte následující požadavek HTTP.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Hlavičky požadavku

Jsou vyžadovány následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Authorization:*|Povinná hodnota. Nastavte na platný  [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer`. |  

### <a name="uri-parameters"></a>Parametry identifikátoru URI

| Název | Popis |
| :--- | :---------- |
| subscriptionId | ID předplatného, které identifikuje předplatné Azure. Pokud máte více předplatných, přečtěte si téma [práce s více předplatnými](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| verze-api | Verze rozhraní API, která se má použít pro požadavek<br /><br /> Tento dokument popisuje verzi `2015-04-01`rozhraní API, která je součástí výše uvedené adresy URL.  |
| $filter | Možnost filtrování pro snížení sady vrácených výsledků. Povolené vzory pro tento parametr jsou k dispozici [v odkazu na operaci protokolu aktivit](/rest/api/monitor/activitylogs/list#uri-parameters). Zobrazený příklad zachycuje všechny události v časovém rozsahu mezi 2018-05-16 a 2018-06-20. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Text požadavku

Pro tuto operaci není nutný žádný text žádosti.

## <a name="handle-the-response"></a>Zpracování odpovědi

Stavový kód 200 se vrátí se seznamem hodnot události stavu odpovídajících parametru filtru, společně s `nextlink` identifikátorem URI, který načte další stránku výsledků.

## <a name="example-response"></a>Příklad odpovědi 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
