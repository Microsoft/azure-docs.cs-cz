---
title: Přidání vlastních akcí do Azure REST API
description: Přečtěte si, jak přidat vlastní akce do Azure REST API. Tento článek vás provede požadavky a osvědčenými postupy pro koncové body, které chtějí implementovat vlastní akce.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650393"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Přidání vlastních akcí do Azure REST API

Tento článek prochází požadavky a osvědčenými postupy pro vytváření koncových bodů poskytovatele vlastních prostředků Azure, které implementují vlastní akce. Pokud nejste obeznámeni s vlastními poskytovateli prostředků Azure, přečtěte si [Přehled o vlastních poskytovatelích prostředků](overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Definování koncového bodu akce

**Koncový bod** je adresa URL, která odkazuje na službu, která implementuje základní smlouvu mezi IT a Azure. Koncový bod je definovaný ve vlastním poskytovateli prostředků a může být libovolná veřejně přístupná adresa URL. Následující ukázka má **akci** volanou `myCustomAction` implementací `endpointURL` .

Vzorový **ResourceProvider**:

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

## <a name="building-an-action-endpoint"></a>Sestavení koncového bodu akce

**Koncový bod** , který implementuje **akci** , musí zpracovat požadavek a odpověď na nové rozhraní API v Azure. Když se vytvoří vlastní poskytovatel prostředků s **akcí** , vygeneruje se v Azure nová sada rozhraní API. V takovém případě bude tato akce generovat nové rozhraní Azure Action API pro `POST` volání:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Příchozí požadavek rozhraní API Azure:

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

Tato žádost se pak přepošle na **koncový bod** ve formátu:

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

Podobně odpověď z **koncového bodu** se pak přepošle zpátky zákazníkovi. Odpověď z koncového bodu by měla vracet:

- Platný dokument objektu JSON. Všechna pole a řetězce by měly být vnořeny do objektu nejvyšší úrovně.
- `Content-Type`Hlavička by měla být nastavená na "Application/JSON; charset = UTF-8.

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

Odpověď zprostředkovatele vlastního prostředku Azure:

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

Existují dva hlavní způsoby volání vlastní akce od vlastního poskytovatele prostředků:

- Azure CLI
- Šablony Azure Resource Manageru

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

Parametr | Povinné | Popis
---|---|---
akce | *Ano* | Název akce definované v **ResourceProvider**.
identifikační | *Ano* | ID prostředku **ResourceProvider**.
tělo žádosti | *Ne* | Text žádosti, který se odešle do **koncového bodu**.

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

> [!NOTE]
> Akce mají v šablonách Azure Resource Manager omezenou podporu. Aby byla akce volána uvnitř šablony, musí obsahovat [`list`](../templates/template-functions-resource.md#list) předponu v názvu.

Ukázka **ResourceProvider** s akcí list:

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

Ukázka šablony Azure Resource Manager:

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

Parametr | Povinné | Popis
---|---|---
resourceIdentifier | *Ano* | ID prostředku **ResourceProvider**.
apiVersion | *Ano* | Verze rozhraní API modulu runtime prostředků Tato možnost by měla být vždy "2018-09-01-Preview".
functionValues | *Ne* | Text žádosti, který se odešle do **koncového bodu**.

## <a name="next-steps"></a>Další kroky

- [Přehled zprostředkovatelů vlastních prostředků Azure](overview.md)
- [Rychlý Start: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: vytvoření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Postupy: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
