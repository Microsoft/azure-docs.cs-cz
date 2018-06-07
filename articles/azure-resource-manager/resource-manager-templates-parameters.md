---
title: Azure oddíle parametr šablony Resource Manageru | Microsoft Docs
description: Popisuje části Parametry šablony Azure Resource Manager pomocí deklarativní syntaxe JSON.
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
ms.date: 05/18/2018
ms.author: tomfitz
ms.openlocfilehash: 6d09a057d9b8a02c7f8313161e64aa3a42eb6db2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604331"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Část parametry šablon Azure Resource Manager
V sekci parametrů šablony zadejte hodnoty, které můžete zadat při nasazování prostředky. Tyto hodnoty parametrů umožňují přizpůsobit nasazení zadáním hodnoty, které jsou přizpůsobené pro konkrétní prostředí (například vývoj, testování a provozním). Není nutné zadat parametry v šabloně, ale bez parametrů šablony vždy nasazení stejné prostředky se stejnými názvy, umístění a vlastnosti.

Jste omezená na 255 parametrů v šabloně. Pomocí objektů, které obsahují více vlastností, jak je znázorněno v tomto článku můžete snížit počet parametrů.

## <a name="define-and-use-a-parameter"></a>Definice a používání parametr

Následující příklad ukazuje definici jednoduchého parametr. Definuje název parametru a určuje, že trvá hodnotu řetězce. Parametr přijímá pouze hodnoty, které dávají smysl pro zamýšlený účel. Určuje výchozí hodnotu. Pokud je během nasazení zadána žádná hodnota. Nakonec parametr obsahuje popis jeho použití. 

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

V šabloně odkazujete hodnota parametru s následující syntaxí:

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

Předchozí příklad ukázal jenom některé z vlastností, které můžete použít v části parametru. Jsou dostupné vlastnosti:

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
| Výchozí hodnota |Ne |Výchozí hodnota pro parametr, pokud není zadána žádná hodnota pro parametr. |
| allowedValues |Ne |Povolené hodnoty pro parametr a ujistěte se, že je zadaná hodnota pravé pole. |
| MinValue |Ne |Minimální hodnota pro parametry typu int, tato hodnota je (včetně). |
| MaxValue |Ne |Maximální hodnota parametry typu int, tato hodnota je (včetně). |
| minLength |Ne |Minimální délka řetězce, securestring a parametry typu pole, tato hodnota je (včetně). |
| Hodnota maxLength |Ne |Maximální délka řetězce, securestring a parametry typu pole, tato hodnota je (včetně). |
| description |Ne |Popis parametru, který se zobrazí uživatelům prostřednictvím portálu. |

## <a name="template-functions-with-parameters"></a>Funkce šablony s parametry

Pokud poskytuje výchozí hodnotu pro parametr, můžete použít většinu funkce šablony. Jiná hodnota parametru můžete použít k vytvoření výchozí hodnotu. Následující šablony demonstruje použití funkce ve výchozí hodnota:

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

Nelze použít `reference` funkce v sekci parametrů. Parametry se vyhodnocují před nasazením proto `reference` funkce nelze získat stav modulu runtime prostředku. 

## <a name="objects-as-parameters"></a>Objektů jako parametry

Může být usnadňují uspořádání souvisejících hodnot pomocí nich předání jako objekt. Tento přístup také snižuje počet parametrů v šabloně.

V šabloně definujte parametr a během nasazení zadat objekt JSON místo jednu hodnotu. 

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

Pak odkazujete podvlastnosti parametru pomocí operátoru tečka.

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

* Minimalizujte využití parametrů. Pokud je to možné, použijte proměnnou nebo literálovou hodnotou. Použijte parametry jenom pro tyto scénáře:
   
   * Nastavení, které chcete použít variace podle prostředí (SKU, velikost, kapacity).
   * Názvy prostředků, které chcete určit pro snazší identifikaci.
   * Hodnoty, které používáte k provedení dalších úloh (například správce uživatelské jméno).
   * Tajné klíče (jako jsou hesla).
   * Číslo nebo pole hodnot, který má použít při vytváření více instancí typu prostředku.
* Použijte formát camelCase pro názvy parametrů.
* Zadejte popis všechny parametry v metadatech:

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

* Definujte výchozí hodnoty pro parametry (s výjimkou hesla a klíče SSH). Tím, že poskytuje výchozí hodnotu, bude parametr volitelný během nasazení. Výchozí hodnota může být prázdný řetězec. 
   
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

* Použití **securestring** pro všechny hesla a tajných klíčů. Pokud předáte citlivá data v objektu JSON, použít **secureObject** typu. Parametry šablony s securestring nebo secureObject typů nelze přečíst po nasazení prostředků. 
   
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

* Použijte parametr k určení umístění a sdílet tuto hodnotu parametru co nejvíce s prostředky, které by mohly být ve stejném umístění. Tento postup minimalizuje počet, který uživatelé vyzváni k zadání informace o umístění. Pokud typ prostředku je podporován pouze omezený počet umístění, můžete chtít zadejte platné umístění přímo v šabloně, nebo přidejte parametr jiné umístění. Když organizace omezuje povolených oblastí pro své uživatele **resourceGroup () .location** výraz může zabránit uživateli v schopnost nasazení šablony. Například jeden uživatel vytvoří skupinu prostředků v oblasti. Druhý uživatel musí nasadit do této skupiny prostředků, ale nemá přístup k oblasti. 
   
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
    
* Vyhněte se použití parametr nebo proměnná pro verze rozhraní API pro typ prostředku. Vlastnosti prostředku a hodnoty se může lišit podle čísla verze. IntelliSense v editoru kódu nemůže určit správné schéma verze rozhraní API je nastavena na parametr nebo proměnná. Místo toho pevný kódu rozhraní API verze v šabloně.
* Vyhněte se zadání názvu parametru v šabloně odpovídající parametr v příkazu pro nasazení. Správce prostředků řeší tento ke konfliktu názvů přidáním operátory **FromTemplate** pro parametr šablony. Například, pokud zahrnete parametr s názvem **ResourceGroupName** v šabloně, je v konfliktu s **ResourceGroupName** parametr ve [New-AzureRmResourceGroupDeployment ](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) rutiny. Během nasazení, budete vyzváni, zadejte hodnotu pro **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Příklad šablony

Tyto šablony příklad ukazují některé scénáře použití parametrů. Je k testování zpracování parametrů v různých scénářích nasazení.

|Šablona  |Popis  |
|---------|---------|
|[parametry mají funkce pro výchozí hodnoty](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstruje použití funkce šablon při definování výchozí hodnoty pro parametry. Šablony není nasazen žádné prostředky. To vytvoří hodnoty parametrů a vrátí tyto hodnoty. |
|[Parametr objekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Ukazuje, jak pomocí objektu pro parametr. Šablony není nasazen žádné prostředky. To vytvoří hodnoty parametrů a vrátí tyto hodnoty. |

## <a name="next-steps"></a>Další postup

* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Pro vstupní hodnoty parametrů během nasazování naleznete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md). 
* Podrobnosti o funkcích, které můžete použít z v rámci šablon najdete v tématu [funkce šablon Azure Resource Manager](resource-group-template-functions.md).
* Informace o používání objekt parametru najdete v tématu [použít jako parametr v šablonu Azure Resource Manager objekt](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).
