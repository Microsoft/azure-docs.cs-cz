---
title: Azure Service Fabric CLI - sfctl chaos plánu | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl chaos plánu.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 665fbbc8668e465c78d93b134f6a314d58791490
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276447"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Získání a nastavení plánu chaos.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| získat | Získáte plán Chaos definování, kdy a jak spustit Chaos. |
| set | Nastavte plán používá Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>získat plán sfctl chaosu
Získáte plán Chaos definování, kdy a jak spustit Chaos.

Získá verzi Chaos plán používá a Chaos plán, který definuje, kdy a jak spustit Chaos.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Nastavte plán používá Chaos.

Chaos se automaticky naplánuje spuštění na základě plánu Chaos. Verze v zadané vstupní plán musí odpovídat verzi Chaos plán na serveru. Pokud poskytnutá verze neodpovídá verzi na serveru, nebude aktualizován v plánu Chaos. Pokud je poskytnutá verze odpovídá verzi na serveru, Chaos plán je aktualizovaný a verzi Chaos plán na serveru se zvýší nahoru o jednu a zabalí zpět na 0, po 2 147 483 647. Pokud Chaos běží, když je toto volání, volání se nezdaří.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --slovník parametrů chaosu | JSON kódovaný seznamu reprezentující mapování z názvů řetězec ChaosParameters používané úlohy. |
| --vypršení platnosti. datum utc | Datum a čas, kdy se mají přestat používat plán naplánování Chaos.  Výchozí\: 9999-12-31T23\:59\:59.999Z. |
| – úlohy | Seznam kódování JSON ChaosScheduleJobs představující, kdy se má spustit Chaos a jaké parametry se mají spustit Chaos s. |
| --start datum utc | Datum a čas, kdy chcete začít používat plán naplánování Chaos.  Výchozí\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| – verze | Číslo verze plánu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

### <a name="examples"></a>Příklady

Následující příkaz nastaví plán (za předpokladu, že aktuální plán má verze 0), který se spustí na 2016-01-01 a končí na položku 2038-01-01, na kterém běží Chaos 24 hodin denně, 7 dní v týdnu. Chaos bude naplánováno na clusteru pro tento čas.

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
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).