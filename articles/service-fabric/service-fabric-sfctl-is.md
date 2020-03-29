---
title: Azure Service Fabric CLI- sfctl je
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu infrastruktury.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906076"
---
# <a name="sfctl-is"></a>sfctl is
Dotazovat a odesílat příkazy do služby infrastruktury.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| command | Vyvolá příkaz pro správu v dané instanci služby infrastruktury. |
| query | Vyvolá dotaz jen pro čtení na dané instanci služby infrastruktury. |

## <a name="sfctl-is-command"></a>sfctl je příkaz
Vyvolá příkaz pro správu v dané instanci služby infrastruktury.

Pro clustery, které mají jednu nebo více instancí služby infrastruktury nakonfigurováno, toto rozhraní API poskytuje způsob, jak odeslat příkazy specifické pro infrastrukturu do konkrétní instance služby infrastruktury. Dostupné příkazy a jejich odpovídající formáty odpovědí se liší v závislosti na infrastruktuře, na které je cluster spuštěn. Toto rozhraní API podporuje platformu Service Fabric; není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --command [Povinné] | Text příkazu, který má být vyvolán. Obsah příkazu je specifický pro infrastrukturu. |
| --service-id | Identita služby infrastruktury. <br><br> Toto je úplný název služby infrastruktury bez schématu identifikátoruri\:. Tento parametr je vyžadován pouze pro cluster, který má spuštěno více než jednu instanci služby infrastruktury. |
| --časový čas -t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-is-query"></a>sfctl je dotaz
Vyvolá dotaz jen pro čtení na dané instanci služby infrastruktury.

Pro clustery, které mají jednu nebo více instancí služby infrastruktury nakonfigurováno, toto rozhraní API poskytuje způsob, jak odeslat dotazy specifické pro infrastrukturu na konkrétní instanci služby infrastruktury. Dostupné příkazy a jejich odpovídající formáty odpovědí se liší v závislosti na infrastruktuře, na které je cluster spuštěn. Toto rozhraní API podporuje platformu Service Fabric; není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --command [Povinné] | Text příkazu, který má být vyvolán. Obsah příkazu je specifický pro infrastrukturu. |
| --service-id | Identita služby infrastruktury. <br><br> Toto je úplný název služby infrastruktury bez schématu identifikátoruri\:. Tento parametr je vyžadován pouze pro cluster, který má spuštěno více než jednu instanci služby infrastruktury. |
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
