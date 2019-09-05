---
title: Uživatelsky definované funkce v šabloně Azure Resource Manager
description: Popisuje, jak definovat a používat uživatelsky definované funkce v šabloně Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 71c166b9c596c3c8628f943ae5c7dbebd9c2d51c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384135"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Uživatelsky definované funkce v šabloně Azure Resource Manager

V rámci šablony můžete vytvořit vlastní funkce. Tyto funkce jsou k dispozici pro použití ve vaší šabloně. Uživatelsky definované funkce jsou oddělené od [standardních funkcí šablon](resource-group-template-functions.md) , které jsou automaticky dostupné v rámci šablony. Vytvářejte vlastní funkce, pokud máte složité výrazy, které se ve vaší šabloně opakovaně používají.

Tento článek popisuje, jak do šablony Azure Resource Manager přidat uživatelsky definované funkce.

## <a name="define-the-function"></a>Definovat funkci

Vaše funkce vyžadují hodnotu oboru názvů, aby se zabránilo konfliktům názvů s funkcemi šablon. Následující příklad ukazuje funkci, která vrací název účtu úložiště:

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

Následující příklad ukazuje, jak zavolat funkci.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>Omezení

Při definování uživatelské funkce existují určitá omezení:

* Funkce nemá přístup k proměnným.
* Funkce může používat pouze parametry, které jsou definovány ve funkci. Použijete-li funkci [Parameters](resource-group-template-functions-deployment.md#parameters) v rámci uživatelsky definované funkce, budete omezeni na parametry této funkce.
* Funkce nemůže volat jiné uživatelsky definované funkce.
* Funkce nemůže používat [odkazy](resource-group-template-functions-resource.md#reference) ani funkce [seznamu](resource-group-template-functions-resource.md#list) .
* Parametry pro funkci nemohou mít výchozí hodnoty.


## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech uživatelsky definovaných funkcí naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Seznam dostupných funkcí šablon naleznete v tématu [Azure Resource Manager Functions Template](resource-group-template-functions.md).
