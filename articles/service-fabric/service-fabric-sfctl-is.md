---
title: Azure Service Fabric CLI – sfctl je
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu infrastruktury.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906076"
---
# <a name="sfctl-is"></a>sfctl is
Dotazování a posílání příkazů do služby infrastruktury

## <a name="commands"></a>Příkazy

|Příkaz|Description|
| --- | --- |
| command | Vyvolá příkaz pro správu v dané instanci služby infrastruktury. |
| query | Vyvolá dotaz jen pro čtení v dané instanci služby infrastruktury. |

## <a name="sfctl-is-command"></a>sfctl je příkaz
Vyvolá příkaz pro správu v dané instanci služby infrastruktury.

V případě clusterů, které mají nakonfigurovanou jednu nebo více instancí služby infrastruktury, poskytuje toto rozhraní API způsob, jak do konkrétní instance služby infrastruktury odeslat příkazy specifické pro infrastrukturu. Dostupné příkazy a jejich odpovídající formáty odpovědi se liší v závislosti na infrastruktuře, na které je cluster spuštěný. Toto rozhraní API podporuje Service Fabric platformu; není určeno pro použití přímo v kódu.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --příkaz [povinné] | Text příkazu, který má být vyvolán. Obsah příkazu je závislý na infrastruktuře. |
| --Service-ID | Identita služby infrastruktury <br><br> Toto je úplný název služby infrastruktury bez \: schématu identifikátoru URI Fabric. Tento parametr je vyžadován pouze pro cluster, který má spuštěnu více než jednu instanci služby infrastruktury. |
| --Timeout-t | Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-is-query"></a>sfctl je dotaz
Vyvolá dotaz jen pro čtení v dané instanci služby infrastruktury.

V případě clusterů, které mají nakonfigurovanou jednu nebo více instancí služby infrastruktury, poskytuje toto rozhraní API způsob, jak odesílat dotazy specifické pro infrastrukturu do konkrétní instance služby infrastruktury. Dostupné příkazy a jejich odpovídající formáty odpovědi se liší v závislosti na infrastruktuře, na které je cluster spuštěný. Toto rozhraní API podporuje Service Fabric platformu; není určeno pro použití přímo v kódu.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --příkaz [povinné] | Text příkazu, který má být vyvolán. Obsah příkazu je závislý na infrastruktuře. |
| --Service-ID | Identita služby infrastruktury <br><br> Toto je úplný název služby infrastruktury bez \: schématu identifikátoru URI Fabric. Tento parametr je vyžadován pouze pro cluster, který má spuštěnu více než jednu instanci služby infrastruktury. |
| --Timeout-t | Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).
