---
title: Plán Azure Service Fabric CLI – sfctl chaos
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro plánování chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: dd911574cd37c18e3043cfc2482bd1fcd5f3857b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260844"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Získejte a nastavte plán chaos.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| get | Získejte plán chaos definující, kdy a jak se má spustit chaos. |
| set | Nastavte plán používaný nástrojem chaos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos plánu Get
Získejte plán chaos definující, kdy a jak se má spustit chaos.

Získá verzi používaného plánu chaos a plán chaos, který definuje, kdy a jak se má spustit chaos.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-chaos-schedule-set"></a>sada plánů sfctl chaos
Nastavte plán používaný nástrojem chaos.

Chaos automaticky naplánuje spuštění na základě plánu chaos. Plán chaos se aktualizuje, pokud zadaná verze odpovídá verzi na serveru. Při aktualizaci plánu chaos se verze na serveru zvyšuje o 1. Verze na serveru se po dosažení velkého množství zalomí zpět na 0. Pokud je chaos spuštěn při tomto volání, volání se nezdaří.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --chaos-Parameters-Dictionary | Seznam kódovaný v kódování JSON představující mapování názvů řetězců na ChaosParameters, které mají být použity úlohami. |
| --Datum vypršení platnosti-UTC | Datum a čas, kdy se má zastavit plánování chaos pomocí plánu.  Výchozí hodnota \: 9999-12-31T23 \: 59 \: 59.999 z. |
| --úlohy | Seznam ChaosScheduleJobs kódovaných pomocí JSON, který představuje, kdy spustit chaos a s jakými parametry se má spustit chaos s. |
| --Start-datum-UTC | Datum a čas, kdy začít používat plán pro plánování chaos  Výchozí hodnota \: 1601-01-01T00 \: 00 \: 00.000 z |
| --Timeout-t | Výchozí hodnota je \: 60. |
| --verze | Číslo verze plánu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

### <a name="examples"></a>Příklady

Následující příkaz nastaví plán (za předpokladu, že má aktuální plán verzi 0), která začíná na 2016-01-01 a končí na 2038-01-01, který spouští chaos 24 hodin dne, 7 dní v týdnu.
Chaos bude pro daný čas naplánován na cluster.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
