---
title: Azure části šablony Resource Manageru | Dokumentace Microsoftu
description: Popisuje části parametrů šablony Azure Resource Manageru pomocí deklarativní syntaxe JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2019
ms.author: tomfitz
ms.openlocfilehash: dc817302ab39d12ccd1d1a20d4dd72f94352c796
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695625"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Sekci parametrů šablony Azure Resource Manageru
V sekci parametrů šablony zadejte hodnoty, které můžete zadat při nasazování prostředků. Tyto hodnoty parametrů umožňují vlastní nastavení nasazení tím, že poskytuje hodnoty, které jsou přizpůsobené pro konkrétní prostředí (jako je vývoj, testování a produkce). Není nutné zadat parametry v šabloně, ale bez parametrů by vždy šablony nasadit stejným prostředkům se stejnými názvy, umístění a vlastnosti.

Jste omezeni na 256 parametrů v šabloně. Počet parametrů můžete omezit použitím objektů, které obsahují více vlastností, jak je znázorněno v tomto článku.

## <a name="define-and-use-a-parameter"></a>Definujte a použijte parametr.

Následující příklad ukazuje definicí jednoduchého parametru. Definuje název parametru a určuje, že přijímá řetězcovou hodnotu. Parametr přijímá pouze hodnoty, které dávají smysl pro zamýšlený účel. Pokud během nasazení se nezadá žádná hodnota určuje výchozí hodnotu. Nakonec tento parametr obsahuje popis jeho použití. 

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

V šabloně referenční hodnota parametru s následující syntaxí:

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

## <a name="available-properties"></a>Dostupné vlastnosti

Předchozí příklad ukázal jenom některé vlastnosti, které můžete v části. Dostupné vlastnosti jsou:

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| Název parametru |Ano |Název parametru. Musí být platný identifikátor jazyka JavaScript. |
| type |Ano |Typ hodnoty parametru. Povolené typy a hodnoty jsou **řetězec**, **securestring**, **int**, **bool**, **objekt**, **secureObject**, a **pole**. |
| Výchozí hodnota |Ne |Výchozí hodnota pro parametr, pokud se nezadá žádná hodnota pro parametr. |
| allowedValues |Ne |Povolené hodnoty pro parametr, abyste měli jistotu, že se zadal správný hodnotu pole. |
| minValue |Ne |Minimální hodnota pro parametry typu int, tato hodnota je také zahrnuto. |
| maxValue |Ne |Maximální hodnoty pro parametry typu int, tato hodnota je také zahrnuto. |
| minLength |Ne |Minimální délku řetězce, zabezpečený řetězec a parametry typu pole, tato hodnota je také zahrnuto. |
| maxLength |Ne |Maximální délka řetězce, zabezpečený řetězec a parametry typu pole, tato hodnota je také zahrnuto. |
| description |Ne |Popis parametru, který se zobrazí uživatelům na portálu. Další informace najdete v tématu [komentáře v šablonách](resource-group-authoring-templates.md#comments). |

## <a name="template-functions-with-parameters"></a>Šablony funkce s parametry

Při zadávání výchozí hodnota pro parametr, můžete použít většina funkcí šablony. Pro vytvoření výchozí hodnoty můžete použít jinou hodnotu parametru. Následující šablona ukazuje použití funkcí v výchozí hodnota:

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

Nelze použít `reference` funkce v sekci parametrů. Jsou parametry vyhodnoceny před nasazením proto `reference` funkce nelze získat běhový stav prostředku. 

## <a name="objects-as-parameters"></a>Objekty, které jako parametry

Může být jednodušší k uspořádání souvisejících hodnot v předáním jako objekt. Tento přístup také snižuje počet parametrů v šabloně.

Definujte parametr v šabloně a během nasazení, zadejte objekt JSON místo jednu hodnotu. 

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

Potom odkazujete objektu třídy subproperties parametru pomocí operátoru tečka.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
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

## <a name="example-templates"></a>Příklad šablony

Tyto šablony příklad ukazují některé scénáře použití parametrů. Nasazení, je otestovat zpracování parametrů v různých scénářích.

|Šablona  |Popis  |
|---------|---------|
|[parametry s využitím functions pro výchozí hodnoty](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Popisuje způsob použití funkce šablon, při definování výchozí hodnoty pro parametry. Šablona nenasadí žádné prostředky. Konstrukce hodnoty parametrů a vrátí tyto hodnoty. |
|[Parametr objektu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Ukazuje použití objektu pro parametr. Šablona nenasadí žádné prostředky. Konstrukce hodnoty parametrů a vrátí tyto hodnoty. |

## <a name="next-steps"></a>Další postup

* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Pro zadání hodnot parametrů během nasazování, naleznete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md). 
* Doporučení o vytváření šablon naleznete v tématu [osvědčené postupy pro šablony Azure Resource Manageru](template-best-practices.md).
* Informace o používání parametr objektu najdete v tématu [použití objektu jako parametr v šabloně Azure Resource Manageru](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).
