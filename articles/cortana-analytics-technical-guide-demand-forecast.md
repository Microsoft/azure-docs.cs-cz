---
title: Požadovat předpovědi v energetické – technický průvodce
description: Technický průvodce do šablony řešení s Microsoft Cortana Intelligence pro energie prognózy poptávky.
services: machine-learning
author: garyericson
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/16/2016
ms.author: garye
ms.openlocfilehash: e18e1fb3e97dd9f846ee71be4f0fbb66aeca3d88
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238858"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Technický průvodce do šablony řešení Cortana Intelligence pro energie prognózy poptávky
## <a name="overview"></a>**Přehled**
Šablony řešení jsou určeny ke zrychlení procesu vytváření ukázku E2E nad Cortana Intelligence Suite. Se nasadila šablona předpisy předplatného pro nezbytné součásti Cortana Intelligence a vytváření vztahů mezi. Je také nasazení nasazuje datový kanál s ukázkovými daty generováno z aplikace data simulace. Stáhnout simulátor dat z uvedeného odkazu a nainstalujte na svém místním počítači, souboru readme.txt pokyny k používání simulátoru. Data vygenerovaná ze simulátoru hydráty datového kanálu a spuštění generování predikcí strojového učení, které lze následně vizualizovat na řídicím panelu Power BI.

