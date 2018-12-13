---
title: Azure Service Fabric CLI - sfctl sítě brány | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl sítě brány.
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
ms.openlocfilehash: 7738f838fade47a486b33d808ee2938101406763
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284805"
---
# <a name="sfctl-mesh-gateway"></a>sfctl brány sítě
Získání a odstranění prostředků brány sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní prostředek brány. |
| list | Vypíše seznam všech prostředků brány. |
| Show | Získá prostředek brány s daným názvem. |

## <a name="sfctl-mesh-gateway-delete"></a>Odstranit bránu sítě sfctl
Odstraní prostředek brány.

Odstraní prostředek brány určený podle názvu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| -Název -n [povinné] | Název prostředku brány. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-gateway-list"></a>seznam bran sítě sfctl
Vypíše seznam všech prostředků brány.

Získá informace o všech prostředků brány v dané skupiny prostředků. Informace obsahují popis a další vlastnosti brány.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl sítě brány show
Získá prostředek brány s daným názvem.

Získá informace o prostředku brány se zadaným názvem. Informace obsahují popis a další vlastnosti brány.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| -Název -n [povinné] | Název prostředku brány. |

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