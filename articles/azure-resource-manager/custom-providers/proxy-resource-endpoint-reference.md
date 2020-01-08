---
title: Referenční informace k proxy vlastních prostředků
description: Odkaz na vlastní proxy prostředky pro poskytovatele vlastních prostředků Azure Tento článek prochází požadavky na koncové body, které implementují vlastní prostředky proxy serveru.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650458"
---
# <a name="custom-resource-proxy-reference"></a>Odkaz na vlastní proxy prostředky

Tento článek prochází požadavky na koncové body, které implementují vlastní prostředky proxy serveru. Pokud nejste obeznámeni s vlastními poskytovateli prostředků Azure, přečtěte si [Přehled o vlastních poskytovatelích prostředků](overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Definování koncového bodu prostředku proxy

Prostředek proxy serveru se dá vytvořit zadáním **routingType** na proxy serveru.

Ukázkový vlastní poskytovatel prostředků:

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

## <a name="building-proxy-resource-endpoint"></a>Vytváření koncového bodu prostředku proxy serveru

**Koncový bod** , který implementuje **koncový bod** prostředku proxy, musí zpracovat požadavek a odpověď na nové rozhraní API v Azure. V takovém případě **ResourceType** vygeneruje nové rozhraní Azure Resource API pro `PUT`, `GET`a `DELETE` k provedení CRUD na jednom prostředku a také `GET` k načtení všech existujících prostředků.

> [!NOTE]
> Pole `id`, `name`a `type` se nevyžadují, ale jsou potřeba k integraci vlastního prostředku se stávajícím ekosystémem Azure.

Ukázkový prostředek:

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
type | Microsoft. CustomProviders/resourceProviders/{ResourceType} | Obor názvů typu prostředku.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>Zprostředkovatelé/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | ID prostředku.

### <a name="create-a-custom-resource"></a>Vytvoření vlastního prostředku

Příchozí požadavek rozhraní API Azure:

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
- Záhlaví `Content-Type` musí být nastavené na "Application/JSON; charset = UTF-8.

**Koncový bod** Základě

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

Podobně odpověď z **koncového bodu** je pak zpětně postoupena zákazníkovi. Odpověď z koncového bodu by měla vracet:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny do objektu nejvyšší úrovně.
- Záhlaví `Content-Type` musí být nastavené na "Application/JSON; charset = UTF-8.

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

Tato žádost se pak přepošle na **koncový bod** ve formátu:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobně odpověď z **koncového bodu** se pak přepošle zpátky zákazníkovi. Odpověď z koncového bodu by měla vracet:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny do objektu nejvyšší úrovně.
- Záhlaví `Content-Type` musí být nastavené na "Application/JSON; charset = UTF-8.

**Koncový bod** Základě

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

Tato žádost se pak přepošle na **koncový bod** ve formátu:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Podobně odpověď z **koncového bodu** se pak přepošle zpátky zákazníkovi. Odpověď z koncového bodu by měla vracet:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny do objektu nejvyšší úrovně.
- Záhlaví `Content-Type` musí být nastavené na "Application/JSON; charset = UTF-8.
- Seznam prostředků by měl být umístěn pod vlastností `value` nejvyšší úrovně.

**Koncový bod** Základě

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
- [Referenční dokumentace: vlastní odkaz na mezipaměť prostředků](proxy-cache-resource-endpoint-reference.md)
