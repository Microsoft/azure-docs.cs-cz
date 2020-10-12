---
title: Azure Service Fabric CLI – vlastnost sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro ukládání a dotazování vlastností.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0a5ebd4822c5f0ff1735464bb4d5b42c436ee529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260320"
---
# <a name="sfctl-property"></a>sfctl property
Uložení a dotazování vlastností pod názvy Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní zadanou vlastnost Service Fabric. |
| get | Získá zadanou vlastnost Service Fabric. |
| list | Získá informace o všech vlastnostech Service Fabric pod daným názvem. |
| převést | Vytvoří nebo aktualizuje vlastnost Service Fabric. |

## <a name="sfctl-property-delete"></a>odstranění vlastnosti sfctl
Odstraní zadanou vlastnost Service Fabric.

Odstraní zadanou vlastnost Service Fabric v rámci daného názvu. Vlastnost musí být vytvořena předtím, než bude možné ji odstranit.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Name-ID [povinné] | Název Service Fabric bez \: schématu identifikátoru URI prostředků infrastruktury |
| --Property-Name [povinné] | Určuje název vlastnosti, která má být získána. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-property-get"></a>sfctl vlastnosti Get
Získá zadanou vlastnost Service Fabric.

Získá zadanou vlastnost Service Fabric pod daným názvem. Tím se vždycky vrátí hodnota i metadata.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Name-ID [povinné] | Název Service Fabric bez \: schématu identifikátoru URI prostředků infrastruktury |
| --Property-Name [povinné] | Určuje název vlastnosti, která má být získána. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-property-list"></a>seznam vlastností sfctl
Získá informace o všech vlastnostech Service Fabric pod daným názvem.

Název Service Fabric může obsahovat jednu nebo více pojmenovaných vlastností, které ukládají vlastní informace. Tato operace načte informace o těchto vlastnostech v seznamu stránkovaného seznamu. Tyto informace obsahují názvy, hodnoty a metadata o každé z vlastností.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Name-ID [povinné] | Název Service Fabric bez \: schématu identifikátoru URI prostředků infrastruktury |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --include-Values | Umožňuje určit, jestli se mají zahrnout hodnoty vrácených vlastností. True, pokud by měly být vráceny hodnoty s metadaty; Hodnota false pro vrácení pouze metadat vlastnosti. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-property-put"></a>sfctl vlastnost Put
Vytvoří nebo aktualizuje vlastnost Service Fabric.

Vytvoří nebo aktualizuje zadanou vlastnost Service Fabric v rámci daného názvu.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Name-ID [povinné] | Název Service Fabric bez \: schématu identifikátoru URI prostředků infrastruktury |
| --Property-Name [povinné] | Název vlastnosti Service Fabric. |
| --Value [povinné] | Popisuje hodnotu Service Fabric vlastnosti. Toto je řetězec JSON. <br><br> Řetězec JSON má dvě pole, "druh" dat a hodnotu, která byla zadána jako "data" dat. Hodnota "Kind" musí být první položka, která se má zobrazit v řetězci JSON, a může mít hodnotu "Binary", "Int64", "Double", "String" nebo "GUID". Hodnota by měla být schopná serializovat zadané typy. Hodnoty "druh" i "data" by měly být zadány jako řetězce. |
| --Custom-ID-– typ | ID vlastního typu vlastnosti Pomocí této vlastnosti může uživatel označit typ hodnoty vlastnosti. |
| --Timeout-t | Výchozí hodnota je \: 60. |

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
