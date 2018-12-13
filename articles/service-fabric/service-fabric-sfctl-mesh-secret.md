---
title: Azure Service Fabric rozhraní příkazového řádku sfctl síť tajný klíč | Dokumentace Microsoftu
description: Popisuje tajných kódů příkazy Service Fabric CLI sfctl sítě.
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
ms.openlocfilehash: e2dc9f815fc578bbe1f01b6b64327ccf23a1aebb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285127"
---
# <a name="sfctl-mesh-secret"></a>tajný kód sfctl sítě
Získání a odstranění tajného kódu prostředků sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní tajných kódů prostředků. |
| list | Vypíše seznam všech tajných kódů prostředků. |
| Show | Získá tajný prostředek s daným názvem. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl síť secret delete
Odstraní tajných kódů prostředků.

Odstraní zadaný prostředek tajných kódů a všechny jeho pojmenovaných hodnot.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| -Název -n [povinné] | Název tajného kódu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-secret-list"></a>seznam tajných kódů sfctl sítě
Vypíše seznam všech tajných kódů prostředků.

Získá informace o všech tajných kódů prostředků v dané skupiny prostředků. Informace obsahují popis a další vlastnosti tajného klíče.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-secret-show"></a>sfctl síť secret show
Získá tajný prostředek s daným názvem.

Získá informace o tajný prostředek s daným názvem. Informace obsahují popis a další vlastnosti tajného klíče.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| -Název -n [povinné] | Název tajného kódu. |

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