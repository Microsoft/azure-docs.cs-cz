---
title: Služba azure service fabric CLI- sfctl mesh service
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání podrobností o službě pro prostředek aplikace.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905921"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Získejte podrobnosti o službě a seznam služeb prostředku aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| list | Zobrazí seznam všech prostředků služby. |
| show | Získá prostředek služby s daným názvem. |

## <a name="sfctl-mesh-service-list"></a>seznam síťových služeb sfctl
Zobrazí seznam všech prostředků služby.

Získá informace o všech službách prostředku aplikace. Informace zahrnují popis a další vlastnosti služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název aplikace --název aplikace [Povinné] | Název aplikace |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh servisní show
Získá prostředek služby s daným názvem.

Získá informace o prostředku služby s daným názvem. Informace zahrnují popis a další vlastnosti služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název aplikace --název aplikace [Povinné] | Název aplikace |
| --name -n [Povinné] | Název služby |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) cli service fabric.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).