---
title: Spouštění analytických dotazů v databázích Azure SQL | Dokumentace Microsoftu
description: Mezi tenanty analytických dotazů pomocí dat extrahovaných z více databází Azure SQL Database v aplikaci s více tenanty.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: 5de707f3f2e6a82d880363eea91fb8ce644fb3aa
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055033"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Pomocí analýzy napříč tenanty extrahovaná data – aplikace s více tenanty
 
V tomto kurzu si projdete dokončení analýzy scénář pro víceklientské implementaci. Tento scénář předvádí, jak analytics můžete povolit firmám inteligentní rozhodování. Použití dat extrahovaných z horizontálně dělené databáze pomocí analytics získat přehled o chování klienta, včetně jejich použití ukázkové SaaS aplikace Wingtip Tickets aplikace. Tento scénář zahrnuje tři kroky: 

1.  **Extrahovat data** z všechny databáze tenantů do analýzy úložiště.
2.  **Optimalizace extrahovaná data** pro zpracování analýzy.
3.  Použití **Business Intelligence** nástrojů pro kreslení na užitečné přehledy, které můžete Průvodce rozhodování. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření tenant, ke kterému analýza ukládat extrahovat data.
> - Extrahovat data z všechny databáze tenantů do analýzy úložiště pomocí elastických úloh.
> - Optimalizujte extrahovaná data (reorganize do hvězdicového schématu).
> - Dotazování databáze analýzy.
> - Pomocí Power BI pro vizualizace dat zvýrazněte trendy v datech tenanta a zkontrolujte doporučení pro vylepšení.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Vzor analýzy tenanta offline

Aplikace SaaS, které vyvíjíte mají přístup k obrovské množství dat tenantů, které jsou uložené v cloudu. Data poskytuje bohaté zdroj přehledů o provozu a využití vaší aplikace a chování klientům. Vývoj funkcí, vylepšení použitelnosti a další investice do aplikace a platformy můžete průvodce tyto přehledy.

Zpřístupnění dat pro všechny tenanty je jednoduché, když všechna data v jediné databázi s více tenanty. Ale přístup je složitější, při distribuci ve velkém měřítku napříč tisíci databázemi. Jedním způsobem, jak zkrotit složitost je extrakce dat pro analytické databáze nebo datového skladu. Potom dotazování datového skladu pro shromažďování přehledů z dat lístků ze všech tenantů.

Tento kurz nabízí kompletní analytics scénář pro tato ukázková aplikace SaaS. První, elastické úlohy se používají k plánování extrakce dat z všechny databáze tenantů. Data se odesílají do úložiště analytics. Analýza úložiště může být buď služby SQL Database nebo SQL Data Warehouse. Pro extrahování velkých objemů dat [Azure Data Factory](../data-factory/introduction.md) je stahování.

