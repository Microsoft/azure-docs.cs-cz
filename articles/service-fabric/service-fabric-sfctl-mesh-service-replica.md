---
title: Azure Service Fabric CLI - sfctl mřížky repliku služby | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl síť repliku služby.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 1e0955ef2a52a6313f0449b956229e03f0a5b5e8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662427"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Získáte podrobnosti repliky a seznam replik dané služby v prostředek aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| list | Zobrazí seznam všech replik služby. |
| zobrazit | Získá dané repliky služby aplikace. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl síť repliku služby seznamu
Zobrazí seznam všech replik služby.

Získá informace o všech replik služby. Informace obsahují popis a další vlastnosti repliku služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – název aplikace – název aplikace [povinné] | Název aplikace. |
| --[povinný] název služby | Název služby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl síť repliku služby show
Získá dané repliky služby aplikace.

Získá informace o repliku služby se zadaným názvem. Informace obsahují popis a další vlastnosti repliku služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – název aplikace – název aplikace [povinné] | Název aplikace. |
| -Název -n [povinné] | Název repliku služby. |
| --[povinný] název služby | Název služby. |

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