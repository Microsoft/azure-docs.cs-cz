---
title: Použití ukázkových datových sad
titleSuffix: ML Studio (classic) - Azure
description: Popisy datových sad použitých v ukázkových modelech zahrnutých v Machine Learning Studio (klasické). Tyto ukázkové datové sady můžete použít pro experimenty.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: b7b8606f7f15f8d6fdd66681a1c7ade60ff506f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217785"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Použití ukázkových datových sad v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[top]: #machine-learning-sample-datasets

Když vytvoříte nový pracovní prostor v Azure Machine Learning Studio (klasické), řada ukázkových datových sad a experimentů jsou zahrnuty ve výchozím nastavení. Mnoho z těchto ukázkových datových sad se používá ukázkovými modely v [Galerii Azure AI](https://gallery.azure.ai/). Jiné jsou zahrnuty jako příklady různých typů dat, které se obvykle používají ve strojovém učení.

Některé z těchto datových sad jsou k dispozici v úložišti objektů blob Azure. Pro tyto datové sady obsahuje následující tabulka přímé propojení. Tyto datové sady můžete použít v experimentech pomocí modulu [Importovat data.][import-data]

Zbývající části těchto ukázkových datových sad jsou k dispozici v pracovním prostoru v části **Uložené datové sady**. Najdete ji v paletě modulů nalevo od plátna experimentu v Machine Learning Studiu (klasika).
Kteroukoli z těchto datových sad můžete použít ve vlastním experimentu přetažením na plátno experimentu.

## <a name="datasets"></a>Datové sady

<table>

<tr>
  <th>Název datové sady</th>
  <th>Popis datové sady</th>
</tr>

<tr>
  <td>Dospělý sčítání lidu příjem binární klasifikace datový soubor</td>
  <td>
Podmnožina databáze sčítání lidu z roku 1994, která využívá pracující dospělé osoby starší 16 let s upraveným indexem příjmů > 100.
<p></p>
<b>Použití:</b> Klasifikovat lidi pomocí demografie předpovědět, zda osoba vydělává více než 50 Tisíc ročně.
<p></p>
<b>Související výzkum:</b> Kohavi, R., Becker, B., (1996). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky </td>
</tr>

<tr>
  <td>Datová sada kódů letiště</td>
  <td>
Kódy letišť v USA.
<p></p>
Tato datová sada obsahuje jeden řádek pro každé letiště v USA, který poskytuje číslo a název letiště spolu s městem a státem umístění.
  </td>
</tr>

<tr>
  <td>Údaje o cenách automobilů (Raw)</td>
  <td>
Informace o automobilech podle makety a modelu, včetně ceny, funkce, jako je počet válců a MPG, stejně jako skóre pojistného rizika.
<p></p>
Skóre rizika je zpočátku spojeno s cenou auto. Poté se upraví pro skutečné riziko v procesu, o jehož pojistníkovi je známo, že symbolizuje. Hodnota +3 označuje, že auto je riskantní a hodnota -3, že je pravděpodobně bezpečné.
<p></p>
<b>Použití:</b> Předpovědět skóre rizika podle funkcí pomocí regrese nebo multivariate klasifikace. 
<p></p>
<b>Související výzkum:</b> Schlimmer, J.C. (1987). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky </td>
</tr>

<tr>
  <td>Datový soubor Půjčovna kol UCI</td>
  <td>
UCI Bike Půjčovna datová sada, která je založena na skutečných datech od společnosti Capital Bikeshare, která udržuje půjčovnu kol sítě ve Washingtonu DC.
<p></p>
Datová sada má jeden řádek pro každou hodinu každý den v 2011 a 2012, celkem 17,379 řádků. Nabídka hodinových půjčoven kol je od 1 do 977.

  </td>
</tr>

<tr>
  <td>Obrázek RGB Billa Gatese</td>
  <td>
Veřejně dostupný soubor obrázků převedený na data CSV.
<p></p>
Kód pro převod obrazu je k dispozici v kvantizaci barev pomocí stránky s podrobnostmi modelu <strong>clusteringu K-Prostředky.</strong>
  </td>
</tr>

<tr>
  <td>Údaje o dárcovství krve</td>
  <td>
Podmnožina dat z databáze dárců krve v centru služby transfuze krve v Hsin-Chu City na Tchaj-wanu.
<p></p>
Údaje o dárcích zahrnují měsíce od posledního darování) a četnost nebo celkový počet odběrů, čas od posledního darování a množství darované krve.
<p></p>
<b>Použití:</b> Cílem je prostřednictvím klasifikace předpovědět, zda dárce daroval krev v březnu 2007, kde 1 označuje dárce během cílového období a 0 nedárce. 
<p></p>
<b>Související výzkum:</b> Ano, I.C., (2008). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky
<p></p>
Yeh, I-Cheng, Yang, King-Jang, a Ting, Tao-Ming, "Zjišťování znalostí na modelu RFM pomocí Bernoulliho sekvence, "Expertní systémy s aplikacemi, 2008,<a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Údaje o rakovině prsu</td>
  <td>
