---
title: Referenční informace k proxy vlastních prostředků
description: Odkaz na vlastní prostředek proxy pro poskytovatele prostředků Azure vlastní. V tomto článku budou procházet požadavky na koncové body implementace proxy vlastní prostředky.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: fe470165d11b24ffb1df704ecaa9804663fc9e5c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796035"
---
# <a name="custom-resource-proxy-reference"></a>Odkaz na vlastní prostředek Proxy

V tomto článku budou procházet požadavky na koncové body implementace proxy vlastní prostředky. Pokud nejste obeznámeni s vlastní poskytovatele prostředků Azure, přečtěte si téma [přehled o poskytovatelích vlastní prostředek](./custom-providers-overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Jak definovat koncový bod prostředku proxy

Prostředek serveru proxy je vytvořit tak, že zadáte **routingType** "Proxy".

Ukázka vlastních prostředků zprostředkovatele:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
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

**Koncový bod** prostředek "Proxy", která implementuje **koncový bod** musí zpracovat žádost a odpověď pro nové rozhraní API v Azure. V takovém případě **resourceType** vygeneruje nový Azure prostředku rozhraní API pro `PUT`, `GET`, a `DELETE` provádět CRUD u jednoho prostředku, stejně jako `GET` k načtení všech existujících prostředků.

> [!NOTE]
> `id`, `name`, A `type` pole nejsou vyžadovány, ale jsou potřebné k integraci vlastního prostředku s existující ekosystému Azure.

Ukázka prostředků:

``` JSON
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

Odkaz na parametr:

Vlastnost | Ukázka | Popis
---|---|---
name | '{myCustomResourceName}' | Název vlastního prostředku.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Obor názvů typu prostředku.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | ID prostředku.

### <a name="create-a-custom-resource"></a>Vytvoření vlastních prostředků

Azure API příchozí žádosti:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

**Koncový bod** odpovědi:

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

Podobně jako odpověď **koncový bod** je pak předán zpět na zákazníka. By měla vrátit odpověď od koncového bodu:

- Platný dokument objekt JSON. Všechny řetězce a pole by se vnoří pod objektu nejvyšší úrovně.
- `Content-Type` Záhlaví musí být nastavená na "application/json; charset = utf-8 ".

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

Tento požadavek bude poté předávat **koncový bod** ve tvaru:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobně, odpověď **koncový bod** je pak předán zpět na zákazníka. By měla vrátit odpověď od koncového bodu:

- Platný dokument objekt JSON. Všechny řetězce a pole by se vnoří pod objektu nejvyšší úrovně.
- `Content-Type` Záhlaví musí být nastavená na "application/json; charset = utf-8 ".

**Koncový bod** odpovědi:

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

Tento požadavek bude poté předávat **koncový bod** ve tvaru:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Podobně, odpověď **koncový bod** je pak předán zpět na zákazníka. By měla vrátit odpověď od koncového bodu:

- Platný dokument objekt JSON. Všechny řetězce a pole by se vnoří pod objektu nejvyšší úrovně.
- `Content-Type` Záhlaví musí být nastavená na "application/json; charset = utf-8 ".
- Seznam prostředků by měl být umístěn v nejvyšší úrovni `value` vlastnost.

**Koncový bod** odpovědi:

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

## <a name="next-steps"></a>Další kroky

- [Přehled poskytovatelů vlastního prostředku Azure](./custom-providers-overview.md)
- [Rychlé zprovoznění: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: Vytvoření vlastní akce a prostředků v Azure](./tutorial-custom-providers-101.md)
- [Postup: Přidání vlastní akce k rozhraní Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referenční dokumentace: Odkaz na vlastní prostředek mezipaměti](./custom-providers-proxy-cache-resource-endpoint-reference.md)
