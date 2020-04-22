---
title: Případy použití pro integrované analýzy s Azure Cosmos DB.
description: Zjistěte, jak používat integrované analýzy s Azure Cosmos DB v různých případech použití.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: adf1db2d737de7004b5ab71c8e0e44d61d1da8c9
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768517"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Případy použití pro integrované analýzy s Azure Cosmos DB

Následujících případů použití lze dosáhnout pomocí integrované analýzy s Apache Spark v Azure Cosmos DB:

## <a name="htap-scenarios"></a>Scénáře HTAP

Integrované analýzy v Azure Cosmos DB lze použít k:

* Odhalte podvody během zpracování transakcí.
* Poskytněte zákazníkům doporučení při procházení obchodu ECommerce.
* Upozorněte operátory na hrozící selhání kritického kusu výrobního zařízení.
* Vytvářejte rychlé a užitečné poznatky vložením analýz v reálném čase přímo do provozních dat.

Azure Cosmos DB podporuje scénáře hybridního transakčního/analytického zpracování (HTAP) pomocí nativně integrované apache spark. Azure Cosmos DB odebere provozní a analytické oddělení, které je dodáváno s tradičními systémy.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Globálně distribuované datové jezero bez nutnosti etl

S nativně integrované Apache Spark, Azure Cosmos DB poskytuje rychlý, jednoduchý a škálovatelný způsob, jak vytvořit globálně distribuované datové jezero, které poskytuje jednu bitovou kopii systému. Globálně distribuovaná data s více modely eliminují potřebu investovat do drahých kanálů ETL a škálování na vyžádání, což přináší revoluci ve způsobu, jakým datové týmy analyzují své datové sady.

## <a name="time-series-analytics-over-historic-data"></a>Analýza časových řad nad historickými daty

V některých případech může být nutné odpovědět na otázky založené na datech jako v určitém okamžiku v čase nad událostmi dokončenými v minulosti. Chcete-li například získat počet stavů aktivit aplikace CRM k určitému datu. Pokud jste sestavu spustili před týdnem, počet stavů by byl podle stavů jednotlivých aktivit v tomto okamžiku. Spuštění stejné zprávy dnes vám dá počet činností, jejichž stavy jsou, jak jsou dnes, které se mohou změnit od minulého týdne, protože procházejí jejich životní cyklus od otevřeného k zavření. Takže je třeba podat zprávu o snímku v každé fázi životního cyklu případu.

S Azure Cosmos DB mají uživatelé možnost implementovat koncept cestování v čase, možnost dotazovat a spouštět analýzy dat retrospektivně a požádat o to, jak data vypadala v určitém časovém okamžiku v historii. To znamená, že uživatelé mohou snadno zobrazit aktuální i historické zobrazení dat a spustit analýzu na něm.

## <a name="globally-distributed-machine-learning-and-ai"></a>Globálně distribuované strojové učení a umělá ai

Vzhledem k tomu, že se podniky potýkají s rychle rostoucím objemem dat a rozšiřující se škálou datových typů a formátů, schopnost získat hlubší a přesnější přehledy se stává téměř nemožnou v petabajtovém měřítku po celém světě. S nativně integrovaným Apache Spark poskytuje Azure Cosmos DB globálně distribuovanou analytickou platformu, která nabízí rozsáhlou knihovnu algoritmů strojového učení. Interaktivní poznámkové bloky Jupyter můžete použít k vytváření a trénování modelů a možností správy clusteru. Tyto funkce umožňují zřídit vysoce vyladěné a automaticky elastické clustery Spark na vyžádání.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Hloubkové učení o globálně distribuovaných datech s více modely

Hloubkové učení je ideálním způsobem, jak poskytovat řešení pro prediktivní analýzu velkých objemů dat, protože objem dat a složitost stále rostou. Díky hlubokému učení mohou firmy využít sílu nestrukturovaných a částečně strukturovaných dat k poskytování případů použití, které využívají techniky, jako je umělá intelidenční analýza, zpracování přirozeného jazyka a další.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Vytváření sestav (integrace s Power Apps, Power BI)

Power BI poskytuje interaktivní vizualizace se samoobslužnými funkcemi business intelligence, které koncovým uživatelům umožňují vytvářet sestavy a řídicí panely samy. Pomocí integrovaného konektoru Spark můžete připojit Power BI Desktop ke clusterům Apache Spark v Azure Cosmos DB. Tento konektor umožňuje použít přímý dotaz k přesměrování zpracování apache spark v Azure Cosmos DB, což je skvělé, když máte obrovské množství dat, které nechcete načíst do Power BI nebo když chcete provádět analýzu v reálném čase.

## <a name="iot-analytics-at-global-scale"></a>Analýza IoT v globálním měřítku

Data vygenerovaná z rostoucích síťových senzorů přinášejí bezprecedentní přehled o dříve neprůhledných systémech a procesech. Klíčem je najít užitečné postřehy v tomto přívalu informací bez ohledu na to, kde jsou zařízení IoT distribuována po celém světě. Azure Cosmos DB umožňuje společnostem IOT analyzovat vysokorychlostní senzor a data časových řad v reálném čase kdekoli na světě. Umožňuje vám využít skutečnou hodnotu propojeného světa k poskytování lepších zákaznických zkušeností, provozní efektivity a nových příležitostí k příjmům.

## <a name="stream-processing-and-event-analytics"></a>Zpracování datových proudů a analýza událostí 

Od souborů protokolu až po data ze senzorů se podniky stále více potýkají s "proudy" dat. Tato data přicházejí v ustáleném proudu, často z více zdrojů současně. I když je možné ukládat tyto datové proudy na disk a analyzovat je retrospektivně, může být někdy rozumné nebo důležité zpracovat a jednat s daty, jakmile dorazí. Například datové proudy dat souvisejících s finančními transakcemi mohou být zpracovány v reálném čase za účelem identifikace a odmítnutí potenciálně podvodných transakcí.

## <a name="interactive-analytics"></a>Interaktivní analýza

Kromě spuštění předdefinovaných dotazů k vytvoření statických řídicích panelů pro prodej, produktivitu nebo ceny akcií můžete data prozkoumat interaktivně. Interaktivní analýzy umožňují klást otázky, zobrazit výsledky, měnit počáteční otázku na základě odpovědi nebo procházet hlouběji do výsledků. Apache Spark v Azure Cosmos DB podporuje interaktivní dotazy tím, že reaguje a rychle se přizpůsobuje.

## <a name="data-exploration-using-jupyter-notebooks"></a>Zkoumání dat pomocí poznámkových bloků Jupyter

Pokud máte novou datovou sadu, než se ponoříte do spuštění modelů a testů, musíte zkontrolovat data. Jinými slovy je třeba provést analýzu průzkumných dat. Zkoumání dat může informovat několik rozhodnutí. Můžete například najít podrobnosti, jako jsou metody, které jsou vhodné pro použití v datech, zda data splňují určité předpoklady modelování, zda by měla být data vyčištěna, restrukturalizována atd. Pomocí nativně integrovaných poznámkových bloků Jupyter u Azure Cosmos DB a Apache Spark můžete provést rychlou a efektivní analýzu průzkumných dat na transakčních a analytických datech.

## <a name="next-steps"></a>Další kroky

Chcete-li začít s těmito případy použití na přejděte na následující články:

* [Integrované poznámkové bloky Jupyter v Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Jak povolit poznámkové bloky pro účty Azure Cosmos](enable-notebooks.md)
* [Vytvoření poznámkového bloku pro analýzu a vizualizaci dat](create-notebook-visualize-data.md)