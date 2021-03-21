---
title: Odeslání události do vlastního Azure Event Grid tématu
description: Tento článek popisuje, jak odeslat událost do vlastního tématu. Zobrazuje formát dat pro odeslání a událost.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ed126487938e524264c94544903460854ffc4d41
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681596"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Příspěvek do vlastního tématu pro Azure Event Grid

Tento článek popisuje, jak odeslat událost do vlastního tématu. Zobrazuje formát dat pro odeslání a událost. [Smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) se vztahuje pouze na příspěvky, které odpovídají očekávanému formátu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Koncový bod

Při odesílání příspěvku HTTP na vlastní téma použijte formát identifikátoru URI: `https://<topic-endpoint>?api-version=2018-01-01` .

Například platný identifikátor URI je: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .

Pokud chcete získat koncový bod pro vlastní téma pomocí Azure CLI, použijte:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Chcete-li získat koncový bod pro vlastní téma s Azure PowerShell, použijte:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Hlavička

V žádosti zahrňte hodnotu záhlaví s názvem `aeg-sas-key` , která obsahuje klíč pro ověření.

Například platná hodnota hlavičky je `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==` .

Pokud chcete získat klíč pro vlastní téma pomocí Azure CLI, použijte:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Pokud chcete získat klíč pro vlastní téma pomocí PowerShellu, použijte:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Data událostí

Pro vlastní témata data nejvyšší úrovně obsahují stejná pole jako standardní události definované prostředky. Jedna z těchto vlastností je vlastnost dat, která obsahuje vlastnosti, které jsou jedinečné pro vlastní téma. Jako vydavatel událostí určíte vlastnosti pro daný datový objekt. Použijte následující schéma:

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

Popis těchto vlastností najdete v tématu [Azure Event Grid schéma událostí](event-schema.md). Při odesílání událostí do tématu Event gridu může mít pole celkovou velikost až 1 MB. Maximální povolená velikost události je také 1 MB. Události větší než 64 KB se účtují v přírůstcích po 64 až KB. 

Například platné schéma dat událostí je:

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

Po odeslání do koncového bodu tématu obdržíte odpověď. Odpověď je standardní kód odpovědi HTTP. Mezi běžné odezvy patří:

|Výsledek  |Odpověď  |
|---------|---------|
|Success  | 200 OK  |
|Data události mají nesprávný formát. | 400 – Chybný požadavek |
|Neplatný přístupový klíč | 401 – Neautorizováno |
|Nesprávný koncový bod | 404 Nenalezeno |
|Pole nebo událost překračuje omezení velikosti. | datová část 413 je moc velká. |

V případě chyb má tělo zprávy následující formát:

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

## <a name="next-steps"></a>Další kroky

* Informace o sledování doručení událostí najdete v tématu [monitorování Event Grid doručování zpráv](monitor-event-delivery.md).
* Další informace o ověřovacím klíči najdete v tématu [Event Grid Security and Authentication](security-authentication.md).
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
