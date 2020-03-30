---
title: Spuštění analytických dotazů v databázích klientů
description: Analytické dotazy napříč tenanty pomocí dat extrahovaných z Azure SQL Database, SQL Data Warehouse, Azure Data Factory nebo Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4791cd3a6b6f72c5d9ee4ca828d66b0d361f356c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73816772"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Prozkoumejte analýzy SaaS pomocí Azure SQL Database, SQL Data Warehouse, Data Factory a Power BI

V tomto kurzu procházíte komplexním scénářem analýzy. Scénář ukazuje, jak analýzy nad daty tenanta může dodavatelé softwaru moc činit chytrá rozhodnutí. Pomocí dat extrahovaných z každé databáze klienta, pomocí analýzy získat přehled o chování klienta, včetně jejich použití ukázkové wingtip vstupenky SaaS aplikace. Tento scénář zahrnuje tři kroky: 

1.  **Extrahujte data** z každé databáze klienta do úložiště analýzy, v tomto případě do datového skladu SQL.
2.  **Optimalizujte extrahovaná data** pro zpracování analýzy.
3.  Pomocí nástrojů **Business Intelligence** můžete získat užitečné poznatky, které mohou řídit rozhodování. 

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> - Vytvořte úložiště analýzy klienta pro načtení.
> - Azure Data Factory (ADF) slouží k extrahování dat z každé databáze klienta do datového skladu analytics.
> - Optimalizujte extrahovaná data (reorganizovat do hvězdného schématu).
> - Dotaz na datový sklad analytics.
> - Pomocí Power BI pro vizualizaci dat můžete zvýraznit trendy v datech tenanta a doporučit zlepšení.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analýza nad extrahovanými daty klienta

Aplikace SaaS uchovávají potenciálně obrovské množství dat klienta v cloudu. Tato data mohou poskytnout bohatý zdroj informací o provozu a využití vaší aplikace a chování vašich klientů. Tyto přehledy mohou vést vývoj funkcí, vylepšení použitelnosti a další investice do aplikací a platformy.

Přístup k datům pro všechny klienty je jednoduchý, když všechna data jsou pouze v jedné databázi více klientů. Ale přístup je složitější při distribuci ve velkém měřítku napříč tisíci databázemi. Jedním ze způsobů, jak zkrotit složitost, je extrahovat data do analytické databáze nebo datového skladu pro dotaz.

