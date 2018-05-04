---
title: Spouštění dotazů analytics na klienta databáze, které používají Azure SQL Data Warehouse | Microsoft Docs
description: Mezi klienta analytické dotazy pomocí dat extrahovaných z více databází Azure SQL Database.
keywords: kurz k sql database
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/08/2017
ms.author: anjangsh; billgib; genemi
ms.openlocfilehash: 487dcd75113b92c1e50caca2ae9df34b4fb2548f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Prozkoumejte SaaS analytics s Azure SQL Database, SQL Data Warehouse, služby Data Factory a Power BI

V tomto kurzu provede scénářem analytics začátku do konce. Tento scénář ukazuje, jak analytics nad daty klienta můžete umožnit dodavatelům softwaru pro efektivní rozhodování. Pomocí dat extrahovaných z každého klienta databáze, použijte analýzy a získáte přehled o chování klienta, včetně jejich použití ukázkové aplikace SaaS Wingtip lístků. Tento scénář zahrnuje tři kroky: 

1.  **Extrahovat data** z každého klienta databáze do úložiště služby analýzy v takovém případě SQL Data Warehouse.
2.  **Optimalizace extrahovaná data** pro zpracování analýzy.
3.  Použití **Business Intelligence** nástrojů pro kreslení na užitečné přehledy, které můžete Průvodce přijímání rozhodnutí. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření klienta, které analytics uložit pro načtení.
> - Extrahovat data z databáze každého klienta do datového skladu analytics pomocí Azure Data Factory (ADF).
> - Optimalizujte extrahovaná data (reorganize do hvězdičky schématu).
> - Dotazování analýzy datového skladu.
> - Pomocí Power BI pro vizualizaci dat zvýrazněte trendů v datech klienta a vytvořit doporučení pro zlepšení.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analýza nad daty extrahované klienta

Aplikace SaaS v cloudu obsahovat potenciálně velká množství dat klienta. Tato data můžou poskytnout bohaté zdroj přehledy o operaci a využití vaší aplikace a chování vašim klientům. Tyto přehledy můžete Průvodce vývoj funkce, vylepšení použitelnost a další investice do aplikace a platformy.

Přístupu k datům pro všechny klienty je jednoduché, když všechna data v právě jednu databázi více klientů. Ale přístup je složitější při distribuci škálované napříč tisíce databází. Jedním ze způsobů tame složitost je extrahovat data do databáze analýzy nebo datového skladu pro dotaz.

