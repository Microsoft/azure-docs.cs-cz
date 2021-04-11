---
title: Logické operátory bicep
description: Popisuje logické operátory bicep, které vyhodnocují podmínky.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211297"
---
# <a name="bicep-logical-operators"></a>Logické operátory bicep

Logické operátory vyhodnocují logické hodnoty, vracejí hodnoty, které nejsou null, nebo vyhodnocují podmíněný výraz. Pokud chcete příklady spustit, použijte Azure CLI nebo Azure PowerShell k [nasazení souboru bicep](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operátor | Name |
| ---- | ---- |
| `&&` | [Ani](#and-) |
| `||` | [Nebo](#or-) |
| `!` | [Mění](#not-) |
| `??` | [COALESCE](#coalesce-) |
| `?` `:` | [Podmíněný výraz](#conditional-expression--) |

## <a name="and-"></a>A &&

`operand1 && operand2`

Určuje, zda jsou obě hodnoty pravdivé.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | První hodnota, která má být zkontrolována, pokud je hodnota true. |
| `operand2` | boolean | Druhá hodnota, která má být zkontrolována v případě hodnoty true. |
| Další operandy | boolean | Je možné zahrnout další operandy. |

### <a name="return-value"></a>Vrácená hodnota

`True` v případě, že jsou obě hodnoty pravdivé, jinak `false` se vrátí.

### <a name="example"></a>Příklad

Vyhodnotí sadu hodnot parametrů a sadu výrazů.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>Nebo | |

`operand1 || operand2`

Určuje, zda má jedna z hodnot hodnotu true.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | První hodnota, která má být zkontrolována, pokud je hodnota true. |
| `operand2` | boolean | Druhá hodnota, která má být zkontrolována v případě hodnoty true. |
| Další operandy | boolean | Je možné zahrnout další operandy. |

### <a name="return-value"></a>Vrácená hodnota

`True` Pokud je kterákoli z hodnot true, `false` vrátí se jinak.

### <a name="example"></a>Příklad

Vyhodnotí sadu hodnot parametrů a sadu výrazů.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Mění!

`!boolValue`

Negace logické hodnoty.

### <a name="operand"></a>Pravý

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `boolValue` | boolean | Logická hodnota, která je typu negace. |

### <a name="return-value"></a>Vrácená hodnota

Negace počáteční hodnoty a vrátí logickou hodnotu. Pokud je počáteční hodnota `true` , pak `false` se vrátí.

### <a name="example"></a>Příklad

`not`Operátor negace hodnoty. Hodnoty mohou být zabaleny pomocí závorek.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `startedTrue` | boolean | false (nepravda) |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>COALESCE?

`operand1 ?? operand2`

Vrátí první hodnotu, která není null, z operandů.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | řetězec, celé číslo, logická hodnota, objekt, pole | Hodnota, která se má testovat `null` . |
| `operand2` | řetězec, celé číslo, logická hodnota, objekt, pole | Hodnota, která se má testovat `null` . |
| Další operandy | řetězec, celé číslo, logická hodnota, objekt, pole | Hodnota, která se má testovat `null` . |

### <a name="return-value"></a>Vrácená hodnota

Vrátí první hodnotu, která není null. Prázdné řetězce, prázdné pole a prázdné objekty nejsou `null` a \<empty> vrátí hodnotu.

### <a name="example"></a>Příklad

Příkazy Output vrací hodnoty, které nejsou null. Typ výstupu musí odpovídat typu v porovnání nebo je vygenerována chyba.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `nonNullStr` | řetězec | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | řetězec | \<empty> |

## <a name="conditional-expression--"></a>Podmíněný výraz? :

`condition ? true-value : false-value`

Vyhodnotí podmínku a vrátí hodnotu, ať je podmínka pravdivá, nebo false.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `condition` | boolean | Podmínka, která se má vyhodnotit jako true nebo false |
| `true-value` | řetězec, celé číslo, logická hodnota, objekt, pole | Hodnota, pokud je podmínka pravdivá |
| `false-value` | řetězec, celé číslo, logická hodnota, objekt, pole | Hodnota, pokud je podmínka nepravdivá |

### <a name="example"></a>Příklad

Tento příklad vyhodnocuje počáteční hodnotu parametru a vrátí hodnotu, ať je podmínka pravdivá, nebo false.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `outValue` | řetězec | hodnota true |

## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit soubor bicep, přečtěte si téma [kurz: vytvoření a nasazení prvního Azure Resource Manager souboru bicep](bicep-tutorial-create-first-bicep.md).
- Informace o tom, jak vyřešit chyby typu bicep, naleznete v části [Any Function for bicep](template-functions-any.md).
- Chcete-li porovnat syntaxi pro bicep a JSON, přečtěte si téma [porovnání JSON a bicep pro šablony](compare-template-syntax.md).
- Příklady funkcí šablon bicep a ARM najdete v tématu [funkce šablon ARM](template-functions.md).
