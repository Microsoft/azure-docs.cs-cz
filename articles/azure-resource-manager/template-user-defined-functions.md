---
title: Uživatelsky definované funkce v šablonách
description: Popisuje, jak definovat a používat uživatelsky definované funkce v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 94fdf80ffc309645a4bc10109a5e8dd4450731e2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143707"
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
* Funkce nemůže používat [odkazovou](resource-group-template-functions-resource.md#reference) funkci ani žádnou z funkcí [seznamu](resource-group-template-functions-resource.md#list) .
* Parametry pro funkci nemohou mít výchozí hodnoty.


## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech uživatelsky definovaných funkcí naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Seznam dostupných funkcí šablon naleznete v tématu [Azure Resource Manager Functions Template](resource-group-template-functions.md).
