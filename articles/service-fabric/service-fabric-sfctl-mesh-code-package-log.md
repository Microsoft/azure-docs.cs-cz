---
title: Azure Service Fabric CLI - sfctl mřížky protokol balíček kódu | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl síť protokol balíček kódu.
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
ms.openlocfilehash: e7bc8491071946eaa2e322517e5d36d681a49130
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837636"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Získáte protokoly pro kontejner balíček zadaný kód pro repliku dané služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| získat | Získá protokoly z kontejneru. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl síť protokol balíček kódu get
Získá protokoly z kontejneru.

Získá protokoly pro kontejner balíček zadaný kód repliku služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – název aplikace – název aplikace [povinné] | Název aplikace. |
| – Kód balíčku název-[povinné] | Název balíčku pro kód služby. |
| --[povinný] název repliky | Název repliky Service Fabric. |
| --[povinný] název služby | Název služby. |
| --Funkce tail | Počet řádků k zobrazení od konce protokoly. Výchozí hodnota je 100. 'všechny zobrazíte kompletní protokoly. |

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