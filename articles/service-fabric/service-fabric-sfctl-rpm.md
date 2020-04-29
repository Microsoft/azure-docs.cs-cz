---
title: Azure Service Fabric CLI – sfctl ot./min.
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro službu Repair Manager.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76904945"
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
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
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
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-rpm-list"></a>seznam sfctl ot./min.
Načte seznam úloh opravy, které odpovídají daným filtrům.

Toto rozhraní API podporuje Service Fabric platformu; není určeno pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --prováděcí modul – filtr | Název prováděcího modulu, jehož deklarované úkoly by měly být uvedeny v seznamu. |
| --State-Filter | Bitové nebo následující hodnoty určující, které stavy úloh by měly být zahrnuty v seznamu výsledků. <ul><li>1 – vytvořeno</li><li>2 – tvrzeno</li><li>4 – Příprava</li><li>8 – schváleno</li><li>16 provedení</li><li>32 – obnovování</li><li>64 – dokončeno</li></ul>
| --Task-ID-Filter | Předpona ID úlohy opravy, která se má shodovat |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: : JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace\:a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).