Jeden ze tří datových sad souvisejících s rakovinou poskytovaných onkologickým institutem, který se často objevuje v literatuře strojového učení. Kombinuje diagnostické informace s funkcemi z laboratorní analýzy asi 300 vzorků tkáně.
<p></p>
<b>Použití:</b> Klasifikují typ rakoviny, na základě 9 atributů, z nichž některé jsou lineární a některé jsou kategorické. 
<p></p>
<b>Související výzkum:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky </td>
</tr>

<tr>
  <td>Rakovina prsu Funkce <td>
Datová sada obsahuje informace pro 102 kB podezřelých oblastí (kandidátů) rentgenových snímků, z nichž každý je popsán 117 funkcemi. Funkce jsou proprietární a jejich význam není odhalen tvůrci datové sady (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Rakovina prsu Info</td>
  <td>
Datová sada obsahuje další informace pro každou podezřelou oblast rentgenového snímku. Každý příklad obsahuje informace (například popisek, ID pacienta, souřadnice opravy vzhledem k celému obrázku) o odpovídajícím čísle řádku v datové sadě Funkce rakoviny prsu. Každý pacient má řadu příkladů. U pacientů, kteří mají rakovinu, některé příklady jsou pozitivní a některé jsou negativní. U pacientů, kteří nemají rakovinu, jsou všechny příklady negativní. Datová sada má příklady 102 kS. Datová sada je zkreslená, 0,6% bodů je pozitivních, zbytek je negativní. Datový soubor byl zpřístupněn společností Siemens Healthcare.
  </td>
</tr>

<tr>
  <td>Sdílené popisky appetency aplikace CRM</td>
  <td>
Štítky z KDD Cup 2009 zákazník a predikce vztahů se zákazníky<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">(orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Sdílené popisky změn CRM</td>
  <td>
Štítky z KDD Cup 2009 zákazník a predikce vztahů se zákazníky<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">(orange_small_train_churn.labels).</a>
  </td>
</tr>

<tr>
  <td>Sdílená datová sada CRM</td>
  <td>
Tato data pocházejí z KDD Cup 2009 zákazník a predikce vztahů se zákazníky<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">(orange_small_train.data.zip</a>).
<p></p>
Datová sada obsahuje 50tisíc zákazníků od francouzské telekomunikační společnosti Orange. Každý zákazník má 230 anonymizovaných funkcí, z nichž 190 je číselných a 40 kategorických. Funkce jsou velmi řídké.
  </td>
</tr>

<tr>
  <td>Sdílené štítky pro upselling aplikace CRM</td>
  <td>
Štítky z KDD Cup 2009 zákazník a predikce vztahů se<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">zákazníky (orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Regresní údaje o energetické účinnosti</td>
  <td>
Kolekce simulovaných energetických profilů založených na 12 různých tvarech budovy. Budovy jsou rozlišeny osmi prvky. To zahrnuje oblast zasklení, rozložení oblasti zasklení a orientaci.
<p></p>
<b>Použití:</b> Použijte buď regresi nebo klasifikaci, abyste předpověděli hodnocení energetické účinnosti založené jako jedna ze dvou reálných hodnotných odpovědí. Pro klasifikaci více tříd je zaokrouhlena proměnná odezvy na nejbližší celé číslo. 
<p></p>
<b>Související výzkum:</b> Xifara, A. & Tsanas, A. (2012). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky </td>
</tr>

<tr>
  <td>Údaje o zpoždění letu</td>
  <td>
Údaje o výkonu letu cestujících v době přepravě převzaté ze sběru dat transtatů ministerstva dopravy USA<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">(On-Time).</a>
<p></p>
Soubor údajů pokrývá období od dubna do října 2013. Před nahráním do Azure Machine Learning Studio (klasické) byla datová sada zpracována následujícím způsobem:
<ul>
  <li>Datová sada byla filtrována tak, aby pokrývala pouze 70 nejrušnějších letišť v kontinentálních</li>
  <li>Zrušené lety byly označeny jako zpožděné o více než 15 minut</li>
  <li>Odkloněné lety byly odfiltrovány</li>
  <li>Byly vybrány následující sloupce: Rok, Měsíc, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Zrušeno</li>
</ul>
</td>
</tr>

<tr>
  <td>Letový včasový výkon (nezpracovaný)</td>
  <td>
Záznamy o přílety letadel a odletů v rámci Spojených států od října 2011.
<p></p>
<b>Použití:</b> Předvídejte zpoždění letu. 
<p></p>
<b>Související výzkum:</b> Z ministerstva dopravy <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>USA .
  </td>
</tr>

<tr>
  <td>Data lesních požárů</td>
  <td>
Obsahuje údaje o počasí, jako jsou indexy teploty a vlhkosti a rychlost větru. Údaje jsou převzaty z oblasti severovýchodního Portugalska v kombinaci se záznamy o lesních požárech.
<p></p>
<b>Použití:</b> Jedná se o obtížný regresní úkol, kde je cílem předpovědět spálenou oblast lesních požárů. 
<p></p>
<b>Související výzkum:</b> Cortez, P., & Morais, A. (2008). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky
<p></p>
[Cortez a Morais, 2007] P. Cortez a A. Morais. Přístup dolování dat předpovědět lesní požáry pomocí meteorologických dat. V J. Neves, M. F. Santos a J. Machado Eds., Nové trendy v oblasti umělé inteligence, Sborník 13th EPIA 2007 - Portugalská konference o umělé inteligenci, prosinec, Guimarães, Portugalsko, s. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. K dispozici <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>na adrese: .
  </td>
</tr>

<tr>
  <td>Německý datový soubor UCI kreditní karty</td>
  <td>
Datová sada UCI Statlog (německá kreditní karta) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data)</a>používající soubor german.data.
<p></p>
Datová sada klasifikuje osoby, které je popsáno sadou atributů, jako nízké nebo vysoké úvěrové riziko. Každý příklad představuje osobu. K dispozici je 20 funkcí, číselných i kategorických, a binární popisek (hodnota úvěrového rizika). Položky s vysokým úvěrovým rizikem mají popisek = 2, položky s nízkým úvěrovým rizikem mají popisek = 1. Náklady na nesprávné klasifikaci příkladu s nízkým rizikem jako vysoké jsou 1, zatímco náklady na chybnou klasifikaci vysoce rizikového příkladu jako nízké jsou 5.
  </td>
