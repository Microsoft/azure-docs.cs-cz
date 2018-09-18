---
title: Spouštět analýzy napříč tenanty pomocí extrahovaných dat | Dokumentace Microsoftu
description: Mezi tenanty analytických dotazů pomocí dat extrahovaných z několika databází Azure SQL Database.
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: sstein
ms.reviewer: anjangsh; billgib; genemi
ms.openlocfilehash: b887572e7c2bdf22c8a6cc8ffa1641e402fd6a58
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736423"
---
# <a name="cross-tenant-analytics-using-extracted-data"></a>Extrahovaná data pomocí analýzy napříč tenanty

V tomto kurzu si projdete scénáři dokončení analýzy. Tento scénář předvádí, jak analytics můžete povolit firmám inteligentní rozhodování. Použití dat extrahovaných z každé databázi tenantů, pomocí analytics získat přehled o tenantovi využití chování a aplikace. Tento scénář zahrnuje tři kroky: 

1.  **Extrahovat** data z každé databázi tenantů a **zatížení** do analýzy úložiště.
2.  **Transformace dat extrahovaných** pro zpracování analýzy.
3.  Použití **business intelligence** nástrojů pro kreslení na užitečné přehledy, které můžete Průvodce rozhodování. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření tenant, ke kterému analýza ukládat extrahovat data.
> - Extrahovat data z všechny databáze tenantů do analýzy úložiště pomocí elastických úloh.
> - Optimalizujte extrahovaná data (reorganize do hvězdicového schématu).
> - Dotazování databáze analýzy.
> - Pomocí Power BI pro vizualizace dat zvýrazněte trendy v datech tenanta a zkontrolujte doporučení pro vylepšení.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Vzor analýzy tenanta offline

Víceklientské aplikace SaaS mají obvykle velké množství dat tenantů, uložená v cloudu. Tato data poskytují bohatou příčiny přehledy o operaci a využití vaší aplikace a chování vašich klientů. Vývoj funkcí, vylepšení použitelnosti a další investice do aplikace a platformy můžete průvodce tyto přehledy.

Všechna data umístěná v jediné databázi s více tenanty je přístup k datům pro všechny tenanty jednoduché. Ale přístup je složitější, když distribuovat ve velkém měřítku napříč potenciálně tisíce databází. Jedním ze způsobů zkrotit složitost a minimalizovat dopad analytických dotazů na transakční data je extrakce dat do skladu analytics účel určená databáze nebo data.

Tento kurz představuje scénář dokončena analýza pro aplikace SaaS aplikace Wingtip Tickets. Nejprve je potřeba *Elastic Jobs* umožňuje extrahovat data z každé databázi tenantů a jejich načtení do pracovních tabulek v úložišti analytics. Analýza úložiště může být buď služby SQL Database nebo SQL Data Warehouse. Pro extrahování velkých objemů dat [Azure Data Factory](../data-factory/introduction.md) se doporučuje.

