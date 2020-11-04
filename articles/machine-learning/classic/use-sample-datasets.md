---
title: 'ML Studio (Classic): použití ukázkových datových sad – Azure'
description: Popisy datových sad použitých v ukázkových modelech, které jsou součástí Machine Learning Studio (Classic). Tyto ukázkové datové sady můžete použít pro vaše experimenty.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 737c63c02ea852e8cf17e2d520ca91b05a5ba12b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325610"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Použití ukázkových datových sad v Azure Machine Learning Studio (klasický)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro. ](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


[top]: #machine-learning-sample-datasets

Když vytvoříte nový pracovní prostor v Azure Machine Learning Studio (Classic), bude ve výchozím nastavení zahrnuto několik ukázkových datových sad a experimentů. Mnohé z těchto ukázkových datových sad jsou používány vzorovými modely v [Azure AI Gallery](https://gallery.azure.ai/). Další jsou zahrnuté jako příklady různých typů dat, které se obvykle používají ve strojovém učení.

Některé z těchto datových sad jsou k dispozici v úložišti objektů BLOB v Azure. Pro tyto datové sady poskytuje následující tabulka přímý odkaz. Tyto datové sady můžete v experimentech použít pomocí modulu [Import dat][import-data] .

Zbytek těchto ukázkových datových sad je v pracovním prostoru v části **uložené datové sady** k dispozici. Najdete ho v paletě modul nalevo od plátna experimentu v Machine Learning Studio (Classic).
Kteroukoli z těchto datových sad můžete použít ve svém vlastním experimentu tak, že ji přetáhnete na plátno experimentu.

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
<b>Související výzkum:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní školy a počítačové vědy </td>
</tr>

<tr>
  <td>Datová sada kódů letišť</td>
  <td>
Kódy letišť USA.
<p></p>
Tato datová sada obsahuje jeden řádek pro každý letiště v USA, který poskytuje ID a název letiště společně s město a městem umístění.
  </td>
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
<b>Související výzkum:</b> Schlimmer, J.C. (1987). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní školy a počítačové vědy </td>
</tr>

<tr>
  <td>Datová sada UCI pro půjčovnu kol</td>
  <td>
Datová sada s daty o pronájmu kol, která je založená na skutečných datech ze společnosti pro velké Bikeshare společnosti, která udržuje síť pronájmu kol v Washington DC.
<p></p>
Datová sada má jeden řádek pro každou hodinu každého dne v 2011 a 2012, celkem 17 379 řádků. Rozsah odpracovaných kol je od 1 do 977.

  </td>
</tr>

<tr>
  <td>Obrázek RGB pro brány pro účtenky</td>
  <td>
Veřejně dostupný soubor obrázku převedený na data ve formátu CSV.
<p></p>
Kód pro převod obrázku je k dispozici na stránce <strong>Color kvantizační s</strong> podrobnostmi o modelu clusteringu.
  </td>
</tr>

<tr>
  <td>Krevní data darování</td>
  <td>
Podmnožina dat z krevní databáze dárce v rámci služby Hsin-Chu City, Tchaj-wan pro krev transfúze.
<p></p>
Data dárce zahrnují měsíce od posledního darování) a četnost nebo celkový počet odběrů, čas od posledního darování a množství krevních darovaných.
<p></p>
<b>Použití:</b> Cílem je předpovědět přes klasifikaci, ať už v březnu 2007, kde 1 označuje dárce během cílového období, a 0 nedárce. 
<p></p>
<b>Související výzkum:</b> Já, I.C., (2008). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní školy a počítačové vědy
<p></p>
Já, I-Cheng, Yang, King-Jang a nocení, značku-Mingu, "zjišťování znalostí na NEPŘEJDE modelu pomocí Bernoulliho sekvence," expert Systems s aplikacemi, 2008, <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Data o prsních rakovinu</td>
  <td>
