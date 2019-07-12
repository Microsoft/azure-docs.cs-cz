---
title: Vytvářet a využívat Azure vlastního zprostředkovatele
description: V tomto kurzu se přenášejí prostřednictvím tom, jak vytvářet a využívat vlastního zprostředkovatele.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799998"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Vytváření koncový bod RESTful pro vlastní zprostředkovatelé

Vlastní zprostředkovatelé umožňují přizpůsobit pracovní postupy v Azure. Vlastní zprostředkovatel je kontrakt mezi Azure a `endpoint`. V tomto kurzu se projít procesem vytvoření vlastního zprostředkovatele. Pokud nejste obeznámeni s vlastní poskytovatele Azure, přečtěte si téma [přehled o poskytovatelích vlastní prostředek](./custom-providers-overview.md).

V tomto kurzu je rozdělen do následujících kroků:

- Co je vlastního zprostředkovatele
- Definování vlastních akcí a prostředky
- Nasazení vlastního zprostředkovatele

V tomto kurzu budete stavět v následujících kurzech:

- [Vytváření koncový bod RESTful pro vlastní zprostředkovatelé](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>Vytvoření vlastního zprostředkovatele

> [!NOTE]
> V tomto kurzu se přenášejí prostřednictvím vytváření koncového bodu. Postupujte prosím podle [kurz na vytváření koncové body RESTful](./tutorial-custom-providers-function-authoring.md) Pokud nemáte koncový bod RESTful.

Jednou `endpoint` je vytvořený, můžete vygenerovat vytvořit vlastního zprostředkovatele pro generování kontrakt mezi ho a `endpoint`. Vlastní zprostředkovatel umožňuje určit seznam definic koncových bodů.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Vlastnost | Požadováno | Popis
---|---|---
name | *Ano* | Název definice koncového bodu. Azure bude vystavovat tento název prostřednictvím jejího rozhraní API v části "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders / {název resourceProviderName} / {endpointDefinitionName}.
routingType | *no* | Určuje typ kontraktu s `endpoint`. Pokud není zadán, nastaví se jako výchozí "Proxy".
endpoint | *Ano* | Koncový bod pro směrování požadavků. To zpracuje odpověď, jakož i všechny vedlejší účinky požadavku.

V takovém případě `endpoint` je adresa URL triggeru funkce Azure functions. `<yourapp>`, `<funcname>`, A `<functionkey>` by měla být nahrazena hodnoty pro vytvořené funkce.

## <a name="defining-custom-actions-and-resources"></a>Definování vlastních akcí a prostředky

Obsahuje seznam definic koncových bodů modelována podle vlastního zprostředkovatele `actions` a `resourceTypes`. `actions` mapování vlastních akcí vystavený poskytovatelem vlastní, zatímco `resourceTypes` jsou vlastní prostředky. V tomto kurzu definujeme vlastní zprostředkovatel s `action` volá `myCustomAction` a `resourceType` volá `myCustomResources`.

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

Nahraďte `endpoint` s adresou URL aktivační události z funkce, které jste v předchozím kurzu vytvořili.

## <a name="deploying-the-custom-provider"></a>Nasazení vlastního zprostředkovatele

> [!NOTE]
> `endpoint` By měla být nahrazena adresu URL funkce.

Výše uvedené vlastního zprostředkovatele se dá nasadit pomocí šablony Azure Resource Manageru.

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

## <a name="using-custom-actions-and-resources"></a>Pomocí vlastních akcí a prostředky

Poté, co jsme vytvořili vlastní poskytovatele, můžeme využívat nová rozhraní API Azure. V další části se popisují, jak volat a využívat vlastního zprostředkovatele.

### <a name="custom-actions"></a>Vlastní akce

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` a `{resourceGroupName}` by měla být nahrazena dané předplatné a skupina prostředků, ve kterém byl nasazen vlastního zprostředkovatele.

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
Akce | *Ano* | Název akce definované ve vytvořené vlastního zprostředkovatele.
ids | *Ano* | ID prostředku vytvořeného vlastního zprostředkovatele.
text žádosti | *no* | Tělo požadavku, který se pošle `endpoint`.

# <a name="templatetabtemplate"></a>[Šablona](#tab/template)

Žádné.

---

### <a name="custom-resources"></a>Vlastní prostředky

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` a `{resourceGroupName}` by měla být nahrazena dané předplatné a skupina prostředků, ve kterém byl nasazen vlastního zprostředkovatele.

Vytvořte vlastní prostředek:

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
je full-object | *Ano* | Značí, že objekt vlastností obsahuje další možnosti, jako je například umístění, značky, sku nebo plán.
id | *Ano* | ID prostředku vlastního prostředku. To by měla existovat z vytvořeného vlastního zprostředkovatele.
properties | *Ano* | Tělo požadavku, který se pošle `endpoint`.

Odstraníte vlastní prostředek Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Požadováno | Popis
---|---|---
id | *Ano* | ID prostředku vlastního prostředku. To by měla existovat z vytvořeného vlastního zprostředkovatele.

Načtěte vlastní prostředek Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Požadováno | Popis
---|---|---
id | *Ano* | ID prostředku vlastního prostředku. To by měla existovat z vytvořeného vlastního zprostředkovatele.

# <a name="templatetabtemplate"></a>[Šablona](#tab/template)

Ukázkové šablony Azure Resource Manageru:

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
resourceTypeName | *Ano* | `name` z *resourceType* podle vlastního zprostředkovatele.
resourceProviderName | *Ano* | Název instance vlastního zprostředkovatele.
customResourceName | *Ano* | Název vlastního prostředku.

---

> [!NOTE]
> Po dokončení nasazování a používání vlastního zprostředkovatele, nezapomeňte vyčistit všechny vytvořené prostředky, včetně funkce Azure functions.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vlastní poskytovatele. Přejdete na další článek k vytvoření vlastního zprostředkovatele.

- [Postup: Přidání vlastní akce k rozhraní Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Postup: Přidání vlastních prostředků do Azure REST API](./custom-providers-resources-endpoint-how-to.md)
