---
title: Azure Service Fabric CLI – brána sítě sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání a odstranění Service Fabricch prostředků brány sítě.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3e398ff8a1a0a28fd9d0650a7c9d18ba04817d33
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906013"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Získejte a odstraňte prostředky brány sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Description|
| --- | --- |
| delete | Odstraní prostředek brány. |
| list | Zobrazí seznam všech prostředků brány. |
| show | Získá prostředek brány s daným názvem. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl sítě pro bránu DELETE
Odstraní prostředek brány.

Odstraní prostředek brány identifikovaný názvem.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --Name-n [povinné] | Název prostředku brány |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-gateway-list"></a>seznam bran sítě sfctl
Zobrazí seznam všech prostředků brány.

Načte informace o všech prostředcích brány v dané skupině prostředků. Tyto informace zahrnují popis a další vlastnosti brány.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-gateway-show"></a>Zobrazit bránu sítě sfctl
Získá prostředek brány s daným názvem.

Načte informace o prostředku brány s daným názvem. Tyto informace zahrnují popis a další vlastnosti brány.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --Name-n [povinné] | Název prostředku brány |

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