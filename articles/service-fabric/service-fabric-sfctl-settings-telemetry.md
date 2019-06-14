---
title: Azure Service Fabric CLI sfctl nastavení telemetrie | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl nastavení telemetrie.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 42a82ab0be37f260a48a1da6cecab5120c24d293
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556334"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Konfigurace nastavení telemetrie pro tuto instanci sfctl místní.

Shromažďuje telemetrická data Sfctl název příkazu bez parametrů, které jsou k dispozici nebo jejich hodnoty sfctl verze, typ operačního systému, verze pythonu, úspěch nebo neúspěch příkazu, který vrátí chybovou zprávu.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| nastavení telemetrie | Zapnout nebo vypnout telemetrii. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl nastavení telemetrie set telemetrie
Zapnout nebo vypnout telemetrii.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --vypnuto | Vypněte telemetrii. |
| – na | Zapněte telemetrii. Toto je výchozí hodnota. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

### <a name="examples"></a>Příklady

Vypněte telemetrii.

```
sfctl settings telemetry set_telemetry --off
```

Zapněte telemetrii.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).