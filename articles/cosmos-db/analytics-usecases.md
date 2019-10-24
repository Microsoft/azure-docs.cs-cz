---
title: Případy použití předdefinovaných analýz s Azure Cosmos DB.
description: Naučte se používat integrované analýzy s Azure Cosmos DB v různých případech použití.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757079"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Případy použití předdefinovaných analýz pomocí Azure Cosmos DB

Následující případy použití je možné dosáhnout pomocí integrovaných analýz s Apache Spark v Azure Cosmos DB:

## <a name="htap-scenarios"></a>Scénáře HTAP

Integrované analýzy v Azure Cosmos DB lze použít k těmto akcím:

* Detekovat podvod během zpracování transakcí.
* Poskytněte doporučení pro nakupující při procházení úložiště elektronického obchodování.
* Obsluha výstrah na hrozící selhání kritického výrobního zařízení.
* Pomocí vkládání analýz v reálném čase přímo na provozní data vytvořte rychlý přehled, který je možné vydávat.

Azure Cosmos DB podporuje scénáře hybridního transakčního a analytického zpracování (HTAP) pomocí nativně integrované Apache Spark. Azure Cosmos DB odstraní provozní a analytické oddělení, které se nachází v tradičních systémech.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Globálně distribuované Data Lake bez vyžadování ETL

S nativně integrovanými Apache Spark Azure Cosmos DB poskytuje rychlý, jednoduchý a škálovatelný způsob, jak vytvořit globálně distribuovaný datový Lake, který poskytuje jednu bitovou kopii systému. Globálně distribuovaná data s více modely eliminují potřebu investovat do drahých kanálů ETL a škálují na vyžádání a revolutionizing způsob, jakým datové týmy analyzují datové sady.

## <a name="time-series-analytics-over-historic-data"></a>Analýza časových řad pomocí historických dat

V některých případech může být nutné odpovědět na otázky na základě dat v určitém časovém okamžiku přes události dokončené v minulosti. Například pro získání počtu stavů aktivity CRM v určité datum. Pokud jste sestavu spustili před týdnem, bude počet stavů na základě stavů každé aktivity v daném okamžiku. Po současném spuštění stejné sestavy získáte počet aktivit, jejichž stavy jsou v dnešní době, což se může od minulého týdne změnit, protože procházejí jejich životním cyklem z otevřených do uzavření. Proto je třeba v každé fázi životního cyklu případu vykázat na snímek.

V tradičních scénářích datového skladu koncept snímku není možný, protože datové sklady nejsou navrženy tak, aby se začlení a data obsahují pouze aktuální pohled na to, co se děje. Díky Azure Cosmos DB mají uživatelé možnost implementovat koncept času, který by dokázal dotazovat a spouštět analýzy dat zpětně a pokládat se na tom, jak se data v historii prohlédla v určitém časovém okamžiku. To znamená, že uživatelé si můžou snadno zobrazit aktuální i historická zobrazení dat a spustit na ní analýzy.

## <a name="globally-distributed-machine-learning-and-ai"></a>Globálně distribuované strojové učení a AI

Jelikož se podniky soupeří s rychlými rostoucími objemy dat a různými datovými typy a formáty, bude možnost získat hlubší a přesnější přehledy na řádu petabajtů škále po celém světě. S nativně integrovanými Apache Spark Azure Cosmos DB poskytuje globálně distribuovanou analytickou platformu, která nabízí rozsáhlou knihovnu algoritmů strojového učení. Interaktivní poznámkové bloky Jupyter můžete použít k sestavování a výukových modelům a možnostem správy clusteru. Tyto funkce umožňují zřídit vysoce vyladěné a automaticky elastické Clustery Spark na vyžádání.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Obsáhlý Learning u globálně distribuovaných dat s více modely

