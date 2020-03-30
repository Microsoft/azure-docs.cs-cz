---
title: Aplikace Azure Service Fabric CLI- sfctl mesh
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu prostředků síťové aplikace Service Fabric.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7c571136e6bef64c2024c81db85ef736f64a4e01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906069"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
Získat a odstranit prostředky aplikace.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní prostředek aplikace. |
| list | Zobrazí seznam všech prostředků aplikace. |
| show | Získá prostředek aplikace s daným názvem. |

## <a name="sfctl-mesh-app-delete"></a>sfctl mesh app delete
Odstraní prostředek aplikace.

Odstraní prostředek aplikace identifikovaný názvem.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --name -n [Povinné] | Název aplikace |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-app-list"></a>seznam síťových aplikací sfctl
Zobrazí seznam všech prostředků aplikace.

Získá informace o všech prostředků aplikace v dané skupině prostředků. Informace zahrnují popis a další vlastnosti Aplikace.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-app-show"></a>sfctl mesh app show
Získá prostředek aplikace s daným názvem.

Získá informace o prostředku aplikace s daným názvem. Informace zahrnují popis a další vlastnosti Aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --name -n [Povinné] | Název aplikace |

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