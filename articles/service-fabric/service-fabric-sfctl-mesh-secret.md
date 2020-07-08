---
title: Azure Service Fabric CLI – tajný kód sítě sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání a odstranění Service Fabricch tajných informací o mřížkách.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fab388ff223eb95020e2ba0945c76532bc54f224
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905980"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Získá a odstraní prostředky tajného klíče sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Description|
| --- | --- |
| delete | Odstraní prostředek tajného kódu. |
| list | Zobrazí seznam všech tajných prostředků. |
| show | Získá prostředek tajného kódu se zadaným názvem. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl odstranit tajný kód sítě
Odstraní prostředek tajného kódu.

Odstraní zadaný tajný prostředek a všechny jeho pojmenované hodnoty.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --Name-n [povinné] | Název tajného prostředku |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-secret-list"></a>sfctl sítě – seznam tajných klíčů
Zobrazí seznam všech tajných prostředků.

Načte informace o všech tajných zdrojích v dané skupině prostředků. Tyto informace obsahují popis a další vlastnosti tajného klíče.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-secret-show"></a>zobrazení tajného kódu sítě sfctl
Získá prostředek tajného kódu se zadaným názvem.

Načte informace o prostředku tajného kódu se zadaným názvem. Tyto informace obsahují popis a další vlastnosti tajného klíče.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --Name-n [povinné] | Název tajného prostředku |

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