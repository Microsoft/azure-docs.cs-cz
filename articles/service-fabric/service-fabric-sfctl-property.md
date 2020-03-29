---
title: Azure Service Fabric CLI- sfctl vlastnost
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro ukládání a dotazování vlastností.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a9bd75e0b7f8bfceb50a71ca83b60ff1e7b45508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905830"
---
# <a name="sfctl-property"></a>sfctl property
Ukládat a dotazovat vlastnosti v části Názvy prostředků infrastruktury služeb.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní zadanou vlastnost Service Fabric. |
| get | Získá zadanou vlastnost Service Fabric. |
| list | Získá informace o všech vlastnostech Service Fabric pod daným názvem. |
| Dát | Vytvoří nebo aktualizuje service fabric vlastnost. |

## <a name="sfctl-property-delete"></a>sfctl, vlastnost delete
Odstraní zadanou vlastnost Service Fabric.

Odstraní zadanou vlastnost Service Fabric pod daným názvem. Vlastnost musí být vytvořena před odstraněním.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --name-id [Povinné] | Název Service Fabric bez schématu IDENTIFIKÁTORURI struktury .\: |
| --property-name [Povinné] | Určuje název vlastnosti, kterou chcete získat. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-property-get"></a>sfctl vlastnost získat
Získá zadanou vlastnost Service Fabric.

Získá zadaný Service Fabric vlastnost pod daný název. Tím se vždy vrátí hodnota a metadata.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --name-id [Povinné] | Název Service Fabric bez schématu IDENTIFIKÁTORURI struktury .\: |
| --property-name [Povinné] | Určuje název vlastnosti, kterou chcete získat. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-property-list"></a>sfctl seznam vlastností
Získá informace o všech vlastnostech Service Fabric pod daným názvem.

Název Service Fabric může mít jednu nebo více pojmenovaných vlastností, které ukládají vlastní informace. Tato operace získá informace o těchto vlastnostech v stránkovaném seznamu. Informace zahrnují název, hodnotu a metadata o každé vlastnosti.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --name-id [Povinné] | Název Service Fabric bez schématu IDENTIFIKÁTORURI struktury .\: |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --include-values | Umožňuje určit, zda mají být zahrnuty hodnoty vrácených vlastností. True, pokud hodnoty by měly být vráceny s metadaty; False vrátit pouze metadata vlastnosti. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-property-put"></a>sfctl vlastnost dát
Vytvoří nebo aktualizuje service fabric vlastnost.

Vytvoří nebo aktualizuje zadanou vlastnost Service Fabric pod daným názvem.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --name-id [Povinné] | Název Service Fabric bez schématu IDENTIFIKÁTORURI struktury .\: |
| --property-name [Povinné] | Název Service Fabric vlastnost. |
| --hodnota [Povinné] | Popisuje hodnotu vlastnosti Service Fabric. Toto je řetězec JSON. <br><br> Řetězec json má dvě pole, "Druh" dat a hodnotu, zadanou jako "Data" dat. Hodnota "Kind" musí být první položkou, která se zobrazí v řetězci JSON, a může být hodnoty Binární, Int64, "Double", "String" nebo Guid. Hodnota by měla být serializovatelné pro dané typy. Hodnoty "Kind" a "Data" by měly být uvedeny jako řetězce. |
| --custom-id-type | Id vlastního typu vlastnosti. Pomocí této vlastnosti je uživatel schopen označit typ hodnoty vlastnosti. |
| --časový čas -t | Výchozí\: 60. |

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