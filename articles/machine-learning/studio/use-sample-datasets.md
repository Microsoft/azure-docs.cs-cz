---
title: Pomocí ukázkových datových sad v nástroji Machine Learning Studio | Microsoft Docs
description: Popis datové sady použité v ukázkových modelů, které jsou zahrnuty v nástroji Machine Learning Studio. Pro experimentů můžete použít tyto ukázkových datových sad.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 03a0b844-e8a7-4896-996f-d3c7a0db7a50
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.openlocfilehash: 87d0eac427f2d3904278dbe8211a3fae3da891ed
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085051"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Použití vzorových datových sad v Azure Machine Learning Studiu
[top]: #machine-learning-sample-datasets

Když vytvoříte nový pracovní prostor v Azure Machine Learning, několik ukázkových datových sad a experimenty jsou zahrnuté ve výchozím nastavení. Mnoho z těchto ukázkových datových sad se používají ukázkové modely v [Azure AI Galerie](http://gallery.cortanaintelligence.com/). Ostatní jsou k dispozici jako příklady různých typů dat, obvykle používané pro strojové učení.

Některé tyto datové sady jsou k dispozici v úložišti objektů Blob Azure. Pro tyto datové sady následující tabulka obsahuje přímý odkaz. Můžete použít tyto datové sady v experimentů pomocí [importovat Data] [ import-data] modulu.

Zbytek tyto ukázkových datových sad jsou k dispozici v pracovním prostoru v části **uložit datové sady**. Tento nástroj naleznete paletě modulů nalevo od plátna experimentu v nástroji Machine Learning Studio.
Všechny tyto datové sady můžete použít v vlastní experimentu přetáhněte na plátno experimentu.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

<table>

<tr>
  <th>Název datové sady</th>
  <th>Popis datové sady</th>
</tr>

<tr>
  <td>Datové sady pro dospělé úplné zjišťování příjem binární klasifikace</td>
  <td>
Podmnožinu databázi roce 1994 za úplné zjišťování, pomocí pracovní dospělí víc než 16 s indexem upravenou příjem > 100.
<p></p>
<b>Použití:</b> klasifikovat uživatelé, kteří používají demografie předpovědět, zda uživatel mírou více než 50 tisíc a roce.
<p></p>
<b>Související Research:</b> Kohavi, R., Becker B., (1996). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače </td>
</tr>

<tr>
  <td>Kódy letiště datové sady</td>
  <td>
Kódy letiště USA.
<p></p>
Tato datová sada obsahuje jeden řádek pro každý letiště USA poskytnutí letiště číslo ID a název spolu s umístění Město a kraj.
  </td>
</tr>

<tr>
  <td>Automobilů price data (Raw)</td>
  <td>
Informace o automobilů podle značka a model, včetně cenu, funkce, například počtu válců a MPG a také pojištění rizikové skóre.
<p></p>
Rizikové skóre je původně přidružen cena automaticky. Toto pravidlo se upraví pak pro skutečné riziko v procesu ví, že znalci jako symboling. Hodnota + 3 označuje, že je automatického rizikové a hodnota -3 je to pravděpodobně bezpečné.
<p></p>
<b>Použití:</b> předpovědi skóre rizika podle funkcí, pomocí klasifikace regrese nebo multivariate. 
<p></p>
<b>Související Research:</b> Schlimmer, J.C. (1987). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače </td>
</tr>

<tr>
  <td>Kolo pronájem UCI datové sady</td>
  <td>
Pronájem kolo UCI datovou sadu, která je založená na reálná data z velké Bikeshare společnosti, která udržuje síť kolo pronájem v Washington DC.
<p></p>
Datová sada obsahuje jeden řádek pro každou hodinu každý den v 2011 a 2012, celkem 17,379 řádků. Rozsah každou hodinu kolo pronájem je od 1 do 977.

  </td>
</tr>

<tr>
  <td>Obrázek RGB faktury brány</td>
  <td>
Veřejně dostupné obrázkový soubor převést na data ve formátu CSV.
<p></p>
Kód pro převod obrázku je uvedený v <strong>barvu kvantizační použití clusteringu K-Means</strong> stránku s podrobnostmi modelu.
  </td>
</tr>

<tr>
  <td>Data odběru krve</td>
  <td>
Podmnožinu dat z databáze dárce krve transfuzním Service Center Hsin Chu města, Tchaj-wan.
<p></p>
Dárce data zahrnují měsíců od poslední odběru) a četnost nebo celkový počet odběrů, doba od poslední odběru a množství krve věnován.
<p></p>
<b>Použití:</b> cílem je k předvídání prostřednictvím klasifikace, zda dárce věnován krve v březnu 2007, kde 1 znamená dárce během období cíl a 0 bez dárce. 
<p></p>
<b>Související Research:</b> Já, systémem, (2008). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače
<p></p>
Já, I-vývoj: Cheng, Yang, král-Jang a si toho, Tao-jde "znalostní báze zjišťování na modelu do režimu omezené Funkčnosti pomocí Bernoulliho pořadí,"Expert systémy s aplikacemi, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Data rakoviny mateřské</td>
  <td>
