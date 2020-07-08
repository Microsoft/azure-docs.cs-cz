---
title: Azure Service Fabric CLI – služba sítě sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání podrobností o službě prostředku aplikace.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905921"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Získejte podrobnosti o službě a seznam služeb prostředku aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Description|
| --- | --- |
| list | Zobrazí seznam všech prostředků služby. |
| show | Získá prostředek služby se zadaným názvem. |

## <a name="sfctl-mesh-service-list"></a>seznam služeb sítě sfctl
Zobrazí seznam všech prostředků služby.

Získá informace o všech službách prostředku aplikace. Tyto informace zahrnují popis a další vlastnosti služby.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-service-show"></a>sfctl síť – zobrazení služby
Získá prostředek služby se zadaným názvem.

Načte informace o prostředku služby s daným názvem. Tyto informace zahrnují popis a další vlastnosti služby.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Name-n [povinné] | Název služby |

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