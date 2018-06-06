---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl je | Microsoft Docs
description: Popisuje rozhraní příkazového řádku služby Fabric sfctl je příkazy.
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
ms.openlocfilehash: aa1e4d588f367351f0bed10370c7d67d50dd9927
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763047"
---
# <a name="sfctl-is"></a>sfctl is
Dotaz a odeslat příkazy ke službě infrastruktury.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| příkaz | Vyvolá příkaz pro správu v dané instanci služby infrastruktury. |
| query | Vyvolá jen pro čtení dotaz na instanci služby dané infrastruktury. |

## <a name="sfctl-is-command"></a>příkaz je sfctl
Vyvolá příkaz pro správu v dané instanci služby infrastruktury.

Toto rozhraní API pro clustery, které mají jednu nebo více instancí služby infrastruktury nakonfigurovali, poskytuje způsob, jak odesílat příkazy specifické pro infrastrukturu na konkrétní instanci služby infrastruktury. Dostupné příkazy a jejich odpovídající formáty odpovědi liší v závislosti na infrastrukturu, na kterém je spuštěn clusteru. Toto rozhraní API podporuje platformy Service Fabric; ji není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – příkaz [potřebný] | Text příkazu, který má být volána. Obsah příkazu je specifické pro infrastrukturu. |
| – id služby | Identita služby infrastruktury. <br><br> Toto je úplný název služby infrastruktury bez "fabric: Schéma identifikátoru URI. Tento parametr vyžaduje se jenom pro cluster, který má více než jednu instanci služby infrastruktury, spuštěná. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-is-query"></a>sfctl je dotazu
Vyvolá jen pro čtení dotaz na instanci služby dané infrastruktury.

Toto rozhraní API pro clustery, které mají jednu nebo více instancí služby infrastruktury nakonfigurovali, poskytuje způsob, jak odesílat dotazy specifické pro infrastrukturu pro konkrétní instanci služby infrastruktury. Dostupné příkazy a jejich odpovídající formáty odpovědi liší v závislosti na infrastrukturu, na kterém je spuštěn clusteru. Toto rozhraní API podporuje platformy Service Fabric; ji není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – příkaz [potřebný] | Text příkazu, který má být volána. Obsah příkazu je specifické pro infrastrukturu. |
| – id služby | Identita služby infrastruktury. <br><br> Toto je úplný název služby infrastruktury bez "fabric\:' schéma identifikátoru URI. Tento parametr vyžaduje se jenom pro cluster, který má více než jednu instanci služby infrastruktury, spuštěná. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).