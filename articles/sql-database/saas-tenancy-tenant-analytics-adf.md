---
title: Spouštění analytických dotazů tenanta databází pomocí Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Mezi tenanty analytických dotazů pomocí dat extrahovaných z několika databází Azure SQL Database.
keywords: kurz k sql database
services: sql-database
documentationcenter: ''
author: anumjs
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: anjangsh
ms.openlocfilehash: 9dad675188782f3feb769e47230aa7b0cb42b10e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45737205"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Prozkoumejte SaaS analýz s využitím Azure SQL Database, SQL Data Warehouse, služby Data Factory a Power BI

V tomto kurzu si projdete scénáři analytics začátku do konce. Tento scénář ukazuje, jak můžete analytics přes data tenanta poskytněte dodavatelům softwaru pro inteligentní rozhodování. Použití dat extrahovaných z každé databázi tenantů, pomocí analytics získat přehled o chování klienta, včetně jejich použití ukázkové SaaS aplikace Wingtip Tickets aplikace. Tento scénář zahrnuje tři kroky: 

1.  **Extrahovat data** z každého tenanta databáze do analýzy úložiště, v tomto případě SQL Data Warehouse.
2.  **Optimalizace extrahovaná data** pro zpracování analýzy.
3.  Použití **Business Intelligence** nástrojů pro kreslení na užitečné přehledy, které můžete Průvodce rozhodování. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření tenanta analytics uložená pro načtení.
> - Extrahovat data z každé databázi tenantů do datového skladu analytics pomocí Azure Data Factory (ADF).
> - Optimalizujte extrahovaná data (reorganize do hvězdicového schématu).
> - Dotaz analytics datového skladu.
> - Pomocí Power BI pro vizualizace dat zvýrazněte trendy v datech tenanta a zkontrolujte doporučení pro vylepšení.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analýza dat extrahovaných tenanta

Aplikace SaaS podržte potenciálně velké množství dat tenantů v cloudu. Tato data můžete zadat zdroj bohaté přehledy o operaci a využití vaší aplikace a chování vašich klientů. Vývoj funkcí, vylepšení použitelnosti a další investice do aplikace a platformy můžete průvodce tyto přehledy.

Zpřístupnění dat pro všechny tenanty je jednoduché, když všechna data v jediné databázi s více tenanty. Ale přístup je složitější, při distribuci ve velkém měřítku napříč tisíci databázemi. Jedním způsobem, jak zkrotit složitost je extrakce dat pro analytické databáze nebo datového skladu pro dotaz.

Tomto kurzu si představíme scénáři začátku do konce analýzy aplikace Wingtip Tickets. Nejprve je potřeba [Azure Data Factory (ADF)](../data-factory/introduction.md) slouží jako nástroj pro orchestraci extrahovat lístky prodeje a související data z každé databázi tenantů. Tato data se načtou do pracovní tabulky v úložišti analytics. Analýza úložiště může být buď služby SQL Database nebo SQL Data Warehouse. Tento kurz používá [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) jako analýzy úložiště.

