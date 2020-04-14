---
title: Ukázková data pro návrháře
titleSuffix: Azure Machine Learning
description: Přečtěte si další informace o ukázkových datových sadách, které jsou k dispozici v návrháři Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: b19c1ed5aa556a2cfb633ac6fbc0cf9f95d0337f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256973"
---
# <a name="sample-datasets-in-azure-machine-learning-designer-preview"></a>Ukázkové datové sady v návrháři Azure Machine Learning (preview)

Když vytvoříte nový kanál v návrháři Azure Machine Learning (preview), ve výchozím nastavení se dosadí několik ukázkových datových sad. Tyto ukázkové datové sady jsou používány ukázkové kanály na domovské stránce návrháře. 

Ukázkové datové sady jsou k dispozici v kategorii**Vzorky datových** **sad.**- Najdete to v paletě modulů vlevo od plátna v návrháři. Můžete použít některou z těchto datových sad ve vlastním kanálu přetažením na plátno.

## <a name="datasets"></a>Datové sady


| Název&nbsp;datové sady&nbsp;&nbsp;&nbsp;&nbsp;| Popis datové sady |
|-------------|:--------------------|
| Dospělý sčítání lidu příjem binární klasifikace datový soubor | Podmnožina databáze sčítání lidu z roku 1994, která využívá pracující dospělé osoby starší 16 let s upraveným indexem příjmů > 100.<br/>**Použití**: Klasifikovat lidi pomocí demografie předpovědět, zda osoba vydělává více než 50 K ročně.<br/> **Související výzkum**: Kohavi, R., Becker, B., (1996). [Úložiště strojového učení UCI](https://archive.ics.uci.edu/ml). Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky|
|Údaje o cenách automobilů (Raw)|Informace o automobilech podle makety a modelu, včetně ceny, funkce, jako je počet válců a MPG, stejně jako skóre pojistného rizika.<br/> Skóre rizika je zpočátku spojeno s cenou auto. Poté se upraví pro skutečné riziko v procesu, o jehož pojistníkovi je známo, že symbolizuje. Hodnota +3 označuje, že auto je riskantní a hodnota -3, že je pravděpodobně bezpečné.<br/>**Použití**</b> : Předpovědět skóre rizika podle funkcí pomocí regrese nebo multivariate klasifikace.<br/>**Související**výzkum</b> : Schlimmer, J.C. (1987). [Úložiště strojového učení UCI](https://archive.ics.uci.edu/ml). Irvine, KALIFORNIE: Kalifornská univerzita, Škola informací a informatiky. |
| Sdílené popisky appetency aplikace CRM |Štítky z KDD Cup 2009 zákazník a predikce vztahů se zákazníky[(orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|Sdílené popisky změn CRM|Štítky z KDD Cup 2009 zákazník a predikce vztahů se zákazníky[(orange_small_train_churn.labels).](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)|
|Sdílená datová sada CRM | Tato data pocházejí z KDD Cup 2009 zákazník a predikce vztahů se zákazníky[(orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Datová sada obsahuje 50tisíc zákazníků od francouzské telekomunikační společnosti Orange. Každý zákazník má 230 anonymizovaných funkcí, z nichž 190 je číselných a 40 kategorických. Funkce jsou velmi řídké. |
|Sdílené štítky pro upselling aplikace CRM|Štítky z KDD Cup 2009 zákazník a predikce vztahů se zákazníky challenge[(orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Údaje o zpoždění letu|Údaje o výkonu letu cestujících v době přepravě převzaté ze sběru dat transtatů ministerstva dopravy USA[(On-Time).](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)<br/>Soubor údajů pokrývá období od dubna do října 2013. Před odesláním do návrháře byla datová sada zpracována následujícím způsobem: <br/>- Datová sada byla filtrována tak, aby pokrývala pouze 70 nejrušnějších letišť v kontinentálních USA <br/>- Zrušené lety byly označeny jako zpožděné o více než 15 minut <br/>- Odkloněné lety byly odfiltrovány <br/>- Byly vybrány následující sloupce: Rok, Měsíc, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Zrušeno|
|Německý datový soubor UCI kreditní karty|Datová sada UCI Statlog (německá kreditní karta) ([Statlog+German+Credit+Data)](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))používající soubor german.data.<br/>Datová sada klasifikuje osoby, které je popsáno sadou atributů, jako nízké nebo vysoké úvěrové riziko. Každý příklad představuje osobu. K dispozici je 20 funkcí, číselných i kategorických, a binární popisek (hodnota úvěrového rizika). Položky s vysokým úvěrovým rizikem mají popisek = 2, položky s nízkým úvěrovým rizikem mají popisek = 1. Náklady na nesprávné klasifikaci příkladu s nízkým rizikem jako vysoké jsou 1, zatímco náklady na chybnou klasifikaci vysoce rizikového příkladu jako nízké jsou 5.|
|Filmové tituly IMDB|Datová sada obsahuje informace o filmech, které byly hodnoceny v tweetech Twitteru: ID filmu ID IMDB, název filmu, žánr a rok výroby. V datové sadě je 17 kS. Datová sada byla zavedena v dokumentu "S. Dooms, T. De Pessemier a L. Martens. MovieTweetings: Movie Rating Datová sada shromážděná z Twitteru. Workshop o crowdsourcingu a lidském výpočtu pro doporučující systémy, CrowdRec na recsys 2013."|
|Hodnocení filmů|Datová sada je rozšířená verze datové sady Movie Tweetings. Datová sada má hodnocení 170K pro filmy, extrahované z dobře strukturovaných tweetů na Twitteru. Každá instance představuje pípání a je n-tice: ID uživatele, IMDB film ID, hodnocení, časové razítko, počet oblíbených pro tento tweet, a počet retweets tohoto pípání. Datovou sadu zpřístupnili A. Said, S. Dooms, B. Loni a D. Tikk pro Recommender Systems Challenge 2014.|
|Datová sada počasí|Hodinová pozemní pozorování počasí z NOAA ([sloučené údaje z let 201304 až 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Údaje o počasí zahrnují pozorování z letištních meteorologických stanic, která pokrývají období od dubna do října 2013. Před odesláním do návrháře byla datová sada zpracována následujícím způsobem:    <br/> - ID meteorologické stanice byly namapovány na odpovídající id letiště    <br/> - Meteorologické stanice, které nejsou spojeny se 70 nejrušnějšími letišti, byly odfiltrovány    <br/> - Sloupec Datum byl rozdělen do samostatných sloupců Rok, Měsíc a Den.    <br/> - Byly vybrány následující sloupce: AirportID, Rok, Měsíc, Den, Čas, TimeZone, SkyCondition, Viditelnost, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaPressurePressurePressure, RecordType, HourPrecip, Alter|
|Datová sada Wikipedie SP 500|Data jsou odvozena zhttps://www.wikipedia.org/) Wikipedie (na základě článků každé společnosti S&P 500, uložených jako XML data.    <br/>Před odesláním do návrháře byla datová sada zpracována následujícím způsobem:    <br/> - Extrahovat textový obsah pro každou konkrétní společnost    <br/> - Odstranit formátování wiki    <br/> - Odstranění nealfanumerických znaků    <br/> - Převést veškerý text na malá písmena    <br/> - Byly přidány známé kategorie společností    <br/>Všimněte si, že u některých společností nebyl nalezen článek, takže počet záznamů je menší než 500.|

## <a name="next-steps"></a>Další kroky

* Naučte se základy prediktivní analýzy a strojového učení pomocí [kurzu: Předvídejte cenu automobilů s designérem](tutorial-designer-automobile-price-train-score.md)

* Přečtěte si, jak upravit existující [ukázky návrháře](samples-designer.md) tak, aby byly přizpůsobit vašim potřebám.