V dalším kroku je skartovány agregovaná data do sady [hvězdicového schématu](https://www.wikipedia.org/wiki/Star_schema) tabulky. Tabulky se skládají z tabulky faktů centrální a související tabulky dimenzí:

- Tabulky faktů centrální v hvězdicové schéma obsahuje data lístku.
- Tabulky dimenzí obsahují údaje o místa, události, zákazníky a koupit data.

Společně – střed a tabulky umožňují efektivní analytické zpracování dimenzí. Hvězdicové schéma používané v tomto kurzu se zobrazí na následujícím obrázku:
 
![StarSchema](media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Nakonec hvězdicového schématu tabulky se generuje dotaz. Výsledky dotazu jsou zobrazeny vizuálně zvýraznit přehled o chování tenantů a jejich používání aplikace. Pomocí této hvězdicového schématu můžete spouštět dotazy, které pomáhají zjistit položky vypadat asi takto:

- Kdo je nákup lístků a od které míst.
- Skryté vzorce a trendy v následujících oblastech:
    - Prodej lístků.
    - Relativní oblíbenost každé místo.

Vysvětlení, jak konzistentně každý tenant používá služba poskytuje možnost Vytvořit plány služeb pro jejich potřeby. Tento kurz obsahuje základní příklady přehledy, které mohou být úsporách získaných dat tenanta.

## <a name="setup"></a>Nastavení

### <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Databázi s více tenanty SaaS aplikace Wingtip Tickets aplikace je nasazená. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání aplikace Wingtip Tickets SaaS víceklientskou databází](saas-multitenantdb-get-started-deploy.md)
- SaaS aplikace Wingtip skripty a aplikace [zdrojový kód](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) se stáhnou z Githubu. Nezapomeňte *odblokovat soubor zip* před extrahováním jeho obsah. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skripty.
- Power BI Desktopu je nainstalovaná. [Stáhněte si Power BI Desktopu](https://powerbi.microsoft.com/downloads/)
- Zřízení dávku dalších tenantů, najdete v článku [ **kurz zřízení tenantů**](saas-multitenantdb-provision-and-catalog.md).
- Agent úlohy a databáze agenta úloh byly vytvořeny. Naleznete v příslušné kroky [ **kurzu Správa schématu**](saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Vytvoření dat pro tuto ukázku

V tomto kurzu se provádí analýzu na prodejní data lístku. V aktuální krok Generovat data lístku pro všechny tenanty.  Později je extrahován tato data pro analýzu. *Ujistěte se, jste zřídili dávku tenantů jak je popsáno výše, abyste docílili smysluplné množství dat*. Dostatečně velký objem dat můžete zpřístupnit celou řadu různých vzorcích nákupu lístků.

1. V **prostředí PowerShell ISE**, otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*a nastavte následující hodnotu:
    - **$DemoScenario** = **1** nákup lístků na akce na všech místech
2. Stisknutím klávesy **F5** ke spuštění skriptu a vytvoření historie pro každou událost v každé místo nákupu lístků.  Skript se spustí na několik minut a můžete generovat desítky tisíc lístků.

### <a name="deploy-the-analytics-store"></a>Nasazení analýzy úložiště
Často se mnoho transakční horizontálně dělené databáze, které společně obsahují všechna data tenanta. Data tenanta z horizontálně dělené databáze musíte agregovat do jednoho analýzy úložiště. Agregace umožňuje efektivní dotaz data. V tomto kurzu se používá databázi Azure SQL Database k ukládání agregovaná data.

V následujících krocích nasazení analýzy úložiště, která se nazývá **tenantanalytics**. Můžete také nasadit předdefinovaných tabulek, které se vyplní později v tomto kurzu:
1. Otevřít v PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Nastavte proměnnou $DemoScenario ve skriptu, tak, aby odpovídaly analýzy úložiště podle vašeho výběru. Pro účely výuky doporučujeme SQL database bez columnstore.
    - Chcete-li používat SQL database bez columnstore, nastavte **$DemoScenario** = **2**
    - Chcete-li používat SQL database prostřednictvím columnstore, nastavte **$DemoScenario** = **3**  
3. Stisknutím klávesy **F5** spustit ukázkový skript (, která volá *nasadit TenantAnalytics<XX>.ps1* skript) vytváří úložišti analýzy tenanta. 

Teď, když nasadíte aplikaci a naplní se zajímavá data tenanta, použijte [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) připojit **tenants1-mt –\<uživatele\>**  a **katalogu-mt –\<uživatele\>**  servery pomocí přihlášení = *developer*, heslo = *P@ssword1*.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

V Průzkumníku objektů proveďte následující kroky:

1. Rozbalte *tenants1-mt –\<uživatele\>*  serveru.
2. Rozbalte uzel databáze a zobrazit *tenants1* databázi obsahující více tenantů.
3. Rozbalte *katalogu-mt –\<uživatele\>*  serveru.
4. Ověřte, jestli analýzy úložiště a databázi jobaccount.

Viz následující položky databáze v Průzkumníku objektů aplikace SSMS rozbalením uzlu úložiště analytics:

- Tabulky **TicketsRawData** a **EventsRawData** obsahovat nezpracované extrahovaných dat z databáze tenantů.
- Hvězdicové schéma tabulky jsou **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, a **dim_Dates** .
- **Sp_ShredRawExtractedData** uložené procedury slouží k naplnění hvězdicového schématu tabulky z nezpracovaných dat tabulky.

![tenantAnalytics](media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extrakce dat 

### <a name="create-target-groups"></a>Vytvoření cílových skupin 

Než budete pokračovat, ujistěte se, že jste nasadili databáze účtu a jobaccount úloh. V další sadů kroků Elastických úloh slouží k extrakci dat z databáze horizontálně dělené tenantů a k uložení dat v úložišti analytics. Druhá úloha Skartuje data a uloží jej do tabulek v hvězdicové schéma. Tyto dvě úlohy spouštět dvě různé cílové skupiny, a to **TenantGroup** a **AnalyticsGroup**. Úloha extrahování spouští TenantGroup, který obsahuje všechny databáze tenantů. Skartace úloha spouští AnalyticsGroup, který obsahuje pouze analýzy úložiště. Vytvoření cílové skupiny pomocí následujících kroků:

1. V aplikaci SSMS připojit k **jobaccount** databáze v katalogu-mt –\<uživatele\>.
2. V aplikaci SSMS otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Upravit @User proměnné v horní části skriptu, nahrazení <User> hodnotou uživatele jste použili při nasazení aplikace Wingtip Tickets SaaS databázi s více tenanty.
4. Stisknutím klávesy **F5** pro spuštění skriptu, který vytvoří dvě cílové skupiny.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahovat nezpracovaná data ze všech tenantů

Může docházet k transakcím častěji pro *lístků a zákazníka* dat než pro *událostí a místní příslušností* data. Proto zvažte extrahování dat lístků a zákazníka odděleně a častěji, než extrahovat data události a místní příslušností. V této části definují a plánování dvou samostatných úloh:

- Extrahujte lístků a zákaznická data.
- Extrahujte data události a místní příslušností.

Každá úloha extrahuje data a publikuje do úložiště analytics. Samostatná úloha existuje Skartuje extrahovaná data do analýzy hvězdicového schématu.

1. V aplikaci SSMS připojit k **jobaccount** databáze v katalogu-mt –\<uživatele\> serveru.
2. V aplikaci SSMS otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Upravit @User v horní části skriptu a nahraďte <User> s uživatelským jménem, které jste použili při nasazení aplikace Wingtip Tickets SaaS databázi s více tenanty. 
4. Stisknutím klávesy **F5** pro spuštění skriptu, který vytvoří a spustí úlohu, extrahuje data lístků a zákazníkům z všechny databáze tenantů. Úloha uloží data do úložiště analytics.
5. Dotaz TicketsRawData tabulku v databázi tenantanalytics, ujistěte se, že v tabulce se vyplní informace lístků ze všech tenantů.

![ticketExtracts](media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Zopakujte předchozí kroky, s výjimkou nahradit tento čas **\ExtractTickets.sql** s **\ExtractVenuesEvents.sql** v kroku 2.

Úspěšně spustit úlohu naplní EventsRawData tabulky v úložišti analytics s nové události a venues informacemi ze všech tenantů. 

## <a name="data-reorganization"></a>Opětovné uspořádání dat

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Skartování extrahovaná data k vyplnění tabulky hvězdicového schématu

Dalším krokem je odstranění extrahované nezpracovaná data do sady tabulek, které jsou optimalizované pro analytické dotazy. Hvězdicové schéma se používá. Tabulku faktů centrální obsahuje prodejní záznamů jednotlivých lístků. Tabulky dimenzí se naplní daty o místa, události, zákazníky a koupit data. 

V této části kurzu definovat a spustit úlohu, která sloučí extrahované nezpracovaná data s daty v hvězdicové schéma tabulky. Po dokončení úlohy sloučení se nezpracovaná data se odstraní, byste museli opustit připravení vyplněn prostředkem další data tenanta tabulky extrahovat úlohy.

1. V aplikaci SSMS připojit k **jobaccount** databáze v katalogu-mt –\<uživatele\>.
2. V aplikaci SSMS otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Stisknutím klávesy **F5** spustit skript, který chcete definovat úlohu, která volá sp_ShredRawExtractedData uloženou proceduru v úložišti analytics.
4. Povolit dostatek času pro úlohu úspěšně spustit.
    - Zkontrolujte **životního cyklu** sloupec tabulky jobs.jobs_execution pro stav úlohy. Ujistěte se, že úloha **Succeeded** než budete pokračovat. Úspěšné spuštění zobrazí data, podobně jako v následující tabulce:

![shreddingJob](media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Zkoumání dat

### <a name="visualize-tenant-data"></a>Vizualizace dat klienta

Data v tabulce hvězdicového schématu obsahuje všechny prodejní data lístku potřeby vaší analýzy. Aby bylo snazší zobrazit trendy ve velkých sadách dat, budete muset vizualizovat graficky.  V této části se dozvíte, jak používat **Power BI** pracovat a vizualizace dat tenanta máte extrahována a uspořádané.

Následující kroky použijte pro připojení k Power BI a k importování zobrazení, která jste vytvořili dříve:

1. Spusťte Power BI desktopu.
2. Z pásu karet Domů vyberte **získat Data**a vyberte **více...** v nabídce.
3. V **získat Data** okna, vyberte databázi SQL Azure.
4. V okně databáze přihlášení, zadejte název serveru (catalog-mt –\<uživatele\>. database.windows.net). Vyberte **Import** pro **režim připojení dat**a potom klikněte na tlačítko OK. 

    ![powerBISignIn](media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Vyberte **databáze** v levém podokně, zadejte uživatelské jméno = *developer*a zadejte heslo = *P@ssword1*. Klikněte na **Připojit**.  

    ![DatabaseSignIn](media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. V **Navigátor** podokně v části analytickou databázi, vyberte hvězdicového schématu tabulky: fact_Tickets dim_Events, dim_Venues, dim_Customers a dim_Dates. Potom vyberte **zatížení**. 

Blahopřejeme! Úspěšně jste načtení dat do Power BI. Nyní můžete začít zkoumat zajímavé vizualizace, se kterými získáte přehled o vašich klientů. Dále si projdete analytics můžete povolit jak poskytovat doporučení na základě dat týmu obchodní aplikace Wingtip Tickets. Doporučení může pomoct optimalizovat prostředí obchodní model a zákazníků.

Začnete tím, že analýza prodejních dat lístku zobrazíte změnu ve využití napříč registrují. Vyberte následující možnosti v Power BI k vykreslení pruhový graf z celkového počtu lístků prodávaný společností každé místo. Z důvodu náhodných kolísání generátor lístek výsledky mohou být jiný.
 
![TotalTicketsByVenues](media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Předchozí diagram potvrdí, že počet lístky prodávaný společností každé místo se liší. Místa, které prodej lístků další nevyčerpáte než místa, které prodej lístků méně používání služby. Můžou existovat příležitost zde přizpůsobit přidělení prostředků podle potřeb jiného tenanta.

Můžete hlouběji analyzovat data chcete zobrazit, jak prodej lístků mění v čase. Vyberte následující možnosti v Power BI k vykreslení celkový počet lístky prodává každý den po dobu 60 dnů.
 
![SaleVersusDate](media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Předchozí graf zobrazuje prodeje zásobníku pro některé venues tohoto lístku. Čelí posílit myšlenkou, že některé venues může spotřebovávat systémové prostředky nepřiměřeně. V kdy provozní špičky uplatňují zatím není žádný zřejmý vzor.

Další chcete hlouběji analyzovat významu těchto dnů prodej ve špičce. Kdy tyto špičky nastat po tickets přechod na prodej? K vykreslení lístky prodaných za den, vyberte následující možnosti v Power BI.

![SaleDayDistribution](media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Předchozí diagram znázorňuje, že některé venues prodávat velké množství lístků na první den v prodeji. Jakmile tickets přechod na prodej na těchto místech, zřejmě MAD – špičce. Toto rozšíření aktivita podle místa několik může vliv na služby u jiných klientů.

Můžete zobrazit podrobnosti o datech znovu a zjistit, jestli tento MAD – špičce hodnotu true pro všechny události, které jsou hostitelem těchto místa. V předchozí graf jste zaznamenali, že Contoso koncertní Hall prodává velké množství lístků a také, že Contoso je prudký nárůst v prodeji lístků v určité dny. Pohrajte si s možnostmi Power BI k vykreslení prodeje kumulativní lístků na vzájemné součinnosti Hall Contoso, zaměřený na vývoj prodeje pro každý z jeho události. Všechny události postupujte podle stejného vzoru prodej?

![ContosoSales](media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Předchozí diagram pro Contoso koncertní Hall ukazuje MAD – špičce se neodehrává pro všechny události. Pohrajte si s možností filtrování zobrazit trendy prodeji pro jiné místa.

Přehled vzorů pro prodej lístků může vést Wingtip Tickets optimalizovat svůj obchodní model. Místo účtování za všechny tenanty stejně, možná byste Wingtip zavést úrovně služby s různých velikostech výpočetních prostředků. Větší místa, které je potřeba prodej lístků další každý den může nabízet vyšší úroveň s vyšším smlouvy o úrovni služeb (SLA). Tyto toho může mít své databáze umístěn ve fondu s vyššími limity prostředků na databázi. Jednotlivé úrovně služby může mít hodinové prodejní přidělení, se poplatky účtují se poplatky za překročení přidělení. Větší místa, které mají pravidelné nárůstům prodeje, budou těžit z vyšší úrovně a Wingtip Tickets efektivněji monetizovat své služby.

Zákazníci, kteří Wingtip Tickets stěžovat mezitím si, že se snaží prodej dostatek lístků k odůvodnění, náklady na služby. Možná v těchto přehledů je příležitost k posílení prodeje lístků na pod provádění místa. Vyšší sales by zvýšila vnímaná hodnotu této služby. Fact_Tickets klikněte pravým tlačítkem a vyberte **novou míru**. Zadejte následující výraz pro novou míru volá **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Vyberte následující možnosti vizualizace k vykreslení lístky procento prodávaný společností každé místo pro určení jejich relativního úspěchu.

![analyticsViews](media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Předchozí diagram ukazuje, i když většina venues prodávat více než 80 % svoje lístky, některé jsou jenom vyplnit více než polovinu licencovaných míst. Pohrajte si s hodnoty a vyberte maximální nebo minimální procento lístky prodává pro každé místo.

Dříve prohloubit vaše analýza ke zjištění, že prodej lístků mívají sklony dodržovat předvídatelným způsobem. Toto zjišťování může umožnit Wingtip Tickets nápovědy nevedou podle očekávání prodej lístků boost venues doporučením dynamické ceny. Toto zjišťování může odhalit příležitosti využívat machine learning postupy k předpovědi prodej lístků pro každou jednotlivou událost. Predikce může rovněž dopadu na výnosy z nabídky slevy na prodej lístků. Power BI Embedded může integrovat do aplikace správu událostí. Integrace může pomáhají vizualizovat předpokládané prodeje a vliv jiné slevy. Aplikace může pomoci navrhnout optimální slevy uplatňovat přímo ze zobrazení analytics.

Trendy v datech tenanta z aplikace v databázi s více tenanty SaaS aplikace Wingtip Tickets jste zaznamenali. Můžete tento další způsoby, jak aplikace může informovat obchodní rozhodnutí pro dodavatele aplikací SaaS. Dodavatelé můžete lépe vyhovovaly potřebám svým klientům. Snad v tomto kurzu má vybavené vám nástroje potřebné k provedení analýzy na data tenanta pro vaši podnikům datově řízená rozhodnutí.

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

Další [kurzy, které vycházejí z aplikace SaaS aplikace Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Elastické úlohy](sql-database-elastic-jobs-overview.md).
- [Pomocí analýzy napříč tenanty extrahovaná data – aplikace s jedním tenantem](saas-tenancy-tenant-analytics.md) 