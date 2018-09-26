---
title: Veřejné datové sady pro Azure analytics | Dokumentace Microsoftu
description: Další informace o veřejných datových sadách, můžete použít k rychlému vytvoření prototypu a testovat řešení a analytické služby Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: douglaslMS
ms.author: douglasl
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 6977995fb33d221b48a633076f61d3ee39d6ba40
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160413"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Veřejných datových sadách pro tvorbu prototypů a testování

Projdete seznam veřejných datových sadách pro data, která vám pomůže se služby úložiště a analytics prototypu a testování a řešení.

## <a name="us-government-and-agency-data"></a>USA Státní správy a agentura dat

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Data pro státní správu USA](https://www.census.gov/data.html) | Více než 190,000 datových sad, které pokrývají zemědělství, klimatu, příjemce, ekosystémů, vzdělávání, energie, financí, stavu, samosprávy, výroba, námořní, oceánu, bezpečnost veřejnosti a vědy a výzkum v USA | Soubory různých velikostí v různých formátech, včetně HTML, XML, CSV, JSON, Excel a mnoha dalších. Dostupné datové sady můžete filtrovat podle formátu souboru. |
| [Data sčítání USA](https://www.census.gov/data.html) | Statistická data o populaci USA | Datové sady jsou v různých formátech. |
| [Data Earth science z NASA](https://earthdata.nasa.gov/) | Zemědělství, prostředí, biosféra, klimatu, cryosphere, lidské dimenze, hydrosféra, pozemního surface, oceánů, sun earth interakce, a další kolekce více než 32 000 data. | Datové sady jsou v různých formátech. |
| [Zpoždění letů letecká společnost a další data doprava](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "USA Ministerstva dopravy vaší kanceláře sleduje Transportation statistiky (BTS) o včasných odletech domácí letů provozovaný ve vzduchu velký prostředníci (tečka). Souhrnné informace o počtu včas, opožděné, zrušené a odkloněných lety se zobrazí... v souhrnné tabulky zveřejňované na tomto webu." | Soubory jsou ve formátu CSV. |
| [Provoz úmrtí - nám Fatality analýzy generování sestav System (FARS)](http://www.nhtsa.gov/FARS) | "FARS je celostátní sčítání poskytuje NHTSA kongresu, a American veřejné roční data týkající se schopnost se zranit při závažné utrpělo v vozidla selhání provozu." | "Vytvořit svoje vlastní data fatality spustíte online s použitím systému FARS dotazu. Nebo si stáhněte všechna data FARS z 1975 zobrazíte ze serveru FTP." |
| [Toxické chemických dat – data EPA Toxicity uživatel (ToxCast™)](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "EPA pro většinu aktualizace, data veřejně dostupné toxicity vysokou propustností na tisíce chemikálií. Tato data je generován pomocí úsilí v oblasti výzkumu EPA ToxCast." | Datové sady jsou k dispozici v různých formátech, včetně tabulek, balíčky R a soubory databáze MySQL. |
| [Toxické chemických dat – NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "2014 Tox21 data před obrovskou výzvou – slouží k pochopení potenciál chemikálií a sloučeniny testován prostřednictvím toxikologie v rámci iniciativy 21st Century narušit biologické cest takovým způsobem, který může mít za následek toxické účinky odborníci přes." | Datové sady jsou k dispozici v ÚSMĚVY a SDF formátů. Poskytuje data "stanovení obsahu data aktivit a chemických struktury v kolekci Tox21 sloučeniny ~ 10 000 (Tox21 10 kB)." |
| [Biotechnologie a genomu data z NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Více datových sad pokrývá genů genomů a bílkovin. | Datové sady jsou v textu, XML, DOSAH a dalších formátech. VÝBUCHU aplikace je k dispozici. |

## <a name="other-statistical-and-scientific-data"></a>Další statistické a vědecké data

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Data taxislužby města New York](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Záznamy cesty taxíkem obsahovat pole zachytávání odběr a odkládací data/časy odběr a odkládací umístění, o jízdách vzdálenosti, rozepsané tarify, míra typy, typy plateb a počty ovladač ohlásil osobní." | Datové sady jsou v souborech CSV podle měsíce. |
| [Microsoft Research datové sady – "Datové vědy pro výzkum"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Více datových sad, interakce s počítačem, zvuk/video, načítání dat dolování nebo informace, Geoprostorové/umístění, zpracování přirozeného jazyka a pro robotiku a počítačové zpracování obrazu. | Datové sady jsou v různých formátech, ZIP ke stažení. |
| [Data veřejného genomu](http://www.completegenomics.com/public-data/) | "Různorodé datové sady plné lidské genomů jsou volně dostupných veřejně přístupný pro zvýšení jakékoli genomický studie..." Privátní komerční corporation je poskytovatel, dokončení Genomics. | Po extrakci, nastavení datových sad a jsou v textovém formátu UNIX. Nástroje pro analýzu jsou také k dispozici. |
| [Otevřete data Science dat v cloudu](https://www.opensciencedatacloud.org/) | "Otevřené cloudové datové vědy poskytuje vědy s prostředky pro ukládání, sdílení a analýzu terabajt a petabajtů vědecké datových sad."| Datové sady jsou v různých formátech. |
| [Globální klimatu dat – WorldcLIM](http://worldclim.org/) | "WorldClim je sada globální klimatu vrstvy (gridded klimatu data) prostorových řešení asi 1 km2. Tyto údaje lze použít k mapování a prostorových modelování." | Tyto soubory obsahují Geoprostorová data. Další informace najdete v tématu [formát dat](http://worldclim.org/formats1). |
| [Data o lidských společnosti - GDELT projekt](http://www.gdeltproject.org/data.html) | "GDELT projektu je největší, nejúplnější a nejvyšší řešení otevřít databázi lidské společnosti, kdy byly vytvořeny." | Soubory nezpracovaná data jsou ve formátu CSV. |
| [Reklamy, klikněte na možnost predikce dat pro machine learning z Criteo](http://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "Největším někdy veřejně vydaných ML datovou sadu." Další informace najdete v tématu [Criteo na 1 TB klikněte na možnost predikce Dataset](https://blogs.technet.microsoft.com/machinelearning/2015/04/01/now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [ClueWeb09 text dolování datové sady z Lemur projekt](http://www.lemurproject.org/clueweb09.php/) | "ClueWeb09 datové sady bylo vytvořeno pro podporu research na načítání informací a související jazyk lidské technologie. Skládá se z přibližně 1 miliardy webových stránek v 10 jazycích, které byly shromážděny v lednu a únor 2009." | Zobrazit [informací o datové sadě](http://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Data online služby

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Archiv Githubu](https://www.githubarchive.org/) | "Githubu archivu je do projektu zaznamenat veřejné Githubu časové osy [events], archivovat je a zpřístupněte ho snadno k další analýze." | Stáhněte JSON encloded archivy událostí ve formátu .gz (Gzip) z webového klienta. |
| [Data aktivity Githubu z The GHTorrent projektu](http://ghtorrent.org/) | "GHTorrent projektu [je] se snaží vytvořit škálovatelný, dotazovatelné, offline zrcadlení dat nabízené prostřednictvím rozhraní REST API Githubu. GHTorrent monitoruje na Githubu veřejná událost časové ose. Pro každou událost načte její obsah a jejich závislosti vyčerpávajícím způsobem." | MySQL database jsou ve formátu CSV. |
| [Výpis dat přetečení zásobníku](https://archive.org/details/stackexchange) | "Toto je anonymizované výpis paměti veškerý obsah za uživatele v síti Stack Exchange [včetně Stack Overflow]". | "Každou lokalitu [například Stack Overflow] je formátován jako samostatné archivu skládající se z XML soubory ZIP prostřednictvím použít kompresi bzip2 7-zip. Každý server archiv obsahuje příspěvky, uživatelé, hlasování, komentáře, PostHistory a PostLinks." |
