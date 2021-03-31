---
title: Síť sítě Azure Service Fabric CLI – sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání a odstranění Service Fabric síťových prostředků sítě.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b2b2c444bb492fa6c6b945a82090e91963fb1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245856"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Získat a odstranit síťové prostředky sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní síťový prostředek. |
| list | Zobrazí seznam všech síťových prostředků. |
| show | Získá síťový prostředek s daným názvem. |

## <a name="sfctl-mesh-network-delete"></a>odstranění sítě v síti sfctl
Odstraní síťový prostředek.

Odstraní síťový prostředek identifikovaný názvem.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název sítě. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-network-list"></a>seznam sítí sítě sfctl
Zobrazí seznam všech síťových prostředků.

Načte informace o všech síťových prostředcích v dané skupině prostředků. Tyto informace zahrnují popis a další vlastnosti sítě.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-network-show"></a>sfctl síť sítě – zobrazení
Získá síťový prostředek s daným názvem.

Načte informace o síťovém prostředku s daným názvem. Tyto informace zahrnují popis a další vlastnosti sítě.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-n [povinné] | Název sítě. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
