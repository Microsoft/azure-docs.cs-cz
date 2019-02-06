---
title: Získat Azure resource health události pomocí rozhraní REST API | Dokumentace Microsoftu
description: Získat události stavu prostředků Azure pomocí rozhraní REST API služby Azure.
services: Resource health
author: rloutlaw
ms.reviewer: routlaw
manager: angerobe
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.author: routlaw
ms.openlocfilehash: 6e4bd5977eb1761a065d5583675e6fd405b4610a
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750354"
---
# <a name="get-resource-health-using-the-rest-api"></a>Získat stav prostředku pomocí rozhraní REST API 

Tento článek příklad ukazuje, jak načíst seznam události stavu prostředků Azure v předplatném pomocí [rozhraní Azure REST API](/rest/api/azure/).

Úplnou referenční dokumentaci a další ukázky pro rozhraní REST API jsou k dispozici v [Reference k rozhraní REST pro monitorování Azure](/rest/api/monitor). 

## <a name="build-the-request"></a>Žádost o sestavení

Pomocí následujících `GET` požadavek protokolu HTTP na seznamu událostí stavu pro vaše předplatné v rozsahu mezi `2018-05-16` a `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Hlavičky požadavku

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Typ obsahu:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastaven na platné `Bearer` [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parametry identifikátoru URI

| Název | Popis |
| :--- | :---------- |
| subscriptionId | ID předplatného, který identifikuje předplatné Azure. Pokud máte více předplatných, přečtěte si téma [práce s několika předplatnými](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| verze API-version | Verze rozhraní API, která se má použít pro daný požadavek.<br /><br /> Tento dokument popisuje verzi api-version `2015-04-01`, který je obsažen v adrese URL výše.  |
| $filter | Filtrování možnost omezit sadu vrácených výsledků. Povolená vzory pro tento parametr jsou k dispozici [v referenční dokumentaci pro operaci protokoly aktivit](/rest/api/monitor/activitylogs/list#uri-parameters). Jak ukazuje příklad zaznamenány všechny události v časovém rozmezí mezi 2018-05-16 a 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Text požadavku

Pro tuto operaci je potřeba není datová část požadavku.

## <a name="handle-the-response"></a>Zpracování odpovědi

Seznam stavů událostí hodnoty odpovídající parametr filtru spolu s se vrátí stavový kód 200 `nextlink` identifikátor URI pro načtení další stránky výsledků.

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
