---
title: Průvodce prediktivní údržbou pro letectví a kosmonautiku – proces týmové datové vědy
description: Technický průvodce šablonou řešení pro prediktivní údržbu v leteckém a kosmickém průmyslu, inženýrských sítích a dopravě.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 0542106f70e96b6c2f63e8ca03d2532de191d365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477166"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Technický průvodce šablonou řešení pro prediktivní údržbu v leteckém a kosmickém průmyslu

> [!Important]
> Tento článek byl zastaral. Diskuse o prediktivní údržbě v leteckém a kosmickém průmyslu je stále relevantní, ale aktuální informace naleznete v části [Přehled řešení pro firemní okruhy uživatelů](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Šablony řešení jsou navrženy tak, aby urychlily proces vytváření ukázky E2E. Nasazená šablona zřídí vaše předplatné s nezbytnými součástmi a pak vytvoří vztahy mezi nimi. Také semena datový kanál s ukázkovými daty z aplikace generátoru dat, který si stáhnete a nainstalujete na místním počítači po nasazení šablony řešení. Data z generátoru hydratují datový kanál a spouštějí generování předpovědí strojového učení, které pak můžete vizualizovat na řídicím panelu Power BI.

Proces nasazení vás provede několika kroky k nastavení přihlašovacích údajů k řešení. Ujistěte se, že zaznamenáváte pověření, jako je název řešení, uživatelské jméno a heslo, které zadáte během nasazení. 


Cílem tohoto článku je:
- Popište referenční architekturu a součásti zřízené ve vašem předplatném.
- Předveďte, jak nahradit ukázková data vlastními daty. 
- Ukažte, jak upravit šablonu řešení.  

## <a name="overview"></a>Přehled
![Architektura prediktivní údržby](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Když nasadíte řešení, aktivuje služby Azure, včetně Event Hub, Stream Analytics, HDInsight, Data Factory a Machine Learning. Diagram architektury ukazuje, jak je vytvořena šablona řešení prediktivní údržby pro letectví a kosmonautiku. Tyto služby můžete prozkoumat na webu Azure Portal tak, že na ně kliknete v diagramu šablony řešení vytvořeném nasazením řešení (s výjimkou HDInsightu, který se zřává na vyžádání, když jsou nutné ke spuštění související aktivity kanálu a jsou poté smazány).
Stáhněte si [verzi diagramu v plné velikosti](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Následující části popisují části řešení.

## <a name="data-source-and-ingestion"></a>Zdroj dat a požití
### <a name="synthetic-data-source"></a>Syntetický zdroj dat
Pro tuto šablonu je použitý zdroj dat generován ze stažené desktopové aplikace, kterou spustíte místně po úspěšném nasazení.

Chcete-li najít pokyny ke stažení a instalaci této aplikace, vyberte první uzel, Predictive Maintenance Data Generator, na diagramu šablony řešení. Pokyny najdete v panelu Vlastnosti. Tato aplikace je zdrojem služby [Azure Event Hub](#azure-event-hub) s datovými body nebo událostmi, které se používají ve zbytku toku řešení. Tento zdroj dat je odvozen z veřejně dostupných dat z [datového úložiště NASA](https://c3.nasa.gov/dashlink/resources/139/) pomocí [datové sady Turbofan Engine Degradation Simulation .](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)

Aplikace pro generování událostí naplní Azure Event Hub pouze při jeho provádění v počítači.  

### <a name="azure-event-hub"></a>Azure Event Hub  
Služba [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) je příjemcem vstupu poskytovaného syntetickým zdrojem dat.

## <a name="data-preparation-and-analysis"></a>Příprava a analýza dat  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) slouží k poskytování analýz téměř v reálném čase na vstupním streamu ze služby [Azure Event Hub.](#azure-event-hub) Výsledky pak publikujete na řídicí mase [Power BI](https://powerbi.microsoft.com) a archivujete všechny nezpracované příchozí události do služby [Azure Storage](https://azure.microsoft.com/services/storage/) pro pozdější zpracování službou Azure [Data Factory.](https://azure.microsoft.com/documentation/services/data-factory/)

### <a name="hdinsight-custom-aggregation"></a>Vlastní agregace HDInsight
Spusťte skripty [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (řízené službou Azure Data Factory) pomocí HDInsight k poskytování agregací na nezpracovaná události archivované pomocí prostředku Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Predikce zbývající životnosti (RUL) konkrétního leteckého motoru můžete provádět pomocí vstupů přijatých službou [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (řízené službou Azure Data Factory). 

## <a name="data-publishing"></a>Publikování dat
### <a name="azure-sql-database"></a>Azure SQL Database
Azure [SQL Database](https://azure.microsoft.com/services/sql-database/) slouží k ukládání předpovědí přijatých Azure Machine Learning, které se pak spotřebovávají na řídicím panelu Power [BI.](https://powerbi.microsoft.com)

## <a name="data-consumption"></a>Využití dat
### <a name="power-bi"></a>Power BI
Pomocí [Power BI](https://powerbi.microsoft.com) můžete zobrazit řídicí panel, který obsahuje agregace a výstrahy poskytované [Službou Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), a také předpovědi RUL uložené v [Azure SQL Database,](https://azure.microsoft.com/services/sql-database/) které byly vytvořeny pomocí [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Jak přinést vlastní data
Tato část popisuje, jak přenést vlastní data do Azure a jaké oblasti vyžadují změny pro data, která do této architektury přenesete.

Je nepravděpodobné, že vaše datová sada odpovídá datové sadě používané [turbofan engine degradace simulace datové sady](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) používané pro tuto šablonu řešení. Pochopení dat a požadavků jsou zásadní pro to, jak upravit tuto šablonu tak, aby fungovala s vlastními daty. 

Následující části popisují části šablony, které vyžadují změny při zavedení nové datové sady.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event Hub je obecný. data mohou být zaúčtována do centra ve formátu CSV nebo JSON. V Centru událostí Azure nedojde k žádnému speciálnímu zpracování, ale je důležité, abyste pochopili data, která se do něj přivázala.

Tento dokument nepopisuje, jak ingestovat data, ale můžete snadno odeslat události nebo data do centra událostí Azure pomocí api centra událostí.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Pomocí prostředku Azure Stream Analytics můžete poskytovat analýzy téměř v reálném čase čtením datových toků a odchozími daty do libovolného počtu zdrojů.

Pro šablonu řešení Prediktivní údržba pro letectví a kosmonautiku se dotaz Azure Stream Analytics skládá ze čtyř dílčích dotazů, přičemž každý dotaz spotřebovává události ze služby Azure Event Hub s výstupy do čtyř různých umístění. Tyto výstupy se skládají ze tří datových sad Power BI a jednoho umístění úložiště Azure.

Dotaz Azure Stream Analytics najdete na adrese:

* Připojení k portálu Azure
* ![Vyhledání ikony](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) Stream Analytics, která byla vygenerována při nasazení řešení *(například* **maintenancesa02asapbi** a **maintenancesa02asablob** pro řešení prediktivní údržby)
* Výběr
  
  * ***INPUTS*** pro zobrazení vstupu dotazu
  * ***DOTAZ*** pro zobrazení samotného dotazu
  * ***VÝSTUPY*** pro zobrazení různých výstupů

Informace o konstrukci dotazů Azure Stream Analytics najdete v [referenční příručce pro dotaz služby Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) na webu MSDN.

V tomto řešení vyvyprávějí dotazy tři datové sady s téměř v reálném čase analytickými informacemi o příchozím datovém toku na řídicí panel Power BI, který je součástí této šablony řešení. Vzhledem k tomu, že existují implicitní znalosti o formátu příchozích dat, musí být tyto dotazy změněny na základě formátu dat.

Dotaz v druhé údržbě úlohy Stream **Analyticsa02asablob** jednoduše vydezuje všechny události [centra událostí](https://azure.microsoft.com/services/event-hubs/) do [služby Azure Storage](https://azure.microsoft.com/services/storage/) a proto nevyžaduje žádnou změnu bez ohledu na formát dat, protože úplné informace o událostech se vypočtou do úložiště.

### <a name="azure-data-factory"></a>Azure Data Factory
Služba [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestruje přesun a zpracování dat. V šabloně řešení prediktivní údržby pro letectví a kosmonautiku se továrna na data skládá ze tří [kanálů,](../../data-factory/concepts-pipelines-activities.md) které přesouvají a zpracovávají data pomocí různých technologií.  Přístup k vaší datové továrně otevřením uzlu Data Factory v dolní části diagramu šablony řešení vytvořeného nasazením řešení. Chyby v datových sadách jsou způsobeny tím, že se před spuštěním generátoru dat nasadí data. Tyto chyby mohou být ignorovány a nebrání vaší datové továrně ve fungování.

![Chyby datové sady datové sady datové sady Data Factory](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Tato část popisuje potřebné [kanály a aktivity](../../data-factory/concepts-pipelines-activities.md) obsažené v [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Zde je diagram zobrazení řešení.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Dva kanály této továrny obsahují [skripty Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) používané k rozdělení a agregaci dat. Když je třeba poznamenat, skripty jsou umístěny v účtu [Azure Storage](https://azure.microsoft.com/services/storage/) vytvořené během instalace. Jejich umístění je: maintenancesascript\\\\script\\\\podregistr\\ \\ (nebo https://[Název vašeho řešení].blob.core.windows.net/maintenancesascript).

Podobně jako dotazy [Azure Stream Analytics,](#azure-stream-analytics-1) [hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty mají implicitní znalosti o formátu příchozích dat a musí být změněna na základě formátu dat.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Tento [kanál](../../data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – [hdinsighthive](../../data-factory/transform-data-using-hadoop-hive.md) aktivitu pomocí [HDInsightLinkedService,](https://msdn.microsoft.com/library/azure/dn893526.aspx) který spouští skript [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) k rozdělení dat umístěných ve [službě Azure Storage](https://azure.microsoft.com/services/storage/) během [úlohy Azure Stream Analytics.](https://azure.microsoft.com/services/stream-analytics/)

Skript [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pro tuto úlohu dělení je ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Tento [kanál](../../data-factory/concepts-pipelines-activities.md) obsahuje několik aktivit, jejichž konečný výsledek je scored předpovědi z experimentu [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) přidružené k této šabloně řešení.

Mezi aktivity patří:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivity pomocí [HDInsightLinkedService,](https://msdn.microsoft.com/library/azure/dn893526.aspx) který spouští skript [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) provádět agregace a funkce inženýrství nezbytné pro experiment [Azure Machine Learning.](https://azure.microsoft.com/services/machine-learning/)
  Skript [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pro tuto úlohu dělení je ***PrepareMLInput.hql***.
* [Zkopírujte](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitu, která přesune výsledky z [hdinsighthive](../../data-factory/transform-data-using-hadoop-hive.md) aktivity do jednoho objektu blob [úložiště Azure](https://azure.microsoft.com/services/storage/) přístup nádanku aktivity [AzureMLBatchScoring.](https://msdn.microsoft.com/library/azure/dn894009.aspx)
* [Aktivita AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) volá experiment [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) s výsledky vloženými do jednoho objektu blob úložiště [Azure.](https://azure.microsoft.com/services/storage/)

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Tento [kanál](../../data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – [aktivitu Copy,](https://msdn.microsoft.com/library/azure/dn835035.aspx) která přesune výsledky experimentu [Azure Machine Learning](#azure-machine-learning) z ***MLScoringPipeline*** do [databáze Azure SQL](https://azure.microsoft.com/services/sql-database/) zřízené jako součást instalace šablony řešení.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Experiment [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) použitý pro tuto šablonu řešení poskytuje zbývající životnost (RUL) leteckého motoru. Experiment je specifický pro spotřebovanou sadu dat a vyžaduje úpravu nebo nahrazení specifické pro data, která byla přinesena.

## <a name="monitor-progress"></a>Sledování průběhu
Po spuštění generátoru dat kanálu začne dehydratovat a různé součásti řešení začít kopat do akce podle příkazů vydaných datové továrny. Existují dva způsoby, jak sledovat potrubí.

* Jedna z úloh Stream Analytics zapisuje nezpracovaná příchozí data do úložiště objektů blob. Pokud kliknete na součást úložiště objektů Blob vašeho řešení z obrazovky, kterou jste úspěšně nasadili řešení a potom klikněte na otevřít v pravém panelu, přejdete na [portál Azure](https://portal.azure.com/). Jakmile tam, klikněte na blobs. Na dalším panelu se zobrazí seznam kontejnerů. Klikněte na **maintenancesadata**. V dalším panelu je složka **rawdata.** Uvnitř složky rawdata jsou složky s názvy jako hour=17 a hour=18. Přítomnost těchto složek označuje, že nezpracovaná data jsou generována v počítači a uložena v úložišti objektů blob. Měli byste vidět csv soubory s omezenými velikostmi v MB v těchto složkách.
* Posledním krokem kanálu je zápis dat (například předpovědi ze strojového učení) do databáze SQL. Bude pravděpodobně muset počkat maximálně tři hodiny pro data se zobrazí v databázi SQL. Jedním ze způsobů, jak sledovat, kolik dat je k dispozici v databázi SQL je prostřednictvím [portálu Azure](https://portal.azure.com/). Na levém panelu vyhledejte ![ikonu](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) SQL SQL sql a klikněte na ni. Pak vyhledejte databázi **pmaintenancedb** a klikněte na ni. Na další stránce v dolní části klikněte na MANAGE.
   
    ![Ikona Spravovat](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Zde můžete kliknout na Nový dotaz a dotaz na počet řádků (například vybrat count(*) z PMResult). Jak databáze roste, počet řádků v tabulce by se měl zvýšit.

## <a name="power-bi-dashboard"></a>Řídicí panel Power BI

Nastavte řídicí panel Power BI pro vizualizaci dat Azure Stream Analytics (horká cesta) a výsledků predikce dávek z Azure Machine Learning (studená cesta).

### <a name="set-up-the-cold-path-dashboard"></a>Nastavení řídicího panelu studené cesty
V datovém kanálu studené cesty je cílem získat prediktivní RUL (zbývající životnost) každého leteckého motoru po dokončení letu (cyklu). Výsledek předpovědi je aktualizován každé 3 hodiny pro předpovídání leteckých motorů, které dokončily let během posledních 3 hodin.

Power BI se připojuje k Azure SQL Database jako zdroj dat, kde jsou uloženy výsledky předpovědi. 

Poznámka: 
1.    Při nasazení řešení předpověď se zobrazí v databázi do 3 hodin. Soubor pbix dodané se stažením generátoru obsahuje některá data osiva, takže můžete ihned vytvořit řídicí panel Power BI. 
2.    V tomto kroku je předpokladem stažení a instalace bezplatného softwaru [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Následující kroky vás povedou k připojení souboru pbix k databázi SQL, která byla v době nasazení řešení obsahující data (například výsledky předpovědi) pro vizualizaci.

1. Získejte pověření databáze.
   
   Před přechodem na další kroky budete potřebovat **název databázového serveru, název databáze, uživatelské jméno a heslo.** Zde jsou kroky, které vás povedou, jak je najít.
   
   * Jakmile **se "Azure SQL Database"** v diagramu šablony řešení změní na zelenou, klikněte na něj a potom klikněte na **tlačítko Otevřít**.
   * Zobrazí se nová karta/okno prohlížeče, které zobrazuje stránku portálu Azure. V levém panelu klepněte na **položku Skupiny zdrojů.**
   * Vyberte předplatné, které používáte k nasazení řešení, a pak vyberte **"YourSolutionName\_ResourceGroup"**.
   * V novém panelu pop-out ![](./media/predictive-maintenance-technical-guide/icon-sql.png) klikněte na ikonu ikony SQL pro přístup k databázi. Název databáze je vedle této ikony (například **pmaintenancedb)** a **název databázového serveru** je uveden pod vlastností Název serveru a měl by vypadat podobně jako **YourSolutionName.database.windows.net**.
   * Uživatelské **jméno** a **heslo** databáze jsou stejné jako uživatelské jméno a heslo dříve zaznamenané během nasazení řešení.
2. Aktualizujte zdroj dat souboru sestavy studené cesty pomocí Power BI Desktopu.
   
   * Ve složce, do které jste stáhli a rozbalili soubor generátoru, poklikejte na soubor **\\PowerBI PredictiveMaintenanceAerospace.pbix.** Pokud se při otevření souboru zobrazí varovné zprávy, ignorujte je. V horní části souboru klikněte na **tlačítko Upravit dotazy**.
     
     ![Upravit dotazy](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Zobrazí se dvě **tabulky, RemainingUsefulLife** a **PMResult**. Vyberte první tabulku ![a](./media/predictive-maintenance-technical-guide/icon-query-settings.png) klikněte na ikonu Nastavení dotazu vedle **položky Zdroj** v části **POUŽITÉ KROKY** v pravém panelu **Nastavení dotazu.** Ignorujte všechny varovné zprávy, které se zobrazí.
   * V rozbalovacím okně nahraďte **"Server"** a **"Databáze"** vlastními názvy serverů a databází a klepněte na tlačítko **OK**. Chcete-li zadat název serveru, ujistěte se, že jste zadali port 1433 (**YourSolutionName.database.windows.net, 1433**). Ponechte pole Databáze jako **pmaintenancedb**. Ignorujte varovné zprávy, které se zobrazují na obrazovce.
   * V dalším vyskakovacím okně se v levém podokně zobrazí dvě možnosti **(Windows** a **Databáze).** Klikněte na **"Databáze"**, vyplňte **své uživatelské jméno** a **heslo** (uživatelské jméno a heslo, které jste zadali při prvním nasazení řešení a vytvoření azure sql database). V ***části Vyberte úroveň, na kterou chcete tato nastavení použít***, zaškrtněte možnost úrovně databáze. Poté klepněte na tlačítko **Připojit**.
   * Klikněte na druhou tabulku ![ **PMResult** a potom klikněte na ikonu](./media/predictive-maintenance-technical-guide/icon-navigation.png) Navigace vedle tlačítka **"Zdroj"** v části **"POUŽITÉ KROKY"** v pravém **panelu Nastavení dotazu** a aktualizujte názvy serverů a databází jako ve výše uvedených krocích a klikněte na TLAČÍTKO OK.
   * Jakmile se vrátíte na předchozí stránku, zavřete okno. Zobrazí se zpráva – klepněte na tlačítko **Použít**. Nakonec změny uložte klepnutím na tlačítko **Uložit.** Soubor Power BI nyní navázal připojení k serveru. Pokud jsou vizualizace prázdné, zrušte zaškrtnutí výběrů vizualizací a vizualizovat všechna data kliknutím na ikonu eposu v pravém horním rohu legend. Tlačítko Aktualizovat slouží k zrcadlení nových dat o vizualizacích. Zpočátku se zobrazí pouze počáteční data na vizualizacích jako továrna dat je naplánováno aktualizovat každé 3 hodiny. Po 3 hodinách se při aktualizaci dat zobrazí nové předpovědi, které se projeví ve vizualizacích.
3. (Nepovinné) Publikujte řídicí panel studené cesty do [Power BI online](https://www.powerbi.com/). Tento krok potřebuje účet Power BI (nebo účet Office 365).
   
   * Klikněte na **Publikovat** a o několik sekund později se zobrazí okno "Publikování na Power BI Úspěch!" se zeleným zaškrtnutím. Klikněte na odkaz pod položkou "Otevřít predictiveMaintenanceAerospace.pbix v Power BI". Podrobné pokyny najdete v [tématu Publikování z Power BI Desktopu](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Vytvoření nového řídicího **+** panelu: klikněte na znaménko vedle oddílu **Řídicí panely** v levém podokně. Pro tento nový řídicí panel zadejte název "Ukázka prediktivní údržby".
   * Po otevření sestavy klikněte ![](./media/predictive-maintenance-technical-guide/icon-pin.png) na ikonu PIN a připněte všechny vizualizace na řídicí panel. Podrobné pokyny najdete v [tématu Připnutí dlaždice na řídicí panel Power BI ze sestavy](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Přejděte na stránku řídicího panelu, upravte velikost a umístění vizualizací a upravte jejich názvy. Podrobné pokyny k úpravám dlaždic najdete v [tématu Úprava dlaždice – změna velikosti, přesunutí, přejmenování, připnutí, odstranění, přidání hypertextového odkazu](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Tady je příklad řídicího panelu s některými vizualizacemi studené cesty, které jsou k němu připnuté.  V závislosti na tom, jak dlouho spustíte generátor dat, se vaše čísla ve vizualizacích mohou lišit.
     <br/>
     ![Konečný pohled](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Chcete-li naplánovat aktualizaci dat, najeďte myší na datovou ![sadu **PredictiveMaintenanceAerospace,** klepněte na ikonu](./media/predictive-maintenance-technical-guide/icon-elipsis.png) Tři tečky a pak zvolte Schedule **Refresh**.
     <br/>
     > [!NOTE]
     > Pokud se zobrazí varovná zpráva, klikněte na **Upravit přihlašovací údaje** a ujistěte se, že vaše databázová pověření jsou stejná jako pověření popsaná v kroku 1.
     <br/>
     ![Aktualizace plánu](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Rozbalte část **Aktualizace plánu.** Zapněte "udržujte data aktuální".
     <br/>
   * Naplánujte aktualizaci na základě vašich potřeb. Další informace najdete [v tématu Aktualizace dat v Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Nastavení řídicího panelu horké cesty
Následující kroky vás povedou k vizualizaci výstupu dat z úloh Stream Analytics, které byly generovány v době nasazení řešení. K provedení následujících kroků je potřeba online účet [Power BI.](https://www.powerbi.com/) Pokud nemáte účet, můžete si [ho vytvořit](https://powerbi.microsoft.com/pricing).

1. Přidejte výstup Power BI ve službě Azure Stream Analytics (ASA).
   
   * Musíte postupovat podle pokynů v [Azure Stream Analytics & Power BI: Analytický řídicí panel pro viditelnost streamovaných dat](../../stream-analytics/stream-analytics-power-bi-dashboard.md) v reálném čase, abyste nastavili výstup úlohy Azure Stream Analytics jako řídicího panelu Power BI.
   * Dotaz ASA má tři výstupy, které jsou **aircraftmonitor**, **aircraftalert**a **flightsbyhour**. Dotaz můžete zobrazit kliknutím na kartu dotazu. Odpovídající každé z těchto tabulek je třeba přidat výstup do ASA. Když přidáte první výstup **(aircraftmonitor),** ujistěte se, že **výstupní alias**, název datové **sady** a **název tabulky** jsou stejné (**aircraftmonitor**). Opakujte kroky pro přidání výstupů pro **aircraftalert**a **flightsbyhour**. Po přidání všech tří výstupních tabulek a spuštění úlohy ASA byste měli dostat potvrzovací zprávu ("Spuštění údržby úlohy Stream Analyticsa02asapbi proběhlo úspěšně").
2. Přihlášení k [Power BI online](https://www.powerbi.com)
   
   * V části Datové sady na levém ***panelu*** v části Můj pracovní prostor by se měly zobrazit názvy **aircraftmonitor**, **aircraftalert**a **flightsbyhour.** Toto jsou streamovaná data, která jste v předchozím kroku odsunuli z Azure Stream Analytics. Datová sada **flightsbyhour** nemusí zobrazit ve stejnou dobu jako další dvě datové sady z důvodu povahy dotazu SQL za ním. Mělo by se však ukázat po hodině.
   * Zkontrolujte, zda je podokno ***Vizualizace*** otevřené a je zobrazeno na pravé straně obrazovky.
3. Jakmile data proudí do Power BI, můžete začít vizualizovat streamovaná data. Níže je uveden příklad řídicího panelu s některými vizualizacemi horkých cest, které jsou k němu připnuty. Můžete vytvořit další dlaždice řídicího panelu na základě příslušných datových sad. V závislosti na tom, jak dlouho spustíte generátor dat, se vaše čísla ve vizualizacích mohou lišit.

    ![Zobrazení řídicího panelu](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Zde je několik kroků k vytvoření jedné z výše uvedených dlaždic – dlaždice "Fleet View of Sensor 11 vs. Threshold 48.26":
   
   * Klepněte na dataset **aircraftmonitor** v levém panelu Sekce datových sad.
   * Klikněte na ikonu **Spojnicový graf.**
   * V podokně **Pole** klikněte na **Zpracované,** aby se v podokně Vizualizace zořilo v části **Osa.**
   * Klikněte na "s11"\_a "s11 alert", aby se obě zobrazily v části "Hodnoty". Klikněte na malou šipku vedle **s11** a **\_s11 upozornění**, změna "Sum" na "Průměr".
   * Klikněte na tlačítko **ULOŽIT** nahoře a pojmenujte zprávu "aircraftmonitor". Sestava s názvem "aircraftmonitor" je zobrazena v části **Sestavy** v podokně **Navigátor** vlevo.
   * Klikněte na ikonu **Pin Visual** v pravém horním rohu tohoto spojnicového grafu. Můžete si vybrat řídicí panel a zobrazit okno "Připnout na řídicí panel". Vyberte možnost Ukázka prediktivní údržby a klikněte na tlačítko "Připnout".
   * Najeďte myší na tuto dlaždici na řídicím panelu, klikněte na ikonu "upravit" v pravém horním rohu a změňte svůj název na "Fleet View of Sensor 11 vs. Threshold 48.26" a titulky k "Průměr napříč vozovým parkem v průběhu času".

## <a name="delete-your-solution"></a>Smazání řešení
Ujistěte se, že zastavíte generátor dat, pokud aktivně nepoužíváte řešení, protože spuštění generátoru dat bude mít vyšší náklady. Pokud řešení nepoužíváte, odstraňte ho. Odstraněním vašeho řešení odstraníte všechny součásti zřízené ve vašem předplatném při nasazení řešení. Chcete-li odstranit řešení, klepněte na název řešení v levém panelu šablony řešení a potom klepněte na tlačítko **Odstranit**.

## <a name="cost-estimation-tools"></a>Nástroje pro odhad nákladů
K dispozici jsou následující dva nástroje, které vám pomohou lépe porozumět celkovým nákladům spojeným se spuštěním šablony řešení prediktivní údržby pro letectví a kosmonautiku v rámci předplatného:

* [Nástroj pro odhad nákladů Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)
* [Nástroj pro odhad nákladů Microsoft Azure (plocha)](https://www.microsoft.com/download/details.aspx?id=43376)

