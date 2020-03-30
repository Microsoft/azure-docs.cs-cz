---
title: Uživatelem definované funkce v šablonách
description: Popisuje, jak definovat a používat uživatelem definované funkce v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943225"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Uživatelem definované funkce v šabloně Azure Resource Manager

V šabloně můžete vytvářet vlastní funkce. Tyto funkce jsou k dispozici pro použití v šabloně. Uživatelem definované funkce jsou odděleny od [standardních funkcí šablony,](template-functions.md) které jsou automaticky k dispozici v rámci šablony. Vytvořte si vlastní funkce, pokud máte složité výrazy, které se opakovaně používají v šabloně.

Tento článek popisuje, jak přidat uživatelem definované funkce v šabloně Azure Resource Manager.

## <a name="define-the-function"></a>Definování funkce

Funkce vyžadují hodnotu oboru názvů, aby se zabránilo konfliktům názvů s funkcemi šablony. Následující příklad ukazuje funkci, která vrací jedinečný název:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Použití funkce

Následující příklad ukazuje šablonu, která obsahuje uživatelem definovanou funkci. Používá tuto funkci k získání jedinečného názvu pro účet úložiště. Šablona má parametr s názvem **storageNamePrefix,** který předá jako parametr funkci.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Omezení

Při definování uživatelské funkce existují určitá omezení:

* Funkce nemá přístup k proměnným.
* Funkce může používat pouze parametry, které jsou definovány ve funkci. Při použití [funkce parametry](template-functions-deployment.md#parameters) v rámci uživatelem definované funkce, jste omezeni na parametry pro tuto funkci.
* Funkce nemůže volat jiné uživatelem definované funkce.
* Funkce nemůže použít [referenční](template-functions-resource.md#reference) funkci nebo některou z funkcí [seznamu.](template-functions-resource.md#list)
* Parametry funkce nemohou mít výchozí hodnoty.


## <a name="next-steps"></a>Další kroky

* Informace o dostupných vlastnostech pro uživatelem definované funkce najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](template-syntax.md).
* Seznam dostupných funkcí šablony najdete v tématu [funkce šablony Správce prostředků Azure](template-functions.md).
