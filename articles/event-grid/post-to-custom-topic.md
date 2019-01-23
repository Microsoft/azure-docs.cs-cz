---
title: Odeslání události do vlastního tématu Azure Event Grid
description: Popisuje, jak publikovat události do vlastního tématu pro Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: b219e9475151ecd14d8b45db9501a06cde05875b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470592"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Publikovat do vlastního tématu pro Azure Event Grid

Tento článek popisuje, jak publikovat události do vlastního tématu. Zobrazuje formát dat post a události. [Smlouva o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) platí jenom pro příspěvky, které odpovídají očekávanému formátu.

## <a name="endpoint"></a>Koncový bod

Při odesílání požadavku HTTP POST do vlastního tématu, použijte formát identifikátoru URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Například je platný identifikátor URI: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Koncový bod vlastního tématu pomocí Azure CLI, použijte:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Koncový bod vlastního tématu pomocí Azure Powershellu, použijte:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Hlavička

V požadavku, zahrnují hodnotu záhlaví s názvem `aeg-sas-key` , který obsahuje klíč pro ověřování.

Je třeba hodnotu platnou hlavičku `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Chcete-li získat klíč vlastního tématu pomocí Azure CLI, použijte:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Pokud chcete získat klíč vlastního tématu pomocí Powershellu, použijte:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Data událostí

Pro vlastní témata nejvyšší úrovně data obsahují stejné pole jako standardní událostí definovaných prostředků. Jedna z těchto vlastností je vlastnost data, která obsahuje vlastnosti, které jsou jedinečné pro vlastního tématu. Jako zdroj události určit vlastnosti pro tento datový objekt. Používejte následující schéma:

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

Popis těchto vlastností najdete v tématu [schéma událostí služby Azure Event Grid](event-schema.md). Při odesílání událostí do téma event gridu, může pole mít celková velikost až 1 MB. Každá událost v poli je omezena na 64 KB.

Schéma dat platné události je například:

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

Po odeslání do tématu koncového bodu, neobdrží odpověď. Odpověď je standardní kód odpovědi HTTP. Jsou některé běžné odpovědi:

|Výsledek  |Odpověď  |
|---------|---------|
|Úspěch  | 200 OK  |
|Data události mají nesprávný formát | 400 – Chybný požadavek |
|Neplatný přístupový klíč | 401 Neautorizováno |
|Nesprávný koncového bodu | 404 – Nenalezeno |
|Pole nebo události překračuje omezení velikosti | Moc velký 413 datové části |

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

* Informace o monitorování doručení událostí najdete v tématu [doručování zpráv služby Event Grid monitorování](monitor-event-delivery.md).
* Další informace o ověřovací klíč najdete v tématu [ověřování a zabezpečení služby Event Grid](security-authentication.md).
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
