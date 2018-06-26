---
title: V letecký s Azure – technické příručce Cortana Intelligence řešení prediktivní údržby | Microsoft Docs
description: Technické příručce k šabloně řešení s Microsoft Cortana Intelligence pro prediktivní údržby v letecký, nástrojů a Transport.
services: machine-learning
documentationcenter: ''
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2c4d2147-0f05-4705-8748-9527c2c1f033
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: fboylu
ms.openlocfilehash: 2916252c08c599d2e528595a8cdf2abca8ea89a3
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938380"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Technické příručce k šabloně Cortana Intelligence řešení pro prediktivní údržby v letecký a jiné firmy

>[!Important]
Tento článek je zastaralá. Diskuze o prediktivní údržby v letecký je stále relevantní, ale aktuální informace, použijte [přehled řešení pro cílové obchodní skupiny](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Šablony řešení jsou navrženy pro urychlit proces vytváření ukázkové E2E nad Cortana Intelligence Suite. Šablonu nasazené zřídí vaše předplatné s nezbytnými součástmi Cortana Intelligence a potom vytvoří vztahy mezi nimi. Doplňuje také datovém kanálu s ukázkovými daty z aplikace generátor dat, která si stáhnout a nainstalovat na místním počítači po nasazení řešení šablony. Data z generátor hydráty datového kanálu a spuštění generování předpovědi strojové učení, které lze následně vizualizovat na řídicím panelu Power BI.

Proces nasazení provede několik kroků nastavte přihlašovací údaje řešení. Ujistěte se, že záznam přihlašovací údaje, jako je například název řešení, uživatelské jméno a heslo, které zadáte během nasazení. 


Cílem tohoto článku je:
- Popisují referenční architektura a komponenty, které jsou zřízené v rámci vašeho předplatného.
- Ukazují, jak nahradit ukázková data s vlastními daty. 
- Ukazují, jak upravit šablonu řešení.  

> [!TIP]
> Můžete stáhnout a vytisknout [PDF verzi tohoto článku](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Přehled
![Architektura prediktivní údržby](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Při nasazení řešení, aktivuje služby Azure v sadě Analytics Cortana (včetně centra událostí, Stream Analytics, HDInsight, objekt pro vytváření dat a strojové učení). Diagram architektury ukazuje, jak je vytvořený prediktivní Údržba leteckých šablony řešení. Můžete prozkoumat kliknutím v diagramu šablonu řešení vytvořené pomocí nasazení řešení (s výjimkou HDInsight, což je zřízený na vyžádání, když jsou požadovány ke spuštění aktivity související kanálu a jsou tyto služby na portálu Azure Odstranit později).
Stáhněte si [plné velikosti verze diagramu](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Následující oddíly popisují části řešení.

## <a name="data-source-and-ingestion"></a>Zdroj dat a přijímání
### <a name="synthetic-data-source"></a>Zdroj dat syntetického
U této šablony se zdroj dat používaný generují z plochy aplikace, která můžete stáhnout a spustit místně po úspěšné nasazení.

Pokyny ke stažení a instalaci této aplikace, vyberete prvního uzlu generátor dat prediktivní údržby, v diagramu šablonu řešení. Pokyny naleznete v panelu Vlastnosti. Tuto aplikaci kanály [centra událostí Azure](#azure-event-hub) služby s datových bodů nebo události, použité ve zbývající části toku řešení. Tento zdroj dat je odvozený od veřejně dostupnými daty z [datové úložiště NASA](https://c3.nasa.gov/dashlink/resources/139/) pomocí [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

Aplikace generování událostí naplní centra událostí Azure pouze tehdy, když je prováděna v počítači.  

### <a name="azure-event-hub"></a>Centrum událostí Azure  
[Centra událostí Azure](https://azure.microsoft.com/services/event-hubs/) příjemce vstup syntetické zdroj dat je služba.

## <a name="data-preparation-and-analysis"></a>Příprava dat a analýzy  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Použití [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) k poskytování téměř v reálném čase analýzy vstupního datového proudu z [centra událostí Azure](#azure-event-hub) služby. Potom publikovat výsledky do [Power BI](https://powerbi.microsoft.com) řídicí panel i archivu všechny nezpracované příchozí události [Azure Storage](https://azure.microsoft.com/services/storage/) služby pro pozdější zpracování pomocí [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)služby.

### <a name="hdinsight-custom-aggregation"></a>Vlastní agregační HDInsight
Spustit [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty (řízená Azure Data Factory) pomocí HDInsight poskytovat agregace na nezpracované události archivovat pomocí služby Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Provádět předpovědi na zbývající životnosti (RUL) konkrétní leteckého motoru, pomocí zadané vstupy obdrželi s portálem [služba Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (řízená Azure Data Factory). 

## <a name="data-publishing"></a>Publikování dat
### <a name="azure-sql-database"></a>Azure SQL Database
Použití [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) k uložení předpovědi přijatých službu Azure Machine Learning, která se pak spotřebovávají ve [Power BI](https://powerbi.microsoft.com) řídicího panelu.

## <a name="data-consumption"></a>Využití dat
### <a name="power-bi"></a>Power BI
Použití [Power BI](https://powerbi.microsoft.com) zobrazit řídicí panel, který obsahuje agregace a výstrahy poskytované [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), stejně jako RUL předpovědi uložené v [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) které byly vytvořeny pomocí [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Jak mají být předány svoje vlastní data
Tato část popisuje, jak převést svoje vlastní data do Azure a oblasti, které vyžadují změny data, která přepnutím do této architektury.

Není pravděpodobné, že datovou sadu odpovídá datové sady používané [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) použít pro tuto šablonu řešení. Seznámení s daty a požadavky na jsou klíčové v tom, jak upravit tuto šablonu pro práci s vlastními daty. 

Následující části popisují části šablony, které vyžadují změny, pokud je zavedená nová datová sada.

### <a name="azure-event-hub"></a>Centrum událostí Azure
Azure Centrum událostí je obecný; data můžete publikované v centru ve formátu CSV nebo formátu JSON. Žádné speciální zpracování dojde v Centru událostí Azure, ale je důležité, abyste rozuměli tomu data, která je dodáni do ní.

Tento dokument nepopisuje ingestovat data, ale můžete snadno odesílat události nebo data do centra událostí Azure pomocí rozhraní API centra událostí.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Použijte službu Azure Stream Analytics k poskytování téměř v reálném čase analytics čtení z datových proudů a odesíláním dat do libovolný počet zdrojů.

Pro prediktivní Údržba leteckých šablony řešení Azure Stream Analytics dotazu se skládá z čtyři dílčí dotazy, každý dotaz využívání události ze služby Azure Event Hub s výstupy do čtyř různých umístění. Tyto výstupy obsahovat tři datové sady Power BI a jedno umístění úložiště Azure.

Dotaz Azure Stream Analytics lze najít:

* Připojení k portálu Azure
* Vyhledání úlohy Stream Analytics ![Stream Analytics ikonu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) které byly generovány při řešení nasazená (*například*, **maintenancesa02asapbi** a **maintenancesa02asablob** pro řešení prediktivní údržby)
* Výběr
  
  * ***VSTUPY*** zobrazení dotazu vstup
  * ***DOTAZ*** zobrazíte samotný dotaz
  * ***VÝSTUPY*** k zobrazení různých výstupy

Informace o vytváření dotazů Azure Stream Analytics najdete v [referenční příručka Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx) na webu MSDN.

Dotazy v tomto řešení výstupní tři datové sady s téměř v reálném čase analytics informace o příchozí datový proud na řídicí panel Power BI, který je dodáván jako součást této šablony řešení. Protože se nachází implicitní znalosti o příchozí data formátu, tyto dotazy musí být změněn podle vaší formát data.

Dotaz v druhé úloze Stream Analytics **maintenancesa02asablob** jednoduše všechny výstupy [centra událostí](https://azure.microsoft.com/services/event-hubs/) události [Azure Storage](https://azure.microsoft.com/services/storage/) a proto vyžaduje žádné změnou bez ohledu na formát vaše data jako úplné událostí je Streamovat informace do úložiště.

### <a name="azure-data-factory"></a>Azure Data Factory
[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) služeb orchestruje přesouvání a zpracování dat. V prediktivní Údržba leteckých šablony řešení, objektu pro vytváření dat se skládá ze tří [kanály](../../data-factory/concepts-pipelines-activities.md) , přesunout a zpracování dat pomocí různých technologií.  Přístup k objektu pro vytváření dat otevřením uzlu služby Data Factory v dolní části diagram řešení šablony vytvořené pomocí nasazení řešení. Chyby v části vaše datové jsou z důvodu objekt pro vytváření dat se nasazené, než generátor dat byla spuštěna. Tyto chyby můžete ignorovat a nezabrání vaší služby data factory funguje

![Chyby datové sady objektu pro vytváření dat](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Tato část popisuje nezbytné [kanály a aktivity](../../data-factory/concepts-pipelines-activities.md) součástí [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Zde je zobrazení diagramu řešení.

![Azure Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dva kanály tento objekt pro vytváření obsahovat [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty použité k vytvoření oddílů a agregovat data. Pokud už jsme zmínili, skripty jsou umístěné v [Azure Storage](https://azure.microsoft.com/services/storage/) účet vytvořený během instalace. Jejich umístění je: maintenancesascript\\\\skriptu\\\\hive\\ \\ (nebo name].blob.core.windows.net/maintenancesascript https://[Your řešení).

Podobně jako [Azure Stream Analytics](#azure-stream-analytics-1) dotazy, [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skripty mají implicitní znalosti o příchozí formátu data a musí být změněn podle vaší formát data.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
To [kanálu](../../data-factory/concepts-pipelines-activities.md) obsahuje jediné aktivity – [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivity pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , která se spouští [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript k oddílu dat umístit do [Azure Storage](https://azure.microsoft.com/services/storage/) během [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) úlohy.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript pro toto rozdělení úloh je ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
To [kanálu](../../data-factory/concepts-pipelines-activities.md) obsahuje několik aktivit, jejichž konečným výsledkem je, že scored předpověď z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentu přidružená k této šabloně řešení.

Aktivity, které jsou zahrnuty jsou:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivity pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , která se spouští [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript k provádění agregací a konstruování potřebné pro [počítače Azure Učení](https://azure.microsoft.com/services/machine-learning/) experiment.
  [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) skript pro toto rozdělení úloh je ***PrepareMLInput.hql***.
* [Kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, která přemísťuje výsledků [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivity na jednu [Azure Storage](https://azure.microsoft.com/services/storage/) blob přístup [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivita.
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivity volání [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentu s výsledky umístit do jednoho [Azure Storage](https://azure.microsoft.com/services/storage/) objektů blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
To [kanálu](../../data-factory/concepts-pipelines-activities.md) obsahuje jediné aktivity - [kopie](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivity, která přemísťuje výsledky [Azure Machine Learning](#azure-machine-learning) experimentovat z  ***MLScoringPipeline*** k [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) zřízený jako součást instalace šablony řešení.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentovat použité pro tuto šablonu řešení poskytuje zbývající doby životnosti (RUL) leteckého motoru. Experimentu je specifický pro datové sady spotřebované a vyžaduje úpravu nebo v místním nahrazení specifické pro data.

Informace o vytváření experimentu Azure Machine Learning najdete v tématu [prediktivní údržby: Krok 1 ze 3, Příprava dat a funkce konstruování](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>Monitorování průběhu
Po spuštění generátor dat kanálu začne nechávají dehydratovat a spustit různé komponenty řešení si to chtěl / do akce následující příkazy vydané službou data factory. Existují dva způsoby, jak monitorovat kanál.

1. Jeden z úlohy Stream Analytics zapíše nezpracovaná příchozí data do úložiště objektů blob. Pokud kliknete na úložiště objektů Blob součást řešení z obrazovky můžete úspěšně nasazené řešení a klikněte na tlačítko Otevřít v pravém panelu, tím přejdete [portál Azure](https://portal.azure.com/). Potom klikněte na objekty BLOB. Další panelu zobrazí se seznam kontejnerů. Klikněte na **maintenancesadata**. V dalším panel je **rawdata** složky. Ve složce rawdata jsou složky s názvy, například hodinu = 17 a hodinu = 18. Přítomnost tyto složky určuje nezpracovaná data, která má být vygenerované v počítači a uložené v úložišti objektů blob. Měli byste vidět souborů csv pomocí konečné velikosti v MB v těchto složkách.
2. Posledním krokem kanálu je zapisovat data (například predikcím ze strojového učení) do databáze SQL. Možná bude muset počkat maximálně tři hodiny pro zobrazení dat v databázi SQL. Je možné sledovat, kolik dat je k dispozici ve vaší databázi SQL pomocí [portál Azure](https://portal.azure.com/). V levém panelu vyhledejte databází SQL ![SQL ikonu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) a klikněte na něj. Vyhledejte databázi **pmaintenancedb** a klepněte na ni. Na další stránce v dolní části klikněte na SPRAVOVAT
   
    ![Ikona Správa](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png).
   
    Zde můžete kliknutím na nový dotaz a dotaz pro počet řádků (například vyberte count(*) z PMResult). S růstem vaší databáze, měli zvýšit počet řádků v tabulce.

## <a name="power-bi-dashboard"></a>Řídicí panel Power BI

Nastavte řídicí panel Power BI můžete vizualizovat data Azure Stream Analytics (aktivní trase) a batch předpovědi výsledky z Azure machine learning (neaktivní trase).

### <a name="set-up-the-cold-path-dashboard"></a>Nastavení řídicího panelu neaktivní trase
V datovém kanálu neaktivní trase cílem je získat prediktivní RUL (zbývající dobu životnosti) každý motor letadla po jeho dokončení letu (cyklus). Výsledek předpovědi se aktualizuje každých 3 hodiny pro predikci letecké motory, které dokončily letu během posledních 3 hodiny.

Power BI se připojí k Azure SQL database jako svůj zdroj dat, kde jsou uložené výsledky předpovědi. Poznámka: 1) pro nasazení řešení, předpovědi se zobrazí v databázi v rámci 3 hodiny.
Soubor pbix, které byly dodány s stahování generátor obsahuje počáteční hodnoty data, takže můžete hned vytvořit řídicí panel Power BI. 2) v tomto kroku předpokladů je ke stažení a instalaci bezplatný software [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Následující kroky vás o tom, jak připojit soubor pbix k databázi SQL, které se spouštějí v době nasazení řešení, který obsahuje data (například předpovědi výsledky) pro vizualizaci.

1. Získáte přihlašovací údaje databáze.
   
   Budete potřebovat **databáze název serveru, název databáze, uživatelské jméno a heslo** než přejdete k dalším krokům. Tady jsou kroky pro pokyny, jak je vyhledat.
   
   * Jednou **databáze SQL Azure** v šabloně řešení změní zelená diagramu, klikněte na něj a potom klikněte na **, otevřete'**.
   * Zobrazí se nové kartě nebo okno prohlížeče, který zobrazí stránku portálu Azure. Klikněte na tlačítko **'skupiny prostředků,** na levém panelu.
   * Vyberte odběr, který používáte pro nasazení řešení a potom vyberte **' YourSolutionName\_ResourceGroup'**.
   * V nové pop na panely, klikněte na tlačítko ![SQL ikonu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) ikonu pro přístup k vaší databázi. Název databáze je vedle ikonu (například **'pmaintenancedb'**) a **název databázového serveru** uvedena pod vlastnost název serveru a by měl vypadat podobně jako  **YourSoutionName.database.windows.net**.
   * Vaše databáze **uživatelské jméno** a **heslo** jsou stejné jako uživatelské jméno a heslo dříve zaznamenaných během nasazení řešení.
2. Aktualizujte zdroj dat souboru sestavy neaktivní trase s Power BI Desktop.
   
   * Ve složce, které jste stáhli a unzipped soubor generátor, dvakrát klikněte **PowerBI\\PredictiveMaintenanceAerospace.pbix** souboru. Pokud se při otevření souboru se zobrazí všechny zprávy upozornění, je ignorujte. Nahoře soubor, klikněte na **'upravit dotazy**.
     
     ![Úpravy dotazů](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Uvidíte dvě tabulky **RemainingUsefulLife** a **PMResult**. Vyberte první tabulky a klikněte na ![ikona nastavení dotazu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) vedle **'Source'** pod **"použít postup** na pravé straně **'Nastavení dotazu'** panely. Ignorujte všechny zprávy upozornění, které se zobrazují.
   * V okně pop, nahraďte **"Server"** a **'Databázi'** s vlastními názvy serveru a databáze a pak klikněte na **"OK"**. Pro název serveru, je nutné zadat port 1433 (**YourSoutionName.database.windows.net, 1433**). Ponechat toto pole databáze jako **pmaintenancedb**. Ignorujte upozornění, které se zobrazují na obrazovce.
   * V dalším okně pop, uvidíte dvě možnosti, v levém podokně (**Windows** a **databáze**). Klikněte na tlačítko **'Databázi'**, vyplňte vaše **'Uživatelského jména'** a **'Password'** (Toto je uživatelské jméno a heslo, které jste zadali při prvním nasazení řešení a vytvořit Azure Databáze SQL). V ***vyberte, kterou úroveň použít tato nastavení***, zkontrolujte úrovně možnost databáze. Pak klikněte na tlačítko **"Připojit"**.
   * Klikněte na druhé tabulky **PMResult** klikněte ![navigační ikonu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) vedle **'Source'** pod **"použít postup** na pravé straně **'Nastavení dotazu'** panelu a aktualizovat název serveru a databáze jako výše uvedené kroky a klikněte na tlačítko OK.
   * Jakmile jste se na základě zpět na předchozí stránku, zavřete okno. Zobrazí se zpráva – klikněte na tlačítko **použít**. Nakonec kliknutím **Uložit** tlačítko a uložte změny. Souboru Power BI je nyní navázat připojení k serveru. Pokud vaše vizualizace jsou prázdná, zkontrolujte, zda že vymazat výběry na vizualizaci, která bude vizualizovat všechna data kliknutím na ikonu mazání v pravém horním rohu legend. Použijte tlačítko Aktualizovat, aby odpovídala nová data v vizualizacemi. Na začátku jenom zobrazit počáteční hodnoty data na vaše vizualizace jako objekt pro vytváření dat je naplánovaná aktualizace každých 3 hodiny. Po 3 hodiny zobrazí se nové předpovědi promítnuta vaší vizualizace při aktualizaci data.
3. (Volitelné) Publikovat k řídicímu neaktivní trase [Power BI online](http://www.powerbi.com/). Všimněte si, že tento krok vyžaduje účet Power BI (nebo účtu Office 365).
   
   * Klikněte na tlačítko **"Publikovat"** a později několik sekund zobrazí se okno zobrazení "Publikování Power BI úspěchu!" s zelená značka zaškrtnutí. Kliknutím na odkaz níže "Otevřete PredictiveMaintenanceAerospace.pbix v Power BI". Podrobné pokyny najdete v části [publikování z Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Chcete-li vytvořit nový řídicí panel: klikněte na tlačítko **+** přihlásit do **řídicí panely** část v levém podokně. Zadejte název "Prediktivní údržby ukázku" pro tento nový řídicí panel.
   * Jakmile otevřete sestavu, klikněte na tlačítko ![ikonu PŘIPNUTÍ](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) připnete všemi vizualizacemi na řídicí panel. Podrobné pokyny najdete v části [dlaždici připnout na řídicí panel Power BI ze sestavy](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Přejděte na stránku řídicího panelu a upravit velikost a umístění vaší vizualizace a upravit jejich názvy. Podrobné pokyny o tom, jak upravit dlaždice naleznete v tématu [Upravit vedle sebe - změny velikosti, přesunout, přejmenovat, kódu pin, odstranit, přidání hypertextového odkazu](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Zde je příklad řídicí panel s vizualizacemi některé neaktivní trase připnuli k němu.  V závislosti na tom, jak dlouho spustíte generátor vaše data může být vaše čísla na vizualizacemi jiný.
     <br/>
     ![Poslední zobrazení](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Chcete naplánovat aktualizaci dat, umístěte ukazatel myši nad **PredictiveMaintenanceAerospace** datovou sadu, klikněte na tlačítko ![tří teček ikonu](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) a potom zvolte **naplánovat aktualizaci**.
     <br/>
     **Poznámka:** Pokud se zobrazí upozornění masáž, klikněte na tlačítko **upravit pověření** a zkontrolujte, zda vaše přihlašovací údaje databáze jsou stejné jako popsané v kroku 1.
     <br/>
     ![Plán aktualizace](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Rozbalte **naplánovat aktualizaci** části. Zapněte "zachovávat aktuálnost dat".
     <br/>
   * Naplánujte aktualizace na základě potřeb. Další informace naleznete v tématu [aktualizace dat v Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Instalační program aktivní trase řídicí panel
Následující kroky vás postupy k vizualizaci dat výstup z úlohy Stream Analytics, které byly vygenerovány v době nasazení řešení. A [Power BI online](http://www.powerbi.com/) účet je potřeba provést následující kroky. Pokud účet nemáte, můžete [vytvořit](https://powerbi.microsoft.com/pricing).

1. Přidáte výstup Power BI v Azure Stream Analytics (ASA).
   
   * Postupujte podle pokynů v [Azure Stream Analytics & Power BI: panelu analýzy pro streamování dat v reálném čase viditelnost](../../stream-analytics/stream-analytics-power-bi-dashboard.md) nastavit výstup úlohy Azure Stream Analytics jako řídicí panel Power BI.
   * Dotaz ASA má tři výstupů, které jsou **aircraftmonitor**, **aircraftalert**, a **flightsbyhour**. Dotaz můžete zobrazit kliknutím na kartu dotaz. Odpovídající každé z těchto tabulek, je nutné přidat výstup do ASA. Když přidáte první výstup (**aircraftmonitor**) zajistěte, aby **Alias pro výstup**, **název datové sady** a **název tabulky** jsou stejné (**aircraftmonitor**). Opakujte postup pro přidání výstupy pro **aircraftalert**, a **flightsbyhour**. Jakmile jste přidali všechny tři výstupní tabulky a spustit úlohy ASA, měli byste obdržet zprávu s potvrzením ("spuštění služby Stream Analytics úlohy maintenancesa02asapbi bylo úspěšné").
2. Přihlaste se k [Power BI online](http://www.powerbi.com)
   
   * Na levém panelu části datové sady v pracovním prostoru ***datovou sadu*** názvy **aircraftmonitor**, **aircraftalert**, a **flightsbyhour** by se zobrazit. Toto je dat, která se poslat ze služby Azure Stream Analytics v předchozím kroku. Datová sada **flightsbyhour** nemusí být uvedeny ve stejnou dobu jako další dvě datové sady vzhledem k povaze příkazu jazyka SQL za ní. Ale by měl zobrazit za hodinu.
   * Zajistěte, aby ***vizualizace*** podokně je otevřený a je zobrazena na pravé straně obrazovky.
3. Jakmile máte dat odesílaných do Power BI, můžete začít vizualizace dat. Níže je příklad řídicí panel s vizualizacemi některé aktivní trase připnuli k němu. Můžete vytvořit další dlaždice řídicího panelu podle příslušné datové sady. V závislosti na tom, jak dlouho spustíte generátor vaše data může být vaše čísla na vizualizacemi jiný.

    ![Zobrazení řídicího panelu](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

1. Tady jsou některé kroky k vytvoření jednoho z výše uvedených – dlaždice "loďstva zobrazení senzor 11 vs. Prahová hodnota šířka 48,26" dlaždice:
   
   * Klikněte na datovou sadu **aircraftmonitor** na levém panelu části datové sady.
   * Klikněte **spojnicový graf** ikonu.
   * Klikněte na tlačítko **zpracovaných** v **pole** tak, aby se zobrazí v části "Osou" v podokně **vizualizace** podokně.
   * Klikněte na tlačítko "s.11" a "s.11\_výstraha" tak, aby obě vyskytovat v části "Hodnoty". Klikněte na malou šipku vedle **s.11** a **s.11\_výstraha**, změňte "Sum" na "Průměr".
   * Klikněte na tlačítko **Uložit** nahoře a název sestavy "aircraftmonitor." Sestavy s názvem "aircraftmonitor" se zobrazí v **sestavy** tématu **Navigátor** podokna na levé straně.
   * Klikněte **Pin Visual** ikonu v pravém horním rohu tento spojnicový graf. Okno "Kód Pin pro řídicí panel" může zobrazovat můžete vybrat řídicí panel. Vyberte "Prediktivní údržby ukázku" a pak klikněte na tlačítko "Pin".
   * Najeďte myší tuto dlaždici na řídicím panelu, klikněte na ikonu "upravit" v pravém horním rohu změnit její název do "loďstva zobrazení senzor 11 vs. Prahová hodnota šířka 48,26" a subtitle na"Průměr napříč firemního vozového v čase."

## <a name="delete-your-solution"></a>Odstranit řešení
Ujistěte se, můžete zastavit generátor dat, když není aktivně pomocí řešení, jako spuštění generátoru dat bude způsobit vyšší náklady. Pokud ji nepoužíváte, odstraňte řešení. Odstraňování řešení odstraní všechny součásti, které jsou zřízené v rámci vašeho předplatného, při nasazení řešení. Pokud chcete odstranit řešení, klikněte na název řešení na levém panelu řešení šablony a pak klikněte na tlačítko **odstranit**.

## <a name="cost-estimation-tools"></a>Odhad nákladů nástroje
Tyto dva nástroje jsou k dispozici vám pomůžou lépe pochopit celkové náklady spojené systémem prediktivní Údržba leteckých řešení šablony ve vašem předplatném:

* [Nástroj odhadu náklady na Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure náklady odhadu Tool (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

