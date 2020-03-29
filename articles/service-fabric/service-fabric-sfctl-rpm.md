---
title: Azure Service Fabric CLI- sfctl rpm
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro službu správce oprav.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904945"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Dotazujte a odesílejte příkazy službě správce oprav.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| schválit-force | Vynutí schválení daného úkolu opravy. |
| delete | Odstraní dokončenou úlohu opravy. |
| list | Získá seznam úloh opravy odpovídající dané filtry. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm schválit-force
Vynutí schválení daného úkolu opravy.

Toto rozhraní API podporuje platformu Service Fabric; není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --task-id [Povinné] | ID úkolu opravy. |
| --verze | Aktuální číslo verze úkolu opravy. Pokud nenulová, bude požadavek úspěšný pouze v případě, že tato hodnota odpovídá aktuální aktuální verzi úkolu opravy. Pokud nula, pak žádná kontrola verze se provádí. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm odstranit
Odstraní dokončenou úlohu opravy.

Toto rozhraní API podporuje platformu Service Fabric; není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --task-id [Povinné] | ID dokončené opravy úkolu, který má být odstraněn. |
| --verze | Aktuální číslo verze úkolu opravy. Pokud nenulová, bude požadavek úspěšný pouze v případě, že tato hodnota odpovídá aktuální aktuální verzi úkolu opravy. Pokud nula, pak žádná kontrola verze se provádí. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-rpm-list"></a>sfctl rpm seznam
Získá seznam úloh opravy odpovídající dané filtry.

Toto rozhraní API podporuje platformu Service Fabric; není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --executor-filter | Název vykonavatele opravy, jehož nárokované úkoly by měly být zahrnuty do seznamu. |
| --stav-filtr | Bitové nebo následující hodnoty určující, které stavy úkolů by měly být zahrnuty do seznamu výsledků. <ul><li>1 - Vytvořeno</li><li>2 - Nárokováno</li><li>4 - Příprava</li><li>8 - Schváleno</li><li>16 - Provedení</li><li>32 - Obnova</li><li>64 - Dokončeno</li></ul>
| --task-id-filter | Předpona ID úlohy opravy, která má být spárována. |

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
