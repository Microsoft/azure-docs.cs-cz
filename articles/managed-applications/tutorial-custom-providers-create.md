---
title: Vytvoření a použití vlastního zprostředkovatele
description: V tomto kurzu se naučíte, jak vytvořit a používat vlastního poskytovatele.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 053cf9fca03bf58cf10c313ae2569ce1918a46b9
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172885"
---
# <a name="create-and-use-a-custom-provider"></a>Vytvoření a použití vlastního zprostředkovatele

Vlastní poskytovatel je kontrakt mezi Azure a koncovým bodem. S vlastními poskytovateli můžete měnit pracovní postupy v Azure. V tomto kurzu se dozvíte, jak vytvořit vlastního poskytovatele. Pokud nejste obeznámeni se službou Azure Custom Providers, přečtěte si téma [Přehled poskytovatelů vlastních prostředků Azure](./custom-providers-overview.md).

## <a name="create-a-custom-provider"></a>Vytvoření vlastního zprostředkovatele

> [!NOTE]
> V tomto kurzu se nezobrazuje, jak vytvořit koncový bod. Pokud nemáte koncový bod RESTFUL, postupujte podle [kurzu vytváření koncových bodů RESTFUL](./tutorial-custom-providers-function-authoring.md), což je základem pro aktuální kurz.

Po vytvoření koncového bodu můžete vytvořit vlastního poskytovatele, který vygeneruje kontrakt mezi poskytovatelem a koncovým bodem. Pomocí vlastního poskytovatele můžete zadat seznam definic koncových bodů:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Vlastnost | Požadováno | Popis
---|---|---
**name** | Ano | Název definice koncového bodu. Azure zpřístupňuje tento název prostřednictvím rozhraní API pod/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | Ne | Typ kontraktu koncového bodu. Pokud hodnota není zadaná, použije se výchozí hodnota "proxy".
**Služba** | Ano | Koncový bod, do kterého mají být směrovány požadavky. Tento koncový bod zpracovává odpověď a všechny vedlejší účinky žádosti.

Hodnota **koncového bodu** je aktivační adresa URL aplikace funkce Azure Functions. Zástupné symboly `<functionkey>` `<funcname>`, a musí být nahrazeny hodnotami pro vaši vytvořenou aplikaci Function App. `<yourapp>`

## <a name="define-custom-actions-and-resources"></a>Definování vlastních akcí a prostředků

Vlastní zprostředkovatel obsahuje seznam definic koncových bodů, které jsou napsány v rámci vlastností **Actions** a **ResourceType** . Vlastnost **Actions (akce** ) se mapuje na vlastní akce vystavené vlastním poskytovatelem **a vlastnost** Resources je vlastní prostředky. V tomto kurzu má vlastní zprostředkovatel vlastnost **Actions** s názvem `myCustomAction` a vlastnost **resourcetypes** s názvem `myCustomResources`.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>Nasazení vlastního zprostředkovatele

> [!NOTE]
> Hodnoty **koncového bodu** je potřeba nahradit adresou URL triggeru z aplikace Function App vytvořené v předchozím kurzu.

Předchozí vlastního zprostředkovatele můžete nasadit pomocí šablony Azure Resource Manager:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>Použití vlastních akcí a prostředků

Po vytvoření vlastního poskytovatele můžete použít nová rozhraní API Azure. Následující karty vysvětlují, jak zavolat a použít vlastního poskytovatele.

### <a name="custom-actions"></a>Vlastní akce

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Zástupné symboly `{subscriptionId}` a `{resourceGroupName}` je nutné nahradit předplatným a skupinou prostředků, kde jste nasadili vlastního zprostředkovatele.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parametr | Požadováno | Popis
---|---|---
*kroky* | Ano | Název akce definované ve vlastním zprostředkovateli
*identifikační* | Ano | ID prostředku vlastního zprostředkovatele
*tělo žádosti* | Ne | Text žádosti, který se odešle do koncového bodu

# <a name="templatetabtemplate"></a>[Šablona](#tab/template)

Žádné.

---

### <a name="custom-resources"></a>Vlastní prostředky

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Zástupné symboly `{subscriptionId}` a `{resourceGroupName}` je nutné nahradit předplatným a skupinou prostředků, kde jste nasadili vlastního zprostředkovatele.

#### <a name="create-a-custom-resource"></a>Vytvoření vlastního prostředku

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parametr | Požadováno | Popis
---|---|---
*je-full-Object* | Ano | Určuje, zda objekt Properties obsahuje další možnosti, jako je umístění, značky, SKU nebo plán.
*id* | Ano | ID prostředku vlastního prostředku Toto ID je rozšířením ID prostředku vlastního poskytovatele.
*Vlastnosti* | Ano | Text žádosti, který se odešle do koncového bodu.

#### <a name="delete-a-custom-resource"></a>Odstranění vlastního prostředku

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Požadováno | Popis
---|---|---
*id* | Ano | ID prostředku vlastního prostředku Toto ID je rozšířením ID prostředku vlastního poskytovatele.

#### <a name="retrieve-a-custom-resource"></a>Načtení vlastního prostředku

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Požadováno | Popis
---|---|---
*id* | Ano | ID prostředku vlastního prostředku Toto ID je rozšířením ID prostředku vlastního poskytovatele.

# <a name="templatetabtemplate"></a>[Šablona](#tab/template)

Vzorová šablona Správce prostředků:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parametr | Požadováno | Popis
---|---|---
*Typ prostředku* | Ano | Hodnota vlastnosti resourcetypes definovaná ve vlastním zprostředkovateli. `name`
*resourceProviderName* | Ano | Název instance vlastního zprostředkovatele.
*customResourceName* | Ano | Název vlastního prostředku

---

> [!NOTE]
> Až dokončíte nasazení a používání vlastního poskytovatele, nezapomeňte vyčistit všechny vytvořené prostředky, včetně aplikace Azure Function App.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vlastních poskytovatelích. Další informace naleznete v tématu:

- [Postup: Přidání vlastních akcí do Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
