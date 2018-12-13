---
title: Azure Service Fabric CLI sfctl ot. / min | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl ot. / min.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: a8ac3899b6ae80668c4472f24b508ba6ba7e2644
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275298"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Dotazování a posílání příkazů do service Manageru opravit.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| platnost schválení | Vynutí schválení úloha dané opravy. |
| delete | Odstraní úlohu dokončené opravit. |
| list | Získá seznam úkolů opravit odpovídajících uvedeným filtrům. |

## <a name="sfctl-rpm-approve-force"></a>Schválit ot. / min sfctl-force
Vynutí schválení úloha dané opravy.

Toto rozhraní API podporuje platformy Service Fabric; není určena pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id úlohy [povinné] | ID úloha opravy. |
| – verze | Aktuální číslo verze úloha opravy. Pokud nenulová, pak žádost bude úspěšné, pouze pokud tato hodnota odpovídá skutečné aktuální verzi úloha opravy. Pokud je nula, je provedena žádná kontrola verzí. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-rpm-delete"></a>Odstranit sfctl ot. / min
Odstraní úlohu dokončené opravit.

Toto rozhraní API podporuje platformy Service Fabric; není určena pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id úlohy [povinné] | ID úkolu dokončení opravy odstranit. |
| – verze | Aktuální číslo verze úloha opravy. Pokud nenulová, pak žádost bude úspěšné, pouze pokud tato hodnota odpovídá skutečné aktuální verzi úloha opravy. Pokud je nula, je provedena žádná kontrola verzí. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-rpm-list"></a>seznam sfctl ot. / min
Získá seznam úkolů opravit odpovídajících uvedeným filtrům.

Toto rozhraní API podporuje platformy Service Fabric; není určena pro použití přímo v kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --prováděcího modulu filtru | Název opravit prováděcího modulu, jehož jste požádali úkoly, měly by být součástí seznamu. |
| --Filtr stavu | Bitový OR následující hodnoty určující, který úkol stavy by měl být součástí seznam výsledků. <br> 1 – Vytvoření <br>2 - uplatněné  <br>4 – Příprava  <br>8 – schválené  <br>16 – provádění  <br>32 - obnovení  <br>64 - dokončeno |
| --Filtr id úkolu | Oprava předpona ID úlohy lze porovnat. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |


## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).