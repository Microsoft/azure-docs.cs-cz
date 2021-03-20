---
title: Interpretace přehledu výkonnostních metrik | Microsoft Docs
description: Naučte se interpretovat scorecard. Karta Scorecard obsahuje agregované a analyzované výsledky testů.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f43d094193fb266d1ecec7089b44d8b3fd5e9b43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91330209"
---
# <a name="interpreting-your-scorecard"></a>Interpretace přehledu výkonnostních metrik

Karta Scorecard obsahuje agregované a analyzované výsledky testů. Každý test má své vlastní přehledy. Přehledy výkonnostních metrik poskytují rychlé a smysluplné souhrny výsledků měření, které poskytují výsledky založené na datech pro vaše síťové požadavky. Internet Analyzer se stará o analýzu, takže se můžete soustředit na rozhodnutí.

Kartu Scorecard najdete v nabídce prostředků nástroje Internet Analyzer. 


## <a name="filters"></a>Filtry

* ***Test:*** Vyberte test, pro který chcete zobrazit výsledky – každý test má svůj vlastní přehled výkonnostních metrik. Data testů se zobrazí, jakmile bude k dispozici dostatek dat k dokončení analýzy – ve většině případů by to mělo být do 24 hodin. 
* ***Časové období & koncové datum:*** Tři přehledy výkonnostních metrik jsou vygenerovány každý den – každý Scorecard odráží jiné agregované období – 24 hodin před (dnem), sedm dní před (týden) a 30 dní před (měsíc). Pomocí filtru "koncové datum" vyberte poslední den časového období, které chcete zobrazit. 
* ***Země:*** Pro každou zemi, ve které máte koncoví uživatelé, se vygeneruje scorecard. Globální filtr obsahuje všechny koncové uživatele.

## <a name="measurement-count"></a>Počet měření

Počet měření ovlivňuje spolehlivost analýzy. Čím vyšší je počet, tím přesnější výsledek. Minimálně testy by měly být zaměřené na minimálně 100 měření na koncový bod za den. Pokud jsou počty měření příliš nízké, nakonfigurujte prosím klienta JavaScriptu tak, aby se ve vaší aplikaci spouštěl častěji. Počty měření pro koncové body A a B by měly být velmi podobné, i když jsou očekávány malé rozdíly a jsou v pořádku. V případě velkých rozdílů by neměly být výsledky důvěryhodné.

## <a name="percentiles"></a>Percentily

Latence, měřená v milisekundách, je oblíbená metrika pro měření rychlosti mezi zdrojem a cílem na internetu. Data latence nejsou normálně distribuována (to znamená, že se nepoužívá "křivka špičky"), protože existuje "Long-konec" velkých hodnot latence, které při použití statistiky, jako je aritmetický průměr, přenesou výsledky. Jako alternativu vám percentily poskytnou způsob, jak analyzovat data. Příklad: Medián, nebo 50. percentil, shrnuje střední část distribučního podílu hodnoty nad IT a polovinou pod ní. Hodnota percentil percentilu znamená, že je větší než 75% všech hodnot v distribuci. Internet Analyzer odkazuje na percentily v podobě zkratky jako P50, P75 a P95.

Percentily nástroje Internet Analyzer jsou _ukázkové metriky_. To je na rozdíl od skutečné _metriky populace_. Například denní střední latence populace mezi studenty na University of Jižní California a Microsoft je střední hodnota latence všech žádostí během daného dne. V praxi je měření hodnoty všech požadavků nepraktické, proto předpokládáme, že je poměrně velká vzorek reprezentativní pro skutečné plnění.

Pro účely analýzy je P50 (medián) užitečné jako očekávaná hodnota pro distribuci latence. Větší percentily, například P95, jsou užitečné pro určení, jak vysoké latence je v nejhorších případech. Pokud vás zajímá porozumění latenci zákazníků obecně, P50 je správná metrika, která se zaměřuje na. Pokud máte obavy o výkon pro nejhoršího provádění zákazníků, měli byste mít fokus P95. P75 je zůstatek mezi těmito dvěma.


## <a name="deltas"></a>Rozdíly

Rozdíl je rozdíl v hodnotách metriky pro koncové body a a B. rozdílové hodnoty jsou vypočítány za účelem zobrazení výhod B po. kladné hodnoty naznačují, že B se vykonává lépe než A, zatímco záporné hodnoty označují výkon B. Rozdílové počet může být absolutní (například 10 milisekund) nebo relativní (5%).

## <a name="confidence-interval"></a>Interval spolehlivosti 

Intervaly spolehlivosti (CI) představují rozsah hodnot, které mají pravděpodobnost obsahující metriky populace, jako je medián, P75 nebo průměr. Sledujeme běžnou statistickou konvenci pomocí 95% CI.

Pro nástroj Internet Analyzer je vhodný interval s úzkým spolehlivostí, protože ukazuje, že ukázková metrika je pravděpodobně velmi blízko se skutečnou metrikou populace. Velký interval spolehlivosti znamená menší jistotu, že naše ukázková metrika odráží skutečnou metriku naplnění. Nejlepším způsobem, jak vylepšit CI, je zvýšit počty měření.

## <a name="time-series"></a>time series (časová řada) 

Časová řada ukazuje, jak se metrika mění v čase. Na internetu existuje mnoho dočasných faktorů, které mají vliv na výkon, jako je špička provozních dob, dny v týdnu a svátky.


## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Přehled nástroje Internet Analyzer](internet-analyzer-overview.md).
