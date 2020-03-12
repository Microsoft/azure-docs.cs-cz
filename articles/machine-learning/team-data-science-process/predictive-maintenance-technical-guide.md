---
title: Návod pro prediktivní Údržba pro letectví - vědecké zpracování týmových dat
description: Technický průvodce šablonou řešení pro prediktivní údržbu v programu Aerospace, nástrojích a dopravě.
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
ms.openlocfilehash: 3b3a0b00ee6e1e170023584c2e643a5802166428
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087653"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Technický průvodce řešením pro prediktivní údržbu v programu Aerospace

> [!Important]
> Tento článek je zastaralá. Diskuze o prediktivní údržbě v Aerospace je stále relevantní, ale aktuální informace najdete v tématu [Přehled řešení pro obchodní publikum](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Šablony řešení jsou navržené tak, aby urychlily proces vytváření E2E ukázky. Nasazená šablona zřídí vaše předplatné s potřebnými komponentami a pak sestaví vztahy mezi nimi. Nasazení také nasazuje datový kanál s ukázkovými daty z aplikace generátor dat, který si stáhnete a nainstalujete na místním počítači po nasazení šablony řešení. Data z generátor hydráty datového kanálu a spuštění generování predikcí strojového učení, které lze následně vizualizovat na řídicím panelu Power BI.

Proces nasazení provede několik kroků pro nastavení přihlašovacích údajů vašeho řešení. Zajistěte, aby že záznam přihlašovací údaje, jako je například název řešení, uživatelské jméno a heslo, které zadáte během nasazení. 


Cíle tohoto článku je:
- Popište referenční architektura a komponenty, které jsou zřízené v rámci vašeho předplatného.
- Ukazují, jak nahradit ukázkových dat s vlastními daty. 
- Ukazují, jak upravit šablonu řešení.  

## <a name="overview"></a>Přehled
![Architektura prediktivní údržby](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Když řešení nasadíte, aktivuje služby Azure, včetně centra událostí, Stream Analytics, HDInsight, Data Factory a Machine Learning. Diagram architektury ukazuje, jak vytvořit prediktivní Údržba leteckých šablony řešení. Můžete prozkoumat tyto služby na webu Azure Portal kliknutím v diagramu šablonu řešení vytvořili s nasazením řešení (s výjimkou HDInsight, které je zřízená na vyžádání, když jsou potřeba ke spouštění aktivit souvisejících kanálu a jsou Odstranit později).
Stáhněte si [verzi diagramu v plné velikosti](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Následující části popisují části řešení.

## <a name="data-source-and-ingestion"></a>Zdroj dat a přijímání
### <a name="synthetic-data-source"></a>Zdroj dat syntetického
V této šabloně se použitý zdroj dat generuje ze stažené aplikace klasické pracovní plochy, kterou spouštíte místně po úspěšném nasazení.

K vyhledání pokynů ke stažení a instalace této aplikace, vyberte první uzel generátor dat prediktivní údržby, v diagramu šablonu řešení. Pokyny jsou součástí na panelu Vlastnosti. Tato aplikace přesměruje službu [centra událostí Azure](#azure-event-hub) s datovými body nebo událostmi, které se používají ve zbytku toku řešení. Tento zdroj dat je odvozen z veřejně dostupných dat z [úložiště dat NASA](https://c3.nasa.gov/dashlink/resources/139/) pomocí [sady dat simulace degradace modulu Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Generování aplikace události naplní Azure Event Hubs pouze tehdy, když je spuštěn v počítači.  

### <a name="azure-event-hub"></a>Azure Event Hub  
Služba [centra událostí Azure](https://azure.microsoft.com/services/event-hubs/) je příjemcem vstupu poskytnutého syntetickým zdrojem dat.

## <a name="data-preparation-and-analysis"></a>Příprava dat a analýza  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Pomocí [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) můžete poskytnout analýzy téměř v reálném čase pro vstupní datový proud ze služby [Azure Event hub](#azure-event-hub) . Výsledky pak publikujete na řídicí panel [Power BI](https://powerbi.microsoft.com) a také archivujte všechny nezpracované příchozí události do služby [Azure Storage](https://azure.microsoft.com/services/storage/) pro pozdější zpracování službou [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hdinsight-custom-aggregation"></a>Vlastní agregační HDInsight
Spouštějte skripty pro [podregistr](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (Orchestrované pomocí Azure Data Factory) pomocí HDInsight k poskytnutí agregací nezpracovaných událostí archivovaných pomocí prostředku Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Předpovědi se na zbývající užitečnou životnost (RUL) konkrétního leteckého motoru pomocí vstupů přijatých ve [službě Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (Orchestrované pomocí Azure Data Factory). 

## <a name="data-publishing"></a>Publikování dat
### <a name="azure-sql-database"></a>Azure SQL Database
Pomocí [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) můžete ukládat předpovědi obdržené Azure Machine Learning, které se pak spotřebovávají na řídicím panelu [Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>Využití dat
### <a name="power-bi"></a>Power BI
Pomocí [Power BI](https://powerbi.microsoft.com) můžete zobrazit řídicí panel obsahující agregace a výstrahy, které poskytuje [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), a také RUL předpovědi uložené v [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) , které byly vytvořeny pomocí [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Jak umožňuje přinést si vlastní data
Tato část popisuje, jak přineste si vlastní data do Azure a oblasti, které vyžadují změny dat, než převedete do této architektury.

Je pravděpodobné, že datová sada odpovídá datové sadě používané [sadou dat simulace degradace modulu Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) , která se používá pro tuto šablonu řešení. Vysvětlení dat a požadavky jsou zásadní v tom, jak změnit tato šablona fungovala s vlastními daty. 

Následující části popisují části šablony, které vyžadují změny, když je zavedená nová datová sada.

### <a name="azure-event-hub"></a>Azure Event Hub
Centrum událostí Azure je obecný; data mohou být účtovány do centra ve formátu CSV nebo JSON. Neproběhne žádné speciální zpracování ve službě Azure Event Hub, ale je důležité pochopit, data, která se zobrazí na něj.

Tento dokument nepopisuje přijímat data, ale můžete snadno odesílat události nebo data do centra událostí Azure pomocí rozhraní API centra událostí.

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Použijte prostředek Azure Stream Analytics k poskytnutí analýz téměř v reálném čase, a to čtením z datových proudů a výstupem dat do libovolného počtu zdrojů.

Prediktivní Údržba leteckých šablony řešení dotazu Azure Stream Analytics se skládá z čtyři sub dotazy, každý dotaz spotřebovávajících událostí ze služby Azure Event Hubs, se výstupy do čtyř různých umístění. Tyto výstupy se skládají ze tří datových sad Power BI a jednoho umístění úložiště Azure.

Dotaz Azure Stream Analytics můžete zobrazit tak:

* Připojte se k webu Azure portal
* Vyhledání Stream Analyticsch úloh ![Stream Analytics ikony](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png), které byly generovány při nasazení řešení (*například* **maintenancesa02asapbi** a **maintenancesa02asablob** pro řešení prediktivní údržby)
* Výběr
  
  * ***Vstupy*** pro zobrazení vstupu dotazu
  * ***Dotaz*** pro zobrazení samotného dotazu
  * ***Výstupy*** pro zobrazení různých výstupů

Informace o vytváření dotazů Azure Stream Analytics najdete v [odkazu na dotaz Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) na webu MSDN.

Dotazy v tomto řešení výstupní tři datové sady s téměř v reálném čase analýzy informace o příchozí datový proud na řídicí panel Power BI poskytuje jako součást Tato šablona řešení. Protože implicitní znalosti o formátu příchozí data se tyto dotazy musí být změněn podle datový formát.

Dotaz ve druhé Stream Analytics úloze **maintenancesa02asablob** jednoduše vypíše všechny události [centra událostí](https://azure.microsoft.com/services/event-hubs/) a [Azure Storage](https://azure.microsoft.com/services/storage/) , takže nevyžaduje žádnou změnu bez ohledu na formát dat, protože úplné informace o události jsou streamované do úložiště.

### <a name="azure-data-factory"></a>Azure Data Factory
Služba [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestruje přesun a zpracování dat. V šabloně řešení prediktivní údržby pro Aerospace se Datová továrna skládá ze tří [kanálů](../../data-factory/concepts-pipelines-activities.md) , které přesouvají a zpracovávají data pomocí různých technologií.  Přístup k vaší datové továrny tak, že otevřete uzel služby Data Factory v dolní části diagramu šablony řešení vytvořené pomocí nasazení řešení. Chyby v rámci vaší datové sady z důvodu služby data factory se nasazují předtím, než byl spuštěn generátor dat. Tyto chyby můžete ignorovat a nezabrání svou datovou továrnu funguje

![Chyby datové sady data Factory](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Tato část popisuje potřebné [kanály a aktivity](../../data-factory/concepts-pipelines-activities.md) obsažené v [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Zde je diagram tohoto řešení.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Dva z těchto kanálů tohoto objektu [Factory obsahují skripty](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pro oddíly, které se používají k dělení a agregaci dat. V případě popsaných se skripty nacházejí v účtu [Azure Storage](https://azure.microsoft.com/services/storage/) vytvořeném při instalaci. Jejich umístění je: maintenancesascript\\\\skriptu\\\\podregistr\\\\ (nebo https://[název vašeho řešení]. blob. Core. Windows. NET/maintenancesascript).

Podobně jako u [Azure Stream Analytics](#azure-stream-analytics-1) dotazů mají skripty v [podregistru](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) implicitní znalosti formátu příchozích dat a musí se měnit v závislosti na vašem datovém formátu.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Tento [kanál](../../data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu aktivity [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , která spouští skript [podregistru](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) , který umožňuje rozdělit data vložená do [Azure Storage](https://azure.microsoft.com/services/storage/) během [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) úlohy.

Skript [podregistru](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pro tento úkol dělení na oddíly je ***AggregateFlightInfo. HQL.***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Tento [kanál](../../data-factory/concepts-pipelines-activities.md) obsahuje několik aktivit, jejichž konečný výsledek je předpovědi skóre z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentu přidruženého k této šabloně řešení.

Aktivity zahrnuté jsou:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivitu pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , který spouští skript v [podregistru](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) , aby prováděl agregace a funkce pro vývoj funkcí, které jsou nezbytné pro [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentování.
  Skript [podregistru](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pro tento úkol dělení na oddíly je ***PrepareMLInput. HQL***.
* Aktivita [kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) , která přesouvá výsledky z aktivity [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) do jediného objektu BLOB [Azure Storage](https://azure.microsoft.com/services/storage/) , k němuž přistupovala aktivita [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .
* Aktivita [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) zavolá [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment s výsledky, které jsou umístěny v jednom [Azure Storage](https://azure.microsoft.com/services/storage/) objektu BLOB.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Tento [kanál](../../data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – aktivitu [kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) , která přesouvá výsledky [Azure Machine Learning](#azure-machine-learning) experiment z ***MLScoringPipeline*** do [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) zřízeného v rámci instalace šablony řešení.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment, který se používá pro tuto šablonu řešení, poskytuje zbývající životnost (RUL) motoru letadla. Experiment je specifická pro datové sady spotřebované a vyžaduje úpravy nebo nahrazení specifické pro data režimu.

## <a name="monitor-progress"></a>Sledování průběhu
Po spuštění generátor dat kanálu začne nechávají dehydratovat a různých komponent řešení start aktivace do akce následující příkazy vydané službou data factory. Existují dva způsoby a začněte monitorovat kanál.

* Jedna z úloh Stream Analytics nezpracované příchozí data zapíše do úložiště objektů blob. Pokud kliknete na Blob Storage součásti řešení na obrazovce, kterou jste úspěšně nasadili řešení, a pak kliknete na tlačítko otevřít v pravém panelu, přejdete na [Azure Portal](https://portal.azure.com/). Potom klikněte na objekty BLOB. V panelu Další uvidíte seznam kontejnerů. Klikněte na **maintenancesadata**. Na dalším panelu je složka **rawData** . Ve složce rawdata jsou složky s názvy, například hodiny = 17 a hodina = 18. Označuje výskyt těchto složek nezpracovaných dat se vygeneruje ve vašem počítači a ukládají ve službě blob storage. Soubory csv s konečnou velikost v MB v těchto složkách, byste měli vidět.
* Posledním krokem kanálu je zápis dat (třeba predikcím ze strojového učení) do služby SQL Database. Budete muset počkat maximálně tři hodiny pro zobrazení dat ve službě SQL Database. Jedním ze způsobů, jak monitorovat, kolik dat je k dispozici ve vašem SQL Database, je prostřednictvím [Azure Portal](https://portal.azure.com/). Na levém panelu vyhledejte databáze SQL ![ikona SQL](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) a klikněte na ni. Pak vyhledejte databázi **pmaintenancedb** a klikněte na ni. Na další stránce v dolní části klikněte na tlačítko na MANAGE
   
    ![Ikona Správa](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Tady můžete kliknout na nový dotaz a dotaz na počet řádků (například vyberte count(*) z PMResult). S růstem databáze, měli byste zvýšit počet řádků v tabulce.

## <a name="power-bi-dashboard"></a>Řídicí panel Power BI

Nastavte řídicí panel Power BI k vizualizaci dat Azure Stream Analytics (horká cesta) a batch výsledky předpovědí z aplikace Azure machine learning (studená cesta).

### <a name="set-up-the-cold-path-dashboard"></a>Nastavit řídicí panel studené cesty
V kanálu dat studené cesty je cílem získat prediktivní RUL (zbývající doba životnosti) každý motor letadla po dokončení letu (cyklu). Předpověď výsledků je aktualizována každé 3 hodiny pro predikci letecké motory, které dokončení let během poslední 3 hodiny.

Power BI se k Azure SQL Database připojí jako zdroj dat, kde jsou uložené výsledky předpovědi. 

Poznámka: 
1.    Při nasazení vašeho řešení se v databázi zobrazí předpověď do 3 hodin. Souboru pbix, která byla součástí ke stažení generátor obsahuje počáteční hodnoty data, takže můžete hned vytvořit řídicí panel Power BI. 
2.    V tomto kroku je nutné stáhnout a nainstalovat bezplatný software [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Následující kroky vás o tom, jak připojit soubor pbix do SQL Database, která byla spuštěné v době nasazení řešení obsahující data (například výsledky předpovědí) za účelem vizualizace.

1. Získáte přihlašovací údaje databáze.
   
   Před přechodem k dalším krokům budete potřebovat **název databázového serveru, název databáze, uživatelské jméno a heslo** . Tady jsou kroky a provede vás postupy je najít.
   
   * Po **' Azure SQL Database '** v diagramu šablony řešení se změní na zelený, klikněte na něj a potom klikněte na **otevřít**.
   * Zobrazí se nová karta nebo okno prohlížeče, které zobrazí stránku Azure Portal. Na levém panelu klikněte na **skupiny prostředků** .
   * Vyberte předplatné, které používáte pro nasazení řešení, a pak vyberte **YourSolutionName\_Resource**.
   * Na panelu Místní vyjmutí klikněte na ikonu ![SQL](./media/predictive-maintenance-technical-guide/icon-sql.png) ikona pro přístup k databázi. Název databáze je vedle této ikony (například **' pmaintenancedb '** ) a **název databázového serveru** je uveden ve vlastnosti název serveru a měl by vypadat podobně jako **YourSolutionName.Database.Windows.NET**.
   * **Uživatelské jméno** a **heslo** databáze jsou stejné jako uživatelské jméno a heslo, které jste dříve nahráli během nasazování řešení.
2. Aktualizujte zdroj dat ze studené cesty souboru sestavy v Power BI Desktopu.
   
   * Ve složce, do které jste stáhli a stáhnete soubor generátoru, poklikejte na soubor **PowerBI\\PredictiveMaintenanceAerospace. pbix** . Pokud všechny zprávy upozornění se zobrazí při otevření souboru, je ignorujte. V horní části souboru klikněte na **Upravit dotazy**.
     
     ![Upravit dotazy](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Zobrazí se dvě tabulky, **RemainingUsefulLife** a **PMResult**. Vyberte první tabulku a klikněte na tlačítko ![ikonu nastavení dotazu](./media/predictive-maintenance-technical-guide/icon-query-settings.png) vedle **položky zdroj** v části **použité kroky** na pravé straně panelu **nastavení dotazu** . Ignorujte jakékoli upozornění, které se zobrazí.
   * V okně místní nabídky nahraďte **' Server '** a **' Database '** vlastními názvy serverů a databází a potom klikněte na tlačítko **' OK '** . V poli název serveru se ujistěte, že zadáváte port 1433 (**YourSolutionName.Database.Windows.NET, 1433**). Pole databáze nechte **pmaintenancedb**. Ignorujte upozornění, které se zobrazí na obrazovce.
   * V dalším okně místní nabídky se zobrazí dvě možnosti v levém podokně (**Windows** a **databáze**). Klikněte na **databáze**, vyplňte **uživatelské** jméno a **heslo** (uživatelské jméno a heslo, které jste zadali při prvním nasazení řešení a vytvořili Azure SQL Database). V možnosti vybrat úroveň, pro kterou ***chcete použít tato nastavení***, zaškrtněte políčko na úrovni databáze. Pak klikněte na **připojit**.
   * Klikněte na druhou tabulku **PMResult** a pak klikněte na navigační ikonu ![](./media/predictive-maintenance-technical-guide/icon-navigation.png) vedle položky **zdroj** v části **použité kroky** na pravé straně **nastavení dotazu** a aktualizujte názvy serverů a databází jako v předchozích krocích a klikněte na OK.
   * Jakmile budete s asistencí zpět na předchozí stránku, zavřete okno. Zobrazí se zpráva – klikněte na **použít**. Nakonec kliknutím na tlačítko **Uložit** změny uložte. Váš soubor Power BI má nyní navázalo se připojení k serveru. Pokud vaše vizualizace jsou prázdné, nezapomeňte že vymazat výběry na vizualizace, která bude vizualizovat všechna data kliknutím na ikonu gumy v pravém horním rohu legend. Pomocí tlačítka aktualizovat tak, aby odrážely nová data na vizualizace. Na začátku zobrazí jenom data počáteční hodnoty na vaše vizualizace jako služby data factory je naplánovaná aktualizace každé 3 hodiny. Po 3 hodiny zobrazí se nových předpovědí projeví ve svých vizualizacích při aktualizaci data.
3. Volitelné Publikujte řídicí panel pro studenou cestu a [Power BI online](https://www.powerbi.com/). Tento krok vyžaduje účet Power BI (nebo účet Office 365).
   
   * Klikněte na tlačítko **' publikovat '** a později se zobrazí okno se zobrazením "publikování Power BI úspěšné!". s zelená značka zaškrtnutí. Klikněte na odkaz níže "Otevřít PredictiveMaintenanceAerospace.pbix v Power BI". Podrobné pokyny najdete v tématu [publikování z Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Vytvoření nového řídicího panelu: v levém podokně klikněte na symbol **+** vedle části **řídicí panely** . Zadejte název "Ukázka prediktivní údržby" pro tento nový řídicí panel.
   * Po otevření sestavy klikněte ![ikonu připnutí](./media/predictive-maintenance-technical-guide/icon-pin.png) a připněte všechny vizualizace na řídicí panel. Podrobné pokyny najdete v tématu [připnutí dlaždice na řídicí panel Power BI ze sestavy](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Přejděte na stránku řídicího panelu a upravte velikost a umístění vašich vizualizací a upravit jejich názvy. Podrobné pokyny k úpravám dlaždic najdete v tématu [Úprava dlaždice – Změna velikosti, přesunutí, přejmenování, připnutí, odstranění, přidání hypertextového odkazu](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Tady je příklad řídicího panelu se k němu připnuté vizualizace studené cesty.  V závislosti na tom, jak dlouho spuštění vaší generátor dat se může lišit čísla na vizualizace.
     <br/>
     ![finální zobrazení](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Pokud chcete naplánovat aktualizaci dat, najeďte myší na **PredictiveMaintenanceAerospace** datovou sadu, klikněte na ![ikonu tří teček](./media/predictive-maintenance-technical-guide/icon-elipsis.png) a pak zvolte **naplánovat aktualizaci**.
     <br/>
     **Poznámka:** Pokud se zobrazí upozornění Massage, klikněte na **Upravit přihlašovací údaje** a ujistěte se, že jsou vaše přihlašovací údaje databáze stejné, jako ty popsané v kroku 1.
     <br/>
     ![naplánovat aktualizaci](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Rozbalte oddíl **naplánovat aktualizaci** . Zapněte "zachovávat aktuálnost dat".
     <br/>
   * Naplánujte aktualizaci na základě vašich potřeb. Další informace najdete [v tématu aktualizace dat v Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Nastavení řídicího panelu kritickou cestu
Následující kroky vás provedou, jak vizualizovat výstup dat z úloh Stream Analytics, které byly vytvořeny v době nasazení řešení. K provedení následujících kroků je nutný [Power BI online](https://www.powerbi.com/) účet. Pokud účet nemáte, můžete [ho vytvořit](https://powerbi.microsoft.com/pricing).

1. Přidáte výstup Power BI v Azure Stream Analytics (ASA).
   
   * Je nutné postupovat podle pokynů v tématu [Azure Stream Analytics & Power BI: řídicí panel analýzy pro zobrazení dat streamování v reálném čase](../../stream-analytics/stream-analytics-power-bi-dashboard.md) , který umožňuje nastavit výstup úlohy Azure Stream Analytics jako řídicí panel Power BI.
   * Dotaz ASA obsahuje tři výstupy, které jsou **aircraftmonitor**, **aircraftalert**a **flightsbyhour**. Dotaz můžete zobrazit kliknutím na kartu dotaz. odpovídající jednotlivým tabulkám musíte přidat výstup do ASA. Když přidáte první výstup (**aircraftmonitor**), zajistěte, aby byl **alias výstupu**, **název datové sady** a **název tabulky** stejný (**aircraftmonitor**). Opakováním kroků přidejte výstupy pro **aircraftalert**a **flightsbyhour**. Po přidání všech tří výstupní tabulky a spuštění úlohy Azure Stream Analytics, měli byste obdržet potvrzovací zpráva ("spouští se Stream Analytics úlohy maintenancesa02asapbi bylo úspěšné").
2. Přihlášení k [Power BI online](https://www.powerbi.com)
   
   * V levém oddílu datových sad na levém panelu v části pracovní prostor se musí zobrazit názvy ***datových sad*** **aircraftmonitor**, **aircraftalert**a **flightsbyhour** . Toto je streamovaných dat, které jste nasdíleli z Azure Stream Analytics v předchozím kroku. Datová sada **flightsbyhour** se nemůže zobrazit současně s ostatními dvěma datovými sadami z důvodu povaze dotazu SQL za ním. Nicméně by se měla zobrazit za hodinu.
   * Ujistěte se, že je podokno ***vizualizace*** otevřené a zobrazuje se na pravé straně obrazovky.
3. Jakmile se data přenášejí do Power BI, můžete začít vizualizace streamovaná data. Níže je příklad řídicího panelu s vizualizacemi některé kritickou cestu k němu připnuté. Můžete vytvořit další řídicí panel dlaždice založené na příslušné datové sady. V závislosti na tom, jak dlouho spuštění vaší generátor dat se může lišit čísla na vizualizace.

    ![Zobrazení řídicího panelu](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Tady je několik kroků, jak vytvořit jednu z dlaždic – "zobrazení loďstva snímače 11 vs. Threshold 48,26" dlaždice:
   
   * V části datové sady na levém panelu klikněte na datová sada **aircraftmonitor** .
   * Klikněte na ikonu **spojnicového grafu** .
   * V podokně **pole** klikněte na **zpracované** a v podokně **vizualizace** se zobrazí v části osa.
   * Klikněte na "S11" a "S11\_Alert", aby se obě zobrazily v části "hodnoty". Klikněte na malou šipku vedle **S11** a **S11 výstrahy\_** a změňte "Sum" na "Average".
   * V horní části klikněte na **Save (Uložit** ) a pojmenujte sestavu "aircraftmonitor". Sestava s názvem "aircraftmonitor" je uvedena v části **sestavy** v podokně **navigátor** na levé straně.
   * Klikněte na ikonu **připnout vizuál** v pravém horním rohu tohoto spojnicového grafu. Můžete zvolit řídicího panelu může zobrazit okno "Připnout na řídicí panel". Vyberte "Ukázka prediktivní údržby" a pak klikněte na tlačítko "Pin".
   * Najeďte myší na tuto dlaždici na řídicím panelu, klikněte na ikonu Upravit v pravém horním rohu a změňte její název na "zobrazení loďstva snímače 11 vs. prahová hodnota 48,26" a podnadpis na "průměr v rámci loďstva v průběhu času".

## <a name="delete-your-solution"></a>Odstranit řešení
Ujistěte se zastavit generátor dat, když aktivně nepoužíváte řešení jako provoz generátoru dat bude mít za následek vyšší náklady. Pokud ji nepoužíváte, odstraňte řešení. Odstraňuje se řešení odstraní všechny součásti, které jsou zřízené ve vašem předplatném při nasazení řešení. Řešení odstraníte tak, že kliknete na název vašeho řešení na levém panelu šablony řešení a potom kliknete na **Odstranit**.

## <a name="cost-estimation-tools"></a>Nástroje pro odhad nákladů
Následující dva nástroje jsou k dispozici vám pomůže lépe pochopit celkové náklady při spuštění prediktivní Údržba leteckých šablony řešení ve vašem předplatném:

* [Nástroj Microsoft Azure cost Estimator (online)](https://azure.microsoft.com/pricing/calculator/)
* [Nástroj Microsoft Azure cost Estimator Tool (Desktop)](https://www.microsoft.com/download/details.aspx?id=43376)

