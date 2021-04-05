---
title: Vzory řešení služby Azure Stream Analytics
description: Přečtěte si o běžných vzorech řešení pro Azure Stream Analytics, jako jsou řídicí panely, zasílání zpráv o událostech, úložiště dat, rozšíření referenčních dat a monitorování.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1bd3c1099344bd266d7e3bc153613daaecfb412a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020311"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Vzory řešení služby Azure Stream Analytics

Stejně jako v případě mnoha dalších služeb v Azure je Stream Analytics nejlépe využít jiné služby k vytvoření většího uceleného řešení. Tento článek popisuje jednoduchá Azure Stream Analytics řešení a různé modely architektury. Pro vývoj složitějších řešení můžete vytvořit tyto vzory. Vzory popsané v tomto článku se dají použít v nejrůznějších scénářích. Příklady vzorů specifických pro scénáře jsou k dispozici v [architektuře řešení Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Vytvoření úlohy Stream Analytics pro práci s řídicím panelem v reálném čase

Pomocí Azure Stream Analytics můžete rychle vytvářet řídicí panely a výstrahy v reálném čase. Jednoduché řešení ingestuje události z Event Hubs nebo IoT Hub a [informační panel Power BI vytvoří pomocí sady streamování dat](/power-bi/service-real-time-streaming). Další informace najdete v podrobném kurzu [Analýza dat podvodných volání pomocí Stream Analytics a vizualizace výsledků v Power BIm řídicím panelu](stream-analytics-real-time-fraud-detection.md).

![ASA Power BI řídicí panel](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

Toto řešení může být sestaveno během několika minut od Azure Portal. Neexistují žádné obsáhlé kódování a jazyk SQL slouží k vyjádření obchodní logiky.

Tento vzor řešení nabízí nejnižší latenci ze zdroje událostí na řídicí panel Power BI v prohlížeči. Azure Stream Analytics je jediná služba Azure s touto integrovanou schopností.

## <a name="use-sql-for-dashboard"></a>Použít SQL pro řídicí panel

Řídicí panel Power BI nabízí nízkou latenci, ale nedá se použít k tvorbě úplných sestav Power BI podrobnějším. Běžným vzorem vytváření sestav je výstup dat, který se SQL Database jako první. Pak použijte konektor SQL Power BI k dotazování SQL na nejnovější data.

![Řídicí panel SQL ASA](media/stream-analytics-solution-patterns/sql-dashboard.png)

Použití SQL Database přináší větší flexibilitu, ale za cenu mírně vyšší latence. Toto řešení je optimální pro úlohy s požadavky na latenci větší než jedna sekunda. Pomocí této metody můžete maximalizovat možnosti Power BI pro další řezy a indexy dat pro sestavy a spoustu dalších možností vizualizace. Získáte také flexibilitu při používání jiných řešení řídicích panelů, jako je například Tableau.

SQL není úložiště dat s vysokou propustností. Maximální propustnost SQL Database z Azure Stream Analytics je aktuálně okolo 24 MB/s. Pokud zdroje událostí ve vašem řešení vytváří data s vyšší rychlostí, je nutné použít logiku zpracování v Stream Analytics k omezení výstupní rychlosti na SQL. Lze použít techniky, jako je filtrování, agregace oken, porovnávání vzorů s doplňováním a doplňování a analytické funkce. Výstupní rychlost do SQL je možné dále optimalizovat pomocí technik popsaných v [Azure Stream Analyticsovém výstupu do Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Zahrnutí informací v reálném čase do vaší aplikace pomocí zasílání zpráv o událostech

Druhým nejoblíbenějším využitím Stream Analytics je generování výstrah v reálném čase. V tomto vzoru řešení je možné pomocí obchodní logiky v Stream Analytics detekovat [dočasné a prostorové vzorce](stream-analytics-geospatial-functions.md) nebo [anomálie](stream-analytics-machine-learning-anomaly-detection.md)a následně generovat signály výstrah. Nicméně na rozdíl od řešení řídicího panelu, kde Stream Analytics používá Power BI jako preferovaný koncový bod, lze použít řadu mezidatových umyvadel. Mezi tyto jímky patří Event Hubs, Service Bus a Azure Functions. Jako tvůrce aplikací se musíte rozhodnout, jaká datová jímka funguje nejlépe pro váš scénář.

Pro generování výstrah v existujícím podnikovém pracovním postupu je nutné implementovat logiku příjemce událostí pro příjem dat. Vzhledem k tomu, že můžete implementovat vlastní logiku v Azure Functions, Azure Functions je nejrychlejší způsob, jakým můžete tuto integraci provést. Kurz použití funkce Azure Functions jako výstupu Stream Analytics úlohy najdete v části [spuštění Azure Functions z Azure Stream Analytics úloh](stream-analytics-with-azure-functions.md). Azure Functions také podporuje různé typy oznámení, včetně textu a e-mailu. Aplikace logiky se dá použít i pro takovou integraci s Event Hubs mezi Stream Analytics a logikou aplikaci.

![Aplikace pro zasílání zpráv o událostech ASA](media/stream-analytics-solution-patterns/event-messaging-app.png)

Event Hubs, na druhé straně, nabízí nejpružnější integrační bod. Mnoho dalších služeb, jako je Azure Průzkumník dat a Time Series Insights, může zpracovávat události z Event Hubs. Služby je možné připojit přímo k Event Hubs jímky z Azure Stream Analytics a dokončit řešení. Event Hubs je také nejvyšší propustnost jako zprostředkovatel zasílání zpráv v Azure, které jsou pro tyto scénáře integrace k dispozici.

## <a name="dynamic-applications-and-websites"></a>Dynamické aplikace a weby

Pomocí Azure Stream Analytics a služby signalizace Azure můžete vytvářet vlastní vizualizace v reálném čase, jako je řídicí panel nebo vizualizace mapy. Pomocí nástroje Signaler lze webové klienty aktualizovat a zobrazit dynamický obsah v reálném čase.

![Dynamická aplikace ASA](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Zahrnutí informací do vaší aplikace v reálném čase prostřednictvím úložišť dat

Většina webových služeb a webových aplikací dnes používá k obsluze prezentační vrstvy vzor požadavků a odpovědí. Vzor požadavků a odpovědí je jednoduchý pro sestavování a dá se snadno škálovat s nízkou dobou odezvy pomocí bezstavového a škálovatelného úložiště, jako je Cosmos DB.

Velký objem dat často vytváří kritická místa výkonu v systému založeném na CRUD. [Vzor řešení zdroje událostí](/azure/architecture/patterns/event-sourcing) se používá k vyřešení problémových míst výkonu. Dočasné vzorce a přehledy jsou také obtížné a neefektivní pro extrakci z tradičního úložiště dat. Moderní aplikace s vysokým objemem dat často přijímají architekturu založenou na toku dat. Azure Stream Analytics jako výpočetní modul pro data v pohybu je linchpin v této architektuře.

![Aplikace pro zdroje událostí ASA](media/stream-analytics-solution-patterns/event-sourcing-app.png)

V tomto vzoru řešení jsou události zpracovávány a agregovány do úložišť dat pomocí Azure Stream Analytics. Aplikační vrstva komunikuje s úložištěm dat pomocí tradičního vzoru požadavků a odpovědí. Vzhledem k tomu, že Stream Analytics ' schopnost zpracovat velký počet událostí v reálném čase, je aplikace vysoce škálovatelná, aniž by bylo potřeba hromadně nahromadit vrstvu úložiště dat. Vrstva úložiště dat je v podstatě materializované zobrazení v systému. [Azure Stream Analytics výstup do Azure Cosmos DB](stream-analytics-documentdb-output.md) popisuje, jak se Cosmos DB používá jako výstup Stream Analytics.

V reálných aplikacích, kde je logika zpracování složitá a je potřeba upgradovat určité části logiky nezávisle, je možné sestavovat více úloh Stream Analytics společně s Event Hubs jako zprostředkující Zprostředkovatel událostí.

![Aplikace pro komplexní zdrojové události ASA](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

Tento model zlepšuje odolnost a možnosti spravovatelnosti systému. Nicméně i když Stream Analytics garantuje, že se právě jednou zpracovává, dojde k malému pravděpodobnosti, že se v zprostředkovatelských Event Hubs můžou vycházet duplicitní události. Je důležité, aby úloha Stream Analytics pro příjem dat na odstranění duplicit pomocí logických klíčů v okně lookback. Další informace o doručování událostí najdete v tématu informace o [zárukách pro doručení událostí](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) .

## <a name="use-reference-data-for-application-customization"></a>Použití referenčních dat pro přizpůsobení aplikace

Funkce referenčních dat Azure Stream Analytics je navržena speciálně pro přizpůsobení koncových uživatelů, jako je prahová hodnota pro výstrahy, pravidla zpracování a [geografické](geospatial-scenarios.md)oblasti. Vrstva aplikace může přijmout změny parametrů a uložit je do SQL Database. Úloha Stream Analytics pravidelně odesílá dotazy na změny z databáze a zpřístupňuje parametry přizpůsobení prostřednictvím připojení referenčních dat. Další informace o tom, jak používat referenční data pro přizpůsobení aplikace, najdete v tématu [referenční data SQL](sql-reference-data.md) a [připojení referenčních dat](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Tento model lze také použít k implementaci modulu pravidel, kde jsou prahové hodnoty pravidel definovány z referenčních dat. Další informace o pravidlech najdete v tématu [proces konfigurovatelného pravidla na základě prahových hodnot v Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![Aplikace referenčních dat ASA](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Přidání Machine Learning k přehledům v reálném čase

Azure Stream Analytics "integrovaný [model detekce anomálií](stream-analytics-machine-learning-anomaly-detection.md) je pohodlný způsob, jak zavést Machine Learning aplikace v reálném čase. Širší škálu Machine Learning potřeb najdete v tématu [Azure Stream Analytics integruje se službou bodování Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Pokročilým uživatelům, kteří chtějí integrovat online školení a bodování do stejného Stream Analytics kanálu, naleznete v tomto příkladu k tomu, jak postupovat pomocí [lineární regrese](stream-analytics-high-frequency-trading.md).

![Aplikace ASA Machine Learning](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="real-time-data-warehousing"></a>Datové sklady v reálném čase

Dalším běžným vzorem jsou datové sklady v reálném čase, označované také jako streamování datového skladu. Kromě událostí přicházejících na Event Hubs a IoT Hub z vaší aplikace se [Azure Stream Analytics spuštěná na IoT Edge](stream-analytics-edge.md) dají použít ke splnění vyčištění dat, snížení počtu dat a uchování dat a potřebnou potřebu. Stream Analytics spuštěné v IoT Edge může řádně zvládnout omezení šířky pásma a problémy s připojením v systému. Stream Analytics může při zápisu do Azure synapse Analytics podporovat míry propustnosti 200 MB/s.

![Datové sklady ASA](media/stream-analytics-solution-patterns/data-warehousing.png)


## <a name="archiving-real-time-data-for-analytics"></a>Archivace dat v reálném čase pro analýzy

Většina aktivit pro datové vědy a analýzy stále probíhá offline. Data je možné archivovat Azure Stream Analytics prostřednictvím výstupních formátů Azure Data Lake Store Gen2 a výstupu. Tato schopnost odstraní třecí data přímo do Azure Data Lake Analytics, Azure Databricks a Azure HDInsight. Azure Stream Analytics se používá jako modul ETL v reálném čase v tomto řešení. Archivovaná data můžete prozkoumat v Data Lake pomocí různých výpočetních motorů.

> [!div class="mx-imgBorder"]
> ![Offline Analýza ASA](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>Použít referenční data pro obohacení

Rozšíření dat je často požadavkem pro moduly ETL. Azure Stream Analytics podporuje rozšíření dat pomocí [referenčních dat](stream-analytics-use-reference-data.md) z SQL Database a úložiště objektů BLOB v Azure. Rozšíření dat je možné provést pro data odpočívadla v Azure Data Lake i ve službě Azure synapse Analytics.


![Offline Analýza ASA s obohacením dat](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>Zprovoznění přehledy z archivovaných dat

Pokud kombinujete model offline analýzy se vzorem aplikace téměř v reálném čase, můžete vytvořit smyčku zpětné vazby. Smyčka zpětné vazby umožňuje aplikaci automaticky upravit na měnící se vzory v datech. Tato smyčka zpětné vazby může být jednoduchá jako Změna prahové hodnoty pro upozorňování nebo jako nesložitá jako přeškolování Machine Learningch modelů. Stejnou architekturu řešení lze použít pro úlohy ASA spuštěné v cloudu a na IoT Edge.

![Provozní provoz ASA Insights](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Jak monitorovat úlohy ASA

Azure Stream Analytics úlohu lze spustit 24/7 a nepřetržitě zpracovávat příchozí události v reálném čase. Záruka na jeho dobu provozu je zásadní pro stav celkové aplikace. I když je Stream Analytics jedinou službou Stream Analytics v oboru, která nabízí  [záruku dostupnosti 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), může se vám stále účtovat určitá úroveň času. V průběhu let Stream Analytics zavedla metriky, protokoly a stavy úloh, aby odrážely stav úloh. Všechny z nich jsou provedené prostřednictvím služby Azure Monitor a je možné je dál exportovat do OMS. Další informace najdete v tématu [pochopení Stream Analytics monitorování úloh a postup monitorování dotazů](stream-analytics-monitoring.md).

![Monitorování ASA](media/stream-analytics-solution-patterns/monitoring.png)

Ke sledování jsou k dispozici dvě klíčové věci:

- [Selhání úlohy stav](job-states.md)

    Nejprve je potřeba zajistit, aby byla úloha spuštěná. Bez úlohy ve stavu spuštěno nejsou vygenerovány žádné nové metriky ani protokoly. Úlohy se můžou u různých důvodů změnit na stav selhání, včetně vysoké úrovně využití SU (tj. nedostatek prostředků).

- [Metriky zpoždění vodoznaku](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Tato metrika odráží, jak daleko je za vaším kanálem zpracování čas (sekundy). Některá z těchto zpoždění jsou spojena s vlastní logikou zpracování. V důsledku toho je sledování rostoucího trendu mnohem důležitější než monitorování absolutní hodnoty. Zpoždění ustáleného stavu by mělo být řešeno návrhem vaší aplikace, nikoli monitorováním nebo výstrahami.

Po selhání jsou protokoly aktivit a [diagnostické protokoly](stream-analytics-job-diagnostic-logs.md) nejlepším místem, kde můžete začít hledat chyby.

## <a name="build-resilient-and-mission-critical-applications"></a>Vytvářejte odolné a klíčové aplikace

Bez ohledu na záruku smlouvy SLA Azure Stream Analytics a na to, jak opatrní spouštíte komplexní aplikaci, dochází k výpadkům. Pokud je vaše aplikace kritická, je potřeba se připravit na výpadky, aby se daly řádně obnovit.

U aplikací s výstrahami je nejdůležitější zjistit další výstrahu. Můžete zvolit, že se má úloha po opětovném obnovování a ignorování minulých výstrah restartovat. Sémantika času zahájení úlohy je podle prvního výstupního času, nikoli podle prvního vstupního času. Vstup se předává zpět na odpovídající dobu, aby se zajistilo, že první výstup v zadaném čase je úplný a správný. V důsledku toho se neočekávaně nevrátí částečné agregace a triggery.

Můžete se také rozhodnout spustit výstup z určitého množství času v minulosti. Jak Event Hubs, tak zásady uchovávání IoT Hub uchovávají přijatelné množství dat, aby bylo možné zpracování z minulosti. Kompromisům je, jak rychle můžete zachytit aktuální čas a začít generovat včasné nové výstrahy. Data v čase rychle ztratí svou hodnotu, takže je důležité rychle zachytit až aktuální čas. Existují dva způsoby, jak rychle zachytit:

- Zřizování více prostředků (SU) při zachytávání
- Restartování z aktuálního času.

Restartování z aktuálního času je jednoduché – s kompromisy při zpracovávání mezery při zpracování. Restartování tohoto způsobu může být v případě scénářů upozorňování, ale může být problematické pro scénáře s řídicím panelem a pro scénáře archivace a datových skladů je nestarter.

Zřizování dalších prostředků může urychlit proces, ale účinek, který je nárůstem míry zpracování, je složitý.

- Otestujte, jestli je vaše úloha škálovatelná na větší počet služby SUs. Ne všechny dotazy jsou škálovatelné. Je nutné zajistit, aby byl dotaz [paralelně](stream-analytics-parallelization.md).

- Ujistěte se, že je v nadřazeném Event Hubs nebo v IoT Hub dostatek oddílů, které vám umožní přidat další jednotky propustnosti (počet propustnosti) a škálovat vstupní propustnost. Mějte na paměti, že každá Event Hubs navyšuje je výstupní rychlostí 2 MB/s.

- Ujistěte se, že jste ve výstupní jímky (tj. SQL Database Cosmos DB) zřídili dostatek prostředků, aby neomezily nárůst ve výstupu, což může někdy způsobit, že se systém zamkne.

Nejdůležitějším aspektem je předpokládat změnu míry zpracování, otestovat tyto scénáře před tím, než začnete pracovat do produkčního prostředí, a připravit se na správné škálování zpracování během doby zotavení po selhání.

V extrémním scénáři, kdy jsou příchozí události zpožděny, je [možné všechny zpožděné události vyřadit](stream-analytics-time-handling.md) , pokud jste do úlohy použili okno s pozdním doručením. Vyřazení událostí může vypadat jako záhadnými chování na začátku; zvažujeme však, že Stream Analytics je modul pro zpracování v reálném čase, očekává se, že příchozí události budou blízko času na zdi. Je nutné vyřadit události, které porušují tato omezení.

### <a name="lambda-architectures-or-backfill-process"></a>Architektury lambda nebo proces nedokončeného naplnění

Naštěstí můžete použít předchozí vzor archivace dat k řádnému zpracování těchto zpožděných událostí. Výsledkem je, že úloha archivace zpracovává příchozí události v čase přijetí a archivuje události do pravého časového intervalu v objektu blob Azure nebo Azure Data Lake Store s jejich časem události. Nezáleží na tom, jak pozdě událost dorazí, nebude nikdy vyřazena. Vždy se bude nakládat v pravém časovém intervalu. Během obnovení je možné znovu zpracovat archivované události a výsledky zpětně vyplnit do úložiště, které si vyberete. To je podobné jako při implementaci vzorů lambda.

![Doplňování ASA](media/stream-analytics-solution-patterns/back-fill.png)

Proces pro obnovení musí být proveden s offline systémem zpracování dávek, který má pravděpodobně jiný programovací model než Azure Stream Analytics. To znamená, že je nutné znovu implementovat celou logiku zpracování.

Pro zpětnou zátěž je stále důležité aspoň dočasně zřídit více prostředků výstupními jímkami, aby bylo možné zvládnout vyšší propustnost, než je potřeba stabilní stav zpracování.

|Scénáře  |Restartovat pouze z Now  |Restartování z posledního pozastaveného času |Restartování z Now a zpětná výplň s archivovanými událostmi|
|---------|---------|---------|---------|
|**Mnoha**   |Vytvoří mezeru.    |OK pro krátký výpadek    |Použití při dlouhém výpadku |
|**Upozorňování**   |Přípustná |OK pro krátký výpadek    |Není nutné |
|**Aplikace pro zdroje událostí** |Přípustná |OK pro krátký výpadek    |Použití při dlouhém výpadku |
|**Datové sklady**   |Ztráta dat  |Přípustná |Není nutné |
|**Offline Analýza**  |Ztráta dat  |Přípustná |Není nutné|

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady

Představte si, že všechny výše uvedené vzory řešení je možné kombinovat dohromady ve složitém koncovém systému. Kombinovaný systém může zahrnovat řídicí panely, výstrahy, aplikace pro generování událostí, datové sklady a možnosti offline analýzy.

Klíčem je navrhovat systém v sestavách, aby každý podsystém mohl být sestaven, testován, upgradován a obnovovat nezávisle.

## <a name="next-steps"></a>Další kroky

Teď jste viděli celou řadu vzorů řešení pomocí Azure Stream Analytics. V dalším kroku se můžete do tématu ponořit hlouběji a vytvořit si svoji první úlohu Stream Analytics:

* [Vytvořit úlohu Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Vytvořte Stream Analyticsovou úlohu pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Pomocí sady Visual Studio vytvořte Stream Analyticsovou úlohu](stream-analytics-quick-create-vs.md).
