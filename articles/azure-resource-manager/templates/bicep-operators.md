---
title: Bicep operátory
description: V této části najdete popis bicep operátorů dostupných pro nasazení Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: 0838ebf6bc03f4237ef76e07f1eb6f25aa996fc0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537821"
---
# <a name="bicep-operators"></a>Bicep operátory

Tento článek popisuje operátory bicep, které jsou k dispozici při vytváření šablony bicep a použití Azure Resource Manager k nasazení prostředků. Operátory slouží k výpočtu hodnot, porovnávání hodnot nebo vyhodnocení podmínek. Existují tři typy operátorů bicep:

- [porovnání](#comparison)
- [biolog](#logical)
- [číselné](#numeric)

Uzavření výrazu mezi `(` a `)` umožňuje přepsat výchozí prioritu operátoru bicep. Například výraz x + y/z vyhodnocuje dělení jako první a pak přidání. Výraz (x + y)/z se však vyhodnocuje jako první a dělené druhé.

## <a name="comparison"></a>Porovnání

Relační operátory porovnávají hodnoty a vracejí buď `true` nebo `false` .

| Operátor | Název | Popis |
| ---- | ---- | ---- |
| `>=` | [Větší než nebo rovno](bicep-operators-comparison.md#greater-than-or-equal-) | Vyhodnotí, zda je první hodnota větší než nebo rovna druhé hodnotě. |
| `>`  | [Větší než](bicep-operators-comparison.md#greater-than-) | Vyhodnotí, zda je první hodnota větší než druhá hodnota. |
| `<=` | [Menší než nebo rovno](bicep-operators-comparison.md#less-than-or-equal-) | Vyhodnotí, zda je první hodnota menší nebo rovna druhé hodnotě. |
| `<`  | [Je menší než](bicep-operators-comparison.md#less-than-) | Vyhodnotí, zda je první hodnota menší než druhá hodnota. |
| `==` | [Je rovno](bicep-operators-comparison.md#equals-) | Vyhodnotí, pokud jsou dvě hodnoty stejné. |
| `!=` | [Není rovno](bicep-operators-comparison.md#not-equal-) | Vyhodnotí, **Pokud se neshodují** dvě hodnoty. |
| `=~` | [Rovná se bez rozlišení velkých a malých písmen](bicep-operators-comparison.md#equal-case-insensitive-) | Ignoruje velikost písmen, aby bylo možné určit, zda jsou dvě hodnoty stejné. |
| `!~` | [Nerovná se bez rozlišení velkých a malých písmen](bicep-operators-comparison.md#not-equal-case-insensitive-) | Ignoruje velikost písmen, aby bylo možné zjistit, **zda nejsou dvě** hodnoty stejné. |

## <a name="logical"></a>Logické

Logické operátory vyhodnocují logické hodnoty, vracejí hodnoty, které nejsou null, nebo vyhodnocují podmíněný výraz.

| Operátor | Název | Popis |
| ---- | ---- | ---- |
| `&&` | [Ani](bicep-operators-logical.md#and-) | Vrátí `true` , zda jsou všechny hodnoty pravdivé. |
| `||`| [Nebo](bicep-operators-logical.md#or-) | Vrátí `true` , zda má jedna z hodnot hodnotu true. |
| `!` | [Mění](bicep-operators-logical.md#not-) | Negace logické hodnoty. |
| `??` | [COALESCE](bicep-operators-logical.md#coalesce-) | Vrátí první hodnotu, která není null. |
| `?` `:` | [Podmíněný výraz](bicep-operators-logical.md#conditional-expression--) | Vyhodnotí podmínku pro true nebo false a vrátí hodnotu. |

## <a name="numeric"></a>Číselný

Číselné operátory používají celá čísla k provádění výpočtů a vracení celočíselných hodnot.

| Operátor | Název | Popis |
| ---- | ---- | ---- |
| `*` | [Násobení](bicep-operators-numeric.md#multiply-) | Vynásobí dvě celá čísla. |
| `/` | [Rozdělovací](bicep-operators-numeric.md#divide-) | Vydělí celé číslo na celé číslo. |
| `%` | [Modulo](bicep-operators-numeric.md#modulo-) | Vydělí celé číslo na celé číslo a vrátí zbytek. |
| `+` | [Přidat](bicep-operators-numeric.md#add-) | Přidá dvě celá čísla. |
| `-` | [Odčítání](bicep-operators-numeric.md#subtract--) | Odečte celé číslo od celého čísla. |
| `-` | [Symbol](bicep-operators-numeric.md#minus--) | Vynásobí celé číslo číslem `-1` . |

> [!NOTE]
> Odečíst a minus použijte stejný operátor. Funkce je odlišná, protože rozdíl používá dva operandy a mínus používá jeden operand.

## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit soubor bicep, přečtěte si téma [kurz: vytvoření a nasazení prvního Azure Resource Manager souboru bicep](bicep-tutorial-create-first-bicep.md).
- Informace o tom, jak vyřešit chyby typu bicep, naleznete v části [Any Function for bicep](template-functions-any.md).
- Chcete-li porovnat syntaxi pro bicep a JSON, přečtěte si téma [porovnání JSON a bicep pro šablony](compare-template-syntax.md).
- Příklady funkcí šablon bicep a ARM najdete v tématu [funkce šablon ARM](template-functions.md).