Tento kurz představuje scénář analýzy od konce pro aplikaci Wingtip Tickets. Za prvé, [Azure Data Factory (ADF)](../data-factory/introduction.md) se používá jako nástroj orchestrace extrahovat prodeje lístků a související data z každé databáze klienta. Tato data se načítají do pracovních tabulek v úložišti analýzy. Úložiště analýzy může být buď SQL Database nebo SQL Data Warehouse. Tento kurz používá [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jako úložiště analýzy.

Dále extrahovaná data je transformována a načtena do sady tabulek [hvězdného schématu.](https://www.wikipedia.org/wiki/Star_schema) Tabulky se skládají z tabulky centrálních faktů a souvisejících tabulek dimenzí:

- Tabulka centrálních faktů ve schématu hvězdobsahuje data o lístku.
- Tabulky dimenzí obsahují data o místech konání, událostech, zákaznících a datech nákupu.

Společně centrální a dimenze tabulky umožňují efektivní analytické zpracování. Hvězdné schéma použité v tomto kurzu je zobrazeno na následujícím obrázku:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Nakonec jsou dotazovány tabulky schématu hvězdy. Výsledky dotazu se zobrazují vizuálně pomocí Power BI ke zvýraznění přehledů o chování klienta a jejich používání aplikace. Pomocí tohoto schématu hvězdspustíte dotazy, které zveřejňují:

- Kdo kupuje vstupenky a z jakého místa.
- Vzory a trendy v prodeji vstupenek.
- Relativní popularita každého místa.

Tento kurz obsahuje základní příklady přehledů, které lze získat z dat wingtip tickets. Pochopení toho, jak každé místo používá službu, může způsobit, že prodejce wingtipových vstupenek přemýšlí například o různých plánech služeb zaměřených na více či méně aktivních místech. 

## <a name="setup"></a>Nastavení

### <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Tento kurz používá funkce Azure Data Factory, které jsou aktuálně v omezeném náhledu (parametrizace propojené služby). Pokud chcete provést tento kurz, zadejte id předplatného [zde](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Jakmile bude vaše předplatné povoleno, zašleme vám potvrzení.

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:
- Wingtip Vstupenky SaaS databáze na klienta aplikace je nasazena. Informace o nasazení za méně než pět minut najdete v [tématu Nasazení a prozkoumání aplikace Wingtip SaaS](saas-dbpertenant-get-started-deploy.md).
- Wingtip Tickets SaaS Databáze na tenanta skripty a [zdrojový kód](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) aplikace se stáhnou z GitHubu. Viz pokyny ke stažení. Nezapomeňte *odblokovat soubor zip* před extrahováníjeho obsahu.
- Power BI Desktop je nainstalovaný. [Stáhněte si Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Dávka dalších klientů byla zřízena, viz [**kurz Zřízení klientů**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Vytvoření dat pro ukázku

Tento kurz zkoumá analýzy nad daty prodeje vstupenek. V tomto kroku vygenerujete data lístku pro všechny klienty. V pozdějším kroku jsou tato data extrahována pro analýzu. _Ujistěte se, že jste zřízené dávky klientů_ (jak je popsáno výše) tak, aby měli dostatek dat vystavit řadu různých vzorů nákupu lístků.

1. V prostředí PowerShell ISE otevřete *...\Výukové moduly\Provozní analýza\Analýza tenanta DW\Demo-TenantAnalyticsDW.ps1*a nastavte následující hodnotu:
    - **$DemoScenario** = **1** Nákup vstupenek na akce na všech místech
2. Stisknutím **klávesy F5** spustíte skript a vytvoříte historii nákupu vstupenek pro všechna místa. S 20 tenanty skript generuje desítky tisíc vstupenek a může trvat 10 minut nebo více.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Nasazení datového skladu SQL, datové továrny a úložiště objektů blob 
V aplikaci Wingtip Tickets jsou transakční data klientů distribuována v mnoha databázích. Azure Data Factory (ADF) se používá k orchestratu extrakce, načtení a transformace (ELT) těchto dat do datového skladu. Chcete-li načíst data do datového skladu SQL co nejefektivněji, ADF extrahuje data do zprostředkujících souborů objektů blob a pak použije [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) k načtení dat do datového skladu.   

V tomto kroku nasadíte další prostředky použité v kurzu: SQL Datový sklad s názvem _tenantanalytics_, Azure Data Factory s názvem _\<dbtodwload- user\>_ a účet úložiště Azure s názvem _wingtipstaging\<uživatele\>_. Účet úložiště se používá k dočasnému uložení extrahovaných datových souborů jako objektů BLOB před jejich načtením do datového skladu. Tento krok také nasazuje schéma datového skladu a definuje kanály ADF, které orchestrují proces ELT.
1. V prostředí PowerShell ISE otevřete *...\Výukové moduly\Provozní analýza\Analýza tenanta DW\Demo-TenantAnalyticsDW.ps1* a nastavte:
    - **$DemoScenario** = **2** Nasazení datového skladu analýzy tenantů, úložiště objektů blob a datové továrny 
1. Stisknutím **klávesy F5** spusťte ukázkový skript a nasaďte prostředky Azure. 

Teď zkontrolujte prostředky Azure, které jste nasadili:
#### <a name="tenant-databases-and-analytics-store"></a>Úložiště databází klientů a analýz
Použití [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) pro připojení k **tenantům1-dpt-&lt;uživatelské&gt; servery** a **katalog-dpt-&lt;uživatelské&gt; ** servery. Nahraďte &lt;uživatele&gt; hodnotou použitou při nasazení aplikace. Použít Login = *developer* a Heslo = *P\@ssword1*. Další informace najdete [v úvodním kurzu.](saas-dbpertenant-wingtip-app-overview.md)

![Připojení k databázovému serveru SQL ze služby SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

V Průzkumníkovi objektů:

1. Rozbalte *&lt;klienty1-dpt-&gt; uživatelský* server.
1. Rozbalte uzel Databáze a podívejte se na seznam databází klientů.
1. Rozbalte uživatelský server *catalog-dpt-&lt;&gt; * .
1. Ověřte, zda se v úložišti analýzy zobrazují následující objekty:
    1. Tabulky **raw_Tickets**, **raw_Customers**, **raw_Events** a **raw_Venues** obsahují nezpracovaná data z databází klienta.
    1. Tabulky schématu hvězdjsou **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**a **dim_Dates**.
    1. Uložená **procedura, sp_transformExtractedData** se používá k transformaci dat a načtení do tabulek hvězdného schématu.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. Na [webu Azure Portal](https://ms.portal.azure.com)přejděte na skupinu prostředků, kterou jste použili k nasazení aplikace. Ověřte, zda byl přidán účet úložiště s názvem **wingtipstaging\<uživatel.\> **

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Klikněte na **wingtipstaging\<\> ** uživatelskéúložiště účet prozkoumat objekty přítomné.
1. Klikněte na **dlaždici Objektů blob**
1. Klepněte na **konfigurační soubor kontejneru.**
1. Ověřte, zda **configfile** obsahuje soubor JSON s názvem **TableConfig.json**. Tento soubor obsahuje názvy zdrojových a cílových tabulek, názvy sloupců a název sloupce sledování.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
Na [webu Azure Portal](https://ms.portal.azure.com) ve skupině prostředků ověřte, že byla přidána Azure Data Factory s názvem _\<dbtodwload - uživatel.\> _ 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Tato část zkoumá vytvořenou datovou továrnu. Chcete-li spustit datovou továrnu, postupujte podle následujících kroků:
1. Na portálu klikněte na datovou továrnu nazvanou **dbtodwload-\<user\>**.
2. Kliknutím na **Položku Autor & monitor** spusťte návrháře datové továrny na samostatné kartě. 

## <a name="extract-load-and-transform-data"></a>Extrahovat, načíst a transformovat data
Azure Data Factory se používá pro orchestrating extrakce, načítání a transformace dat. V tomto kurzu extrahovat data ze čtyř různých zobrazení SQL z každé databáze klienta: **rawTickets**, **rawCustomers**, **rawEvents**a **rawVenues**. Tato zobrazení zahrnují ID místa konání, takže můžete diskriminovat data z každého místa v datovém skladu. Data se načtou do odpovídajících přípravných tabulek v datovém skladu: **raw_Tickets**, **raw_customers**, **raw_Events** a **raw_Venue**. Uložená procedura pak transformuje nezpracovaná data a naplní tabulky hvězdného schématu: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**a **dim_Dates**.

V předchozí části jste nasadili a inicializovali potřebné prostředky Azure, včetně datové továrny. Nasaditdata továrna zahrnuje kanály, datové sady, propojené služby atd., potřebné k extrahování, načtení a transformaci dat klienta. Pojďme prozkoumat tyto objekty dále a potom spusťte kanál přesunout data z databází klientů do datového skladu.

### <a name="data-factory-pipeline-overview"></a>Přehled kanálu datové továrny
Tato část zkoumá objekty vytvořené v datové továrně. Následující obrázek popisuje celkový pracovní postup kanálu ADF použitého v tomto kurzu. Pokud chcete později prozkoumat kanál a nejprve zobrazit výsledky, přejděte k další části **Trigger the pipeline run**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Na stránce s přehledem přepněte na kartu **Autor** na levém panelu a všimněte si, že jsou vytvořeny tři [kanály](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) a tři [datové sady.](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Tři vnořené kanály jsou: SQLDBToDW, DBCopy a TableCopy.

**Kanál 1 – SQLDBToDW** vyhledá názvy databází klientů uložených v databázi katalogu (název tabulky: [__ShardManagement].[ ShardsGlobal]) a pro každou databázi klienta provede kanál **DBCopy.** Po dokončení je proveden zadaný **sp_TransformExtractedData** uložené schéma procedury. Tato uložená procedura transformuje načtená data v pracovních tabulkách a naplní tabulky hvězdného schématu.

**Kanál 2 – DBCopy** vyhledá názvy zdrojových tabulek a sloupců z konfiguračního souboru uloženého v úložišti objektů blob.  **Kanál TableCopy** je pak spuštěn pro každou ze čtyř tabulek: TicketFacts, CustomerFacts, EventFacts a VenueFacts. **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** aktivita provede paralelně pro všech 20 databází. ADF umožňuje maximálně 20 opakování smyčky spustit paralelně. Zvažte vytvoření více kanálů pro více databází.    

**Kanál 3 - TableCopy** používá čísla verzí řádků v databázi SQL (_rowversion_) k identifikaci řádků, které byly změněny nebo aktualizovány. Tato aktivita vyhledá počáteční a konec řádku verze pro extrahování řádků ze zdrojových tabulek. Tabulka **CopyTracker** uložená v každé databázi klienta sleduje poslední řádek extrahovaný z každé zdrojové tabulky v každém spuštění. Nové nebo změněné řádky se zkopírují do odpovídajících pracovních tabulek v datovém skladu: **raw_Tickets**, **raw_Customers**, **raw_Venues**a **raw_Events**. Nakonec je poslední verze řádku uložena v tabulce **CopyTracker,** která má být použita jako počáteční verze řádku pro další extrakci. 

Existují také tři parametrizované propojené služby, které propojují datovou továrnu se zdrojovými databázemi SQL, cílovým datovým skladem SQL a zprostředkujícím úložištěm objektů blob. Na kartě **Autor** klikněte na **Připojení** a prozkoumejte propojené služby, jak je znázorněno na následujícím obrázku:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Odpovídající tři propojené služby, existují tři datové sady, které odkazují na data, která používáte v potrubí aktivity jako vstupy nebo výstupy. Prozkoumejte každou z datových sad a sledujte použitá připojení a parametry. _AzureBlob_ odkazuje na konfigurační soubor obsahující zdrojové a cílové tabulky a sloupce, stejně jako sloupec sledování v každém zdroji.
  
### <a name="data-warehouse-pattern-overview"></a>Přehled vzorů datového skladu
SQL Data Warehouse se používá jako úložiště analýzy k provádění agregace na data klienta. V této ukázce polybase slouží k načtení dat do skladu dat SQL. Nezpracovaná data se načítají do pracovních tabulek, které mají sloupec identity pro sledování řádků, které byly transformovány do tabulek hvězdného schématu. Následující obrázek znázorňuje ![vzor načítání: loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

V tomto příkladu se používají pomalu se měnící tabulky dimenzí typu 1 . Každá dimenze má náhradní klíč definovaný pomocí sloupce identity. Jako osvědčený postup je tabulka dimenze data předem vyplněna, aby se ušetřil čas. Pro ostatní tabulky dimenzí vytvořit tabulku jako výběr... (CTAS) příkaz se používá k vytvoření dočasné tabulky obsahující existující upravené a nezměněné řádky, spolu s náhradní klíče. To se provádí s IDENTITY_INSERT=ON. Nové řádky jsou pak vloženy do tabulky s IDENTITY_INSERT=OFF. Pro snadné vrácení zpět je stávající tabulka dimenzí přejmenována a dočasná tabulka je přejmenována tak, aby se stala novou tabulkou dimenzí. Před každým spuštěním je odstraněna stará tabulka dimenzí.

Tabulky dimenzí jsou načteny před tabulku faktů. Toto pořadí zajišťuje, že pro každou příchozí skutečnost již existují všechny odkazované dimenze. Při načítání faktů je obchodní klíč pro každou odpovídající dimenzi spárován a ke každému faktu jsou přidány odpovídající náhradní klíče.

Poslední krok transformace odstraní pracovní data připravená pro další spuštění kanálu.
   
### <a name="trigger-the-pipeline-run"></a>Spuštění spuštění kanálu
Postupujte podle následujících kroků a spusťte úplný kanál extrakce, načtení a transformace pro všechny databáze klientů:
1. Na kartě **Autor** uživatelského rozhraní ADF vyberte kanál **SQLDBToDW** z levého podokna.
1. Klepněte na **tlačítko Spouštět** a v nabídce stažené z nabídky klepněte na **tlačítko Spustit nyní**. Tato akce spustí kanál okamžitě. Ve scénáři výroby byste definovali časový plán pro spuštění kanálu k aktualizaci dat podle plánu.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Na stránce **Pipeline Run** klikněte na **Dokončit**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu
1. V uživatelském rozhraní ADF přepněte na kartu **Monitor** z nabídky vlevo.
1. Klepněte na tlačítko **Aktualizovat,** dokud nebude stav kanálu SQLDBToDW **úspěšný**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Připojte se k datovému skladu pomocí SSMS a dotazujte se na tabulky hvězdného schématu, abyste ověřili, že data byla načtena v těchto tabulkách.

Po dokončení kanálu obsahuje tabulka faktů data o prodeji vstupenek pro všechna místa a tabulky dimenzí jsou naplněny odpovídajícími místy, událostmi a zákazníky.

## <a name="data-exploration"></a>Průzkum dat

### <a name="visualize-tenant-data"></a>Vizualizace dat klienta

Data ve schématu hvězdiček poskytují všechna data o prodeji vstupenek potřebná pro vaši analýzu. Vizualizace dat graficky usnadňuje zobrazení trendů ve velkých datových sadách. V této části používáte **Power BI** k manipulaci a vizualizaci dat klienta v datovém skladu.

Pomocí následujících kroků se připojte k Power BI a importujte zobrazení, která jste vytvořili dříve:

1. Spusťte desktop Power BI.
2. Na pásu karet Domů vyberte **Získat data**a vyberte **Další...** z nabídky.
3. V okně **Získat data** vyberte Azure **SQL Database**.
4. Do přihlašovacího okna databáze zadejte název serveru (**catalog-dpt-&lt;&gt;User .database.windows.net**). Vyberte **Importovat** **pro režim připojení k datům**a klepněte na tlačítko **OK**. 

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. V levém podokně vyberte **Možnost Databáze,** zadejte uživatelské jméno = *vývojář*a zadejte heslo = *P\@ssword1*. Klikněte na **Připojit**.  

    ![přihlášení do databáze](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. V podokně **Navigátor** vyberte v databázi analýzy tabulky hvězdného schématu: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** a **dim_Dates**. Pak vyberte **Načíst**. 

Blahopřejeme! Úspěšně jste načetli data do Power BI. Nyní prozkoumejte zajímavé vizualizace a získejte přehled o vašich tenantech. Pojďme si projít, jak může analytika poskytnout obchodní tým Wingtip Tickets některá doporučení založená na datech. Doporučení mohou pomoci optimalizovat obchodní model a zákaznickou zkušenost.

Začněte analýzou dat o prodeji vstupenek, abyste viděli rozdíly v využití v různých místech. Výběrem možností zobrazených v Power BI vykreslete pruhový graf celkového počtu vstupenek prodaných jednotlivými místy. (Vzhledem k náhodným změnám v generátoru vstupenek se vaše výsledky mohou lišit.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Předchozí parcela potvrzuje, že počet vstupenek prodaných jednotlivými místy se liší. Místa, která prodávají více vstupenek, využívají vaše služby více než místa, která prodávají méně vstupenek. Může zde být příležitost přizpůsobit přidělení prostředků podle různých potřeb klienta.

Můžete dále analyzovat data a zjistit, jak se prodej vstupenek v průběhu času liší. Vyberte možnosti zobrazené na následujícím obrázku v Power BI a vykreslete celkový počet vstupenek prodaných každý den po dobu 60 dnů.
 
![Datum saleversus](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Předchozí graf ukazuje, že prodej vstupenek špička pro některá místa. Tyto špičky posilují myšlenku, že některá místa mohou neúměrně spotřebovávat systémové prostředky. Zatím neexistuje žádný zřejmý vzor, když dojde k hrotům.

Dále pojďme prozkoumat význam těchto dnů vrcholu prodeje. Kdy se tyto vrcholy vyskytují po vstupenkách jít do prodeje? Pokud chcete vYkreslit lístky prodané za den, vyberte v Power BI možnosti zobrazené na následujícím obrázku.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Tento pozemek ukazuje, že některé místa prodávají velké množství vstupenek na první den prodeje. Jakmile vstupenky jdou do prodeje na těchto místech, zdá se, že šílený spěch. Tento nárůst aktivity několika místmůže ovlivnit službu pro ostatní klienty.

Můžete přejít do dat znovu, abyste zjistili, zda tento šílený spěch platí pro všechny události pořádané těmito místy. V předchozích pozemků, jste viděli, že Contoso Koncertní síň prodává mnoho vstupenek, a že Contoso má také špičku v prodeji vstupenek v určitých dnech. Pohrajte si s možnostmi Power BI a vykreslete kumulativní prodej vstupenek do koncertní síně Contoso se zaměřením na prodejní trendy pro každou z jejích událostí. Řídí se všechny události stejným vzorem prodeje? Pokuste se vytvořit spiknutí, jako je ten níže.

![ContosoProdej](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Tento graf kumulativní prodeje vstupenek v průběhu času pro Contoso koncertní síň pro každou událost ukazuje, že šílený spěch se nestane pro všechny události. Pohrajte si s možnostmi filtru, abyste prozkoumali prodejní trendy pro jiná místa.

Pohledy na vzorce prodeje vstupenek by mohly vést Wingtip Tickets k optimalizaci jejich obchodního modelu. Namísto účtování všech klientů stejně, možná Wingtip by měl zavést úrovně služeb s různými výpočetními velikostmi. Větší místa, která potřebují prodat více vstupenek za den, by mohla být nabídnuta vyšší úroveň se smlouvou o vyšší úrovni služeb (SLA). Tato místa by mohla mít své databáze umístěny do fondu s vyšší limity prostředků pro databázi. Každá úroveň služby může mít hodinové přidělení prodeje s dodatečnými poplatky účtovanými za překročení přidělení. Větší místa, která mají periodické výbuchy prodeje, by měla prospěch z vyšších úrovní a vstupenky Wingtip mohou efektivněji zpeněžit své služby.

Mezitím, někteří zákazníci Wingtip Vstupenky si stěžují, že se snaží prodat dostatek vstupenek ospravedlnit náklady na služby. Možná, že v těchto postřehů je příležitost ke zvýšení prodeje vstupenek pro nedostatečně výkonná místa. Vyšší tržby by zvýšily vnímanou hodnotu služby. Klepněte pravým tlačítkem myši fact_Tickets a vyberte **možnost Nový rozměr**. Zadejte následující výraz pro nový rozměr s názvem **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Vyberte následující možnosti vizualizace a vykreslete procentuální lístky prodané jednotlivými místy, abyste zjistili jejich relativní úspěch.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Výše uvedený pozemek ukazuje, že i když většina míst prodává více než 80% svých vstupenek, někteří se snaží zaplnit více než polovinu svých míst. Pohrajte si s Hodnotami Dobře a vyberte maximální nebo minimální procento vstupenek prodaných pro každé místo.

## <a name="embedding-analytics-in-your-apps"></a>Vkládání analýz do aplikací 
Tento kurz se zaměřuje na analýzy mezi tenanty, které slouží ke zlepšení porozumění dodavatele softwaru jejich tenantům. Služba Analytics může také poskytovat _přehledy pro klienty_, které jim pomohou efektivněji spravovat své podnikání sami. 

V příkladu Wingtip Tickets jste dříve zjistili, že prodej vstupenek má tendenci dodržovat předvídatelné vzory. Tento přehled může být použit k pomoci nevýkonných míst zvýšit prodej vstupenek. Možná existuje příležitost využít techniky strojového učení k předvídání prodeje vstupenek na události. Účinky cenových změn by rovněž mohly být modelovány, aby bylo možné předvídat dopad nabízených slev. Power BI Embedded by mohl být integrován do aplikace pro správu událostí pro vizualizaci předpovídaných prodejů, včetně dopadu slev na celkový prodaný počet prodaných míst a příjmů z událostí s nízkým prodejem. S Power BI Embedded můžete dokonce integrovat skutečné uplatnění slevy na ceny vstupenek přímo do prostředí vizualizace.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasaďte datový sklad SQL naplněný hvězdným schématem pro analýzu klienta.
> * Azure Data Factory slouží k extrahování dat z každé databáze klienta do datového skladu analytics.
> * Optimalizujte extrahovaná data (reorganizovat do hvězdného schématu).
> * Dotaz na datový sklad analytics. 
> * Pomocí Power BI můžete vizualizovat trendy v datech napříč všemi tenanty.

Blahopřejeme!

## <a name="additional-resources"></a>Další zdroje

- Další [výukové programy, které vycházejí z aplikace Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