</tr>

<tr>
  <td>Filmové tituly IMDB</td>
  <td>
Datová sada obsahuje informace o filmech, které byly hodnoceny v tweetech Twitteru: ID filmu ID IMDB, název filmu, žánr a rok výroby. V datové sadě je 17 kS. Datová sada byla zavedena v dokumentu "S. Dooms, T. De Pessemier a L. Martens. MovieTweetings: Movie Rating Datová sada shromážděná z Twitteru. Workshop o crowdsourcingu a lidském výpočtu pro doporučující systémy, CrowdRec na recsys 2013."
  </td>
</tr>

<tr>
  <td>Iris dvě třídy data</td>
  <td>
Toto je možná nejznámější databáze, která se nachází v literatuře rozpoznávání vzorů. Datová sada je relativně malá a obsahuje 50 příkladů každého z měření okvětních plodů ze tří odrůd duhovky.
<p></p>
<b>Použití:</b> Předpovědět typ duhovky z měření.  
<p></p>
<b>Související výzkum:</b> Fisher, R.A. (1988). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky </td>
</tr>

<tr>
  <td>Film Tweets</td>
  <td>
Datová sada je rozšířená verze datové sady Movie Tweetings. Datová sada má hodnocení 170K pro filmy, extrahované z dobře strukturovaných tweetů na Twitteru. Každá instance představuje pípání a je n-tice: ID uživatele, IMDB film ID, hodnocení, časové razítko, počet oblíbených pro tento tweet, a počet retweets tohoto pípání. Datovou sadu zpřístupnili A. Said, S. Dooms, B. Loni a D. Tikk pro Recommender Systems Challenge 2014.
  </td>
