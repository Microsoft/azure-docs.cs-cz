---
title: Spouštění dotazů analytics databází Azure SQL | Microsoft Docs
description: Mezi klienta analytické dotazy pomocí dat extrahovaných z více databází Azure SQL Database.
keywords: kurz SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: anjangsh
ms.reviewer: billgib, genemi
ms.openlocfilehash: c0ffbdfb9110860a6a9083423b91a571671c3308
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="cross-tenant-analytics-using-extracted-data"></a>Analytics mezi klienta pomocí extrahovaná data

V tomto kurzu provede scénáři dokončení analýzy. Tento scénář ukazuje, jak povolit analytics firmám umožňuje efektivní rozhodování. Pomocí dat extrahovaných z horizontálně dělené databáze, použijte analýzy a získáte přehled o chování klienta, včetně jejich použití ukázkové aplikace SaaS Wingtip lístků. Tento scénář zahrnuje tři kroky: 

1.  **Extrahovat data** z každého klienta databáze do úložiště služby analýzy.
2.  **Optimalizace extrahovaná data** pro zpracování analýzy.
3.  Použití **Business Intelligence** nástrojů pro kreslení na užitečné přehledy, které můžete Průvodce přijímání rozhodnutí. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření klienta, které analytics uložit extrahovat data do.
> - Elastické úlohy použijte k extrahování dat z databáze každého klienta do úložiště analýzy.
> - Optimalizujte extrahovaná data (reorganize do hvězdičky schématu).
> - Dotaz na databázi analýzy.
> - Pomocí Power BI pro vizualizaci dat zvýrazněte trendů v datech klienta a vytvořit doporučení pro zlepšení.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Vzor analytics offline klienta

Aplikace SaaS, které vyvíjíte mít přístup k velká množství dat klienta uložená v cloudu. Data poskytuje bohaté zdroj statistiky o provozu a využití vaší aplikace a o chování klienty. Tyto přehledy můžete Průvodce vývoj funkce, vylepšení použitelnost a další investice do aplikace a platformy.

Přístupu k datům pro všechny klienty je jednoduché, když všechna data v právě jednu databázi více klientů. Ale přístup se složitější při distribuci škálované napříč tisíce databází. Jedním ze způsobů tame složitost je extrahovat data do databáze analýzy nebo datového skladu. Potom dotazování datového skladu získat přehledy z dat lístků pro všechny klienty.

Tento kurz představuje scénář dokončení analýzy pro této ukázkové aplikaci SaaS. První, elastické úlohy se používají k plánování extrahování dat z databáze každého klienta. Data je odeslána na obchod analytics. Analýza úložiště může být buď k SQL Database nebo SQL Data Warehouse. Pro rozsáhlé datové extrakci [Azure Data Factory](../data-factory/introduction.md) je stahování.

