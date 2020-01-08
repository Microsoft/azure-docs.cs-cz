---
title: Azure Service Fabric CLI – sfctl je
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu infrastruktury.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: c3c98e8da0c2a5449296a0fd108977ec0633f83d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639118"
---
# <a name="sfctl-is"></a>sfctl is
Dotazování a posílání příkazů do služby infrastruktury

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| command | Vyvolá příkaz pro správu v dané instanci služby infrastruktury. |
| query | Vyvolá dotaz jen pro čtení v dané instanci služby infrastruktury. |

## <a name="sfctl-is-command"></a>sfctl je příkaz
Vyvolá příkaz pro správu v dané instanci služby infrastruktury.

V případě clusterů, které mají nakonfigurovanou jednu nebo více instancí služby infrastruktury, poskytuje toto rozhraní API způsob, jak do konkrétní instance služby infrastruktury odeslat příkazy specifické pro infrastrukturu. Dostupné příkazy a jejich odpovídající formáty odpovědi se liší v závislosti na infrastruktuře, na které je cluster spuštěný. Toto rozhraní API podporuje Service Fabric platformu; není určeno pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --příkaz [povinné] | Text příkazu, který má být vyvolán. Obsah příkazu je závislý na infrastruktuře. |
| --service-id | Identita služby infrastruktury <br><br> Toto je úplný název služby infrastruktury bez schématu identifikátoru URI\:Fabric. Tento parametr je vyžadován pouze pro cluster, který má spuštěnu více než jednu instanci služby infrastruktury. |
| --Timeout-t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-is-query"></a>sfctl je dotaz
Vyvolá dotaz jen pro čtení v dané instanci služby infrastruktury.

V případě clusterů, které mají nakonfigurovanou jednu nebo více instancí služby infrastruktury, poskytuje toto rozhraní API způsob, jak odesílat dotazy specifické pro infrastrukturu do konkrétní instance služby infrastruktury. Dostupné příkazy a jejich odpovídající formáty odpovědi se liší v závislosti na infrastruktuře, na které je cluster spuštěný. Toto rozhraní API podporuje Service Fabric platformu; není určeno pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --příkaz [povinné] | Text příkazu, který má být vyvolán. Obsah příkazu je závislý na infrastruktuře. |
| --service-id | Identita služby infrastruktury <br><br> Toto je úplný název služby infrastruktury bez schématu identifikátoru URI\:Fabric. Tento parametr je vyžadován pouze pro cluster, který má spuštěnu více než jednu instanci služby infrastruktury. |
| --Timeout-t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).