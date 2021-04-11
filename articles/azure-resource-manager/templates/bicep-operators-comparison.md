---
title: Bicep operátory porovnání
description: Popisuje operátory porovnání bicep, které porovnávají hodnoty.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c382ef355131d271d9f4fa4a978a807b9aac1e4f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211293"
---
# <a name="bicep-comparison-operators"></a>Bicep operátory porovnání

Relační operátory porovnávají hodnoty a vracejí buď `true` nebo `false` . Pokud chcete příklady spustit, použijte Azure CLI nebo Azure PowerShell k [nasazení souboru bicep](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operátor | Name |
| ---- | ---- |
| `>=` | [Větší než nebo rovno](#greater-than-or-equal-) |
| `>`  | [Větší než](#greater-than-) |
| `<=` | [Menší než nebo rovno](#less-than-or-equal-) |
| `<`  | [Je menší než](#less-than-) |
| `==` | [Je rovno](#equals-) |
| `!=` | [Není rovno](#not-equal-) |
| `=~` | [Rovná se bez rozlišení velkých a malých písmen](#equal-case-insensitive-) |
| `!~` | [Nerovná se bez rozlišení velkých a malých písmen](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Větší než nebo rovno >=

`operand1 >= operand2`

Vyhodnotí, zda je první hodnota větší než nebo rovna druhé hodnotě.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | Integer, String | První hodnota v porovnání |
| `operand2` | Integer, String | Druhá hodnota v porovnání |

### <a name="return-value"></a>Vrácená hodnota

Pokud je první hodnota větší než nebo rovna druhé hodnotě, `true` je vrácena. V opačném případě `false` se vrátí.

### <a name="example"></a>Příklad

Je porovnána dvojice celých čísel a párů řetězců.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>Větší než >

`operand1 > operand2`

Vyhodnotí, zda je první hodnota větší než druhá hodnota.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | Integer, String | První hodnota v porovnání |
| `operand2` | Integer, String | Druhá hodnota v porovnání |

### <a name="return-value"></a>Vrácená hodnota

Pokud je první hodnota větší než druhá hodnota, `true` je vrácena. V opačném případě `false` se vrátí.

### <a name="example"></a>Příklad

Je porovnána dvojice celých čísel a párů řetězců.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

Výstup z příkladu:

V ohybu **e** v **ohybu** je první řetězec větší.

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>Menší než nebo rovno <=

`operand1 <= operand2`

Vyhodnotí, zda je první hodnota menší nebo rovna druhé hodnotě.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | Integer, String | První hodnota v porovnání |
| `operand2` | Integer, String | Druhá hodnota v porovnání |

### <a name="return-value"></a>Vrácená hodnota

Pokud je první hodnota menší nebo stejná jako druhá hodnota, `true` je vrácena. V opačném případě `false` se vrátí.

### <a name="example"></a>Příklad

Je porovnána dvojice celých čísel a párů řetězců.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>Menší než <

`operand1 < operand2`

Vyhodnotí, zda je první hodnota menší než druhá hodnota.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | Integer, String | První hodnota v porovnání |
| `operand2` | Integer, String | Druhá hodnota v porovnání |

### <a name="return-value"></a>Vrácená hodnota

Pokud je první hodnota menší než druhá hodnota, `true` je vrácena. V opačném případě `false` se vrátí.

### <a name="example"></a>Příklad

Je porovnána dvojice celých čísel a párů řetězců.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

Výstup z příkladu:

Řetězec je z `true` důvodu malých písmen méně než velká písmena.

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>Equals = =

`operand1 == operand2`

Vyhodnotí, jestli jsou hodnoty stejné.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | řetězec, celé číslo, logická hodnota, pole, objekt | První hodnota v porovnání |
| `operand2` | řetězec, celé číslo, logická hodnota, pole, objekt | Druhá hodnota v porovnání |

### <a name="return-value"></a>Vrácená hodnota

Pokud jsou operandy stejné, `true` je vrácena. Pokud se operandy liší, `false` je vrácen.

### <a name="example"></a>Příklad

Jsou porovnány páry celých čísel, řetězců a logických hodnot.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

## <a name="not-equal-"></a>Není rovno! =

`operand1 != operand2`

Vyhodnotí, **Pokud se neshodují** dvě hodnoty.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | řetězec, celé číslo, logická hodnota, pole, objekt | První hodnota v porovnání |
| `operand2` | řetězec, celé číslo, logická hodnota, pole, objekt | Druhá hodnota v porovnání |

### <a name="return-value"></a>Vrácená hodnota

Pokud operandy **nejsou stejné,** `true` je vrácena. Pokud jsou operandy stejné, `false` je vrácena.

### <a name="example"></a>Příklad

Jsou porovnány páry celých čísel, řetězců a logických hodnot.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

## <a name="equal-case-insensitive-"></a>Velká a malá písmena = ~

`operand1 =~ operand2`

Ignoruje velikost písmen, aby bylo možné zjistit, zda jsou tyto dvě hodnoty stejné.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1`  | řetězec | První řetězec v porovnání |
| `operand2`  | řetězec | Druhý řetězec v porovnání |

### <a name="return-value"></a>Vrácená hodnota

Pokud jsou řetězce stejné, `true` je vrácena. V opačném případě `false` se vrátí.

### <a name="example"></a>Příklad

Porovná řetězce, které používají velká písmena.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | false (nepravda) |

## <a name="not-equal-case-insensitive-"></a>Nerovná se velká a malá písmena! ~

`operand1 !~ operand2`

Ignoruje velikost písmen, aby bylo možné zjistit, **zda nejsou tyto** dvě hodnoty stejné.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | řetězec | První řetězec v porovnání |
| `operand2` | řetězec | Druhý řetězec v porovnání |

### <a name="return-value"></a>Vrácená hodnota

Pokud **se řetězce neshodují** , `true` je vrácena. V opačném případě `false` se vrátí.

### <a name="example"></a>Příklad

Porovná řetězce, které používají velká písmena.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | false (nepravda) |

## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit soubor bicep, přečtěte si téma [kurz: vytvoření a nasazení prvního Azure Resource Manager souboru bicep](bicep-tutorial-create-first-bicep.md).
- Informace o tom, jak vyřešit chyby typu bicep, naleznete v části [Any Function for bicep](template-functions-any.md).
- Chcete-li porovnat syntaxi pro bicep a JSON, přečtěte si téma [porovnání JSON a bicep pro šablony](compare-template-syntax.md).
- Příklady funkcí šablon bicep a ARM najdete v tématu [funkce šablon ARM](template-functions.md).
