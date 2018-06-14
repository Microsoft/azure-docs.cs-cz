---
title: Požadovat prognózy v technické příručce energie | Microsoft Docs
description: Technické příručce do šablony řešení s Microsoft Cortana Intelligence pro vyžádání prognózy v energie.
services: cortana-analytics
documentationcenter: ''
author: yijichen
manager: ilanr9
editor: yijichen
ms.assetid: 7f1a866b-79b7-4b97-ae3e-bc6bebe8c756
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: inqiu;yijichen;ilanr9
ms.openlocfilehash: bb3520d36e4c34c752fe388f3126da285e2161cd
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "27600142"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Technické příručce k šabloně Cortana Intelligence řešení pro vyžádání prognózy v energie
## <a name="overview"></a>**Přehled**
Šablony řešení jsou navrženy pro urychlit proces vytváření ukázkové E2E nad Cortana Intelligence Suite. Šablonu nasazené zřídí vaše předplatné s nezbytné součásti Cortana Intelligence a vytvářet vztahy mezi. Je také doplňuje datovém kanálu s ukázkovými daty generováno z aplikace simulace data. Stáhnout simulátoru data z uvedeného odkazu a nainstalujte ji na místním počítači, naleznete v souboru readme.txt pokyny k použití simulátoru. Data vygenerovaná ze simulátoru hydráty datového kanálu a spuštění generování předpovědi strojové učení, které lze následně vizualizovat na řídicím panelu Power BI.

