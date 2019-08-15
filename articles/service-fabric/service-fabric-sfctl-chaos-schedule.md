---
title: Azure Service Fabric CLI – sfctl chaos Schedule | Microsoft Docs
description: Popisuje příkazy plánování Service Fabric CLI sfctl chaos.
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
ms.openlocfilehash: 1664978110b7c700906cbf4e6c80806ac70f1f05
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036531"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Získejte a nastavte plán chaos.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| získat | Získejte plán chaos definující, kdy a jak se má spustit chaos. |
| set | Nastavte plán používaný nástrojem chaos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos plánu Get
Získejte plán chaos definující, kdy a jak se má spustit chaos.

Získá verzi používaného plánu chaos a plán chaos, který definuje, kdy a jak se má spustit chaos.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Timeout-t | Časový limit serveru v sekundách.  Výchozí\: hodnota je 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-chaos-schedule-set"></a>sada plánů sfctl chaos
Nastavte plán používaný nástrojem chaos.

Chaos automaticky naplánuje spuštění na základě plánu chaos. Verze v zadaném vstupním plánu musí odpovídat verzi plánu chaos na serveru. Pokud zadaná verze neodpovídá verzi na serveru, plán chaos se neaktualizuje. Pokud je zadaná verze shodná s verzí na serveru, pak se aktualizuje plán chaos a verze plánu chaos na serveru se zvýší o jednu a po 2 147 483 647 se zalomí zpět na 0. Pokud je chaos spuštěn při tomto volání, volání se nezdaří.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --chaos-Parameters-Dictionary | Seznam kódovaný v kódování JSON představující mapování názvů řetězců na ChaosParameters, které mají být použity úlohami. |
| --Datum vypršení platnosti-UTC | Datum a čas, kdy se má zastavit plánování chaos pomocí plánu.  Výchozí\: hodnota 9999-12-\:31T23\:59 59.999 z. |
| --úlohy | Seznam ChaosScheduleJobs kódovaných pomocí JSON, který představuje, kdy spustit chaos a s jakými parametry se má spustit chaos s. |
| --Start-datum-UTC | Datum a čas, kdy začít používat plán pro plánování chaos  Výchozí\: hodnota 1601-01-\:01T00\:00 00.000 z |
| --Timeout-t | Časový limit serveru v sekundách.  Výchozí\: hodnota je 60. |
| --verze | Číslo verze plánu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

### <a name="examples"></a>Příklady

Následující příkaz nastaví plán (za předpokladu, že má aktuální plán verzi 0), která začíná na 2016-01-01 a končí na 2038-01-01, který spouští chaos 24 hodin dne, 7 dní v týdnu. Chaos bude pro daný čas naplánován na cluster.

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


## <a name="next-steps"></a>Další postup
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).
