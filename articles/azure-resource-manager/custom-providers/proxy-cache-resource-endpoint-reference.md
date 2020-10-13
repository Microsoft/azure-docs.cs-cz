---
title: Referenční informace k mezipaměti vlastních prostředků
description: Odkaz na vlastní mezipaměť prostředků pro poskytovatele vlastních prostředků Azure Tento článek prochází požadavky na koncové body, které implementují vlastní prostředky mezipaměti.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75650380"
---
# <a name="custom-resource-cache-reference"></a>Vlastní odkaz na mezipaměť prostředků

Tento článek prochází požadavky na koncové body, které implementují vlastní prostředky mezipaměti. Pokud nejste obeznámeni s vlastními poskytovateli prostředků Azure, přečtěte si [Přehled o vlastních poskytovatelích prostředků](overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Definování koncového bodu prostředku mezipaměti

Prostředek proxy serveru se dá vytvořit zadáním **routingType** na proxy, cache.

Ukázkový vlastní poskytovatel prostředků:

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

## <a name="building-proxy-resource-endpoint"></a>Vytváření koncového bodu prostředku proxy serveru

**Koncový** bod, který implementuje "proxy server, mezipaměť" **, musí zpracovat** požadavek a odpověď na nové rozhraní API v Azure. V takovém případě **ResourceType** vygeneruje nové rozhraní API prostředků Azure pro `PUT` , `GET` a `DELETE` k provedení CRUD na jednom prostředku a také `GET` k načtení všech existujících prostředků:

> [!NOTE]
> Rozhraní API Azure vygeneruje metody požadavku `PUT` , `GET` a `DELETE` , ale **koncový bod** mezipaměti potřebuje zpracovat `PUT` a `DELETE` .
> Doporučujeme, aby **koncový bod** taky implementoval `GET` .

### <a name="create-a-custom-resource"></a>Vytvoření vlastního prostředku

Příchozí požadavek rozhraní API Azure:

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

Tato žádost se pak přepošle na **koncový bod** ve formátu:

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

Podobně odpověď z **koncového bodu** se pak přepošle zpátky zákazníkovi. Odpověď z koncového bodu by měla vracet:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny do objektu nejvyšší úrovně.
- `Content-Type`Hlavička by měla být nastavená na "Application/JSON; charset = UTF-8.
- Vlastní poskytovatel prostředků přepíše `name` `type` pole, a `id` pro požadavek.
- Vlastní poskytovatel prostředků vrátí pouze pole v rámci `properties` objektu pro koncový bod mezipaměti.

**Koncový bod** Základě

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

`name`Pole, `id` a `type` budou automaticky vygenerována pro vlastní prostředek vlastním poskytovatelem prostředků.

Odpověď zprostředkovatele vlastního prostředku Azure:

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

Příchozí požadavek rozhraní API Azure:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tato žádost se pak přepošle na **koncový bod** ve formátu:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobně odpověď z **koncového bodu** se pak přepošle zpátky zákazníkovi. Odpověď z koncového bodu by měla vracet:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny do objektu nejvyšší úrovně.
- `Content-Type`Hlavička by měla být nastavená na "Application/JSON; charset = UTF-8.
- Poskytovatel vlastního prostředku Azure odstraní jenom položku ze své mezipaměti, pokud se vrátí odpověď na úrovni 200. I v případě, že prostředek neexistuje, **koncový bod** by měl vrátit 204.

**Koncový bod** Základě

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Odpověď zprostředkovatele vlastního prostředku Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Načtení vlastního prostředku

Příchozí požadavek rozhraní API Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Požadavek **se** nepřepošle na **koncový bod**.

Odpověď zprostředkovatele vlastního prostředku Azure:

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

### <a name="enumerate-all-custom-resources"></a>Zobrazení výčtu všech vlastních prostředků

Příchozí požadavek rozhraní API Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tato **žádost se** nepřepošle na **koncový bod**.

Odpověď zprostředkovatele vlastního prostředku Azure:

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

- [Přehled zprostředkovatelů vlastních prostředků Azure](overview.md)
- [Rychlý Start: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: vytvoření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Postupy: Přidání vlastních akcí do Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referenční dokumentace: odkaz na vlastní proxy prostředků](proxy-resource-endpoint-reference.md)