Jeden ze tří související rakoviny datové sady poskytované Institute radiology, který se zobrazí často v machine learning dokumentace. Kombinuje diagnostické informace a funkce z laboratorní analýzy přibližně 300 tkáňových vzorků.
<p></p>
<b>Použití:</b> klasifikace typ rakoviny, na základě 9 atributů, z nichž některé jsou lineární a některé jsou kategorií. 
<p></p>
<b>Související Research:</b> Wohlberg, čísel, ulici, W.N. a Mangasarian, O.L. (1995). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače </td>
</tr>

<tr>
  <td>Funkce rakoviny mateřské <td>
Datová sada obsahuje informace o 102 tisíc podezřelé oblasti (kandidáty) X-ray bitových kopií, každá popsána 117 funkce. Funkce, které jsou proprietární a jejich význam není zjištěné při creators datovou sadu (Siemens zdravotní péče). 
  </td>
</tr>

<tr>
  <td>Informace o rakovině mateřské</td>
  <td>
Datová sada obsahuje další informace pro každou podezřelou oblast X-ray bitové kopie. Každý příklad poskytuje informace (například štítek, pacienta ID, souřadnice opravy relativně k celé image) o příslušné číslo řádku v sadě dat mateřské rakoviny funkce. Každý pacienta má počet příklady. Pro pacientů, kteří mají rakoviny je několik příkladů kladné a některé jsou záporné. Všechny příklady jsou pro pacientů, kteří nemají rakoviny, záporné. Datová sada obsahuje příklady 102 kB. Tendenční datovou sadu 0,6 % bodů jsou kladné, zbytek záporné. Datová sada byla provedena dostupné Siemens zdravotní péče.
  </td>
</tr>

<tr>
  <td>Popisky Appetency CRM sdílet</td>
  <td>
