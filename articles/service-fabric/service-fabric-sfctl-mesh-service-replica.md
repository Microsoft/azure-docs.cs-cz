---
title: Azure Service Fabric CLI – služba sítě sfctl – replika
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání podrobností o replice pro prostředky aplikace.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: cbfdba30663e2aa531ab1db955b0e035a0588709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245717"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Získá podrobnosti o replice a vypíše repliky dané služby v prostředku aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| list | Zobrazí seznam všech replik služby. |
| show | Získá danou repliku služby aplikace. |

## <a name="sfctl-mesh-service-replica-list"></a>Služba sítě sfctl – seznam replik
Zobrazí seznam všech replik služby.

Získá informace o všech replikách služby. Tyto informace zahrnují popis a další vlastnosti repliky služby.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Service-Name [povinné] | Název služby |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-service-replica-show"></a>Služba sítě sfctl – zobrazení repliky
Získá danou repliku služby aplikace.

Načte informace o replice služby se zadaným názvem. Tyto informace zahrnují popis a další vlastnosti repliky služby.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --App-Name--název aplikace [povinné] | Název aplikace |
| --Name-n [povinné] | Název repliky služby. |
| --Service-Name [povinné] | Název služby |

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
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
