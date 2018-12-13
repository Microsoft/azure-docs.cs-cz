---
title: Service Fabric CLI sfctl sítě služby Azure | Dokumentace Microsoftu
description: Popisuje příkazy služby Service Fabric CLI sfctl sítě.
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
ms.openlocfilehash: 6c40cf2340568e704a5c76ad367c07c85826943b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284798"
---
# <a name="sfctl-mesh-service"></a>sfctl sítě služby
Získejte podrobnosti o službě a seznam služeb prostředek aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| list | Vypíše seznam všech prostředků služby. |
| Show | Získá prostředek služby se zadaným názvem. |

## <a name="sfctl-mesh-service-list"></a>seznam služeb sítě sfctl
Vypíše seznam všech prostředků služby.

Získá informace o všech službách prostředek aplikace. Informace obsahují popis a další vlastnosti služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – název aplikace – název aplikace [povinné] | Název aplikace. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-service-show"></a>sfctl sítě služby show
Získá prostředek služby se zadaným názvem.

Získá informace o prostředku služby se zadaným názvem. Informace obsahují popis a další vlastnosti služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – název aplikace – název aplikace [povinné] | Název aplikace. |
| -Název -n [povinné] | Název služby. |

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