---
title: Referenční informace k mezipaměti vlastních prostředků
description: Odkaz na vlastní mezipaměť prostředků pro zprostředkovatele vlastních prostředků Azure. Tento článek projde požadavky na koncové body implementující vlastní prostředky mezipaměti.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650380"
---
# <a name="custom-resource-cache-reference"></a>Odkaz na vlastní mezipaměť prostředků

Tento článek projde požadavky na koncové body implementující vlastní prostředky mezipaměti. Pokud nejste obeznámeni s Azure Vlastní zprostředkovatelé prostředků, podívejte se [na přehled o vlastní zprostředkovatelé prostředků](overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Jak definovat koncový bod prostředku mezipaměti

Prostředek proxy lze vytvořit zadáním **routingType** na "Proxy, Cache".

Ukázka vlastního zprostředkovatele prostředků:

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

## <a name="building-proxy-resource-endpoint"></a>Vytváření koncového bodu prostředků proxy

**Koncový bod,** který implementuje koncový **bod** prostředku "Proxy, cache" musí zpracovat požadavek a odpověď pro nové rozhraní API v Azure. V takovém případě **resourceType** vygeneruje nové `PUT`rozhraní `GET`API `DELETE` prostředků Azure pro , a provádět `GET` CRUD na jeden prostředek, stejně jako načíst všechny existující prostředky:

> [!NOTE]
> Rozhraní Azure `PUT`API vygeneruje metody požadavku , `GET`a `DELETE`, ale `DELETE`koncový **bod** mezipaměti potřebuje jenom zpracování `PUT` a .
> Doporučujeme, aby **koncový** bod `GET`také implementuje .

### <a name="create-a-custom-resource"></a>Vytvoření vlastního prostředku

Příchozí požadavek na rozhraní AZURE API:

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
- Vlastní zprostředkovatel prostředků přepíše `name` `type`pole `id` , a pole pro požadavek.
- Vlastní zprostředkovatel prostředků vrátí pouze `properties` pole pod objektem pro koncový bod mezipaměti.

**Koncový bod** Reakce:

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

Pole `name` `id`, `type` a budou automaticky generována pro vlastní prostředek vlastním poskytovatelem prostředků.

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

Podobně ,odpověď z **koncového bodu** je pak předána zpět zákazníkovi. Odpověď z koncového bodu by měla vrátit:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny pod horní objekt.
- Záhlaví `Content-Type` by měla být nastavena na "application/json; charset=utf-8".
- Zprostředkovatel vlastních prostředků Azure odebere položku z mezipaměti pouze v případě, že je vrácena odpověď na úrovni 200. I v případě, že prostředek neexistuje, **koncový bod** by měl vrátit 204.

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

Požadavek **nebude** předán ke **koncovému bodu**.

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

Tento požadavek **nebude** předán ke **koncovému bodu**.

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
- [Odkaz: Odkaz na vlastní proxy prostředek](proxy-resource-endpoint-reference.md)