Jedna ze tří datových sad souvisejících s rakovinu, kterou poskytuje Oncology Institute, což se často objevuje v dokumentaci ke službě Machine Learning. Kombinuje diagnostické informace s funkcemi z laboratorní analýzy o 300 tkáních vzorků.
<p></p>
<b>Použití:</b> Klasifikace typu rakoviny na základě 9 atributů, z nichž některé jsou lineární a některé jsou kategorií. 
<p></p>
<b>Související výzkum:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní školy a počítačové vědy </td>
</tr>

<tr>
  <td>Funkce prsních rakoviny <td>
Datová sada obsahuje informace o podezřelých oblastech 102K (kandidáty) z rentgenových imagí, z nichž každý popisuje funkce 117. Tyto funkce jsou proprietární a jejich význam nejsou tvůrci datových sad (zdravotnictví) odhaleny. 
  </td>
</tr>

<tr>
  <td>Informace o prsních Rakovinuch</td>
  <td>
Datová sada obsahuje další informace pro každou podezřelou oblast obrazu na ose X-ray. Každý příklad obsahuje informace o odpovídajícím čísle řádku v datové sadě vlastností prsních rakoviny (například popisek, ID pacienta, souřadnice týkající se opravy vzhledem k celému obrázku). Každý pacient má několik příkladů. Pro pacienty, kteří mají rakovinu, jsou některé příklady pozitivní a některé jsou záporné. Pro pacienty, kteří nemají rakovinu, jsou všechny příklady záporné. Tato datová sada obsahuje příklady 102K. Datová sada je posunuta, 0,6% bodů je pozitivní, zbývající jsou záporné. Tato datová sada byla zpřístupněna pro zdravotní péči společnosti Siemens.
  </td>
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
Tato data pocházejí z konference KDDu pro předpověď vztahů se zákazníky 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
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
  <td>Energy-Efficiency regresní data</td>
  <td>
Kolekce simulovaných energetických profilů na základě 12 různých stavebních tvarů. Budovy jsou odlišené o osm funkcí. To zahrnuje oblast zasklení, distribuci zasklení oblasti a orientaci.
<p></p>
<b>Použití:</b> Pomocí regrese nebo klasifikace předpovědět hodnocení energetické účinnosti na základě jedné ze dvou odpovědí s skutečnou hodnotou. U klasifikace s více třídami zaokrouhlí proměnnou odpovědi na nejbližší celé číslo. 
<p></p>
<b>Související výzkum:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní školy a počítačové vědy </td>
</tr>

<tr>
  <td>Data o zpoždění letů</td>
  <td>
