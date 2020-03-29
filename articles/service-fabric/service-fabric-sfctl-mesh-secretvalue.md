---
title: Azure Service Fabric CLI- sfctl mesh secretvalue
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro získání a odstranění prostředků service fabric mesh secretvalue.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b2fe3a7995cdd88a8f63d0d865e72e9f161cf9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905960"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Získat a odstranit prostředky mřížky secretvalue.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní zadanou hodnotu pojmenovaného tajného prostředku. |
| list | Seznam názvů všech hodnot zadaného tajného prostředku. |
| show | Uvádí zadanou hodnotu tajného prostředku. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mřížka secretvalue odstranit
Odstraní zadanou hodnotu pojmenovaného tajného prostředku.

Odstraní tajný zdroj hodnoty identifikovaný názvem. Název prostředku je obvykle verze přidružená k této hodnotě. Odstranění se nezdaří, pokud je zadaná hodnota používána.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --secret-name -n [Povinné] | Název tajného prostředku. |
| --verze -v [Povinné] | Název tajné verze. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-secretvalue-list"></a>seznam tajných hodnot sítě sfctl
Seznam názvů všech hodnot zadaného tajného prostředku.

Získá informace o všech tajných zdrojů hodnoty zadaného tajného prostředku. Informace obsahuje názvy tajných zdrojů hodnoty, ale nikoli skutečné hodnoty.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --secret-name -n [Povinné] | Název tajného prostředku. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Uvádí zadanou hodnotu tajného prostředku.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --secret-name -n [Povinné] | Název tajného prostředku. |
| --verze -v [Povinné] | Název tajné verze. |
| --show-value | Zobrazit skutečnou hodnotu tajné verze. |

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