---
title: Použití ukázkových datových sad
titleSuffix: Azure Machine Learning
description: Popisy datových sad použitých v ukázkových modelech obsažených v Návrháři Machine Learning. Tyto ukázkové datové sady můžete použít pro vaše kanály.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165059"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Použití ukázkových datových sad v Návrháři Azure Machine Learning (Preview)

Při vytváření nového kanálu v Návrháři Azure Machine Learning (Preview) je ve výchozím nastavení zahrnuto několik ukázkových datových sad. Mnohé z těchto ukázkových datových sad jsou používány vzorovými modely na domovské stránce návrháře. Další jsou zahrnuté jako příklady různých typů dat, které se obvykle používají ve strojovém učení.

Některé z těchto datových sad jsou k dispozici v úložišti objektů BLOB v Azure. Pro tyto datové sady poskytuje následující tabulka přímý odkaz. Tyto datové sady můžete ve svých kanálech použít pomocí modulu [Import dat](./algorithm-module-reference/import-data.md) .

Zbytek těchto ukázkových datových sad je k dispozici v části **datové sady**-kategorii **ukázek** . Najdete ho v paletě modulu nalevo od plátna v návrháři. Můžete použít kteroukoli z těchto datových sad ve vlastním kanálu přetažením na plátno.

## <a name="datasets"></a>Datové sady

<table>

<tr>
  <th>Název datové sady</th>
  <th>Popis datové sady</th>
</tr>

<tr>
  <td>Datová sada klasifikace binárního výnosu pro dospělé</td>
  <td>
Podmnožina 1994ch databází, která používá pracovní dospělí za stáří 16, s upraveným indexem příjmů > 100.
<p></p>
<b>Použití:</b> Klasifikujte lidi pomocí demografických údajů, abyste předpovídat, jestli osoba za 50 tis za rok.
<p></p>
<b>Související výzkum:</b> Kohavi, R., Becker, B., (1996). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>úložiště UCI Machine Learning. Irvine, CA: University of California, školní školy a počítačové vědy </td>
</tr>

<tr>
  <td>Údaje o cenách automobilu (RAW)</td>
  <td>
Informace o automatickém mobilním zařízení, včetně ceny, funkcí, jako je třeba počet lahví a MPG, a hodnocení rizikového rizika.
<p></p>
K automatické ceně je zpočátku přidruženo skóre rizika. Pak se upraví na skutečné riziko v procesu známém Pojistní matematici jako symboling. Hodnota + 3 znamená, že auto je rizikové a hodnota-3 je pravděpodobně bezpečná.
<p></p>
<b>Použití:</b> Předpověď rizikového skóre podle funkcí pomocí regrese nebo klasifikace lineární 
<p></p>
<b>Související výzkum:</b> Schlimmer, J.C. (1987). <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>úložiště UCI Machine Learning. Irvine, CA: University of California, školní školy a počítačové vědy </td>
</tr>


<tr>
  <td>Appetency popisky CRM – sdílené</td>
  <td>
Štítky z konference KDD (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. labels</a>2009) – výzva pro předpověď vztahů se zákazníky.
  </td>
</tr>

<tr>
  <td>Sdílené změny v CRM</td>
  <td>
Štítky z konference KDD (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. labels</a>2009) – výzva pro předpověď vztahů se zákazníky.
  </td>
</tr>

<tr>
  <td>Sdílená datová sada CRM</td>
  <td>
Tato data pocházejí z konference KDD (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train. data. zip</a>) s 2009.
<p></p>
Datová sada obsahuje zákazníky 50 tis z francouzské telekomunikační společnosti oranžová. Každý zákazník má 230 funkcí Anonyme, 190 z nich je numerická a 40 jsou kategorií. Funkce jsou velmi zhuštěné.
  </td>
</tr>

<tr>
  <td>Sdílení popisků pro CRM</td>
  <td>
Štítky z konference KDD (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. labels</a>2009) – výzva pro předpověď vztahů se zákazníky.
  </td>
</tr>

<tr>
  <td>Data o zpoždění letů</td>
  <td>
