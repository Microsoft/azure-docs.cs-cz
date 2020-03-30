---
title: Získání událostí stavu prostředků Azure pomocí rozhraní REST API | Dokumenty společnosti Microsoft
description: Pomocí api Azure REST získáte události stavu pro vaše prostředky Azure.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77653997"
---
# <a name="get-resource-health-using-the-rest-api"></a>Získat stav prostředků pomocí rozhraní REST API 

Tento příklad článku ukazuje, jak načíst seznam událostí stavu pro prostředky Azure ve vašem předplatném pomocí [rozhraní AZURE REST API](/rest/api/azure/).

Kompletní referenční dokumentace a další ukázky pro rozhraní REST API jsou k dispozici v [odkazu Azure Monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Sestavení požadavku

Pomocí následujícího `GET` požadavku HTTP zobrazíte seznam událostí stavu pro `2018-05-16` `2018-06-20`vaše předplatné v rozsahu mezi a .

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

| Name (Název) | Popis |
| :--- | :---------- |
| subscriptionId | ID předplatného, které identifikuje předplatné Azure. Pokud máte více předplatných, přečtěte si informace [o práci s více předplatnými](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| verze-api | Verze rozhraní API pro požadavek.<br /><br /> Tento dokument se `2015-04-01`vztahuje api-verze , které jsou zahrnuty ve výše uvedené URL.  |
| $filter | Možnost filtrování snížit sadu vrácených výsledků. Povolené vzorky pro tento parametr jsou k dispozici [v odkazu na operaci protokoly aktivit](/rest/api/monitor/activitylogs/list#uri-parameters). Zobrazený příklad zachycuje všechny události v časovém rozsahu mezi 2018-05-16 a 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Text požadavku

Pro tuto operaci není potřeba žádné tělo požadavku.

## <a name="handle-the-response"></a>Zpracování odpovědi

Stavový kód 200 je vrácen se seznamem hodnot událostí stavu `nextlink` odpovídajících parametru filtru spolu s identifikátorem URI pro načtení další stránky výsledků.

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
