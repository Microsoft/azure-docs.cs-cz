---
title: Azure Service Fabric CLI – telemetrie nastavení sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro konfiguraci telemetrie sfctl.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: cdb4a44c8f19b31c164e2ba3ea5e16b7a09e743e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645272"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Nakonfigurujte nastavení telemetrie jako místní pro tuto instanci sfctl.

Telemetrie Sfctl shromažďuje název příkazu bez zadaných parametrů nebo jejich hodnot, verze Sfctl, typ operačního systému, verze Pythonu, úspěch nebo neúspěch příkazu, vrácená chybová zpráva.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| sada-telemetrie | Zapnutí nebo vypnutí telemetrie. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sada telemetrie nastavení sfctl – telemetrie
Zapnutí nebo vypnutí telemetrie.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --vypnuto | Vypněte telemetrii. |
| --zapnuto | Zapnout telemetrii. Toto je výchozí hodnota. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

### <a name="examples"></a>Příklady

Vypněte telemetrii.

```
sfctl settings telemetry set_telemetry --off
```

Zapnout telemetrii.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).