---
title: Veřejné datové sady pro Azure analytics | Microsoft Docs
description: Další informace o veřejné datové sady, můžete použít k prototypu a testování Azure analytické služby a řešení.
services: sql-database
author: douglaslMS
manager: craigg
ms.custom: reference
ms.service: sql-database
ms.topic: article
ms.date: 04/01/2018
ms.author: douglasl
ms.openlocfilehash: d6fd83f2a6648d3ba6c3ef55b832820d62ed5690
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Veřejné datové sady pro testování a při vytváření prototypu

Vyhledejte tento seznam veřejné datové sady pro data, která můžete použít ke službám úložiště a analýzy prototypu a testování a řešení.

## <a name="us-government-and-agency-data"></a>USA Agentura a Government dat

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Data vláda USA](https://www.census.gov/data.html) | Více než 190,000 datových souborů zemědělství, klimatem, příjemce, ekosystémů, education, energie, finance, stavu, místní government, výrobní námořní, oceánu, veřejné zabezpečení a vědecké účely a research v USA | Soubory různých velikostí v různých formátech, včetně HTML, XML, CSV, JSON, Excel a mnohé další. K dispozici datové sady můžete filtrovat podle formát souboru. |
| [USA – úplné zjišťování dat](https://www.census.gov/data.html) | Statistické údaje o populaci USA | Datové sady jsou v různých formátech. |
| [Vědecké účely Earth data z NASA](https://earthdata.nasa.gov/) | Kolekce víc než 32 000 datových zahrnující zemědělství, prostředí, biosféra, klimatem, cryosphere, dimenze lidské, hydrosféry, krajině prostor, oceánu, interakce sun země a další. | Datové sady jsou v různých formátech. |
| [Zpoždění letů letecká společnost a dalších dat Transport](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "USA Ministerstvo dopravy je kanceláře Transport statistiky (BTS) sleduje výkon na čas domácí letů provozovat ve velkých letecké prostředníci (tečka). Souhrnné informace o počtu na čas, zpožděné, zrušené a odkloněných letů se zobrazí... v souhrnné tabulky odeslány na tomto webu." | Soubory jsou ve formátu CSV. |
| [Provoz úhynu - nám Fatality Analysis Reporting systému (FARS)](http://www.nhtsa.gov/FARS) | "FARS je celostátní úplné zjišťování poskytuje NHTSA kongresu, a American veřejná roční data týkající se závažné zranění vyskytla v automobil provoz dojde k chybě." | "Vytvořit svoje vlastní data fatality spustit online pomocí systému FARS dotazu. Nebo si stáhnout všechna data FARS 1975 k dispozici ze serveru FTP." |
| [Toxické chemické data – data EPA Toxicity uživatel (ToxCast™)](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "Většinu EPA na aktualizace, data veřejně dostupné toxicity vysokou propustností na tisíce chemikálií. Tato data se generuje prostřednictvím EPA ToxCast research úsilí." | Datové sady jsou k dispozici v různých formátech, včetně tabulek, balíčků R a soubory databáze MySQL. |
| [Toxické chemické data – NIH Tox21 Data výzvy 2014](https://tripod.nih.gov/tox21/challenge/) | "Výzvy 2014 Tox21 data je určena pro pomoci porozumět potenciální chemikálií a sloučeniny testuje prostřednictvím toxikologie v rámci iniciativy 21 narušit biologické způsobů způsoby, které může mít za následek toxické účinky vědců." | Datové sady jsou k dispozici v ÚSMĚVY a SDF formáty. Poskytuje data "stanovení obsahu data aktivit a chemické struktury na kolekci Tox21 sloučenin ~ 10 000 (Tox21 10 TIS)." |
| [Biotechnologie a genomu data z NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Zahrnující genů, genomů a bílkoviny víc datových sad. | Datové sady jsou v textu, XML, VYSOKÝCH a dalších formátů. VYSOKÝCH aplikace je k dispozici. |

## <a name="other-statistical-and-scientific-data"></a>Další data statistické a vědecké

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [New Yorku taxíkem dat](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Taxi záznamů obsahovat pole zaznamenávání vyzvednutí a odkládací datum a čas, vyzvednutí a odkládací umístění, vzdálenosti služební cestě, rozepsané tarify, míra typy, typy plateb, a počty ovladač ohlásil osobní." | Datové sady jsou v souborech CSV po měsících. |
| [Datové sady Microsoft Research – "Datové vědy pro výzkum"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Víc datových sad, zahrnujících interakce lidské počítače, zvuku a videa, dolování nebo informace o načtení dat, geoprostorové nebo umístění, zpracování přirozeného jazyka a vize robotics nebo počítače. | Datové sady jsou v různých formátech, metoda ZIP ke stažení. |
| [Veřejné genomu dat](http://www.completegenomics.com/public-data/) | "Data různého typu plné lidské genomů jsou volně dostupných pro veřejné použití k vylepšení žádné Genomické studie..." Zprostředkovatel, dokončení genomiku, je privátní usilujícím o zisk corporation. | Datové sady, po extrakci, jsou v textovém formátu UNIX. K dispozici jsou také nástrojů pro analýzu. |
| [Otevřete datové vědy dat v cloudu](https://www.opensciencedatacloud.org/) | "Open Data Cloud vědecké účely poskytuje vědě s prostředky pro ukládání, sdílení a analýzu terabajt a petabajty škálování scientific datové sady."| Datové sady jsou v různých formátech. |
| [Globální klimatem data – WorldcLIM](http://worldclim.org/) | "WorldClim je sada globální klimatem vrstvy (gridded klimatem data) s prostorových řešení asi 1 km2. Tato data lze použít pro mapování a prostorových modelování." | Tyto soubory obsahují geoprostorové data. Další informace najdete v tématu [formát dat](http://worldclim.org/formats1). |
| [Data o lidské společnosti – projektu GDELT](http://www.gdeltproject.org/data.html) | "GDELT projektu je největší, nejúplnější a nejvyšší rozlišení otevřít databázi lidského společnosti někdy vytvářeli." | Soubory nezpracovaná data jsou ve formátu CSV. |
| [Inzerování, klikněte na tlačítko předpovědi dat pro machine learning z Criteo](http://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "Největší někdy veřejně vydaných ML datovou sadu." Další informace najdete v tématu [Criteo na 1 TB, klikněte na tlačítko Dataset předpovědi](https://blogs.technet.microsoft.com/machinelearning/2015/04/01/now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [ClueWeb09 text dolování datových sad z Lemur projektu](http://www.lemurproject.org/clueweb09.php/) | "Datovou sadu ClueWeb09 byl vytvořen pro podporu research na načítání informací a související lidského jazyk technologie. Skládá se asi 1 miliardy webových stránek v 10 jazycích, které byly shromážděny v lednu a únor 2009." | V tématu [informací o datové sadě](http://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Data online služby

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Archiv Githubu](https://www.githubarchive.org/) | "Githubu archivu je projektu zaznamenávat veřejné Githubu časovou osu [events], archivovat je a zpřístupněte ho snadno k další analýze." | Stáhněte si JSON encloded archivy událostí ve formátu .gz (Gzip) z webového klienta. |
| [Data aktivit Githubu z GHTorrent projektu](http://ghtorrent.org/) | "Projektu GHTorrent [je] snaze vytvořit zrcadlení škálovatelná, dotazovatelná, offline dat nabízeným přes rozhraní REST API Githubu. GHTorrent monitoruje časové ose veřejnou událost Githubu. Pro každou jednotlivou událost načte její obsah a jejich závislosti podrobně." | Výpisy databáze MySQL jsou ve formátu CSV. |
| [Výpis dat přetečení zásobníku](https://archive.org/details/stackexchange) | "Toto je anonymizovaná výpis veškerý obsah podílí uživatele v síti Exchange zásobníku [včetně Stack Overflow]". | "Každá lokalita [například Stack Overflow] je formátován jako samostatné archivu skládající se z soubory XML, ZIP prostřednictvím 7-zip pomocí bzip2 komprese. Každé lokality archivu zahrnuje příspěvky, uživatelé, hlasování, komentáře, PostHistory a PostLinks." |
