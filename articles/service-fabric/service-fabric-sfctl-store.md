---
title: Úložiště CLI- sfcTL azure service fabric
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro provádění operací na úrovni souborů v úložišti bitových obrazových obrázků clusteru.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 75c62b54ff3aa7f3af344aa3e1ca81d431ae0ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905626"
---
# <a name="sfctl-store"></a>sfctl store
Provádějte základní operace na úrovni souborů v úložišti bitových obrazů clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní existující obsah úložiště obrázků. |
| kořenové informace | Získá informace o obsahu v kořenovém adresáři úložiště obrázků. |
| Stat | Získá informace o obsahu úložiště obrázků. |

## <a name="sfctl-store-delete"></a>sfctl úložiště odstranit
Odstraní existující obsah úložiště obrázků.

Odstraní existující obsah úložiště obrázků, který se nachází v relativní cestě k danému úložišti obrázků. Tento příkaz lze odstranit nahrané balíčky aplikací po jejich zřízení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --cesta obsahu [Povinné] | Relativní cesta k souboru nebo složce v úložišti obrázků z jeho kořenového adresáře. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-store-root-info"></a>sfctl úložiště kořen-info
Získá informace o obsahu v kořenovém adresáři úložiště obrázků.

Vrátí informace o obsahu úložiště obrázků v kořenovém adresáři úložiště obrázků.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-store-stat"></a>sfctl obchod stat
Získá informace o obsahu úložiště obrázků.

Vrátí informace o obsahu úložiště obrázků na zadaný contentPath. ContentPath je relativní ke kořenu úložiště obrázků.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --cesta obsahu [Povinné] | Relativní cesta k souboru nebo složce v úložišti obrázků z jeho kořenového adresáře. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Setup](service-fabric-cli.md) the Service Fabric CLI.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).