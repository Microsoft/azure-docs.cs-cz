---
title: Parametry v šablonách
description: Popisuje, jak definovat parametry v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122419"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parametry v šablonách Azure Resource Manageru

Tento článek popisuje, jak definovat a používat parametry v šabloně Azure Resource Manager. Zadáním různých hodnot pro parametry můžete znovu použít šablonu pro různá prostředí.

Správce prostředků řeší hodnoty parametrů před zahájením operací nasazení. Všude tam, kde je parametr použit v šabloně, Správce prostředků jej nahradí vyřešenou hodnotou.

## <a name="define-parameter"></a>Definovat parametr

Následující příklad ukazuje jednoduchou definici parametru. Definuje parametr s názvem **storageSKU**. Parametr je řetězcová hodnota a přijímá pouze hodnoty, které jsou platné pro jeho zamýšlené použití. Parametr používá výchozí hodnotu, pokud během nasazení není k dispozici žádná hodnota.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Použít parametr

V šabloně odkazujete na hodnotu parametru pomocí funkce [parameters.](template-functions-deployment.md#parameters) V následujícím příkladu se hodnota parametru používá k nastavení skladové položky pro účet úložiště.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Funkce šablon

Při zadávání výchozí hodnoty parametru můžete použít většinu funkcí šablony. K vytvoření výchozí hodnoty můžete použít jinou hodnotu parametru. Následující šablona ukazuje použití funkcí ve výchozí hodnotě. Pokud není pro web k dispozici žádný název, vytvoří jedinečnou hodnotu řetězce a připojí ji k **webu**. Pokud není k dispozici žádný název pro plán hostitele, přebírá hodnotu webu a připojí **-plan**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Nelze použít [referenční](template-functions-resource.md#reference) funkci nebo žádnou z funkcí [seznamu](template-functions-resource.md#list) v části parametry. Tyto funkce získat runtime stav prostředku a nelze spustit před nasazením při parametry jsou vyřešeny.

## <a name="objects-as-parameters"></a>Objekty jako parametry

Může být jednodušší uspořádat související hodnoty jejich předáním jako objekt. Tento přístup také snižuje počet parametrů v šabloně.

Následující příklad ukazuje parametr, který je objektem. Výchozí hodnota zobrazuje očekávané vlastnosti objektu.

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Vlastnosti objektu můžete odkazovat pomocí operátoru tečka.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>Ukázkové šablony

Následující příklady ukazují scénáře pro použití parametrů.

|Šablona  |Popis  |
|---------|---------|
|[parametry s funkcemi pro výchozí hodnoty](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Ukazuje, jak používat funkce šablony při definování výchozích hodnot pro parametry. Šablona nenasazuje žádné prostředky. Vytvoří hodnoty parametrů a vrátí tyto hodnoty. |
|[objekt parametru](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Ukazuje použití objektu pro parametr. Šablona nenasazuje žádné prostředky. Vytvoří hodnoty parametrů a vrátí tyto hodnoty. |


## <a name="next-steps"></a>Další kroky

* Informace o dostupných vlastnostech parametrů najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](template-syntax.md).
* Informace o předávání hodnot parametrů jako souboru naleznete v tématu [Vytvoření souboru parametrů Správce prostředků](parameter-files.md).
* Doporučení týkající se vytváření parametrů naleznete v tématu [Doporučené postupy – parametry](template-best-practices.md#parameters).
