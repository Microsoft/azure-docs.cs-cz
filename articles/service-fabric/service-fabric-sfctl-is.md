---
title: Azure Service Fabric CLI - sfctl je | Dokumentace Microsoftu
description: Popisuje, Service Fabric CLI sfctl je příkazy.
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
ms.openlocfilehash: abc1e835fa153fc5d061cca5a3eb009931240332
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276328"
---
# <a name="sfctl-is"></a>sfctl is
Dotazování a posílání příkazů do služeb infrastruktury.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| command | Vyvolá příkaz pro správu v dané instanci služby infrastruktury. |
| query | Vyvolá dotazu jen pro čtení na instanci služby dané infrastruktury. |

## <a name="sfctl-is-command"></a>sfctl je příkaz
Vyvolá příkaz pro správu v dané instanci služby infrastruktury.

Pro clustery, které mají jednu nebo více instancí služby infrastruktury nakonfigurované toto rozhraní API poskytuje způsob, jak odesílat příkazy specifické pro infrastrukturu k určité instanci služby infrastruktury. Dostupné příkazy a jejich odpovídající odpověď formáty lišit v závislosti na infrastrukturu, na kterém je spuštěný cluster. Toto rozhraní API podporuje platformy Service Fabric; není určena pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – příkaz [povinné] | Text příkazu, který má být volána. Obsah příkazu je specifické pro infrastrukturu. |
| – id služby | Identita služeb infrastruktury. <br><br> Toto je úplný název služeb infrastruktury bez "fabric\:' schéma identifikátoru URI. Tento parametr se vyžaduje jenom pro cluster, který má více než jednu instanci služby infrastruktury, které jsou spuštěné. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-is-query"></a>sfctl je dotaz
Vyvolá dotazu jen pro čtení na instanci služby dané infrastruktury.

Toto rozhraní API poskytuje pro clustery, které mají jednu nebo více instancí služeb infrastruktury, které jsou nakonfigurované tak, aby odesílal specifické pro infrastrukturu dotazy na konkrétní instanci služby infrastruktury. Dostupné příkazy a jejich odpovídající odpověď formáty lišit v závislosti na infrastrukturu, na kterém je spuštěný cluster. Toto rozhraní API podporuje platformy Service Fabric; není určena pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – příkaz [povinné] | Text příkazu, který má být volána. Obsah příkazu je specifické pro infrastrukturu. |
| – id služby | Identita služeb infrastruktury. <br><br> Toto je úplný název služeb infrastruktury bez "fabric\:' schéma identifikátoru URI. Tento parametr se vyžaduje jenom pro cluster, který má více než jednu instanci služby infrastruktury, které jsou spuštěné. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |


## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).