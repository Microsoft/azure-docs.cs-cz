---
title: Azure Service Fabric rozhraní příkazového řádku sfctl ot | Microsoft Docs
description: Popisuje příkazy Service Fabric rozhraní příkazového řádku sfctl ot. / min.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 5e7cf1b28e8f38f0539e0084e1f745e0dab4dec3
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763608"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Dotaz a odesílat příkazy na service manager opravit.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| platnost schválení | Vynutí schválení úloha dané opravy. |
| odstraňovat | Odstraní úloha dokončena opravy. |
| Seznam | Získá seznam úloh oprava odpovídající dané filtry. |

## <a name="sfctl-rpm-approve-force"></a>Schválit rpm sfctl-force
Vynutí schválení úloha dané opravy.

Toto rozhraní API podporuje platformy Service Fabric; ji není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id úlohy [vyžaduje] | ID úloha opravy. |
| – verze | Aktuální číslo verze úloha opravy. Pokud nulová, pak požadavek bude úspěšné, pouze pokud tato hodnota odpovídá skutečným aktuální verzi úloha opravy. Pokud nula, bude provedena žádná kontrola verze. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-rpm-delete"></a>Odstranit sfctl ot. / min
Odstraní úloha dokončena opravy.

Toto rozhraní API podporuje platformy Service Fabric; ji není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id úlohy [vyžaduje] | ID úloha dokončena opravy k odstranění. |
| – verze | Aktuální číslo verze úloha opravy. Pokud nulová, pak požadavek bude úspěšné, pouze pokud tato hodnota odpovídá skutečným aktuální verzi úloha opravy. Pokud nula, bude provedena žádná kontrola verze. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-rpm-list"></a>sfctl rpm seznamu
Získá seznam úloh oprava odpovídající dané filtry.

Toto rozhraní API podporuje platformy Service Fabric; ji není určen pro použití přímo z vašeho kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --prováděcího modulu filtru | Název opravy prováděcího modulu, jehož deklarovaný úlohy by měl být součástí seznamu. |
| --Filtr stavu | Seznam výsledků by měl být součástí bitové operace OR následující hodnoty určující, který úkol stavy. <br> 1 – Vytvoření <br>2 – vyžádaná  <br>4 – Příprava  <br>8 – schválení  <br>16 – provádění  <br>32 - obnovení  <br>64 - dokončena |
| --Filtr id úkolu | Oprava předpona ID úloh lze porovnat. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).