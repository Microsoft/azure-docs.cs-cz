---
title: Azure Service Fabric CLI – telemetrie nastavení sfctl | Microsoft Docs
description: Popisuje příkazy telemetrie Service Fabric CLI sfctl Settings.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: cf5ebbeb4d9b4757e0c55eeb1a9268065efb2c7c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035206"
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
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
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


## <a name="next-steps"></a>Další postup
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).