V tomto kurzu uvede scénářem začátku do konce analýza pro aplikace Wingtip lístků. První, [Azure Data Factory (ADF)](../data-factory/introduction.md) slouží jako nástroj orchestration extrahovat lístky prodeje a související data z databáze každého klienta. Tato data je načten do pracovních tabulek v úložišti analytics. Analýza úložiště může být buď k SQL Database nebo SQL Data Warehouse. Tento kurz používá [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jako analytics uložit.

V dalším kroku extrahovaná data transformována a načíst do sady [hvězdičky schématu](https://www.wikipedia.org/wiki/Star_schema) tabulky. V tabulkách se zabývají tabulku faktů centrální a dimenze související tabulky:

- Tabulky faktů centrální ve schématu hvězdičky obsahuje data lístku.
- Tabulky dimenzí obsahovat data o místa, události, zákazníků a nákupech kalendářní data.

Společně na střed a tabulky povolit efektivní analytické zpracování dimenzí. Schéma hvězdičky použili v tomto kurzu se zobrazí na následujícím obrázku:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Nakonec jsou předmětem dotazování hvězdičky schéma tabulky. Výsledky dotazu jsou zobrazeny vizuálně pomocí Power BI, abyste měli na očích přehledy chování klienta a jejich používání aplikace. S toto hvězdičku – schéma můžete spustit dotazy, které zveřejňují:

- Kdo je kdybyste kupovali lístků a z které místo.
- Vzory a trendy v prodej lístků.
- Relativní době Oblíbené každý místo.

Tento kurz obsahuje základní příklady přehledy, které může shromažďovat informace z dat Wingtip lístků. Pochopení, jak každý místo používá službu může způsobit dodavatele Wingtip lístky myslet zaměřený na více nebo méně active místa, například plány jinou službu. 

## <a name="setup"></a>Nastavení

### <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Tento kurz používá funkce objektu pro vytváření dat Azure, které jsou aktuálně v omezené preview (parameterization propojené služby). Pokud chcete provést v tomto kurzu, zadejte svoje ID předplatného [zde](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Zašleme vám potvrzení hned, jak vaše předplatné bylo povoleno.

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:
- Nasazení aplikace Wingtip lístky SaaS databáze za klienta. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s aplikací Wingtip SaaS](saas-dbpertenant-get-started-deploy.md).
- Adresář Wingtip lístky SaaS databáze za klienta skripty a aplikace [zdrojový kód](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) se stáhnou z Githubu. Najdete pokyny ke stahování. Nezapomeňte *odblokovat soubor zip* před extrahování její obsah.
- Power BI Desktop je nainstalována. [Stáhněte si Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Zřízená dávku další klienty, najdete v článku [ **kurzu zřizování klientů**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Vytvořit data pro ukázku

V tomto kurzu jsou zde popsány analytics přes data prodeje lístků. V tomto kroku generuje data lístku pro všechny klienty. Tato data se v pozdější fázi, extrahuje pro analýzu. _Ujistěte se, zřízený dávky klientů_ (jak je popsáno výše) tak, že máte dostatek dat, která zveřejňuje řadu různých lístek nákupu vzory.

1. V prostředí PowerShell ISE otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*a nastavte následující hodnotu:
    - **$DemoScenario** = **1** zakoupit lístky pro události na všechna místa
2. Stiskněte klávesu **F5** vytvoření lístku zakoupení historie pro všechna místa a spusťte skript. S 20 klienty skript generuje desetitisíce lístků a může trvat 10 minut nebo déle.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Nasazení SQL Data Warehouse, Data Factory a úložiště objektů Blob 
V aplikaci Wingtip lístky transakčních dat s klienty distribuován přes mnoha databázemi. Azure Data Factory (ADF) se používá k orchestraci extrakce, načítání a transformace ELT () těchto dat do datového skladu. Nejvíce efektivně načíst data do SQL Data Warehouse, extrahuje data do souborů zprostředkující objektu blob ADF a pak používá [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) chcete načíst data do datového skladu.   

V tomto kroku nasadíte další prostředky v tomto kurzu použili: názvem SQL Data Warehouse _tenantanalytics_, objekt pro vytváření dat Azure volá _dbtodwload -\<uživatele\>_  , a účet úložiště Azure s názvem _wingtipstaging\<uživatele\>_. Účet úložiště se používá k dočasnému ukládání extrahovaná data soubory jako objekty BLOB před načtením do datového skladu. Tento krok také nasadí schématu datového skladu a definuje ADF kanály, které organizují ELT proces.
1. V prostředí PowerShell ISE otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* a nastavte:
    - **$DemoScenario** = **2** nasazení klienta analýzy datového skladu, úložiště objektů blob a objektu pro vytváření dat 
1. Stiskněte klávesu **F5** spustit ukázkový skript a nasazení prostředků Azure. 

Nyní zkontrolujte prostředky Azure, které jste nasadili:
#### <a name="tenant-databases-and-analytics-store"></a>Úložiště databáze a analýzy klienta
Použití [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) pro připojení k **tenants1-dpt -&lt;uživatele&gt;**  a **katalogu-dpt -&lt;uživatele&gt;**  servery. Nahraďte &lt;uživatele&gt; hodnota použitá při nasazení aplikace. Použít přihlášení = *vývojáře* a heslo = *P@ssword1*. Najdete v článku [úvodní kurz](saas-dbpertenant-wingtip-app-overview.md) další pokyny.

![Připojení k databázi SQL serveru pomocí aplikace SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

V Průzkumníku objektů:

1. Rozbalte *tenants1-dpt -&lt;uživatele&gt;*  serveru.
1. Rozbalte uzel databáze a zobrazit seznam databází klienta.
1. Rozbalte *katalogu-dpt -&lt;uživatele&gt;*  serveru.
1. Ověřte, najdete v části analytics úložiště, který obsahuje následující objekty:
    1. Tabulky **raw_Tickets**, **raw_Customers**, **raw_Events** a **raw_Venues** uložení nezpracovaná extrahovaná data z databáze klienta.
    1. Hvězdičky schématu tabulky se **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, a **dim_Dates** .
    1. Uložená procedura **sp_transformExtractedData** se používá k transformaci dat a načtení do tabulky hvězdičky schématu.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. V [portálu Azure](https://ms.portal.azure.com), přejděte do skupiny prostředků, který jste použili pro nasazení aplikace. Ověřte, že účet úložiště s názvem **wingtipstaging\<uživatele\>**  byla přidána.

  ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Klikněte na tlačítko **wingtipstaging\<uživatele\>**  účet úložiště a prozkoumejte objekty existuje.
1. Klikněte na tlačítko **objekty BLOB** dlaždici
1. Klikněte na kontejner **configfile**
1. Ověřte, že **configfile** obsahuje soubor JSON s názvem **TableConfig.json**. Tento soubor obsahuje zdrojové a cílové názvy tabulek, názvy sloupců a název sloupce sledovací modul.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
V [portálu Azure](https://ms.portal.azure.com) ve skupině prostředků, ověřte, že název objektu pro vytváření dat Azure _dbtodwload -\<uživatele\>_  byla přidána. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Tato část popisuje objektu pro vytváření dat vytvořit. Použijte následující postup ke spuštění objektu pro vytváření dat:
1. Na portálu, klikněte na objekt pro vytváření dat volat **dbtodwload -\<uživatele\>**.
2. Klikněte na tlačítko **Autor & monitorování** dlaždici spustíte návrháře služby Data Factory na samostatné kartě. 

## <a name="extract-load-and-transform-data"></a>Extrahování, zatížení a transformovat data
Azure Data Factory slouží k Orchestrace extrakce, načítání a transformace dat. V tomto kurzu extrahovat data z čtyři různá zobrazení SQL z každého klienta databází: **rawTickets**, **rawCustomers**, **rawEvents**, a  **rawVenues**. Mezi tato zobrazení patří místo Id, takže můžete rozlišení dat z jednotlivých místo v datovém skladu. Data načtená do odpovídající pracovní tabulky v datovém skladu: **raw_Tickets**, **raw_customers**, **raw_Events** a **raw_Venue**. Uložené procedury pak transformuje nezpracovaných dat a naplní hvězdičky schématu tabulek: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** , a **dim_Dates**.

V předchozí části nasazení a inicializovat potřebné prostředky Azure, včetně služby data factory. Vytváření nasazené dat zahrnuje kanály, datové sady, propojené služby, atd., potřebné k extrakci, spouštění a transformovat data klienta. Podíváme se na tyto další objekty a pak aktivační událost kanál pro přesun dat z databáze klienta do datového skladu.

### <a name="data-factory-pipeline-overview"></a>Přehled profilace objekt pro vytváření dat
Tato část popisuje objekty vytvořené v datové továrně. Následující obrázek popisuje celkového pracovního postupu kanálu ADF použili v tomto kurzu. Pokud dáváte přednost později prozkoumat kanálu a zobrazit výsledky poprvé, přejděte k další části **aktivovat kanálu spustit**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Na stránce Přehled přepnout na **Autor** kartě na levém panelu a zjistíte, že existují tři [kanály](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) a tři [datové sady](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) vytvořili.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Jsou tři vnořené kanály: SQLDBToDW, DBCopy a TableCopy.

**Kanál 1 - SQLDBToDW** vyhledá názvy klienta databáze, uložené v databázi katalogu (název tabulky: [__ShardManagement]. [[ ShardsGlobal]) a pro každou databázi klienta provádí **DBCopy** kanálu. Po dokončení zadaných **sp_TransformExtractedData** provedení uložené procedury schématu. Tuto uloženou proceduru transformuje načtená data v pracovních tabulek a naplní hvězdičky schéma tabulky.

**Kanál 2 - DBCopy** vyhledá názvy zdrojové tabulky a sloupce z konfiguračního souboru uložené v úložišti objektů blob.  **TableCopy** kanálu je spusťte pro každou čtyři tabulek: TicketFacts, CustomerFacts, EventFacts a VenueFacts. **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** aktivity spustí paralelně pro všechny databáze, 20. ADF povoluje maximálně 20 smyčky iterací spustit souběžně. Zvažte vytvoření více kanálů pro další databáze.    

**Kanál 3 - TableCopy** používá verzi čísla řádků v databázi SQL (_rowversion_) k identifikaci řádků, které byly změněny nebo aktualizovány. Tato aktivita vyhledá spuštění a end verze řádku pro extrahování řádků ze zdrojové tabulky. **CopyTracker** tabulky uložené v každé databázi klienta sleduje poslední řádek extrahovat z každé zdrojové tabulky při každém spuštění. Nové nebo změněné řádky se zkopírují do odpovídající pracovní tabulky v datovém skladu: **raw_Tickets**, **raw_Customers**, **raw_Venues**, a **raw_ Události**. Nakonec poslední verze řádku je uložen v **CopyTracker** tabulka, která se použije jako počáteční řádek verze pro další extrakce. 

Existují také tři parametrizované propojené služby, tento odkaz objektu pro vytváření dat do zdroje databází SQL, cíl SQL Data Warehouse a zprostředkující úložiště objektů Blob. V **Autor** klikněte na **připojení** prozkoumat propojené služby, jak je znázorněno na následujícím obrázku:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Odpovídající tři propojené služby, existují tři datové sady, které odkazují na data, která používáte v aktivitách kanálu jako vstupy nebo výstupy. Prozkoumejte každou z datové sady, abyste viděli připojení a použité parametry. _AzureBlob_ odkazuje na konfigurační soubor obsahující zdrojové a cílové tabulky a sloupce, jakož i sloupci sledovací modul v jednotlivých zdrojů.
  
### <a name="data-warehouse-pattern-overview"></a>Přehled vzorů datového skladu
SQL Data Warehouse slouží jako úložiště analytics k provádění agregace dat klienta. V této ukázce se PolyBase používá k načtení dat do SQL Data warehouse. Nezpracovaná data je načten do pracovní tabulky, které mají sloupec identity ke sledování řádků, které byly transformovány do tabulek hvězdičky schématu. Následující obrázek ukazuje vzoru načítání: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Změna dimenze (SCD) typu 1 dimenzi tabulky pomalu se používají v tomto příkladu. Každá dimenze obsahuje náhradní klíč definovat pomocí sloupce identity. Jako osvědčený postup je předem vyplněná ušetřit čas Tabulka dimenze data. Pro jiné tabulky dimenzí vytvořit tabulku jako vyberte... Příkaz (funkce CTAS) se používá k vytvoření dočasné tabulky obsahující existující upravené a – změnil řádky, společně s náhradní klíče. To lze provést pomocí IDENTITY_INSERT = ON. Nové záznamy jsou pak vloženy do tabulky s IDENTITY_INSERT = OFF. Pro snadné vrácení zpět je přejmenován existující tabulce dimenze a dočasné tabulky je přejmenovat se nové tabulky dimenzí. Před každé spuštění se odstraní původní tabulky dimenzí.

Tabulky dimenzí jsou načteny před tabulky faktů. Toto pořadí zajistí, že pro každý které fakt všechny odkazované dimenze již existují. Jak tato fakta jsou načteny, obchodní klíč pro každý odpovídající dimenze je nalezena shoda s a odpovídající náhradní klíče jsou přidány do jednotlivých faktů.

Posledním krokem pro transformaci odstraní pracovní data připraveno pro další zpracování kanálu.
   
### <a name="trigger-the-pipeline-run"></a>Aktivuje spuštění kanálu
Postupujte podle kroků, které extrahování tohoto postupu spusťte kompletní zatížení a transformace kanálu pro všechny databáze klienta:
1. V **Autor** ADF uživatelské rozhraní, vyberte na kartě **SQLDBToDW** kanálu v levém podokně.
1. Klikněte na tlačítko **aktivační událost** a z načtený nabídce klikněte na tlačítko **nyní aktivační událost**. Tato akce spustí kanálu okamžitě. V produkční scénář můžete definovat časový plán pro spuštění kanálu se aktualizovat data podle plánu.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Na **spustit kanál** klikněte na tlačítko **Dokončit**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu
1. V uživatelském rozhraní ADF přepnout **monitorování** kartě z nabídky na levé straně.
1. Klikněte na tlačítko **aktualizovat** dokud je stav SQLDBToDW kanálu **úspěšné**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Připojení do datového skladu pomocí SSMS a dotazování tabulky hvězdičky schématu, ověřte, že byla data načtena v těchto tabulkách.

Po dokončení kanálu tabulky faktů uchovává data prodeje lístků pro všechna místa a tabulky dimenzí se naplní odpovídající místa, události a zákazníků.

## <a name="data-exploration"></a>Zkoumání dat

### <a name="visualize-tenant-data"></a>Vizualizace dat klienta

Data v schéma hvězdičky poskytuje všechny lístku prodejní data potřebná pro analýzy. Vizualizace dat graficky usnadňuje najdete v části trendy ve velkých datových sad. V této části můžete použít **Power BI** manipulaci a vizualizaci dat klienta do datového skladu.

Pomocí následujících kroků pro připojení k Power BI a k importu zobrazení, která jste vytvořili dříve:

1. Spusťte Power BI desktop.
2. Na pásu karet Domů vyberte **načíst Data**a vyberte **více...** v nabídce.
3. V **načíst Data** vyberte **Azure SQL Database**.
4. V okně databáze přihlášení, zadejte název serveru (**katalogu-dpt -&lt;uživatele&gt;. database.windows.net**). Vyberte **Import** pro **režim připojení dat**a potom klikněte na **OK**. 

    ![Sign v na power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Vyberte **databáze** v levém podokně, pak zadejte uživatelské jméno = *vývojáře*a zadejte heslo = *P@ssword1*. Klikněte na **Připojit**.  

    ![databáze sign-in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. V **Navigátor** podokně v části databáze analýzy, vyberte hvězdičky schéma tabulky: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** a **dim_Dates**. Potom vyberte **zatížení**. 

Blahopřejeme! Úspěšně načíst data do Power BI. Nyní prozkoumejte zajímavé vizualizace a získáte přehled o vašich klientů. Projděme jak analytics můžete zadat několik doporučení řízené daty, které obchodní oddělení Wingtip lístků. K optimalizaci obchodní model a zákazník prostředí může pomoci doporučení.

Spusťte analýzou data prodeje lístků zobrazíte variace využití napříč místa. Vyberte možnosti uvedené v Power BI k vykreslení pruhový graf z celkového počtu lístků za účelem prodeje podle jednotlivých místo. (Z důvodu náhodné variace v lístku generátoru výsledky mohou lišit.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Předchozí výkresu potvrdí, že počet lístků za účelem prodeje podle jednotlivých místo se liší. Místa, které prodej lístků další se intenzivněji než místa, které prodej lístků méně pomocí služby. Může mít příležitost Zde můžete nastavit, přidělení prostředků podle potřeb různých klienta.

Dále můžete analyzovat dat a zjistit, jak prodej lístků lišit v průběhu času. Vyberte možnosti vidět na následujícím obrázku v Power BI k vykreslení celkový počet lístků prodaných každý den po dobu 60 dnů.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Předchozí graf zobrazuje prodeje Špička pro některé místa tohoto lístku. Tyto špičky posílit představu, že některé místa může být využívá systémové prostředky nepřiměřeně. Pokud není žádná zřejmé vzor dojít, když špičky.

Další umožňuje prozkoumat význam dní prodej ve špičce. Když tyto vrcholů se provádějí až po lístků, přejděte na prodej? K vykreslení lístky za účelem prodeje za den, vyberte možnosti vidět na následujícím obrázku v Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Toto zobrazovanou ukazuje, že některé místa prodeje na první den prodej velkého počtu lístků. Jakmile lístky přejděte do prodeje za tyto místa, nejspíš MAD – expresní. Tato shluků aktivita podle místa několik může mít vliv na služby u jiných klientů.

Můžete rozbalit dat znovu a zjistit, pokud tato MAD – expresní platí pro všechny události hostované tyto místa. V předchozích pozemků jste viděli, že Contoso vzájemné součinnosti Hall prodává mnoho lístků, a že Contoso také Špička v lístku prodej v určité dny. Přehrání s možnostmi Power BI k vykreslení prodej kumulativní lístek pro vzájemné součinnosti Hall Contoso, zaměřené na prodej trendy pro jednotlivé události. Všechny události postupují stejným způsobem prodej? Pokuste se vytvořit vykreslení stejný, jako je níže.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Toto zobrazovanou prodej kumulativní lístek v čase pro vzájemné součinnosti Hall společnosti Contoso pro každou jednotlivou událost ukazuje, že MAD – expresní neodehrává pro všechny události. Přehrání s nastavením filtru prozkoumat trendy prodej pro další místa.

Přehled o lístek prodávané vzory může vést Wingtip lístky za účelem optimalizace svůj obchodní model. Místo ukládání všichni klienti stejně, možná Wingtip vhodné zavést úrovně služeb s úrovně různých výkonu. Větší místa, které je potřeba prodej lístků další za den může být nabízí vyšší úroveň s vyšší smlouvu o úrovni služeb (SLA). Své databáze umístěna ve fondu s vyššími limity prostředků jednotlivé databáze může mít tyto místa. Jednotlivých úrovních služby může mít hodinové prodeje přidělení, s další poplatky za překročení přidělení. Větší místa, které mají pravidelné shluky prodeje by využívat vyšší úrovně a Wingtip lístky monetizovat své služby efektivněji.

Někteří zákazníci Wingtip lístky stížnost mezitím se čtením prodej dostatek lístků k odůvodnění, náklady na služby. Možná v tyto přehledy není příležitost zvýšit prodej lístků pro nevedou podle očekávání místa. Vyšší prodej by zvýšit dosahovaný hodnotu této služby. Klikněte pravým tlačítkem na fact_Tickets a vyberte **novou measure**. Zadejte následující výraz pro nový míru názvem **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Vyberte následující možnosti vizualizace k vykreslení lístky procento za účelem prodeje podle jednotlivých místo k určení jejich relativní úspěch.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Výše uvedené výkresu ukazuje, že i když většina místa prodeje více než 80 % jejich lístků, některé jsou naopak obtížně zvládá vyplnění víc než poloviny jejich licencí. Přehrání s hodnoty dobře vybrat maximální nebo minimální procento lístků prodaných pro každý místo.

## <a name="embedding-analytics-in-your-apps"></a>Vložení analytics ve svých aplikacích 
Tento kurz se zaměřuje na klienta mezi analytics používají ke zlepšení dodavatele softwaru pochopení svým klientům. Analýza může taky poskytovat přehled pro _klienty_, aby mohl snadněji efektivněji spravovat své firmy sami. 

V příkladu Wingtip lístky jste dříve zjistit, že prodej lístků mívají sklony dodržovat předvídatelný vzory. Tento přehled se dají používat ke nevedou podle očekávání prodej lístků nárůst místa. Možná není příležitost využívat machine learning postupy k předvídání prodej lístků pro události. Důsledky změny cen může být také modelován umožňující dopad nabídky slevy na předpovědět. Power BI Embedded může integrovat do aplikace událostí správy k vizualizaci předpokládaných prodej, včetně dopad slevy na celkový počet licencí za účelem prodeje a výnosy na základě událostí prodeje nízká. S Power BI Embedded můžete i integrovat ve skutečnosti použití slevy na ceny lístek, přímo na možnosti vizualizace.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasaďte SQL Data Warehouse naplněný hvězdicového schématu pro analýzu klienta.
> * Extrahovat data z databáze každého klienta do datového skladu analytics pomocí Azure Data Factory.
> * Optimalizujte extrahovaná data (reorganize do hvězdičky schématu).
> * Dotazování analýzy datového skladu. 
> * Pomocí Power BI vizualizovat trendů v datech mezi všechny klienty.

Blahopřejeme!

## <a name="additional-resources"></a>Další zdroje informací:

- Další [návodů, které aplikace pro adresář Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