Štítky z předpovědi výzvy KDD Cup 2009 zákazníka relace (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Popisky změn CRM sdílet</td>
  <td>
Štítky z předpovědi výzvy KDD Cup 2009 zákazníka relace (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>Datová sada CRM sdílet</td>
  <td>
Tato data pocházejí z předpovědi výzvy KDD Cup 2009 zákazníka relace (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Datová sada obsahuje 50 tisíc zákazníkům společnosti francouzština telekomunikace oranžová. Každý zákazník má 230 anonymizovaná funkce 190, které jsou číselné a 40 jsou kategorií. Funkce jsou velmi zhuštěných.
  </td>
</tr>

<tr>
  <td>Popisky Upselling CRM sdílet</td>
  <td>
Štítky z předpovědi výzvy KDD Cup 2009 zákazníka relace (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Energetické úspornosti regrese dat</td>
  <td>
Kolekce simulované energie profily založené na 12 jiné budovy tvarů. Budovy jsou rozlišené pomocí osm funkce. To zahrnuje použitém oblasti, zasklení oblasti distribuce a orientace.
<p></p>
<b>Použití:</b> použít k předpovědi energií hodnocení na základě jako jednu ze dvou skutečných hodnot odpovědí regrese nebo klasifikace. Pro více třída klasifikace se zaokrouhlí proměnnou odpovědi na nejbližší celé číslo. 
<p></p>
<b>Související Research:</b> Xifara A. & Tsanas A. (2012). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače </td>
</tr>

<tr>
  <td>Letu zpozdí dat</td>
  <td>
Osobní letu na běhu výkonu dat získaných z kolekce dat TranStats USA Ministerstvo dopravy (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">na čas</a>).
<p></p>
Datová sada obsahuje časové období duben – říjen 2013. Před nahrát do Azure Machine Learning Studio, byla datová sada zpracována následujícím způsobem:
<ul>
  <li>Datová sada se filtrované tak, aby pokrývalo pouze 70 nejvytíženější letištích v kontinentální USA</li>
  <li>Zrušené lety měla označené jako zpožděn víc než 15 minut</li>
  <li>Odkloněných lety byly vyfiltrovány.</li>
  <li>Nebyly vybrány následující sloupce: rok, měsíc, DayofMonth, DayOfWeek, operátora, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, zrušeno</li>
</ul>
</td>
</tr>

<tr>
  <td>Letu na časový výkon (Raw)</td>
  <td>
Záznamy letadle letu doručení a odchylky v USA z října 2011.
<p></p>
<b>Použití:</b> předpovědi zpoždění letů. 
<p></p>
<b>Související Research:</b> z USA oddělení Transport <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time"> http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time </a>.
  </td>
</tr>

<tr>
  <td>Data o lesních požárech</td>
  <td>
Obsahuje data, počasí, jako je například teploty a vlhkosti indexy a rychlosti větru. Data jsou převzaty z oblasti severovýchodním Portugalsku, v kombinaci s záznamy aktivuje se v doménové struktuře.
<p></p>
<b>Použití:</b> Toto je úloha obtížné regrese, kde je cílem předpovídat oblasti vypáleném aktivuje se v doménové struktuře. 
<p></p>
<b>Související Research:</b> Cortez, P. & Morais A. (2008). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače
<p></p>
[Cortez a Morais 2007] P. Cortez a A. Morais. Na Data Mining přístup k předvídání lesních požárech pomocí meteorologických Data. V J. Neves, M. F. Santos a Edit.: J. Machado, nové trendy v umělé inteligence řízení 13. EPIA 2007 - portugalština konference o umělé Intelligence, prosinec, 523-Guimarães, Portugalsko s. 512, 2007. APPIA, ISBN-13 978-989-95618-0-9. K dispozici na: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf"> http://www.dsi.uminho.pt/~pcortez/fires.pdf </a>.
  </td>
</tr>

<tr>
  <td>Němčina platební karty UCI datové sady</td>
  <td>
Datová sada UCI Statlog (němčina platební karty) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + němčina + platební + Data</a>), pomocí souboru german.data.
<p></p>
Datová sada klasifikuje osoby, popsaného sadu atributů, jako nízkou nebo vysokou platební rizika. Každý příklad představuje osoby. Existují 20 funkcí číselné i kategorií a binární popisek (hodnota riziko platební). Vysoká platební riziko položky mají popisek = 2, nízkou úvěrové riziko položky mají popisek = 1. Náklady na misclassifying příklad nízké riziko tak vysoké je 1, zatímco náklady misclassifying vysoce rizikové příklad jako nízkou je 5.
  </td>
</tr>

<tr>
  <td>Názvy filmů IMDB</td>
  <td>
Datová sada obsahuje informace o filmy, které byly hodnocení v Twitter tweetů: IMDB ID film, název film, genre a produkční roku. Existují 17 tisíc filmy v datové sadě. Datová sada byla zavedena v dokumentu "S. Dooms, T. De Pessemier a L. Martens. MovieTweetings: film hodnocení datovou sadu se shromažďují ze služby Twitter. Dílny na Crowdsourcingový a lidské výpočtů systémů doporučené CrowdRec na RecSys 2013."
  </td>
</tr>

<tr>
  <td>Data Iris dva – třída</td>
  <td>
Toto je možná nejlepší známé databáze má být vyhledána v dokumentace rozpoznávání vzor. Datová sada je poměrně malý, obsahující 50 příklady každý Okvětní lístek měření z tři typy iris.
<p></p>
<b>Použití:</b> předpovědi typu iris z měření.  
<p></p>
<b>Související Research:</b> Fisherovy R.A. (1988). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače </td>
</tr>

<tr>
  <td>Film Tweetů</td>
  <td>
Datová sada je rozšířené verze film Tweetings datové sady. Datová sada má 170K hodnocení filmy, které se extrahují z dobře strukturovaných tweetů na Twitteru. Každá instance představuje tweet a je řazené kolekce členů: ID uživatele, IMDB film ID, hodnocení, časové razítko, počet oblíbených položek pro tento tweet a počet retweets tento tweet. Datová sada byla k dispozici A. uvedená, S. Dooms, B. Loni a D. Tikk pro doporučené systémy výzvy 2014.
  </td>
</tr>

<tr>
  <td>Data o spotřebě paliv u různých automobilů</td>
  <td>
Tato datová sada mírně upravenou verzi poskytované knihovně StatLib univerzity Carnegie Mellon datovou sadu. Datová sada se používá v 1983 American statistické přidružení budeme.
<p></p>
Data jsou uvedené spotřeby paliva u různých automobilů v miles za spotřeby. Zahrnuje také informace, například počtu válců, modul posouvání, koňských sil, celkový počet váhy a akcelerace.
<p></p>
<b>Použití:</b> předpovědi paliva na základě tří s více hodnotami diskrétních atributů a pět souvislé atributy. 
<p></p>
<b>Související Research:</b> StatLib, Carnegie TruSecure, (1993). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače </td>
</tr>

<tr>
  <td>Datová sada Pima indiánů Diabetes binární klasifikace</td>
  <td>
Podmnožinu dat z National Institute Diabetes a trávícího a ledviny nákaz databáze. Datová sada se filtruje tak, aby se zaměřují na ženského pacientů Indie dědictví Pima. Data obsahují lékařské data, jako jsou glukosy a inulinový úrovně, jakož i lifestyle faktory.
<p></p>
<b>Použití:</b> předpovědět, zda má předmět diabetes (binární klasifikace). 
<p></p>
<b>Související Research:</b> Sigillito, V. (1990). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml"</a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače </td>
</tr>

<tr>
  <td>Data zákazníků restaurace</td>
  <td>
Sada metadata o zákazníků, včetně demografické údaje a předvolby.
<p></p>
<b>Použití:</b> použít tuto datovou sadu v kombinaci s další dva restaurace datové sady, a natrénuje a otestuje doporučené systému. 
<p></p>
<b>Související Research:</b> Bache, K. a Lichman, M. (2013). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače.
  </td>
</tr>

<tr>
  <td>Data funkce restaurace</td>
  <td>
Sada metadata o restaurace a jejich funkce, jako je například typ jídlo, nabízet stylu a umístění.
<p></p>
<b>Použití:</b> použít tuto datovou sadu v kombinaci s další dva restaurace datové sady, a natrénuje a otestuje doporučené systému. 
<p></p>
<b>Související Research:</b> Bache, K. a Lichman, M. (2013). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače.
  </td>
</tr>

<tr>
  <td>Restaurace hodnocení</td>
  <td>
Obsahuje hodnocení poskytují uživatelům restaurace na škále od 0 do 2.
<p></p>
<b>Použití:</b> použít tuto datovou sadu v kombinaci s další dva restaurace datové sady, a natrénuje a otestuje doporučené systému. 
<p></p>
<b>Související Research:</b> Bache, K. a Lichman, M. (2013). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače.
  </td>
</tr>

<tr>
  <td>Ocelové datovou sadu Annealing více – třída</td>
  <td>
Tato datová sada obsahuje řadu záznamy ze oceli žíhání zkušební verze. Obsahuje fyzické atributy (šířka, tloušťka, typ (smyčka, list atd.) výsledná oceli typy.
<p></p>
<b>Použití:</b> dva atributy číselné třídy; tvrdosti nebo sílu předpovědi. Může také analyzovat korelací mezi atributy.
<p></p>
Ocelové tříd podle sady standard, definované SAE a jiných organizací. Hledáte konkrétní "třída" (třída proměnná) a chcete získat další informace potřebné hodnoty. 
<p></p>
<b>Související Research:</b> šterlinků, D. & Buntine, dokončeno (NA). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity kalifornské, školní informace a vědecké účely počítače
<p></p>
Užitečné Průvodce ocelovými tříd naleznete zde: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Telescope dat</td>
  <td>
Záznam vysoké energii gama částice shluky společně s hluku na pozadí, obě simulated pomocí typu Monte Carlo procesu.
<p></p>
K zajištění přesnosti na základě základů důsledky Cherenkov gama dalekohledy byl záměr simulace. To se provádí pomocí metody statistické k rozlišení mezi požadované signál (Cherenkov záření sprchy) a hluku na pozadí (hadronic sprchy iniciovaná cosmic paprsky ve velkých prostředí).
<p></p>
Data byla předem zpracovaných vytvořit podlouhlého cluster s na dlouhém osy je zaměřen na center fotoaparát. Charakteristiky této třemi tečkami (často říká Hillas parametry) patří mezi parametry bitové kopie, které lze použít pro diskriminace.
<p></p>
<b>Použití:</b> předpovědět, zda obrázek sprcha reprezentuje šumu signál nebo na pozadí.
<p></p>
<b>Poznámky:</b> jednoduché klasifikace přesnost není smysl pro tato data od klasifikace pozadí události, jako je signál zhoršení než klasifikace událost signálu jako pozadí. Pro porovnání různých třídění je třeba použít ROC grafu. Pravděpodobnost přijímání událostí pozadí jako signálu musí být níže jednu z těchto prahových hodnot: 0,01, 0,02, hodnotu 0,05, 0,1 nebo 0,2.
<p></p>
Všimněte si také, že počet událostí na pozadí (pro hadronic sprchy h) je podceňována. V reálného měření představuje třídy h nebo šumu většiny událostí. 
<p></p>
<b>Související Research:</b> Bock, R.K. (1995). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: Univerzity z kalifornské, školní informace </td>
</tr>

<tr>
  <td>Datová sada počasí</td>
  <td>
Hodinové hodnoty na základě krajině počasí s NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">slučování dat z 201304 201310</a>).
<p></p>
Údaje o počasí popisuje připomínkám z letiště počasí stanice, přičemž zahrnuje časové období duben – říjen 2013. Před nahrát do Azure Machine Learning Studio, byla datová sada zpracována následujícím způsobem:
<ul>
  <li>ID stanice počasí byly mapovány na odpovídající letiště ID</li>
  <li>Stanice počasí nejsou přidružené 70 letiště nejvytíženější byly vyfiltrovány.</li>
  <li>Sloupce s datem byl rozdělen do samostatných sloupců rok, měsíc a den</li>
  <li>Nebyly vybrány následující sloupce: AirportID, rok, měsíc, den, čas, časové pásmo, SkyCondition, viditelnost, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, Rychlost, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, výškoměru</li>
</ul>
  </td>
</tr>

<tr>
  <td>Web Wikipedia SP 500 datové sady</td>
  <td>
Data je odvozený od Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) založené na články, každý S & P 500 společnosti, uložené jako XML data.
<p></p>
Před nahrát do Azure Machine Learning Studio, byla datová sada zpracována následujícím způsobem:
<ul>
  <li>Extrahování obsahu text pro každou konkrétní společnosti</li>
  <li>Odebrat wiki formátování</li>
  <li>Odeberte jiné než alfanumerické znaky</li>
  <li>Převést veškerý text na malá písmena</li>
  <li>Kategorie známé společnosti byly přidány.</li>
</ul>
<p></p>
Všimněte si, že pro některé společnosti článek nelze najít, tak počet záznamů je menší než 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Datová sada obsahuje data zákazníků a údaje o jejich reakci na přímé poštovní kampaň. Každý řádek představuje zákazníka. Datová sada obsahuje devět funkce o demografie uživatelů a po chování a tři označovat sloupců (navštěvovat, převodu a tráví).  Najdete je na binární sloupec, který označuje, že zákazník navštívené po marketingové kampaně. Převod označuje, že zákazník něco zakoupili. Tráví je množství, které byl stráven.  Datová sada byla provedena dostupné kevina Hillstrom pro MineThatData e-mailu Analytics a Data Mining výzvu.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funkce testovací příklady v datové sadě RCV1 V2 Reuters zprávy. Datová sada obsahuje články s příspěvky 781 tisíc spolu s jejich ID (první sloupec datové sady). Každý článek tokenizovaného stopworded a nasadí. Datová sada byla provedena dostupné David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funkce školení příklady v datové sadě RCV1 V2 Reuters zprávy. Datová sada obsahuje články s příspěvky 23 tisíc spolu s jejich ID (první sloupec datové sady). Každý článek tokenizovaného stopworded a nasadí. Datová sada byla provedena dostupné David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Datové sady z KDD Cup 1999 znalostní báze zjišťování a dolování dat nástroje soutěže (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Datová sada byla stažena a uložená v úložišti objektů Blob v Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) a zahrnuje jak trénování a testování datové sady. Datová sada školení má přibližně 126 tisíc řádků a sloupců 43, včetně popisků. Tři sloupce jsou součástí informací o popisek a 40 sloupce, který se skládá z funkce číselný a řetězec, kategorií, jsou k dispozici pro trénování modelu. Testovací data mají přibližně 22,5 K testování příklady stejné 43 sloupců, jako jsou Cvičná data.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Téma přiřazení pro články s příspěvky v datové sadě RCV1 V2 Reuters zprávy. Aktuální článek lze přiřadit na několik témat. Formát každý řádek je "&lt;název tématu&gt; &lt;id dokumentu&gt; 1". Datová sada obsahuje 2.6M tématu přiřazení. Datová sada byla provedena dostupné David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Tato data pocházejí z výzvy vyhodnocení výkonu KDD Cup 2010 Student (<a href="http://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">vyhodnocení výkonu student</a>). Data použitá je sada školení Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, S. A. Ritter, Gordon, G.J. & Koedinger, k. r. (2010). Algebra I 2008-2009. Výzvy datové sady z KDD Cup 2010 výukových Data Mining výzvu. Najít v <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Datová sada byla stažena a uložená v úložišti objektů Blob v Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) a obsahuje soubory protokolů z student soukromé vyučování/doučování systému. Zadaná funkce zahrnují ID problému a jeho stručný popis, student ID, časové razítko a počet pokusů o student provedené před správné způsobem řešení tohoto problému. Původní datové sady obsahuje záznamy 8,9 M; Tato datová sada byl vzorkovat dolů na první řádky 100 kB. Datová sada má 23 tabulátorem sloupce různé typy: číselné literály, kategorií a časové razítko.
  </td>
</tr>

</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