</tr>

<tr>
  <td>MPG data pro různé automobily</td>
  <td>
Tato datová sada je mírně upravená verze datové sady poskytované knihovnou StatLib univerzity Carnegie Mellon. Soubor dat byl použit v roce 1983 v Americké statistické asociaci Expozice.
<p></p>
Data uvádějí spotřebu paliva pro různé automobily v mílích na galon. Obsahuje také informace, jako je počet válců, zdvihový objem motoru, výkon, celková hmotnost a zrychlení.
<p></p>
<b>Použití:</b> Předvídejte spotřebu paliva na základě tří vícehodnotových diskrétních atributů a pěti spojitých atributů. 
<p></p>
<b>Související výzkum:</b> StatLib, Univerzita Carnegie Mellon, (1993). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky </td>
</tr>

<tr>
  <td>Pima Indiáni Diabetes Binární klasifikace datový soubor</td>
  <td>
Podmnožina dat z databáze Národního institutu pro diabetes a trávicí a ledvinová onemocnění. Datová sada byla filtrována tak, aby se zaměřila na pacientky indického dědictví Pima. Údaje zahrnují lékařské údaje, jako je hladina glukózy a inzulínu, stejně jako faktory životního stylu.
<p></p>
<b>Použití:</b> Předpovědět, zda předmět má cukrovku (binární klasifikace). 
<p></p>
<b>Související výzkum:</b> Sigillito, V. (1990). Úložiště strojového učení UCI <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml"</a>. Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky </td>
</tr>

<tr>
  <td>Zákaznická data restaurace</td>
  <td>
Sada metadat o zákaznících, včetně demografických údajů a předvoleb.
<p></p>
<b>Použití:</b> Pomocí této datové sady v kombinaci s dalšími dvěma datovými sadami restaurace můžete trénovat a testovat systém doporučování. 
<p></p>
<b>Související výzkum:</b> Bache, K. a Lichman, M. (2013). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, KALIFORNIE: Kalifornská univerzita, Škola informací a informatiky.
  </td>
</tr>

<tr>
  <td>Data funkcí restaurace</td>
  <td>
Sada metadat o restauracích a jejich funkcích, jako je typ jídla, styl stravování a umístění.
<p></p>
<b>Použití:</b> Pomocí této datové sady v kombinaci s dalšími dvěma datovými sadami restaurace můžete trénovat a testovat systém doporučování. 
<p></p>
<b>Související výzkum:</b> Bache, K. a Lichman, M. (2013). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, KALIFORNIE: Kalifornská univerzita, Škola informací a informatiky.
  </td>
</tr>

<tr>
  <td>Hodnocení restaurací</td>
  <td>
