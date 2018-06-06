---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl chaos plán | Microsoft Docs
description: Popisuje plán příkazy chaos sfctl Service Fabric rozhraní příkazového řádku.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 0d09338f71d71d07ab0e037d4736cfaa1f3cff85
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764001"
---
# <a name="sfctl-chaos-schedule"></a>plán chaos sfctl
Získání a nastavení chaos plán.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| GET | Získáte definice, kdy a jak spustit Chaos Chaos plán. |
| nastavení | Nastavte plán Chaos má být používána Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>získat sfctl chaos plán
Získáte definice, kdy a jak spustit Chaos Chaos plán.

Získá verzi plán Chaos používá a Chaos plán, který definuje, kdy a jak spustit Chaos.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos plán sady
Nastavte plán Chaos má být používána Chaos.

Nastavte plán Chaos aktuálně používán podle Chaos. Chaos se automaticky naplánuje spuštění na základě plánu Chaos. Verze v zadané vstupní plánu musí shodovat s verzí Chaos plánu na serveru. Pokud verze dodané neodpovídá verzi na serveru, není aktualizován Chaos plán. Pokud verze dodané odpovídá verzi na serveru, se aktualizuje podle plánu Chaos a verzi Chaos plán na serveru se zvýší až o jednu a zabalí zpět na hodnotu 0, po 2 147 483 647. Pokud Chaos běží, když je proveden toto volání, volání se nezdaří.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --chaos slovník parametrů | JSON kódovaný seznamu reprezentující mapování názvů řetězec k ChaosParameters má být používána úlohy. |
| --vypršení platnosti. datum utc | Datum a čas, kdy se mají přestat používat plán, naplánovat Chaos.  Výchozí\: 9999-12-31T23\:59\:59.999Z. |
| --úlohy | JSON kódovaný seznamu ChaosScheduleJobs představující, kdy se má spustit Chaos a jaké pro spuštění s parametry Chaos s. |
| --start datum utc | Datum a čas, kdy chcete začít používat při plánování Chaos plán.  Výchozí\: 1601-01-01T00\:00\:00.000Z. |
| – verze | Číslo verze plánu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

### <a name="examples"></a>Příklady

Tento příkaz nastaví plán (za předpokladu, že má plán aktuální verze 0), který začíná na 2016-01-01 a vyprší na 2038-01-01, který spouští Chaos 24 hodin denně, 7 dní v týdnu. Chaos bude naplánována na clusteru pro tento čas.

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
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).