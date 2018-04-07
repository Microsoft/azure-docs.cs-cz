---
title: POST událost, která má vlastní téma Azure událostí mřížky
description: Popisuje, jak odeslat událost do vlastní tématu pro Azure událostí mřížky
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/05/2018
ms.author: tomfitz
ms.openlocfilehash: 1c23aef0773ffddbc26e4090ecf137b632394ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Odeslání na vlastní tématu pro Azure událostí mřížky

Tento článek popisuje, jak odeslat událost do vlastní tématu. Zobrazuje formátu dat post a události. [Smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) se vztahuje pouze na příspěvky, které odpovídají očekávanému formátu.

## <a name="endpoint"></a>Koncový bod

Při odesílání HTTP POST do vlastní tématu, použijte formát identifikátoru URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Například je platný identifikátor URI: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Koncový bod pro vlastní téma pomocí rozhraní příkazového řádku Azure, použijte:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Koncový bod pro vlastní téma s prostředím Azure PowerShell, použijte:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Záhlaví

V žádosti o zahrnout hodnotu záhlaví s názvem `aeg-sas-key` obsahující klíče pro ověřování.

Je třeba hodnotu hlavičky platné `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Klíč pro vlastní téma se rozhraní příkazového řádku Azure, použijte:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Klíč pro vlastní téma pomocí prostředí PowerShell, použijte:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Data událostí

Pro vlastní témata obsahuje dat nejvyšší úrovně stejných polí jako standardní události definované prostředků. Jedna z těchto vlastností je vlastnost data, která obsahuje vlastnosti, které jsou jedinečné pro vlastní heslo. Jako vydavatel události určit vlastnosti pro tento datový objekt. Použijte následující schéma:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Popis těchto vlastností najdete v tématu [schématu události mřížky událostí Azure](event-schema.md).

Schéma dat platný událostí je například:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Odpověď

Po odeslání ke koncovému bodu tématu, obdržíte odpověď. Odpověď je standardní kódu odpovědi HTTP. Některé běžné odpovědi jsou:

|Výsledek  |Odpověď  |
|---------|---------|
|Úspěch  | 200 OK  |
|Nesprávný koncový bod | 404 – Nenalezeno |
|Neplatný přístupový klíč | 401 unauthorized |
|Data události mají nesprávný formát. | 400 – Chybný požadavek |

Text zprávy pro chyby, má následující formát:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Další postup

* Informace o sledování událostí dodávky najdete v tématu [doručení zpráv monitorování událostí mřížky](monitor-event-delivery.md).
* Další informace o klíči ověřování najdete v tématu [mřížky událostí zabezpečení a ověřování](security-authentication.md).
* Další informace o vytváření předplatného služby Azure událostí mřížky, najdete v části [schématu odběru událostí mřížky](subscription-creation-schema.md).
