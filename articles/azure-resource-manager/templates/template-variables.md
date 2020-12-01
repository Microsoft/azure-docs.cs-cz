---
title: Proměnné v šablonách
description: Popisuje definování proměnných v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 5d9b58d63e96656c45d3494d24099bbeadc46b11
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353455"
---
# <a name="variables-in-arm-template"></a>Proměnné v šabloně ARM

Tento článek popisuje, jak definovat a používat proměnné v šabloně Azure Resource Manager (šablona ARM). K zjednodušení šablony můžete použít proměnné. Místo opakujících se složitých výrazů v rámci šablony definujete proměnnou, která obsahuje složitý výraz. Pak na tuto proměnnou odkazujete podle potřeby v rámci šablony.

Správce prostředků vyřeší proměnné před spuštěním operací nasazení. Všude, kde je proměnná v šabloně použitá, ji Resource Manager nahradí vyřešenou hodnotou.

Formát každé proměnné musí odpovídat jednomu z [datových typů](template-syntax.md#data-types).

## <a name="define-variable"></a>Definovat proměnnou

Následující příklad ukazuje definici proměnné. Vytvoří hodnotu řetězce pro název účtu úložiště. Používá několik funkcí šablon k získání hodnoty parametru a zřetězuje je do jedinečného řetězce.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

V oddílu Variables nemůžete použít [referenční](template-functions-resource.md#reference) funkci ani žádnou z funkcí [seznamu](template-functions-resource.md#list) . Tyto funkce získávají běhový stav prostředku a nelze jej provést před nasazením, když jsou proměnné vyřešeny.

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

## <a name="configuration-variables"></a>Konfigurační proměnné

Můžete definovat proměnné, které uchovávají související hodnoty pro konfiguraci prostředí. Proměnnou definujete jako objekt s hodnotami. Následující příklad ukazuje objekt, který obsahuje hodnoty pro dvě prostředí – **test** a **prod**.

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

V parametrech vytvoříte hodnotu, která určuje, které hodnoty konfigurace se mají použít.

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

Chcete-li načíst nastavení pro zadané prostředí, použijte proměnnou a parametr dohromady.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Příklady šablon

Následující příklady ukazují scénáře použití proměnných.

|Template (Šablona)  |Popis  |
|---------|---------|
| [Definice proměnných](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Ukazuje různé typy proměnných. Šablona neimplementuje žádné prostředky. Vytvoří proměnné hodnoty a vrátí tyto hodnoty. |
| [konfigurační proměnná](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Ukazuje použití proměnné definující konfigurační hodnoty. Šablona neimplementuje žádné prostředky. Vytvoří proměnné hodnoty a vrátí tyto hodnoty. |
| [pravidla zabezpečení sítě](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) a [soubor parametrů](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Sestaví pole ve správném formátu pro přiřazení pravidel zabezpečení do skupiny zabezpečení sítě. |

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech proměnných najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Doporučení k vytváření proměnných najdete v tématu [osvědčené postupy – proměnné](template-best-practices.md#variables).
