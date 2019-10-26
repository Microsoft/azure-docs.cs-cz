---
title: Azure Service Fabric CLI – vlastnost sfctl | Microsoft Docs
description: Popisuje příkazy vlastnosti Service Fabric CLI sfctl.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 368dad54c611e4532b46f11669bcf8e363b9a740
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901062"
---
# <a name="sfctl-property"></a>sfctl property
Uložení a dotazování vlastností pod názvy Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| delete | Odstraní zadanou vlastnost Service Fabric. |
| Čtěte | Získá zadanou vlastnost Service Fabric. |
| list | Získá informace o všech vlastnostech Service Fabric pod daným názvem. |
| převést | Vytvoří nebo aktualizuje vlastnost Service Fabric. |

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