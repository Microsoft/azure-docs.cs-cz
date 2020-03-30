---
title: Vzory řešení služby Azure Stream Analytics
description: Seznamte se s běžnými vzory řešení pro Azure Stream Analytics, jako je řídicí panel, zasílání zpráv o událostech, úložiště dat, obohacení referenčních dat a monitorování.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535778"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Vzory řešení služby Azure Stream Analytics

Stejně jako mnoho jiných služeb v Azure, Stream Analytics se nejlépe používá s jinými službami k vytvoření většího komplexního řešení. Tento článek popisuje jednoduchá řešení Azure Stream Analytics a různé vzory architektury. Můžete stavět na těchto vzorcích a vyvíjet složitější řešení. Vzory popsané v tomto článku lze použít v široké škále scénářů. Příklady vzorců specifických pro scénář jsou k dispozici na [architekturách řešení Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Vytvoření úlohy Stream Analytics pro napájení prostředí řídicího panelu v reálném čase

S Azure Stream Analytics můžete rychle postavit řídicí panely a výstrahy v reálném čase. Jednoduché řešení ingestuje události z Centra událostí nebo IoT Hubu a [napájí řídicí panel Power BI se sadou streamovaných dat](/power-bi/service-real-time-streaming). Další informace najdete v podrobném kurzu [Analýza dat telefonních hovorů pomocí Stream Analytics a vizualizace výsledků na řídicím panelu Power BI](stream-analytics-manage-job.md).

![Řídicí panel ASA Power BI](media/stream-analytics-solution-patterns/pbidashboard.png)

Toto řešení můžete vytvořit během několika minut z portálu Azure. Neexistuje žádné rozsáhlé kódování zapojeny a jazyk SQL se používá k vyjádření obchodní logiky.

Tento vzor řešení nabízí nejnižší latenci od zdroje událostí k řídicímu panelu Power BI v prohlížeči. Azure Stream Analytics je jediná služba Azure s touto integrovanou funkcí.

## <a name="use-sql-for-dashboard"></a>Použití SQL pro řídicí panel

Řídicí panel Power BI nabízí nízkou latenci, ale nejde ho použít k vytváření plnohodnotných sestav Power BI. Běžným vzorem pro vytváření sestav je nejprve výstup dat do databáze SQL. Pak použijte SQL konektor Power BI k dotazování SQL na nejnovější data.

![Řídicí panel ASA SQL](media/stream-analytics-solution-patterns/sqldashboard.png)

Použití databáze SQL poskytuje větší flexibilitu, ale na úkor mírně vyšší latence. Toto řešení je optimální pro úlohy s požadavky na latenci větší než jednu sekundu. Pomocí této metody můžete maximalizovat možnosti Power BI, abyste dále rozřezali a nakrájeli data pro sestavy a mnohem více možností vizualizace. Můžete také získat flexibilitu při používání jiných řešení řídicího panelu, jako je například Tableau.

SQL není úložiště dat s vysokou propustností. Maximální propustnost databáze SQL z Azure Stream Analytics je aktuálně kolem 24 MB/s. Pokud zdroje událostí ve vašem řešení vytvářejí data vyšší rychlostí, musíte použít logiku zpracování v Stream Analytics, abyste snížili výstupní rychlost na SQL. Lze použít techniky, jako je filtrování, agregáty s okny, porovnávání vzorků s časovými spojeními a analytické funkce. Výstupní rychlost sql lze dále optimalizovat pomocí technik popsaných ve [výstupu Azure Stream Analytics do Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Začleňte přehledy v reálném čase do vaší aplikace pomocí zasílání zpráv o událostech

Druhým nejoblíbenějším využitím Stream Analytics je generování upozornění v reálném čase. V tomto modelu řešení obchodní logiku v Stream Analytics lze použít ke zjištění [časové a prostorové vzory](stream-analytics-geospatial-functions.md) nebo [anomálie](stream-analytics-machine-learning-anomaly-detection.md), pak vytvářet signály výstrah. Na rozdíl od řešení řídicího panelu, kde Stream Analytics používá Power BI jako upřednostňovaný koncový bod, lze použít několik přechodů zprostředkujících dat. Tyto jímky patří centra událostí, Service Bus a Azure funkce. Vy, jako tvůrce aplikace, je třeba rozhodnout, která jímka dat funguje nejlépe pro váš scénář.

Logika příjemce událostí musí být implementována tak, aby generovala výstrahy ve vašem stávajícím pracovním postupu podniku. Vzhledem k tomu, že můžete implementovat vlastní logiku v Azure Functions, Azure Functions je nejrychlejší způsob, jak můžete provést tuto integraci. Kurz pro použití Funkce Azure jako výstupu pro úlohu Stream Analytics najdete v [spouštění funkcí Azure z úloh Azure Stream Analytics](stream-analytics-with-azure-functions.md). Funkce Azure také podporuje různé typy oznámení, včetně textu a e-mailu. Aplikace logiky může být také použita pro takovou integraci, s Event Hubs mezi Stream Analytics a logic App.

![Aplikace pro zasílání zpráv o událostech ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Event Hubs naopak nabízí nejflexibilnější integrační bod. Mnoho dalších služeb, jako je Azure Data Explorer a Time Series Insights můžete využívat události z centra událostí. Služby lze připojit přímo k jímce Centra událostí z Azure Stream Analytics k dokončení řešení. Event Hubs je také nejvyšší zprostředkovatel eposu propustnost, který je k dispozici v Azure pro takové scénáře integrace.

## <a name="dynamic-applications-and-websites"></a>Dynamické aplikace a webové stránky

Pomocí Azure Stream Analytics a služby Azure SignalR můžete vytvářet vlastní vizualizace v reálném čase, jako je řídicí panel nebo vizualizace mapy. Pomocí SignalR lze webové klienty aktualizovat a zobrazovat dynamický obsah v reálném čase.

![Dynamická aplikace ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Začleňte přehledy v reálném čase do vaší aplikace prostřednictvím úložišť dat

Většina webových služeb a webových aplikací dnes používá vzor žádosti a odpovědi, který slouží prezentační vrstvě. Vzor požadavku a odpovědi je jednoduché sestavení a lze snadno škálovat s nízkou dobu odezvy pomocí bezstavové front-endu a škálovatelné úložiště, jako je Cosmos DB.

Vysoký objem dat často vytváří problémová místa výkonu v systému založeném na CRUD. [Vzor řešení zdroje událostí](/azure/architecture/patterns/event-sourcing) se používá k řešení kritických bodů výkonu. Časové vzory a přehledy jsou také obtížné a neefektivní extrahovat z tradičníúložiště dat. Moderní velkoobjemové aplikace založené na datech často přijímají architekturu založenou na toku dat. Azure Stream Analytics jako výpočetní modul pro data v pohybu je základní mačká v této architektuře.

![Aplikace pro získávání událostí ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

V tomto vzoru řešení jsou události zpracovávány a agregovány do úložišť dat pomocí Azure Stream Analytics. Aplikační vrstva spolupracuje s úložišti dat pomocí tradičního vzoru požadavku a odpovědi. Vzhledem k schopnosti Stream Analytics zpracovat velký počet událostí v reálném čase je aplikace vysoce škálovatelná bez nutnosti hromadně vrstvy úložiště dat. Vrstva úložiště dat je v podstatě materializované zobrazení v systému. [Výstup Azure Stream Analytics do Azure Cosmos DB](stream-analytics-documentdb-output.md) popisuje, jak se Cosmos DB používá jako výstup Stream Analytics.

V reálných aplikacích, kde je logika zpracování složitá a je potřeba upgradovat určité části logiky nezávisle, může být více úloh Stream Analytics složeno společně s event huby jako zprostředkovatelský zprostředkovatel událostí.

![Komplexní aplikace pro získávání událostí ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Tento vzor zlepšuje odolnost proti chybám a ovladatelnost systému. I když však Stream Analytics zaručuje přesně jednou zpracování, je malá šance, že duplicitní události mohou přistát ve zprostředkujících event hubech. Pro úlohu navazující hospo- stream analytics je důležité navodit události pomocí logických kláves v okně zpětného vyhledávání. Další informace o doručení události naleznete v tématu [Event Delivery Guarantees](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) reference.

## <a name="use-reference-data-for-application-customization"></a>Použití referenčních dat pro přizpůsobení aplikace

Funkce referenčních dat Azure Stream Analytics je navržena speciálně pro přizpůsobení koncových uživatelů, jako je prahová hodnota pro upozorňování, pravidla zpracování a [geografické zóny](geospatial-scenarios.md). Aplikační vrstva může přijímat změny parametrů a ukládat je do databáze SQL. Úloha Stream Analytics pravidelně dotazuje na změny z databáze a zpřístupňuje parametry vlastního nastavení prostřednictvím spojení referenčních dat. Další informace o použití referenčních dat pro přizpůsobení aplikace naleznete v tématu [SQL reference data](sql-reference-data.md) and reference data [join](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Tento vzor lze také použít k implementaci modulu pravidel, kde jsou definovány prahové hodnoty pravidel z referenčních dat. Další informace o pravidlech najdete [v tématu Zpracování konfigurovatelných pravidel založených na prahových hodnotách v Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![Aplikace asa referenční data](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Přidejte strojové učení do svých přehledů v reálném čase

Integrovaný [model detekce anomálií](stream-analytics-machine-learning-anomaly-detection.md) Azure Stream Analytics představuje pohodlný způsob, jak představit strojové učení do vaší aplikace v reálném čase. Širší škála potřeb strojového učení najdete v tématu [Azure Stream Analytics integruje se službou azure machine learning u vyhodnocování](stream-analytics-machine-learning-integration-tutorial.md).

Pro pokročilé uživatele, kteří chtějí začlenit online školení a vyhodnocování do stejného kanálu Stream Analytics, podívejte se na tento příklad, jak to udělat s [lineární regrese](stream-analytics-high-frequency-trading.md).

![Aplikace ASA Machine Learning](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Skladování dat téměř v reálném čase

Dalším běžným vzorem je ukládání dat v reálném čase, nazývané také datový sklad datových proudů. Kromě událostí, které přicházejí do event hubů a IoT Hubzvaší aplikace z vaší aplikace, lze [Azure Stream Analytics spuštěné na IoT Edge](stream-analytics-edge.md) použít ke splnění potřeb pro čištění dat, redukci dat a ukládání dat a předávání dál. Stream Analytics spuštěná na IoT Edge dokáže řádně zpracovat omezení šířky pásma a problémy s připojením v systému. Výstupní adaptér SQL lze použít k výstupu do datového skladu SQL; maximální propustnost je však omezena na 10 MB/s.

![Skladování dat ASA](media/stream-analytics-solution-patterns/datawarehousing.png)

Jedním ze způsobů, jak zlepšit propustnost s nějakou latence kompromis je archivovat události do úložiště objektů Blob Azure a pak [je importovat do sql datového skladu s Polybase](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Je nutné ručně sešít výstup z Stream Analytics do úložiště objektů blob a vstup z úložiště objektů blob do datového skladu SQL [archivací dat podle časového razítka](stream-analytics-custom-path-patterns-blob-storage-output.md) a pravidelným importem.

V tomto vzoru využití Azure Stream Analytics se používá jako téměř v reálném čase ETL engine. Nově příchozí události jsou průběžně transformovány a ukládány pro spotřebu navazujících analytických služeb.

![ASA s vysokou propustností datového skladu](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Archivace dat v reálném čase pro analýzu

Většina datových věd a analytických aktivit stále provádějte offline. Data můžete archivovat pomocí Azure Stream Analytics prostřednictvím výstupu Azure Data Lake Store Gen2 a formátů výstupů parket. Tato funkce odstraňuje tření pro přenos dat přímo do Azure Data Lake Analytics, Azure Databricks a Azure HDInsight. Azure Stream Analytics se v tomto řešení používá jako modul ETL téměř v reálném čase. Archivovaná data v Datovém jezeře můžete prozkoumat pomocí různých výpočetních modulů.

![Analýza ASA offline](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Použití referenčních dat pro obohacení

Obohacení dat je často požadavkem pro motory ETL. Azure Stream Analytics podporuje obohacení dat [referenčními daty](stream-analytics-use-reference-data.md) z databáze SQL i z úložiště objektů blob Azure. Obohacení dat lze provést pro přistoupení dat v Azure Data Lake a SQL Data Warehouse.

![Analýza ASA offline s obohacením dat](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Zprovoznění přehledů z archivovaných dat

Pokud zkombinujete vzor analýzy offline s aplikací téměř v reálném čase, můžete vytvořit zpětnou vazbu. Zpětná vazba umožňuje aplikaci automaticky upravit pro změnu vzorů v datech. Tato zpětná vazba může být stejně jednoduchá jako změna prahové hodnoty pro výstrahy nebo stejně složitá jako rekvalifikace modelů strojového učení. Stejnou architekturu řešení lze použít pro úlohy ASA spuštěné v cloudu i na IoT Edge.

![Operační systém asa insights](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Jak sledovat asa úlohy

Úlohu Azure Stream Analytics lze spustit 24 hodin denně, 7 dní v brzy a zpracovat příchozí události nepřetržitě v reálném čase. Jeho záruka uptime je zásadní pro zdraví celkové aplikace. Zatímco Stream Analytics je jedinou analytickou službou pro streamování v odvětví, která nabízí [99,9% záruku dostupnosti](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), stále vám může vzniknout určitá úroveň prostojů. V průběhu let stream analytics zavedla metriky, protokoly a stavy úloh, aby odrážely stav úloh. Všechny z nich jsou zobrazeny prostřednictvím služby Azure Monitor a lze dále exportovat do OMS. Další informace naleznete [v tématu Understand Stream Analytics job monitoring and how to monitoring queries](stream-analytics-monitoring.md).

![Monitorování ASA](media/stream-analytics-solution-patterns/monitoring.png)

Existují dvě klíčové věci, které je třeba sledovat:

- [Stav úlohy se nezdařil.](job-states.md)

    V první řadě se musíte ujistit, že je úloha spuštěna. Bez úlohy ve spuštěném stavu nejsou generovány žádné nové metriky nebo protokoly. Úlohy se mohou změnit na neúspěšný stav z různých důvodů, včetně vysoké úrovně využití SU (tj. vyčerpání prostředků).

- [Metriky zpoždění vodoznaku](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Tato metrika odráží, jak daleko za kanálem zpracování je v době nástěnné hodiny (sekundy). Některé zpoždění je připisována vlastní logiky zpracování. V důsledku toho je sledování rostoucího trendu mnohem důležitější než sledování absolutní hodnoty. Zpoždění ustáleného stavu by měla být řešena návrhu aplikace, nikoli monitorování nebo výstrahy.

Po selhání protokoly aktivit a [protokoly diagnostiky](stream-analytics-job-diagnostic-logs.md) jsou nejlepší místa, kde začít hledat chyby.

## <a name="build-resilient-and-mission-critical-applications"></a>Vytvářejte odolné a kritické aplikace

Bez ohledu na záruku SLA služby Azure Stream Analytics a na to, jak pečlivě spouštěte aplikaci od konce, k výpadkům dochází. Pokud je vaše aplikace kritická, musíte být připraveni na výpadky, abyste se řádně zotavili.

Pro upozorňování aplikací je nejdůležitější zjistit další výstrahu. Můžete se rozhodnout restartovat úlohu od aktuálního času při obnovení a ignorovat minulé výstrahy. Sémantiku čas zahájení úlohy jsou podle prvního výstupního času, nikoli prvního vstupního času. Vstup je převíjena zpět vhodná doba k zajištění prvního výstupu v zadaném čase je kompletní a správné. V důsledku toho nezískáte částečné agregace a neočekávaně aktivujete výstrahy.

Můžete se také rozhodnout spustit výstup z určitého množství času v minulosti. Obě centra událostí a zásady uchovávání informací ioT Hub uchovávat přiměřené množství dat, které umožňuje zpracování z minulosti. Kompromis je, jak rychle můžete dohnat aktuální čas a začít generovat včasné nové výstrahy. Data v průběhu času rychle ztrácejí svou hodnotu, takže je důležité rychle dohnat aktuální čas. Existují dva způsoby, jak rychle dohnat:

- Zřizte více zdrojů (SU) při dohánění.
- Restartujte od aktuálního času.

Restartování z aktuálního času je jednoduché, s kompromisem opuštění mezery během zpracování. Restartování tohoto způsobu může být v pořádku pro upozorňování scénářů, ale může být problematické pro scénáře řídicího panelu a je non-startér pro archivaci a data skladování scénáře.

Zřizování dalších prostředků může proces urychlit, ale účinek nárůstu rychlosti zpracování je složitý.

- Otestujte, že vaše úloha je škálovatelná na větší počet su. Ne všechny dotazy jsou škálovatelné. Je třeba se ujistit, že dotaz je [paralelizován](stream-analytics-parallelization.md).

- Ujistěte se, že je dostatek oddílů v centru událostí upstream nebo IoT Hub, které můžete přidat další jednotky propustnosti (Jednotky propustnosti) pro škálování vstupní propustnosti. Nezapomeňte, že každý Event Hubs TU maxes ven na výstupní rychlost 2 MB/s.

- Ujistěte se, že jste zřídili dostatek prostředků ve výstupních jímky (tj. SQL Database, Cosmos DB), aby neomezovali nárůst výstupu, což může někdy způsobit uzamčení systému.

Nejdůležitější je předvídat změnu rychlosti zpracování, otestovat tyto scénáře před přechodem do produkčního prostředí a být připraveni správně škálovat zpracování během doby obnovení selhání.

V extrémním scénáři, že všechny příchozí události jsou zpožděné, [je možné, že všechny zpožděné události jsou vynechány,](stream-analytics-time-handling.md) pokud jste použili okno pozdního příjezdu do úlohy. Svržení událostí se může zdát být záhadné chování na začátku; vzhledem však stream analytics je modul zpracování v reálném čase, očekává, že příchozí události budou blízko času nástěnných hodin. Musí vynechat události, které porušují tato omezení.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda Architektury nebo Backfill proces

Předchozí vzor archivace dat lze naštěstí použít ke zpracování těchto pozdních událostí elegantně. Myšlenka spočine na to, že úloha archivace zpracovává příchozí události v čase příjezdu a archivuje události do kontejneru správného času v azure blob nebo Azure Data Lake Store s časem události. Nezáleží na tom, jak pozdě událost přijde, nikdy nebude upuštěno. Vždy přistane ve správném čase. Během obnovení je možné znovu zpracovat archivované události a zaplnit výsledky do úložiště volby. To je podobné tomu, jak jsou implementovány vzory lambda.

![Asa backfill](media/stream-analytics-solution-patterns/backfill.png)

Proces zpětného vyplňování musí být proveden pomocí offline dávkového zpracování systému, který s největší pravděpodobností má jiný programovací model než Azure Stream Analytics. To znamená, že budete muset znovu implementovat celou logiku zpracování.

Pro zasypávání je stále důležité alespoň dočasně zřídit více prostředků do jímek výstupu pro zpracování vyšší propustnosti, než vyžaduje zpracování v ustáleném stavu.

|Scénáře  |Restartovat pouze od této chvíle  |Restartovat od posledního zastaveného času |Restartovat od nynějška + doplnit archivovanými událostmi|
|---------|---------|---------|---------|
|**Řídicí panel**   |Vytvoří mezeru    |OK pro krátký výpadek    |Použití pro dlouhý výpadek |
|**Upozorňování**   |Přijatelné |OK pro krátký výpadek    |Není nutné |
|**Aplikace pro získávání událostí** |Přijatelné |OK pro krátký výpadek    |Použití pro dlouhý výpadek |
|**Datové sklady**   |Ztráta dat  |Přijatelné |Není nutné |
|**Offline analýzy**  |Ztráta dat  |Přijatelné |Není nutné|

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady

Není těžké si představit, že všechny výše uvedené vzorce řešení lze kombinovat ve složitém end-to-end systému. Kombinovaný systém může zahrnovat řídicí panely, výstrahy, aplikace pro získávání událostí, ukládání dat a možnosti offline analýzy.

Klíčem je navrhnout systém v kompozitovatelných vzorech, aby každý subsystém mohl být sestaven, testován, upgradován a obnovován nezávisle.

## <a name="next-steps"></a>Další kroky

Nyní jste viděli různé vzory řešení pomocí Azure Stream Analytics. V dalším kroku se můžete do tématu ponořit hlouběji a vytvořit si svoji první úlohu Stream Analytics:

* [Vytvořit úlohu Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Vytvořte úlohu Stream Analytics pomocí Azure PowerShellu](stream-analytics-quick-create-powershell.md).
* [Vytvořte úlohu Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md).
