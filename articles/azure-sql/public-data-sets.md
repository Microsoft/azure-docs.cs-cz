---
title: Veřejné datové sady pro Azure Analytics
description: Přečtěte si o veřejných datových sadách, které můžete použít k prototypování a testování služeb a řešení Azure Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 30f6bf7629152050bc1249a1e22aff0aa30642de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88209534"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Veřejné datové sady pro testování a vytváření prototypů
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Projděte si tento seznam veřejných datových sad pro data, která můžete použít k prototypování a testování služby úložiště a analytických služeb a řešení.

## <a name="us-government-and-agency-data"></a>Data pro státní správu a úřady USA

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Data pro státní správu USA](https://catalog.data.gov/dataset) | Více než 250 000 datových sad, které se týkají zemědělství, klimatu, spotřebitelů, ekosystémů, vzdělávání, energetiky, financí, zdravotnictví, místní samosprávy, výroby, mořského, oceánu, veřejného zabezpečení a vědy a výzkumu v USA | Soubory různých velikostí v různých formátech, mezi které patří HTML, XML, CSV, JSON, Excel a spousta dalších. Dostupné datové sady můžete filtrovat podle formátu souboru. |
| [Data o sčítání USA](https://www.census.gov/data.html) | Statistická data o naplnění USA | Datové sady jsou v různých formátech. |
| [Země – věda – data z NASA](https://earthdata.nasa.gov/) | Více než 32 000 kolekcí dat, které se týkají zemědělství, atmosféry, biosféry, klimatu, cryosphere, lidských rozměrů, hydrosphere, půdního povrchu, oceánů, interakcí Sun-Earth a dalších. | Datové sady jsou v různých formátech. |
| [Zpoždění leteckých letů a další přepravní data](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Středisko pro povýšení statistik přepravosti (tečka) v USA sleduje provoz domácích letů provozovaných v rámci velkých leteckých dopravců. Zobrazí se souhrnné informace o počtu neúspěšných, zpožděných, zrušených a předaných letů... v souhrnných tabulkách publikovaných na tomto webu. | Soubory jsou ve formátu CSV. |
| [Závažnosti provozu – systém generování sestav analýzy závažnosti USA (FARS)](https://www.nhtsa.gov/FARS) | "FARS je celostátní soupis, který poskytuje NHTSA, Kongresem a každoroční údaje o závažných škodách utrpěných při selhání provozu motorových vozidel." | Možnost vytvořit vlastní data o závažnosti se spouští online pomocí systému dotazů FARS. Můžete si také stáhnout všechna FARS data z 1975, která se nacházejí od serveru FTP. |
| [Data o toxických chemických datech – EPA toxicita (ToxCast &trade; )](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "NejEPAější a veřejně dostupná data o toxicitě s vysokou propustností na tisících chemických látek. Tato data se generují prostřednictvím výzkumného úsilí EPA ToxCast. | Datové sady jsou k dispozici v různých formátech, včetně tabulek, balíčků R a souborů databáze MySQL. |
| [Toxický chemický data – NIH Tox21 data Challenge 2014](https://tripod.nih.gov/tox21) | 2014 "Tox21 data Challenge je navržená tak, aby pomáhala pracovníkům, kteří porozuměli potenciálním chemickým látkám a sloučeninám, které jsou testovány prostřednictvím toxikologického podnětu v rámci 21. | Datové sady jsou dostupné v SMAJLÍKech a SDF formátech. Data poskytují "data o zkušebních činnostech a chemické struktury pro kolekci Tox21 ~ 10 000 sloučeniny (Tox21 10 000)." |
| [Biotechnologie a data genomu z NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Více datových sad, které pokrývají genů, genomy a bílkoviny. | Datové sady jsou v textu, formátu XML, vysokých a dalších formátech. K dispozici je aplikace pro vysoké dostupnosti. |

## <a name="other-statistical-and-scientific-data"></a>Další statistická a vědecká data

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Data taxislužby v New Yorku](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Záznamy o cestách taxislužby zahrnují pole zaznamenávající výběr a dropoff data a časy, umístění pro výběr a dropoff, vzdálenosti cest, vyhodnocené tarify, typy sazeb, typy plateb a počty spolupracujících řidičů." | Datové sady jsou v souborech CSV podle měsíců. |
| [Microsoft Research data Sets – "data vědy pro výzkum"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Několik datových sad, které pokrývají interakci lidského počítače, zvuk/video, dolování dat, načítání informací, geoprostorového a umístění, zpracování přirozeného jazyka a roboty/počítačové vidění. | Datové sady jsou v různých formátech, stáhnout pro stažení. |
| [Otevření dat cloudu pro vědu data](https://www.opensciencedatacloud.org/projects/) | "Open věda data Cloud poskytuje vědeckou komunitu s využitím prostředků pro ukládání, sdílení a analýzu terabajtů a řádu petabajtů vědeckých datových sad."| Datové sady jsou v různých formátech. |
| [Globální klimatická data – WorldClim](https://worldclim.org/) | "WorldClim je sada globálních vrstev klimatu (Gridded klimatických dat) s prostorovým rozlišením přibližně 1 km2. Tato data lze použít pro mapování a prostorové modelování. " | Tyto soubory obsahují geoprostorové údaje. Další informace najdete v tématu [Formát dat](https://worldclim.org/formats1). |
| [Data o lidské společnosti – projekt GDELT](https://www.gdeltproject.org/data.html) | "Projekt GDELT je největší, nejucelenější a nejsnadnějším řešením otevřené databáze lidské společnosti, která byla vytvořena dříve." | Soubory nezpracovaných dat jsou ve formátu CSV. |
| [Inzerce na data předpovědi pro Machine Learning z Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "Největší dříve veřejně vydanou datovou sadu ML." Další informace najdete v části [Criteo 1 TB klikněte na prediktivní datovou sadu](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [Sada dat dolování textu ClueWeb09 z projektu lemur](https://www.lemurproject.org/clueweb09.php/) | "Datová sada ClueWeb09 byla vytvořena pro podporu výzkumu při načítání informací a souvisejících technologiích lidského jazyka. Skládá se z přibližně 1 000 000 000 webových stránek v 10 jazycích shromážděných v lednu a února 2009. " | Viz [informace o datové sadě](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Data služby online

| Zdroj dat | Informace o datech | O souborech |
|---|---|---|
| [Archiv GitHubu](https://www.githubarchive.org/) | "Archiv GitHubu je projekt pro záznam vaší časové osy veřejného GitHubu [z událostí], jeho archivace a usnadnění pro další analýzu." | Stáhnout archivy událostí kódované JSON ve formátu. gz (gzip) z webového klienta. |
| [Data aktivity GitHubu z projektu GHTorrent](http://ghtorrent.org/) | "Projekt GHTorrent [je] snaha vytvořit škálovatelnou, Queryable, offline zrcadlo dat nabízených prostřednictvím REST API GitHubu. GHTorrent monitoruje časovou linii události GitHubu. Pro každou událost načte svůj obsah a jejich závislosti vyčerpány. " | Výpisy databáze MySQL jsou ve formátu CSV. |
| [Stack Overflow výpis dat](https://archive.org/details/stackexchange) | "Toto je vyanonymý výpis všech uživatelem přidaných obsahu v síti Exchange stacku [včetně Stack Overflow]." | "Každá lokalita [například Stack Overflow] je formátována jako samostatný archiv skládající se ze souborů XML zip prostřednictvím 7-zip pomocí komprese bzip2. Každý archiv lokality zahrnuje příspěvky, uživatele, hlasy, komentáře, PostHistory a PostLinks. |
