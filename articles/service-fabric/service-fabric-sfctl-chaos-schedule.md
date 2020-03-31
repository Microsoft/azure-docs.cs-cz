---
title: Plán chaosu Azure Service Fabric- sfctl
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro plánování chaosu.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 29b365c48e405830e238945f1d94156f477c15b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906186"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Najdi a nastavte plán chaosu.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| get | Získejte chaos plán definování, kdy a jak spustit Chaos. |
| set | Nastavte plán používaný Chaosem. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos plán dostat
Získejte chaos plán definování, kdy a jak spustit Chaos.

Získá verzi Chaos plán v provozu a chaos plán, který definuje, kdy a jak spustit Chaos.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos plán nastavit
Nastavte plán používaný Chaosem.

Chaos bude automaticky plánovat běží na základě Chaos plánu. Plán chaosu bude aktualizován, pokud zadaný verze odpovídá verzi na serveru. Při aktualizaci chaos plán, verze na serveru se zpřímí o 1. Verze na serveru se po dosažení velkého počtu vrátí zpět na 0. Pokud Chaos běží při tomto volání, volání se nezdaří.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --chaos-parameters-dictionary --chaos-parameters-dictionary --chaos-parameters-dictionary --chaos | JSON kódovaný seznam představující mapování názvů řetězců na ChaosParameters, které mají být použity Jobs. |
| --datum vypršení platnosti-utc | Datum a čas, kdy přestat používat plán naplánovat Chaos.  Výchozí\: 9999-12-31T23\:\:59 59.999z. |
| --práce | JSON kódovaný seznam ChaosScheduleJobs představující, kdy spustit Chaos a s jakými parametry spustit Chaos s. |
| --počáteční datum-utc | Datum a čas, kdy začít používat plán naplánovat Chaos.  Výchozí\: hodnota 1601-01-01T00\:00\:00.000Z. |
| --časový čas -t | Výchozí\: 60. |
| --verze | Číslo verze plánu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

### <a name="examples"></a>Příklady

Následující příkaz nastaví plán (za předpokladu, že aktuální plán má verzi 0), který začíná na 2016-01-01 a vyprší na 2038-01-01, který běží Chaos 24 hodin denně, 7 dní v týdnu.
Chaos bude naplánován na clusteru pro tuto dobu.
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
- [Nastavte](service-fabric-cli.md) cli service fabric.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).
