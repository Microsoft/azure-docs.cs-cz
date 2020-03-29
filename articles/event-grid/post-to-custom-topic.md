---
title: Zveřejnit událost do vlastního tématu Azure Event Grid
description: Tento článek popisuje, jak zveřejnit událost na vlastní téma. Zobrazuje formát post a data událostí.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 0afad249f71a36bf7552da499e985b68d48ee7a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721550"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Odeslání vlastního tématu pro Azure Event Grid

Tento článek popisuje, jak zveřejnit událost na vlastní téma. Zobrazuje formát post a data událostí. [Smlouva o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) se vztahuje pouze na příspěvky, které odpovídají očekávanému formátu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Koncový bod

Při odesílání funkce HTTP POST na vlastní téma `https://<topic-endpoint>?api-version=2018-01-01`použijte formát URI: .

Platný identifikátor URI je `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`například: .

Chcete-li získat koncový bod pro vlastní téma pomocí azure cli, použijte:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Pokud chcete získat koncový bod pro vlastní téma s Azure PowerShellem, použijte:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Hlavička

Do požadavku uveďte hodnotu `aeg-sas-key` hlavičky s názvem, která obsahuje klíč pro ověřování.

Například platná hodnota záhlaví `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`je .

Chcete-li získat klíč pro vlastní téma pomocí azure cli, použijte:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Chcete-li získat klíč pro vlastní téma s PowerShellem, použijte:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Data událostí

U vlastních témat data nejvyšší úrovně obsahují stejná pole jako standardní události definované prostředky. Jednou z těchto vlastností je vlastnost dat, která obsahuje vlastnosti jedinečné pro vlastní téma. Jako vydavatel události určíte vlastnosti tohoto datového objektu. Použijte následující schéma:

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

Popis těchto vlastností najdete [v tématu Schéma událostí Sítě událostí Azure](event-schema.md). Při zaúčtování událostí do tématu mřížky událostí může mít pole celkovou velikost až 1 MB. Každá událost v poli je omezena na 64 KB (obecná dostupnost) nebo 1 MB (náhled).

> [!NOTE]
> Na událost o velikosti až 64 kB se vztahuje smlouva o úrovni služeb obecné dostupnosti (GA). Podpora pro událost o velikosti až 1 MB je aktuálně ve verzi Preview. Události nad 64 KB se účtují v krocích po 64 KB. 

Platné schéma dat událostí je například:

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

Po odeslání do koncového bodu tématu obdržíte odpověď. Odpověď je standardní kód odpovědi HTTP. Některé běžné odpovědi jsou:

|Výsledek  |Odpověď  |
|---------|---------|
|Úspěch  | 200 OK  |
|Data událostí mají nesprávný formát. | 400 Chybný požadavek |
|Neplatný přístupový klíč | 401 Neautorizováno |
|Nesprávný koncový bod | 404 Nenalezeno |
|Pole nebo událost překračuje omezení velikosti | 413 Užitečné zatížení je příliš velké |

V případě chyb má text zprávy následující formát:

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

* Informace o dodávkách událostí monitorování naleznete v [tématu Sledování doručování zpráv v programu Event Grid](monitor-event-delivery.md).
* Další informace o ověřovacím klíči naleznete v tématu [Zabezpečení a ověřování mřížky událostí](security-authentication.md).
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
