---
title: Přidání vlastních prostředků do rozhraní AZURE REST API
description: Zjistěte, jak přidat vlastní prostředky do rozhraní AZURE REST API. Tento článek bude procházet požadavky a osvědčené postupy pro koncové body, které chtějí implementovat vlastní prostředky.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650523"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Přidání vlastních prostředků do rozhraní AZURE REST API

Tento článek projde požadavky a osvědčené postupy pro vytváření koncových bodů Azure vlastní zprostředkovatele prostředků, který implementuje vlastní prostředky. Pokud nejste obeznámeni s Azure Vlastní zprostředkovatelé prostředků, podívejte se [na přehled o vlastní zprostředkovatelé prostředků](overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Jak definovat koncový bod prostředku

**Koncový bod** je adresa URL, která odkazuje na službu, která implementuje základní kontrakt mezi ní a Azure. Koncový bod je definován ve vlastním zprostředkovateli prostředků a může být libovolná veřejně přístupná adresa URL. Následující ukázka má **resourceType** `myCustomResource` `endpointURL`nazývá implementována .

Ukázkový **zprostředkovatel prostředků**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Vytváření koncového bodu prostředku

**Koncový bod,** který implementuje **resourceType** musí zpracovat požadavek a odpověď pro nové rozhraní API v Azure. Když je vytvořen vlastní poskytovatel prostředků s **resourceType,** vygeneruje novou sadu api v Azure. V takovém případě **resourceType** vygeneruje nové `PUT`rozhraní `GET`API `DELETE` prostředků Azure pro , a `GET` provádět CRUD na jeden prostředek, jakož i načíst všechny existující prostředky:

Manipulujte s`PUT`jedním `GET`zdrojem ( , a `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Načíst všechny`GET`prostředky ( ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Pro vlastní prostředky nabízejí vlastní zprostředkovatelé prostředků`Proxy`dva typy`Proxy, Cache` **směrováníTypy**: " " a " ".

### <a name="proxy-routing-type"></a>typ směrování proxy

"`Proxy`" **routingType** proxy všechny metody požadavku na **koncový bod** zadaný ve vlastním zprostředkovateli prostředků. Kdy použít`Proxy`" ":

- Je nutná plná kontrola nad odezvou.
- Integrace systémů se stávajícími zdroji.

Další informace o`Proxy`"" zdrojích naleznete [v odkazu na vlastní proxy prostředek](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>Typ směrování mezipaměti proxy

"`Proxy, Cache`" **routingType** proxy `PUT` servery pouze a `DELETE` požadovat metody koncového **bodu** zadaného ve vlastním zprostředkovateli prostředků. Vlastní zprostředkovatel prostředků automaticky `GET` vrátí požadavky na základě toho, co má uloženy v mezipaměti. Pokud je vlastní prostředek označen mezipamětí, poskytovatel vlastního prostředku také přidá nebo přepíše pole v odpovědi, aby rozhraní API Azure byla kompatibilní. Kdy použít`Proxy, Cache`" ":

- Vytvoření nového systému, který nemá žádné existující prostředky.
- Pracujte se stávajícím ekosystémem Azure.

Další informace o`Proxy, Cache`prostředcích naleznete [v odkazu na vlastní mezipaměť prostředků.](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Vytvoření vlastního prostředku

Existují dva hlavní způsoby vytvoření vlastního prostředku mimo vlastního poskytovatele prostředků:

- Azure CLI
- Šablony Azure Správce prostředků

### <a name="azure-cli"></a>Azure CLI

Vytvoření vlastního prostředku:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parametr | Požaduje se | Popis
---|---|---
je-full-objekt | *Ano* | Označuje, že objekt vlastností obsahuje další možnosti, jako je umístění, značky, sku nebo plán.
id | *Ano* | ID prostředku vlastního prostředku. To by mělo existovat mimo **ResourceProvider**
properties | *Ano* | Tělo požadavku, který bude odeslán do **koncového bodu**.

Odstranění vlastního prostředku Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Požaduje se | Popis
---|---|---
id | *Ano* | ID prostředku vlastního prostředku. To by mělo existovat mimo **ResourceProvider**.

Načíst vlastní prostředek Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Požaduje se | Popis
---|---|---
id | *Ano* | ID prostředku vlastního prostředku. To by mělo existovat mimo **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

> [!NOTE]
> Prostředky vyžadují, aby odpověď `id` `name`obsahovala `type` odpovídající , a z **koncového bodu**.

Šablony Azure Resource Manager `id` `name`vyžadují, `type` aby , a jsou vráceny správně z koncového bodu příjem dat. Vrácená odpověď prostředku by měla být ve formuláři:

Odpověď **cílového parametru vzorku:**

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Ukázková šablona Správce prostředků Azure:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parametr | Požaduje se | Popis
---|---|---
resourceTypeName | *Ano* | **Název** **resourceType** definované ve vlastním zprostředkovateli.
název_prostředku_prostředku_prostředku_prostředku | *Ano* | Název instance vlastního zprostředkovatele prostředků.
customResourceName | *Ano* | Vlastní název prostředku.

## <a name="next-steps"></a>Další kroky

- [Přehled o poskytovatelích vlastních prostředků Azure](overview.md)
- [Úvodní příručka: Vytvoření vlastního zprostředkovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: Vytváření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Postup: Přidání vlastních akcí do rozhraní API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Odkaz: Odkaz na vlastní proxy prostředek](proxy-resource-endpoint-reference.md)
- [Odkaz: Odkaz na vlastní mezipaměť prostředků](proxy-cache-resource-endpoint-reference.md)
