---
title: Azure části šablony Resource Manageru | Dokumentace Microsoftu
description: Popisuje části parametrů šablony Azure Resource Manageru pomocí deklarativní syntaxe JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomfitz
ms.openlocfilehash: 83ba1b94413990c0eb8dff42c49d46456a658d5a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417765"
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
| Hodnota minValue |Ne |Minimální hodnota pro parametry typu int, tato hodnota je také zahrnuto. |
| Hodnota maxValue |Ne |Maximální hodnoty pro parametry typu int, tato hodnota je také zahrnuto. |
| minLength |Ne |Minimální délka string, securestring a parametry typu pole, tato hodnota je také zahrnuto. |
| maxLength |Ne |Maximální délka řetězce, securestring a parametry typu pole, tato hodnota je také zahrnuto. |
| description |Ne |Popis parametru, který se zobrazí uživatelům na portálu. |

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

## <a name="recommendations"></a>Doporučení
Při práci s parametry, může být užitečné následující informace:

* Minimalizace používání parametrů. Kdykoli je to možné, použijte proměnnou nebo literálovou hodnotou. Použití parametrů pouze pro tyto scénáře:
   
   * Nastavení, které chcete použít variace podle prostředí (SKU, velikost, kapacita).
   * Názvy prostředků, které se mají určit pro snadnější identifikaci.
   * Hodnoty, které často používáte k provedení dalších úloh (jako je například uživatelské jméno správce).
   * Tajné kódy (například hesla).
   * Číslo nebo pole hodnot, který má použít při vytváření více než jednu instanci typu prostředku.
* Použijte formát camelCase pro názvy parametrů.
* Zadejte popis každý parametr v metadatech:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Definujte výchozí hodnoty pro parametry (s výjimkou hesla a klíčů SSH). Zadáním výchozí hodnoty parametru volitelně během nasazení. Výchozí hodnota může být prázdný řetězec. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Použití **securestring** pro všechny tajné kódy a hesla. Pokud předáte citlivá data v objektu JSON, použijte **secureObject** typu. Parametry šablony s securestring nebo secureObject typy nelze přečíst po nasazení prostředků. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Parametr použít k určení umístění a sdílet tuto hodnotu parametru co největší míře s prostředky, které by mohly být ve stejném umístění. Tento přístup minimalizuje počet pokusů, které se uživatelům zobrazí výzva k zadání umístění informací. Pokud typ prostředku se podporuje jenom pro omezený počet míst, můžete chtít zadejte platné umístění přímo v šabloně, nebo přidat další umístění parametr. Když organizace omezí povolených oblastí pro své uživatele **resourceGroup () .location** výraz mohou uživatelům bránit v nasazení šablony. Například jeden uživatel vytvoří skupinu prostředků v oblasti. Druhý uživatel musí nasadit do této skupiny prostředků, ale nemá přístup k oblasti. 
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[parameters('location')]",
         ...
     }
   ]
   ```
    
* Nepoužívejte parametr nebo proměnná pro verzi rozhraní API pro typ prostředku. Vlastnosti prostředku a hodnoty se můžou lišit podle čísla verze. Technologie IntelliSense v editoru kódu nelze určit správné schéma, pokud verze rozhraní API je nastavena na parametr nebo proměnná. Místo toho verzi pevně zakódovat rozhraní API v šabloně.
* Vyhněte se zadání názvu parametru v šabloně, která odpovídá parametru v příkazu pro nasazení. Resource Manager řeší tento konflikt názvů tak, že přidáte Příponové **FromTemplate** parametru šablony. Například pokud zahrnete parametr s názvem **ResourceGroupName** v šabloně, je v konfliktu s **ResourceGroupName** parametr [New-AzureRmResourceGroupDeployment ](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) rutiny. Během nasazení, budete vyzváni k zadání hodnoty pro **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Příklad šablony

Tyto šablony příklad ukazují některé scénáře použití parametrů. Nasazení, je otestovat zpracování parametrů v různých scénářích.

|Šablona  |Popis  |
|---------|---------|
|[parametry s využitím functions pro výchozí hodnoty](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Popisuje způsob použití funkce šablon, při definování výchozí hodnoty pro parametry. Šablona nenasadí žádné prostředky. Konstrukce hodnoty parametrů a vrátí tyto hodnoty. |
|[Parametr objektu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Ukazuje použití objektu pro parametr. Šablona nenasadí žádné prostředky. Konstrukce hodnoty parametrů a vrátí tyto hodnoty. |

## <a name="next-steps"></a>Další postup

* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Pro zadání hodnot parametrů během nasazování, naleznete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md). 
* Podrobnosti o funkce, které můžete použít z v rámci šablony najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md).
* Informace o používání parametr objektu najdete v tématu [použití objektu jako parametr v šabloně Azure Resource Manageru](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).
