---
title: Použití ukázkových datových sad v Návrháři Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Přečtěte si další informace o ukázkových datových sadách obsažených v Návrháři Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037311"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Použití ukázkových datových sad v Návrháři Azure Machine Learning (Preview)

Při vytváření nového kanálu v Návrháři Azure Machine Learning (Preview) je ve výchozím nastavení zahrnuto několik ukázkových datových sad. Tyto ukázkové datové sady jsou používány vzorovými kanály na domovské stránce návrháře. 

Ukázkové datové sady jsou k dispozici v části **datové sady**-kategorie **ukázek** . Najdete ho v paletě modulu nalevo od plátna v návrháři. Můžete použít kteroukoli z těchto datových sad ve vlastním kanálu přetažením na plátno.

## <a name="datasets"></a>Datové sady


| Datová sada&nbsp;název&nbsp;&nbsp;&nbsp;&nbsp;| Popis datové sady |
|-------------|:--------------------|
| Datová sada klasifikace binárního výnosu pro dospělé | Podmnožina 1994ch databází, která používá pracovní dospělí za stáří 16, s upraveným indexem příjmů > 100.<br/>**Použití**: Klasifikujte lidi pomocí demografických údajů, abyste předpovídat, jestli osoba za 50 tis za rok.<br/> **Související výzkum**: Kohavi, R., Becker, B., (1996). [UCI Machine Learning úložiště](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, školní školy a počítačové vědy|
|Údaje o cenách automobilu (RAW)|Informace o automatickém mobilním zařízení, včetně ceny, funkcí, jako je třeba počet lahví a MPG, a hodnocení rizikového rizika.<br/> K automatické ceně je zpočátku přidruženo skóre rizika. Pak se upraví na skutečné riziko v procesu známém Pojistní matematici jako symboling. Hodnota + 3 znamená, že auto je rizikové a hodnota-3 je pravděpodobně bezpečná.<br/>**Použití**:</b> předpověď rizikového skóre podle funkcí pomocí regrese nebo klasifikace lineární.<br/>**Související výzkum**:</b> Schlimmer, J.C. (1987). [UCI Machine Learning úložiště](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, školní informace a počítačové vědy. |
| Appetency popisky CRM – sdílené |Štítky z konference KDD ([orange_small_train_appetency. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)2009) – výzva pro předpověď vztahů se zákazníky.|
|Sdílené změny v CRM|Štítky z konference KDD ([orange_small_train_churn. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)2009) – výzva pro předpověď vztahů se zákazníky.|
|Sdílená datová sada CRM | Tato data pocházejí z konference KDD ([orange_small_train. data. zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)) s 2009. <br/>Datová sada obsahuje zákazníky 50 tis z francouzské telekomunikační společnosti oranžová. Každý zákazník má 230 funkcí Anonyme, 190 z nich je numerická a 40 jsou kategorií. Funkce jsou velmi zhuštěné. |
|Sdílení popisků pro CRM|Popisky z konference KDD ([orange_large_train_upselling. labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels) – výzva k vy2009ce vztahu zákazníka|
|Data o zpoždění letů|Data o výkonu pro osobní čas, která jsou přijímána z kolekce TranStatsch dat ministerstva dopravy USA ([včas).](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)<br/>Datová sada se zabývá časovým obdobím od dubna do října 2013. Před nahráním do návrháře byla datová sada zpracována takto: <br/>– Datová sada byla vyfiltrovaná tak, aby se kryla jenom 70 nejvytíženější letiště v kontinentální části USA. <br/>– Zrušené lety byly označeny za zpožděné o více než 15 minut. <br/>– Odstraněné lety byly odfiltrovány. <br/>-Byly vybrány následující sloupce: Year, month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled.|
|Datová sada UCI pro německé kreditní karty|Datová sada Statlog (německá kreditní karta) ([Statlog + němčina + Credit + data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))) s použitím souboru německého. data.<br/>Datová sada klasifikuje osoby, které jsou popsány sadou atributů, jako nízké nebo vysoké úvěrové riziko. Každý příklad představuje osobu. K dispozici jsou 20 funkcí, číselné i kategorií a binární označení (hodnota úvěrového rizika). Položky vysokého kreditu mají štítek = 2, nízké položky úvěrového rizika mají označení = 1. Náklady na chybnou klasifikaci příkladu s nízkým rizikem, který je vysoký, je 1, zatímco náklady na netřídění s vysokým rizikovým příkladem jsou 5.|
|Názvy filmů IMDB|Datová sada obsahuje informace o videích, které byly ohodnoceny na Twitteru tweety: IMDB ID filmu, název filmu, Žánr a produkční rok. V datové sadě jsou 17K filmy. Datová sada byla představena v dokumentu "S". Dooms, T. de Pessemier a L. Martens. MovieTweetings: datová sada hodnocení filmu shromážděná z Twitteru. Dílna v Crowdsourcingový a lidské výpočtu pro doporučované systémy, CrowdRec na RecSys 2013. "|
|Hodnocení filmů|Datová sada je rozšířená verze datové sady ve formátu videa. Tato datová sada má 170K hodnocení filmů, která se extrahují z dobře strukturovaných tweety na Twitteru. Každá instance představuje prostupnost a je řazená kolekce členů: ID uživatele, ID videa IMDB, hodnocení, časové razítko, počet oblíbených položek pro tento seznam a počet předaných prostředků. Datová sada byla zpřístupněna prostřednictvím. říkáme, S. Dooms, B. loni a D. Tikk pro doporučované systémy Challenge 2014.|
|Datová sada počasí|Hodinově založené počasí z NOAA ([sloučená data z 201304 do 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Údaje o počasí se týkají pozorování z povětrnostních stanic na letišti, které pokrývají dobu od dubna do října 2013. Před nahráním do návrháře byla datová sada zpracována takto:    <br/> – ID stanice počasí byly namapovány na odpovídající ID letišť.    <br/> – Počasí, které nejsou přidružené k letiště 70 nejvytíženější, se odfiltrují.    <br/> – Sloupec data byl rozdělen na sloupce samostatného roku, měsíce a dne.    <br/> -Byly vybrány následující sloupce: AirportID, Year, month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Wikipedii sada dat SP 500|Data se odvozují z Wikipedii (https://www.wikipedia.org/) na základě článků z každé & P 500 společnosti, která je uložená jako data XML.    <br/>Před nahráním do návrháře byla datová sada zpracována takto:    <br/> – Extrakce textového obsahu pro každou konkrétní firmu    <br/> -Odebrat formátování wiki    <br/> -Odebrání jiných než alfanumerických znaků    <br/> -Převést veškerý text na malá písmena    <br/> – Přidaly se známé kategorie společnosti.    <br/>Upozorňujeme, že některé společnosti nemohly najít článek, takže počet záznamů je menší než 500.|

## <a name="next-steps"></a>Další kroky

* Seznamte se se základy prediktivní analýzy a strojového učení s využitím [kurzu: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md)

* Naučte se, jak upravit existující [ukázky Návrháře](samples-designer.md) a přizpůsobit je vašim potřebám.
