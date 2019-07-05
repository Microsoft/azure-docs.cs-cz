---
title: Referenční informace k mezipaměti vlastních prostředků
description: Odkaz na vlastní prostředek mezipaměti pro poskytovatele prostředků Azure vlastní. V tomto článku budou procházet požadavky na koncové body implementaci mezipaměti vlastní prostředky.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6a3d570d9695516a293b601b3d34c2bcba6b058d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478885"
---
# <a name="custom-resource-cache-reference"></a>Odkaz na vlastní prostředek mezipaměti

V tomto článku budou procházet požadavky na koncové body implementaci mezipaměti vlastní prostředky. Pokud nejste obeznámeni s vlastní poskytovatele prostředků Azure, přečtěte si téma [přehled o poskytovatelích vlastní prostředek](./custom-providers-overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Jak definovat koncový bod mezipaměti prostředku

Prostředek serveru proxy je vytvořit tak, že zadáte **routingType** "Proxy, mezipaměti".

Ukázka vlastních prostředků zprostředkovatele:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>Koncový bod prostředku vytváření proxy serveru

**Koncový bod** prostředek "Proxy, mezipaměti", která implementuje **koncový bod** musí zpracovat žádost a odpověď pro nové rozhraní API v Azure. V takovém případě **resourceType** vygeneruje nový Azure prostředku rozhraní API pro `PUT`, `GET`, a `DELETE` provádět CRUD u jednoho prostředku, stejně jako `GET` k načtení všech existujících prostředků:

> [!NOTE]
> Rozhraní API služby Azure se generovat požadavek metody `PUT`, `GET`, a `DELETE`, ale mezipaměť **koncový bod** jenom je potřeba zpracovat `PUT` a `DELETE`.
> Doporučujeme, který **koncový bod** také implementuje `GET`.

### <a name="create-a-custom-resource"></a>Vytvoření vlastních prostředků

Azure API příchozí žádosti:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Tento požadavek bude poté předávat **koncový bod** ve tvaru:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Podobně, odpověď **koncový bod** je pak předán zpět na zákazníka. By měla vrátit odpověď od koncového bodu:

- Platný dokument objekt JSON. Všechny řetězce a pole by se vnoří pod objektu nejvyšší úrovně.
- `Content-Type` Záhlaví musí být nastavená na "application/json; charset = utf-8 ".
- Dojde k přepsání poskytovatele vlastních prostředků `name`, `type`, a `id` pole pro daný požadavek.
- Zprostředkovatel vlastní prostředek pouze vrátí pole v rámci `properties` objektu pro koncový bod mezipaměti.

**Koncový bod** odpovědi:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

`name`, `id`, A `type` pole bude vytvořen automaticky pro vlastní prostředek poskytovatelem vlastních prostředků.

Odpověď zprostředkovatele vlastních prostředků Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Odebrat vlastní prostředek

Azure API příchozí žádosti:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tento požadavek bude poté předávat **koncový bod** ve tvaru:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobně, odpověď **koncový bod** je pak předán zpět na zákazníka. By měla vrátit odpověď od koncového bodu:

- Platný dokument objekt JSON. Všechny řetězce a pole by se vnoří pod objektu nejvyšší úrovně.
- `Content-Type` Záhlaví musí být nastavená na "application/json; charset = utf-8 ".
- Poskytovatel prostředků Azure vlastní odebere pouze položky uloženou v mezipaměti pokud se vrátí odpověď 200 úrovních. I v případě, prostředek neexistuje, **koncový bod** by měla vrátit 204.

**Koncový bod** odpovědi:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Odpověď zprostředkovatele vlastních prostředků Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Načíst vlastní prostředek

Azure API příchozí žádosti:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Požadavek bude **není** předávat **koncový bod**.

Odpověď zprostředkovatele vlastních prostředků Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Vytvořit výčet všech vlastních prostředků

Azure API příchozí žádosti:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tento požadavek se **není** předávat **koncový bod**.

Odpověď zprostředkovatele vlastních prostředků Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Další postup

- [Přehled poskytovatelů vlastního prostředku Azure](./custom-providers-overview.md)
- [Kurz: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postup: Přidání vlastní akce k rozhraní Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referenční dokumentace: Odkaz na vlastní prostředek Proxy](./custom-providers-proxy-resource-endpoint-reference.md)
