---
title: Referenční informace k proxy vlastních prostředků
description: Odkaz na vlastní proxy prostředky pro zprostředkovatele vlastních prostředků Azure. Tento článek projde požadavky na koncové body implementující vlastní prostředky proxy.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650458"
---
# <a name="custom-resource-proxy-reference"></a>Odkaz na vlastní proxy prostředek

Tento článek projde požadavky na koncové body implementující vlastní prostředky proxy. Pokud nejste obeznámeni s Azure Vlastní zprostředkovatelé prostředků, podívejte se [na přehled o vlastní zprostředkovatelé prostředků](overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Jak definovat koncový bod prostředku proxy

Prostředek proxy lze vytvořit zadáním **routingType** na "Proxy".

Ukázka vlastního zprostředkovatele prostředků:

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

## <a name="building-proxy-resource-endpoint"></a>Vytváření koncového bodu prostředků proxy

**Koncový bod,** který implementuje **koncový bod** prostředku "proxy" musí zpracovat požadavek a odpověď pro nové rozhraní API v Azure. V takovém případě **resourceType** vygeneruje nové `PUT`rozhraní `GET`API `DELETE` prostředků Azure pro , a provádět `GET` CRUD na jeden prostředek, jakož i načíst všechny existující prostředky.

> [!NOTE]
> Pole `id` `name`, `type` a nejsou povinné, ale jsou potřeba k integraci vlastního prostředku s existujícím ekosystémem Azure.

Ukázkový zdroj:

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
jméno | '{myCustomResourceName}' | Název vlastního prostředku.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Obor názvů typu prostředku.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | ID prostředku.

### <a name="create-a-custom-resource"></a>Vytvoření vlastního prostředku

Příchozí požadavek na rozhraní AZURE API:

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

Tento požadavek pak bude předán **ke koncovému bodu** ve formuláři:

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

Podobně odpověď z **koncového bodu** je pak předána zpět zákazníkovi. Odpověď z koncového bodu by měla vrátit:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny pod horní objekt.
- Záhlaví `Content-Type` by měla být nastavena na "application/json; charset=utf-8".

**Koncový bod** Reakce:

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

Odpověď na vlastní zprostředkovatele prostředků Azure:

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

### <a name="remove-a-custom-resource"></a>Odebrání vlastního prostředku

Příchozí požadavek na rozhraní AZURE API:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tento požadavek pak bude předán **ke koncovému bodu** ve formuláři:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobně odpověď z **koncového bodu** je pak předána zpět zákazníkovi. Odpověď z koncového bodu by měla vrátit:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny pod horní objekt.
- Záhlaví `Content-Type` by měla být nastavena na "application/json; charset=utf-8".

**Koncový bod** Reakce:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Odpověď na vlastní zprostředkovatele prostředků Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Načtení vlastního prostředku

Příchozí požadavek na rozhraní AZURE API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tento požadavek pak bude předán **ke koncovému bodu** ve formuláři:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobně odpověď z **koncového bodu** je pak předána zpět zákazníkovi. Odpověď z koncového bodu by měla vrátit:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny pod horní objekt.
- Záhlaví `Content-Type` by měla být nastavena na "application/json; charset=utf-8".

**Koncový bod** Reakce:

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

Odpověď na vlastní zprostředkovatele prostředků Azure:

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

### <a name="enumerate-all-custom-resources"></a>Výčet všech vlastních prostředků

Příchozí požadavek na rozhraní AZURE API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tento požadavek pak bude předán **ke koncovému bodu** ve formuláři:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Podobně odpověď z **koncového bodu** je pak předána zpět zákazníkovi. Odpověď z koncového bodu by měla vrátit:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny pod horní objekt.
- Záhlaví `Content-Type` by měla být nastavena na "application/json; charset=utf-8".
- Seznam prostředků by měl být umístěn `value` pod vlastnost í nejvyšší úrovně.

**Koncový bod** Reakce:

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

Odpověď na vlastní zprostředkovatele prostředků Azure:

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

- [Přehled o poskytovatelích vlastních prostředků Azure](overview.md)
- [Úvodní příručka: Vytvoření vlastního zprostředkovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: Vytváření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Postup: Přidání vlastních akcí do rozhraní API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Odkaz: Odkaz na vlastní mezipaměť prostředků](proxy-cache-resource-endpoint-reference.md)
