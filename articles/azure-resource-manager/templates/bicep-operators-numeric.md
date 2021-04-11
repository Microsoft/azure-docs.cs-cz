---
title: Bicep číselné operátory
description: Popisuje bicep číselné operátory, které počítají hodnoty.
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211292"
---
# <a name="bicep-numeric-operators"></a>Bicep číselné operátory

Číselné operátory používají celá čísla k provádění výpočtů a vracení celočíselných hodnot. Pokud chcete příklady spustit, použijte Azure CLI nebo Azure PowerShell k [nasazení souboru bicep](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

| Operátor | Name |
| ---- | ---- |
| `*` | [Násobení](#multiply-) |
| `/` | [Rozdělovací](#divide-) |
| `%` | [Modulo](#modulo-) |
| `+` | [Přidat](#add-) |
| `-` | [Odčítání](#subtract--) |
| `-` | [Symbol](#minus--) |

> [!NOTE]
> Odečíst a minus použijte stejný operátor. Funkce je odlišná, protože rozdíl používá dva operandy a mínus používá jeden operand.

## <a name="multiply-"></a>Hodnotou

`operand1 * operand2`

Vynásobí dvě celá čísla.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1`  | integer | Číslo, které se má vynásobit |
| `operand2`  | integer  | Násobitel čísla. |

### <a name="return-value"></a>Vrácená hodnota

Násobení vrátí produkt jako celé číslo.

### <a name="example"></a>Příklad

Vynásobí se dvě celá čísla a vrátí produkt.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `product` | integer | 10 |

## <a name="divide-"></a>Rozdělovací

`operand1 / operand2`

Vydělí celé číslo na celé číslo.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | integer | Celé číslo, které je rozděleno. |
| `operand2` | integer | Celé číslo, které se používá pro dělení. Nemůže být nula. |

### <a name="return-value"></a>Vrácená hodnota

Dělení vrátí podíl jako celé číslo.

### <a name="example"></a>Příklad

Dvě celá čísla jsou rozdělena a vrátí podíl.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `quotient` | integer | 5 |

## <a name="modulo-"></a>Operace

`operand1 % operand2`

Vydělí celé číslo na celé číslo a vrátí zbytek.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1`  | integer  | Celé číslo, které je rozděleno. |
| `operand2`  | integer  | Celé číslo, které se používá pro dělení. Nemůže být 0. |

### <a name="return-value"></a>Vrácená hodnota

Zbytek je vrácen jako celé číslo. Pokud dělení nevyprodukuje zbytek, vrátí 0.

### <a name="example"></a>Příklad

Dvě dvojice celých čísel jsou rozdělené a vrátí zbytek.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `remainder` | integer | 1 |
| `zeroRemainder` | integer | 0 |

## <a name="add-"></a>Přidat +

`operand1 + operand2`

Přidá dvě celá čísla.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | integer | Číslo, které se má přidat |
| `operand2` | integer | Číslo, které je přidáno k číslu. |

### <a name="return-value"></a>Vrácená hodnota

Sčítání vrátí součet jako celé číslo.

### <a name="example"></a>Příklad

Přidávají se dvě celá čísla a vrátí součet.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `sum` | integer | 12 |

## <a name="subtract--"></a>Odečten

`operand1 - operand2`

Odečte celé číslo od celého čísla.

### <a name="operands"></a>Operandy

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `operand1` | integer | Větší číslo, od kterého se odečte. |
| `operand2` | integer | Číslo, které se odečte od většího čísla. |

### <a name="return-value"></a>Vrácená hodnota

Odčítání vrátí rozdíl jako celé číslo.

### <a name="example"></a>Příklad

Celé číslo je odečteno a vrátí rozdíl.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `difference` | integer | 6 |

## <a name="minus--"></a>Symbol

`-integerValue`

Vynásobí celé číslo číslem `-1` .

### <a name="operand"></a>Pravý

| Pravý | Typ | Description |
| ---- | ---- | ---- |
| `integerValue` | integer | Celé číslo vynásobené `-1` . |

### <a name="return-value"></a>Vrácená hodnota

Celé číslo je vynásobeno hodnotou `-1` . Kladné celé číslo vrátí záporné celé číslo a záporné celé číslo vrátí kladné celé číslo. Hodnoty mohou být zabaleny pomocí závorek.

### <a name="example"></a>Příklad

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

Výstup z příkladu:

| Název | Typ | Hodnota |
| ---- | ---- | ---- |
| `startedPositive` | integer | -10 |
| `startedNegative` | integer | 20 |

## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit soubor bicep, přečtěte si téma [kurz: vytvoření a nasazení prvního Azure Resource Manager souboru bicep](bicep-tutorial-create-first-bicep.md).
- Informace o tom, jak vyřešit chyby typu bicep, naleznete v části [Any Function for bicep](template-functions-any.md).
- Chcete-li porovnat syntaxi pro bicep a JSON, přečtěte si téma [porovnání JSON a bicep pro šablony](compare-template-syntax.md).
- Příklady funkcí šablon bicep a ARM najdete v tématu [funkce šablon ARM](template-functions.md).