Dále je skartovány agregovaná data do sady [hvězdičky schématu](https://www.wikipedia.org/wiki/Star_schema) tabulky. V tabulkách se zabývají tabulku faktů centrální a dimenze související tabulky:

- Tabulky faktů centrální ve schématu hvězdičky obsahuje data lístku.
- Tabulky dimenzí obsahovat data o místa, události, zákazníků a nákupech kalendářní data.

Společně na střed a tabulky povolit efektivní analytické zpracování dimenzí. Schéma hvězdičky použili v tomto kurzu se zobrazí na následujícím obrázku:
 
![StarSchema](media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Nakonec jsou předmětem dotazování hvězdičky schéma tabulky. Výsledky dotazu jsou zobrazeny vizuálně zvýrazněte přehledy chování klienta a jejich používání aplikace. S toto hvězdičku – schéma můžete spustit dotazy, které pomáhají zjistit položky, jako jsou následující:

- Kdo je kdybyste kupovali lístků a z které místo.
- Skrytá vzory a trendy v těchto oblastech:
    - Prodeje lístků.
    - Relativní době Oblíbené každý místo.

Pochopení, jak konzistentně každý klient používá službu poskytuje možnost vytvářet plány služby pro jejich potřeb. Tento kurz obsahuje základní příklady přehledy, které může shromažďovat informace z dat klienta.

## <a name="setup"></a>Nastavení

### <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Nasazení aplikace Wingtip lístky SaaS víceklientské databáze. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s aplikací Wingtip lístky SaaS víceklientské databáze](saas-multitenantdb-get-started-deploy.md)
- Adresář Wingtip SaaS skripty a aplikace [zdrojový kód](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) se stáhnou z Githubu. Nezapomeňte *odblokovat soubor zip* před extrahování její obsah. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS.
- Power BI Desktop je nainstalována. [Stáhněte si Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Zřízená dávku další klienty, najdete v článku [ **kurzu zřizování klientů**](saas-multitenantdb-provision-and-catalog.md).
- Úlohy agenta a úlohy agenta databáze byly vytvořeny. Najdete v příslušné kroky [ **schématu správu kurzu**](saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Vytvořit data pro ukázku

V tomto kurzu provedení analýzy na prodejní data lístku. V aktuální krok generuje data lístku pro všechny klienty.  Tato data se později extrahuje pro analýzu. *Ujistěte se, když máte zřízenou dávky klientů, jak je popsáno výše, tak, aby smysluplný množství dat*. Dostatečně velké množství dat můžete zpřístupnit celou řadu různých lístku zakoupení vzory.

1. V **prostředí PowerShell ISE**, otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*a nastavte následující hodnotu:
    - **$DemoScenario** = **1** zakoupit lístky pro události na všechna místa
2. Stiskněte klávesu **F5** vytvoření lístku zakoupení historie pro každou událost v každé místo a spusťte skript.  Skript se spustí pro generování desetitisíce lístků několik minut.

### <a name="deploy-the-analytics-store"></a>Nasazení úložišti analytics
Často jsou množství transakcí horizontálně dělené databáze, které drží všechna data klientů. Musí agregovat data klienta z horizontálně dělené databáze do jednoho analytics úložiště. Agregace umožňuje efektivní dotazování data. V tomto kurzu databázi Azure SQL Database slouží k uložení agregovaná data.

V následujících krocích nasazení úložišti analýzy, která se nazývá **tenantanalytics**. Můžete také nasadit předdefinovaných tabulek, které jsou naplněny později v tomto kurzu:
1. V prostředí PowerShell ISE otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Nastavte proměnnou $DemoScenario ve skriptu tak, aby odpovídaly zvoleného analytics úložiště. Pro účely učení, se doporučuje databáze SQL bez columnstore.
    - Chcete-li použít SQL database bez columnstore, nastavte **$DemoScenario** = **2**
    - Chcete-li používat databázi SQL s columnstore, nastavte **$DemoScenario** = **3**  
3. Stiskněte klávesu **F5** spustit ukázkový skript (, který volá *nasadit TenantAnalytics<XX>.ps1* skriptu) vytváří úložišti analytics klienta. 

Teď, když máte nasazené aplikace a vyplněnou zajímavé údaje klienta, použijte [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) připojit **tenants1-mt -\<uživatele\>**  a **katalogu-mt -\<uživatele\>**  servery pomocí přihlášení = *vývojáře*, heslo = *P@ssword1*.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

V Průzkumníku objektů proveďte následující kroky:

1. Rozbalte *tenants1-mt -\<uživatele\>*  serveru.
2. Rozbalte uzel databáze a zobrazit *tenants1* databáze obsahující více klientů.
3. Rozbalte *katalogu-mt -\<uživatele\>*  serveru.
4. Ověřte, uvidíte úložišti analýzy a jobaccount databáze.

Zobrazit následující položky databáze v Průzkumníku objektů aplikace SSMS rozbalením uzlu úložiště analytics:

- Tabulky **TicketsRawData** a **EventsRawData** uložení nezpracovaná extrahovaná data z databáze klienta.
- Hvězdičky schématu tabulky se **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, a **dim_Dates** .
- **Sp_ShredRawExtractedData** uložená procedura se používá k naplnění hvězdičky schéma tabulky z tabulky nezpracovaná data.

![tenantAnalytics](media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extrakce dat 

### <a name="create-target-groups"></a>Vytvoření cílové skupiny 

Než budete pokračovat, ujistěte se, že jste nasadili databázi účet a jobaccount úlohy. V sadě další kroky elastické úlohy slouží k extrahování dat z databáze horizontálně dělené klientů a pro ukládání dat v úložišti analytics. Potom úlohu druhý Skartuje data a ukládá je do tabulek ve schématu hvězdičky. Tyto dvě úlohy dvou různé cílové skupiny, spouštění konkrétně **TenantGroup** a **AnalyticsGroup**. Úlohy extrahování spouští TenantGroup, který obsahuje všechny databáze klienta. Skartace úloha spouští AnalyticsGroup, obsahující jenom úložišti analytics. Vytvoření cílové skupiny pomocí následujících kroků:

1. V aplikaci SSMS, připojte k **jobaccount** databáze v katalogu-mt -\<uživatele\>.
2. V aplikaci SSMS, otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Změnit @User proměnné v horní části na skript, nahraďte <User> s hodnotou uživatel používá při nasazení aplikace Wingtip lístky SaaS víceklientské databáze.
4. Stiskněte klávesu **F5** spustit skript, který vytvoří dva cílové skupiny.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahování nezpracovaná data ze všech klientů

Transakce se mohou vyskytovat častěji u *lístku a k zákaznickým* dat, než pro *událostí a místo* data. Zvažte proto extrahování dat lístků a zákazník samostatně a častěji, než budete extrahovat data události a místo. V této části definují a plánování dvou samostatných úloh:

- Extrahujte data lístku a zákazníka.
- Extrahujte data události a místo.

Každá úloha extrahuje data a odešle ji do úložiště analýzy. Samostatná úloha existuje Skartuje extrahovaná data do analytics hvězdičky schema.

1. V aplikaci SSMS, připojte k **jobaccount** databáze v katalogu-mt -\<uživatele\> serveru.
2. V aplikaci SSMS, otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Upravit @User v horní části skriptu a nahradit <User> s uživatelským jménem použít při nasazení aplikace Wingtip lístky SaaS víceklientské databáze. 
4. Stiskněte klávesu **F5** spustit skript, který vytvoří a spustí úlohu, která extrahuje lístků a zákazníky data z databáze každého klienta. Úloha uloží data do úložiště analytics.
5. Dotaz TicketsRawData tabulky v databázi tenantanalytics zajistit, že je v tabulce naplněný lístky informace od všech klientů.

![ticketExtracts](media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Opakujte předchozí kroky, s výjimkou nahraďte tento čas **\ExtractTickets.sql** s **\ExtractVenuesEvents.sql** v kroku 2.

Úspěšně spustit úlohu naplní EventsRawData tabulky v úložišti analytics s informacemi o místa od všech klientů a nové události. 

## <a name="data-reorganization"></a>Reorganizaci dat

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Skartování extrahovaná data k naplnění hvězdičky schématu tabulek

Dalším krokem je skartovat extrahované nezpracovaná data do sady tabulek, které jsou optimalizované pro analytické dotazy. Hvězdičky – schéma se používá. Tabulka faktů centrální obsahuje jednotlivých lístků prodeje záznamy. Tabulky dimenzí se naplní dat týkajících se místa, události, zákazníků a nákupech kalendářní data. 

V této části kurzu definovat a spusťte úlohu, která sloučí extrahované nezpracovaná data s daty v tabulkách hvězdičky schématu. Po dokončení úlohy sloučení nezpracovaná data jsou odstraněna, ponechat připravení naplnit daty další klienta tabulky extrahovat úlohy.

1. V aplikaci SSMS, připojte k **jobaccount** databáze v katalogu-mt -\<uživatele\>.
2. V aplikaci SSMS, otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Stiskněte klávesu **F5** spustit skript, který chcete definovat úlohu, která volá sp_ShredRawExtractedData uložené procedury v úložišti analytics.
4. Povolit dostatek času pro úlohu úspěšně spustit.
    - Zkontrolujte **životního cyklu** sloupec tabulky jobs.jobs_execution pro stav úlohy. Ujistěte se, že úloha **úspěšné** než budete pokračovat. Spuštění úspěšné zobrazí data podobně jako v následujícím grafu:

![shreddingJob](media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Zkoumání dat

### <a name="visualize-tenant-data"></a>Vizualizace dat klienta

Data v tabulce hvězdičky schéma poskytuje všechny lístku prodejní data potřebná pro analýzy. Aby bylo snazší zobrazíte trendy ve velkých datových sad, potřebujete je vizualizovat graficky.  V této části se dozvíte, jak používat **Power BI** manipulaci a vizualizaci dat klienta jste extrahovali a uspořádány.

Pomocí následujících kroků pro připojení k Power BI a k importu zobrazení, která jste vytvořili dříve:

1. Spusťte Power BI desktop.
2. Na pásu karet Domů vyberte **načíst Data**a vyberte **více...** v nabídce.
3. V **načíst Data** okně vyberte Azure SQL Database.
4. V okně databáze přihlášení, zadejte název serveru (katalogu-mt -\<uživatele\>. database.windows.net). Vyberte **Import** pro **režim připojení dat**a pak klikněte na tlačítko OK. 

    ![powerBISignIn](media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Vyberte **databáze** v levém podokně, pak zadejte uživatelské jméno = *vývojáře*a zadejte heslo = *P@ssword1*. Klikněte na **Připojit**.  

    ![DatabaseSignIn](media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. V **Navigátor** podokně v části databáze analýzy, vyberte hvězdičky schéma tabulky: fact_Tickets dim_Events, dim_Venues, dim_Customers a dim_Dates. Potom vyberte **zatížení**. 

Blahopřejeme! Mít úspěšně načtena data do Power BI. Nyní můžete začít seznamovat se zajímavé vizualizace, se kterými získáte přehled o vašich klientů. Dále můžete provede analytics můžete povolit jak poskytovat řízené daty doporučení obchodní oddělení Wingtip lístků. K optimalizaci obchodní model a zákazník prostředí může pomoci doporučení.

Můžete začít analýza data prodeje lístků zobrazíte variace využití napříč místa. Vyberte následující možnosti v Power BI k vykreslení pruhový graf z celkového počtu lístků za účelem prodeje podle jednotlivých místo. Z důvodu náhodné variace v lístku generátoru může být odlišné výsledky.
 
![TotalTicketsByVenues](media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Předchozí výkresu potvrdí, že počet lístků za účelem prodeje podle jednotlivých místo se liší. Místa, které prodej lístků další se intenzivněji než místa, které prodej lístků méně pomocí služby. Může mít příležitost Zde můžete nastavit, přidělení prostředků podle potřeb různých klienta.

Dále můžete analyzovat dat a zjistit, jak prodej lístků lišit v průběhu času. Vyberte následující možnosti v Power BI k vykreslení celkový počet lístků prodaných každý den po dobu 60 dnů.
 
![SaleVersusDate](media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Předchozí grafu zobrazí prodejní Špička pro některé místa tohoto lístku. Tyto špičky posílit představu, že některé místa může být využívá systémové prostředky nepřiměřeně. Pokud není žádná zřejmé vzor dojít, když špičky.

Dále budete chtít podrobněji význam dní prodej ve špičce. Když tyto vrcholů se provádějí až po lístků, přejděte na prodej? K vykreslení lístky za účelem prodeje za den, vyberte následující možnosti v Power BI.

![SaleDayDistribution](media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Předchozí výkresu ukazuje, že některé místa prodeje spoustu lístků na první den prodej. Jakmile lístky přejděte do prodeje za tyto místa, nejspíš MAD – expresní. Tato shluků aktivita podle místa několik může mít vliv na služby u jiných klientů.

Můžete rozbalit dat znovu a zjistit, pokud tato MAD – expresní platí pro všechny události hostované tyto místa. V předchozí pozemků jste zaznamenali, že Contoso vzájemné součinnosti Hall prodává spoustu lístků, a že Contoso také Špička v lístku prodej v určité dny. Přehrání s možnostmi Power BI k vykreslení prodej kumulativní lístek pro vzájemné součinnosti Hall Contoso, zaměřené na prodej trendy pro jednotlivé události. Všechny události postupují stejným způsobem prodej?

![ContosoSales](media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Předchozí výkresu pro vzájemné součinnosti Hall Contoso ukazuje, že MAD – expresní neodehrává pro všechny události. Přehrání s nastavením filtru zobrazíte prodej trendy pro další místa.

Přehled o lístek prodávané vzory může vést Wingtip lístky za účelem optimalizace svůj obchodní model. Místo ukládání všichni klienti stejně, možná Wingtip vhodné zavést úrovně služeb s úrovně různých výkonu. Větší místa, které je potřeba prodej lístků další za den může být nabízí vyšší úroveň s vyšší smlouvu o úrovni služeb (SLA). Své databáze umístěna ve fondu s vyššími limity prostředků jednotlivé databáze může mít tyto místa. Jednotlivých úrovních služby může mít hodinové prodeje přidělení, s další poplatky za překročení přidělení. Větší místa, které mají pravidelné shluky prodeje by využívat vyšší úrovně a Wingtip lístky monetizovat své služby efektivněji.

Někteří zákazníci Wingtip lístky stížnost mezitím se čtením prodej dostatek lístků k odůvodnění, náklady na služby. Možná v tyto přehledy není příležitost zvýšit prodej lístků pro pod provádění místa. Vyšší prodej by zvýšit dosahovaný hodnotu této služby. Klikněte pravým tlačítkem na fact_Tickets a vyberte **novou measure**. Zadejte následující výraz pro nový míru názvem **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Vyberte následující možnosti vizualizace k vykreslení lístky procento za účelem prodeje podle jednotlivých místo k určení jejich relativní úspěch.

![analyticsViews](media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Předchozí výkresu ukazuje, že i když většina místa prodeje více než 80 % jejich lístků, některé jsou naopak obtížně zvládá vyplnění víc než poloviny licencí. Přehrání s hodnoty dobře vybrat maximální nebo minimální procento lístků prodaných pro každý místo.

Dříve prohloubit analýzy chcete zjistit, že prodej lístků mívají sklony dodržovat předvídatelný vzory. Toto zjišťování může umožní Wingtip lístků nápovědy nevedou podle očekávání místa zvyšte lístku prodej doporučením dynamické ceny. Toto zjišťování může odhalit příležitost využívat machine learning postupy k předvídání prodej lístků pro každou jednotlivou událost. Predikce může rovněž pro dopad na výnosy nabídky slevy na prodej lístků. Power BI Embedded může integrovat do aplikace událostí správy. Integrace může pomoct vizualizovat předpokládaných prodeje a účinek různých slevy. Aplikace by mohla pomoci navrhnout optimální slevu má být použita přímo ze zobrazení analýzy.

Jste zaznamenali trendů v datech klienta z aplikací Wingtip lístky SaaS víceklientské databáze. Můžete zamýšlené další způsoby, které aplikace může informovat obchodních rozhodnutí pro dodavatele aplikací SaaS. Dodavatelé můžete lépe nahrazovat potřebám svým klientům. Zpravidla v tomto kurzu má vybavený můžete pomocí nástrojů, které jsou potřeba provádět analýzy dat klienta na základě kterých vaše firmám rozhodnutí řízené daty.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Nasazení databáze analýzy klienta s tabulkami předem definované hvězdicového schématu
> - Použít elastické úlohy extrahovat data z databáze nástroje klienta
> - Sloučit extrahovaná data do tabulky ve schématu hvězdičky určený pro analýzu
> - Dotaz na databázi analýzy 
> - Sledovat trendy v datech klienta pomocí Power BI pro vizualizaci dat 

Blahopřejeme!

## <a name="additional-resources"></a>Další zdroje informací:

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). -->
- [Elastické úlohy](sql-database-elastic-jobs-overview.md).
