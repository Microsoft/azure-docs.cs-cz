---
title: Způsoby řešení Azure Stream Analytics
description: Další informace o vzorech řešení pro Azure Stream Analytics.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 80843abe130f1388a5d4081adab7b9128446763b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761977"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Způsoby řešení Azure Stream Analytics

Stream Analytics je nejvhodnější s jinými službami vytvořit větší-ucelené řešení stejně jako mnoho dalších služeb v Azure. Tento článek popisuje jednoduché řešení Azure Stream Analytics a různých vzorech architektury. Můžete vytvořit na tyto vzory pro vývoj řešení pro složitější. Tyto vzory se dají popsaných v tomto článku je použít v nejrůznějších scénářích. Příkladem scénáře vzory jsou k dispozici na [architektury řešení Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-with-a-real-time-dashboard"></a>Vytvoření úlohy Stream Analytics pomocí řídicího panelu v reálném čase

Azure Stream Analytics snadné použití rychle dokážete výstrahy a řídicí panely v reálném čase. Jednoduchým řešením ingestuje události ze služby Event Hubs nebo služby IoT Hub a [řídicí panel Power BI s nastavená streamovaná data se předají](/power-bi/service-real-time-streaming). Další informace najdete v tématu podrobný kurz [analýzu dat telefonních hovorů službou Stream Analytics a vizualizaci výsledků na řídicím panelu Power BI](stream-analytics-manage-job.md).

![Řídicí panel Power BI Azure Stream Analytics](media/stream-analytics-solution-patterns/pbidashboard.png)

Toto řešení může být sestaven v několika málo minut z webu Azure portal. Neexistuje že žádné rozsáhlé kódování nepodílí a je jazykem SQL express obchodní logiku.

Tento model řešení řídicí panel v reálném čase nabízí nejnižší latenci ze zdroje události na řídicí panel Power BI v prohlížeči. Azure Stream Analytics je služba pouze Azure díky této předdefinované funkci.

## <a name="use-sql-for-dashboard"></a>Použití SQL pro řídicí panel

Řídicí panel Power BI nabízí nízkou latenci, ale nelze ji použít k vytvoření úplné fledged sestav Power BI. Běžným postupem vytváření sestav je nejprve výstupní data do služby SQL database. Pak pomocí Power BI konektor SQL k dotazování SQL pro nejnovější data.

![Řídicí panel SQL Azure Stream Analytics](media/stream-analytics-solution-patterns/sqldashboard.png)

Použití SQL database poskytuje větší flexibilitu za cenu zvýšení latence. Toto řešení je ideální pro úlohy s požadavky na latenci větší než jedna sekunda. Pomocí této metody můžete maximalizovat nástroj Power BI a dalších řezů a kostek data pro sestavy. Můžete také získat lepší flexibilitu vycházející z používání jiných řídicího panelu řešení, například tableau. představují.

SQL není úložiště dat vysokou propustnost a maximální propustnost do databáze SQL z Azure Stream Analytics je 24 MB/s. Pokud zdroje událostí ve vašem řešení vytvářejí data s větší rychlostí, budete muset použít logika zpracování ve službě Stream Analytics ke snížení frekvence výstupu SQL. Techniky, jako je například filtrování, agregace v okně, vzorovou shodu s dočasných spojení, a můžou používat analytických funkcí. Míra výstupních SQL můžete dál optimalizovat pomocí technik popsaných v [výstupu Azure Stream Analytics ke službě Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Začlenit do vaší aplikace se zasíláním zpráv na události v reálném čase

Za druhé nejoblíbenější použití Stream Analytics je Generovat výstrahy v reálném čase. V tomto vzoru řešení obchodní logiky ve službě Stream Analytics lze použít k detekci [dočasné a prostorových vzorů](stream-analytics-geospatial-functions.md) nebo [anomálie](stream-analytics-machine-learning-anomaly-detection.md), pak vytvoří výstrahy signálů. Ale na rozdíl od řídicího panelu řešení, kde Stream Analytics používá Power BI jako upřednostňované koncový bod, počet jímky dočasných dat je možné. Tyto jímky zahrnují služby Event Hubs, služby Service Bus a Azure Functions. Jako tvůrce aplikací, musíte se rozhodnout, které datová jímka pro váš scénář nejvhodnější.

Příjem událostí příjemce logiky musí být implementované Generovat výstrahy v pracovním postupu vaší existující firmy. Vzhledem k tomu, že můžete implementovat vlastní logiku ve službě Azure Functions, Functions je nejrychlejší způsob, jak můžete provést integraci. Kurz pro použití funkce Azure Functions jako výstup pro úlohu Stream Analytics lze nalézt v [spuštění Azure Functions z úloh Azure Stream Analytics](stream-analytics-with-azure-functions.md). Služba Azure Functions podporuje také různé typy oznámení, včetně textu a e-mailu. Aplikace logiky slouží také pro tyto integraci s Event Hubs mezi Stream Analytics a aplikaci logiky.

![Aplikace pro zasílání zpráv událostí Azure Stream Analytics](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Event Hubs, na druhé straně nabízí flexibilní bod integrace. Mnoho dalších služeb, jako je Průzkumník dat Azure a Time Series Insights, můžete zpracovávat události ze služby Event Hubs. Služby se dají připojit přímo do služby Event Hubs jímky z Azure Stream Analytics k dokončení řešení. Event Hubs je také nejvyšší propustnost zasílání zpráv zprostředkovatele k dispozici v Azure pro takové scénáře integrace.

## <a name="dynamic-applications-and-websites"></a>Dynamická aplikace a weby

Můžete vytvořit vlastní vizualizace v reálném čase, jako je například řídicího panelu nebo vizualizace, mapy pomocí Azure Stream Analytics a služby Azure SignalR. Pomocí nástroje SignalR, webovými klienty je možné aktualizovat a zobrazit dynamického obsahu v reálném čase.

![Dynamická aplikace Azure Stream Analytics](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Začlenit do vaší aplikace v reálném čase prostřednictvím úložiště dat

Většina webových služeb a webových aplikací ještě dnes používají vzor požadavku nebo odpovědi k obsluze prezentační vrstvy. Vzor požadavku nebo odpovědi je jednoduché sestavit a je možné snadno škálovat s nízkou odezvu pomocí bezstavové front-end a škálovatelné úložiště, jako jsou služby Cosmos DB.

Objem dat vysoké často vytvoří problémových míst výkonu v systému založených na přístupu CRUD. [Modelu event sourcing řešení vzor](/azure/architecture/patterns/event-sourcing) umožňuje řešit výkonnostní kritické body. Dočasné vzorců a přehledů jsou také složitý a neefektivní extrahovat z tradiční úložiště. Moderní velkého objemu dat databázových aplikací často přijmout architektura založená na toku dat. Azure Stream Analytics jako výpočetní modul přenášených dat je základní pilíř v této architektuře.

![Azure Stream Analytics event sourcing aplikace](media/stream-analytics-solution-patterns/eventsourcingapp.png)

V tomto vzoru řešení událostí zpracování a agregovat do úložišť dat, služba Azure Stream Analytics. Aplikační vrstvu komunikuje s úložišti dat pomocí vzoru tradiční žádostí a odpovědí. Z důvodu schopnost Stream Analytics zpracovávat velké množství událostí v reálném čase, aplikace je vysoce škálovatelná bez nutnosti hromadně nahoru Datová vrstva úložiště. Vrstvy úložiště dat je v podstatě materializovaného zobrazení v systému. [Výstup Azure Stream Analytics ke službě Azure Cosmos DB](stream-analytics-documentdb-output.md) popisuje, jak služby Cosmos DB používá jako výstup Stream Analytics.

Ve skutečných aplikací, kde zpracování logiky je složitá a zde je potřeba upgradovat určité části logiku nezávisle na sobě, více úloh Stream Analytics se může skládat společně s Event Hubs jako zprostředkovatel Zprostředkovatel událostí.

![Azure Stream Analytics komplexní event sourcing aplikace](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Tento model zvyšuje odolnost a možnosti správy systému. I když Stream Analytics zaručuje právě jedno zpracování, existuje ale malé šance, že duplicitní události může dostat do zprostředkující služby Event Hubs. Je důležité pro odstranění duplicit události pomocí logiky klíče v okně lookback podřízené úlohy Stream Analytics. Další informace o doručování událostí, naleznete v tématu [záruky doručení událostí](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) odkaz.

## <a name="use-reference-data-for-application-customization"></a>Využití referenčních dat pro přizpůsobení aplikace

Funkce referenčních dat Azure Stream Analytics je určený speciálně pro přizpůsobení koncových uživatelů jako upozornění prahové hodnoty, pravidel, zpracování a [monitorovaná geografická zóna](geospatial-scenarios.md). Aplikační vrstvu můžete přijmout změny parametrů a jejich uložení v SQL database. Úlohy Stream Analytics pravidelně dotazuje na změny z databáze a zpřístupňuje parametry přizpůsobení prostřednictvím spojení referenční data. Další informace o tom, jak použít referenční data pro přizpůsobení aplikace najdete v tématu [SQL referenčních dat](sql-reference-data.md) a [referenční data spojení](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Tento vzor lze také provádět stroj pravidel kde jsou definovány mezní hodnoty pravidel z referenční data. Další informace o pravidlech najdete v tématu [konfigurovatelných pravidel založené na prahových hodnotách ve službě Azure Stream Analytics zpracovávat](stream-analytics-threshold-based-rules.md).

![Azure Stream Analytics referenční datové aplikace](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Přidat Machine Learning v reálném čase

Azure Stream Analytics integrované [detekce anomálií modelu](stream-analytics-machine-learning-anomaly-detection.md) je pohodlný způsob, jak zavést strojového učení pro aplikace v reálném čase. Musí se širší rozsah v Machine Learning, naleznete v tématu [Azure Stream Analytics se integruje s hodnoticí služby Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Pokročilí uživatelé, kteří chtějí začlenit online trénování a vyhodnocování do stejné kanál Stream Analytics najdete v tématu jak k tomu tento příklad [lineární regrese](stream-analytics-high-frequency-trading.md).

![Aplikace Machine Learning Azure Stream Analytics](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Téměř v reálném čase datové sklady

Další běžný vzor je v reálném čase datové sklady, tzv streamování datového skladu. Kromě událostí přicházejících u služby Event Hubs a služby IoT Hub z vašich aplikací [Azure Stream Analytics na hraničních zařízeních IoT s](stream-analytics-edge.md) lze použít ke splnění čištění dat, Redukce dat a úložiště dat a dopředné potřebám. Stream Analytics na hraničních zařízeních IoT s můžete elegantně zpracovat omezení šířky pásma a problémy s připojením v systému. Adaptér pro výstup SQL lze použít na výstup do služby SQL Data Warehouse; maximální propustnost je však omezeno na 10 MB/s.

![Azure Stream Analytics pro datové sklady](media/stream-analytics-solution-patterns/datawarehousing.png)

Jedním způsobem, jak zlepšit propustnost s některých zpoždění kompromis určeno k archivaci události do úložiště objektů Blob v Azure a potom [naimportovat do služby SQL Data Warehouse pomocí Polybase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Je nutné ručně spojit dohromady výstup ze Stream Analytics do úložiště objektů blob a vstup z úložiště objektů blob do služby SQL Data Warehouse pomocí [archivaci dat pomocí časového razítka](stream-analytics-custom-path-patterns-blob-storage-output.md) a import pravidelně.

V tomto vzoru využití Azure Stream Analytics se používá jako téměř v reálném čase modulu ETL. Nově se opravovány událostí průběžně transformovat a uložit pro příjem dat analytics používání služby.

![Azure Stream Analytics vysokou propustností datových skladů](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Archivace dat pro analýzu v reálném čase

Většina aktivity vědy a analýzy dat k ní v režimu offline. Data je možné archivovat služba Azure Stream Analytics prostřednictvím Azure Data Lake Store Gen2 výstup a Parquet výstupních formátů. Tato možnost odebere řešit zádrhele spojené s k přísun dat přímo do Azure Data Lake Analytics, Azure Databricks a Azure HDInsight. Azure Stream Analytics se používá jako téměř v reálném čase modulu ETL v tomto řešení. Archivovaná data ve službě Data Lake můžete prozkoumat pomocí různých strojů výpočetní prostředky.

![Offline analýzy Azure Stream Analytics](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Využití referenčních dat pro rozšíření

Rozšiřování dat je často požadavek pro moduly ETL. Azure Stream Analytics podporuje rozšiřování dat s [odkazují na data](stream-analytics-use-reference-data.md) z SQL database a Azure Blob storage. Rozšiřování dat lze provést data úvodní v Azure Data Lake a SQL Data Warehouse.

![Azure Stream Analytics offline analýzy s rozšiřování dat](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Zprovoznění přehledy na základě archivovaných dat.

Pokud kombinujete vzor offline analýzy s téměř v reálném čase aplikací vzor, může vytvořit smyčku zpětné vazby. Smyčka zpětné vazby umožňuje aplikaci automaticky upravit pro změnu vzorce v datech. Tato zpětnovazební smyčka může být stejně snadné jako změna prahová hodnota pro výstrahy, nebo komplexního, jako přetrénování modelů Machine Learning. Stejnou architekturu řešení můžete použít pro obě úlohy Azure Stream Analytics, ve kterých v cloudu i na hraničních zařízeních IoT.

![Azure Stream Analytics insights operacionalizace](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Jak monitorovat úlohy Azure Stream Analytics

Úlohy Azure Stream Analytics je možné spustit 24 hodin denně, 7 pro zpracování příchozích událostí průběžně v reálném čase. Záruka jeho dostupnosti je zásadní význam pro stav celkové aplikace. Zatímco Stream Analytics je jediná služba analýzy datových proudů v odvětví, které nabízí [záruku dostupnosti 99,9 %](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), může i za určitou úroveň časové prodlevy. V průběhu let Stream Analytics zavedla metriky, protokoly a stavy úloh podle stavu úlohy. Všechny z nich zobrazují se prostřednictvím služby Azure Monitor a je možné dále exportovat do OMS. Další informace najdete v tématu [pochopit Stream Analytics úlohy sledování a monitorování dotazů](stream-analytics-monitoring.md).

![Monitorování Azure Stream Analytics](media/stream-analytics-solution-patterns/monitoring.png)

Existují dva důležitých věcí, které monitorování:

- [Úloha se nezdařila, stav](job-states.md)

    Především budete muset Ujistěte se, že úloha je spuštěna. Bez úlohy v běžícím stavu jsou generovány žádné nové metriky nebo protokoly. Úlohy můžete změnit do chybového stavu z různých důvodů, včetně s vysokou úroveň využití SU (například dochází prostředky).

- [Metriky zpoždění vodoznak](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Tato Metrika vyjadřuje, jak daleko za zpracování kanálu je v skutečný čas (v sekundách). Některých zpoždění je přiřadit vlastní zpracování logiky. V důsledku toho monitorování rostoucí trend je mnohem důležitější než absolutní hodnota monitorování. Zpoždění stabilního stavu by měl řešit návrhu aplikace, nikoli na základě monitorování nebo výstrah.

Po selhání, protokoly aktivit a [diagnostické protokoly](stream-analytics-job-diagnostic-logs.md) jsou nejlepších zdrojů, Zahájit hledání chyb.

## <a name="build-resilient-and-mission-critical-applications"></a>Sestavení, které jsou odolné a nejdůležitější aplikace

Bez ohledu na to, Azure Stream Analytics se zárukou smlouvy SLA a jak pečlivé spustíte svou aplikaci začátku do konce dojít k výpadku. Pokud vaše aplikace je zásadně důležité, budete muset připravit k výpadku, aby bylo možné provést řádné zotavení.

Pro upozornění aplikace, je nejdůležitější, zjišťovat další výstraha. Můžete se rozhodnout, restartujte úlohu z aktuální čas při obnovení, ignoruje se poslední výstrahy. Sémantika čas spuštění úlohy jsou ve výstupní poprvé, není vstupní čas. Vstup je zpětně převinuta odpovídající množství času zaručuje, že první výstup v určený čas je kompletní a správný. Nelze získat částečné agregace a aktivovat výstrahy neočekávaně v důsledku.

Můžete také spustit výstup z určitou část času v minulosti. Zásady uchovávání informací služby Event Hubs a služby IoT Hub podržte přiměřené dat umožňující zpracování v minulosti. Výměnou za to je, jak rychle můžete dohnat na aktuální čas a začněte a vytvořte nový zobrazují i včasné výstrahy. Proto je důležité dohnat na aktuální čas rychle data rychle ztratí její hodnotu v průběhu času. Existují dva způsoby, jak rychle dohnat:

- Zřízení více prostředků (SU) při zachycování.
- Restartujte od aktuálního času.

Restartování z aktuální čas je snadno udělat za cenu opuštění mezera během zpracování. Restartuje se tímto způsobem může být OK pro generování výstrah scénáře, ale může být problematické pro scénáře, řídicí panel a bez starter archivace a scénáře datových skladů.

Zřízení více prostředků můžou urychlit proces, ale účinek s nárůst rychlost zpracování je složitá.

- Test, že vaše úloha není škálovatelné, aby u většího počtu su. Ne všechny dotazy jsou škálovatelné. Je třeba Ujistěte se, že váš dotaz je [paralelizovaná](stream-analytics-parallelization.md).

- Ujistěte se, že nejsou k dispozici dostatek oddílů v nadřazeného Event Hubs nebo centra IoT, které můžete přidat další jednotky propustnosti (jednotek propustnosti, které) škálovat propustnost vstupu. Nezapomeňte, že každý jednotek Propustnosti Event Hubs navyšuje si výstup rychlostí 2 MB za sekundu.

- Ujistěte se, že máte zřízené dostatek prostředků ve výstupních jímek (tj. SQL Database, Cosmos DB), takže se nemusíte omezit prudký nárůst výstup, který můžete někdy způsobit zhroucení systému.

Nejdůležitějším principem je předvídat změnit rychlost zpracování, testování scénářů před přechodem do produkčního prostředí a být připravené ke škálování zpracování správně během čas selhání obnovení.

V extrémním případě, že jsou všechny příchozí události zpožděné, [je možné všechny zpožděné události se zahodí](stream-analytics-time-handling.md) Pokud jste použili pozdní opravovány okno pro vaši úlohu. Vyřazování událostí může zobrazit záhadnými a často chování na začátku; considering Stream Analytics je modul pro zpracování v reálném čase, ale očekává příchozím událostem na blížící skutečný čas. Je třeba vyřadit události, které porušují tato omezení.

### <a name="backfilling-process"></a>Backfilling procesu

Naštěstí předchozí vzorek archivace dat lze použít ke zpracování těchto událostí bez výpadku. Cílem je, že úloha archivace zpracovává příchozí události v čas doručení a archivaci události do kontejneru správný čas v Azure Data Lake Store nebo Azure Blob s jejich čas události. Nezáleží na nejpozdější události dorazí, se nikdy zahodí. Vždy ho přímo do kontejneru správný čas. Během obnovení je možné znovu zpracovat archivované události a obnovení dat výsledků do úložiště podle výběru.

![Obnovení dat Azure Stream Analytics](media/stream-analytics-solution-patterns/backfill.png)

Proces obnovení dat je třeba provést pomocí offline dávkové zpracování systém, který pravděpodobně obsahuje různé programovací model, než Azure Stream Analytics. To znamená, že budete muset znovu implementovat logiku zpracování celé.

Pro backfilling je stále potřeba alespoň dočasně zřízení více prostředků k výstupu jímky pro zpracování vyšší výkon než stabilního stavu zpracování potřebám.

|Scénáře  |Restartovat nyní pouze  |Restartování z poslední čas zastavení |Restartovat nyní + obnovení dat pomocí archivované událostí|
|---------|---------|---------|---------|
|**Mnoha**   |Vytvoří mezeru    |OK pro krátký výpadek    |Použití pro dlouhé výpadek |
|**Výstrahy**   |Přijatelné |OK pro krátký výpadek    |Není potřeba |
|**Event sourcing aplikace** |Přijatelné |OK pro krátký výpadek    |Použití pro dlouhé výpadek |
|**Datového skladu**   |Ztráta dat  |Přijatelné |Není potřeba |
|**Offline analýzy**  |Ztráta dat  |Přijatelné |Není potřeba|

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady

Není těžko Představte si, že všechny vzorky řešení uvedeného výše se dá zkopírovat dohromady v komplexního systému začátku do konce. Kombinované systému může obsahovat řídicí panely, výstrahy, event sourcing aplikací, datového skladu a možnosti offline analýzy.

Klíčem je návrh systému ve složení vzorech, takže každý subsystém je možné sestavit, testovat, upgradovat a obnovit nezávisle na sobě.

## <a name="next-steps"></a>Další postup

Nyní jste viděli různých vzorů řešení pomocí Azure Stream Analytics. V dalším kroku se můžete do tématu ponořit hlouběji a vytvořit si svoji první úlohu Stream Analytics:

* [Vytvořit úlohu Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Vytvořit úlohu Stream Analytics pomocí Azure PowerShellu](stream-analytics-quick-create-powershell.md)
* [Vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md).