Data o výkonu pro osobní čas, která jsou přijímána z kolekce TranStatsch dat ministerstva dopravy USA (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">včas).</a>
<p></p>
Datová sada se zabývá časovým obdobím od dubna do října 2013. Před nahráním do návrháře byla datová sada zpracována takto:
<ul>
  <li>Datová sada se vyfiltruje tak, aby se kryla jenom 70 nejvytíženější letiště v kontinentální části USA.</li>
  <li>Zrušené lety byly označeny za zpožděné o více než 15 minut.</li>
  <li>Odfiltrované lety byly odfiltrovány</li>
  <li>Byly vybrány následující sloupce: Year, month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled.</li>
</ul>
</td>
</tr>

<tr>
  <td>Datová sada UCI pro německé kreditní karty</td>
  <td>
Datová sada Statlog (německá kreditní karta) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + němčina + Credit + data</a>) s použitím souboru německého. data.
<p></p>
Datová sada klasifikuje osoby, které jsou popsány sadou atributů, jako nízké nebo vysoké úvěrové riziko. Každý příklad představuje osobu. K dispozici jsou 20 funkcí, číselné i kategorií a binární označení (hodnota úvěrového rizika). Položky vysokého kreditu mají štítek = 2, nízké položky úvěrového rizika mají označení = 1. Náklady na chybnou klasifikaci příkladu s nízkým rizikem, který je vysoký, je 1, zatímco náklady na netřídění s vysokým rizikovým příkladem jsou 5.
  </td>
</tr>

<tr>
  <td>Názvy filmů IMDB</td>
  <td>
Datová sada obsahuje informace o videích, které byly ohodnoceny na Twitteru tweety: IMDB ID filmu, název filmu, Žánr a produkční rok. V datové sadě jsou 17K filmy. Datová sada byla představena v dokumentu "S". Dooms, T. de Pessemier a L. Martens. MovieTweetings: datová sada hodnocení filmu shromážděná z Twitteru. Dílna v Crowdsourcingový a lidské výpočtu pro doporučované systémy, CrowdRec na RecSys 2013. "
  </td>
</tr>

<tr>
  <td>Hodnocení filmů</td>
  <td>
Datová sada je rozšířená verze datové sady ve formátu videa. Tato datová sada má 170K hodnocení filmů, která se extrahují z dobře strukturovaných tweety na Twitteru. Každá instance představuje prostupnost a je řazená kolekce členů: ID uživatele, ID videa IMDB, hodnocení, časové razítko, počet oblíbených položek pro tento seznam a počet předaných prostředků. Datová sada byla zpřístupněna prostřednictvím. říkáme, S. Dooms, B. loni a D. Tikk pro doporučované systémy Challenge 2014.
  </td>
</tr>


<tr>
  <td>Datová sada počasí</td>
  <td>
Hodinově založené počasí z NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">sloučená data z 201304 do 201310</a>).
<p></p>
Údaje o počasí se týkají pozorování z povětrnostních stanic na letišti, které pokrývají dobu od dubna do října 2013. Před nahráním do návrháře byla datová sada zpracována takto:
<ul>
  <li>ID povětrnostních stanic byly namapovány na odpovídající ID letišť.</li>
  <li>Nefiltrované povětrnostní stanice, které nejsou spojené s nejvytíženější letiště 70.</li>
  <li>Sloupec data byl rozdělen na sloupce samostatného roku, měsíce a dne.</li>
  <li>Byly vybrány následující sloupce: AirportID, Year, month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedii sada dat SP 500</td>
  <td>
Data jsou odvozena z Wikipedii (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) na základě článků z každé & P 500 společnosti, která je uložená jako data XML.
<p></p>
Před nahráním do návrháře byla datová sada zpracována takto:
<ul>
  <li>Extrakce textového obsahu pro každou konkrétní firmu</li>
  <li>Odebrat formátování wikiwebu</li>
  <li>Odebrat jiné než alfanumerické znaky</li>
  <li>Převést veškerý text na malá písmena</li>
  <li>Byly přidány známé kategorie společnosti.</li>
</ul>
<p></p>
Upozorňujeme, že některé společnosti nemohly najít článek, takže počet záznamů je menší než 500.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Další kroky

* Seznamte se se základy prediktivní analýzy a strojového učení s využitím [kurzu: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md)

* Import ukázkových datových sad pomocí modulu [Import dat](./algorithm-module-reference/import-data.md)