Šablona řešení naleznete [sem](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

Proces nasazení provede několik kroků nastavte přihlašovací údaje řešení. Ujistěte se, že záznam tyto přihlašovací údaje, jako je například název řešení, uživatelské jméno a heslo, které zadáte během nasazení.

Cílem tohoto dokumentu je vysvětlit referenční architektura a různé součásti, které jsou zřízené v rámci vašeho předplatného v rámci této šablony řešení. Dokument komunikuje také o tom, jak nahradit ukázková data vlastní, abyste mohli zobrazit statistiky nebo předpovědi od vás won data skutečná data. Kromě toho dokument hovoří o části šablony řešení, která by bylo potřeba změnit, pokud chcete přizpůsobit řešení s vlastními daty. Pokyny o tom, jak vytvořit řídicí panel Power BI pro tuto šablonu řešení najdete na konci.

## <a name="details"></a>**Podrobnosti**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Architektura vysvětlené
Při nasazení řešení, jsou aktivované různé služby Azure v rámci Cortana Analytics Suite (tedy centra událostí, Stream Analytics, HDInsight, Data Factory, Machine Learning, *atd*). Diagram architektury ukazuje na vysoké úrovni, jak se prognózy vyžádání šablona řešení energie sestavený ze začátku do konce. Tyto služby můžete prozkoumat klepnutím na ně v diagramu šablonu řešení vytvořené pomocí nasazení řešení. Následující části popisují každého jednotlivého.

## <a name="data-source-and-ingestion"></a>**Zdroj dat a přijímání**
### <a name="synthetic-data-source"></a>Zdroj dat syntetického
U této šablony se zdroj dat používaný generují z plochy aplikace, která můžete stáhnout a spustit místně po úspěšné nasazení. Pokyny ke stažení a instalaci této aplikace v panelu Vlastnosti po výběru prvního uzlu názvem simulátoru dat prognózy energie v diagramu šablonu řešení pro vás. Tuto aplikaci kanály [centra událostí Azure](#azure-event-hub) služby s datových bodů nebo událostí, které se používají ve zbývající části toku řešení.

Aplikace generování událostí naplní centra událostí Azure pouze tehdy, když je prováděna v počítači.

### <a name="azure-event-hub"></a>Azure Event Hub
[Centra událostí Azure](https://azure.microsoft.com/services/event-hubs/) služba je příjemce vstup syntetické zdroj dat popsané.

## <a name="data-preparation-and-analysis"></a>**Příprava dat a analýzy**
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) služby slouží k poskytování téměř v reálném čase analýzy vstupního datového proudu z [centra událostí Azure](#azure-event-hub) služby a publikovat výsledky do [Power BI](https://powerbi.microsoft.com) řídicí panel, jakož i všechny nezpracované příchozí události k archivaci [Azure Storage](https://azure.microsoft.com/services/storage/) služby pro pozdější zpracování pomocí [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) služby.

### <a name="hdinsight-custom-aggregation"></a>Vlastní HDInsight agregace
Služby Azure HDInsight slouží ke spouštění [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty (řízená Azure Data Factory) k poskytování agregace na nezpracované události, které byly archivovat pomocí služby Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) služba se používá (řízená Azure Data Factory) Chcete-li prognózy na budoucí spotřebu konkrétní oblasti zadané vstupy přijata.

## <a name="data-publishing"></a>**Publikování dat**
### <a name="azure-sql-database-service"></a>Azure SQL Database Service
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) služby se používá k ukládání (spravované službou Azure Data Factory) předpovědi přijatých službu Azure Machine Learning, která se využívá v [Power BI](https://powerbi.microsoft.com) řídicího panelu.

## <a name="data-consumption"></a>**Spotřeba dat**
### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) služby se používá k zobrazení řídicí panel, který obsahuje agregace poskytované [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) služby a také vyžádání prognózy výsledky uložené v [Azure SQL Databáze](https://azure.microsoft.com/services/sql-database/) které byly vytvořeny pomocí [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) služby. Návod, jak vytvořit řídicí panel Power BI pro tuto šablonu řešení naleznete v následující části.

## <a name="how-to-bring-in-your-own-data"></a>**Jak mají být předány svoje vlastní data**
Tato část popisuje, jak převést svoje vlastní data do Azure a oblasti, které by vyžadovaly změny data, která přepnutím do této architektury.

Není pravděpodobné, že žádné datové sady, které přepnutím odpovídá datová sada používá pro tuto šablonu řešení. Seznámení s daty a požadavky na jsou klíčové v tom, jak upravit tuto šablonu pro práci s vlastními daty. Pokud jsou nové službě Azure Machine Learning, můžete získat Úvod do něj s použitím v příkladu v [postup vytvoření prvního experimentu](machine-learning/studio/create-experiment.md).

Následující části popisují části šablony, která vyžaduje změny, pokud je zavedená nová datová sada.

### <a name="azure-event-hub"></a>Azure Event Hub
[Centra událostí Azure](https://azure.microsoft.com/services/event-hubs/) služba je obecný, tak, aby se data můžou být publikované rozbočovače ve formátu CSV nebo formátu JSON. Žádné speciální zpracování dojde v Centru událostí Azure, ale je důležité, že rozumíte data, která je dodáni do ní.

Tento dokument nepopisuje ingestovat data, ale jeden můžete snadno odesílat události nebo data do centra událostí Azure, pomocí [API centra událostí](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) služby slouží k poskytování téměř v reálném čase analytics čtení z datových proudů a odesíláním dat do libovolný počet zdrojů.

Vyžádání prognózy pro šablonu řešení energie dotaz Azure Stream Analytics tvořen dvěma poddotazy, každý náročné událostí ze služby Azure Event Hub jako vstupy a výstupy museli dvou různých umístěních. Tyto výstupy obsahovat jednu datovou sadu Power BI a jedno umístění úložiště Azure.

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) dotazu lze najít:

* Přihlášení k [portálu Azure](https://portal.azure.com/)
* Vyhledání úlohy stream analytics ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) které byly generovány při řešení byl nasazen. Jeden je pro předání dat do blob storage (například mytest1streaming432822asablob) a druhý je pro předání dat do Power BI (například mytest1streaming432822asapbi).
* Výběr

  * ***VSTUPY*** zobrazení dotazu vstup
  * ***DOTAZ*** zobrazíte samotný dotaz
  * ***VÝSTUPY*** k zobrazení různých výstupy

Informace o vytváření dotazů Azure Stream Analytics najdete v [referenční příručka Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx) na webu MSDN.

V tomto řešení se v rámci této šablony řešení poskytuje úlohy Azure Stream Analytics, které vyprodukuje datovou sadu s téměř v reálném čase analytics informace o příchozí datový proud na řídicí panel Power BI. Protože je implicitní znalosti o příchozí data formátu, tyto dotazy by bylo nutné změnit podle vaší formát data.

Jiné úlohy Azure Stream Analytics všechny výstupy [centra událostí](https://azure.microsoft.com/services/event-hubs/) události [Azure Storage](https://azure.microsoft.com/services/storage/) a proto vyžaduje žádné změnou bez ohledu na formát vaše data, protože informace o úplné události je streamování na úložiště.

### <a name="azure-data-factory"></a>Azure Data Factory
[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) služeb orchestruje přesouvání a zpracování dat. V vyžádání prognózy energie řešení šablony objektu pro vytváření dat se skládá z 12 [kanály](data-factory/concepts-pipelines-activities.md) , přesunout a zpracování dat pomocí různých technologií.

  Objekt pro vytváření dat může přístup otevřením uzlu služby Data Factory v dolní části diagram řešení šablony vytvořené pomocí nasazení řešení. Zobrazí objektu pro vytváření dat na portálu Azure. Pokud se zobrazí chyby v rámci vaší datové sady, můžete těch, které jsou z důvodu nasazení před generátor dat byla spuštěna pro vytváření dat ignorovat. Tyto chyby, nebudou bránit datovou továrnu nebude fungovat.

Tato část popisuje nezbytné [kanály](data-factory/concepts-pipelines-activities.md) a [aktivity](data-factory/concepts-pipelines-activities.md) součástí [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Následující obrázek je zobrazení diagramu řešení:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Pět kanálů tento objekt pro vytváření obsahovat [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty, které se používají k vytvoření oddílů a agregovat data. Pokud už jsme zmínili, skripty jsou umístěné v [Azure Storage](https://azure.microsoft.com/services/storage/) účet vytvořený během instalace. Jejich umístění je: demandforecasting\\\\skriptu\\\\hive\\ \\ (nebo name].blob.core.windows.net/demandforecasting https://[Your řešení).

Podobně jako [Azure Stream Analytics](#azure-stream-analytics-1) dotazy, [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty mají implicitní znalosti o příchozí data formátu, tyto dotazy by bylo potřeba změnit podle vaší formát dat a [konstruování](machine-learning/team-data-science-process/create-features.md) požadavky.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje jediné aktivity – [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) aktivity pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , která se spouští [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript pro agregační přenášené datovými proudy v vyžádání dat každých 10 sekund v úrovni transformovny každou hodinu oblast úroveň a umístí [Azure Storage](https://azure.microsoft.com/services/storage/) prostřednictvím úlohy Azure Stream Analytics.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript pro toto rozdělení úloh je ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje dvě aktivity:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) aktivity pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , spouští skript Hive a agregovat hodinové vyžádání data historie v úrovni transformovny každou hodinu oblast úroveň umístit do úložiště Azure během Azure Stream Úlohy Analytics
* [Kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, která přemísťuje agregovaná data z objektu blob úložiště Azure do Azure SQL Database, zřízenou jako součást instalace šablony řešení.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript pro tuto úlohu je ***AggregateDemandHistoryRegion.hql***.

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
Tyto [kanály](data-factory/concepts-pipelines-activities.md) obsahovat několik aktivit a jehož konečný výsledek je scored predikcím ze Azure Machine Learning experimentu přidružená k této šabloně řešení. Jsou téměř shodné s tím rozdílem, každý z nich zpracovává jenom jiné oblasti, která se právě provádí jiný RegionID předaná ADF kanálu a skriptu hive pro každou oblast.  
Aktivity obsažené v kanálu jsou:

* [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) aktivity pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , spouští skript Hive k provádění agregací a konstruování potřebné pro Azure Machine Learning experimentu. Hive skripty pro tuto úlohu se příslušné ***PrepareMLInputRegionX.hql***.
* [Kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, která přemísťuje výsledků [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) aktivitu do jediného objektu blob Azure Storage, který může být přístup [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivity.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivity, která volá experimentu Azure Machine Learning, což vede k výsledky se umístit do jediného objektu blob úložiště Azure.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje jediné aktivity - [kopie](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, která přechází z příslušné výsledky experimentu Azure Machine Learning ***MLScoringRegionXPipeline***do Azure SQL Database, zřízenou jako součást instalace šablony řešení.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje jediné aktivity - [kopie](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, která se přesouvají data agregované probíhající vyžádání z ***LoadHistoryDemandDataPipeline*** do Azure SQL Databáze, která zřízenou jako součást instalace šablony řešení.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
To [kanálu](data-factory/concepts-pipelines-activities.md) obsahuje jediné aktivity - [kopie](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, která přemísťuje referenční data oblasti nebo transformovny/Topologygeo, která se odešlou do objektu blob Azure Storage v rámci šablony řešení instalace do Azure SQL Database, zřízenou jako součást instalace šablony řešení.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentovat použité pro tuto šablonu řešení poskytuje předpovědi vyžádání oblasti. Experimentu je specifický pro datové sady spotřebované a proto vyžaduje úpravu nebo nahrazení specifické pro data, která je převeden do stavu v.

## <a name="monitor-progress"></a>**Monitorování průběhu**
Po spuštění generátor dat kanálu začne HYDRATOVANÝ získat a spustit různé komponenty řešení si to chtěl / do akce následující příkazy vydané službou Data Factory. Existují dva způsoby, jak můžete monitorovat kanál.

1. Zkontrolujte, zda data z Azure Blob Storage.

    Jeden z úlohy Stream Analytics zapíše nezpracovaná příchozí data do úložiště objektů blob. Pokud kliknete na **Azure Blob Storage** součásti vašeho řešení na obrazovce úspěšně nasadit řešení a pak klikněte na tlačítko **otevřete** v pravém panelu trvá, abyste [Azure portál](https://portal.azure.com). Potom klikněte na **objekty BLOB**. Další panelu zobrazí se seznam kontejnerů. Klikněte na **"energysadata"**. Další panelu se zobrazí **"demandongoing"** složky. V této složce rawdata zobrazit složky s názvy, například datum = 2016-01-28 atd. Pokud se zobrazí tyto složky, znamená to úspěšně je nezpracovaná data se vygenerované v počítači a uložené v úložišti objektů blob. Měli byste vidět soubory, které by měl mít konečné velikosti v MB v těchto složkách.
2. Zkontrolujte, zda data z databáze SQL Azure.

    Posledním krokem kanálu je zapisovat data (například predikcím ze strojového učení) do databáze SQL. Možná bude muset počkat maximálně dvě hodiny pro zobrazení dat v databázi SQL. Je možné sledovat, kolik dat je k dispozici ve vaší databázi SQL prostřednictvím [portál Azure](https://portal.azure.com/). V levém panelu, vyhledejte databází SQL![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) a klikněte na něj. Poté vyhledejte databázi (tj. demo123456db) a klepněte na ni. Na další stránce pod **"Připojení k vaší databázi"** klikněte na tlačítko **"Dotazy na spuštění Transact-SQL pro svoji databázi SQL"**.

    Zde můžete kliknutím na nový dotaz a dotaz pro počet řádků (například "Vyberte count(*) z DemandRealHourly)" s růstem databáze počet řádků v tabulce měli zvýšit.)
3. Zkontrolujte, zda data z řídicí panel Power BI.

    Řídicí panel Power BI aktivní trase můžete nastavit pro monitorování nezpracovaná příchozí data. Postupujte podle pokynů v části "Řídicí panel Power BI".

## <a name="power-bi-dashboard"></a>**Řídicí panel Power BI**
### <a name="overview"></a>Přehled
Tato část popisuje, jak nastavit řídicí panel Power BI vizualizovat data v reálném čase, ze služby Azure stream analytics (aktivní trase) a také prognózy výsledky z Azure machine learning (neaktivní trase).

### <a name="setup-hot-path-dashboard"></a>Instalační program aktivní trase řídicí panel
Následující kroky vás postupy k vizualizaci dat v reálném čase výstup z úlohy Stream Analytics, které byly vygenerovány v době nasazení řešení. A [Power BI online](http://www.powerbi.com/) účet je potřeba provést následující kroky. Pokud účet nemáte, můžete [vytvořit](https://powerbi.microsoft.com/pricing).

1. Přidáte výstup Power BI v Azure Stream Analytics (ASA).

   * Je nutné postupovat podle pokynů v [Azure Stream Analytics & Power BI: řídicí panel analýzy v reálném čase pro streamování dat v reálném čase viditelnost](stream-analytics/stream-analytics-power-bi-dashboard.md) nastavit výstup úlohy Azure Stream Analytics jako řídicí panel Power BI .
   * Vyhledejte úlohy stream analytics v vaše [portál Azure](https://portal.azure.com). Název úlohy musí být: YourSolutionName + "streamingjob" + náhodné číslo + "asapbi" (tj. demostreamingjob123456asapbi).
   * Přidání PowerBI výstupu úlohy ASA. Nastavte **výstup Alias** jako **'PBIoutput'**. Nastavit vaše **název datové sady** a **název tabulky** jako **'EnergyStreamData'**. Po přidání výstup, klikněte na tlačítko **"Start"** v dolní části stránky při spuštění úlohy Stream Analytics. Měli byste obdržet zprávu s potvrzením (například "spouštění úlohy stream analytics myteststreamingjob12345asablob bylo úspěšné").
2. Přihlaste se k [Power BI online](http://www.powerbi.com)

   * Na levém panelu, část datové sady v části pracovní prostor, byste měli vidět nová datová sada zobrazuje na levém panelu Power BI. Toto je dat, která se poslat ze služby Azure Stream Analytics v předchozím kroku.
   * Zajistěte, aby ***vizualizace*** podokně je otevřený a je zobrazena na pravé straně obrazovky.
3. Dlaždice "Vyžádání podle časového razítka" vytvoření:

   * Klikněte na datovou sadu **'EnergyStreamData'** na levém panelu části datové sady.
   * Klikněte na tlačítko **"Spojnicový graf"** ikonu ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Klikněte na tlačítko 'EnergyStreamData' v **pole** panelu.
   * Klikněte na tlačítko **"Časové razítko"** a zajistěte, aby se zobrazí v části "Osou". Klikněte na tlačítko **"Zatížení"** a zajistěte, aby se zobrazí v části "Hodnoty".
   * Klikněte na tlačítko **Uložit** nahoře a název sestavy jako "EnergyStreamDataReport". Sestavy s názvem "EnergyStreamDataReport" se zobrazí v části sestavy v podokně Navigátor na levé straně.
   * Klikněte na tlačítko **"Pin Visual"** ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) ikonu v pravém horním rohu tento spojnicový graf, okno "Kód Pin pro řídicí panel" může zobrazovat můžete vybrat řídicí panel. Vyberte "EnergyStreamDataReport" a potom klikněte na tlačítko "Pin".
   * Najeďte myší nad tuto dlaždici na řídicím panelu klikněte na "upravit" ikonu v pravém horním rohu, chcete-li změnit její název jako "Vyžádání podle časového razítka"
4. Vytvořte další dlaždice řídicího panelu podle příslušné datové sady. Zobrazení řídicího panelu poslední: ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Instalační program neaktivní trase řídicí panel
V datovém kanálu neaktivní trase nezbytné cílem je získat vyžádání předpovězené každou oblast. Power BI se připojí k Azure SQL database jako svůj zdroj dat, kde jsou uložené výsledky předpovědi.

> [!NOTE]
> 1) Jak dlouho trvá několik hodin ke shromažďování dostatečně prognózy výsledky pro řídicí panel. Doporučujeme, aby že tento proces zahájíte 2 – 3 hodiny po něco generátor data. 2) v tomto kroku předpokladů je ke stažení a instalaci bezplatný software [Power BI desktop](https://powerbi.microsoft.com/desktop).
>
>

1. Získáte přihlašovací údaje databáze.

   Je třeba **databáze název serveru, název databáze, uživatelské jméno a heslo** než přejdete k dalším krokům. Tady jsou kroky pro pokyny, jak je vyhledat.

   * Jednou **"Azure SQL Database"** v šabloně řešení změní zelená diagramu, klikněte na něj a potom klikněte na **"Otevřené"**. Jsou základě k portálu Azure a také otevření stránky informace o vaší databáze.
   * Na stránce můžete najít oddíl "Databáze". Zobrazí seznam na databázi, kterou jste vytvořili. Název databáze musí být **"Váš název řešení + náhodné číslo +"db""** (například "mytest12345db").
   * Klikněte na vaši databázi v nové pop na panelu, můžete najít název serveru databáze v horní části. Název serveru databáze by měla být `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (například "mytest12345.database.windows.net,1433").
   * Vaše databáze **uživatelské jméno** a **heslo** jsou stejné jako uživatelské jméno a heslo dříve zaznamenaných během nasazení řešení.
2. Aktualizovat zdroj dat souboru neaktivní trase Power BI

   * Ujistěte se, že máte nainstalovanou nejnovější verzi [Power BI desktop](https://powerbi.microsoft.com/desktop).
   * V **"DemandForecastingDataGeneratorv1.0"** složky jste stáhli, dvakrát klikněte **'Template\DemandForecastPowerBI.pbix Power BI.** souboru. Počáteční vizualizacemi jsou založené na fiktivní data. **Poznámka:** Pokud se zobrazí chybová zpráva, ujistěte se, že máte nainstalovanou nejnovější verzi Power BI Desktop.

     Jakmile otevřete, horní soubor, klikněte na tlačítko **'upravit dotazy**. V okně serveru pop, klikněte dvakrát na **'Source'** na pravém panelu.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * V okně pop, nahraďte **"Server"** a **"Databáze"** s vlastními názvy serveru a databáze a pak klikněte na **"OK"**. Pro název serveru, je nutné zadat port 1433 (**YourSolutionName.database.windows.net, 1433**). Ignorujte upozornění, které se zobrazují na obrazovce.
   * V dalším okně pop, uvidíte dvě možnosti, v levém podokně (**Windows** a **databáze**). Klikněte na tlačítko **"Databáze"**, vyplňte vaše **"Username"** a **"Password"** (Toto je uživatelské jméno a heslo, které jste zadali při prvním nasazení řešení a vytvořit Azure Databáze SQL). V ***vyberte, kterou úroveň použít tato nastavení***, zkontrolujte úrovně možnost databáze. Pak klikněte na tlačítko **"Připojit"**.
   * Jakmile jste se na základě zpět na předchozí stránku, zavřete okno. Zpráva POP kapacity – klikněte na tlačítko **použít**. Nakonec kliknutím **Uložit** tlačítko a uložte změny. Souboru Power BI je nyní navázat připojení k serveru. Pokud vaše vizualizace jsou prázdná, zkontrolujte, zda že vymazat výběry na vizualizaci, která bude vizualizovat všechna data kliknutím na ikonu mazání v pravém horním rohu legend. Použijte tlačítko Aktualizovat, aby odpovídala nová data v vizualizacemi. Na začátku jenom zobrazit počáteční hodnoty data na vaše vizualizace jako objekt pro vytváření dat je naplánovaná aktualizace každých 3 hodiny. Po 3 hodiny uvidíte nový předpovědi promítnuta vaší vizualizace při aktualizaci data.
3. (Volitelné) Publikovat k řídicímu neaktivní trase [Power BI online](http://www.powerbi.com/). Všimněte si, že tento krok vyžaduje účet Power BI (nebo účtu Office 365).

   * Klikněte na tlačítko **"Publikovat"** a později několik sekund zobrazí se okno zobrazení "Publikování Power BI úspěchu!" s zelená značka zaškrtnutí. Kliknutím na následující odkaz "Otevřete demoprediction.pbix v Power BI". Podrobné pokyny najdete v části [publikování z Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Chcete-li vytvořit nový řídicí panel: klikněte na tlačítko **+** přihlásit do **řídicí panely** část v levém podokně. Zadejte název "Vyžádání prognózy ukázku" pro tento nový řídicí panel.
   * Jakmile otevřete sestavu, klikněte na tlačítko ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) připnete všemi vizualizacemi na řídicí panel. Podrobné pokyny najdete v části [dlaždici připnout na řídicí panel Power BI ze sestavy](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Přejděte na stránku řídicího panelu a upravit velikost a umístění vaší vizualizace a upravit jejich názvy. Podrobné pokyny o tom, jak upravit dlaždice naleznete v tématu [Upravit vedle sebe - změny velikosti, přesunout, přejmenovat, kódu pin, odstranit, přidání hypertextového odkazu](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Zde je příklad řídicí panel s vizualizacemi některé neaktivní trase připnuli k němu.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Volitelné) Plán aktualizace zdroje dat.

   * Chcete naplánovat aktualizaci dat, umístěte ukazatel myši nad **EnergyBPI konečné** datovou sadu, klikněte na tlačítko ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) a potom zvolte **naplánovat aktualizaci**.
     **Poznámka:** Pokud se zobrazí upozornění masáž, klikněte na tlačítko **upravit pověření** a zkontrolujte, zda vaše přihlašovací údaje databáze jsou stejné jako popsané v kroku 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Rozbalte **naplánovat aktualizaci** části. Zapněte "zachovávat aktuálnost dat".
   * Naplánujte aktualizace na základě potřeb. Další informace naleznete v tématu [aktualizace dat v Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## <a name="how-to-delete-your-solution"></a>**Postup odstranění řešení**
Ujistěte se, můžete zastavit generátor dat, když není aktivně pomocí řešení, jako spuštění generátoru dat způsobuje vyšší náklady. Pokud ji nepoužíváte, odstraňte řešení. Odstraňování řešení odstraní všechny součásti, které jsou zřízené v rámci vašeho předplatného, při nasazení řešení. Chcete-li odstranit řešení klikněte na název vašeho řešení na levém panelu řešení šablonu a klikněte na odstranit.

## <a name="cost-estimation-tools"></a>**Náklady odhad nástroje**
Tyto dva nástroje jsou k dispozici vám pomůžou lépe pochopit celkové náklady spojené spuštěna vyžádání prognózy šablona řešení energie ve vašem předplatném:

* [Nástroj odhadu náklady na Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure náklady odhadu Tool (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Potvrzení**
Tento článek je dílem vědecký data pracovník Svoboda Yijing a softwaru pracovníka Qiu Min v Microsoftu.
