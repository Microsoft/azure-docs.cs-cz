---
title: Spouštění analytických dotazů
description: Analytické dotazy pro více tenantů využívají data extrahovaná z více Azure SQL Database databází v aplikaci s více klienty.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: f12c823f609ac309d4b5ddbbaa7d5a076a7bb9ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96447296"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Analýza mezi klienty pomocí extrahovaných dat – více tenantů aplikace
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
V tomto kurzu si projdete kompletní scénář analýzy pro víceklientské implementace. Tento scénář předvádí, jak může analýzy umožnit firmám provádět inteligentní rozhodnutí. Pomocí dat extrahovaných z databáze horizontálně dělené můžete pomocí analýzy získat přehled o chování tenanta, včetně jejich použití s ukázkovou aplikací Wingtip lístky SaaS. Tento scénář zahrnuje tři kroky: 

1.  **Extrahuje data** z každé databáze tenanta do úložiště analýz.
2.  **Optimalizujte extrahovaná data** pro zpracování analýz.
3.  Pomocí nástrojů **Business Intelligence** můžete vykreslit užitečné poznatky, které vám můžou pomoct při rozhodování. 

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> - Vytvořte úložiště analýzy tenanta, do kterého se budou data extrahovat.
> - Pomocí elastických úloh můžete extrahovat data z každé databáze tenanta do úložiště analýz.
> - Optimalizujte extrahovaná data (přeuspořádejte je do schématu hvězdičky).
> - Dotazování analytické databáze
> - Pomocí Power BI pro vizualizaci dat zvýrazněte trendy v datech tenanta a udělejte doporučení na vylepšení.

