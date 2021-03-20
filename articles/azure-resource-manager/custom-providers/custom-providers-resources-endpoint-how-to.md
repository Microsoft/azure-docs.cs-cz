---
title: Přidání vlastních prostředků do Azure REST API
description: Přečtěte si, jak přidat vlastní prostředky do Azure REST API. Tento článek vás provede požadavky a osvědčenými postupy pro koncové body, které chtějí implementovat vlastní prostředky.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650523"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Přidání vlastních prostředků do Azure REST API

Tento článek prochází požadavky a osvědčenými postupy pro vytváření koncových bodů poskytovatele vlastních prostředků Azure, které implementují vlastní prostředky. Pokud nejste obeznámeni s vlastními poskytovateli prostředků Azure, přečtěte si [Přehled o vlastních poskytovatelích prostředků](overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Definování koncového bodu prostředku

**Koncový bod** je adresa URL, která odkazuje na službu, která implementuje základní smlouvu mezi IT a Azure. Koncový bod je definovaný ve vlastním poskytovateli prostředků a může být libovolná veřejně přístupná adresa URL. Následující ukázka má typ **ResourceType** , který se nazývá `myCustomResource` implementováno `endpointURL` .

Vzorový **ResourceProvider**:

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

## <a name="building-a-resource-endpoint"></a>Sestavení koncového bodu prostředku

**Koncový bod** , který implementuje **ResourceType** , musí zpracovat požadavek a odpověď na nové rozhraní API v Azure. Když **se vytvoří** vlastní poskytovatel prostředků s prostředky, vytvoří se v Azure nová sada rozhraní API. V takovém případě **ResourceType** vygeneruje nové rozhraní API prostředků Azure pro `PUT` , `GET` a `DELETE` k provedení CRUD na jednom prostředku a také `GET` k načtení všech existujících prostředků:

Manipulace s jedním prostředkem ( `PUT` , a `GET` `DELETE` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Načíst všechny prostředky ( `GET` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

U vlastních prostředků nabízí vlastní poskytovatelé prostředků dva typy **routingTypes**: " `Proxy` " a " `Proxy, Cache` ".

### <a name="proxy-routing-type"></a>typ směrování proxy

`Proxy` **RoutingType** proxy všechny metody žádosti na **koncový bod** zadaný ve vlastním poskytovateli prostředků. Kdy použít " `Proxy` ":

- Je potřeba mít plnou kontrolu nad odpovědí.
- Integrace systémů se stávajícími prostředky.

Další informace o `Proxy` prostředcích najdete v tématu [Reference k vlastnímu proxy prostředku](proxy-resource-endpoint-reference.md) .

### <a name="proxy-cache-routing-type"></a>typ směrování mezipaměti proxy

`Proxy, Cache`Pouze proxy servery **routingType** `PUT` a `DELETE` požadavky na **koncový bod** zadané ve vlastním poskytovateli prostředků. Vlastní poskytovatel prostředků automaticky vrátí `GET` žádosti na základě toho, co v mezipaměti ukládá. Pokud je vlastní prostředek označený jako mezipaměť, vlastní poskytovatel prostředků také přidá/přepíše pole v odpovědi, aby rozhraní API splňovala požadavky na Azure. Kdy použít " `Proxy, Cache` ":

- Vytvoření nového systému, který nemá žádné existující prostředky.
- Pracujte se stávajícím ekosystémem Azure.

Další informace o `Proxy, Cache` prostředcích najdete v referenčních informacích k [vlastní mezipaměti prostředků](proxy-cache-resource-endpoint-reference.md) .

## <a name="creating-a-custom-resource"></a>Vytvoření vlastního prostředku

Existují dva hlavní způsoby vytvoření vlastního prostředku z vlastního poskytovatele prostředků:

- Azure CLI
- Šablony Azure Resource Manageru

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

Parametr | Povinné | Popis
---|---|---
je-full-Object | *Ano* | Označuje, že objekt Properties obsahuje další možnosti, jako je umístění, značky, SKU a/nebo plán.
id | *Ano* | ID prostředku vlastního prostředku To by mělo být mimo **ResourceProvider**
properties | *Ano* | Text žádosti, který se odešle do **koncového bodu**.

Odstranění vlastního prostředku Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Povinné | Popis
---|---|---
id | *Ano* | ID prostředku vlastního prostředku To by mělo být mimo **ResourceProvider**.

Načtení vlastního prostředku Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Povinné | Popis
---|---|---
id | *Ano* | ID prostředku vlastního prostředku To by mělo být mimo **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

> [!NOTE]
> Prostředky vyžadují, aby odpověď obsahovala odpovídající `id` , `name` a `type` z **koncového bodu**.

Šablony Azure Resource Manager vyžadují, `id` aby `name` byly, a `type` správně vráceny z koncového bodu pro příjem dat. Vrácená odpověď prostředku by měla být ve tvaru:

Ukázková odpověď **koncového bodu** :

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

Ukázka šablony Azure Resource Manager:

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

Parametr | Povinné | Popis
---|---|---
Typ prostředku | *Ano* | **Název** elementu **ResourceType** definovaného ve vlastním zprostředkovateli.
resourceProviderName | *Ano* | Název instance vlastního poskytovatele prostředků.
customResourceName | *Ano* | Název vlastního prostředku

## <a name="next-steps"></a>Další kroky

- [Přehled zprostředkovatelů vlastních prostředků Azure](overview.md)
- [Rychlý Start: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: vytvoření vlastních akcí a prostředků v Azure](./tutorial-get-started-with-custom-providers.md)
- [Postupy: Přidání vlastních akcí do Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referenční dokumentace: odkaz na vlastní proxy prostředků](proxy-resource-endpoint-reference.md)
- [Referenční dokumentace: vlastní odkaz na mezipaměť prostředků](proxy-cache-resource-endpoint-reference.md)
