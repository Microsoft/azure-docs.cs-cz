---
title: Vytvoření a použití vlastního zprostředkovatele
description: Tento kurz ukazuje, jak vytvořit a použít vlastního zprostředkovatele Azure. Ke změně pracovních postupů v Azure použijte vlastní zprostředkovatele.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 09df78955de6423244c2d8ec94e1e1c06ecab257
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650027"
---
# <a name="create-and-use-a-custom-provider"></a>Vytvoření a použití vlastního zprostředkovatele

Vlastní zprostředkovatel je smlouva mezi Azure a koncovýbod. S vlastními poskytovateli můžete změnit pracovní postupy v Azure. Tento kurz ukazuje proces vytváření vlastního zprostředkovatele. Pokud nejste obeznámeni s vlastními zprostředkovateli Azure, podívejte [se na přehled poskytovatelů vlastních prostředků Azure](overview.md).

## <a name="create-a-custom-provider"></a>Vytvoření vlastního zprostředkovatele

> [!NOTE]
> Tento kurz neukazuje, jak vytvořit koncový bod. Pokud nemáte klidný koncový bod, postupujte podle [kurzu o vytváření koncových bodů RESTful](./tutorial-custom-providers-function-authoring.md), což je základem pro aktuální kurz.

Po vytvoření koncového bodu můžete vytvořit vlastního zprostředkovatele pro generování smlouvy mezi zprostředkovatelem a koncovým bodem. Pomocí vlastního zprostředkovatele můžete zadat seznam definic koncových bodů:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Vlastnost | Požaduje se | Popis
---|---|---
**Jméno** | Ano | Název definice koncového bodu. Azure zveřejňuje tento název prostřednictvím svého rozhraní API pod /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**typ směrování** | Ne | Typ smlouvy koncového bodu. Pokud hodnota není zadána, je výchozí "Proxy".
**endpoint** | Ano | Koncový bod pro směrování požadavků. Tento koncový bod zpracovává odpověď a všechny vedlejší účinky požadavku.

Hodnota **koncového bodu** je aktivační adresa URL aplikace funkce Azure. `<yourapp>`Zástupné `<funcname>`symboly , a `<functionkey>` zástupné symboly musí být nahrazeny hodnotami pro vytvořenou aplikaci funkcí.

## <a name="define-custom-actions-and-resources"></a>Definování vlastních akcí a zdrojů

Vlastní zprostředkovatel obsahuje seznam definic koncových bodů modelovaných pod **vlastnostmi actions** a **resourceTypes.** Vlastnost **akce** se mapuje na vlastní akce vystavené vlastním zprostředkovatelem a vlastnost **resourceTypes** je vlastní prostředky. V tomto kurzu má vlastní **actions** zprostředkovatel `myCustomAction` vlastnost akce s `myCustomResources`názvem a vlastnost **resourceTypes** s názvem .

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
> Je nutné nahradit hodnoty **koncového bodu** aktivační adresu URL z aplikace funkce vytvořené v předchozím kurzu.

Předchozího vlastního zprostředkovatele můžete nasadit pomocí šablony Azure Resource Manageru:

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

## <a name="use-custom-actions-and-resources"></a>Použití vlastních akcí a zdrojů

Po vytvoření vlastního zprostředkovatele můžete použít nová azure api. Následující karty vysvětlují, jak volat a používat vlastního zprostředkovatele.

### <a name="custom-actions"></a>Vlastní akce

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Je nutné `{subscriptionId}` nahradit `{resourceGroupName}` zástupné symboly a odběr a skupiny prostředků, kde jste nasadili vlastního zprostředkovatele.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parametr | Požaduje se | Popis
---|---|---
*Akce* | Ano | Název akce definované ve vlastním zprostředkovateli
*Id* | Ano | ID prostředku vlastního zprostředkovatele
*orgán žádosti* | Ne | Tělo požadavku, který bude odeslán do koncového bodu

# <a name="template"></a>[Šablony](#tab/template)

Žádné.

---

### <a name="custom-resources"></a>Vlastní prostředky

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Je nutné `{subscriptionId}` nahradit `{resourceGroupName}` zástupné symboly a odběr a skupiny prostředků, kde jste nasadili vlastního zprostředkovatele.

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

Parametr | Požaduje se | Popis
---|---|---
*je-full-objekt* | Ano | Označuje, zda objekt vlastností obsahuje další možnosti, jako je umístění, značky, skladová položka nebo plán.
*Id* | Ano | ID prostředku vlastního prostředku. Toto ID je rozšíření mnoství id vlastního zprostředkovatele.
*Vlastnosti* | Ano | Tělo požadavku, který bude odeslán do koncového bodu.

#### <a name="delete-a-custom-resource"></a>Odstranění vlastního prostředku

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Požaduje se | Popis
---|---|---
*Id* | Ano | ID prostředku vlastního prostředku. Toto ID je rozšíření mnoství id vlastního zprostředkovatele.

#### <a name="retrieve-a-custom-resource"></a>Načtení vlastního prostředku

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Požaduje se | Popis
---|---|---
*Id* | Ano | ID prostředku vlastního prostředku. Toto ID je rozšíření mnoství id vlastního zprostředkovatele.

# <a name="template"></a>[Šablony](#tab/template)

Ukázková šablona Správce prostředků:

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

Parametr | Požaduje se | Popis
---|---|---
*resourceTypeName* | Ano | Hodnota `name` vlastnosti **resourceTypes** definované ve vlastním zprostředkovateli.
*název_prostředku_prostředku_prostředku_prostředku* | Ano | Název instance vlastního zprostředkovatele.
*customResourceName* | Ano | Vlastní název prostředku.

---

> [!NOTE]
> Po dokončení nasazení a používání vlastního zprostředkovatele nezapomeňte vyčistit všechny vytvořené prostředky, včetně aplikace funkce Azure.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vlastní zprostředkovatelé. Další informace naleznete v tématu:

- [Postup: Přidání vlastních akcí do rozhraní AZURE REST API](./custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do rozhraní AZURE REST API](./custom-providers-resources-endpoint-how-to.md)