![Diagram zobrazuje přehled architektury používané pro tento článek.](./media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Model analýzy klientů offline

SaaS aplikace, které vyvíjíte, mají přístup k obrovskému množství dat tenanta uložených v cloudu. Data poskytují bohatou sadu přehledů o provozu a využití vaší aplikace a o chování klientů. Tyto přehledy můžou pořídit vývoj funkcí, vylepšení použitelnosti a další investice do aplikace a platformy.

Přístup k datům pro všechny klienty je jednoduchý, pokud jsou všechna data v jediné databázi s více klienty. Ale přístup je složitější v případě distribuované škály v tisících databází. Jedním ze způsobů, jak zkrotit složitosti, je extrahovat data do analytické databáze nebo datového skladu. Pak se Dotazujte na datový sklad, abyste mohli shromažďovat přehledy z dat lístků všech tenantů.

Tento kurz představuje kompletní scénář analýzy pro tuto ukázkovou aplikaci SaaS. Za prvé, elastické úlohy se používají k naplánování extrakce dat z každé databáze tenanta. Data se odesílají do úložiště analýz. Úložiště analýz může být buď SQL Database, nebo Azure synapse Analytics. Pro extrakci dat ve velkém měřítku [Azure Data Factory](../../data-factory/introduction.md) Commended.

V dalším kroku jsou agregovaná data skartována do sady tabulek se [schématem hvězdiček](https://www.wikipedia.org/wiki/Star_schema) . Tabulky sestávají z centrální tabulky faktů a souvisejících tabulek dimenzí:

- Centrální tabulka faktů ve schématu hvězdičky obsahuje data lístků.
- Tabulky dimenzí obsahují data o místech, událostech, zákaznících a nákupních datech.

Společně centrální tabulky a tabulky dimenzí umožňují efektivní analytické zpracování. Schéma hvězdičky používané v tomto kurzu se zobrazuje na následujícím obrázku:
 
![Databázový diagram zobrazuje čtyři databázové objekty, které jsou připojeny k objektu centrální databáze.](./media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Nakonec se dotazují tabulky schématu hvězdičky. Výsledky dotazu se zobrazují vizuálně, aby bylo zdůrazněno, co je přehled o chování tenanta a jejich použití. Pomocí tohoto schématu hvězdičky můžete spouštět dotazy, které vám pomůžou najít následující položky:

- Kdo je kupující lístky a ze kterého se má místo.
- Skryté vzory a trendy v následujících oblastech:
    - Prodej lístků.
    - Relativní oblíbenosti každé místo.

Porozumění, jak konzistentně každý tenant používá službu, nabízí možnost vytvářet plány služeb pro potřeby podle svých potřeb. V tomto kurzu najdete základní příklady přehledů, které je možné mohli z dat tenanta.

## <a name="setup"></a>Nastavení

### <a name="prerequisites"></a>Předpoklady

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Je nasazená databázová aplikace Wingtip Tickets SaaS s více klienty. Nasazení za méně než pět minut najdete v tématu [nasazení a prozkoumání SaaS aplikace pro více tenantů](./saas-multitenantdb-get-started-deploy.md) .
- Skripty a [zdrojový kód](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) aplikace Wingtip SaaS se stáhnou z GitHubu. Před extrakcí obsahu nezapomeňte *soubor zip odblokovat* . Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů Wingtip Tickets SaaS.
- Power BI Desktop je nainstalována. [Stažení Power BI Desktopu](https://powerbi.microsoft.com/downloads/)
- Dávky dalších tenantů se zřídily v [**kurzu zřízení tenantů**](./saas-multitenantdb-provision-and-catalog.md).
- Byl vytvořen Agent úlohy a databáze agenta úloh. Projděte si příslušné kroky v [**kurzu Správa schématu**](./saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Vytvoření dat pro ukázku

V tomto kurzu se analyzují data o prodeji lístku. V aktuálním kroku vygenerujete data lístku pro všechny klienty.  Později jsou tato data extrahována k analýze. Ujistěte se, že *máte zřízenou dávku tenantů, jak je popsáno výše, abyste měli smysluplnější objem dat*. Dostatečně velké množství dat může vystavovat rozsah různých způsobů nákupu lístků.

1. V **PowerShellu ISE** otevřete *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* a nastavte následující hodnotu:
    - **$DemoScenario**  =  **1** lístky nákupu pro události se všemi místy
2. Stisknutím klávesy **F5** spusťte skript a v každém z nich vytvořte historii nákupů lístků pro každou událost.  Skript se spustí několik minut, než se vygeneruje desítky tisíců lístků.

### <a name="deploy-the-analytics-store"></a>Nasazení úložiště analýzy
K dispozici je často mnoho transakčních horizontálně dělené databází, které dohromady uchovávají všechna data tenanta. Data tenanta musíte agregovat z databáze horizontálně dělené do jednoho úložiště analýz. Agregace umožňuje efektivní dotaz na data. V tomto kurzu se k ukládání agregovaných dat používá Azure SQL Database databáze.

V následujících krocích nasadíte úložiště analýzy, které se nazývá **tenantanalytics**. Nasadíte také předdefinované tabulky, které jsou vyplněny později v tomto kurzu:
1. V prostředí PowerShell ISE otevřete *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Nastavte ve skriptu $DemoScenario proměnnou tak, aby odpovídala vašemu výběru úložiště analýz. Pro účely učení se doporučuje používat databázi bez columnstore.
    - Pokud chcete použít SQL Database bez columnstore, nastavte **$DemoScenario**  =  **2** .
    - Pokud chcete použít SQL Database s columnstore, nastavte **$DemoScenario**  =  **3** .  
3. Stisknutím klávesy **F5** Spusťte ukázkový skript (který volá skript *Deploy-TenantAnalytics \<XX> . ps1* ), který vytvoří úložiště analýzy tenanta. 

Teď, když jste nasadili aplikaci a vyplnili ji zajímavými daty tenanta, pomocí [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) připojte **tenants1- \<User\> Mt-** a **Catalog- \<User\> Mt-** servery pomocí přihlašovacích údajů = *Developer*, Password = *P \@ ssword1*.

![architectureOverView](./media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

V Průzkumník objektů proveďte následující kroky:

1. Rozbalte *tenants1-MT- \<User\>* Server.
2. Rozbalte uzel databáze a podívejte se na *tenants1* Database obsahující několik tenantů.
3. Rozbalte *Catalog-MT- \<User\>* Server.
4. Ověřte, že se zobrazuje úložiště analýzy a databáze jobaccount.

Rozbalením uzlu analytického úložiště se podívejte na následující položky databáze Průzkumník objektů SSMS:

- Tabulky **TicketsRawData** a **EventsRawData** uchovávají nezpracovaná extrahovaná data z databází tenantů.
- Tabulky schématu hvězdiček jsou **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** a **dim_Dates**.
- Uložená procedura **sp_ShredRawExtractedData** slouží k naplnění tabulek schématu hvězdiček z nezpracovaných tabulek dat.

![Snímek obrazovky zobrazuje Průzkumník objektů S S M M S pro uzel úložiště analýzy, včetně tabulek, zobrazení a uzlů.](./media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extrakce dat 

### <a name="create-target-groups"></a>Vytváření cílových skupin 

Než budete pokračovat, ujistěte se, že jste nasadili účet úlohy a databázi jobaccount. V další sadě kroků se elastické úlohy používají k extrakci dat z databáze tenantů horizontálně dělené a k uložení dat do úložiště analýz. Pak druhá úloha Skartuje data a ukládá je do tabulek ve schématu hvězdičky. Tyto dvě úlohy se spouštějí ve dvou různých cílových skupinách, konkrétně ve skupině **tenantů** a službě **Analytics**. Úloha extrakce se spustí na Tenantovi tenanta, která obsahuje všechny databáze tenantů. Úloha skartace se spouští ve službě Analytics, která obsahuje jenom úložiště analýz. Vytvořte cílové skupiny pomocí následujících kroků:

1. V SSMS se připojte k databázi **jobaccount** v katalogu – MT- \<User\> .
2. V SSMS otevřete *. ..\Learning Modules\Operational Analytics\Tenant Analytics \ TargetGroups. SQL* 
3. Upravte @User proměnnou v horní části skriptu a nahraďte `<User>` ji hodnotou uživatele, která se používá při nasazení aplikace SaaS multi-tenant Database aplikace Wingtip lístky.
4. Stisknutím klávesy **F5** spusťte skript, který vytvoří dvě cílové skupiny.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahovat nezpracovaná data ze všech tenantů

Transakce se můžou u *lístků a zákaznických* dat vyskytnout častěji než pro data *událostí a* místa. Proto zvažte, že se data lístků a zákazníků odbalí samostatně a častěji než extrahují data událostí a míst. V této části definujete a naplánujete dvě samostatné úlohy:

- Extrahujte data lístku a zákazníka.
- Rozbalte událost a data místa.

Každá úloha extrahuje svá data a odešle je do úložiště analýz. V rámci samostatné úlohy se extrahovaná data Skartuje do schématu Analytics hvězda.

1. V SSMS se připojte k databázi **jobaccount** v katalogu – MT- \<User\> Server.
2. V SSMS otevřete *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.SQL*.
3. Upravte @User v horní části skriptu a nahraďte `<User>` uživatelským jménem, které jste použili při nasazení aplikace SaaS multi-tenant Database aplikace Wingtip lístky. 
4. Stisknutím klávesy **F5** spusťte skript, který vytvoří a spustí úlohu, která extrahuje údaje o lístkech a zákaznících z každé databáze tenanta. Úloha uloží data do úložiště analýz.
5. Dotaz na tabulku TicketsRawData v databázi tenantanalytics, aby se zajistilo, že se tabulka vyplní informacemi z lístků ze všech tenantů.

![Snímek obrazovky zobrazuje databázi ExtractTickets s TicketsRawDataem d b o vybraným v Průzkumník objektů.](./media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Opakujte předchozí kroky, kromě této doby nahraďte **\ExtractTickets.SQL** pomocí **\ExtractVenuesEvents.SQL** v kroku 2.

Úspěšné spuštění úlohy naplní tabulku EventsRawData v úložišti analýzy novými událostmi a místí informace ze všech tenantů. 

## <a name="data-reorganization"></a>Reorganizace dat

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Skartovat extrahovaná data pro naplnění tabulek se schématy s hvězdičkami

Dalším krokem je skartovat extrahovaná nezpracovaná data do sady tabulek, které jsou optimalizované pro analytické dotazy. Použije se schéma hvězdičky. Ústřední tabulka faktů obsahuje individuální záznamy o prodeji lístků. Tabulky dimenzí se naplní daty o místech, událostech, zákaznících a nákupních datech. 

V této části kurzu definujete a spustíte úlohu, která sloučí extrahovaná nezpracovaná data s daty v tabulkách se schématy hvězdiček. Po dokončení úlohy sloučení se nezpracovaná data odstraní a opustí tabulky připravené k vyplnění další úlohou extrakce dat tenanta.

1. V SSMS se připojte k databázi **jobaccount** v katalogu – MT- \<User\> .
2. V SSMS otevřete *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.SQL*.
3. Stisknutím klávesy **F5** spusťte skript pro definování úlohy, která volá sp_ShredRawExtractedData uloženou proceduru v analytickém úložišti.
4. Umožněte, aby úloha běžela dostatečně dlouho.
    - Stav úlohy najdete ve sloupci **životní cyklus** Jobs.jobs_execution tabulky. Než budete pokračovat, ujistěte se, že byla úloha **úspěšně dokončena** . Úspěšné spuštění zobrazuje data podobná následujícímu grafu:

![Snímek obrazovky ukazuje úspěšný výsledek spuštění sp_ShredRawExtractedData postupu.](./media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Zkoumání dat

### <a name="visualize-tenant-data"></a>Vizualizace dat tenanta

Data v tabulce hvězdiček-Schema poskytují všechna data o prodeji lístků potřebná pro vaši analýzu. Aby bylo snazší zobrazit trendy ve velkých datových sadách, je nutné graficky vizualizovat.  V této části se dozvíte, jak pomocí **Power BI** manipulovat s daty tenanta, která jste extrahovali a organizujete.

Pomocí následujících kroků se připojte k Power BI a importujte zobrazení, která jste vytvořili dříve:

1. Spusťte Power BI Desktop.
2. Na pásu karet domů vyberte **získat data** a pak klikněte na **Další...** z nabídky.
3. V okně **získat data** vyberte Azure SQL Database.
4. V okně přihlášení k databázi zadejte název vašeho serveru (Catalog-MT- \<User\> . Database.Windows.NET). Vyberte možnost **importovat** do **režimu připojení dat** a pak klikněte na tlačítko OK. 

    ![Snímek obrazovky se zobrazí dialogové okno SQL Server Database, kde můžete zadat server a databázi.](./media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. V levém podokně vyberte **databáze** a pak zadejte uživatelské jméno = *vývojář* a zadejte heslo = *P \@ ssword1*. Klikněte na **Připojit**.  

    ![Snímek obrazovky se zobrazí v dialogovém okně SQL Server databázi, kde můžete zadat uživatelské jméno a heslo.](./media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. V podokně **navigátor** v části analytická databáze vyberte tabulky schématu hvězdiček: fact_Tickets, dim_Events, dim_Venues, dim_Customers a dim_Dates. Pak vyberte **načíst**. 

Gratulujeme! Data byla úspěšně načtena do Power BI. Teď můžete začít zkoumat zajímavé vizualizace, které vám pomůžou získat přehled o vašich klientech. Dále vám ukážeme, jak vám analýza umožní poskytnout doporučení na základě dat pro obchodní tým Wingtip Tickets. Doporučení můžou přispět k optimalizaci obchodního modelu a prostředí pro zákazníky.

Začnete analýzou dat o prodeji lístků, abyste viděli variaci využití v rámci míst. Vyberte následující možnosti v Power BI k vykreslení pruhového grafu celkového počtu lístků prodávaných každým jejich konáním. V důsledku náhodné variace generátoru lístků se vaše výsledky můžou lišit.
 
![Snímek obrazovky ukazuje vizualizaci a ovládací prvky pro vizualizaci dat na pravé straně.](./media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Předchozí vykreslení potvrdí, že počet lístků prodávaných jednotlivými místy se liší. Místa, která prodávají další lístky, využívají vaši službu více než místo míst, které prodávají méně lístků. Tady může být příležitost přizpůsobit přidělování prostředků podle různých potřeb tenanta.

Data můžete dál analyzovat, abyste viděli, jak se v průběhu času mění prodej lístku. Vyberte následující možnosti v Power BI pro vykreslení celkového počtu lístků prodaných každý den po dobu 60 dnů.
 
![Snímek obrazovky zobrazuje vizualizaci Power B s názvem distribuce prodeje lístků vs. den prodeje.](./media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

V předchozím grafu se zobrazuje špička prodeje lístků pro určité místo. Tyto špičky posílí představu o tom, že některá místa můžou spotřebovávat systémové prostředky neúměrně. Zatím v době, kdy dojde k špičkám, neexistuje žádný zřejmý vzor.

Dále si přejete prozkoumat význam těchto dní v prodeji ve špičce. Kdy k těmto špičkám dochází po vypsání lístků na prodej? Pokud chcete vykreslit lístky prodávané za den, vyberte v Power BI následující možnosti.

![SaleDayDistribution](./media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Předchozí vykreslení ukazuje, že některé místa prodávají spoustu lístků v prvním dni prodeje. Jakmile se lístky dostanou při prodeji na těchto místech, zdá se, že se jedná o Mad – nespěcháte. Tento nárůst aktivity o několik míst může ovlivnit službu pro ostatní klienty.

Můžete znovu přejít k datům a zjistit, jestli je tato Mad – nespěcháte pravdivá pro všechny události hostované těmito místy. V předchozích zkusných grafech jste si ukázali, že se společnost Contoso provedla spoustu lístků a že společnost Contoso má v některých dnech špičku prodeje. Přehrajte si s využitím Power BI možností pro vykreslení kumulativního prodeje lístků pro společnost Contoso, které se zaměřují na trendy v prodeji jednotlivých událostí. Dodržují všechny události stejný vzor prodeje?

![ContosoSales](./media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Předchozí vykreslení pro ve firmě contoso zobrazuje, že Mad – nespěcháte pro všechny události neprobíhá. Přehrajte si s možnostmi filtru a podívejte se na trendy v prodeji pro další místa.

Přehledy o vzorech prodávajícího lístku mohou vést k optimalizaci obchodního modelu lístků společnosti Wingtip. Místo toho, aby všichni klienti současně nabíjíi, možná společnost Wingtip zavedla úrovně služeb s různými výpočetními velikostmi. Větší místa, která je potřeba k prodeji dalších lístků za den, vám může nabídnout vyšší úroveň s vyšší smlouvou o úrovni služeb (SLA). Těmto místům můžou být databáze umístěné ve fondu s vyššími limity pro prostředky pro každou databázi. Každá úroveň služby by mohla mít přidělenou hodinu v prodeji a za překročení přidělení se účtují další poplatky. Větší místa, která mají pravidelné nárůsty prodeje, by měla těžit z vyšších úrovní a lístky Wingtip můžou monetizovat své služby efektivněji.

Mezitím můžou někteří zákazníci, kteří si bojovat, podali za to, že si dostanou dostatek lístků za účelem zarovnání nákladů na službu. V těchto přehledech je možné zvýšit prodej lístků za účelem jejich konání. Vyšší tržby by zvýšily vnímanou hodnotu služby. Klikněte pravým tlačítkem fact_Tickets a vyberte možnost **Nová míra**. Pro novou míru nazvanou **AverageTicketsSold** zadejte následující výraz:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Vyberte následující možnosti vizualizace k vykreslení procentuálních lístků prodávaných každým místem k určení jejich relativního úspěchu.

![analyticsViews](./media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Předchozí vykreslení ukazuje, že i když většina míst prodává více než 80% svých lístků, některé jsou působit potíže, aby vyplnily více než polovinu stanic. Pokud chcete vybrat maximální nebo minimální procento lístků, které se prodávají pro každé místo, začněte s hodnotami.

Dříve jste prohloubi analýzu za účelem zjištění, že prodej lístku by měl být v úmyslu podstoupit předvídatelné vzory. Toto zjišťování může dovolit, aby lístky Wingtip pomohly zvýšit prodej lístků tím, že doporučí dynamické ceny. Tato funkce zjišťování by mohla odhalit příležitost využívat techniky strojového učení, aby bylo možné předpovědět prodej lístků pro každou událost. Předpovědi je také možné ovlivnit na tržby za nabídky slev při prodeji lístků. Power BI Embedded lze integrovat do aplikace pro správu událostí. Integrace by mohla vést k vizualizaci předpokládaných prodejů a účinku různých slev. Aplikace může přispět k přípravě optimální slevy, která se má použít přímo ze zobrazení analýzy.

Proznamenali jste trendy v datech tenanta z aplikace SaaS multi-tenant Database aplikace Wingtip lístky. Můžete vzájemně porozumět jiným způsobům, jak může aplikace informovat o obchodních rozhodnutích pro dodavatele aplikací SaaS. Dodavatelé můžou lépe přirozumět potřebám svých klientů. Snad v tomto kurzu jste si vystavili nástroje potřebné k provádění analýz v datech tenantů, které vašim firmám umožní rozhodování na základě dat.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Nasadila se databáze tenanta Analytics s předem definovanými tabulkami schémat hvězdičky.
> - Pro extrakci dat ze všech databází tenanta se použily elastické úlohy.
> - Sloučit extrahovaná data do tabulek ve schématu hvězdičky navržené pro analýzy
> - Dotazování analytické databáze 
> - Použití Power BI pro vizualizaci dat ke sledování trendů v datech tenanta 

Gratulujeme!

## <a name="additional-resources"></a>Další zdroje informací

Další [kurzy, které se vytvářejí na aplikaci Wingtip SaaS](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) 
- [Elastické úlohy](./elastic-jobs-overview.md).
- [Analýza mezi klienty pomocí extrahované aplikace pro jednoho tenanta](saas-tenancy-tenant-analytics.md)