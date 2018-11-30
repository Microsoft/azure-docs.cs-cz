---
title: Použití ukázkových datových sad - Azure Machine Learning Studio | Dokumentace Microsoftu
description: Popis datové sady použité v ukázkových modelů, které jsou zahrnuté v nástroji Machine Learning Studio. Tyto ukázkové datové sady můžete použít pro vaše experimenty.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 57d9e1c7b501b67a441dc3490356261fa29ebb7d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308830"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Použití vzorových datových sad v Azure Machine Learning Studiu
[top]: #machine-learning-sample-datasets

Když vytvoříte nový pracovní prostor ve službě Azure Machine Learning, několik ukázkových datových sad a experimenty jsou zahrnuté ve výchozím nastavení. Mnohé z těchto ukázkových datových sad jsou používány ukázkových modelů v [galerii Azure AI](http://gallery.cortanaintelligence.com/). Ostatní jsou zahrnuty jako příklady různých typů dat se obvykle používá ve službě machine learning.

Některé tyto datové sady jsou dostupné ve službě Azure Blob storage. Pro tyto datové sady následující tabulka obsahuje přímý odkaz. Tyto datové sady můžete použít ve své experimenty s použitím [Import dat] [ import-data] modulu.

Zbytek těchto ukázkových datových sad je k dispozici ve vašem pracovním prostoru v části **uložení datové sady**. Zjistíte to paletě modulů nalevo od plátna experimentu v nástroji Machine Learning Studio.
Všechny tyto datové sady můžete použít ve vlastním experimentu přetáhněte na plátno experimentu.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

<table>

<tr>
  <th>Název datové sady</th>
  <th>Popis datové sady</th>
</tr>

<tr>
  <td>Datová sada pro dospělé binární klasifikace příjmů v sčítání</td>
  <td>
Podmnožinu databázi sčítání 1994 využitím dospělé pracovní víc než 16 s indexem upravené příjem > 100.
<p></p>
<b>Použití:</b> klasifikovat uživatele, kteří používají demografické údaje k predikci, jestli osoba nepřesahuje více než 50 tisíc v roce.
<p></p>
<b>Související Research:</b> Kohavi, R., Becker B., (1996). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy </td>
</tr>

<tr>
  <td>Datová sada letiště kódy</td>
  <td>
Kódy letiště USA.
<p></p>
Tato datová sada obsahuje jeden řádek pro každý letiště USA, zadejte do něj letiště identifikační číslo a název spolu s umístění Město a stát.
  </td>
</tr>

<tr>
  <td>Automobilů price data (Raw)</td>
  <td>
Informace o automobilů podle značka a model, včetně cen, funkce, jako je počet cylindrů a MPG, jakož i pojištění rizikové skóre.
<p></p>
Rizikové skóre je zpočátku spojeno s cenou automaticky. To se potom upraví skutečné rizika v procesu známém jako symboling matematici. Hodnota + 3 značí, že automatické je riskantní a hodnota čísla -3 je pravděpodobně bezpečné.
<p></p>
<b>Použití:</b> předvídání skóre rizika na základě funkcí, pomocí klasifikace regrese nebo času odeslání. 
<p></p>
<b>Související Research:</b> Schlimmer, J.C. (1987). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy </td>
</tr>

<tr>
  <td>Datová sada pronájmu Cyklistických kol</td>
  <td>
Pronájmu Cyklistických kol dataset, který je založen na reálná data z Bikeshare velké společnosti, která udržuje síť pronájem kolo v Washington DC.
<p></p>
Datová sada obsahuje jeden řádek pro každou hodinu každý den v 2011 a 2012, celkem tedy 17,379 řádků. Rozsah po hodinách pronajatých kol za je od 1 do 977.

  </td>
</tr>

<tr>
  <td>Obrázek RGB Billem Gatesem</td>
  <td>
Veřejně dostupné image soubor převést na data ve formátu CSV.
<p></p>
Kód pro převod této image najdete v <strong>barva kvantizační použití clusteringu s K-Means</strong> stránku podrobností o model.
  </td>
</tr>

<tr>
  <td>Krve donation dat</td>
  <td>
Podmnožinu dat z databáze dárcovského krve transfuzním služby System Center Hsin Chu Město, Tchaj-wan.
<p></p>
Dárcovského data obsahují měsíců od poslední odběr) a četnost nebo celkový počet odběrů, čas od poslední odběr a množství věnován krve.
<p></p>
<b>Použití:</b> cílem je možnost předvídat pomocí klasifikace, zda dárcovského věnován krve 2007 dne, kde 1 označuje dárcovského během období cíl a 0 bez dárcovského. 
<p></p>
<b>Související Research:</b> Já, systémem, (2008). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy
<p></p>
Já jsem-Cheng, Yang, King-Jang a Nocení, jde značku, "Knowledge discovery na aktualizace modelu s použitím Bernoulliho pořadí,"Expert systémy s aplikacemi, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Mateřské prsu</td>
  <td>
