---
title: Azure Service Fabric CLI - sfctl síť aplikace | Dokumentace Microsoftu
description: Popisuje příkazy aplikace Service Fabric CLI sfctl sítě.
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
ms.openlocfilehash: 3c2194f6a001e4fc49dcf1694f8a9cda41550ace
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284833"
---
# <a name="sfctl-mesh-app"></a>sfctl aplikace sítě
Získání a odstranění prostředků aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní prostředek služby Application. |
| list | Vypíše seznam všech prostředků aplikace. |
| Show | Získá prostředek aplikace s daným názvem. |

## <a name="sfctl-mesh-app-delete"></a>sfctl sítě app delete
Odstraní prostředek služby Application.

Odstraní prostředek služby Application určený podle názvu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| -Název -n [povinné] | Název aplikace. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-app-list"></a>sfctl seznamu aplikací sítě
Vypíše seznam všech prostředků aplikace.

Získá informace o všech prostředků aplikace v dané skupiny prostředků. Informace obsahují popis a další vlastnosti aplikace.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-app-show"></a>sfctl sítě app show
Získá prostředek aplikace s daným názvem.

Získá informace o prostředku aplikace se zadaným názvem. Informace obsahují popis a další vlastnosti aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| -Název -n [povinné] | Název aplikace. |

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