V dalším kroku agregovaná data se transformuje na sadu [hvězdicového schématu](https://www.wikipedia.org/wiki/Star_schema) tabulky. Tabulky se skládají z tabulky faktů centrální a související tabulky dimenzí.  Pro aplikace Wingtip Tickets:

- Tabulky faktů centrální v hvězdicové schéma obsahuje data lístku.
- Tabulky dimenzí popisují místa, události, zákazníky a koupit data.

Centrální tabulkami faktů a dimenzí společně umožňují efektivní analytické zpracování. Hvězdicové schéma používané v tomto kurzu můžete vidět na následujícím obrázku:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Nakonec je analýzy úložiště dotazovat pomocí **PowerBI** zvýrazněte přehled o chování tenantů a jejich použití aplikace Wingtip Tickets. Spuštění dotazů, které:
 
- Zobrazit relativní oblíbenost každé místo
- Zvýrazněte vzory v prodeji lístků pro jednotlivé události
- Zobrazit relativní úspěch různých místech v prodej, jejich události

Vysvětlení, jak je každý tenant pomocí služby se používá k prozkoumat možnosti pro zhodnocovat služby a zlepšování služby, které pomůžou tenantů úspěšnější. Tento kurz obsahuje základní příklady typů přehledy, které mohou být úsporách získaných dat tenanta.

## <a name="setup"></a>Nastavení

### <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Nasazení aplikace Wingtip Tickets SaaS databáze na Tenanta. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání SaaS aplikace Wingtip aplikace](saas-dbpertenant-get-started-deploy.md)
- Skriptů aplikace Wingtip Tickets SaaS databáze na Tenanta a aplikace [zdrojový kód](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) se stáhnou z Githubu. Zobrazit pokyny ke stažení. Nezapomeňte *odblokovat soubor zip* před extrahováním jeho obsah. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skripty.
- Power BI Desktopu je nainstalovaná. [Stáhněte si Power BI Desktopu](https://powerbi.microsoft.com/downloads/)
- Zřízení dávku dalších tenantů, najdete v článku [ **kurz zřízení tenantů**](saas-dbpertenant-provision-and-catalog.md).
- Byl vytvořen účet úlohy a databáze účtu úlohy. Naleznete v příslušné kroky [ **kurzu Správa schématu**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Vytvoření dat pro tuto ukázku

V tomto kurzu se provádí analýzu na prodejní data lístku. V aktuální krok Generovat data lístku pro všechny tenanty.  Později je extrahován tato data pro analýzu. *Ujistěte se, jste zřídili dávku tenantů jak je popsáno výše, abyste docílili smysluplné množství dat*. Dostatečně velký objem dat můžete zpřístupnit celou řadu různých vzorcích nákupu lístků.

1. Otevřít v PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*a nastavte následující hodnotu:
    - **$DemoScenario** = **1** nákup lístků na akce na všech místech
2. Stisknutím klávesy **F5** ke spuštění skriptu a vytvoření historie pro každou událost v každé místo nákupu lístků.  Skript se spustí na několik minut a můžete generovat desítky tisíc lístků.

### <a name="deploy-the-analytics-store"></a>Nasazení analýzy úložiště
Často se mnoho pro transakční databáze, které společně obsahují všechny data tenanta. Data tenanta z mnoha transakční databáze musíte agregovat do jednoho analýzy úložiště. Agregace umožňuje efektivní dotaz data. V tomto kurzu se používá databázi Azure SQL Database k ukládání agregovaná data.

V následujících krocích nasazení analýzy úložiště, která se nazývá **tenantanalytics**. Můžete také nasadit předdefinovaných tabulek, které se vyplní později v tomto kurzu:
1. Otevřít v PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Nastavte proměnnou $DemoScenario ve skriptu, tak, aby odpovídaly podle vašeho výběru analýzy úložiště:
    - Chcete-li používat SQL database bez úložiště sloupců, nastavte **$DemoScenario** = **2**
    - Chcete-li používat SQL database prostřednictvím úložiště sloupců, nastavte **$DemoScenario** = **3**  
3. Stisknutím klávesy **F5** spustit ukázkový skript (, která volá *nasadit TenantAnalytics<XX>.ps1* skript) vytváří úložišti analýzy tenanta. 

Teď, když nasadíte aplikaci a naplní se zajímavá data tenanta, použijte [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) připojit **tenants1-dpt -&lt;uživatele&gt;**  a **katalogu-dpt -&lt;uživatele&gt;**  servery pomocí přihlášení = *developer*, heslo = *P@ssword1*. Zobrazit [úvodní tutoriál](saas-dbpertenant-wingtip-app-overview.md) další pokyny.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

V Průzkumníku objektů proveďte následující kroky:

1. Rozbalte *tenants1-dpt -&lt;uživatele&gt;*  serveru.
2. Rozbalte uzel databáze a zobrazit seznam databází tenantů.
3. Rozbalte *katalogu-dpt -&lt;uživatele&gt;*  serveru.
4. Ověřte, jestli analýzy úložiště a databázi jobaccount.

Viz následující položky databáze v Průzkumníku objektů aplikace SSMS rozbalením uzlu úložiště analytics:

- Tabulky **TicketsRawData** a **EventsRawData** obsahovat nezpracované extrahovaných dat z databáze tenantů.
- Hvězdicové schéma tabulky jsou **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, a **dim_Dates** .
- Uložené procedury slouží k naplnění hvězdicového schématu tabulky z nezpracovaných dat tabulky.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extrakce dat 

### <a name="create-target-groups"></a>Vytvoření cílových skupin 

Než budete pokračovat, ujistěte se, že jste nasadili databáze účtu a jobaccount úloh. V další sadů kroků Elastických úloh používá extrahovat data z každé databázi tenantů a k uložení dat v úložišti analytics. Druhá úloha Skartuje data a uloží jej do tabulek v hvězdicové schéma. Tyto dvě úlohy spouštět dvě různé cílové skupiny, a to **TenantGroup** a **AnalyticsGroup**. Úloha extrahování spouští TenantGroup, který obsahuje všechny databáze tenantů. Skartace úloha spouští AnalyticsGroup, který obsahuje pouze analýzy úložiště. Vytvoření cílové skupiny pomocí následujících kroků:

1. V aplikaci SSMS připojit k **jobaccount** databáze v katalogu-dpt -&lt;uživatele&gt;.
2. V aplikaci SSMS otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Upravit @User proměnné v horní části skriptu, nahrazení <User> hodnotou uživatele jste použili při nasazení SaaS aplikace Wingtip.
4. Stisknutím klávesy **F5** pro spuštění skriptu, který vytvoří dvě cílové skupiny.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahovat nezpracovaná data ze všech tenantů

Podrobné údaje úpravy mohou vyskytovat častěji u *lístků a zákazníka* dat než pro *událostí a místní příslušností* data. Proto zvažte extrahování dat lístků a zákazníka odděleně a častěji, než extrahovat data události a místní příslušností. V této části definují a plánování dvou samostatných úloh:

- Extrahujte lístků a zákaznická data.
- Extrahujte data události a místní příslušností.

Každá úloha extrahuje data a publikuje do úložiště analytics. Samostatná úloha existuje Skartuje extrahovaná data do analýzy hvězdicového schématu.

1. V aplikaci SSMS připojit k **jobaccount** databáze v katalogu-dpt -&lt;uživatele&gt; serveru.
2. V aplikaci SSMS otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Upravit @User v horní části skriptu a nahraďte <User> s uživatelským jménem, které jste použili při nasazení SaaS aplikace Wingtip 
4. Stisknutím klávesy F5 spusťte skript, který vytvoří a spustí úlohu, který extrahuje data lístků a zákazníkům z všechny databáze tenantů. Úloha uloží data do úložiště analytics.
5. Dotaz TicketsRawData tabulku v databázi tenantanalytics, ujistěte se, že v tabulce se vyplní informace lístků ze všech tenantů.

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Zopakujte předchozí kroky, s výjimkou nahradit tento čas **\ExtractTickets.sql** s **\ExtractVenuesEvents.sql** v kroku 2.

Úspěšně spustit úlohu naplní EventsRawData tabulky v úložišti analytics s nové události a venues informacemi ze všech tenantů. 

## <a name="data-reorganization"></a>Opětovné uspořádání dat

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Skartování extrahovaná data k vyplnění tabulky hvězdicového schématu

Dalším krokem je odstranění extrahované nezpracovaná data do sady tabulek, které jsou optimalizované pro analytické dotazy. Hvězdicové schéma se používá. Tabulku faktů centrální obsahuje prodejní záznamů jednotlivých lístků. Další tabulky se vyplní se souvisejícími daty o místa, události a zákazníky. A jsou tabulky dimenzí čas. 

V této části kurzu definovat a spustit úlohu, která sloučí extrahované nezpracovaná data s daty v hvězdicové schéma tabulky. Po dokončení úlohy sloučení se nezpracovaná data se odstraní, byste museli opustit připravení vyplněn prostředkem další data tenanta tabulky extrahovat úlohy.

1. V aplikaci SSMS připojit k **jobaccount** databáze v katalogu-dpt -&lt;uživatele&gt;.
2. V aplikaci SSMS otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Stisknutím klávesy **F5** spustit skript, který chcete definovat úlohu, která volá sp_ShredRawExtractedData uloženou proceduru v úložišti analytics.
4. Povolit dostatek času pro úlohu úspěšně spustit.
    - Zkontrolujte **životního cyklu** sloupec tabulky jobs.jobs_execution pro stav úlohy. Ujistěte se, že úloha **Succeeded** než budete pokračovat. Úspěšné spuštění zobrazí data, podobně jako v následující tabulce:

![Skartace](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Zkoumání dat

### <a name="visualize-tenant-data"></a>Vizualizace dat klienta

Data v tabulce hvězdicového schématu obsahuje všechny prodejní data lístku potřeby vaší analýzy. Aby bylo snazší zobrazit trendy ve velkých sadách dat, budete muset vizualizovat graficky.  V této části se dozvíte, jak používat **Power BI** pracovat a vizualizace dat tenanta máte extrahována a uspořádané.

Následující kroky použijte pro připojení k Power BI a k importování zobrazení, která jste vytvořili dříve:

1. Spusťte Power BI desktopu.
2. Z pásu karet Domů vyberte **získat Data**a vyberte **více...** v nabídce.
3. V **získat Data** okna, vyberte databázi SQL Azure.
4. V okně databáze přihlášení, zadejte název serveru (catalog-dpt -&lt;uživatele&gt;. database.windows.net). Vyberte **Import** pro **režim připojení dat**a potom klikněte na tlačítko OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Vyberte **databáze** v levém podokně, zadejte uživatelské jméno = *developer*a zadejte heslo = *P@ssword1*. Klikněte na **Připojit**.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. V **Navigátor** podokně v části analytickou databázi, vyberte hvězdicového schématu tabulky: fact_Tickets dim_Events, dim_Venues, dim_Customers a dim_Dates. Potom vyberte **zatížení**. 

Blahopřejeme! Úspěšně jste načtení dat do Power BI. Nyní můžete začít zkoumat zajímavé vizualizace, se kterými získáte přehled o vašich klientů. Dále si projdete analytics můžete povolit jak poskytovat doporučení na základě dat týmu obchodní aplikace Wingtip Tickets. Doporučení může pomoct optimalizovat prostředí obchodní model a zákazníků.

Začnete tím, že analýza prodejních dat lístku zobrazíte změnu ve využití napříč registrují. Vyberte následující možnosti v Power BI k vykreslení pruhový graf z celkového počtu lístků prodávaný společností každé místo. Z důvodu náhodných kolísání generátor lístek výsledky mohou být jiný.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Předchozí diagram potvrdí, že počet lístky prodávaný společností každé místo se liší. Místa, které prodej lístků další nevyčerpáte než místa, které prodej lístků méně používání služby. Můžou existovat příležitost zde přizpůsobit přidělení prostředků podle potřeb jiného tenanta.

Můžete hlouběji analyzovat data chcete zobrazit, jak prodej lístků mění v čase. Vyberte následující možnosti v Power BI k vykreslení celkový počet lístky prodává každý den po dobu 60 dnů.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Předchozí graf zobrazuje prodeje zásobníku pro některé venues tohoto lístku. Čelí posílit myšlenkou, že některé venues může spotřebovávat systémové prostředky nepřiměřeně. V kdy provozní špičky uplatňují zatím není žádný zřejmý vzor.

Další chcete hlouběji analyzovat významu těchto dnů prodej ve špičce. Kdy tyto špičky nastat po tickets přechod na prodej? K vykreslení lístky prodaných za den, vyberte následující možnosti v Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Předchozí diagram znázorňuje, že některé venues prodávat velké množství lístků na první den v prodeji. Jakmile tickets přechod na prodej na těchto místech, zřejmě MAD – špičce. Toto rozšíření aktivita podle místa několik může vliv na služby u jiných klientů.

Můžete zobrazit podrobnosti o datech znovu a zjistit, jestli tento MAD – špičce hodnotu true pro všechny události, které jsou hostitelem těchto místa. V předchozí graf jste zaznamenali, že Contoso koncertní Hall prodává velké množství lístků a také, že Contoso je prudký nárůst v prodeji lístků v určité dny. Pohrajte si s možnostmi Power BI k vykreslení prodeje kumulativní lístků na vzájemné součinnosti Hall Contoso, zaměřený na vývoj prodeje pro každý z jeho události. Všechny události postupujte podle stejného vzoru prodej?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Předchozí diagram pro Contoso koncertní Hall ukazuje MAD – špičce se neodehrává pro všechny události. Pohrajte si s možností filtrování zobrazit trendy prodeji pro jiné místa.

Přehled vzorů pro prodej lístků může vést Wingtip Tickets optimalizovat svůj obchodní model. Místo účtování za všechny tenanty stejně, možná byste Wingtip zavést úrovně služby s různých velikostech výpočetních prostředků. Větší místa, které je potřeba prodej lístků další každý den může nabízet vyšší úroveň s vyšším smlouvy o úrovni služeb (SLA). Tyto toho může mít své databáze umístěn ve fondu s vyššími limity prostředků na databázi. Jednotlivé úrovně služby může mít hodinové prodejní přidělení, se poplatky účtují se poplatky za překročení přidělení. Větší místa, které mají pravidelné nárůstům prodeje, budou těžit z vyšší úrovně a Wingtip Tickets efektivněji monetizovat své služby.

Zákazníci, kteří Wingtip Tickets stěžovat mezitím si, že se snaží prodej dostatek lístků k odůvodnění, náklady na služby. Třeba v těchto přehledů je příležitost k posílení prodeje lístků na nevedou místa podle očekávání. Vyšší sales by zvýšila vnímaná hodnotu této služby. Fact_Tickets klikněte pravým tlačítkem a vyberte **novou míru**. Zadejte následující výraz pro novou míru volá **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Vyberte následující možnosti vizualizace k vykreslení lístky procento prodávaný společností každé místo pro určení jejich relativního úspěchu.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Předchozí diagram ukazuje, i když většina venues prodávat více než 80 % svoje lístky, některé jsou jenom vyplnit více než polovinu licencovaných míst. Pohrajte si s hodnoty a vyberte maximální nebo minimální procento lístky prodává pro každé místo.

Dříve prohloubit vaše analýza ke zjištění, že prodej lístků mívají sklony dodržovat předvídatelným způsobem. Toto zjišťování může umožnit Wingtip Tickets nápovědy nevedou podle očekávání prodej lístků boost venues doporučením dynamické ceny. Toto zjišťování může odhalit příležitosti využívat machine learning postupy k předpovědi prodej lístků pro každou jednotlivou událost. Predikce může rovněž dopadu na výnosy z nabídky slevy na prodej lístků. Power BI Embedded může integrovat do aplikace správu událostí. Integrace může pomáhají vizualizovat předpokládané prodeje a vliv jiné slevy. Aplikace může pomoci navrhnout optimální slevy uplatňovat přímo ze zobrazení analytics.

Trendy v datech tenanta z aplikace WingTip jste zaznamenali. Můžete tento další způsoby, jak aplikace může informovat obchodní rozhodnutí pro dodavatele aplikací SaaS. Dodavatelé můžete lépe vyhovovaly potřebám svým klientům. Snad v tomto kurzu má vybavené vám nástroje potřebné k provedení analýzy na data tenanta pro vaši podnikům datově řízená rozhodnutí.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Nasazení analytické databáze tenantů s předem definovaných hvězdicového schématu tabulky
> - Elastické úlohy umožňují extrahovat data z všechny databáze tenantů
> - Sloučit extrahovaná data do tabulek v hvězdicové schéma navržená pro analýzy
> - Dotazování databáze analýzy 
> - Pomocí Power BI pro vizualizace dat můžete sledovat trendy v datech tenanta 

Blahopřejeme!

## <a name="additional-resources"></a>Další zdroje informací:

- Další [kurzy, které vycházejí z aplikace SaaS aplikace Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Elastické úlohy](sql-database-elastic-jobs-overview.md).
