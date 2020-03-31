---
title: Veřejné datové sady pro Azure analýzy
description: Přečtěte si o veřejných datových sadách, které můžete použít k prototypování a testování analytických služeb a řešení Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 4b1c1a963b065411f1a0ab84141bdf1835930ebb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973539"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Veřejné datové sady pro testování a prototypování

Projděte si tento seznam veřejných datových sad, kde najdete data, která můžete použít k prototypovým a testovacím službám a řešením úložiště a analýz.

## <a name="us-government-and-agency-data"></a>Údaje vlády a agentury USA

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Údaje vlády USA](https://catalog.data.gov/dataset) | Více než 250.000 datových souborů zahrnujících zemědělství, klima, spotřebitele, ekosystémy, vzdělávání, energetika, finance, zdraví, místní samosprávy, výroba, námořní, oceán, veřejná bezpečnost a věda a výzkum v USA | Soubory různých velikostí v různých formátech, včetně HTML, XML, CSV, JSON, Excel a mnoho dalších. Dostupné sady dat můžete filtrovat podle formátu souboru. |
| [Údaje ze sčítání lidu USA](https://www.census.gov/data.html) | Statistické údaje o populaci usa | Datové sady jsou v různých formátech. |
| [Data vědy o Zemi z NASA](https://earthdata.nasa.gov/) | Více než 32 000 sběrů dat zahrnujících zemědělství, atmosféru, biosféru, klima, kryosféru, lidské rozměry, hydrosféru, povrch země, oceány, interakce mezi Sluncem a Zemí a další. | Datové sady jsou v různých formátech. |
| [Zpoždění letů letecké společnosti a další přepravní údaje](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Úřad pro dopravní statistiku (BTS) ministerstva dopravy USA sleduje včasovou výkonnost vnitrostátních letů provozovaných velkými leteckými dopravci. Zobrazí se souhrnné informace o počtu včasných, zpožděných, zrušených a odkloněných letů ... v souhrnných tabulkách zveřejněných na této webové stránce." | Soubory jsou ve formátu CSV. |
| [Dopravní nehody - US Fatality Analysis Reporting System (FARS)](https://www.nhtsa.gov/FARS) | "FARS je celostátní sčítání lidu poskytující NHTSA, Kongres, a americká veřejnost roční údaje o smrtelných zranění chutrána při dopravních nehodách motorových vozidel." | "Vytvořte si vlastní osudovost dat spustit on-line pomocí FARS Query System. Nebo si stáhněte všechna data FARS z roku 1975, která jsou k dispozici ze stránek FTP." |
| [Údaje o toxických chemických látkách - DATA EPA Toxicity ForeCaster (ToxCast™)](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "EPA je nejvíce aktualizované, veřejně dostupné high-propustnost údaje o toxicitě na tisíce chemických látek. Tato data jsou generována prostřednictvím výzkumného úsilí EPA ToxCast." | Datové sady jsou k dispozici v různých formátech, včetně tabulek, R balíčky a MySQL databázové soubory. |
| [Údaje o toxických chemických látkách - NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "Výzva v oblasti dat Tox21 z roku 2014 je navržena tak, aby vědcům pomohla pochopit potenciál chemických látek a sloučenin testovaných prostřednictvím toxikologie v iniciativě 21st Century, která má narušit biologické cesty způsoby, které mohou mít za následek toxické účinky." | Datové sady jsou k dispozici ve formátech SMILES a SDF. Údaje poskytují "údaje o aktivitě a chemické struktury na tox21 sběr ~ 10,000 sloučenin (Tox21 10K)." |
| [Biotechnologie a genom u dat z NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Několik datových sad zahrnujících geny, genomy a proteiny. | Datové sady jsou v textech, XML, BLAST a dalších formátech. K dispozici je aplikace BLAST. |

## <a name="other-statistical-and-scientific-data"></a>Ostatní statistické a vědecké údaje

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [New York City taxi data](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Záznamy o jízdě taxíkem zahrnují pole zachycující data/časy vyzvednutí a předání, místa vyzvednutí a odstávky, vzdálenosti jízdy, rozepsanýtarify, typy sazeb, typy plateb a počty cestujících nahlášené řidičem." | Datové sady jsou v souborech CSV podle měsíce. |
| [Datové sady Microsoft Research – "Datové vědy pro výzkum"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Více datových sad zahrnujících interakci mezi člověkem a počítačem, audio/video, dolování dat/vyhledávání informací, geoprostorové/lokalizační, zpracování přirozeného jazyka a robotiku/počítačové vidění. | Datové sady jsou v různých formátech, zip ke stažení. |
| [Údaje o veřejném genomu](https://www.completegenomics.com/public-data/) | "Různorodá datová sada celých lidských genomů je volně k dispozici pro veřejné použití, aby se zlepšila jakákoli genomická studie..." Poskytovatel, Complete Genomics, je soukromá nezisková společnost. | Datové sady jsou po extrakci v textovém formátu UNIX. K dispozici jsou také analytické nástroje. |
| [Data cloudu open science data](https://www.opensciencedatacloud.org/projects/) | "Open Science Data Cloud poskytuje vědecké komunitě zdroje pro ukládání, sdílení a analýzu terabajtů a petabajtových vědeckých datových sad."| Datové sady jsou v různých formátech. |
| [Globální údaje o klimatu - WorldClim](https://worldclim.org/) | "WorldClim je soubor globálních klimatických vrstev (mřížkované klimatické údaje) s prostorovým rozlišením asi 1 km2. Tato data lze použít pro mapování a prostorové modelování." | Tyto soubory obsahují geoprostorová data. Další informace najdete [v tématu Datový formát](https://worldclim.org/formats1). |
| [Údaje o lidské společnosti - Projekt GDELT](https://www.gdeltproject.org/data.html) | "Projekt GDELT je největší, nejkomplexnější a nejkomplexnější otevřená databáze lidské společnosti, která kdy byla vytvořena." | Nezpracovaná data jsou ve formátu CSV. |
| [Reklama klikněte predikce dat pro strojové učení od Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "Největší veřejně vydaná datová sada ML." Další informace naleznete v [criteo je 1 TB click prediction dataset](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [ClueWeb09 text dolování dat z Lemur projektu](https://www.lemurproject.org/clueweb09.php/) | "Datová sada ClueWeb09 byla vytvořena na podporu výzkumu v oblasti získávání informací a souvisejících technologií lidského jazyka. Skládá se z přibližně 1 miliardy webových stránek v 10 jazycích, které byly shromážděny v lednu a únoru 2009." | Viz [Informace o datové sadě](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Údaje o online službách

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Archiv GitHubu](https://www.githubarchive.org/) | "GitHub Archive je projekt pro záznam veřejné časové osy GitHub [událostí], archivaci a jeho snadné zpřístupnění pro další analýzu." | Stáhněte si archivy událostí kódované jsonem ve formátu .gz (Gzip) z webového klienta. |
| [Data aktivity GitHub z projektu GHTorrent](http://ghtorrent.org/) | "Projekt GHTorrent [je] snahou vytvořit škálovatelné, dotazovatelné, offline zrcadlo dat nabízených prostřednictvím rozhraní GitHub REST API. GHTorrent monitoruje časovou osu veřejné události GitHub. Pro každou událost, načte jeho obsah a jejich závislosti, vyčerpávajícím způsobem." | Výpisy databáze MySQL jsou ve formátu CSV. |
| [Výpis dat přetečení zásobníku](https://archive.org/details/stackexchange) | "Jedná se o anonymizovaný výpis veškerého obsahu v síti Stack Exchange, který přispěl uživatelem [včetně přetečení zásobníku]." | "Každý web [například Přetečení zásobníku] je formátován jako samostatný archiv skládající se ze souborů XML zip přes 7-zip pomocí komprese bzip2. Každý archiv webu obsahuje příspěvky, uživatelé, hlasy, komentáře, posthistory a postlinks." |
