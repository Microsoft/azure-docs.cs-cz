---
title: Azure Resource Manager syntaxe a výrazy šablony
description: Popisuje deklarativní syntaxi JSON pro šablony Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 1e7288da19e2e81d609b952e03d5143b03a65c63
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259480"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxe a výrazy v šablonách Azure Resource Manager

Základní syntaxí šablony je JSON. Můžete však použít výrazy k rozšiřování hodnot JSON dostupných v rámci šablony.  Výrazy začínají a končí závorkami: `[` a `]`v uvedeném pořadí. Hodnota výrazu je vyhodnocena při nasazení šablony. Výraz může vracet řetězec, celé číslo, logickou hodnotu, pole nebo objekt.

Výraz šablony nemůže být delší než 24 576 znaků.

## <a name="use-functions"></a>Použití funkcí

Následující příklad ukazuje výraz ve výchozí hodnotě parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Syntaxe `resourceGroup()` v rámci výrazu volá jednu z funkcí, které správce prostředků poskytují pro použití v rámci šablony. V tomto případě je to funkce [Resource](resource-group-template-functions-resource.md#resourcegroup) . Stejně jako v jazyce JavaScript jsou volání funkcí formátována `functionName(arg1,arg2,arg3)`jako. Syntaxe `.location` načte jednu vlastnost z objektu vráceného touto funkcí.

Funkce šablon a jejich parametry rozlišují velká a malá písmena. Například Správce prostředků vyřeší **proměnné (' var1 ')** a **proměnné (' var1 ')** jako stejné. Je-li tato funkce vyhodnocena, pokud funkce Express nemění velká a malá písmena (například toUpper nebo toLower), funkce zachovává případ. Některé typy prostředků můžou mít požadavky na případy, které jsou oddělené od způsobu, jakým se funkce vyhodnocují.

Chcete-li předat řetězcovou hodnotu jako parametr funkci, použijte jednoduché uvozovky.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Řídicí znaky

Chcete-li, aby byl řetězcový literál začínat levou hranatou `[` závorkou `]`a končit pravou závorkou, ale nebyl interpretován jako výraz, přidejte další hranatou závorku, `[[`která zahájí řetězec s. Například proměnná:

```json
"demoVar1": "[[test value]"
```

Přeloží `[test value]`na.

Nicméně pokud literální řetězec nekončí závorkou, nezařídí první vymezovač. Například proměnná:

```json
"demoVar2": "[test] value"
```

Přeloží `[test] value`na.

Chcete-li ve výrazu, jako je například přidání objektu JSON do šablony, řídicí dvojité uvozovky, použijte zpětné lomítko.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>Další postup

* Úplný seznam funkcí šablon naleznete v tématu [Azure Resource Manager Functions Template](resource-group-template-functions.md).
* Další informace o souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
