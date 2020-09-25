---
title: Spouštění analytických dotazů pro databáze tenantů
description: Analytické dotazy pro více tenantů využívají data extrahovaná z Azure SQL Database, Azure synapse Analytics, Azure Data Factory nebo Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 66f22fa2781fb4c0f4caa07323b3de8cac1ef9fd
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361105"
---
# <a name="explore-saas-analytics-with-azure-sql-database-azure-synapse-analytics-data-factory-and-power-bi"></a>Prozkoumejte SaaS Analytics pomocí Azure SQL Database, Azure synapse Analytics, Data Factory a Power BI
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu provedete kompletní scénář analýzy. Tento scénář předvádí, jak můžou analýzy dat klientů umožnit dodavatelům softwaru provádět inteligentní rozhodnutí. Pomocí dat extrahovaných z každé databáze tenanta můžete získat přehled o chování tenanta, včetně použití ukázkové aplikace SaaS lístky Wingtip. Tento scénář zahrnuje tři kroky:

1. **Extrahujte data** z každé databáze tenanta do úložiště analýz, v tomto případě ve fondu SQL.
2. **Optimalizujte extrahovaná data** pro zpracování analýz.
3. Pomocí nástrojů **Business Intelligence** můžete vykreslit užitečné poznatky, které vám můžou pomoct při rozhodování.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
>
> - Vytvořte úložiště analýzy tenanta pro načtení.
> - K extrakci dat z každé databáze tenanta do datového skladu Analytics použijte Azure Data Factory (ADF).
> - Optimalizujte extrahovaná data (přeuspořádejte je do schématu hvězdičky).
> - Dotazování analytického datového skladu.
> - Pomocí Power BI pro vizualizaci dat zvýrazněte trendy v datech tenanta a udělejte doporučení na vylepšení.