Obsáhlý Learning je ideální způsob, jak zajistit prediktivní Analytická řešení pro velké objemy dat, protože se i nadále zvětšují objemy dat a složitost. Díky obsáhlému učení mohou firmy využít sílu nestrukturovaných a částečně strukturovaných dat k dodávání případů použití, které využívají techniky jako AI, zpracování přirozeného jazyka a další.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Vytváření sestav (integrace s Power Apps, Power BI)

Power BI poskytuje interaktivní vizualizace s funkcemi samoobslužného business intelligence a umožňuje koncovým uživatelům vytvářet vlastní sestavy a řídicí panely. Pomocí integrovaného konektoru Spark se můžete připojit Power BI Desktop k Apache Spark clusterům v Azure Cosmos DB. Tento konektor vám umožní použít přímý dotaz na přesměrování zpracování na Apache Spark v Azure Cosmos DB, což je skvělé, když máte obrovské množství dat, která nechcete načíst do Power BI nebo když chcete provést analýzu téměř v reálném čase.

## <a name="iot-analytics-at-global-scale"></a>Analýza IoT v globálním měřítku

Data vygenerovaná ze zvýšeného síťového senzorů přináší nepředcházející přehled o dříve neprůhledných systémech a procesech. Klíčem je najít užitečné přehledy v tomto torrent informací bez ohledu na to, kde jsou zařízení IoT distribuována po celém světě. Azure Cosmos DB umožňuje společnostem IOT analyzovat vysokorychlostní senzory a data časových řad v reálném čase kdekoli po celém světě. Umožňuje využít skutečnou hodnotu propojeného světa, aby poskytovala vylepšené prostředí pro zákazníky, provozní efektivitu a nové možnosti příjmů.

## <a name="stream-processing-and-event-analytics"></a>Zpracování datových proudů a analýza událostí 

Firmy stále potřebují se ze souborů protokolů na data senzorů vypořádat s datovými proudy dat. Tato data přicházejí do konstantního datového proudu, často z více zdrojů současně. I když je vhodné ukládat tyto datové proudy na disk a analyzovat je zpětně, může být někdy rozumné nebo důležité zpracovat data a pracovat s nimi při jejich doručování. Například datové proudy dat souvisejících s finančními transakcemi lze zpracovávat v reálném čase k identifikaci a odmítání potenciálně podvodných transakcí.

## <a name="interactive-analytics"></a>Interaktivní analýzy

Kromě spouštění předem definovaných dotazů pro vytváření statických řídicích panelů pro prodej, produktivitu nebo ceny akcií můžete chtít data prozkoumat interaktivně. Interaktivní analýza vám umožní klást otázky, zobrazovat výsledky, měnit počáteční otázku na základě odpovědi nebo podrobného podrobnějšího zobrazení výsledků. Apache Spark v Azure Cosmos DB podporuje interaktivní dotazy díky rychlé reakci a přizpůsobení.

## <a name="data-exploration-using-jupyter-notebooks"></a>Zkoumání dat pomocí poznámkových bloků Jupyter

Pokud máte novou datovou sadu, před tím, než podrobně spustíte modely a testy, je nutné zkontrolovat data. Jinými slovy je potřeba provést analýzu průzkumnéch dat. Zkoumání dat může informovat několik rozhodnutí. Například můžete najít podrobnosti, například metody, které jsou vhodné pro použití na vašich datech, ať už data splňují určité předpoklady modelování, a to bez ohledu na to, jestli se data mají vyčistit, restrukturovat atd. Pomocí Azure Cosmos DB nativně integrovaných poznámkových bloků Jupyter a Apache Spark můžete provádět rychlou a efektivní analýzu dat na základě transakčních a analytických dat.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s těmito případy použití, přečtěte si následující články:

* [Integrované poznámkové bloky Jupyter v Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Jak povolit poznámkové bloky pro účty Azure Cosmos](enable-notebooks.md)
* [Vytvoření poznámkového bloku pro analýzu a vizualizaci dat](create-notebook-visualize-data.md)