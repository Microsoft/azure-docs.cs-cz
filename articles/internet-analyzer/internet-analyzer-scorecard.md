---
title: Interpretace přehledu výkonnostních metrik | Dokumenty společnosti Microsoft
description: Nejčastější dotazy k nástroji Azure Internet Analyzer.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512883"
---
# <a name="interpreting-your-scorecard"></a>Interpretace přehledu výkonnostních metrik

Karta přehledu výkonnostních metrik obsahuje agregované a analyzované výsledky testů. Každý test má své vlastní přehledy výkonnostních metrik. Přehledy výkonnostních metrik poskytují rychlé a smysluplné souhrny výsledků měření, které poskytují výsledky založené na datech pro vaše síťové požadavky. Internet Analyzer se stará o analýzu, což vám umožní soustředit se na rozhodnutí.

Kartu přehledu výkonnostních metrik naleznete v nabídce zdrojů Nástroje pro analýzu internetu. 


## <a name="filters"></a>Filtry

* ***Test:*** Vyberte test, pro který chcete zobrazit výsledky – každý test má svůj vlastní přehled výkonnostních metrik. Testovací data se objeví, jakmile je dostatek dat k dokončení analýzy – ve většině případů by to mělo být do 24 hodin. 
* ***Časové období & koncové datum:*** Každý den jsou generovány tři přehledy výkonnostních metrik – každý přehled výkonnostních metrik odráží jiné období agregace – 24 hodin před (den), sedm dní před (týden) a 30 dní před (měsíc). Pomocí filtru Koncové datum vyberte poslední den časového období, které chcete zobrazit. 
* ***Země:*** Pro každou zemi, ve které máte koncové uživatele, se vygeneruje přehled výkonnostních metrik. Globální filtr obsahuje všechny koncové uživatele.

## <a name="measurement-count"></a>Počet měření

Počet měření ovlivňuje spolehlivost analýzy. Čím vyšší je počet, tím přesnější je výsledek. Zkoušky by měly být zaměřeny minimálně na 100 měření na cílový parametr za den. Pokud jsou počty měření příliš nízké, nakonfigurujte klienta JavaScriptu tak, aby se ve vaší aplikaci spouštěl častěji. Počty měření pro koncové body A a B by měly být velmi podobné, i když se očekávají malé rozdíly a jsou v pořádku. V případě velkých rozdílů by výsledky neměly být důvěryhodné.

## <a name="percentiles"></a>Percentily

Latence, měřená v milisekundách, je oblíbenou metrikou pro měření rychlosti mezi zdrojem a cílem na Internetu. Data latence nejsou obvykle distribuována (tj. nesleduje "Bell Curve"), protože existuje "long-tail" velkých hodnot latence, které zkosení výsledků při použití statistiky, jako je například aritmetický průměr. Jako alternativu percentily poskytují "bez distribuce" způsob analýzy dat. Jako příklad medián nebo 50 percentil shrnuje střed rozdělení - polovina hodnot je nad ním a polovina je pod ním. 75 hodnota percentilu znamená, že je větší než 75 % všech hodnot v rozdělení. Internet Analyzer odkazuje na percentily v zkratka jako P50, P75 a P95.

Percentily nástroje Internet Analyzer jsou _ukázkové metriky_. To je na rozdíl od skutečné _metriky populace_. Například denní skutečná střední latence populace mezi studenty na Univerzitě jižní Kalifornie a Microsoftu je střední hodnota latence všech požadavků během tohoto dne. V praxi je měření hodnoty všech požadavků nepraktické, takže předpokládáme, že přiměřeně velký vzorek je reprezentativní pro skutečnou populaci.

Pro účely analýzy P50 (medián), je užitečné jako očekávaná hodnota pro distribuci latence. Vyšší percentily, například P95, jsou užitečné pro identifikaci, jak vysoká latence je v nejhorších případech. Pokud máte zájem pochopit latenci zákazníků obecně, P50 je správná metrika, na kterou se zaměřit. Pokud se zajímáte o pochopení výkonu pro zákazníky s nejhorším výkonem, pak p95 by měla být zaměřena. P75 je rovnováha mezi těmito dvěma.


## <a name="deltas"></a>Delty

Rozdíl je rozdíl v hodnotách metrik pro koncové body A a B. Rozdíly jsou vypočteny tak, aby zobrazovaly přínos B oproti A. Kladné hodnoty označují, že B bylo provedeno lépe než A, zatímco záporné hodnoty označují, že výkon B je horší. Delta mohou být absolutní (např. 10 milisekund) nebo relativní (5 %).

## <a name="confidence-interval"></a>Interval spolehlivosti 

Intervaly spolehlivosti (CI) jsou rozsah hodnot, které mají pravděpodobnost, že obsahují metriku základního souboru, například medián, P75 nebo průměr. Řídíme se společnou statistickou konvencí používání 95% CI.

Pro Internet Analyzer úzký interval spolehlivosti je dobré, protože ukazuje, že metrika vzorku je pravděpodobně velmi blízko k metriky skutečné populace. Široký interval spolehlivosti znamená menší jistotu, že naše metrika vzorku odráží skutečnou metriku populace. Nejlepší způsob, jak zlepšit CI, je zvýšit počet měření.

## <a name="time-series"></a>Časové řady 

Časová řada ukazuje, jak se metrika v průběhu času mění. Na internetu existuje mnoho časových faktorů, které ovlivňují výkon, jako jsou období špičkového provozu, rozdíly v populaci ve všední den a víkend a svátky.


## <a name="next-steps"></a>Další kroky

Další informace naleznete v našem [přehledu nástroje Internet Analyzer](internet-analyzer-overview.md).
