---
title: Azure Service Fabric CLI- sfctl mesh kód-package-log
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání protokolů pro zadaný balíček kódu.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906046"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Získejte protokoly pro kontejner zadaný balíček kódu pro danou repliku služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| get | Získá protokoly z kontejneru. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh kód-balíček-log získat
Získá protokoly z kontejneru.

Získá protokoly pro kontejner zadaný kód balíčku repliky služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název aplikace --název aplikace [Povinné] | Název aplikace |
| --code-package-name [Povinné] | Název balíčku kódu služby. |
| --název repliky [Povinné] | Název repliky service fabric. |
| --název služby [Povinné] | Název služby |
| --ocas | Počet řádků, které mají být zobrazovány z konce protokolů. Výchozí hodnota je 100. 'all' pro zobrazení úplných protokolů. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) cli service fabric.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).