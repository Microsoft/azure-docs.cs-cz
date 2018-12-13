---
title: Azure Service Fabric CLI - sfctl síť secretvalue | Dokumentace Microsoftu
description: Popisuje secretvalue příkazy Service Fabric CLI sfctl sítě.
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
ms.openlocfilehash: b8172ba50f7fec8f0aa1ba768c05d087ecabf8c8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285414"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl secretvalue sítě
Získání a odstraňte prostředky secretvalue sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní zadanou hodnotu tajného kódu s názvem prostředku. |
| list | Vypsat názvy všech hodnot zadaný prostředek tajného kódu. |
| Show | Načte hodnotu určenou verzi tajného kódu prostředků. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl síť secretvalue delete
Odstraní zadanou hodnotu tajného kódu s názvem prostředku.

Odstraní určený podle názvu prostředku tajná hodnota. Název prostředku je obvykle spojené s touto hodnotou verze. Odstranění se nezdaří, pokud zadaná hodnota je používán.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název tajného kódu - n [povinné] | Název tajného kódu prostředku. |
| – verze - v [povinné] | Název tajného kódu verze. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl síť secretvalue seznamu
Vypsat názvy všech hodnot zadaný prostředek tajného kódu.

Získá informace o všech prostředků tajná hodnota zadaného tajného kódu prostředku. Tyto informace zahrnují názvy tajná hodnota prostředky, ale nikoli skutečné hodnoty.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název tajného kódu - n [povinné] | Název tajného kódu prostředku. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl síť secretvalue show
Načte hodnotu určenou verzi tajného kódu prostředků.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název tajného kódu - n [povinné] | Název tajného kódu prostředku. |
| – verze - v [povinné] | Název tajného kódu verze. |
| --show hodnota | Zobrazit skutečnou hodnotu verzi tajného klíče. |

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