Jeden ze tří rakoviny souvisejících datových sad poskytované radiology ústav, který se zobrazuje často dokumentace machine learning. Kombinuje diagnostické informace a funkce z laboratorní analýzy přibližně 300 tkáňových vzorků.
<p></p>
<b>Použití:</b> umožňuje klasifikovat typ rakoviny, na základě 9 atributů, z nichž některé jsou lineární a některé jsou zařazené do kategorií. 
<p></p>
<b>Související Research:</b> O.L. Wohlberg, čísel, ulici, W.N. & Mangasarian, (1995). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy </td>
</tr>

<tr>
  <td>Funkce rakoviny mateřské <td>
Datová sada obsahuje informace o 102K podezřelých oblastí (kandidáty) X-ray imagí popsány 117 potřebuje pro funkce. Funkce jsou speciální a jejich význam neposkytuje Tvůrce datové sady (Siemens zdravotnictví). 
  </td>
</tr>

<tr>
  <td>Informace o rakovině mateřské</td>
  <td>
Datová sada obsahuje další informace pro každou podezřelou oblast X-ray bitové kopie. Každý příklad obsahuje informace (například popisku, pacientů ID, souřadnice opravy vzhledem k celého obrázku) o příslušné číslo řádku v datové sadě mateřské rakoviny funkce. Každý pacient má několik příkladů. U pacientů, kteří mají rakovinu jsou některé příklady kladné a některé jsou záporné. U pacientů, kteří nemají rakovinu všechny příklady jsou záporné. Objekt dataset obsahuje příklady 102 tis. Tendenční datovou sadu % 0.6 bodů jsou kladné, ostatní jsou záporné. Datová sada se zpřístupní Siemens zdravotní péče.
  </td>
</tr>

<tr>
  <td>Sdílené popisky Appetency CRM</td>
  <td>
