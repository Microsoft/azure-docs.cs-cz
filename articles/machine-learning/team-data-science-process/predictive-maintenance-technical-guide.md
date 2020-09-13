---
title: Průvodce prediktivní údržbou pro program Aerospace – tým pro vědecké zpracování dat
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
ms.openlocfilehash: 6677f9275d3b6f0569216eb16046d096c574beab
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030897"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Technický průvodce řešením pro prediktivní údržbu v programu Aerospace

> [!Important]
> Tento článek se už nepoužívá. Diskuze o prediktivní údržbě v Aerospace je stále relevantní, ale aktuální informace najdete v tématu [Přehled řešení pro obchodní publikum](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Šablony řešení jsou navržené tak, aby urychlily proces vytváření E2E ukázky. Nasazená šablona zřídí vaše předplatné s potřebnými komponentami a pak sestaví vztahy mezi nimi. Vyhodnotí také kanál dat s ukázkovými daty z aplikace generátoru dat, který po nasazení šablony řešení stáhnete a nainstalujete do místního počítače. Data od generátoru zahájí datový kanál a začnou generovat Machine Learning předpovědi, který se pak může vizuálně vyfiltrovat na řídicím panelu Power BI.

Proces nasazení vás provede několika kroky k nastavení vašich přihlašovacích údajů k řešení. Ujistěte se, že jste si zaznamenali přihlašovací údaje, jako je název řešení, uživatelské jméno a heslo, které zadáte během nasazování. 


Cílem tohoto článku je:
- Popište referenční architekturu a součásti zřízené ve vašem předplatném.
- Ukazují, jak nahradit vzorová data vlastními daty. 
- Ukazuje, jak upravit šablonu řešení.  

## <a name="overview"></a>Přehled
![Architektura prediktivní údržby](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Když řešení nasadíte, aktivuje služby Azure, včetně centra událostí, Stream Analytics, HDInsight, Data Factory a Machine Learning. Diagram architektury ukazuje, jak je vytvořena šablona řešení prediktivní údržba pro Aerospace. Tyto služby můžete prozkoumat v Azure Portal tak, že na ně kliknete v diagramu šablony řešení vytvořeném pomocí nasazení řešení (s výjimkou služby HDInsight, která je zřízená na vyžádání, když je potřeba spustit související aktivity kanálu a následně je odstranit).
Stáhněte si [verzi diagramu v plné velikosti](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Následující části popisují části řešení.

## <a name="data-source-and-ingestion"></a>Zdroj dat a ingestování
### <a name="synthetic-data-source"></a>Syntetický zdroj dat
V této šabloně se použitý zdroj dat generuje ze stažené aplikace klasické pracovní plochy, kterou spouštíte místně po úspěšném nasazení.

Chcete-li najít pokyny ke stažení a instalaci této aplikace, vyberte první uzel a možnost generátor dat prediktivní údržby v diagramu šablon řešení. Pokyny najdete na panelu Vlastnosti. Tato aplikace přesměruje službu [centra událostí Azure](#azure-event-hub) s datovými body nebo událostmi, které se používají ve zbytku toku řešení. Tento zdroj dat je odvozen z veřejně dostupných dat z [úložiště dat NASA](https://c3.nasa.gov/dashlink/resources/139/) pomocí [sady dat simulace degradace modulu Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Aplikace pro generování událostí naplní centrum událostí Azure jenom v době, kdy se provádí na vašem počítači.  

### <a name="azure-event-hub"></a>Azure Event Hub  
Služba [centra událostí Azure](https://azure.microsoft.com/services/event-hubs/) je příjemcem vstupu poskytnutého syntetickým zdrojem dat.

## <a name="data-preparation-and-analysis"></a>Příprava a analýza dat  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Pomocí [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) můžete poskytnout analýzy téměř v reálném čase pro vstupní datový proud ze služby [Azure Event hub](#azure-event-hub) . Výsledky pak publikujete na řídicí panel [Power BI](https://powerbi.microsoft.com) a také archivujte všechny nezpracované příchozí události do služby [Azure Storage](https://azure.microsoft.com/services/storage/) pro pozdější zpracování službou [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hdinsight-custom-aggregation"></a>Vlastní agregace HDInsight
Spouštějte skripty pro [podregistr](https://docs.microsoft.com/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) (Orchestrované pomocí Azure Data Factory) pomocí HDInsight k poskytnutí agregací nezpracovaných událostí archivovaných pomocí prostředku Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Předpovědi se na zbývající užitečnou životnost (RUL) konkrétního leteckého motoru pomocí vstupů přijatých ve [službě Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning/) (Orchestrované pomocí Azure Data Factory). 

## <a name="data-publishing"></a>Publikování dat
### <a name="azure-sql-database"></a>Azure SQL Database
Pomocí [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) můžete ukládat předpovědi obdržené Azure Machine Learning, které se pak spotřebovávají na řídicím panelu [Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>Využití dat
### <a name="power-bi"></a>Power BI
Pomocí [Power BI](https://powerbi.microsoft.com) můžete zobrazit řídicí panel obsahující agregace a výstrahy, které poskytuje [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), a také RUL předpovědi uložené v [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) , které byly vytvořeny pomocí [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Jak přenést vlastní data
Tato část popisuje, jak přenést vlastní data do Azure a jaké oblasti vyžadují změny dat, která do této architektury přinesete.

Je pravděpodobné, že datová sada odpovídá datové sadě používané [sadou dat simulace degradace modulu Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) , která se používá pro tuto šablonu řešení. Porozumění vašim datům a požadavkům je rozhodující způsob, jak tuto šablonu upravit, aby fungovala s vašimi vlastními daty. 

Následující části popisují části šablony, které vyžadují úpravy při zavedení nové datové sady.

### <a name="azure-event-hub"></a>Azure Event Hub
Centrum událostí Azure je obecné. data se dají publikovat do centra buď ve formátu CSV, nebo ve formátu JSON. V centru událostí Azure se neprovádí žádné speciální zpracování, ale je důležité, abyste pochopili data, která do ní dostanou.

Tento dokument nepopisuje, jak přijímat vaše data, ale můžete snadno odesílat události nebo data do centra událostí Azure pomocí rozhraní API centra událostí.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Použijte prostředek Azure Stream Analytics k poskytnutí analýz téměř v reálném čase, a to čtením z datových proudů a výstupem dat do libovolného počtu zdrojů.

Pro šablonu řešení prediktivní údržba pro společnosti Aerospace se Azure Stream Analytics dotaz skládá ze čtyř poddotazů, z nichž každý dotaz spotřebovává události ze služby Azure Event hub, a obsahuje výstupy do čtyř jedinečných umístění. Tyto výstupy se skládají ze tří Power BI datových sad a jednoho Azure Storageho umístění.

Dotaz Azure Stream Analytics lze najít pomocí:

* Připojit k Azure Portal
* Vyhledání Stream Analytics úloh ![ Stream Analytics ikonu ](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) , která byla generována při nasazení řešení (*například* **maintenancesa02asapbi** a **maintenancesa02asablob** pro řešení prediktivní údržby)
* Kliknutím
  
  * ***Vstupy*** pro zobrazení vstupu dotazu
  * ***Dotaz*** pro zobrazení samotného dotazu
  * ***Výstupy*** pro zobrazení různých výstupů

Informace o vytváření dotazů Azure Stream Analytics najdete v [odkazu na dotaz Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) na webu MSDN.

V tomto řešení dotazy nahlásí tři datové sady téměř v reálném čase s informacemi o příchozím datovém proudu na Power BI, které jsou součástí této šablony řešení. Vzhledem k tomu, že existují implicitní znalosti o formátu příchozích dat, je třeba tyto dotazy změnit na základě formátu dat.

Dotaz ve druhé Stream Analytics úloze **maintenancesa02asablob** jednoduše vypíše všechny události [centra událostí](https://azure.microsoft.com/services/event-hubs/) a [Azure Storage](https://azure.microsoft.com/services/storage/) , takže nevyžaduje žádnou změnu bez ohledu na formát dat, protože úplné informace o události jsou streamované do úložiště.

### <a name="azure-data-factory"></a>Azure Data Factory
Služba [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestruje přesun a zpracování dat. V šabloně řešení prediktivní údržby pro Aerospace se Datová továrna skládá ze tří [kanálů](../../data-factory/concepts-pipelines-activities.md) , které přesouvají a zpracovávají data pomocí různých technologií.  Přístup k objektu pro vytváření dat získáte tak, že otevřete uzel Data Factory v dolní části diagramu šablony řešení vytvořené pomocí nasazení řešení. Chyby v rámci vašich datových sad jsou způsobeny tím, že služba Data Factory je nasazena před spuštěním generátoru dat. Tyto chyby je možné ignorovat a nebránit v fungování vaší datové továrny.

![Chyby Data Factory datové sady](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Tato část popisuje potřebné [kanály a aktivity](../../data-factory/concepts-pipelines-activities.md) obsažené v [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Tady je zobrazení diagramu řešení.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Dva z těchto kanálů tohoto objektu [Factory obsahují skripty](https://docs.microsoft.com/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) pro oddíly, které se používají k dělení a agregaci dat. V případě popsaných se skripty nacházejí v účtu [Azure Storage](https://azure.microsoft.com/services/storage/) vytvořeném při instalaci. Jejich umístění je: maintenancesascript \\ \\ \\ \\ podregistr Script \\ \\ (nebo https://[název vašeho řešení]. blob. Core. Windows. NET/maintenancesascript).

Podobně jako u [Azure Stream Analytics](#azure-stream-analytics-1) dotazů mají skripty v [podregistru](https://docs.microsoft.com/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) implicitní znalosti formátu příchozích dat a musí se měnit v závislosti na vašem datovém formátu.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Tento [kanál](../../data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu aktivity [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , která spouští skript [podregistru](https://docs.microsoft.com/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) , který umožňuje rozdělit data vložená do [Azure Storage](https://azure.microsoft.com/services/storage/) během [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) úlohy.

Skript [podregistru](https://docs.microsoft.com/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) pro tento úkol dělení na oddíly je ***AggregateFlightInfo. HQL.***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Tento [kanál](../../data-factory/concepts-pipelines-activities.md) obsahuje několik aktivit, jejichž konečný výsledek je předpovědi skóre z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentu přidruženého k této šabloně řešení.

Mezi zahrnuté aktivity patří:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) aktivitu pomocí [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , který spouští skript v [podregistru](https://docs.microsoft.com/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) , aby prováděl agregace a funkce pro vývoj funkcí, které jsou nezbytné pro [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentování.
  Skript [podregistru](https://docs.microsoft.com/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) pro tento úkol dělení na oddíly je ***PrepareMLInput. HQL***.
* Aktivita [kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) , která přesouvá výsledky z aktivity [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) do jediného objektu BLOB [Azure Storage](https://azure.microsoft.com/services/storage/) , k němuž přistupovala aktivita [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .
* Aktivita [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) zavolá [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment s výsledky, které jsou umístěny v jednom [Azure Storage](https://azure.microsoft.com/services/storage/) objektu BLOB.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Tento [kanál](../../data-factory/concepts-pipelines-activities.md) obsahuje jednu aktivitu – aktivitu [kopírování](https://msdn.microsoft.com/library/azure/dn835035.aspx) , která přesouvá výsledky [Azure Machine Learning](#azure-machine-learning) experiment z ***MLScoringPipeline*** do [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) zřízeného v rámci instalace šablony řešení.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experiment, který se používá pro tuto šablonu řešení, poskytuje zbývající životnost (RUL) motoru letadla. Experiment je specifický pro spotřebovaný datovou sadu a vyžaduje změnu nebo náhradu, která je specifická pro data, která jsou v nástroji.

## <a name="monitor-progress"></a>Průběh monitorování
Po spuštění generátoru dat se kanál začne dehydratované a jednotlivé komponenty vašeho řešení začnou propustit do akce, které následují po příkazech vydaných datovou továrnou. Existují dva způsoby, jak kanál monitorovat.

* Jedna z Stream Analytics úloh zapisuje nezpracovaná příchozí data do úložiště objektů BLOB. Pokud kliknete na Blob Storage součásti řešení na obrazovce, kterou jste úspěšně nasadili řešení, a pak kliknete na tlačítko otevřít v pravém panelu, přejdete na [Azure Portal](https://portal.azure.com/). Potom klikněte na objekty blob. Na dalším panelu vidíte seznam kontejnerů. Klikněte na **maintenancesadata**. Na dalším panelu je složka **rawData** . Ve složce rawData jsou složky s názvy, jako je například hour = 17 a hodina = 18. Přítomnost těchto složek indikuje, že se v počítači generují nezpracovaná data a ukládají se do úložiště objektů BLOB. V těchto složkách byste měli vidět soubory CSV s omezenými velikostmi v MB.
* Posledním krokem kanálu je zápis dat (například předpovědi ze strojového učení) do SQL Database. Aby se data zobrazovala v SQL Database, možná budete muset počkat maximálně tři hodiny. Jedním ze způsobů, jak monitorovat, kolik dat je k dispozici ve vašem SQL Database, je prostřednictvím [Azure Portal](https://portal.azure.com/). Na levém panelu najděte ikonu databáze SQL databases ![ ](./media/predictive-maintenance-technical-guide/icon-SQL-databases.png) a klikněte na ni. Pak vyhledejte databázi **pmaintenancedb** a klikněte na ni. Na další stránce dole klikněte na spravovat.
   
    ![Spravovat ikonu](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Tady můžete kliknout na nový dotaz a dotaz na počet řádků (například vybrat počet (*) z PMResult). Při zvětšování databáze by se měl zvýšit počet řádků v tabulce.

## <a name="power-bi-dashboard"></a>Řídicí panel Power BI

Nastavte řídicí panel Power BI pro vizualizaci dat Azure Stream Analytics (kritická cesta) a výsledků předpovědi dávky z Azure Machine Learning (studená cesta).

### <a name="set-up-the-cold-path-dashboard"></a>Nastavení řídicího panelu pro studenou cestu
V datovém kanálu studené cesty je cílem získat prediktivní RUL (zbývající životnost) každého motoru letadla, jakmile se dokončí letu (cyklus). Výsledek předpovědi se aktualizuje každé 3 hodiny pro předpověď leteckých motorů, které za poslední 3 hodiny dokončily let.

Power BI se k Azure SQL Database připojí jako zdroj dat, kde jsou uložené výsledky předpovědi. 

Poznámka: 
1.    Při nasazení vašeho řešení se v databázi zobrazí předpověď do 3 hodin. Soubor pbix, který byl dodán se stažením generátoru, obsahuje data počátečních dat, abyste mohli řídicí panel Power BI hned vytvořit. 
2.    V tomto kroku je nutné stáhnout a nainstalovat bezplatný software [Power BI Desktop](https://docs.microsoft.com/power-bi/fundamentals/desktop-get-the-desktop).

Následující postup vás provede postupem připojení souboru pbix k SQL Database, který se provedl v době nasazení řešení obsahující data (například výsledky předpovědi) pro vizualizaci.

1. Získat pověření databáze.
   
   Před přechodem k dalším krokům budete potřebovat **název databázového serveru, název databáze, uživatelské jméno a heslo** . Tady jsou kroky, které vám pomohou zjistit, jak je najít.
   
   * Po **' Azure SQL Database '** v diagramu šablony řešení se změní na zelený, klikněte na něj a potom klikněte na **otevřít**.
   * Zobrazí se nová karta nebo okno prohlížeče, které zobrazí stránku Azure Portal. Na levém panelu klikněte na **skupiny prostředků** .
   * Vyberte předplatné, které používáte pro nasazení řešení, a pak vyberte **YourSolutionName \_ Resource**.
   * V novém panelu pro místní vyjmutí klikněte na  ![ ikonu ikona SQL ](./media/predictive-maintenance-technical-guide/icon-sql.png) pro přístup k databázi. Název databáze je vedle této ikony (například **' pmaintenancedb '**) a **název databázového serveru** je uveden ve vlastnosti název serveru a měl by vypadat podobně jako **YourSolutionName.Database.Windows.NET**.
   * **Uživatelské jméno** a **heslo** databáze jsou stejné jako uživatelské jméno a heslo, které jste dříve nahráli během nasazování řešení.
2. Aktualizujte zdroj dat pro soubor sestavy studené cesty pomocí Power BI Desktop.
   
   * Ve složce, do které jste stáhli a stáhnete soubor generátoru, poklikejte na soubor **PowerBI \\ PredictiveMaintenanceAerospace. pbix** . Pokud se při otevření souboru zobrazí nějaké zprávy s upozorněním, ignorujte je. V horní části souboru klikněte na **Upravit dotazy**.
     
     ![Upravit dotazy](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Zobrazí se dvě tabulky, **RemainingUsefulLife** a **PMResult**. Vyberte první tabulku a v ![ pravém podokně nastavení dotazu klikněte na ikonu nastavení dotazu ](./media/predictive-maintenance-technical-guide/icon-query-settings.png) vedle položky **zdroj** v části **použité kroky** . **'Query Settings'** Ignorujte všechny zobrazené varovné zprávy.
   * V okně místní nabídky nahraďte **' Server '** a **' Database '** vlastními názvy serverů a databází a potom klikněte na tlačítko **' OK '**. V poli název serveru se ujistěte, že zadáváte port 1433 (**YourSolutionName.Database.Windows.NET, 1433**). Pole databáze nechte **pmaintenancedb**. Ignorujte varovné zprávy, které se zobrazí na obrazovce.
   * V dalším okně místní nabídky se zobrazí dvě možnosti v levém podokně (**Windows** a **databáze**). Klikněte na **databáze**, vyplňte **uživatelské** jméno a **heslo** (uživatelské jméno a heslo, které jste zadali při prvním nasazení řešení a vytvořili Azure SQL Database). V možnosti vybrat úroveň, pro kterou ***chcete použít tato nastavení***, zaškrtněte políčko na úrovni databáze. Pak klikněte na **připojit**.
   * Klikněte na druhou tabulku **PMResult** a potom klikněte na ![ ikonu navigace ](./media/predictive-maintenance-technical-guide/icon-navigation.png) vedle položky **zdroj** v části **použité kroky** na pravé straně **nastavení dotazu** a aktualizujte názvy serverů a databází jako v předchozích krocích a klikněte na OK.
   * Až budete s přechodem na předchozí stránku, okno zavřete. Zobrazí se zpráva – klikněte na **použít**. Nakonec kliknutím na tlačítko **Uložit** změny uložte. Váš soubor Power BI nyní navázal připojení k serveru. Pokud jsou vaše vizualizace prázdné, ujistěte se, že jste v pravém horním rohu legendy vymazali výběry pro vizualizaci všech dat kliknutím na ikonu gumy. Pomocí tlačítka Aktualizovat můžete odrážet nová data ve vizualizacích. Zpočátku se ve vašich vizualizacích zobrazí jenom data o počátečním stavu, ve kterém se naplánuje aktualizace služby Data Factory každé 3 hodiny. Po 3 hodinách se při aktualizaci dat v vizualizacích zobrazí nový předpovědi.
3. Volitelné Publikujte řídicí panel pro studenou cestu a [Power BI online](https://www.powerbi.com/). Tento krok vyžaduje účet Power BI (nebo pracovní nebo školní účet).
   
   * Klikněte na tlačítko **' publikovat '** a později se zobrazí okno se zobrazením "publikování Power BI úspěšné!". se zeleným znakem zaškrtnutí. Klikněte na odkaz níže "otevřít PredictiveMaintenanceAerospace. pbix v Power BI". Podrobné pokyny najdete v tématu [publikování z Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Vytvoření nového řídicího panelu: **+** v levém podokně klikněte na symbol vedle části **řídicí panely** . Zadejte název "ukázka prediktivní údržby" pro tento nový řídicí panel.
   * Po otevření sestavy klikněte na ![ ikonu Připnout, ](./media/predictive-maintenance-technical-guide/icon-pin.png) aby se všechny vizualizace připnout na řídicí panel. Podrobné pokyny najdete v tématu [připnutí dlaždice na řídicí panel Power BI ze sestavy](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Přejít na stránku řídicího panelu a upravit velikost a umístění vizualizací a upravit jejich názvy. Podrobné pokyny k úpravám dlaždic najdete v tématu [Úprava dlaždice – Změna velikosti, přesunutí, přejmenování, připnutí, odstranění, přidání hypertextového odkazu](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Tady je příklad řídicího panelu s některými vizualizacemi na studenou cestu, na které se připnuté.  V závislosti na tom, jak dlouho spouštíte generátor dat, se vaše čísla ve vizualizacích můžou lišit.
     <br/>
     ![Finální zobrazení](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Pokud chcete naplánovat aktualizaci dat, najeďte myší na **PredictiveMaintenanceAerospace** datovou sadu, klikněte na ![ ikonu se třemi tečkami ](./media/predictive-maintenance-technical-guide/icon-elipsis.png) a pak zvolte **naplánovat aktualizaci**.
     <br/>
     > [!NOTE]
     > Pokud se zobrazí zpráva s upozorněním, klikněte na **Upravit přihlašovací údaje** a ujistěte se, že jsou vaše přihlašovací údaje databáze stejné, jako ty popsané v kroku 1.
     <br/>
     ![Naplánování aktualizace](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Rozbalte oddíl **naplánovat aktualizaci** . Zapněte možnost udržovat data v aktuálním stavu.
     <br/>
   * Naplánujte aktualizaci podle svých potřeb. Další informace najdete [v tématu aktualizace dat v Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Nastavení řídicího panelu pro pochodovou cestu
Následující kroky popisují, jak vizualizovat výstup dat z Stream Analytics úloh, které byly vygenerovány v době nasazování řešení. K provedení následujících kroků je nutný [Power BI online](https://www.powerbi.com/) účet. Pokud účet nemáte, můžete [ho vytvořit](https://powerbi.microsoft.com/pricing).

1. Přidat výstup Power BI v Azure Stream Analytics (ASA).
   
   * Je nutné postupovat podle pokynů v tématu [Azure Stream Analytics & Power BI: řídicí panel analýzy pro zobrazení dat streamování v reálném čase](../../stream-analytics/stream-analytics-power-bi-dashboard.md) , který umožňuje nastavit výstup úlohy Azure Stream Analytics jako řídicí panel Power BI.
   * Dotaz ASA obsahuje tři výstupy, které jsou **aircraftmonitor**, **aircraftalert**a **flightsbyhour**. Dotaz můžete zobrazit kliknutím na kartu dotaz. Odpovídající jednotlivým tabulkám, je nutné přidat výstup do ASA. Když přidáte první výstup (**aircraftmonitor**), zajistěte, aby byl **alias výstupu**, **název datové sady** a **název tabulky** stejný (**aircraftmonitor**). Opakováním kroků přidejte výstupy pro **aircraftalert**a **flightsbyhour**. Po přidání všech tří výstupních tabulek a spuštění úlohy ASA byste měli obdržet potvrzovací zprávu ("spuštění Stream Analytics úlohy maintenancesa02asapbi úspěšné").
2. Přihlášení k [Power BI online](https://www.powerbi.com)
   
   * V levém oddílu datových sad na levém panelu v části pracovní prostor se musí zobrazit názvy ***datových sad*** **aircraftmonitor**, **aircraftalert**a **flightsbyhour** . Toto je streamovaná data, která jste odeslali z Azure Stream Analytics v předchozím kroku. Datová sada **flightsbyhour** se nemůže zobrazit současně s ostatními dvěma datovými sadami z důvodu povaze dotazu SQL za ním. Měl by se ale zobrazovat po celou hodinu.
   * Ujistěte se, že je podokno ***vizualizace*** otevřené a zobrazuje se na pravé straně obrazovky.
3. Jakmile budete mít data toků do Power BI, můžete začít vizualizovat streamovaná data. Níže je příklad řídicího panelu s některými vizualizacemi s aktivními cestami, které jsou k němu připnuté. Na základě příslušných datových sad můžete vytvořit další dlaždice řídicího panelu. V závislosti na tom, jak dlouho spouštíte generátor dat, se vaše čísla ve vizualizacích můžou lišit.

    ![Zobrazení řídicího panelu](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Tady je několik kroků, jak vytvořit jednu z dlaždic – "zobrazení loďstva snímače 11 vs. Threshold 48,26" dlaždice:
   
   * V části datové sady na levém panelu klikněte na datová sada **aircraftmonitor** .
   * Klikněte na ikonu **spojnicového grafu** .
   * V podokně **pole** klikněte na **zpracované** a v podokně **vizualizace** se zobrazí v části osa.
   * Klikněte na "S11" a "S11 \_ Alert", aby se obě zobrazily v části "hodnoty". Klikněte na malou šipku vedle **S11** a **S11 \_ výstrahy**, změňte "Sum" na "Average".
   * V horní části klikněte na **Save (Uložit** ) a pojmenujte sestavu "aircraftmonitor". Sestava s názvem "aircraftmonitor" je uvedena v části **sestavy** v podokně **navigátor** na levé straně.
   * Klikněte na ikonu **připnout vizuál** v pravém horním rohu tohoto spojnicového grafu. Může se zobrazit okno připnout na řídicí panel, ve kterém můžete vybrat řídicí panel. Vyberte možnost prediktivní údržba ukázka a pak klikněte na připnout.
   * Najeďte myší na tuto dlaždici na řídicím panelu, klikněte na ikonu Upravit v pravém horním rohu a změňte její název na "zobrazení loďstva snímače 11 vs. prahová hodnota 48,26" a podnadpis na "průměr v rámci loďstva v průběhu času".

## <a name="delete-your-solution"></a>Odstranění řešení
Ujistěte se, že zadáváte generátor dat, když toto řešení aktivně nepoužíváte, protože při spuštění generátoru dat dojde k vyššímu náklady. Odstraňte řešení, pokud ho nepoužíváte. Odstranění řešení odstraní všechny součásti zřízené ve vašem předplatném při nasazení řešení. Řešení odstraníte tak, že kliknete na název vašeho řešení na levém panelu šablony řešení a potom kliknete na **Odstranit**.

## <a name="cost-estimation-tools"></a>Nástroje pro odhad nákladů
K dispozici jsou tyto dva nástroje, které vám pomůžou lépe pochopit celkové náklady spojené s prováděním šablony řešení prediktivní údržba pro společnost Aerospace v rámci vašeho předplatného:

* [Nástroj Microsoft Azure cost Estimator (online)](https://azure.microsoft.com/pricing/calculator/)
* [Nástroj Microsoft Azure cost Estimator Tool (Desktop)](https://www.microsoft.com/download/details.aspx?id=43376)

