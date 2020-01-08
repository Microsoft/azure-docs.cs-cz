---
title: Azure Service Fabric CLI – vlastnost sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro zadávání scénářů a dotazování na vlastnosti.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: d9129f07db9f3499b51707965d10426dbc3d3c12
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639101"
---
# <a name="sfctl-property"></a>sfctl property
Uložení a dotazování vlastností pod názvy Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní zadanou vlastnost Service Fabric. |
| získat | Získá zadanou vlastnost Service Fabric. |
| list | Získá informace o všech vlastnostech Service Fabric pod daným názvem. |
| put | Vytvoří nebo aktualizuje vlastnost Service Fabric. |

## <a name="sfctl-property-delete"></a>odstranění vlastnosti sfctl
Odstraní zadanou vlastnost Service Fabric.

Odstraní zadanou vlastnost Service Fabric v rámci daného názvu. Vlastnost musí být vytvořena předtím, než bude možné ji odstranit.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-ID [povinné] | Název Service Fabric bez schématu identifikátoru URI\:prostředků infrastruktury |
| --Property-Name [povinné] | Určuje název vlastnosti, která má být získána. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-property-get"></a>sfctl vlastnosti Get
Získá zadanou vlastnost Service Fabric.

Získá zadanou vlastnost Service Fabric pod daným názvem. Tím se vždycky vrátí hodnota i metadata.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-ID [povinné] | Název Service Fabric bez schématu identifikátoru URI\:prostředků infrastruktury |
| --Property-Name [povinné] | Určuje název vlastnosti, která má být získána. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-property-list"></a>seznam vlastností sfctl
Získá informace o všech vlastnostech Service Fabric pod daným názvem.

Název Service Fabric může obsahovat jednu nebo více pojmenovaných vlastností, které ukládají vlastní informace. Tato operace načte informace o těchto vlastnostech v seznamu stránkovaného seznamu. Tyto informace obsahují názvy, hodnoty a metadata o každé z vlastností.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-ID [povinné] | Název Service Fabric bez schématu identifikátoru URI\:prostředků infrastruktury |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --include-Values | Umožňuje určit, jestli se mají zahrnout hodnoty vrácených vlastností. True, pokud by měly být vráceny hodnoty s metadaty; Hodnota false pro vrácení pouze metadat vlastnosti. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-property-put"></a>sfctl vlastnost Put
Vytvoří nebo aktualizuje vlastnost Service Fabric.

Vytvoří nebo aktualizuje zadanou vlastnost Service Fabric v rámci daného názvu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Name-ID [povinné] | Název Service Fabric bez schématu identifikátoru URI\:prostředků infrastruktury |
| --Property-Name [povinné] | Název vlastnosti Service Fabric. |
| --Value [povinné] | Popisuje hodnotu Service Fabric vlastnosti. Toto je řetězec JSON. <br><br> Řetězec JSON má dvě pole, "druh" dat a "hodnotu" dat. Hodnota "Kind" musí být první položka, která se má zobrazit v řetězci JSON, a může mít hodnotu "Binary", "Int64", "Double", "String" nebo "GUID". Hodnota by měla být schopná serializovat zadané typy. Hodnoty "druh" i "data" by měly být zadány jako řetězce. |
| --Custom-ID-– typ | ID vlastního typu vlastnosti Pomocí této vlastnosti může uživatel označit typ hodnoty vlastnosti. |
| --Timeout-t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).