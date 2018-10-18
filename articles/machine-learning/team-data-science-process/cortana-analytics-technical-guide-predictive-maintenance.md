---
title: Prediktivní údržby v leteckém průmyslu s Azure – technický průvodce řešení Cortana Intelligence | Dokumentace Microsoftu
description: Technický průvodce do šablony řešení s Microsoft Cortana Intelligence pro účely prediktivní údržby v leteckém průmyslu, nástroje a doprava.
services: machine-learning
documentationcenter: ''
author: fboylu
manager: cgronlun
editor: cgronlun
ms.assetid: 2c4d2147-0f05-4705-8748-9527c2c1f033
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: fboylu
ms.openlocfilehash: 04e0a694d3e8d978a21417e728feabf32f85299f
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394591"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Šablony řešení Cortana Intelligence pro účely prediktivní údržby v leteckém průmyslu a další provozovny – technický průvodce

>[!Important]
Tento článek je zastaralá. Diskuze o prediktivní údržby v leteckém průmyslu je stále relevantní, ale aktuální informace, přečtěte si [přehled řešení pro firmy cílovým skupinám na místě](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Šablony řešení jsou určeny ke zrychlení procesu vytváření ukázku E2E nad Cortana Intelligence Suite. Se nasadila šablona zřídí předplatnému pomocí komponenty potřebné Cortana Intelligence a poté sestaví vztahy mezi nimi. Nasazení také nasazuje datový kanál s ukázkovými daty z aplikace generátor dat, který si stáhnete a nainstalujete na místním počítači po nasazení šablony řešení. Data z generátor hydráty datového kanálu a spuštění generování predikcí strojového učení, které lze následně vizualizovat na řídicím panelu Power BI.

Proces nasazení provede několik kroků pro nastavení přihlašovacích údajů vašeho řešení. Zajistěte, aby že záznam přihlašovací údaje, jako je například název řešení, uživatelské jméno a heslo, které zadáte během nasazení. 


Cíle tohoto článku je:
- Popište referenční architektura a komponenty, které jsou zřízené v rámci vašeho předplatného.
- Ukazují, jak nahradit ukázkových dat s vlastními daty. 
- Ukazují, jak upravit šablonu řešení.  

> [!TIP]
> Můžete stáhnout a vytisknout [PDF verzi tohoto článku](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Přehled
![Architektura prediktivní údržby](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Když nasadíte řešení, aktivuje služeb Azure v sadě Cortana Analytics Suite (včetně Event Hub, Stream Analytics, HDInsight, objekt pro vytváření dat a Machine Learning). Diagram architektury ukazuje, jak vytvořit prediktivní Údržba leteckých šablony řešení. Můžete prozkoumat tyto služby na webu Azure Portal kliknutím v diagramu šablonu řešení vytvořili s nasazením řešení (s výjimkou HDInsight, které je zřízená na vyžádání, když jsou potřeba ke spouštění aktivit souvisejících kanálu a jsou Odstranit později).
Stáhněte si [reklamy verze diagramu](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Následující části popisují části řešení.

## <a name="data-source-and-ingestion"></a>Zdroj dat a přijímání
### <a name="synthetic-data-source"></a>Zdroj dat syntetického
Pro tuto šablonu je použitý zdroj dat generovaných desktopová aplikace, stáhněte a spusťte místně po úspěšném nasazení.

K vyhledání pokynů ke stažení a instalace této aplikace, vyberte první uzel generátor dat prediktivní údržby, v diagramu šablonu řešení. Pokyny jsou součástí na panelu Vlastnosti. Tato aplikace se předají [Azure Event Hubs](#azure-event-hub) služba s datové body nebo události, použita ve zbytku toku řešení. Tento zdroj dat je odvozen z veřejně dostupných dat z [úložiště dat NASA](https://c3.nasa.gov/dashlink/resources/139/) pomocí [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Generování aplikace události naplní Azure Event Hubs pouze tehdy, když je spuštěn v počítači.  

### <a name="azure-event-hub"></a>Centrum událostí Azure  
[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) příjemce vstup syntetické zdrojem dat je služba.

## <a name="data-preparation-and-analysis"></a>Příprava dat a analýza  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Použití [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) k poskytování téměř v reálném čase analýzy vstupního datového proudu z [Azure Event Hubs](#azure-event-hub) služby. Potom publikovat výsledky do [Power BI](https://powerbi.microsoft.com) i archivovat vše nezpracované příchozí události do řídicího panelu [služby Azure Storage](https://azure.microsoft.com/services/storage/) service pro pozdější zpracování [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)služby.

### <a name="hdinsight-custom-aggregation"></a>Vlastní agregační HDInsight
Spustit [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skriptů (orchestrovaných službou Azure Data Factory) pomocí HDInsight poskytuje agregace nezpracovaných událostí archivovaných pomocí služby Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Vytvoření predikcí zbývající životnosti (RUL) konkrétního leteckého motoru pomocí vstupů obdrželi s portálem [služby Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (orchestrovaných službou Azure Data Factory). 

## <a name="data-publishing"></a>Publikování dat
### <a name="azure-sql-database"></a>Azure SQL Database
Použití [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) k ukládání předpovědí přijaté službou Azure Machine Learning, který se následně používají na [Power BI](https://powerbi.microsoft.com) řídicího panelu.

## <a name="data-consumption"></a>Využití dat
### <a name="power-bi"></a>Power BI
Použití [Power BI](https://powerbi.microsoft.com) zobrazíte řídicí panel, který obsahuje agregace a poskytuje výstrahy [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), stejně jako predikcí zbývající doby životnosti uložených v [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) které byly vytvořeny pomocí [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Jak umožňuje přinést si vlastní data
Tato část popisuje, jak přineste si vlastní data do Azure a oblasti, které vyžadují změny dat, než převedete do této architektury.

Není pravděpodobné, že vaše datová sada odpovídá datové sady používané [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) použít pro tuto šablonu řešení. Vysvětlení dat a požadavky jsou zásadní v tom, jak změnit tato šablona fungovala s vlastními daty. 

Následující části popisují části šablony, které vyžadují změny, když je zavedená nová datová sada.

### <a name="azure-event-hub"></a>Centrum událostí Azure
Centrum událostí Azure je obecný; data mohou být účtovány do centra ve formátu CSV nebo JSON. Neproběhne žádné speciální zpracování ve službě Azure Event Hub, ale je důležité pochopit, data, která se zobrazí na něj.

Tento dokument nepopisuje přijímat data, ale můžete snadno odesílat události nebo data do centra událostí Azure pomocí rozhraní API centra událostí.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Pomocí služby Azure Stream Analytics poskytuje téměř v reálném čase analýzy čtení z datových proudů a výstup dat do libovolného počtu zdrojů.

Prediktivní Údržba leteckých šablony řešení dotazu Azure Stream Analytics se skládá z čtyři sub dotazy, každý dotaz spotřebovávajících událostí ze služby Azure Event Hubs, se výstupy do čtyř různých umístění. Tyto výstupy se skládají ze tří datových sad Power BI a jednoho umístění úložiště Azure.

Dotaz Azure Stream Analytics můžete zobrazit tak:

* Připojte se k webu Azure portal
* Vyhledání úlohy Stream Analytics ![Stream Analytics ikonu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) , které byly generovány při řešení bylo nasazeno (*například*, **maintenancesa02asapbi** a **maintenancesa02asablob** pro řešení prediktivní údržby)
* Výběr
  
  * ***VSTUPY*** o dotazu vstup
  * ***DOTAZ*** zobrazíte samotný dotaz
  * ***VYPÍŠE*** Chcete-li zobrazit různé výstupy

Informace o Azure Stream Analytics konstrukce dotazů najdete v [referenční příručka k Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx) na webové stránce MSDN.

Dotazy v tomto řešení výstupní tři datové sady s téměř v reálném čase analýzy informace o příchozí datový proud na řídicí panel Power BI poskytuje jako součást Tato šablona řešení. Protože implicitní znalosti o formátu příchozí data se tyto dotazy musí být změněn podle datový formát.

Dotaz v druhé úloze Stream Analytics **maintenancesa02asablob** jednoduše vypíše všechny [centra událostí](https://azure.microsoft.com/services/event-hubs/) události [služby Azure Storage](https://azure.microsoft.com/services/storage/) a proto nevyžaduje žádné změny bez ohledu na formát dat jako celá událost informace Streamovat do úložiště.

### <a name="azure-data-factory"></a>Azure Data Factory
[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) přesouvání a zpracování dat zorganizuje služba. V prediktivní Údržba leteckých šablony řešení, služby data factory se skládá ze tří [kanály](../../data-factory/concepts-pipelines-activities.md) , přesouvat a zpracovávat data pomocí různých technologií.  Přístup k vaší datové továrny tak, že otevřete uzel služby Data Factory v dolní části diagramu šablony řešení vytvořené pomocí nasazení řešení. Chyby v rámci vaší datové sady z důvodu služby data factory se nasazují předtím, než byl spuštěn generátor dat. Tyto chyby můžete ignorovat a nezabrání svou datovou továrnu funguje

![Chyby datové sady data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Tato část pojednává o nezbytné [kanály a aktivity](../../data-factory/concepts-pipelines-activities.md) součástí [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Zde je diagram tohoto řešení.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dva kanály tento objekt pro vytváření obsahovat [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty používané k rozdělení a agregovat data. Pokud jste si poznamenali, skripty jsou umístěny v [služby Azure Storage](https://azure.microsoft.com/services/storage/) účet vytvořený během instalace. Jejich umístění je: maintenancesascript\\\\skript\\\\hive\\ \\ (nebo name].blob.core.windows.net/maintenancesascript https://[Your řešení).

Podobně jako [Azure Stream Analytics](#azure-stream-analytics-1) dotazy, [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty mají implicitní znalosti o příchozích dat formátu a musí být změněn podle datový formát.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
To [kanálu](../../data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) pomocí aktivity [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , který běží [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript pro rozdělení dat vložte [služby Azure Storage](https://azure.microsoft.com/services/storage/) během [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) úlohy.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skriptu pro tento úkol dělení je ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
To [kanálu](../../data-factory/concepts-pipelines-activities.md) obsahuje několik aktivit, jejichž konečný výsledek je Vyhodnocená předpovědí z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment přidružená k této šabloně řešení.

Aktivity zahrnuté jsou:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivity pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , který běží [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skriptů k provádění agregací a konstruování nezbytné pro [počítači Azure Učení](https://azure.microsoft.com/services/machine-learning/) experimentovat.
  [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skriptu pro tento úkol dělení je ***PrepareMLInput.hql***.
* [Kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, který přesouvá výsledků [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivit do jediné [služby Azure Storage](https://azure.microsoft.com/services/storage/) blob přistupuje [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivita.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) volání aktivity [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentu s výsledky do jediného [služby Azure Storage](https://azure.microsoft.com/services/storage/) objektů blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
To [kanálu](../../data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – [kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, který přesouvá výsledky [Azure Machine Learning](#azure-machine-learning) experiment z  ***MLScoringPipeline*** k [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) zřízeným jako součást instalace šablony řešení.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentovat použité pro tuto šablonu řešení poskytuje zbývající dobu životnosti (RUL) leteckého motoru. Experiment je specifická pro datové sady spotřebované a vyžaduje úpravy nebo nahrazení specifické pro data režimu.

Informace o způsobu vytvoření experimentu Azure Machine Learning najdete v tématu [prediktivní údržba: Krok 1 ze 3, přípravy dat a vytváření funkcí](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>Sledování průběhu
Po spuštění generátor dat kanálu začne nechávají dehydratovat a různých komponent řešení start aktivace do akce následující příkazy vydané službou data factory. Existují dva způsoby a začněte monitorovat kanál.

1. Jedna z úloh Stream Analytics nezpracované příchozí data zapíše do úložiště objektů blob. Pokud kliknete na úložiště objektů Blob součást řešení z obrazovky jste úspěšně nasadili řešení a klikněte v pravém panelu na možnost otevřít, tím přejdete [webu Azure portal](https://portal.azure.com/). Potom klikněte na objekty BLOB. V panelu Další uvidíte seznam kontejnerů. Klikněte na **maintenancesadata**. V dalším je panel **rawdata** složky. Ve složce rawdata jsou složky s názvy, například hodiny = 17 a hodina = 18. Označuje výskyt těchto složek nezpracovaných dat se vygeneruje ve vašem počítači a ukládají ve službě blob storage. Soubory csv s konečnou velikost v MB v těchto složkách, byste měli vidět.
2. Posledním krokem kanálu je zápis dat (třeba predikcím ze strojového učení) do služby SQL Database. Budete muset počkat maximálně tři hodiny pro zobrazení dat ve službě SQL Database. Je možné sledovat, kolik dat je k dispozici ve službě SQL Database prostřednictvím [webu Azure portal](https://portal.azure.com/). Na panelu vlevo vyhledejte databází SQL ![SQL ikonu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) a klikněte na něj. Vyhledejte vaši databázi **pmaintenancedb** a klikněte na něj. Na další stránce v dolní části klikněte na tlačítko na MANAGE
   
    ![Ikona Správa](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    Tady můžete kliknout na nový dotaz a dotaz na počet řádků (například vyberte count(*) z PMResult). S růstem databáze, měli byste zvýšit počet řádků v tabulce.

## <a name="power-bi-dashboard"></a>Řídicí panel Power BI

Nastavte řídicí panel Power BI k vizualizaci dat Azure Stream Analytics (horká cesta) a batch výsledky předpovědí z aplikace Azure machine learning (studená cesta).

### <a name="set-up-the-cold-path-dashboard"></a>Nastavit řídicí panel studené cesty
V kanálu dat studené cesty je cílem získat prediktivní RUL (zbývající doba životnosti) každý motor letadla po dokončení letu (cyklu). Předpověď výsledků je aktualizována každé 3 hodiny pro predikci letecké motory, které dokončení let během poslední 3 hodiny.

Power BI se připojí ke službě Azure SQL database jako zdroj dat, kde jsou uloženy výsledky předpovědí. Poznámka: 1) pro nasazení řešení, predikcí se zobrazí v databázi během 3 hodin.
Souboru pbix, která byla součástí ke stažení generátor obsahuje počáteční hodnoty data, takže můžete hned vytvořit řídicí panel Power BI. (2) v tomto kroku kontrolu požadovaných součástí je ke stažení a instalaci tohoto bezplatného softwaru [Power BI desktopu](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Následující kroky vás o tom, jak připojit soubor pbix do SQL Database, která byla spuštěné v době nasazení řešení obsahující data (například výsledky předpovědí) za účelem vizualizace.

1. Získáte přihlašovací údaje databáze.
   
   Budete potřebovat **databáze název serveru, název databáze, uživatelské jméno a heslo** před přechodem na další kroky. Tady jsou kroky a provede vás postupy je najít.
   
   * Jednou **"Azure SQL Database"** v šabloně řešení diagram změní na zelenou, klikněte na něj a potom klikněte na tlačítko **"Otevřít"**.
   * Zobrazí se vám nové kartě nebo okno prohlížeče zobrazující stránky Azure portal. Klikněte na tlačítko **"Skupiny prostředků"** na levém panelu.
   * Vyberte předplatné, který používáte pro nasazení řešení a pak vyberte **"YourSolutionName\_ResourceGroup"**.
   * V nové lokalitě pop si panelu, klikněte na tlačítko ![SQL ikonu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikonu pro přístup k vaší databázi. Název databáze je vedle této ikony (například **"pmaintenancedb"**) a **název databázového serveru** je uvedený v části vlastnosti název serveru a by měl vypadat podobně jako  **YourSoutionName.database.windows.net**.
   * Vaše databáze **uživatelské jméno** a **heslo** jsou stejné jako uživatelské jméno a heslo zaznamenaných během nasazení řešení.
2. Aktualizujte zdroj dat ze studené cesty souboru sestavy v Power BI Desktopu.
   
   * Ve složce, kam jste stáhli a odblokujte ho generátor, dvakrát klikněte **PowerBI\\PredictiveMaintenanceAerospace.pbix** souboru. Pokud všechny zprávy upozornění se zobrazí při otevření souboru, je ignorujte. V horní části souboru, klikněte na tlačítko **upravit dotazy**.
     
     ![Upravit dotazy](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Zobrazí se vám dvou tabulek, **RemainingUsefulLife** a **PMResult**. Vyberte v první tabulce a klikněte na tlačítko ![ikona nastavení dotazu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) vedle **'Source'** pod **použitý postup** na pravé straně **"Nastavení dotazu"** panel. Ignorujte jakékoli upozornění, které se zobrazí.
   * V lokalitě pop si okna nahradit **"Server"** a **'Databáze'** s vlastními názvy serveru a databáze a pak klikněte na tlačítko **"OK"**. Pro název serveru, je nutné zadat port 1433 (**YourSoutionName.database.windows.net, 1433**). Ponechejte pole databáze jako **pmaintenancedb**. Ignorujte upozornění, které se zobrazí na obrazovce.
   * V dalším pop si okno se zobrazí dvě možnosti, v levém podokně (**Windows** a **databáze**). Klikněte na tlačítko **'Databáze'**, vyplňte vaše **'Jméno uživatele'** a **'Password'** (Toto je uživatelské jméno a heslo, které jste zadali při prvním nasazení řešení a vytvoří Azure SQL database). V ***jaké úroveň, která se použije toto nastavení vyberte***, zaškrtněte možnost na úrovni databáze. Pak klikněte na tlačítko **"Připojit"**.
   * Klikněte na druhé tabulky **PMResult** klikněte ![ikona navigace](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) vedle **'Source'** pod **použitý postup** na pravé straně **"Nastavení dotazu"** panelu a aktualizujte název serveru a databáze jako výše uvedené kroky a klikněte na tlačítko OK.
   * Jakmile budete s asistencí zpět na předchozí stránku, zavřete okno. Zobrazí se zpráva – klikněte na tlačítko **použít**. A konečně, klikněte na tlačítko **Uložit** tlačítko Uložit změny. Váš soubor Power BI má nyní navázalo se připojení k serveru. Pokud vaše vizualizace jsou prázdné, nezapomeňte že vymazat výběry na vizualizace, která bude vizualizovat všechna data kliknutím na ikonu gumy v pravém horním rohu legend. Pomocí tlačítka aktualizovat tak, aby odrážely nová data na vizualizace. Na začátku zobrazí jenom data počáteční hodnoty na vaše vizualizace jako služby data factory je naplánovaná aktualizace každé 3 hodiny. Po 3 hodiny zobrazí se nových předpovědí projeví ve svých vizualizacích při aktualizaci data.
3. (Volitelné) Publikovat na řídicím panelu studené cesty [Power BI online](http://www.powerbi.com/). Všimněte si, že tento krok potřebuje účet Power BI (nebo účet Office 365).
   
   * Klikněte na tlačítko **"Publikovat"** a několik sekund později se zobrazí okno zobrazení "Publikování do Power BI úspěch!" s zelená značka zaškrtnutí. Klikněte na odkaz níže "Otevřít PredictiveMaintenanceAerospace.pbix v Power BI". Podrobné pokyny najdete v tématu [publikování z Power BI Desktopu](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Chcete-li vytvořit nový řídicí panel: klikněte na tlačítko **+** podepsat vedle **řídicí panely** části v levém podokně. Zadejte název "Ukázka prediktivní údržby" pro tento nový řídicí panel.
   * Po otevření sestavy, klikněte na tlačítko ![ikonu PŘIPÍNÁČKU](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) všechny vizualizace na řídicí panel připnout. Podrobné pokyny najdete v tématu [Připnutí dlaždice na řídicí panel Power BI ze sestavy](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Přejděte na stránku řídicího panelu a upravte velikost a umístění vašich vizualizací a upravit jejich názvy. Podrobné pokyny o tom, jak upravit dlaždice, najdete v tématu [úpravy dlaždice – Změna velikosti, přesunutí, přejmenování, kódu pin, odstranění, přidání hypertextového odkazu](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Tady je příklad řídicího panelu se k němu připnuté vizualizace studené cesty.  V závislosti na tom, jak dlouho spuštění vaší generátor dat se může lišit čísla na vizualizace.
     <br/>
     ![Poslední zobrazení](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Chcete naplánovat aktualizaci dat, najeďte myší **PredictiveMaintenanceAerospace** datovou sadu, klikněte na tlačítko ![ikonu tří teček](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) a klikněte na tlačítko **naplánovat aktualizaci**.
     <br/>
     **Poznámka:** Pokud se zobrazí upozornění masáž, klikněte na tlačítko **upravit přihlašovací údaje** a ujistěte se, že vaše přihlašovací údaje databáze jsou stejné jako je popsáno v kroku 1.
     <br/>
     ![Aktualizace plánu.](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Rozbalte **naplánovat aktualizaci** oddílu. Zapněte "zachovávat aktuálnost dat".
     <br/>
   * Naplánujte aktualizaci na základě vašich potřeb. Další informace najdete v tématu [aktualizace dat v Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Nastavení řídicího panelu kritickou cestu
Následující kroky vás provedou, jak vizualizovat výstup dat z úloh Stream Analytics, které byly vytvořeny v době nasazení řešení. A [Power BI online](http://www.powerbi.com/) účtu se vyžaduje k provedení následujících kroků. Pokud účet nemáte, můžete si [vytvořit](https://powerbi.microsoft.com/pricing).

1. Přidáte výstup Power BI v Azure Stream Analytics (ASA).
   
   * Postupujte podle pokynů v [Azure Stream Analytics a Power BI: analytickém řídicím panelu pro streamování dat v reálném čase viditelnost](../../stream-analytics/stream-analytics-power-bi-dashboard.md) nastavit výstup úlohy Azure Stream Analytics jako řídicí panel Power BI.
   * Dotaz Azure Stream Analytics obsahuje tři výstupy, které jsou **aircraftmonitor**, **aircraftalert**, a **flightsbyhour**. Zobrazí se dotaz po kliknutí na kartě dotazu. Odpovídající každé z těchto tabulek, musíte přidat výstup Azure Stream Analytics. Když přidáte první výstup (**aircraftmonitor**) ujistěte se, že **Alias pro výstup**, **název datové sady** a **název tabulky** jsou stejné (**aircraftmonitor**). Opakujte postup pro přidání výstupy pro **aircraftalert**, a **flightsbyhour**. Po přidání všech tří výstupní tabulky a spuštění úlohy Azure Stream Analytics, měli byste obdržet potvrzovací zpráva ("spouští se Stream Analytics úlohy maintenancesa02asapbi bylo úspěšné").
2. Přihlaste se k [Power BI online](http://www.powerbi.com)
   
   * Na levém panelu části datové sady v pracovním prostoru ***datovou sadu*** názvy **aircraftmonitor**, **aircraftalert**, a **flightsbyhour** by se zobrazit. Toto je streamovaných dat, které jste nasdíleli z Azure Stream Analytics v předchozím kroku. Datová sada **flightsbyhour** pravděpodobně nezobrazí ve stejnou dobu jako další dvě datové sady vzhledem k povaze jazyka SQL za ní. Nicméně by se měla zobrazit za hodinu.
   * Ujistěte se, ***vizualizace*** podokně je otevřený a je zobrazena na pravé straně obrazovky.
3. Jakmile se data přenášejí do Power BI, můžete začít vizualizace streamovaná data. Níže je příklad řídicího panelu s vizualizacemi některé kritickou cestu k němu připnuté. Můžete vytvořit další řídicí panel dlaždice založené na příslušné datové sady. V závislosti na tom, jak dlouho spuštění vaší generátor dat se může lišit čísla na vizualizace.

    ![Zobrazení řídicího panelu](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

1. Tady je několik kroků k vytvoření jedné z výše uvedených – dlaždic "vozového parku zobrazení senzor 11 vs. Prahová hodnota šířka 48,26" dlaždice:
   
   * Klikněte na datovou sadu **aircraftmonitor** na levém panelu části datové sady.
   * Klikněte na tlačítko **spojnicový graf** ikonu.
   * Klikněte na tlačítko **zpracované** v **pole** tak, že se zobrazí v části "OS" v podokně **vizualizace** podokně.
   * Klikněte na tlačítko "s.11" a "s.11\_výstraha" tak, aby obě jsou uvedeny v části "Hodnoty". Klikněte na malou šipku vedle **s.11** a **s.11\_výstraha**, změňte "Sum" na "Average".
   * Klikněte na tlačítko **Uložit** nahoře a název sestavy "aircraftmonitor." Sestava s názvem "aircraftmonitor" zobrazí v **sestavy** tématu **Navigátor** podokna na levé straně.
   * Klikněte na tlačítko **PIN kód Visual** ikonu v pravém horním rohu tomto spojnicovém grafu. Můžete zvolit řídicího panelu může zobrazit okno "Připnout na řídicí panel". Vyberte "Ukázka prediktivní údržby" a pak klikněte na tlačítko "Pin".
   * Najeďte myší na tuto dlaždici na řídicím panelu, klikněte na ikonu pro "úpravy" v pravém horním rohu Chcete-li změnit její název na "vozového parku zobrazení senzor 11 vs. Prahová hodnota šířka 48,26" a titulek"Průměru napříč fleet v čase."

## <a name="delete-your-solution"></a>Odstranit řešení
Ujistěte se zastavit generátor dat, když aktivně nepoužíváte řešení jako provoz generátoru dat bude mít za následek vyšší náklady. Pokud ji nepoužíváte, odstraňte řešení. Odstraňuje se řešení odstraní všechny součásti, které jsou zřízené ve vašem předplatném při nasazení řešení. Odstranit řešení, klikněte na název svého řešení na levém panelu šablony řešení a potom klikněte na tlačítko **odstranit**.

## <a name="cost-estimation-tools"></a>Nástroje pro odhad nákladů
Následující dva nástroje jsou k dispozici vám pomůže lépe pochopit celkové náklady při spuštění prediktivní Údržba leteckých šablony řešení ve vašem předplatném:

* [Microsoft Azure Cost Estimator Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost Estimator nástroj (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

