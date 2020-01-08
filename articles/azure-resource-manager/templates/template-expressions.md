---
title: Syntaxe a výrazy šablon
description: Popisuje deklarativní syntaxi JSON pro šablony Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: b7682ba10c30290e5935bc2dd17e2a83852d92f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484191"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxe a výrazy v šablonách Azure Resource Manager

Základní syntaxí šablony je JSON. Můžete však použít výrazy k rozšiřování hodnot JSON dostupných v rámci šablony.  Výrazy začínají a končí závorkami `[` a `]` v uvedeném pořadí. Hodnota výrazu se vyhodnocuje po nasazení šablony. Výraz může vrátit řetězec, celé číslo, logickou hodnotu, pole nebo objekt.

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

V rámci výrazu syntaxe `resourceGroup()` volá jednu z funkcí, které Správce prostředků poskytuje pro použití v rámci šablony. V tomto případě je to funkce [Resource](template-functions-resource.md#resourcegroup) . Stejně jako v jazyce JavaScript jsou volání funkcí formátována jako `functionName(arg1,arg2,arg3)`. Syntaxe `.location` načte jednu vlastnost z objektu vráceného touto funkcí.

Funkce šablon a jejich parametry rozlišují velká a malá písmena. Například Správce prostředků vyřeší **proměnné (' var1 ')** a **proměnné (' var1 ')** jako stejné. Je-li tato funkce vyhodnocena, pokud funkce Express nemění velká a malá písmena (například toUpper nebo toLower), funkce zachovává případ. Některé typy prostředků můžou mít požadavky na případy, které jsou oddělené od způsobu, jakým se funkce vyhodnocují.

Chcete-li předat řetězcovou hodnotu jako parametr funkci, použijte jednoduché uvozovky.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Řídicí znaky

Chcete-li, aby byl řetězcový literál začínat levou hranatou závorkou `[` a končit pravou hranatou závorkou `]`, ale nebyl interpretován jako výraz, přidejte další hranatou závorku, aby se řetězec začal `[[`. Například proměnná:

```json
"demoVar1": "[[test value]"
```

Přeloží na `[test value]`.

Nicméně pokud literální řetězec nekončí závorkou, nezařídí první vymezovač. Například proměnná:

```json
"demoVar2": "[test] value"
```

Přeloží na `[test] value`.

Chcete-li ve výrazu, jako je například přidání objektu JSON do šablony, řídicí dvojité uvozovky, použijte zpětné lomítko.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>Další kroky

* Úplný seznam funkcí šablon naleznete v tématu [Azure Resource Manager Functions Template](template-functions.md).
* Další informace o souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