Popisky z konference KDD Cup 2009 zákazníka vztah předpovědi před obrovskou výzvou – (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Sdílené popisky změn CRM</td>
  <td>
Popisky z konference KDD Cup 2009 zákazníka vztah předpovědi před obrovskou výzvou – (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>Datová sada CRM sdílené</td>
  <td>
Tato data pochází z konference KDD Cup 2009 zákazníka vztah předpovědi před obrovskou výzvou – (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Datová sada obsahuje 50 tisíc zákazníků francouzské telekomunikační společnosti oranžově. Každý zákazník má 230 anonymizované funkce 190, z nichž jsou číselná a 40 jsou zařazené do kategorií. Funkce jsou velmi zhuštěné.
  </td>
</tr>

<tr>
  <td>Sdílené popisky Upselling CRM</td>
  <td>
Popisky z konference KDD Cup 2009 zákazníka vztah předpovědi před obrovskou výzvou – (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Regrese energetickou účinnost dat</td>
  <td>
Kolekce profily simulované energie, založené na 12 vytváření různých tvarů. Budovy jsou rozlišené pomocí osm funkce. Jedná se o použitém oblast, sklo oblasti distribuce a orientaci.
<p></p>
<b>Použití:</b> používají k vytváření prognóz energetickou účinnost hodnocení závislosti mezi dvěma reálné Vážíme si toho odpovědi regrese nebo klasifikaci. Pro klasifikaci roc se zaokrouhlí proměnné odpovědi na nejbližší celé číslo. 
<p></p>
<b>Související Research:</b> Xifara A. & Tsanas A. (2012). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy </td>
</tr>

<tr>
  <td>Data zpoždění letů</td>
  <td>
Civilní let odloží o včasných údaje o výkonu z TranStats shromažďování dat z USA Department of Transportation (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">včas</a>).
<p></p>
Datová sada obsahuje časové období. dubna – říjen 2013. Než nahrajete do Azure Machine Learning Studio, byla datová sada zpracována následujícím způsobem:
<ul>
  <li>Datová sada se filtrovat k pokrytí pouze 70 za nejvytíženější letiště v kontinentální části USA</li>
  <li>Zrušené lety byly označený jako zpožděné o víc než 15 minut</li>
  <li>Byly odfiltrovány odkloněných lety</li>
  <li>Nebyly vybrány následující sloupce: rok, měsíc, DayofMonth, DayOfWeek, operátora, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, zrušeno</li>
</ul>
</td>
</tr>

<tr>
  <td>Flight o včasných odletech (Raw)</td>
  <td>
Záznamy o doručení letu letadlo a odchylky v rámci USA z října 2011.
<p></p>
<b>Použití:</b> předpověď zpoždění letu. 
<p></p>
<b>Související Research:</b> z USA oddělení of Transportation <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time"> http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time </a>.
  </td>
</tr>

<tr>
  <td>Data o lesních požárech</td>
  <td>
Obsahuje data o počasí, jako je například teploty a vlhkosti indexy a rychlost větru. Data pocházejí z oblasti severovýchodním Portugalsku v kombinaci s záznamy o lesních požárech.
<p></p>
<b>Použití:</b> Toto je úloha obtížné regrese, kde je cílem předpovídat vypálený oblasti lesních požárech. 
<p></p>
<b>Související Research:</b> Cortez P. & Morais A. (2008). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy
<p></p>
[Cortez a Morais 2007] P. Cortez a A. Morais. Data Mining přístup k předvídání lesních požárech pomocí meteorologická Data. V J. Neves, M. F. Santos a J. Machado Edit.:, nových trendů v umělé inteligence, in: Proceedings of 13 EPIA 2007 – portugalština Conference on umělé inteligence, dne, 523-Guimarães, Portugalsko, str. 512, 2007. APPIA, ISBN-13 978-989-95618-0-9. K dispozici na: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf"> http://www.dsi.uminho.pt/~pcortez/fires.pdf </a>.
  </td>
</tr>

<tr>
  <td>Němčina UCI platební kartu datové sady</td>
  <td>
Datová sada UCI Statlog (němčina platební karty) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + němčina + + daty o úvěrovém</a>), pomocí souboru german.data.
<p></p>
Klasifikuje datovou sadu uživatelů, popsaného sadu atributů jako střední nebo vysoké úvěrové riziko. Každý příklad představuje osobu. Existují 20 funkcí, číselná a zařazená do kategorií a binární popisek (hodnota riziko kreditů). Vysoké úvěrové riziko položky mají popisek = 2, nízké úvěrové riziko položky mají popisek = 1. Náklady na misclassifying příklad s nízkým rizikem jako vysoké je 1, zatímco misclassifying příklad vysoké riziko co nejnižší náklady na je 5.
  </td>
</tr>

<tr>
  <td>Názvy IMDB filmů</td>
  <td>
Datová sada obsahuje informace o videa, která byla hodnocena v Twitteru tweety: IMDB film ID, název filmu, rozšířením podle tematických a výrobní rok. Existují 17K filmy v datové sadě. Datová sada byla zavedena v dokumentu "S. Dooms T. De Pessemier a L. Martens. MovieTweetings: filmu hodnocení datové sady se shromažďují z Twitteru. Seminář o Crowdsourcingu a lidské výpočtu systémů doporučení CrowdRec na RecSys 2013."
  </td>
</tr>

<tr>
  <td>Data Iris dvě třídy</td>
  <td>
To je pravděpodobně nejznámější databáze najdete v dokumentaci rozpoznávání vzor. Tato datová sada je poměrně malý, obsahující 50 příklady každý petal měření z tři typy iris.
<p></p>
<b>Použití:</b> předpověď typu iris z měření.  
<p></p>
<b>Související Research:</b> Fisher R.A. (1988). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy </td>
</tr>

<tr>
  <td>Video Tweetů</td>
  <td>
Tato datová sada je rozšířenou verzi film Tweetings datové sady. Objekt dataset obsahuje 170K hodnocení filmů, extrahovat z strukturované tweetů na Twitteru. Každá instance představuje tweet a n-tice: ID uživatele, ID IMDB film, hodnocení, časové razítko, počet Oblíbené položky pro tento tweet a počet retweets tento tweet. Datová sada byla k dispozici A. říká, že, S. Dooms, Loni b a D. Tikk pro doporučení systémy Challenge 2014.
  </td>
</tr>

<tr>
  <td>Data MPG u různých automobilů</td>
  <td>
Tato datová sada je mírně upravenou verzi poskytovaných knihovnou StatLib univerzity Carnegie Mellon datové sady. 1983 American statistické přidružení budeme použila datové sady.
<p></p>
Data jsou uvedeny spotřeby paliva u různých automobilů v mil za paliva. Také obsahuje informace, jako je počet cylindrů, modul posouvání, výkon, Celková váha a akceleraci.
<p></p>
<b>Použití:</b> předpovědět paliva na základě tří diskrétních atributů s více hodnotami a pět souvislé atributy. 
<p></p>
<b>Související Research:</b> StatLib Carnegie Mellon University, (1993). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy </td>
</tr>

<tr>
  <td>Binární klasifikace diabetem Pima indiánů datové sady</td>
  <td>
Podmnožinu dat z národní Institute diabetem a trávícího a nemocemi chránit ledviny databáze. Datové sady se filtrovat, aby se zaměřují na Ženský pacientů Indická dědictví Pima. Data obsahují lékařská data, jako jsou glukosy a inulinový úrovně, jakož i lifestyle faktorů.
<p></p>
<b>Použití:</b> předpovědět, jestli má předmět diabetem (binární klasifikace). 
<p></p>
<b>Související Research:</b> Sigillito, V. (1990). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml"</a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy </td>
</tr>

<tr>
  <td>Restaurace zákaznická data</td>
  <td>
Sada metadata o zákaznících, včetně demografických údajů a předvolby.
<p></p>
<b>Použití:</b> použít tuto datovou sadu v kombinaci s další dva restaurace datové sady, trénování a testování systému doporučení. 
<p></p>
<b>Související Research:</b> Bache K. a Lichman, M. (2013). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačových věd.
  </td>
</tr>

<tr>
  <td>Data funkce restaurace</td>
  <td>
Sada metadata o restaurace a jejich funkce, jako je například typ food, obědvajících stylu a umístění.
<p></p>
<b>Použití:</b> použít tuto datovou sadu v kombinaci s další dva restaurace datové sady, trénování a testování systému doporučení. 
<p></p>
<b>Související Research:</b> Bache K. a Lichman, M. (2013). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačových věd.
  </td>
</tr>

<tr>
  <td>Restaurace hodnocení</td>
  <td>
Obsahuje hodnocení vzhledem uživateli restaurace na škále od 0 do 2.
<p></p>
<b>Použití:</b> použít tuto datovou sadu v kombinaci s další dva restaurace datové sady, trénování a testování systému doporučení. 
<p></p>
<b>Související Research:</b> Bache K. a Lichman, M. (2013). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačových věd.
  </td>
</tr>

<tr>
  <td>Oceli Annealing roc dataset</td>
  <td>
Tato datová sada obsahuje řadu záznamů z oceli žíhání zkušební verze. Obsahuje fyzické atributy (šířku, tloušťka, typ (smyčka, list, atd.) výsledný oceli typy.
<p></p>
<b>Použití:</b> předpovědět dva atributy číselné třídy; tvrdosti nebo šifrování. Může také analyzovat korelace mezi atributy.
<p></p>
Oceli známek použijte standardní sadu definovaném SAE a jiných společností. Hledáte konkrétní "třída" (třída proměnná) a chcete pochopit, jaké hodnoty potřebné. 
<p></p>
<b>Související Research:</b> Sterlingem, d & Buntine, W. (NENÍ K DISPOZICI). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole a počítačové vědy
<p></p>
Užitečný průvodce oceli známek najdete tady: <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Telescope dat</td>
  <td>
Záznam vysokou energie gama částice nárůstem spolu s šum na pozadí, obě simulované pomocí simulace typu Monte Carlo procesu.
<p></p>
Aby se zlepšila přesnost založené na začátku atmosféru Cherenkov gama dalekohledy bylo záměrem simulace. To se provádí pomocí statistické metody rozlišovat mezi požadované signál (Cherenkov záření sprchy) a šum na pozadí (hadronic sprchy iniciovaných cosmic paprsky v horním prostředí).
<p></p>
Data byla předběžného zpracování k vytvoření clusteru služby prodlouženého s long osy je zaměřen na centru fotoaparát. Vlastnosti tohoto tři tečky (často označované jako parametry Hillas) jsou mezi parametry bitové kopie, které lze použít pro diskriminace.
<p></p>
<b>Použití:</b> předpovědět, jestli obrázek oslavu představuje šumu signál nebo na pozadí.
<p></p>
<b>Poznámky:</b> jednoduché klasifikace přesnost není smysl pro tato data od klasifikace události na pozadí, jako je signál horší než klasifikace signál události jako pozadí. Pro porovnání různých třídění je třeba použít graf roc s více TŘÍDAMI. Pravděpodobnost přijetí události na pozadí jako signálu musí mít míň než jednu z těchto prahových hodnot: 0.01, 0,02, 0,05, 0.1 nebo 0.2.
<p></p>
Všimněte si také, že je podcenit počet událostí na pozadí (pro hadronic sprchy h). V reálných představuje třídu h nebo šumu většinou událostí. 
<p></p>
<b>Související Research:</b> Bock, R.K. (1995). UCI strojového učení úložiště <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, certifikační Autorita: University of California, z informací o škole </td>
</tr>

<tr>
  <td>Datová sada počasí</td>
  <td>
Po hodinách na základě pozemního počasí pozorování z NOAA (<a href="http://az754797.vo.msecnd.net/data/WeatherDataset.csv">sloučit data z 201304 201310</a>).
<p></p>
Data o počasí pokrývá pozorování z letiště meteorologická stanice, pokrývající časové období. dubna – říjen 2013. Než nahrajete do Azure Machine Learning Studio, byla datová sada zpracována následujícím způsobem:
<ul>
  <li>Meteorologická stanice identifikátory byly mapovány na letišti odpovídající ID</li>
  <li>Stanice počasí nejsou spojena s 70 za nejvytíženější letiště byly odfiltrovány.</li>
  <li>Sloupec data byla rozdělit na samostatné sloupce, rok, měsíc a den</li>
  <li>Nebyly vybrány následující sloupce: AirportID, rok, měsíc, den, čas, časové pásmo, SkyCondition, viditelnost, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius a RelativeHumidity, Rychlost větru WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, typ záznamu, HourlyPrecip, výškoměru</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 datové sady</td>
  <td>
Data jsou odvozena z wikipedii (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) založené na články S & P 500 společnosti, ukládají jako XML data.
<p></p>
Než nahrajete do Azure Machine Learning Studio, byla datová sada zpracována následujícím způsobem:
<ul>
  <li>Extrahování textového obsahu u každé konkrétní společnosti</li>
  <li>Odebrat formátování wiki</li>
  <li>Odeberte jiné než alfanumerické znaky</li>
  <li>Veškerý text převést na malá písmena</li>
  <li>Kategorie známé společnosti byly přidány.</li>
</ul>
<p></p>
Všimněte si, že pro některé společnosti článku nelze nalézt, tak počet záznamů, které je menší než 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Datová sada obsahuje zákaznická data a údaje o jejich reakce na kampaň poštovní s přímým přístupem. Každý řádek představuje zákazník. Datová sada obsahuje devět funkce o demografických údajích uživatelů a za chování a třetí popisek sloupce (navštíví, převod a věnovat).  Navštivte je binární sloupec, který označuje, že zákazník zobrazeny po marketingové kampaně. Převod označuje, že zákazník něco zakoupili. Výdaje, který byl vynaložen na velikost.  Datová sada se zpřístupní Kevin Hillstrom pro MineThatData e-mailu Analytics a Data Mining výzvu.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funkce testu příklady v této datové sadě RCV1 V2 Reuters zpráv. Objekt dataset obsahuje články o novinkách 781K spolu s jejich ID (první sloupec datové sady). Každý článek je tokenizovaného stopworded a nasadí. Datová sada se zpřístupní David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funkce školení příklady v této datové sadě RCV1 V2 Reuters zpráv. Objekt dataset obsahuje články o novinkách 23K spolu s jejich ID (první sloupec datové sady). Každý článek je tokenizovaného stopworded a nasadí. Datová sada se zpřístupní David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Nástroje datové sady z konference KDD Cup 1999 Knowledge Discovery a dolování dat. soutěže (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Datová sada byla stažena a ukládají ve službě Azure Blob storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) a zahrnuje i trénování a testování datových sad. Trénovací datové sady má přibližně 126 tisíc řádků a sloupců 43, včetně popisků. Tři sloupce jsou součástí informace popisku a 40 sloupců, skládající se z funkce číselné a řetězec/zařazené do kategorií, které jsou k dispozici pro trénování modelu. Testovací data se přibližně 22.5K testování příkladů se stejnými sloupci 43, stejně jako v trénovací data.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Téma přiřazení pro články o novinkách v této datové sadě RCV1 V2 Reuters zpráv. Zpravodajskému článku je možné přiřadit na několik témat. Každý řádek formát je "&lt;název tématu&gt; &lt;id dokumentu&gt; 1". Datová sada obsahuje 2,6 milionů tématu přiřazení. Datová sada se zpřístupní David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Tato data pochází z konference KDD Cup 2010 Student výkonu hodnocení před obrovskou výzvou – (<a href="http://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">hodnocení výkonu studentů</a>). Data používaná je cvičnou sadou Algebra_2008_2009 (Niculescu Stamper J.,-Mizil, S. A. Ritter, Gordon, G.J. a Koedinger k. r. (2010). Algebraický můžu 2008-2009. Datová sada Challenge z konference KDD Cup 2010 vzdělávací Data Mining Challenge. Nachází se na <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Datová sada byla stažena a ukládají ve službě Azure Blob storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) a obsahuje soubory protokolu z student výukovými systému. Zadaná funkce zahrnují ID problému a jeho stručný popis, ID studenta, časové razítko a kolik pokusů student vytvořená před správným způsobem řešení tohoto problému. Původní datové sady má záznamy 8,9 M; Tato datová sada se předvýpočtem zredukovaných na prvních 100 tisíc řádků. Objekt dataset obsahuje 23 oddělené tabulátorem sloupci různých typů: číselné literály, kategorií a časové razítko.
  </td>
</tr>

</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
