---
title: Parametry v šablonách
description: Popisuje, jak definovat parametry v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76122419"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parametry v šablonách Azure Resource Manageru

Tento článek popisuje, jak definovat a používat parametry v šabloně Azure Resource Manager. Zadáním různých hodnot pro parametry můžete znovu použít šablonu pro různá prostředí.

Správce prostředků řeší hodnoty parametrů před spuštěním operací nasazení. Všude, kde je parametr použit v šabloně, Správce prostředků ho nahradí vyřešenou hodnotou.

## <a name="define-parameter"></a>Definovat parametr

V následujícím příkladu je uvedena definice jednoduchého parametru. Definuje parametr s názvem **storageSKU**. Parametr je hodnota řetězce a přijímá pouze hodnoty, které jsou platné pro zamýšlené použití. Parametr používá výchozí hodnotu, pokud není k dispozici žádná hodnota během nasazování.

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

V šabloně odkazujete na hodnotu parametru pomocí funkce [Parameters](template-functions-deployment.md#parameters) . V následujícím příkladu se hodnota parametru používá k nastavení SKU pro účet úložiště.

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

Když zadáte výchozí hodnotu pro parametr, můžete použít většinu funkcí šablon. K vytvoření výchozí hodnoty můžete použít jinou hodnotu parametru. Následující šablona ukazuje použití funkcí ve výchozí hodnotě. Není-li pro web k dispozici žádný název, vytvoří jedinečnou řetězcovou hodnotu a připojí ji k **webu**. Pokud není pro plán hostitele k dispozici žádný název, převezme se hodnota lokality a připojí se **plán**.

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

V části Parameters (parametry) nemůžete použít [odkazovou](template-functions-resource.md#reference) funkci ani žádnou z funkcí [seznamu](template-functions-resource.md#list) . Tyto funkce získávají běhový stav prostředku a nelze jej provést před nasazením, když jsou parametry vyřešeny.

## <a name="objects-as-parameters"></a>Objekty jako parametry

Můžete snadněji organizovat související hodnoty jejich předáním v podobě objektu. Tento přístup také snižuje počet parametrů v šabloně.

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

Odkazujete na vlastnosti objektu pomocí operátoru tečka.

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

## <a name="example-templates"></a>Příklady šablon

Následující příklady ukazují scénáře použití parametrů.

|Šablona  |Popis  |
|---------|---------|
|[parametry s funkcemi pro výchozí hodnoty](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Ukazuje, jak používat funkce šablon při definování výchozích hodnot pro parametry. Šablona neimplementuje žádné prostředky. Vytvoří hodnoty parametrů a vrátí tyto hodnoty. |
|[objekt parametru](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Ukazuje použití objektu pro parametr. Šablona neimplementuje žádné prostředky. Vytvoří hodnoty parametrů a vrátí tyto hodnoty. |


## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech parametrů naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
* Další informace o předávání hodnot parametrů jako souboru naleznete v tématu [Create správce prostředků Parameter File](parameter-files.md).
* Doporučení k vytváření parametrů najdete v tématu [osvědčené postupy – parametry](template-best-practices.md#parameters).