Obsahuje hodnocení, která uživatelé udali restauracím na stupnici od 0 do 2.
<p></p>
<b>Použití:</b> Pomocí této datové sady v kombinaci s dalšími dvěma datovými sadami restaurace můžete trénovat a testovat systém doporučování. 
<p></p>
<b>Související výzkum:</b> Bache, K. a Lichman, M. (2013). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, KALIFORNIE: Kalifornská univerzita, Škola informací a informatiky.
  </td>
</tr>

<tr>
  <td>Vícetřídní datová sada pro žíhání oceli</td>
  <td>
Tato datová sada obsahuje řadu záznamů ze zkoušek žíhání oceli. Obsahuje fyzikální atributy (šířka, tloušťka, typ (cívka, plech atd.) výsledných typů oceli.
<p></p>
<b>Použití:</b> Předpovědět některý ze dvou atributů číselné třídy; tvrdost nebo pevnost. Můžete také analyzovat korelace mezi atributy.
<p></p>
Třídy oceli se řídí stanoveným standardem definovaným SAE a dalšími organizacemi. Hledáte konkrétní 'stupeň' (proměnná třídy) a chcete pochopit potřebné hodnoty. 
<p></p>
<b>Související výzkum:</b> Sterling, D. & Buntine, W. (NA). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, CA: Kalifornská univerzita, Škola informatiky a informatiky
<p></p>
Užitečný průvodce pro třídy oceli lze nalézt zde:<a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Data dalekohledu</td>
  <td>
Záznam vysokoenergetických gama částic praskne spolu s hlukem pozadí, oba simulované pomocí procesu Monte Carlo.
<p></p>
Záměrem simulace bylo zlepšit přesnost pozemních atmosférických Cherenkovových gama teleskopů. To se provádí pomocí statistických metod rozlišovat mezi požadovaným signálem (Cherenkov záření sprchy) a hluk v pozadí (hadroické sprchy iniciované kosmického záření v horní atmosféře).
<p></p>
Data byla předem zpracována za účelem vytvoření podlouhlého clusteru s dlouhou osou orientovanou směrem ke středu kamery. Charakteristiky této elipsy (často nazývané Parametry Hillas) patří mezi parametry obrazu, které lze použít k diskriminaci.
<p></p>
<b>Použití:</b> Předpovědět, zda obraz sprchy představuje signál nebo šum na pozadí.
<p></p>
<b>Poznámky:</b> Jednoduchá přesnost klasifikace není pro tato data smysluplná, protože klasifikace události na pozadí jako signálje horší než klasifikace události signálu jako pozadí. Pro porovnání různých klasifikátorů by měl být použit graf ROC. Pravděpodobnost přijetí události na pozadí jako signálu musí být nižší než jedna z následujících prahových hodnot: 0,01, 0,02, 0,05, 0,1 nebo 0,2.
<p></p>
Také si všimněte, že počet doprovodných událostí (h, pro hadronické sprchy) je podceňován. V reálných měřeních představuje třída h nebo hluk většinu událostí. 
<p></p>
<b>Související výzkum:</b> Bock, R.K. (1995). Úložiště strojového učení <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>UCI . Irvine, KALIFORNIE: Kalifornská univerzita, Škola informací </td>
</tr>

<tr>
  <td>Datová sada počasí</td>
  <td>
Hodinová pozemní pozorování počasí z NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">sloučené údaje z let 201304 až 201310</a>).
<p></p>
Údaje o počasí zahrnují pozorování z letištních meteorologických stanic, která pokrývají období od dubna do října 2013. Před nahráním do Azure Machine Learning Studio (klasické) byla datová sada zpracována následujícím způsobem:
<ul>
  <li>ID meteorologické stanice byly namapovány na odpovídající ID letiště</li>
  <li>Meteorologické stanice, které nejsou spojeny se 70 nejrušnějšími letišti, byly odfiltrovány</li>
  <li>Sloupec Datum byl rozdělen do samostatných sloupců Rok, Měsíc a Den.</li>
  <li>Byly vybrány následující sloupce: AirportID, Rok, Měsíc, Den, Čas, TimeZone, SkyCondition, Viditelnost, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Datová sada Wikipedie SP 500</td>
  <td>
Data jsou odvozena z<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>Wikipedie ( ) na základě článků každé společnosti S&P 500, uložených jako XML data.
<p></p>
Před nahráním do Azure Machine Learning Studio (klasické) byla datová sada zpracována následujícím způsobem:
<ul>
  <li>Extrahování textového obsahu pro každou konkrétní společnost</li>
  <li>Odebrání formátování wikiwebu</li>
  <li>Odebrání nealfanumerických znaků</li>
  <li>Převést veškerý text na malá písmena</li>
  <li>Byly přidány známé kategorie společností.</li>
</ul>
<p></p>
Všimněte si, že u některých společností nebyl nalezen článek, takže počet záznamů je menší než 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Datová sada obsahuje údaje o zákaznících a údaje o jejich reakci na přímou poštovní kampaň. Každý řádek představuje zákazníka. Datová sada obsahuje devět funkcí o demografických údajích uživatelů a chování v minulosti a tři sloupce popisků (návštěva, konverze a výdaje).  Návštěva je binární sloupec, který označuje, že zákazník navštívil po marketingové kampani. Konverze označuje, že zákazník něco zakoupil. Útrata je částka, která byla vynaložena.  Datový soubor byl k dispozici Kevin Hillstrom pro MineThatData E-Mail Analytics a data Mining Challenge.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funkce testovacích příkladů v datové sadě zpráv RCV1-V2 Reuters. Datová sada obsahuje zpravodajské články 781 kB spolu s jejich ID (první sloupec datové sady). Každý článek je tokenizován, stopworded a stemmed. Datovou sadu zpřístupnil David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funkce školení příklady v RCV1-V2 Reuters novinky datové sady. Datová sada obsahuje 23 kB zpravodajských článků spolu s jejich ID (první sloupec datové sady). Každý článek je tokenizován, stopworded a stemmed. Datovou sadu zpřístupnil David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Datová sada z KDD Cupu 1999 Knowledge Discovery and Data Mining Tools Competition<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">(kddcup99.html).</a>
<p></p>
Datová sada byla stažena a uložena v úložišti objektů Blob Azure<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">(network_intrusion_detection.csv)</a>a zahrnuje trénovací i testovací datové sady. Trénovací datová sada má přibližně 126 kB řádků a 43 sloupců, včetně popisků. Tři sloupce jsou součástí informace popisek a 40 sloupců, skládající se z číselné a řetězec/kategorické prvky, jsou k dispozici pro školení modelu. Testovací data má přibližně 22.5K příklady testů se stejnými sloupci 43 jako v trénovacích datech.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Téma přiřazení pro zpravodajské články v RCV1-V2 Reuters novinky datové sady. Zpravodajský článek lze přiřadit k několika tématům. Formát každého řádku je&lt;"&gt; &lt;název&gt; tématu document id 1 ". Datová sada obsahuje přiřazení tématu 2.6M. Datovou sadu zpřístupnil David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Tato data pocházejí z KDD Cup 2010 Hodnocení výkonnosti studentů výzvu<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">(hodnocení výkonnosti studentů</a>). Použitá data jsou Algebra_2008_2009 trénovací množina (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Challenge datový soubor z KDD Cup 2010 vzdělávací data mining challenge. Najít na <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Datová sada byla stažena a uložena v úložišti objektů Blob Azure<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">(student_performance.txt)</a>a obsahuje soubory protokolu ze systému doučování studentů. Mezi dodané funkce patří ID problému a jeho stručný popis, ID studenta, časové razítko a kolik pokusů student učinil před správným řešením problému. Původní datová sada má 8,9 milionů záznamů; tato datová sada byla vzorkována dolů na první řádky 100 kB. Datová sada má 23 sloupců oddělených kartami různých typů: číselné, kategorické a časové razítko.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastartujte své experimenty s příklady](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