V dalším kroku extrahovaných dat je transformovat a načíst do sady [hvězdicového schématu](https://www.wikipedia.org/wiki/Star_schema) tabulky. Tabulky se skládají z tabulky faktů centrální a související tabulky dimenzí:

- Tabulky faktů centrální v hvězdicové schéma obsahuje data lístku.
- Tabulky dimenzí obsahují údaje o místa, události, zákazníky a koupit data.

Společně – střed a tabulky umožňují efektivní analytické zpracování dimenzí. Hvězdicové schéma používané v tomto kurzu se zobrazí na následujícím obrázku:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Nakonec hvězdicového schématu tabulky se generuje dotaz. Výsledky dotazu jsou zobrazeny, vizuálně pomocí Power BI, abyste měli na očích informace o chování tenantů a jejich používání aplikace. Pomocí této hvězdicového schématu spusťte dotazy, které zpřístupňují:

- Kdo je nákup lístků a od které míst.
- Vzorce a trendy v prodeji lístků.
- Relativní oblíbenost každé místo.

Tento kurz obsahuje přehledy, které mohou být úsporách získaných dat Wingtip Tickets základních příkladů. Vysvětlení, jak každé místo používá službu může způsobit dodavatele Wingtip Tickets přemýšlet o různých plánech zaměřený na víc nebo míň aktivní místa, např. 

## <a name="setup"></a>Nastavení

### <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Tento kurz používá funkce služby Azure Data Factory, která jsou aktuálně ve verzi limited preview (Parametrizace propojenou službu). Pokud chcete provést v tomto kurzu, pošlete ID svého předplatného [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Pošleme vám potvrzení ihned poté, co vaše předplatné se povolila.

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:
- Nasazení aplikace Wingtip Tickets SaaS databáze na Tenanta. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání SaaS aplikace Wingtip aplikace](saas-dbpertenant-get-started-deploy.md).
- Skriptů aplikace Wingtip Tickets SaaS databáze na Tenanta a aplikace [zdrojový kód](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) se stáhnou z Githubu. Zobrazit pokyny ke stažení. Nezapomeňte *odblokovat soubor zip* před extrahováním jeho obsah.
- Power BI Desktopu je nainstalovaná. [Stáhněte si Power BI Desktopu](https://powerbi.microsoft.com/downloads/).
- Zřízení dávku dalších tenantů, najdete v článku [ **kurz zřízení tenantů**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Vytvoření dat pro tuto ukázku

Tento kurz se věnuje analýza prodejních dat lístků. V tomto kroku vygenerujete data lístku pro všechny tenanty. V pozdějším kroku je extrahována tato data pro analýzu. _Ujistěte se, jste zřídili dávku tenantů_ (jak je popsáno dříve), abyste měli k dispozici dostatek dat. vystavit celou řadu různých lístku vzory chování při nákupu.

1. Otevřít v PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*a nastavte následující hodnotu:
    - **$DemoScenario** = **1** nákup lístků na akce na všech místech
2. Stisknutím klávesy **F5** ke spuštění skriptu a vytvoření historie pro všechna místa nákupu lístků. S 20 tenanty skript generuje desítky tisíc lístků a může trvat 10 minut nebo déle.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Nasazení SQL Data Warehouse, služby Data Factory a Blob Storage 
V aplikaci Wingtip Tickets klienty transakční data distribuovaná mezi mnoha databázemi. Azure Data Factory (ADF) se používá k orchestraci extrakce, načítání a transformace (ELT) těchto dat do datového skladu. Co nejefektivněji načtení dat do SQL Data Warehouse, extrahuje data do soubory zprostředkující blob ADF a pak ho použije [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) k načtení dat do datového skladu.   

V tomto kroku nasadíte další prostředky v tomto kurzu použili: volá se SQL Data Warehouse _tenantanalytics_, službu Azure Data Factory volat _dbtodwload -\<uživatele\>_  , a účet úložiště Azure s názvem _wingtipstaging\<uživatele\>_. Účet úložiště se používá pro dočasné uchování dat extrahované soubory jako objekty BLOB, před načtením do datového skladu. Tento krok také nasadí schématu datového skladu a definuje ADF kanály, které orchestrují procesu ELT.
1. Otevřít v PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* a nastavte:
    - **$DemoScenario** = **2** nasazení tenant analytics datového skladu, úložiště objektů blob a data factory 
1. Stisknutím klávesy **F5** a spustit ukázkový skript nasazení prostředků Azure. 

Teď zkontrolujte prostředky Azure, které jste nasadili:
#### <a name="tenant-databases-and-analytics-store"></a>Úložiště databáze a analýzy tenanta
Použití [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) pro připojení k **tenants1-dpt -&lt;uživatele&gt;**  a **katalogu-dpt -&lt;uživatele&gt;**  servery. Nahraďte &lt;uživatele&gt; s hodnotou použitou při nasazení aplikace. Použijte přihlašovací jméno = *developer* a heslo = *P@ssword1*. Zobrazit [úvodní tutoriál](saas-dbpertenant-wingtip-app-overview.md) další pokyny.

![Připojení k serveru SQL Database pomocí SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

V Průzkumníku objektů:

1. Rozbalte *tenants1-dpt -&lt;uživatele&gt;*  serveru.
1. Rozbalte uzel databáze a zobrazit seznam databází tenantů.
1. Rozbalte *katalogu-dpt -&lt;uživatele&gt;*  serveru.
1. Ověřte, jestli analýzy úložiště, který obsahuje následující objekty:
    1. Tabulky **raw_Tickets**, **raw_Customers**, **raw_Events** a **raw_Venues** obsahovat nezpracované extrahovaných dat z databáze tenantů.
    1. Hvězdicové schéma tabulky jsou **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, a **dim_Dates** .
    1. Uloženou proceduru **sp_transformExtractedData** slouží k transformaci dat a jejich načtení do tabulek hvězdicového schématu.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. V [webu Azure Portal](https://ms.portal.azure.com), přejděte do skupiny prostředků, který jste použili k nasazení aplikace. Ověřte, že účet úložiště s názvem **wingtipstaging\<uživatele\>**  byla přidána.

  ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Klikněte na tlačítko **wingtipstaging\<uživatele\>**  účet úložiště, pokud chcete prozkoumat objekty, které jsou k dispozici.
1. Klikněte na tlačítko **objekty BLOB** dlaždici
1. Klikněte na kontejner **configfile**
1. Ověřte, že **configfile** obsahuje soubor JSON s názvem **TableConfig.json**. Tento soubor obsahuje zdrojové a cílové názvy tabulek, názvy sloupců a název sloupce sledování.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
V [webu Azure Portal](https://ms.portal.azure.com) ve skupině prostředků, ověřte, že objekt pro vytváření dat Azure s názvem _dbtodwload -\<uživatele\>_  byla přidána. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Tato část popisuje vytvoření datové továrny. Podle následujících pokynů ke spuštění služby data factory:
1. Na portálu klikněte na datovou továrnu, volá **dbtodwload -\<uživatele\>**.
2. Klikněte na tlačítko **vytvořit a monitorovat** dlaždice pro spuštění návrháře služby Data Factory na samostatné kartě. 

## <a name="extract-load-and-transform-data"></a>Extrakce, načítání a transformaci dat.
Azure Data Factory se používá pro orchestraci extrakce, načítání a transformovat data. V tomto kurzu se extrahovat data ze čtyř různých zobrazení SQL ze všech databází tenantů: **rawTickets**, **rawCustomers**, **rawEvents**, a  **rawVenues**. Mezi tato zobrazení patří místní příslušností Id, tak můžete rozlišení data z každé místo v datovém skladu. Načtení dat do odpovídajících pracovních tabulek v datovém skladu: **raw_Tickets**, **raw_customers**, **raw_Events** a **raw_Venue**. Uloženou proceduru pak transformuje nezpracovaných dat a naplní tabulky hvězdicového schématu: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** , a **dim_Dates**.

V předchozí části nasazení a inicializovat nezbytné prostředky Azure, včetně služby data factory. Nasazené datové továrny zahrnuje kanály, datové sady, propojené služby, atd., třeba extrakce, načítání a transformace dat tenanta. Podívejme se na tyto objekty dál a potom aktivační událost kanálu pro přesun dat z databáze tenantů do datového skladu.

### <a name="data-factory-pipeline-overview"></a>Přehled kanálu služby data factory
Tato část popisuje objekty vytvořené ve službě data factory. Následující obrázek popisuje celkového pracovního postupu v tomto kurzu používá kanálu ADF. Pokud chcete prozkoumat později kanál a zobrazit výsledky nejprve, přejděte k další části **aktivuje spuštění kanálu**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Na stránce Přehled přepnout na **Autor** kartu na panelu vlevo a podívejte se, že existují tři [kanály](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) a tři [datových sad](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) vytvořili.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

Jsou tři vnořené kanály: SQLDBToDW DBCopy a TableCopy.

**Kanál 1 - SQLDBToDW** vyhledá názvy databází tenantů, které jsou uloženy v databázi katalogu (název tabulky: [__ShardManagement]. [ ShardsGlobal]) a pro každou databázi tenantů, spustí **DBCopy** kanálu. Po dokončení zadaných **sp_TransformExtractedData** provést uloženou proceduru schématu. Tuto uloženou proceduru transformuje načtených dat v pracovních tabulek a naplní tabulky hvězdicového schématu.

**Kanál 2 - DBCopy** vyhledá názvy zdrojových tabulek a sloupců z konfiguračního souboru uložená v blob storage.  **TableCopy** pak spuštění kanálu pro všechny čtyři tabulky: TicketFacts, CustomerFacts, EventFacts a VenueFacts. **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** aktivita spustí paralelně pro všemi 20 databázemi. ADF povoluje maximálně 20 průchod cyklem běžet paralelně. Zvažte vytvoření více kanálů pro více databází.    

**Kanál 3 - TableCopy** používá verzi čísla řádků ve službě SQL Database (_rowversion_) k identifikaci řádků, které byly změněny nebo aktualizovány. Tato aktivita vyhledá počáteční a koncová verze řádků pro extrahování řádků ze zdrojových tabulek. **CopyTracker** sleduje poslední řádek z každé zdrojové tabulce při každém spuštění extrahovat tabulky uložené v každé databázi tenantů. Nové nebo změněné řádky se zkopírují do odpovídajících pracovních tabulek v datovém skladu: **raw_Tickets**, **raw_Customers**, **raw_Venues**, a **raw_ Události**. Nakonec poslední řádek verze je uloženo v **CopyTracker** tabulka, která se použije jako počáteční řádek verze pro další extrakce. 

Existují také tři parametry propojené služby, tento odkaz objektu pro vytváření dat do databáze SQL zdroje, cíle SQL Data Warehouse a zprostředkujících úložiště objektů Blob. V **Autor** klikněte na tlačítko na **připojení** prozkoumat propojené služby, jak je znázorněno na následujícím obrázku:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Odpovídající tři propojené služby, existují tři datové sady, které odkazují na data, která používáte v aktivity kanálu jako vstupy nebo výstupy. Prozkoumejte všech datových sad sledovat připojení a použité parametry. _AzureBlob_ odkazuje na konfigurační soubor obsahující zdrojové a cílové tabulky a sloupce, jakož i sloupec sledování v jednotlivých zdrojů.
  
### <a name="data-warehouse-pattern-overview"></a>Přehled modelu služby data warehouse
SQL Data Warehouse umožňuje jako úložiště analytics provádí agregaci dat tenanta. V této ukázce se používá technologie PolyBase k načtení dat do SQL Data warehouse. Nezpracovaná data se načtou do pracovní tabulky, které se mají sloupce identity ke sledování řádků, které se transformuje na hvězdicové schéma tabulky. Následující obrázek ukazuje vzor načítání: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Změna dimenze (SCD) tabulky 1 dimenzi typu pomalu se používají v tomto příkladu. Každá dimenze obsahuje náhradní klíč definovány pomocí sloupec identity. Jako osvědčený postup je předem vyplněna šetří čas tabulce dimenzí date. Pro jiné tabulky dimenzí použít příkaz pro vytvoření TABLE AS SELECT... Příkaz (CTAS) slouží k vytvoření dočasné tabulky obsahující existující upravené a -modified řádky, spolu s náhradní klíče. Používá se k tomu IDENTITY_INSERT = ON. Nové řádky jsou potom vložen do tabulky s IDENTITY_INSERT = OFF. Pro jednoduché vrácení zpět přejmenovat existující tabulce dimenzí a v dočasné tabulce se přejmenovalo na stát novou tabulku dimenze. Před každé spuštění se odstraní původní tabulky dimenzí.

Tabulky dimenzí se načítají než tabulky faktů. Toto pořadí zajistí, že pro každý opravovány fakt, všechny odkazované dimenze již existují. Načtou se skutečností, se najde shoda klíče obchodní pro každý odpovídající dimenzi a odpovídající náhradní klíče jsou přidány do jednotlivých faktů.

Posledním krokem transformací, která se odstraní pracovní data připravená pro další spuštění kanálu.
   
### <a name="trigger-the-pipeline-run"></a>Aktivace spuštění kanálu
Sledujte následujících spusťte kompletní kroků extrakce, načítání a transformace kanálu pro všechny databáze tenantů:
1. V **Autor** ADF uživatelského rozhraní, vyberte na kartě **SQLDBToDW** kanálu v levém podokně.
1. Klikněte na tlačítko **aktivační událost** a načtený rozevírací nabídku klikněte na tlačítko **aktivovat**. Tato akce spustí kanál okamžitě. V případě produkčního prostředí byste definovali časový plán pro spuštění kanálu pro aktualizaci dat podle plánu.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. Na **spuštění kanálu** klikněte na **Dokončit**.
 
### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu
1. V uživatelském rozhraní ADF, přepněte **monitorování** kartu z nabídky na levé straně.
1. Klikněte na tlačítko **aktualizovat** až stav kanálu SQLDBToDW **Succeeded**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Připojení k datovému skladu pomocí SSMS a dotazovat tabulky hvězdicového schématu, ověřte, že se data načetla v těchto tabulkách.

Po dokončení kanálu tabulky faktů uchovává data o prodeji lístků pro všechna místa a tabulkách s dimenzemi se naplní odpovídající místa, události a zákazníky.

## <a name="data-exploration"></a>Zkoumání dat

### <a name="visualize-tenant-data"></a>Vizualizace dat klienta

Data v hvězdicové schéma poskytuje všechny prodejní data lístku potřeby vaší analýzy. Vizualizace dat graficky usnadňuje zobrazit trendy ve velkých datových sad. V této části použijete **Power BI** pracovat a vizualizace dat tenantů v datovém skladu.

Následující kroky použijte pro připojení k Power BI a k importování zobrazení, která jste vytvořili dříve:

1. Spusťte Power BI desktopu.
2. Z pásu karet Domů vyberte **získat Data**a vyberte **více...** v nabídce.
3. V **získat Data** okně **Azure SQL Database**.
4. V okně databáze přihlášení, zadejte název serveru (**katalogu-dpt -&lt;uživatele&gt;. database.windows.net**). Vyberte **Import** pro **režim připojení dat**a potom klikněte na tlačítko **OK**. 

    ![znak v na power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Vyberte **databáze** v levém podokně, zadejte uživatelské jméno = *developer*a zadejte heslo = *P@ssword1*. Klikněte na **Připojit**.  

    ![databáze – přihlášení](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. V **Navigátor** podokně v části analytickou databázi, vyberte hvězdicového schématu tabulky: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** a **dim_Dates**. Potom vyberte **zatížení**. 

Blahopřejeme! Úspěšně načtete data do Power BI. Teď si projděte zajímavé vizualizace a získejte přehled o vašich klientů. Projděme si jak poskytnout několik doporučení řízené daty, které tým Wingtip Tickets obchodní analýzy. Doporučení může pomoct optimalizovat prostředí obchodní model a zákazníků.

Začněte tím, že analýza prodejních dat lístku zobrazíte změnu ve využití napříč registrují. Vyberte možnosti zobrazené v Power BI k vykreslení pruhový graf z celkového počtu lístků prodávaný společností každé místo. (Z důvodu náhodných kolísání generátor lístek, výsledky mohou lišit.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Předchozí diagram potvrdí, že počet lístky prodávaný společností každé místo se liší. Místa, které prodej lístků další nevyčerpáte než místa, které prodej lístků méně používání služby. Můžou existovat příležitost zde přizpůsobit přidělení prostředků podle potřeb jiného tenanta.

Můžete hlouběji analyzovat data chcete zobrazit, jak prodej lístků mění v čase. Vyberte požadované možnosti je znázorněno na následujícím obrázku v Power BI k vykreslení celkový počet lístky prodává každý den po dobu 60 dnů.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Předchozí graf ukazuje prodejní zásobníku pro některé venues tohoto lístku. Čelí posílit myšlenkou, že některé venues může spotřebovávat systémové prostředky nepřiměřeně. V kdy provozní špičky uplatňují zatím není žádný zřejmý vzor.

Další Pojďme prozkoumat významu těchto dnů prodej ve špičce. Kdy tyto špičky nastat po tickets přechod na prodej? K vykreslení lístky prodaných za den, vyberte požadované možnosti je znázorněno na následujícím obrázku v Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Tento diagram znázorňuje, že některé venues prodávat velké množství lístků na první den v prodeji. Jakmile tickets přechod na prodej na těchto místech, zřejmě MAD – špičce. Toto rozšíření aktivita podle místa několik může vliv na služby u jiných klientů.

Můžete zobrazit podrobnosti o datech znovu a zjistit, jestli tento MAD – špičce hodnotu true pro všechny události, které jsou hostitelem těchto místa. V předchozí graf jste viděli, že organizace Contoso koncertní prodává mnoho lístky, a že Contoso také prudký nárůst v prodeji lístků v určité dny. Pohrajte si s možnostmi Power BI k vykreslení prodeje kumulativní lístků na vzájemné součinnosti Hall Contoso, zaměřený na vývoj prodeje pro každý z jeho události. Všechny události postupujte podle stejného vzoru prodej? Došlo k pokusu o vytvoření diagramu, jako je uvedený níže.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Tento diagram kumulativní lístek prodej v čase pro Contoso koncertní vedení se zaměstnanci pro každou jednotlivou událost ukazuje, že MAD – špičce neproběhne ani pro všechny události. Pohrajte si s možností filtrování prozkoumat trendy prodeji pro jiné místa.

Přehled vzorů pro prodej lístků může vést Wingtip Tickets optimalizovat svůj obchodní model. Místo účtování za všechny tenanty stejně, možná byste Wingtip zavést úrovně služby s různých velikostech výpočetních prostředků. Větší místa, které je potřeba prodej lístků další každý den může nabízet vyšší úroveň s vyšším smlouvy o úrovni služeb (SLA). Tyto toho může mít své databáze umístěn ve fondu s vyššími limity prostředků na databázi. Jednotlivé úrovně služby může mít hodinové prodejní přidělení, se poplatky účtují se poplatky za překročení přidělení. Větší místa, které mají pravidelné nárůstům prodeje, budou těžit z vyšší úrovně a Wingtip Tickets efektivněji monetizovat své služby.

Zákazníci, kteří Wingtip Tickets stěžovat mezitím si, že se snaží prodej dostatek lístků k odůvodnění, náklady na služby. Třeba v těchto přehledů je příležitost k posílení prodeje lístků na nevedou místa podle očekávání. Vyšší sales by zvýšila vnímaná hodnotu této služby. Fact_Tickets klikněte pravým tlačítkem a vyberte **novou míru**. Zadejte následující výraz pro novou míru volá **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Vyberte následující možnosti vizualizace k vykreslení lístky procento prodávaný společností každé místo pro určení jejich relativního úspěchu.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Výše uvedené diagram ukazuje, i když většina venues prodávat více než 80 % svoje lístky, některé jsou jenom vyplnit více než polovinu svoje licence. Pohrajte si s hodnoty a vyberte maximální nebo minimální procento lístky prodává pro každé místo.

## <a name="embedding-analytics-in-your-apps"></a>Vkládání analýz do vašich aplikací 
Tento kurz se zaměřuje na napříč tenanty analytics využívá ke zlepšení dodavatele softwaru znalost svým klientům. Analytics můžete najít přehledy, které _tenantů_, aby to pomohl ostatním efektivněji spravovat jejich obchodní sami. 

V příkladu Wingtip Tickets je dříve zjistí, že prodej lístků mívají sklony dodržovat předvídatelným způsobem. Tyto informace mohou využít k nevedou prodej lístků boost místa podle očekávání. Možná je možnost využívat machine learning postupy k predikci prodeje lístků na události. Účinky změny cen může být také modelovat umožňující dopad nabízí slevy předpovídat. Power BI Embedded může integrovat do aplikace správy událostí k vizualizaci předpokládané prodeje, včetně dopad slevy na celkový počet licencovaných míst prodává a výnosy, prodej s nízkou událostí. S Power BI Embedded můžete i integrovat skutečně použití slevy na ceny lístek, klikněte pravým tlačítkem myši v práci s vizualizací.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasaďte SQL Data Warehouse vyplní hvězdicové schéma analýz tenantů.
> * Extrahovat data z každé databázi tenantů do datového skladu analytics pomocí Azure Data Factory.
> * Optimalizujte extrahovaná data (reorganize do hvězdicového schématu).
> * Dotaz analytics datového skladu. 
> * Pomocí Power BI můžete vizualizovat trendy v datech ve všech tenantech.

Blahopřejeme!

## <a name="additional-resources"></a>Další zdroje informací:

- Další [kurzy, které vycházejí z aplikace SaaS aplikace Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
