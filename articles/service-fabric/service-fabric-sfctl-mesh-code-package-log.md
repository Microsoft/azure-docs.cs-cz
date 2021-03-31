---
title: Azure Service Fabric CLI – sfctl pro kód sítě-balíček – protokol
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání protokolů pro zadaný balíček kódu.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257306"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Načte protokoly pro kontejner zadaného balíčku kódu pro danou repliku služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| get | Načte protokoly z kontejneru. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl – kód sítě – balíček – načíst protokol
Načte protokoly z kontejneru.

Načte protokoly pro kontejner zadaného balíčku kódu repliky služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Code-Package-Name [povinné] | Název balíčku kódu služby. |
| --název repliky [povinné] | Název repliky Service Fabric. |
| --Service-Name [povinné] | Název služby |
| --konec | Počet řádků, které se mají zobrazit na konci protokolů Výchozí hodnota je 100. All pro zobrazení úplných protokolů. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
