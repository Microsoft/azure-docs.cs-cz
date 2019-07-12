---
title: Přidání vlastních prostředků do rozhraní REST API služby Azure
description: Zjistěte, jak přidat vlastní prostředky rozhraní Azure REST API. V tomto článku najdete pomocí požadavky a osvědčené postupy pro koncové body, které chcete implementovat vlastní prostředky.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: a3cd1fe69a0d99f9faf3a451f76a3a420d713711
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795215"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Přidání vlastních prostředků do Azure REST API

Tento článek bude projít požadavky a osvědčené postupy pro vytváření koncových bodů vlastního poskytovatele prostředků Azure, které implementují vlastní prostředky. Pokud nejste obeznámeni s vlastní poskytovatele prostředků Azure, přečtěte si téma [přehled o poskytovatelích vlastní prostředek](./custom-providers-overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Jak definovat koncový bod prostředku

**Koncový bod** je adresa URL, která odkazuje na službu, která implementuje základní kontrakt mezi ním a Azure. Koncový bod je definována v poskytovateli vlastních prostředků a může být libovolná adresu URL veřejně přístupná. Následující ukázka je **resourceType** volá `myCustomResource` implementované `endpointURL`.

Ukázka **ResourceProvider**:

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

## <a name="building-a-resource-endpoint"></a>Vytváření koncový bod prostředku

**Koncový bod** , který implementuje **resourceType** musí zpracovat žádost a odpověď pro nové rozhraní API v Azure. Pokud zprostředkovatel vlastních prostředků s **resourceType** je vytvořen, vygeneruje se nová sada rozhraní API v Azure. V takovém případě **resourceType** vygeneruje nový Azure prostředku rozhraní API pro `PUT`, `GET`, a `DELETE` provádět CRUD u jednoho prostředku stejně jako `GET` k načtení všech existujících prostředků:

Manipulace s jeden prostředek (`PUT`, `GET`, a `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Načíst všechny prostředky (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Pro vlastní prostředky, vlastní prostředek poskytovatelé nabízí dva typy **routingTypes**: "`Proxy`"a"`Proxy, Cache`".

### <a name="proxy-routing-type"></a>Typ směrování proxy

"`Proxy`" **RoutingType** všechna žádosti metody pro proxy servery **koncový bod** zadán v poskytovateli vlastních prostředků. Kdy použít "`Proxy`":

- Je potřeba plnou kontrolu nad odpovědi.
- Integrace se stávajícími prostředky systémy.

Další informace o "`Proxy`" prostředky, najdete v článku [proxy odkaz na vlastní prostředek](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>Typ směrování mezipaměti proxy serveru

"`Proxy, Cache`" **RoutingType** proxy pouze `PUT` a `DELETE` žádosti metody **koncový bod** zadán v poskytovateli vlastních prostředků. Zprostředkovatel vlastní prostředek bude automaticky vrátit `GET` požadavky podle co jsou uloženy v mezipaměti. Pokud vlastní prostředek je označená pomocí mezipaměti, vlastní prostředek zprostředkovatele také přidat nebo přepsat pole v reakci na přizpůsobí rozhraní API Azure. Kdy použít "`Proxy, Cache`":

- Vytvoření nového systému, který nemá žádné existující prostředky.
- Práce s existující ekosystému Azure.

Další informace o "`Proxy, Cache`" prostředky, najdete v článku [mezipaměti odkaz na vlastní prostředek](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Vytváření vlastních prostředků

Vytváření vlastních prostředků z vlastních prostředků poskytovatele dvěma způsoby:

- Azure CLI
- Šablony Azure Resource Manageru

### <a name="azure-cli"></a>Azure CLI

Vytvořte vlastní prostředek:

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

Parametr | Požadováno | Popis
---|---|---
je full-object | *Ano* | Značí, že objekt vlastností obsahuje další možnosti, jako je například umístění, značky, sku nebo plán.
id | *Ano* | ID prostředku vlastního prostředku. To by měla existovat odhlásit z **ResourceProvider**
properties | *Ano* | Tělo požadavku, který se pošle **koncový bod**.

Odstraníte vlastní prostředek Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Požadováno | Popis
---|---|---
id | *Ano* | ID prostředku vlastního prostředku. To by měla existovat odhlásit z **ResourceProvider**.

Načtěte vlastní prostředek Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Požadováno | Popis
---|---|---
id | *Ano* | ID prostředku vlastního prostředku. To by měla existovat odhlásit z **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

> [!NOTE]
> Prostředky vyžadují, obsahují odpovídající odpověď `id`, `name`, a `type` z **koncový bod**.

Šablony Azure Resource Manageru vyžaduje, že `id`, `name`, a `type` jsou vráceny správně z podřízené koncového bodu. Odpověď vrácená prostředku by měl být ve tvaru:

Ukázka **koncový bod** odpovědi:

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

Ukázkové šablony Azure Resource Manageru:

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

Parametr | Požadováno | Popis
---|---|---
resourceTypeName | *Ano* | **Název** z **resourceType** podle vlastního zprostředkovatele.
resourceProviderName | *Ano* | Název instance zprostředkovatele vlastních prostředků.
customResourceName | *Ano* | Název vlastního prostředku.

## <a name="next-steps"></a>Další postup

- [Přehled poskytovatelů vlastního prostředku Azure](./custom-providers-overview.md)
- [Rychlé zprovoznění: Vytvoření vlastního poskytovatele prostředků Azure a nasazení vlastních prostředků](./create-custom-provider.md)
- [Kurz: Vytvoření vlastní akce a prostředků v Azure](./tutorial-custom-providers-101.md)
- [Postup: Přidání vlastní akce k rozhraní Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referenční dokumentace: Odkaz na vlastní prostředek Proxy](./custom-providers-proxy-resource-endpoint-reference.md)
- [Referenční dokumentace: Odkaz na vlastní prostředek mezipaměti](./custom-providers-proxy-cache-resource-endpoint-reference.md)
