---
title: Přidání vlastních akcí do rozhraní AZURE REST API
description: Přečtěte si, jak přidat vlastní akce do rozhraní AZURE REST API. Tento článek bude procházet požadavky a osvědčené postupy pro koncové body, které chtějí implementovat vlastní akce.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650393"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Přidání vlastních akcí do rozhraní AZURE REST API

Tento článek projde požadavky a osvědčené postupy pro vytváření koncových bodů Azure vlastní zprostředkovatele prostředků, které implementují vlastní akce. Pokud nejste obeznámeni s Azure Vlastní zprostředkovatelé prostředků, podívejte se [na přehled o vlastní zprostředkovatelé prostředků](overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Jak definovat koncový bod akce

**Koncový bod** je adresa URL, která odkazuje na službu, která implementuje základní kontrakt mezi ní a Azure. Koncový bod je definován ve vlastním zprostředkovateli prostředků a může být libovolná veřejně přístupná adresa URL. Níže uvedený příklad má `myCustomAction` **akci,** kterou implementuje `endpointURL`aplikace .

Ukázkový **zprostředkovatel prostředků**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
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

## <a name="building-an-action-endpoint"></a>Vytvoření koncového bodu akce

**Koncový bod,** který implementuje **akci** musí zpracovat požadavek a odpověď pro nové rozhraní API v Azure. Když se vytvoří vlastní poskytovatel prostředků s **akcí,** vygeneruje novou sadu api v Azure. V takovém případě akce vygeneruje nové `POST` rozhraní API akce Azure pro volání:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Příchozí požadavek na rozhraní AZURE API:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Tento požadavek pak bude předán **ke koncovému bodu** ve formuláři:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Podobně odpověď z **koncového bodu** je pak předána zpět zákazníkovi. Odpověď z koncového bodu by měla vrátit:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny pod horní objekt.
- Záhlaví `Content-Type` by měla být nastavena na "application/json; charset=utf-8".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Odpověď na vlastní zprostředkovatele prostředků Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Volání vlastní akce

Existují dva hlavní způsoby volání vlastní akce mimo vlastního poskytovatele prostředků:

- Azure CLI
- Šablony Azure Správce prostředků

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parametr | Požaduje se | Popis
---|---|---
action | *Ano* | Název akce definované v **ResourceProvider**.
Id | *Ano* | ID prostředku **zprostředkovatele prostředků**.
orgán žádosti | *ne* | Tělo požadavku, který bude odeslán do **koncového bodu**.

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

> [!NOTE]
> Akce mají omezenou podporu v šablonách Azure Resource Manager. Aby byla akce volána uvnitř šablony, musí [`list`](../templates/template-functions-resource.md#list) obsahovat předponu v jejím názvu.

Ukázkový **zprostředkovatel prostředků** s akcí seznamu:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Ukázková šablona Správce prostředků Azure:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parametr | Požaduje se | Popis
---|---|---
resourceIdentifier | *Ano* | ID prostředku **zprostředkovatele prostředků**.
apiVersion | *Ano* | Verze rozhraní API za běhu prostředků. To by mělo být vždy "2018-09-01-preview".
functionValues | *ne* | Tělo požadavku, který bude odeslán do **koncového bodu**.

## <a name="next-steps"></a>Další kroky

- [Přehled o poskytovatelích vlastních prostředků Azure](overview.md)
- [Úvodní příručka: Vytvoření vlastního zprostředkovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: Vytváření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Postup: Přidání vlastních prostředků do rozhraní API Azure REST](./custom-providers-resources-endpoint-how-to.md)
