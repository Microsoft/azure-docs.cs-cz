---
title: Proměnné v šablonách
description: Popisuje, jak definovat proměnné v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483814"
---
# <a name="variables-in-azure-resource-manager-template"></a>Proměnné v šabloně Azure Resource Manager

Tento článek popisuje, jak definovat a používat proměnné v šabloně Azure Resource Manager. Proměnné se používají ke zjednodušení šablony. Místo opakování složitých výrazů v celé šabloně definujete proměnnou, která obsahuje komplikovaný výraz. Potom odkazovat na tuto proměnnou podle potřeby v celé šabloně.

Správce prostředků řeší proměnné před zahájením operací nasazení. Všude tam, kde je proměnná použita v šabloně, Správce prostředků ji nahradí vyřešenou hodnotou.

## <a name="define-variable"></a>Definovat proměnnou

Následující příklad ukazuje definici proměnné. Vytvoří hodnotu řetězce pro název účtu úložiště. Používá několik funkcí šablony k získání hodnoty parametru a zřetězí ji na jedinečný řetězec.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Nelze použít [referenční](template-functions-resource.md#reference) funkci nebo žádnou funkci [seznamu](template-functions-resource.md#list) v části proměnné. Tyto funkce získat runtime stav prostředku a nelze spustit před nasazením při proměnné jsou vyřešeny.

## <a name="use-variable"></a>Použít proměnnou

V šabloně odkazujete na hodnotu parametru pomocí funkce [proměnných.](template-functions-deployment.md#variables) Následující příklad ukazuje, jak použít proměnnou pro vlastnost prostředku.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Konfigurační proměnné

Můžete definovat proměnné, které uchovávají související hodnoty pro konfiguraci prostředí. Proměnnou definujete jako objekt s hodnotami. Následující příklad ukazuje objekt, který obsahuje hodnoty pro dvě prostředí - **test** a **prod**.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

V parametrech vytvoříte hodnotu, která označuje, které hodnoty konfigurace se mají použít.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Chcete-li načíst nastavení pro zadané prostředí, použijte proměnnou a parametr společně.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Ukázkové šablony

Následující příklady ukazují scénáře pro použití proměnných.

|Šablona  |Popis  |
|---------|---------|
| [definice proměnných](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Ukazuje různé typy proměnných. Šablona nenasazuje žádné prostředky. Vytvoří hodnoty proměnných a vrátí tyto hodnoty. |
| [konfigurační proměnná](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Ukazuje použití proměnné, která definuje hodnoty konfigurace. Šablona nenasazuje žádné prostředky. Vytvoří hodnoty proměnných a vrátí tyto hodnoty. |
| [pravidla zabezpečení sítě](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) a [soubor parametrů](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Vytvoří pole ve správném formátu pro přiřazení pravidel zabezpečení skupině zabezpečení sítě. |

## <a name="next-steps"></a>Další kroky

* Informace o dostupných vlastnostech proměnných najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](template-syntax.md).
* Doporučení týkající se vytváření proměnných naleznete v [tématu Doporučené postupy – proměnné](template-best-practices.md#variables).