![architectureOverView](./media/saas-tenancy-tenant-analytics-adf/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analýza přes extrahovaná data tenanta

Aplikace SaaS uchovávají potenciálně obrovské množství dat tenantů v cloudu. Tato data můžou poskytovat bohatou řadu přehledů o provozu a využití vaší aplikace a o chování vašich klientů. Tyto přehledy můžou seřizovat vývoj funkcí, vylepšení použitelnosti a další investice do aplikací a platforem.

Přístup k datům pro všechny klienty je jednoduchý, pokud jsou všechna data v jediné databázi s více klienty. Ale přístup je složitější v případě distribuované škály v tisících databází. Jedním ze způsobů, jak zkrotit složitosti, je extrahovat data do analytické databáze nebo datového skladu pro dotaz.

V tomto kurzu se seznámíte s komplexním scénářem analýzy pro aplikaci Wingtip Tickets. První [Azure Data Factory (ADF)](../../data-factory/introduction.md) se používá jako nástroj orchestrace k extrakci obchodních lístků a souvisejících dat z každé databáze tenanta. Tato data se načtou do pracovních tabulek v analytickém úložišti. Úložiště analýz může být SQL Database nebo fond SQL. V tomto kurzu se jako úložiště analýzy používá [Azure synapse Analytics (dříve SQL Data Warehouse)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) .

V dalším kroku se extrahovaná data transformují a načtou do sady tabulek se [schématy hvězdiček](https://www.wikipedia.org/wiki/Star_schema) . Tabulky sestávají z centrální tabulky faktů a souvisejících tabulek dimenzí:

- Centrální tabulka faktů ve schématu hvězdičky obsahuje data lístků.
- Tabulky dimenzí obsahují data o místech, událostech, zákaznících a nákupních datech.

Společně centrální tabulky a tabulky dimenzí umožňují efektivní analytické zpracování. Schéma hvězdičky používané v tomto kurzu se zobrazuje na následujícím obrázku:

![architectureOverView](./media/saas-tenancy-tenant-analytics-adf/starschematables.JPG)

Nakonec se dotazují tabulky schématu hvězdičky. Výsledky dotazu jsou vizuálně zobrazovány pomocí Power BI, které zvýrazní přehled o chování tenanta a jejich použití. Pomocí tohoto schématu hvězdičky spustíte dotazy, které zveřejňují:

- Kdo je kupující lístky a ze kterého se má místo.
- Vzory a trendy v prodeji lístků.
- Relativní oblíbenosti každé místo.

V tomto kurzu najdete základní příklady přehledů, které se dají mohli z dat lístků Wingtip. Porozumět, jak každý z míst používá službu, může způsobit, že dodavatel Wingtip tickete bude přemýšlet o různých plánech služeb cílených na více nebo méně aktivních míst, například.

## <a name="setup"></a>Nastavení

### <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Nasadí se aplikace Wingtip Tickets SaaS Database na tenanta. K nasazení během méně než pěti minut si přečtěte téma [nasazení a prozkoumání aplikace Wingtip SaaS](../../sql-database/saas-dbpertenant-get-started-deploy.md).
- Z GitHubu se stáhnou skripty SaaS Database na tenanta a [zdrojový kód](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) aplikace. Viz pokyny ke stažení. Před extrakcí obsahu nezapomeňte *soubor zip odblokovat* .
- Power BI Desktop je nainstalována. [Stáhněte si Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Dávky dalších tenantů se zřídily v [**kurzu zřízení tenantů**](../../sql-database/saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Vytvoření dat pro ukázku

V tomto kurzu se seznámíte s analýzou údajů o prodeji lístků. V tomto kroku vygenerujete data lístku pro všechny klienty. V pozdějším kroku jsou tato data extrahována k analýze. Ujistěte se, že _jste zřídili dávku tenantů_ (jak je popsáno výše), abyste měli dostatek dat k vystavení rozsahu různých způsobů nákupu lístků.

1. V prostředí PowerShell ISE otevřete soubor *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*a nastavte následující hodnotu:
    - **$DemoScenario**  =  **1** lístky nákupu pro události se všemi místy
2. Stisknutím klávesy **F5** spusťte skript a vytvořte historii nákupů lístků pro všechny místo. Pomocí 20 tenantů vygeneruje skript desítky tisíc lístků a může trvat 10 minut nebo i déle.

### <a name="deploy-azure-synapse-analytics-data-factory-and-blob-storage"></a>Nasazení Azure synapse Analytics, Data Factory a Blob Storage

V aplikaci Wingtip Tickets jsou transakční data tenantů distribuována přes mnoho databází. Azure Data Factory (ADF) se používá k organizování extrakce, načítání a transformace (ELT) těchto dat do datového skladu. Aby se data načetla do služby Azure synapse Analytics (dříve SQL Data Warehouse), nástroj ADF extrahuje data do zprostředkujících souborů objektů BLOB a potom pomocí [základu](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) načte data do datového skladu.

V tomto kroku nasadíte další prostředky používané v tomto kurzu: fond SQL s názvem _tenantanalytics_, Azure Data Factory nazvaný _dbtodwload- \<user\> _a účet úložiště Azure s názvem _wingtipstaging \<user\> _. Účet úložiště se používá k dočasnému blokování extrahovaných datových souborů jako objektů BLOB předtím, než se načtou do datového skladu. Tento krok také nasadí schéma datového skladu a definuje kanály ADF, které orchestrují proces ELT.

1. V prostředí PowerShell ISE otevřete soubor *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* a nastavte:
    - **$DemoScenario**  =  **2** . nasazení datového skladu Analytics klienta, úložiště objektů BLOB a objektu pro vytváření dat
1. Stisknutím klávesy **F5** Spusťte ukázkový skript a nasaďte prostředky Azure.

Nyní zkontrolujte prostředky Azure, které jste nasadili:

#### <a name="tenant-databases-and-analytics-store"></a>Databáze tenantů a úložiště analýz

Pomocí [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) se připojte k **tenants1-DPT- &lt; User &gt; ** a **Catalog-DPT- &lt; User &gt; ** Servers. Nahraďte &lt; uživatele &gt; hodnotou použitou při nasazení aplikace. Použijte Login = *Developer* a Password = *P \@ ssword1*. Další pokyny najdete v [úvodním kurzu](../../sql-database/saas-dbpertenant-wingtip-app-overview.md) .

![Připojení k SQL Database z SSMS](./media/saas-tenancy-tenant-analytics-adf/ssmsSignIn.JPG)

V Průzkumník objektů:

1. Rozbalte *tenants1-DPT- &lt; User &gt; * Server.
1. Rozbalte uzel databáze a zobrazte seznam databází tenantů.
1. Rozbalte *Catalog-DPT- &lt; User &gt; * Server.
1. Ověřte, že se zobrazuje úložiště analýzy obsahující následující objekty:
    1. Tabulky **raw_Tickets**, **raw_Customers**, **raw_Events** a **raw_Venues** uchovávají nezpracovaná extrahovaná data z databází tenantů.
    1. Tabulky schématu hvězdiček jsou **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**a **dim_Dates**.
    1. Uložená procedura, **sp_transformExtractedData** slouží k transformaci dat a jejich načtení do tabulek se schématem hvězdiček.

![Snímek obrazovky zobrazuje Průzkumník objektů s rozbalenými tabulkami k zobrazení různých databázových objektů.](./media/saas-tenancy-tenant-analytics-adf/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage

1. V [Azure Portal](https://ms.portal.azure.com)přejděte do skupiny prostředků, kterou jste použili pro nasazení aplikace. Ověřte, že se přidal účet úložiště s názvem **wingtipstaging \<user\> ** .

   ![DWtables](./media/saas-tenancy-tenant-analytics-adf/adf-staging-storage.PNG)

1. Klikněte **na \<user\> wingtipstaging** účet úložiště a prozkoumejte objekty, které jsou k dispozici.
1. Kliknout na dlaždici **objekty blob**
1. Klikněte na kontejner **ConfigFile**
1. Ověřte, že **ConfigFile** obsahuje soubor JSON s názvem **TableConfig.jsv**. Tento soubor obsahuje názvy zdrojových a cílových tabulek, názvy sloupců a název sloupce sledování.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)

V [Azure Portal](https://ms.portal.azure.com) ve skupině prostředků ověřte, že se přidal Azure Data Factory s názvem _dbtodwload- \<user\> _ .

 ![adf_portal](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory-portal.png)

V této části se seznámíte s vytvořenou datovou továrnou.
Pomocí následujících kroků spusťte datovou továrnu:

1. Na portálu klikněte na objekt pro vytváření dat s názvem **dbtodwload \<user\> -**.
2. Kliknutím na dlaždici **vytvořit & monitorování** spustíte návrháře Data Factory na samostatné kartě.

## <a name="extract-load-and-transform-data"></a>Extrakce, načítání a transformace dat

Azure Data Factory slouží k orchestraci extrakce, načítání a transformace dat. V tomto kurzu extrahujete data ze čtyř různých zobrazení SQL ze všech databází klientů: **rawTickets**, **rawCustomers**, **rawEvents**a **rawVenues**. Mezi tato zobrazení patří ID místa, takže můžete odlišit data z jednotlivých míst v datovém skladu. Data se načtou do odpovídajících pracovních tabulek v datovém skladu: **raw_Tickets**, **raw_customers**, **raw_Events** a **raw_Venue**. Uložená procedura pak transformuje nezpracovaná data a naplní tabulky schématu hvězdiček: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**a **dim_Dates**.

V předchozí části jste nasadili a inicializovali nezbytné prostředky Azure, včetně datové továrny. Nasazená Datová továrna zahrnuje kanály, datové sady, propojené služby atd., které se vyžadují k extrakci, načtení a transformaci dat tenanta. Pojďme tyto objekty dále prozkoumat a pak aktivovat kanál pro přesun dat z databází klientů do datového skladu.

### <a name="data-factory-pipeline-overview"></a>Přehled kanálu Data Factory

V této části se prozkoumá objekty vytvořené v datové továrně. Následující obrázek popisuje celkový pracovní postup kanálu ADF používaného v tomto kurzu. Pokud dáváte přednost prozkoumání kanálu později a vidíte nejprve výsledky, přejděte k další části **Aktivace spuštění kanálu**.

![adf_overview](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory.PNG)

Na stránce Přehled přejděte na levý panel na kartu **Autor** a sledujte, že se vytvořily tři [kanály](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) a tři [datové sady](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) .
![adf_author](./media/saas-tenancy-tenant-analytics-adf/adf_author_tab.JPG)

Tři vnořené kanály jsou: SQLDBToDW, DBCopy a TableCopy.

**Kanál 1 – SQLDBToDW** vyhledá názvy databází klientů uložených v databázi katalogu (název tabulky: [__ShardManagement]. [ ShardsGlobal]) a pro každou databázi tenanta spustí kanál **DBCopy** . Po dokončení se spustí zadané **sp_TransformExtractedData** schéma uložené procedury. Tato uložená procedura transformuje načtená data v pracovních tabulkách a naplní tabulky schématu hvězdiček.

**Kanál 2 – DBCopy** vyhledá názvy zdrojových tabulek a sloupců z konfiguračního souboru uloženého v úložišti objektů BLOB.  Kanál **TableCopy** se pak spustí pro každou ze čtyř tabulek: TicketFacts, CustomerFacts, EventFacts a VenueFacts. Aktivita **[foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** se spouští paralelně pro všechny 20 databází. ADF umožňuje souběžně běžet maximálně 20 iterací cyklů. Zvažte vytvoření více kanálů pro další databáze.

**Kanál 3 – TableCopy** používá čísla verzí řádků v SQL Database (_rowversion_) k identifikaci řádků, které byly změněny nebo aktualizovány. Tato aktivita vyhledá počáteční a koncovou verzi řádku pro extrakci řádků ze zdrojových tabulek. Tabulka **CopyTracker** uložená v každé databázi tenanta sleduje poslední řádek extrahovaný z každé zdrojové tabulky v každém spuštění. Nové nebo změněné řádky se zkopírují do odpovídajících pracovních tabulek v datovém skladu: **raw_Tickets**, **raw_Customers**, **raw_Venues**a **raw_Events**. Nakonec je verze poslední řádek uložená v tabulce **CopyTracker** , která se použije jako počáteční verze řádku pro další extrakci.

Existují také tři parametrizované propojené služby, které propojí datovou továrnu se zdrojovými databázemi SQL, cílovým fondem SQL a zprostředkujícím úložištěm objektů BLOB. Na kartě **Autor** klikněte na **připojení** a Prozkoumejte propojené služby, jak je znázorněno na následujícím obrázku:

![adf_linkedservices](./media/saas-tenancy-tenant-analytics-adf/linkedservices.JPG)

Vzhledem k třem propojeným službám existují tři datové sady, které odkazují na data, která používáte v rámci aktivity kanálu jako vstupy nebo výstupy. Prozkoumejte každou datovou sadu, která bude sledovat připojení a použité parametry. _Azureblobu_ odkazuje na konfigurační soubor obsahující zdrojové a cílové tabulky a sloupce a také sloupec sledování v jednotlivých zdrojích.
  
### <a name="data-warehouse-pattern-overview"></a>Přehled vzoru datového skladu

Azure synapse (dříve SQL Data Warehouse) se používá jako úložiště analýzy k provádění agregace pro data tenanta. V této ukázce se používá základ k načtení dat do datového skladu. Nezpracovaná data se načítají do pracovních tabulek, které mají sloupec identity, aby bylo možné sledovat řádky, které byly transformované na tabulky se schématy hvězdiček. Následující obrázek znázorňuje vzor načítání: ![ diagram znázorňuje vzor načítání databázových tabulek.](./media/saas-tenancy-tenant-analytics-adf/loadingpattern.JPG)

V tomto příkladu se používají tabulky dimenzí typu s pomalou změnou (SCD). Každá dimenze má definován náhradní klíč pomocí sloupce identity. Jako osvědčený postup je předem vyplněna tabulka dimenze kalendářních dat za účelem úspory času. Pro ostatní tabulky dimenzí CREATE TABLE jako vyberte... (CTAS) příkaz slouží k vytvoření dočasné tabulky obsahující existující upravené a neupravené řádky spolu s náhradními klíči. To se provádí s IDENTITY_INSERT = ON. Nové řádky jsou potom vloženy do tabulky pomocí IDENTITY_INSERT = OFF. U snadného vrácení se změnami se přejmenuje stávající tabulka dimenzí a dočasná tabulka se přejmenuje, aby se stala novou tabulkou dimenzí. Před každým spuštěním se stará tabulka dimenzí odstraní.

Tabulky dimenzí jsou načteny před tabulku faktů. Tato sekvence zajišťuje, že pro každou příchodný fakt již existují všechny odkazované dimenze. Vzhledem k tomu, že jsou fakta načteny, odpovídá obchodnímu klíči pro každou odpovídající dimenzi a do každého faktu jsou přidány odpovídající náhradní klíče.

Poslední krok transformace odstraní pracovní data připravená pro další spuštění kanálu.

### <a name="trigger-the-pipeline-run"></a>Aktivace spuštění kanálu

Pomocí následujících kroků spusťte kompletní kanál pro extrakci, načtení a transformaci pro všechny databáze tenantů:

1. Na kartě **Autor** v UŽIVATELSKÉM rozhraní ADF v levém podokně vyberte kanál **SQLDBToDW** .
1. Klikněte na **aktivační událost** a v rozevírací nabídce klikněte na **aktivovat hned**. Tato akce spustí kanál okamžitě. V produkčním scénáři byste definovali časový harmonogram pro spuštění kanálu, který aktualizuje data podle plánu.
  ![Snímek obrazovky ukazuje prostředky továrny pro kanál s názvem S Q L D B až D W s rozbalenou možností triggeru a aktivací nyní.](./media/saas-tenancy-tenant-analytics-adf/adf_trigger.JPG)
1. Na stránce **spuštění kanálu** klikněte na **Dokončit**.

### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. V uživatelském rozhraní ADF přepněte v nabídce na levé straně na kartu **monitorování** .
1. Klikněte na **aktualizovat** až do **úspěšného**stavu kanálu SQLDBToDW.
  ![Snímek obrazovky ukazuje kanál S Q L D B až D W se stavem úspěch.](./media/saas-tenancy-tenant-analytics-adf/adf_monitoring.JPG)
1. Připojte se k datovému skladu pomocí SSMS a vytvořte dotaz na tabulky se schématem hvězdiček, abyste ověřili, že data byla načtena v těchto tabulkách.

Po dokončení kanálu budou v tabulce faktů uložena data o prodeji lístků pro všechna místa a tabulky dimenzí jsou vyplněny odpovídajícími místy, událostmi a zákazníky.

## <a name="data-exploration"></a>Zkoumání dat

### <a name="visualize-tenant-data"></a>Vizualizace dat tenanta

Data ve schématu hvězdičky poskytují všechna data o prodeji lístků potřebná pro vaši analýzu. Vizualizace dat graficky usnadňuje zobrazení trendů ve velkých datových sadách. V této části použijete **Power BI** k manipulaci s daty klienta v datovém skladu a jejich vizualizaci.

Pomocí následujících kroků se připojte k Power BI a importujte zobrazení, která jste vytvořili dříve:

1. Spusťte Power BI Desktop.
2. Na pásu karet domů vyberte **získat data**a pak klikněte na **Další...** z nabídky.
3. V okně **získat data** vyberte **Azure SQL Database**.
4. V okně přihlášení k databázi zadejte název vašeho serveru (**Catalog-DPT- &lt; User &gt; . Database.Windows.NET**). Vyberte možnost **importovat** do **režimu připojení dat**a pak klikněte na tlačítko **OK**.

    ![přihlášení k Power BI](./media/saas-tenancy-tenant-analytics-adf/powerBISignIn.PNG)

5. V levém podokně vyberte **databáze** a pak zadejte uživatelské jméno = *vývojář*a zadejte heslo = *P \@ ssword1*. Klikněte na **Připojit**.  

    ![databáze – přihlášení](./media/saas-tenancy-tenant-analytics-adf/databaseSignIn.PNG)

6. V podokně **navigátor** v části analytická databáze vyberte tabulky schématu hvězdiček: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** a **dim_Dates**. Pak vyberte **načíst**.

Blahopřejeme! Data byla úspěšně načtena do Power BI. Teď Prozkoumejte zajímavé vizualizace, abyste získali přehled o vašich klientech. Podívejme se, jak Analytics může poskytnout některá doporučení řízená daty do obchodního týmu Wingtip Tickets. Doporučení můžou přispět k optimalizaci obchodního modelu a prostředí pro zákazníky.

Začněte analýzou dat o prodeji lístků, abyste viděli variaci využití v rámci míst. Vyberte možnosti zobrazené v Power BI k vykreslení pruhového grafu celkového počtu lístků prodávaných každým jejich konáním. (Kvůli náhodné variaci generátoru lístků se vaše výsledky můžou lišit.)

![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/TotalTicketsByVenues-DW.PNG)

Předchozí vykreslení potvrdí, že počet lístků prodávaných jednotlivými místy se liší. Místa, která prodávají další lístky, využívají vaši službu více než místo míst, které prodávají méně lístků. Tady může být příležitost přizpůsobit přidělování prostředků podle různých potřeb tenanta.

Data můžete dál analyzovat, abyste viděli, jak se v průběhu času mění prodej lístku. Vyberte možnosti zobrazené na následujícím obrázku v Power BI k vykreslení celkového počtu lístků prodaných každý den po dobu 60 dnů.

![SaleVersusDate](./media/saas-tenancy-tenant-analytics-adf/SaleVersusDate-DW.PNG)

V předchozím grafu vidíte, že se u některých míst zobrazuje špička prodeje lístků. Tyto špičky posílí představu o tom, že některá místa můžou spotřebovávat systémové prostředky neúměrně. Zatím v době, kdy dojde k špičkám, neexistuje žádný zřejmý vzor.

Teď se podíváme na význam těchto dní v prodeji ve špičce. Kdy k těmto špičkám dochází po vypsání lístků na prodej? Pokud chcete vykreslit lístky prodávané za den, vyberte možnosti zobrazené na následujícím obrázku v Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics-adf/SaleDistributionPerDay-DW.PNG)

Tento graf znázorňuje, že některé místo vychází z prvního dne prodeje velkým počtem lístků. Jakmile se lístky dostanou při prodeji na těchto místech, zdá se, že se jedná o Mad – nespěcháte. Tento nárůst aktivity o několik míst může ovlivnit službu pro ostatní klienty.

Můžete znovu přejít k datům a zjistit, jestli je tato Mad – nespěcháte pravdivá pro všechny události hostované těmito místy. V předchozích zkusných grafech jste si ukázali, že se v rámci společnosti Contoso prochází mnoho lístků a že společnost Contoso má v některých dnech špičku prodeje. Přehrajte si s využitím Power BI možností pro vykreslení kumulativního prodeje lístků pro společnost Contoso, které se zaměřují na trendy v prodeji jednotlivých událostí. Dodržují všechny události stejný vzor prodeje? Zkuste vytvořit vykreslení jako na následujícím obrázku.

![ContosoSales](./media/saas-tenancy-tenant-analytics-adf/EventSaleTrends.PNG)

Tato znázornění kumulativního prodeje lístků v čase pro všechny události společnosti Contoso pro každou událost ukazuje na to, že Mad – nespěcháte pro všechny události neproběhne. Přehrajte si s možnostmi filtru a prozkoumejte trendy v prodeji pro jiné místo.

Přehledy o vzorech prodávajícího lístku mohou vést k optimalizaci obchodního modelu lístků společnosti Wingtip. Místo toho, aby všichni klienti současně nabíjíi, možná společnost Wingtip zavedla úrovně služeb s různými výpočetními velikostmi. Větší místa, která je potřeba k prodeji dalších lístků za den, vám může nabídnout vyšší úroveň s vyšší smlouvou o úrovni služeb (SLA). Těmto místům můžou být databáze umístěné ve fondu s vyššími limity pro prostředky pro každou databázi. Každá úroveň služby by mohla mít přidělenou hodinu v prodeji a za překročení přidělení se účtují další poplatky. Větší místa, která mají pravidelné nárůsty prodeje, by měla těžit z vyšších úrovní a lístky Wingtip můžou monetizovat své služby efektivněji.

Mezitím můžou někteří zákazníci, kteří si bojovat, podali za to, že si dostanou dostatek lístků za účelem zarovnání nákladů na službu. V těchto přehledech je například možné zvýšit prodej lístků za účelem jejich konání. Vyšší tržby by zvýšily vnímanou hodnotu služby. Klikněte pravým tlačítkem fact_Tickets a vyberte možnost **Nová míra**. Pro novou míru nazvanou **AverageTicketsSold**zadejte následující výraz:

```sql
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Vyberte následující možnosti vizualizace k vykreslení procentuálních lístků prodávaných každým místem k určení jejich relativního úspěchu.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/AvgTicketsByVenues-DW.PNG)

Výše uvedený diagram znázorňuje, že i když většina míst prodává více než 80% svých lístků, některé z nich jsou působit potíže, aby vyplnily více než polovinu jejich sedadel. Pokud chcete vybrat maximální nebo minimální procento lístků, které se prodávají pro každé místo, začněte s hodnotami.

## <a name="embedding-analytics-in-your-apps"></a>Vkládání analýz do vašich aplikací

Tento kurz se zaměřuje na analýzy mezi klienty, které slouží ke zlepšení porozumění jejich klientům od dodavatele softwaru. Analýzy také poskytují informace o _klientech_, které jim pomůžou efektivněji spravovat jejich podnikání.

V příkladu lístků Wingtip jste dříve zjistili, že prodej lístku by měl být v úmyslu podřídit předvídatelné vzory. Tento přehled se dá využít k tomu, aby se zvýšila situace, kdy se podíváme na místo. Možná máte příležitost využít techniky strojového učení k předpovědi prodeje lístků pro události. Účinky cenové změny by mohly být také modelovány, aby bylo možné odhadnout dopad nabídky slev. Power BI Embedded mohl být integrován do aplikace pro správu událostí, aby bylo možné vizualizovat předpokládaný prodej, včetně dopadu slev na celkový počet prodaných míst a tržby na události s nízkým prodejem. Pomocí Power BI Embedded můžete dokonce integrovat slevu na ceny za lístky přímo v prostředí vizualizace.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> - Vytvořte úložiště analýzy tenanta pro načtení.
> - K extrakci dat z každé databáze tenanta do datového skladu Analytics použijte Azure Data Factory (ADF).
> - Optimalizujte extrahovaná data (přeuspořádejte je do schématu hvězdičky).
> - Dotazování analytického datového skladu.
> - Pomocí Power BI pro vizualizaci dat zvýrazněte trendy v datech tenanta a udělejte doporučení na vylepšení.

Blahopřejeme!

## <a name="additional-resources"></a>Další zdroje

- Další [kurzy, které se vytvářejí na aplikaci Wingtip SaaS](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