Šablona řešení najdete [zde](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

Proces nasazení provede několik kroků pro nastavení přihlašovacích údajů vašeho řešení. Zajistěte, aby že zaznamenávat tyto přihlašovací údaje, jako je například název řešení, uživatelské jméno a heslo, které zadáte během nasazení.

Cílem tohoto dokumentu je vysvětlit referenční architekturu a různých komponent, které jsou zřízené v rámci vašeho předplatného jako součást Tato šablona řešení. Dokument také mluví o tom, jak ukázková data, nahraďte reálná data vlastní, bude moci zobrazit přehledy/predikcí od vás získané data. Kromě toho dokument hovoří o tom, části šablony řešení, které by bylo potřeba změnit, pokud chcete přizpůsobit řešení s vlastními daty. Pokyny, jak vytvořit řídicí panel Power BI pro tuto šablonu řešení jsou k dispozici na konci.

## <a name="details"></a>**Podrobnosti**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Vysvětlení architektury
Po nasazení řešení různé služby Azure v sadě Cortana Analytics Suite aktivují se (to znamená, Centrum událostí, Stream Analytics, HDInsight, Data Factory, Machine Learning, *atd*). Diagram architektury ukazuje na vysoké úrovni, jak Prognózování poptávky po energii šablona řešení je vytvořit od začátku do konce. Po kliknutí na diagram šablony řešení vytvořené s nasazením řešení můžete prozkoumat tyto služby. Následující části popisují jednotlivé komponenty.

## <a name="data-source-and-ingestion"></a>**Zdroj dat a přijímání**
### <a name="synthetic-data-source"></a>Zdroj dat syntetického
Pro tuto šablonu je použitý zdroj dat generovaných desktopová aplikace, stáhněte a spusťte místně po úspěšném nasazení. Pokyny ke stažení a instalace této aplikace na panelu Vlastnosti, když vyberete první uzel s názvem simulátor dat prognózy energie v diagramu šablonu řešení pro vás. Tato aplikace se předají [Azure Event Hubs](#azure-event-hub) služba s datové body nebo události, které se používají ve zbytku toku řešení.

Generování aplikace události naplní Azure Event Hubs pouze tehdy, když je spuštěn v počítači.

### <a name="azure-event-hub"></a>Centrum událostí Azure
[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) je služba příjemce vstup syntetické zdroj dat je popsáno.

## <a name="data-preparation-and-analysis"></a>**Příprava dat a analýza**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) služby slouží k poskytování téměř v reálném čase analýzy vstupního datového proudu z [Azure Event Hubs](#azure-event-hub) služby a publikovat výsledky do [Power BI](https://powerbi.microsoft.com)řídicího panelu a také archivuje všechny nezpracované příchozí události do [služby Azure Storage](https://azure.microsoft.com/services/storage/) service pro pozdější zpracování [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) služby.

### <a name="hdinsight-custom-aggregation"></a>HDInsight vlastní agregace
Služba Azure HDInsight se používá ke spouštění [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skriptů (orchestrovaných službou Azure Data Factory) a poskytuje agregace nezpracovaných událostí archivovaných pomocí služby Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) service (orchestrovaná službou Azure Data Factory) k Prognózování hladiny v budoucí spotřebu energie v konkrétní oblasti na základě přijatých vstupů.

## <a name="data-publishing"></a>**Publikování dat**
### <a name="azure-sql-database-service"></a>Služba Azure SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) služby se používá k ukládání (spravované službou Azure Data Factory) předpovědí přijaté službou Azure Machine Learning, která se využívá v [Power BI](https://powerbi.microsoft.com) řídicího panelu.

## <a name="data-consumption"></a>**Využití dat**
### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) služby se používá k zobrazení řídicího panelu, který obsahuje agregace poskytované [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) služby, jakož i vyžádání prognózy výsledky uložené v [Azure SQL Databáze](https://azure.microsoft.com/services/sql-database/) , které byly vytvořeny pomocí [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) služby. Pokyny, jak vytvořit řídicí panel Power BI pro tuto šablonu řešení najdete v následující části.

## <a name="how-to-bring-in-your-own-data"></a>**Jak umožňuje přinést si vlastní data**
Tato část popisuje, jak přineste si vlastní data do Azure a oblasti, které by vyžadovaly změny dat, než převedete do této architektury.

Není pravděpodobné, že všechny datové sady, vy přivedete odpovídají datové sady použité pro tuto šablonu řešení. Vysvětlení dat a požadavky jsou zásadní v tom, jak změnit tato šablona fungovala s vlastními daty. Pokud jsou nové službě Azure Machine Learning, můžete získat Úvod k němu pomocí příkladu v [vytvoření prvního experimentu](machine-learning/studio/create-experiment.md).

Následující části popisují části šablony, která vyžaduje změny, pokud je zavedená nová datová sada.

### <a name="azure-event-hub"></a>Centrum událostí Azure
[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) služby není obecný, takže můžete odeslat data do centra ve formátu CSV nebo JSON. Neproběhne žádné speciální zpracování ve službě Azure Event Hub, ale je důležité, že rozumíte data, která se zobrazí na něj.

Tento dokument nepopisuje přijímat data, ale jeden můžete snadno odesílat události nebo data do centra událostí Azure pomocí [Event Hub API](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) služby slouží k poskytování téměř v reálném čase analýzy čtení z datových proudů a výstup dat do libovolného počtu zdrojů.

Prognózování poptávky po energii šablony řešení Azure Stream Analytics query tvořen dvěma poddotazy, každý využívající událostí ze služby Azure Event Hubs jako vstupy a výstupy museli dvou různých umístěních. Tyto výstupy skládat z jedné datové sady Power BI a jednoho umístění úložiště Azure.

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) najdou dotazu:

* Přihlásit [webu Azure portal](https://portal.azure.com/)
* Vyhledání úlohy stream analytics ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) , které byly generovány při řešení bylo nasazeno. Jedna je pro odesílání dat do úložiště objektů blob (například mytest1streaming432822asablob) a druhou pro odesílání dat do Power BI (například mytest1streaming432822asapbi).
* Výběr

  * ***VSTUPY*** o dotazu vstup
  * ***DOTAZ*** zobrazíte samotný dotaz
  * ***VYPÍŠE*** Chcete-li zobrazit různé výstupy

Informace o Azure Stream Analytics konstrukce dotazů najdete v [referenční příručka k Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx) na webové stránce MSDN.

V tomto řešení se úlohy Azure Stream Analytics, jejichž výstupem jsou datové sady s téměř v reálném čase analýzy informace o příchozí datový proud na řídicí panel Power BI poskytuje jako součást Tato šablona řešení. Protože implicitní znalosti o formátu příchozí data se tyto dotazy by bylo potřeba změnit podle datový formát.

Další úlohy Azure Stream Analytics vypíše všechny [centra událostí](https://azure.microsoft.com/services/event-hubs/) události [služby Azure Storage](https://azure.microsoft.com/services/storage/) a proto vyžaduje žádné změny bez ohledu na formát dat, jak je informace celá událost Streamovat do úložiště.

### <a name="azure-data-factory"></a>Azure Data Factory
[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) přesouvání a zpracování dat zorganizuje služba. V Prognózování poptávky po energii šablonu řešení data factory se skládá z 12 [kanály](data-factory/concepts-pipelines-activities.md) , přesouvat a zpracovávat data pomocí různých technologií.

  Datové továrny můžete přistupovat tak, že otevřete uzel služby Data Factory v dolní části diagramu šablony řešení vytvořené pomocí nasazení řešení. Zobrazí se služby data factory na webu Azure Portal. Pokud v rámci vaší datové sady se zobrazí chyby, můžete tyto ignorovat jsou kvůli nasazení předtím, než byl spuštěn generátor dat služby data factory. Tyto chyby nebrání svou datovou továrnu fungovat.

Tato část pojednává o nezbytné [kanály](data-factory/concepts-pipelines-activities.md) a [aktivity](data-factory/concepts-pipelines-activities.md) součástí [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Následující obrázek je diagram tohoto řešení:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Pět kanály tento objekt pro vytváření obsahovat [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty, které se používají k rozdělení a agregovat data. Pokud jste si poznamenali, skripty jsou umístěny v [služby Azure Storage](https://azure.microsoft.com/services/storage/) účet vytvořený během instalace. Jejich umístění je: demandforecasting\\\\skript\\\\hive\\ \\ (nebo name].blob.core.windows.net/demandforecasting https://[Your řešení).

Podobně jako [Azure Stream Analytics](#azure-stream-analytics-1) dotazy, [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty mají implicitní znalosti o příchozích formát dat, tyto dotazy by bylo potřeba změnit na základě formátu data a [konstruování](machine-learning/team-data-science-process/create-features.md) požadavky.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) pomocí aktivity [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , který běží [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript data o poptávce agregační datovým proudem v každých 10 sekund v úrovni transformovny každou hodinu oblasti úroveň a vložit [služby Azure Storage](https://azure.microsoft.com/services/storage/) prostřednictvím úlohy Azure Stream Analytics.

[Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skriptu pro tento úkol dělení je ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje dvě aktivity:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) pomocí aktivity [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , která spouští skript Hive k agregaci hodinové data o poptávce historie transformovny úrovni každou hodinu úrovni oblasti a put ve službě Azure Storage během Azure Stream Analýza úloh
* [Kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, který přesouvá agregovaná data z Azure Storage blob do Azure SQL Database, které bylo zřízené jako součást instalace šablony řešení.

[Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skriptu pro tento úkol je ***AggregateDemandHistoryRegion.hql***.

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
Tyto [kanály](data-factory/concepts-pipelines-activities.md) obsahovat několik aktivit a jejichž konečný výsledek je Vyhodnocená předpovědí z experimentu Azure Machine Learning spojené s touto šablonou řešení. Jsou téměř shodné s tím rozdílem, každý z nich pouze zpracovává jiné oblasti, které se provádí různé RegionID předaný kanálu ADF a skript hive pro každou oblast.  
Aktivity obsažené v tomto kanálu jsou:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) pomocí aktivity [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , která spouští skript Hive k provádění agregací a konstruování nezbytné pro Azure Machine Learning experimentu. Skriptů Hive pro tuto úlohu jsou příslušné ***PrepareMLInputRegionX.hql***.
* [Kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, který přesouvá výsledky z [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) aktivitu pro jeden objekt blob Azure Storage, který může být přístupný [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivity.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitu, která volá experiment Azure Machine Learning, což vede k výsledky se umístit do jednoho objektu blob Azure Storage.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – [kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, který přesouvá výsledky experimentu služby Azure Machine Learning z příslušné ***MLScoringRegionXPipeline***do Azure SQL Database, které bylo zřízené jako součást instalace šablony řešení.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – [kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitu, která se provádí přesun dat agregované probíhající vyžádání z ***LoadHistoryDemandDataPipeline*** do Azure SQL Databáze, které bylo zřízené jako součást instalace šablony řešení.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyTopologyDataPipeline CopyRegionDataPipeline CopySubstationDataPipeline,*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – [kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitu, která se provádí přesun dat odkaz na oblast/transformovny/Topologygeo, které se nahrají do služby Azure Storage blob jako součást šablony řešení instalace do Azure SQL Database, které bylo zřízené jako součást instalace šablony řešení.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentovat použité pro tuto šablonu řešení poskytuje předpověď poptávky oblasti. Experiment je specifická pro datové sady spotřebované a proto vyžaduje změny nebo specifické pro data, která je uvedena v nahrazení.

## <a name="monitor-progress"></a>**Sledování průběhu**
Po spuštění generátor dat kanálu začne získat HYDRATOVANÝ a různých komponent řešení start aktivace do akce následující příkazy vydané službou Data Factory. Existují dva způsoby, jak můžete monitorovat kanál.

1. Zkontrolujte data z úložiště objektů Blob v Azure.

    Jedna z úloh Stream Analytics nezpracované příchozí data zapíše do úložiště objektů blob. Pokud kliknete na **úložiště objektů Blob v Azure** součásti vašeho řešení z obrazovky jste úspěšně nasadili řešení a klikněte na **otevřít** v pravém panelu trvá, abyste [Azure portál](https://portal.azure.com). Potom klikněte na **objekty BLOB**. V panelu Další uvidíte seznam kontejnerů. Klikněte na **"energysadata"**. V dalším panelu se zobrazí **"demandongoing"** složky. Ve složce rawdata uvidíte složky s názvy, například datum = 2016-01-28 atd. Pokud se zobrazí tyto složky, znamená to, že nezpracovaná data se úspěšně vygenerované v počítači a ukládají ve službě blob storage. Měli byste vidět soubory, které by měly být omezené velikosti v MB v těchto složkách.
2. Kontrola dat ze služby Azure SQL Database.

    Posledním krokem kanálu je zápis dat (například predikcím ze strojového učení) do služby SQL Database. Budete muset počkat maximálně dvě hodiny pro zobrazení dat ve službě SQL Database. Je možné sledovat, kolik dat je k dispozici ve službě SQL Database prostřednictvím [webu Azure portal](https://portal.azure.com/). Na panelu vlevo vyhledejte databází SQL![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) a klikněte na něj. Poté vyhledejte vaši databázi (to znamená demo123456db) a klikněte na něj. Na další stránce v části **"Připojení k databázi"** klikněte na tlačítko **"Spuštění příkazů jazyka Transact-SQL dotazy na vaši službu SQL database"**.

    Tady můžete kliknout na nový dotaz a dotaz na počet řádků (například "Vyberte count(*) z DemandRealHourly)" "s růstem databáze by měl zvýšit počet řádků v tabulce.)
3. Zkontrolujte data z řídicího panelu Power BI.

    Můžete nastavit řídicí panel Power BI kritickou cestu k monitorování nezpracované příchozí data. Postupujte podle pokynů v části "Řídicí panel Power BI".

## <a name="power-bi-dashboard"></a>**Řídicí panel Power BI**
### <a name="overview"></a>Přehled
Tato část popisuje, jak nastavit řídicí panel Power BI pro vizualizace dat v reálném čase ze služby Azure stream analytics (horká cesta), stejně jako prognózy výsledky z aplikace Azure machine learning (studená cesta).

### <a name="setup-hot-path-dashboard"></a>Nastavení řídicího panelu kritickou cestu
Následující kroky vás provedou jak vizualizace dat v reálném čase výstup z úlohy Stream Analytics, které byly vytvořeny v době nasazení řešení. A [Power BI online](http://www.powerbi.com/) účtu se vyžaduje k provedení následujících kroků. Pokud účet nemáte, můžete si [vytvořit](https://powerbi.microsoft.com/pricing).

1. Přidáte výstup Power BI v Azure Stream Analytics (ASA).

   * Budete muset postupovat podle pokynů v [Azure Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro streamovaná data v reálném čase viditelnost](stream-analytics/stream-analytics-power-bi-dashboard.md) nastavit výstup úlohy Azure Stream Analytics jako řídicí panel Power BI .
   * Vyhledejte úlohu stream analytics ve vaší [webu Azure portal](https://portal.azure.com). Název úlohy musí být: YourSolutionName + "streamingjob" + náhodné číslo + "asapbi" (to znamená demostreamingjob123456asapbi).
   * Přidáte výstup Power BI pro úlohu Azure Stream Analytics. Nastavte **výstup Alias** jako **"PBIoutput"**. Nastavte vaše **název datové sady** a **název tabulky** jako **"EnergyStreamData"**. Po přidání výstup, klikněte na tlačítko **"Start"** v dolní části stránky a spusťte úlohu Stream Analytics. Měli byste obdržet potvrzovací zpráva (například "spuštění úlohy stream analytics myteststreamingjob12345asablob bylo úspěšné").
2. Přihlaste se k [Power BI online](http://www.powerbi.com)

   * Na levém panelu, část datové sady v pracovním prostoru, byste měli vidět nové datové sady ukazující na levém panelu Power BI. Toto je streamovaných dat, které jste nasdíleli z Azure Stream Analytics v předchozím kroku.
   * Ujistěte se, ***vizualizace*** podokně je otevřený a je zobrazena na pravé straně obrazovky.
3. Vytvoření dlaždice "Vyžádání podle časového razítka":

   * Klikněte na datovou sadu **"EnergyStreamData"** na levém panelu části datové sady.
   * Klikněte na tlačítko **"Spojnicový graf"** ikonu ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Klikněte na tlačítko "EnergyStreamData" v **pole** panelu.
   * Klikněte na tlačítko **"Časové razítko"** a ujistěte se, že se zobrazí v části "OS". Klikněte na tlačítko **"Načítání"** a ujistěte se, že se zobrazí v části "Hodnoty".
   * Klikněte na tlačítko **Uložit** nahoře a název sestavy jako "EnergyStreamDataReport". Sestavy s názvem "EnergyStreamDataReport" se zobrazí v části sestavy v navigačním podokně na levé straně.
   * Klikněte na tlačítko **"PIN kód Visual"** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) ikonu v pravém horním rohu tomto spojnicovém grafu "Připnout na řídicí panel" okně může zobrazit si můžete vybrat řídicí panel. Vyberte "EnergyStreamDataReport" a pak klikněte na tlačítko "Pin".
   * Přesuňte ukazatel myši nad tuto dlaždici na řídicím panelu, klikněte na položku "upravit" ikonu v pravém horním rohu, chcete-li změnit její název jako "Vyžádání podle časového razítka"
4. Vytvořte další řídicí panel dlaždice založené na příslušné datové sady. Zobrazit poslední řídicí panel: ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Nastavení řídicího panelu studené cesty
V trase datového kanálu základní cílem je získat prognózu poptávky každé oblasti. Power BI se připojí ke službě Azure SQL database jako zdroj dat, kde jsou uloženy výsledky předpovědí.

> [!NOTE]
> 1) Trvá několik hodin shromažďovat dostatečně předpokládané výsledky pro řídicí panel. Doporučujeme, abyste že tento proces začněte 2 – 3 hodiny po něco generátor dat. (2) v tomto kroku kontrolu požadovaných součástí je ke stažení a instalaci tohoto bezplatného softwaru [Power BI desktopu](https://powerbi.microsoft.com/desktop).
>
>

1. Získáte přihlašovací údaje databáze.

   Potřebujete **databáze název serveru, název databáze, uživatelské jméno a heslo** před přechodem na další kroky. Tady jsou kroky a provede vás postupy je najít.

   * Jednou **"Azure SQL Database"** v šabloně řešení diagram změní na zelenou, klikněte na něj a potom klikněte na tlačítko **"Otevřená"**. Provádějí na webu Azure portal a také otevření stránky informace o vaší databáze.
   * Na stránce můžete najít oddíl "Databáze". Vypíše navýšení kapacity databáze, kterou jste vytvořili. Název databáze by měl být **"Váš název řešení náhodné číslo +"db""** (například "mytest12345db").
   * Klikněte na vaši databázi v nové lokalitě pop si panelu, můžete najít v horní části název vašeho databázového serveru. Název vašeho databázového serveru by měl být `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (například "mytest12345.database.windows.net,1433").
   * Vaše databáze **uživatelské jméno** a **heslo** jsou stejné jako uživatelské jméno a heslo zaznamenaných během nasazení řešení.
2. Aktualizovat zdroj dat souboru Power BI studené cesty

   * Ujistěte se, že máte nainstalovanou nejnovější verzi [Power BI desktopu](https://powerbi.microsoft.com/desktop).
   * V **"DemandForecastingDataGeneratorv1.0"** složky jste si stáhli, poklikejte na **"Power BI Template\DemandForecastPowerBI.pbix"** souboru. Počáteční vizualizace jsou založeny na fiktivní data. **Poznámka:** Pokud se zobrazí chybová zpráva, ujistěte se, že máte nainstalovanou nejnovější verzi Power BI Desktopu.

     Po otevření, horní části souboru, klikněte na tlačítko **upravit dotazy**. V lokalitě pop si okna, dvakrát klikněte na panel **'Source'** na pravém panelu.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * V lokalitě pop si okna nahradit **"Server"** a **"Databázi"** s vlastními názvy serveru a databáze a pak klikněte na tlačítko **"OK"**. Pro název serveru, je nutné zadat port 1433 (**YourSolutionName.database.windows.net, 1433**). Ignorujte upozornění, které se zobrazí na obrazovce.
   * V dalším pop si okno se zobrazí dvě možnosti, v levém podokně (**Windows** a **databáze**). Klikněte na tlačítko **"Databázi"**, vyplňte vaše **"Username"** a **"Password"** (Toto je uživatelské jméno a heslo, které jste zadali při prvním nasazení řešení a vytvoří Azure SQL database). V ***jaké úroveň, která se použije toto nastavení vyberte***, zaškrtněte možnost na úrovni databáze. Pak klikněte na tlačítko **"Připojit"**.
   * Jakmile budete s asistencí zpět na předchozí stránku, zavřete okno. Zpráva POP navýšení kapacity – klikněte na tlačítko **použít**. A konečně, klikněte na tlačítko **Uložit** tlačítko Uložit změny. Váš soubor Power BI má nyní navázalo se připojení k serveru. Pokud vaše vizualizace jsou prázdné, nezapomeňte že vymazat výběry na vizualizace, která bude vizualizovat všechna data kliknutím na ikonu gumy v pravém horním rohu legend. Pomocí tlačítka aktualizovat tak, aby odrážely nová data na vizualizace. Na začátku zobrazí jenom data počáteční hodnoty na vaše vizualizace jako služby data factory je naplánovaná aktualizace každé 3 hodiny. Po 3 hodiny by se zobrazit projeví ve svých vizualizacích při aktualizaci dat nových předpovědí.
3. (Volitelné) Publikovat na řídicím panelu studené cesty [Power BI online](http://www.powerbi.com/). Všimněte si, že tento krok potřebuje účet Power BI (nebo účet Office 365).

   * Klikněte na tlačítko **"Publikovat"** a několik sekund později se zobrazí okno zobrazení "Publikování do Power BI úspěch!" s zelená značka zaškrtnutí. Kliknutím na následující odkaz "Otevřít demoprediction.pbix v Power BI". Podrobné pokyny najdete v tématu [publikování z Power BI Desktopu](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Chcete-li vytvořit nový řídicí panel: klikněte na tlačítko **+** podepsat vedle **řídicí panely** části v levém podokně. Zadejte název "Ukázka Prognózování poptávky" pro tento nový řídicí panel.
   * Po otevření sestavy, klikněte na tlačítko ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) všechny vizualizace na řídicí panel připnout. Podrobné pokyny najdete v tématu [Připnutí dlaždice na řídicí panel Power BI ze sestavy](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Přejděte na stránku řídicího panelu a upravte velikost a umístění vašich vizualizací a upravit jejich názvy. Podrobné pokyny o tom, jak upravit dlaždice, najdete v tématu [úpravy dlaždice – Změna velikosti, přesunutí, přejmenování, kódu pin, odstranění, přidání hypertextového odkazu](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Tady je příklad řídicího panelu se k němu připnuté vizualizace studené cesty.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Volitelné) Naplánovat aktualizaci zdroje dat.

   * Chcete naplánovat aktualizaci dat, najeďte myší **EnergyBPI koncový** datovou sadu, klikněte na tlačítko ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) a klikněte na tlačítko **naplánovat aktualizaci**.
     **Poznámka:** Pokud se zobrazí upozornění masáž, klikněte na tlačítko **upravit přihlašovací údaje** a ujistěte se, že vaše přihlašovací údaje databáze jsou stejné jako je popsáno v kroku 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Rozbalte **naplánovat aktualizaci** oddílu. Zapněte "zachovávat aktuálnost dat".
   * Naplánujte aktualizaci na základě vašich potřeb. Další informace najdete v tématu [aktualizace dat v Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## <a name="how-to-delete-your-solution"></a>**Jak odstranit řešení**
Ujistěte se zastavit generátor dat, když aktivně nepoužíváte řešení jako provoz generátoru dat s sebou nese náklady vyšší náklady. Pokud ji nepoužíváte, odstraňte řešení. Odstraňuje se řešení odstraní všechny součásti, které jsou zřízené ve vašem předplatném při nasazení řešení. Odstranit řešení kliknutím na název vašeho řešení na levém panelu šablony řešení a klikněte na odstranit.

## <a name="cost-estimation-tools"></a>**Nástroje pro odhad nákladů**
Následující dva nástroje jsou k dispozici vám pomůže lépe pochopit celkové náklady, které jsou součástí běží Prognózování poptávky po energii šablonu řešení ve vašem předplatném:

* [Microsoft Azure Cost Estimator Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost Estimator nástroj (desktop)](https://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Potvrzení**
Tento článek je autorem je mezi odborníky přes data Yijing Svoboda a softwarový inženýr Qiu Min v Microsoftu.
