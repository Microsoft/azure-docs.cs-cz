---
title: Proměnné v šablonách
description: Popisuje definování proměnných v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874430"
---
# <a name="variables-in-arm-template"></a>Proměnné v šabloně ARM

Tento článek popisuje, jak definovat a používat proměnné v šabloně Azure Resource Manager (šablona ARM). K zjednodušení šablony můžete použít proměnné. Místo opakujících se složitých výrazů v rámci šablony definujete proměnnou, která obsahuje složitý výraz. Pak na tuto proměnnou odkazujete podle potřeby v rámci šablony.

Správce prostředků vyřeší proměnné před spuštěním operací nasazení. Všude, kde je proměnná v šabloně použitá, ji Resource Manager nahradí vyřešenou hodnotou.

## <a name="define-variable"></a>Definovat proměnnou

Při definování proměnné zadejte hodnotu nebo výraz šablony, který se přeloží na [datový typ](template-syntax.md#data-types). Při vytváření proměnné můžete použít hodnotu z parametru nebo jiné proměnné.

Můžete použít [funkce šablon](template-functions.md) v deklaraci proměnné, ale nemůžete použít [odkazovou](template-functions-resource.md#reference) funkci ani žádnou z funkcí [seznamu](template-functions-resource.md#list) . Tyto funkce získávají běhový stav prostředku a nelze jej provést před nasazením, když jsou proměnné vyřešeny.

Následující příklad ukazuje definici proměnné. Vytvoří hodnotu řetězce pro název účtu úložiště. Používá několik funkcí šablon k získání hodnoty parametru a zřetězuje je do jedinečného řetězce.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>Použít proměnnou

V šabloně odkazujete na hodnotu parametru pomocí funkce [Variables](template-functions-deployment.md#variables) . Následující příklad ukazuje, jak použít proměnnou pro vlastnost prostředku.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>Příklad šablony

Následující šablona neimplementuje žádné prostředky. Pouze ukazuje několik způsobů, jak deklarovat proměnné.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Konfigurační proměnné

Můžete definovat proměnné, které uchovávají související hodnoty pro konfiguraci prostředí. Proměnnou definujete jako objekt s hodnotami. Následující příklad ukazuje objekt, který obsahuje hodnoty pro dvě prostředí – **test** a **prod**. Během nasazování předáte jednu z těchto hodnot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech proměnných najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Doporučení k vytváření proměnných najdete v tématu [osvědčené postupy – proměnné](template-best-practices.md#variables).
* Příklad šablony, která přiřazuje pravidla zabezpečení skupině zabezpečení sítě, najdete v tématu [pravidla zabezpečení sítě](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) a [soubor parametrů](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
