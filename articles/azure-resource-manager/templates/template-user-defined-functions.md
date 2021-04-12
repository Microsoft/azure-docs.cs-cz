---
title: Uživatelsky definované funkce v šablonách
description: Popisuje, jak definovat a používat uživatelsky definované funkce v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 9c7480958e6315c8aea1fd8d12613bcf9d606723
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379620"
---
# <a name="user-defined-functions-in-arm-template"></a>Uživatelsky definované funkce v šabloně ARM

V rámci šablony můžete vytvořit vlastní funkce. Tyto funkce jsou k dispozici pro použití ve vaší šabloně. Uživatelsky definované funkce jsou oddělené od [standardních funkcí šablon](template-functions.md) , které jsou automaticky dostupné v rámci šablony. Vytvářejte vlastní funkce, pokud máte složité výrazy, které se ve vaší šabloně opakovaně používají.

Tento článek popisuje, jak přidat uživatelsky definované funkce v šabloně Azure Resource Manager (šablona ARM).

## <a name="define-the-function"></a>Definovat funkci

Vaše funkce vyžadují hodnotu oboru názvů, aby se zabránilo konfliktům názvů s funkcemi šablon. Následující příklad ukazuje funkci, která vrací jedinečný název:

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

Následující příklad ukazuje šablonu, která obsahuje uživatelsky definovanou funkci pro získání jedinečného názvu pro účet úložiště. Šablona obsahuje parametr s názvem `storageNamePrefix` , který je předán jako parametr funkci.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Během nasazování se `storageNamePrefix` Parametr předává funkci:

* Šablona definuje parametr s názvem `storageNamePrefix` .
* Funkce používá, `namePrefix` protože můžete použít pouze parametry definované ve funkci. Další informace najdete v tématu [omezení](#limitations).
* V `resources` oddílu šablony `name` prvek používá funkci a předá `storageNamePrefix` hodnotu funkci `namePrefix` .

## <a name="limitations"></a>Omezení

Při definování uživatelské funkce existují určitá omezení:

* Funkce nemá přístup k proměnným.
* Funkce může používat pouze parametry, které jsou definovány ve funkci. Použijete-li funkci [Parameters](template-functions-deployment.md#parameters) v rámci uživatelsky definované funkce, budete omezeni na parametry této funkce.
* Funkce nemůže volat jiné uživatelsky definované funkce.
* Funkce nemůže používat [odkazovou](template-functions-resource.md#reference) funkci ani žádnou z funkcí [seznamu](template-functions-resource.md#list) .
* Parametry pro funkci nemohou mít výchozí hodnoty.

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech uživatelsky definovaných funkcí najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Seznam dostupných funkcí šablon najdete v tématu [funkce šablon ARM](template-functions.md).