Data o výkonu pro osobní čas, která jsou přijímána z kolekce TranStatsch dat ministerstva dopravy USA (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">včas).</a>
<p></p>
Datová sada se zabývá časovým obdobím April-October 2013. Před nahráním do Azure Machine Learning Studio (Classic) byla datová sada zpracována následujícím způsobem:
<ul>
  <li>Datová sada se vyfiltruje tak, aby se kryla jenom 70 nejvytíženější letiště v kontinentální části USA.</li>
  <li>Zrušené lety byly označeny za zpožděné o více než 15 minut.</li>
  <li>Odfiltrované lety byly odfiltrovány</li>
  <li>Byly vybrány následující sloupce: Year, month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled.</li>
</ul>
</td>
</tr>

<tr>
  <td>Výkon včas nezpracovaných letových časů (RAW)</td>
  <td>
Záznamy o příjezdu a odchodech letů v letadle v rámci USA od října 2011.
<p></p>
<b>Použití:</b> Předpovědět zpoždění letů. 
<p></p>
<b>Související výzkum:</b> Od oddělení USA. přepravuje se <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a> .
  </td>
</tr>

<tr>
  <td>Data jsou aktivována v doménové struktuře.</td>
  <td>
Obsahuje data o počasí, jako je teplota a indexy vlhkosti a rychlost větru. Data jsou přijímána z oblasti severovýchodního Portugalska v kombinaci se záznamy o struktuře požárů.
<p></p>
<b>Použití:</b> Jedná se o obtížný regresní úkol, ve kterém se zaměřuje na předpověď vypálené oblasti v doménové struktuře, která se aktivuje. 
<p></p>
<b>Související výzkum:</b> Cortez, P., & Morais, A. (2008). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní školy a počítačové vědy
<p></p>
[Cortez a Morais, 2007] P. Cortez a. Morais. Přístup k dolování dat pro předpověď doménové struktury se aktivují pomocí meteorologických dat. V J. Neves, M. F. Santos a J. Machado EDS. nové trendy v umělých inteligentních Intelligencech, jednání nějak změnily 13ho EPIA 2007-portugalské konference o uměle Intelligence, prosinec, Guimarães, Portugalsku, PP. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. K dispozici na adrese: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a> .
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
  <td>Iris – dvě data třídy</td>
  <td>
To je pravděpodobně nejlepší známá databáze, která se nachází v dokumentaci pro rozpoznávání vzorků. Datová sada je relativně malá, obsahující 50 příklady každé Petal měření ze tří odrůd Iris.
<p></p>
<b>Použití:</b> Předpovědět typ Iris z měření.  
<p></p>
<b>Související výzkum:</b> Fisher, R.A. (1988). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní školy a počítačové vědy </td>
</tr>

<tr>
  <td>Video tweety</td>
  <td>
Datová sada je rozšířená verze datové sady ve formátu videa. Tato datová sada má 170K hodnocení filmů, která se extrahují z dobře strukturovaných tweety na Twitteru. Každá instance představuje prostupnost a je řazená kolekce členů: ID uživatele, ID videa IMDB, hodnocení, časové razítko, počet oblíbených položek pro tento seznam a počet předaných prostředků. Datová sada byla zpřístupněna prostřednictvím. říkáme, S. Dooms, B. loni a D. Tikk pro doporučované systémy Challenge 2014.
  </td>
</tr>

<tr>
  <td>Data MPG pro různé automobilní zařízení</td>
  <td>
Tato datová sada je mírně upravená verze datové sady poskytované knihovnou StatLib Carnegie Mellon University. Datová sada se použila ve Exposition dataassociation 1983 American.
<p></p>
Tato data vypisuje spotřebu paliva pro různé automobilní zařízení v mílích za galon. Obsahuje také informace, jako je počet lahví, posun motoru, výkonová hodnota, celková váha a zrychlení.
<p></p>
<b>Použití:</b> Předpovídání úspor paliva na základě tří diskrétních atributů s více hodnotami a pěti souvislých atributů. 
<p></p>
<b>Související výzkum:</b> StatLib, Carnegie Mellon University, (1993). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní školy a počítačové vědy </td>
</tr>

<tr>
  <td>Datová sada klasifikace Pima Indians diabetes Binary</td>
  <td>
Podmnožina dat z Národního institutu databáze nemocí diabetes a trávící a ledvin. Datová sada se vyfiltroval, aby se zaměřila na saminí pacienty Pima indických dědictví. Data zahrnují zdravotnické údaje, jako jsou glukosy a inulinové úrovně, a také součinitele životního stylu.
<p></p>
<b>Použití:</b> Předpovědět, zda má předmět diabetes (binární klasifikace). 
<p></p>
<b>Související výzkum:</b> Sigillito, V. (1990). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml "</a>. Irvine, CA: University of California, školní školy a počítačové vědy </td>
</tr>

<tr>
  <td>Zákaznická data restaurace</td>
  <td>
Sada metadat o zákaznících, včetně demografických údajů a předvoleb.
<p></p>
<b>Použití:</b> Tuto datovou sadu použijte v kombinaci s dalšími dvěma datovými sadami restaurace, ke školení a testování doporučení systému. 
<p></p>
<b>Související výzkum:</b> Bache, K. a Lichman, M. (2013). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní informace a počítačové vědy.
  </td>
</tr>

<tr>
  <td>Data funkcí restaurace</td>
  <td>
Sada metadat o restauracích a jejich funkcích, jako je například typ potraviny, druh stravování a umístění.
<p></p>
<b>Použití:</b> Tuto datovou sadu použijte v kombinaci s dalšími dvěma datovými sadami restaurace, ke školení a testování doporučení systému. 
<p></p>
<b>Související výzkum:</b> Bache, K. a Lichman, M. (2013). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní informace a počítačové vědy.
  </td>
</tr>

<tr>
  <td>Hodnocení restaurace</td>
  <td>
Obsahuje hodnocení vydaná uživateli do restaurací na škále od 0 do 2.
<p></p>
<b>Použití:</b> Tuto datovou sadu použijte v kombinaci s dalšími dvěma datovými sadami restaurace, ke školení a testování doporučení systému. 
<p></p>
<b>Související výzkum:</b> Bache, K. a Lichman, M. (2013). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní informace a počítačové vědy.
  </td>
</tr>

<tr>
  <td>Žíhání ocelová datová sada s více třídami</td>
  <td>
Tato datová sada obsahuje řadu záznamů z žíháních testů z oceli. Obsahuje fyzické atributy (šířku, tloušťku, typ (cívka, list atd.) výsledných typů oceli.
<p></p>
<b>Použití:</b> Předvídání všech dvou atributů číselné třídy; tvrdost nebo pevnost. Můžete také analyzovat korelace mezi atributy.
<p></p>
Třídy oceli se řídí standardním standardem, který definuje SAE a jiné organizace. Hledáte konkrétní "třídu" (proměnnou třídy) a chcete pochopit potřebné hodnoty. 
<p></p>
<b>Související výzkum:</b> Sterling, D. & Buntine, W. (NA). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of California, školní školy a počítačové vědy
<p></p>
Užitečnou příručku ke jakostním odvětvím najdete tady: <a href="https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf">https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Data v oboru</td>
  <td>
Záznam základních částic s vysokou spotřebou hodnot gamma spolu s hlukem na pozadí se simulují pomocí procesu Monte Carlo.
<p></p>
Účelem simulace bylo zlepšit přesnost atmosféry Cherenkovch hodnot gamma v rozsahu od základu. To se provádí pomocí statistických metod k odlišení od požadovaného signálu (Cherenkov záření) a hluku na pozadí (hadronic sprchy iniciované paprsky COSMIC v horní atmosféře).
<p></p>
Data byla předem zpracována za účelem vytvoření clusteru s prodlouženou osou se zaměřením na centrum kamery. Charakteristiky této elipsy (často označované jako parametry Hillas) jsou mezi parametry obrázku, které se dají použít k diskriminaci.
<p></p>
<b>Použití:</b> Předpovídání, zda obrázek sprchy představuje signál nebo šum na pozadí.
<p></p>
<b>Poznámky:</b> Jednoduchá klasifikace není pro tato data smysluplná, protože klasifikace události na pozadí jako signalizace je horší než klasifikace události signálu jako pozadí. Pro porovnání různých klasifikátorů by se měla použít graf ROC. Pravděpodobnost přijetí události na pozadí jako signalizace musí být pod jedním z následujících mezních hodnot: 0,01, 0,02, 0,05, 0,1 nebo 0,2.
<p></p>
Všimněte si také, že počet událostí na pozadí (h, pro hadronic sprchy) je pododhadovaná. Ve skutečných měřeních představuje třída h nebo Noise většinu událostí. 
<p></p>
<b>Související výzkum:</b> Bock, R.K. (1995). UCI Machine Learning úložiště <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornie, School of Information </td>
</tr>

<tr>
  <td>Datová sada počasí</td>
  <td>
Hodinově založené počasí z NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">sloučená data z 201304 do 201310</a>).
<p></p>
Údaje o počasí se týkají pozorování z povětrnostních stanic na letišti, které pokrývají časové období April-October 2013. Před nahráním do Azure Machine Learning Studio (Classic) byla datová sada zpracována následujícím způsobem:
<ul>
  <li>ID povětrnostních stanic byly namapovány na odpovídající ID letišť.</li>
  <li>Nefiltrované povětrnostní stanice, které nejsou spojené s nejvytíženější letiště 70.</li>
  <li>Sloupec data byl rozdělen na sloupce samostatného roku, měsíce a dne.</li>
  <li>Byly vybrány následující sloupce: AirportID, Year, month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter,,</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedii sada dat SP 500</td>
  <td>
Data jsou odvozena z Wikipedii ( <a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a> ) na základě článků z každé z&společnosti P 500, uložená jako data XML.
<p></p>
Před nahráním do Azure Machine Learning Studio (Classic) byla datová sada zpracována následujícím způsobem:
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

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Datová sada obsahuje zákaznická data a údaje o jejich reakci na přímou poštovní kampaň. Každý řádek představuje zákazníka. Datová sada obsahuje devět funkcí demografických údajů uživatele a minulého chování a tři sloupce popisků (Přejít, převod a výdaje).  Návštěva je binární sloupec, který indikuje, že zákazník navštívil po marketingové kampani. Konverze znamená, že zákazník si koupil nějakého nějakého. Útrata je množství, které bylo stráveno.  Datovou sadu vystavila služba Kevin Hillstrom pro MineThatDatay e-mailové analýzy a pro dolování dat.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funkce příkladů testů v datové sadě RCV1-v2 pro zprávy Reuters Datová sada obsahuje články 781K News spolu s jejich ID (první sloupec datové sady). Každý článek má tokeny, stopworded a lemmatizátor. Datová sada byla zpřístupněna prostřednictvím David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Příklady výukových příkladů v datové sadě RCV1-v2 pro zprávy Reuters Datová sada obsahuje články 23K News spolu s jejich ID (první sloupec datové sady). Každý článek má tokeny, stopworded a lemmatizátor. Datová sada byla zpřístupněna prostřednictvím David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Datová sada z konference KDDu 1999 pro zjišťování znalostí a dolování dat (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Datová sada byla stažena a uložena v úložišti objektů BLOB v Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) a zahrnuje školení a testování datových sad. Datová sada školení má přibližně 126K řádky a 43 sloupců, včetně popisků. Tři sloupce jsou součástí informací popisku a 40 sloupců, které obsahují číselné a řetězcové a kategorií funkce, jsou k dispozici pro školení modelu. Testovací data obsahují přibližně 22.5 K testovacím příkladům se stejnými sloupci 43 jako v školicích datech.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Přiřazení témat pro články s příspěvky v datové sadě RCV1-v2 pro zprávy Reuters Článek s příspěvky lze přiřadit několika tématům. Formát každého řádku je " &lt; název tématu &gt; &lt; dokumentu ID &gt; 1". Datová sada obsahuje 2,6 M přiřazení tématu. Datová sada byla zpřístupněna prostřednictvím David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Tato data pocházejí z konference KDDu pro testování výkonu studenta 2010 (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">vyhodnocení výkonu studenta</a>). Použitá data jsou Algebra_2008_2009 sada školení (razítko, J. NICULESCU-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebraický I 2008-2009. Datová sada výzvy z konference KDD 2010 – výzva k dolování dat vzdělávání. Najdete ho na adrese <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Datová sada byla stažena a uložena v úložišti objektů BLOB v Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) a obsahuje soubory protokolu ze systému student výukovými. Dodávané funkce zahrnují ID problému a jeho stručný popis, ID studenta, časové razítko a počet pokusů o vydaný studenta před vyřešením problému správným způsobem. Původní datová sada má 8,9 M záznamů; Tato datová sada byla vyrozdělena do vzorků na první 100 tisíc řádky. Datová sada má 23 sloupců oddělených tabulátorem různých typů: číselná, kategorií a timestamp.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kickstart své experimenty s příklady](sample-experiments.md)

<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data