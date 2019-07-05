---
title: Přidání vlastní akce k rozhraní Azure REST API
description: Zjistěte, jak přidat vlastní akce k rozhraní Azure REST API. V tomto článku najdete pomocí požadavky a osvědčené postupy pro koncové body, které chcete implementovat vlastní akce.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 1bfc0be81d42e922c47755543fb65aa413ec73a9
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478755"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Přidání vlastní akce k rozhraní Azure REST API

Tento článek bude projít požadavky a osvědčené postupy pro vytváření koncových bodů vlastního poskytovatele prostředků Azure, které implementují vlastní akce. Pokud nejste obeznámeni s vlastní poskytovatele prostředků Azure, přečtěte si téma [přehled o poskytovatelích vlastní prostředek](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Jak definovat koncový bod akce

**Koncový bod** je adresa URL, která odkazuje na službu, která implementuje základní kontrakt mezi ním a Azure. Koncový bod je definována v poskytovateli vlastních prostředků a může být libovolná adresu URL veřejně přístupná. Následující ukázka je **akce** volá `myCustomAction` implementované `endpointURL`.

Ukázka **ResourceProvider**:

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

## <a name="building-an-action-endpoint"></a>Vytváření koncového bodu akce

**Koncový bod** , který implementuje **akce** musí zpracovat žádost a odpověď pro nové rozhraní API v Azure. Pokud zprostředkovatel vlastních prostředků s **akce** je vytvořen, vygeneruje se nová sada rozhraní API v Azure. V takovém případě akce vygeneruje nový Azure akce rozhraní API pro `POST` volání:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API příchozí žádosti:

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

Tento požadavek bude poté předávat **koncový bod** ve tvaru:

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

Podobně, odpověď **koncový bod** je pak předán zpět na zákazníka. By měla vrátit odpověď od koncového bodu:

- Platný dokument objekt JSON. Všechny řetězce a pole by se vnoří pod objektu nejvyšší úrovně.
- `Content-Type` Záhlaví musí být nastavená na "application/json; charset = utf-8 ".

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

Odpověď zprostředkovatele vlastních prostředků Azure:

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

Existují dva hlavní způsoby volání vlastní akce ze zprostředkovatele vlastního prostředku:

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

Parametr | Požaduje se | Popis
---|---|---
action | *Ano* | Název akce definované v **ResourceProvider**.
ids | *Ano* | ID prostředku **ResourceProvider**.
text žádosti | *no* | Tělo požadavku, který se pošle **koncový bod**.

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

> [!NOTE]
> Akce mají omezenou podporu v šablonách Azure Resource Manageru. Aby akce, která má být volána v rámci šablony, musí se skládat [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) předpony v názvu.

Ukázka **ResourceProvider** seznamu akcí:

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

Ukázkové šablony Azure Resource Manageru:

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
resourceIdentifier | *Ano* | ID prostředku **ResourceProvider**.
apiVersion | *Ano* | Verze rozhraní API modulu runtime prostředků. To by měla být vždy "2018-09-01-preview".
functionValues | *no* | Tělo požadavku, který se pošle **koncový bod**.

## <a name="next-steps"></a>Další postup

- [Přehled poskytovatelů vlastního prostředku Azure](./custom-providers-overview.md)
- [Kurz: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Postup: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
