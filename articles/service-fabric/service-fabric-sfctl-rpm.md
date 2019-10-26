---
title: Azure Service Fabric CLI – sfctl ot./min. | Microsoft Docs
description: Popisuje příkazy Service Fabric CLI sfctl ot./min.
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
ms.openlocfilehash: 9c37dc8131f14a3b35e68b3e88502c91f96810f6
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901040"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Dotazování a posílání příkazů do služby správce oprav.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| schválit – vynutit | Vynutí schválení dané úlohy opravy. |
| delete | Odstraní dokončenou úlohu opravy. |
| list | Načte seznam úloh opravy, které odpovídají daným filtrům. |

## <a name="sfctl-rpm-approve-force"></a>schválení sfctl ot./min. – Force
Vynutí schválení dané úlohy opravy.

Toto rozhraní API podporuje Service Fabric platformu; není určeno pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Task-ID [povinné] | ID úlohy opravy |
| --verze | Aktuální číslo verze úlohy opravy. Pokud je hodnota nenulová, požadavek bude úspěšný pouze v případě, že tato hodnota odpovídá skutečné aktuální verzi úlohy opravy. Pokud je nula, není provedena žádná kontrolní verze. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-rpm-delete"></a>odstranění sfctl ot./min.
Odstraní dokončenou úlohu opravy.

Toto rozhraní API podporuje Service Fabric platformu; není určeno pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Task-ID [povinné] | ID dokončené úlohy opravy, která se má odstranit |
| --verze | Aktuální číslo verze úlohy opravy. Pokud je hodnota nenulová, požadavek bude úspěšný pouze v případě, že tato hodnota odpovídá skutečné aktuální verzi úlohy opravy. Pokud je nula, není provedena žádná kontrolní verze. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-rpm-list"></a>seznam sfctl ot./min.
Načte seznam úloh opravy, které odpovídají daným filtrům.

Toto rozhraní API podporuje Service Fabric platformu; není určeno pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --prováděcí modul – filtr | Název prováděcího modulu, jehož deklarované úkoly by měly být uvedeny v seznamu. |
| --State-Filter | Bitové nebo následující hodnoty určující, které stavy úloh by měly být zahrnuty v seznamu výsledků. <br> 1 – vytvořeno <br>2 – tvrzeno  <br>4 – Příprava  <br>8 – schváleno  <br>16 provedení  <br>32 – obnovování  <br>64 – dokončeno |
| --Task-ID-Filter | Předpona ID úlohy opravy, která se má shodovat |

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