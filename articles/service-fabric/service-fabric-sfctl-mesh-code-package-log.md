---
title: Azure Service Fabric CLI – sfctl pro kód sítě-balíček – protokol
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání protokolů pro zadaný balíček kódu.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 78edc9bb36b711f72300942bc9900b0fde7c51d2
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646139"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Načte protokoly pro kontejner zadaného balíčku kódu pro danou repliku služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| získat | Načte protokoly z kontejneru. |

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
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).