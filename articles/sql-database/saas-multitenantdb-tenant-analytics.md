---
title: Spuštění analytických dotazů
description: Analytické dotazy napříč tenanty pomocí dat extrahovaných z více databází Azure SQL Database v aplikaci s více klienty.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
ms.date: 09/19/2018
ms.openlocfilehash: 067afd09f942b8062825553a3cf90f715e8d3938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822151"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Analýzy napříč tenanty pomocí extrahovaných dat – víceklientská aplikace
 
V tomto kurzu projdete kompletní analytický scénář pro víceklientské implementace. Scénář ukazuje, jak může analytika umožnit firmám činit chytrá rozhodnutí. Pomocí dat extrahovaných z databáze s rozdělením, pomocí analýzy získat přehled o chování klienta, včetně jejich použití ukázkové wingtip vstupenky SaaS aplikace. Tento scénář zahrnuje tři kroky: 

1.  **Extrahujte data** z každé databáze klienta do úložiště analýzy.
2.  **Optimalizujte extrahovaná data** pro zpracování analýzy.
3.  Pomocí nástrojů **Business Intelligence** můžete získat užitečné poznatky, které mohou řídit rozhodování. 

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> - Vytvořte úložiště analýzy tenanta pro extrahování dat do.
> - Pomocí elastických úloh extrahujte data z každé databáze klienta do úložiště analýz.
> - Optimalizujte extrahovaná data (reorganizovat do hvězdného schématu).
> - Dotaz na databázi analýzy.
> - Pomocí Power BI pro vizualizaci dat můžete zvýraznit trendy v datech tenanta a doporučit zlepšení.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Vzor analýzy offline tenanta

Aplikace SaaS, které vyvíjíte, mají přístup k obrovskému množství dat klientů uložených v cloudu. Data poskytují bohatý zdroj informací o provozu a využití vaší aplikace a o chování klientů. Tyto přehledy mohou vést vývoj funkcí, vylepšení použitelnosti a další investice do aplikace a platformy.

Přístup k datům pro všechny klienty je jednoduchý, když všechna data jsou pouze v jedné databázi více klientů. Ale přístup je složitější při distribuci ve velkém měřítku napříč tisíci databázemi. Jedním ze způsobů, jak zkrotit složitost, je extrahovat data do analytické databáze nebo datového skladu. Potom dotaz datového skladu shromažďovat přehledy z dat lístky všech klientů.

Tento kurz představuje kompletní analytický scénář pro tuto ukázkovou aplikaci SaaS. Nejprve elastické úlohy se používají k plánování extrakce dat z každé databáze klienta. Data jsou odeslána do analytického úložiště. Úložiště analýzy může být buď SQL Database nebo SQL Data Warehouse. Pro rozsáhlé extrakce dat [Azure Data Factory](../data-factory/introduction.md) je pochválen.

Dále jsou agregovaná data skartována do sady tabulek [hvězdného schématu.](https://www.wikipedia.org/wiki/Star_schema) Tabulky se skládají z tabulky centrálních faktů a souvisejících tabulek dimenzí:

- Tabulka centrálních faktů ve schématu hvězdobsahuje data o lístku.
- Tabulky dimenzí obsahují data o místech konání, událostech, zákaznících a datech nákupu.

Společně centrální a dimenze tabulky umožňují efektivní analytické zpracování. Hvězdné schéma použité v tomto kurzu je zobrazeno na následujícím obrázku:
 
![Hvězdák](media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Nakonec jsou dotazovány tabulky schématu hvězdy. Výsledky dotazu jsou zobrazeny vizuálně zvýraznit přehled chování klienta a jejich použití aplikace. Pomocí tohoto schématu hvězdmůžete spouštět dotazy, které pomáhají zjišťovat položky, jako je následující:

- Kdo kupuje vstupenky a z jakého místa.
- Skryté vzory a trendy v následujících oblastech:
    - Prodej vstupenek.
    - Relativní popularita každého místa.

Pochopení toho, jak konzistentně každý klient používá službu poskytuje příležitost k vytvoření plánů služeb, které uspokojí jejich potřeby. Tento kurz obsahuje základní příklady přehledů, které lze získat z dat klienta.

## <a name="setup"></a>Nastavení

### <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Wingtip Vstupenky SaaS Víceklientdatabáze aplikace je nasazena. Informace o nasazení za méně než pět minut najdete v [tématu Nasazení a prozkoumání aplikace Wingtip Tickets SaaS Multi-tenant Database](saas-multitenantdb-get-started-deploy.md)
- Wingtip SaaS skripty a [zdrojový kód](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) aplikace jsou staženy z GitHubu. Nezapomeňte *odblokovat soubor zip* před extrahováníjeho obsahu. Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování wingtip vstupenky SaaS skripty.
- Power BI Desktop je nainstalovaný. [Stažení Power BI Desktopu](https://powerbi.microsoft.com/downloads/)
- Dávka dalších klientů byla zřízena, viz [**kurz Zřízení klientů**](saas-multitenantdb-provision-and-catalog.md).
- Byl vytvořen agent úlohy a databáze agenta úlohy. Podívejte se na příslušné kroky v [**kurzu pro správu schématu**](saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Vytvoření dat pro ukázku

V tomto kurzu se analýza provádí na datech prodeje vstupenek. V aktuálním kroku vygenerujete data lístku pro všechny klienty.  Později jsou tato data extrahována pro analýzu. *Ujistěte se, že jste zřízené dávky klientů, jak je popsáno výše, takže máte smysluplné množství dat*. Dostatečně velké množství dat může vystavit řadu různých vzorců nákupu vstupenek.

1. V **prostředí PowerShell ISE**otevřete *...\Výukové moduly\Provozní analýza\Analýza tenanta\Demo-TenantAnalytics.ps1*a nastavte následující hodnotu:
    - **$DemoScenario** = **1** Nákup vstupenek na akce na všech místech
2. Stisknutím **klávesy F5** spustíte skript a vytvoříte historii nákupu vstupenek pro každou událost v každém místě.  Skript běží několik minut generovat desítky tisíc vstupenek.

### <a name="deploy-the-analytics-store"></a>Nasazení úložiště analýzy
Často existují četné transakční databáze s oddíly, které společně obsahovat všechna data klienta. Je nutné agregovat data klienta z rozdělené databáze do jednoho úložiště analýzy. Agregace umožňuje efektivní dotaz na data. V tomto kurzu databáze Azure SQL Database se používá k ukládání agregovaných dat.

V následujících krocích nasadíte úložiště analýzy, které se nazývá **tenantanalytics**. Můžete také nasadit předdefinované tabulky, které jsou naplněny později v kurzu:
1. V prostředí PowerShell ISE otevřete *...\Výukové moduly\Provozní analýza\Analýza tenanta\Demo-TenantAnalytics.ps1* 
2. Nastavte proměnnou $DemoScenario ve skriptu tak, aby odpovídala vašemu výběru úložiště analýzy. Pro účely učení sql databáze bez columnstore se doporučuje.
    - Chcete-li použít databázi SQL bez columnstore, nastavte **$DemoScenario** = **2**
    - Chcete-li použít databázi SQL s columnstore, nastavte **$DemoScenario** = **3**  
3. Stisknutím **klávesy F5** spusťte ukázkový skript (který volá skript *\<Deploy-TenantAnalytics XX>.ps1),* který vytvoří úložiště analýzy klientů. 

Nyní, když jste nasadili aplikaci a naplnili ji zajímavými daty klienta, použijte [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) pro připojení **tenantů1-mt-\<User\> ** a **catalog-mt-\<User\> ** servery pomocí Login = *developer*, Password = *P\@ssword1*.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

V Průzkumníkovi objektů proveďte následující kroky:

1. Rozbalte *\<klienty1-mt-\> Uživatelský* server.
2. Rozbalte uzel Databáze a podívejte se na *databázi tenantů1* obsahující více klientů.
3. Rozbalte *\<katalog-mt-\> Uživatelský* server.
4. Ověřte, zda se zobrazí úložiště analýzy a databáze účtů úloh.

Rozbalte uzel úložiště analýzy v následujících položkách databáze v Průzkumníku objektů SSMS:

- Tabulky **TicketsRawData** a **EventsRawData** obsahují nezpracovaná extrahovaná data z databází klienta.
- Tabulky schématu hvězdjsou **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**a **dim_Dates**.
- Uložená procedura **sp_ShredRawExtractedData** se používá k naplnění tabulek hvězdného schématu z tabulek nezpracovaných dat.

![tenantAnalytics](media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extrakce dat 

### <a name="create-target-groups"></a>Vytvořit cílové skupiny 

Než budete pokračovat, ujistěte se, že jste nasadili účet úlohy a databázi jobaccount. V další sadě kroků elastické úlohy se používá k extrahování dat z databáze s oddíly klienty a k ukládání dat v úložišti analýzy. Pak druhá úloha skartuje data a uloží je do tabulek ve schématu hvězdy. Tyto dvě úlohy jsou spuštěny proti dvěma různým cílovým skupinám, konkrétně **skupinám TenantGroup** a **AnalyticsGroup**. Úloha extraktu běží proti TenantGroup, která obsahuje všechny databáze klienta. Úloha skartace je spuštěna proti analyticsgroup, která obsahuje pouze úložiště analýzy. Vytvořte cílové skupiny pomocí následujících kroků:

1. V SSMS se připojte k databázi **jobaccount** v catalog-mt-\<User\>.
2. V SSMS otevřete *...\Výukové moduly\Provozní analýza\Analýza tenanta\ TargetGroups.sql* 
3. Upravte @User proměnnou v horní části `<User>` skriptu a nahraďte ji uživatelskou hodnotou použitou při nasazení aplikace Wingtip Tickets SaaS Multi-tenant Database.
4. Stisknutím **klávesy F5** spusťte skript, který vytvoří dvě cílové skupiny.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahovat nezpracovaná data ze všech klientů

Transakce se mohou vyskytovat častěji pro data *o letenkách a zákaznících* než pro data *událostí a místa konání.* Proto zvažte extrahování dat o vstupenkách a zákaznících samostatně a častěji než extrahovat data událostí a míst konání. V této části definujete a naplánujete dvě samostatné úlohy:

- Extrahujte data o lístku a zákaznících.
- Extrahujte data událostí a míst konání.

Každá úloha extrahuje svá data a zaúčtuje je do úložiště analýz. Tam samostatná úloha skartuje extrahovaná data do schématu hvězd analýzy.

1. V SSMS se připojte k databázi **jobaccount** v katalogu-mt-\<Uživatelský\> server.
2. V ssms otevřete *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Upravte @User v horní části skriptu a nahradit `<User>` uživatelské jméno používané při nasazení Wingtip vstupenky SaaS víceklientské databázové aplikace. 
4. Stisknutím **klávesy F5** spusťte skript, který vytvoří a spustí úlohu, která extrahuje lístky a data zákazníků z každé databáze klienta. Úloha uloží data do úložiště analýzy.
5. Dotaz TicketsRawData tabulka v tenantanalytics databázi, aby zajistily, že tabulka je naplněna lístky informace ze všech klientů.

![ticketExtracts](media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Opakujte předchozí kroky, s výjimkou této doby nahradit **\ExtractTickets.sql** **\ExtractVenuesEvents.sql** v kroku 2.

Úspěšné spuštění úlohy naplní tabulka EventsRawData v úložišti analýzy novými událostmi a informacemi o místech od všech klientů. 

## <a name="data-reorganization"></a>Reorganizace dat

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Skartovat extrahovaná data k naplnění tabulek hvězdného schématu

Dalším krokem je skartace extrahovaných nezpracovaných dat do sady tabulek, které jsou optimalizovány pro analytické dotazy. Používá se hvězdné schéma. Centrální tabulka faktů obsahuje záznamy o prodeji jednotlivých vstupenek. Tabulky dimenzí jsou naplněny údaji o místech konání, událostech, zákaznících a datech nákupu. 

V této části kurzu definujete a spustíte úlohu, která slučuje extrahovaná nezpracovaná data s daty v tabulkách hvězdného schématu. Po dokončení úlohy sloučení jsou nezpracovaná data odstraněna a tabulky jsou připraveny k naplnění další úlohou extrakce dat klienta.

1. V SSMS se připojte k databázi **jobaccount** v catalog-mt-\<User\>.
2. V ssms, otevřít *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Stisknutím **klávesy F5** spusťte skript a definujte úlohu, která volá sp_ShredRawExtractedData uloženou proceduru v úložišti analýzy.
4. Ponechte dostatek času pro úspěšné spuštění úlohy.
    - Stav úlohy naleznete ve sloupci **Životní cyklus** jobs.jobs_execution tabulce. Ujistěte se, že **úloha byla úspěšná** před pokračováním. Úspěšné spuštění zobrazí data podobná následujícímu grafu:

![skartaceÚloha](media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Zkoumání dat

### <a name="visualize-tenant-data"></a>Vizualizace dat klienta

Data v tabulce hvězdného schématu poskytují všechna data o prodeji vstupenek potřebná pro analýzu. Chcete-li usnadnit zobrazení trendů ve velkých datových sadách, je třeba je vizualizovat graficky.  V této části se dozvíte, jak pomocí **Power BI** manipulovat a vizualizovat data tenanta, která jste extrahovali a uspořádali.

Pomocí následujících kroků se připojte k Power BI a importujte zobrazení, která jste vytvořili dříve:

1. Spusťte desktop Power BI.
2. Na pásu karet Domů vyberte **Získat data**a vyberte **Další...** z nabídky.
3. V okně **Získat data** vyberte Azure SQL Database.
4. Do přihlašovacího okna databáze zadejte název serveru\<(catalog-mt- User\>.database.windows.net). Vyberte **Importovat** **pro režim připojení k datům**a pak klepněte na OK. 

    ![powerBISignIn](media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. V levém podokně vyberte **Možnost Databáze,** zadejte uživatelské jméno = *vývojář*a zadejte heslo = *P\@ssword1*. Klikněte na **Připojit**.  

    ![DatabázeSignin](media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. V podokně **Navigátor** vyberte v databázi analýzy tabulky hvězdného schématu: fact_Tickets, dim_Events, dim_Venues, dim_Customers a dim_Dates. Pak vyberte **Načíst**. 

Blahopřejeme! Úspěšně jste načetli data do Power BI. Teď můžete začít prozkoumávat zajímavé vizualizace, které vám pomohou získat přehled o vašich tenantech. Dále si projdete, jak vám analytika umožní poskytnout doporučení založená na datech obchodnímu týmu Wingtip Tickets. Doporučení mohou pomoci optimalizovat obchodní model a zákaznickou zkušenost.

Začnete analýzou dat prodeje vstupenek, abyste viděli rozdíly v využití v různých místech. V yberte následující možnosti v Power BI a vykreslete pruhový graf celkového počtu vstupenek prodaných jednotlivými místy. Vzhledem k náhodným změnám v generátoru vstupenek se vaše výsledky mohou lišit.
 
![TotalTicketsByVenues](media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Předchozí parcela potvrzuje, že počet vstupenek prodaných jednotlivými místy se liší. Místa, která prodávají více vstupenek, využívají vaše služby více než místa, která prodávají méně vstupenek. Může zde být příležitost přizpůsobit přidělení prostředků podle různých potřeb klienta.

Můžete dále analyzovat data a zjistit, jak se prodej vstupenek v průběhu času liší. V Power BI vyberte následující možnosti, chcete-li vykreslit celkový počet vstupenek prodaných každý den po dobu 60 dnů.
 
![Datum saleversus](media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Předchozí graf zobrazuje, že prodej vstupenek špička pro některá místa. Tyto špičky posilují myšlenku, že některá místa mohou neúměrně spotřebovávat systémové prostředky. Zatím neexistuje žádný zřejmý vzor, když dojde k hrotům.

Dále chcete dále zkoumat význam těchto dnů špičkového prodeje. Kdy se tyto vrcholy vyskytují po vstupenkách jít do prodeje? Pokud chcete vYkreslit lístky prodané za den, vyberte v Power BI následující možnosti.

![SaleDayDistribution](media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Předchozí pozemek ukazuje, že některé místa prodávají spoustu vstupenek na první den prodeje. Jakmile vstupenky jdou do prodeje na těchto místech, zdá se, že šílený spěch. Tento nárůst aktivity několika místmůže ovlivnit službu pro ostatní klienty.

Můžete přejít do dat znovu, abyste zjistili, zda tento šílený spěch platí pro všechny události pořádané těmito místy. V předchozích pozemků, jste si všimli, že Contoso Koncertní síň prodává hodně vstupenek, a že Contoso má také špičku v prodeji vstupenek v určitých dnech. Pohrajte si s možnostmi Power BI a vykreslete kumulativní prodej vstupenek do koncertní síně Contoso se zaměřením na prodejní trendy pro každou z jejích událostí. Řídí se všechny události stejným vzorem prodeje?

![ContosoProdej](media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Předchozí děj pro Contoso Koncertní síň ukazuje, že šílený spěch se nestane pro všechny události. Pohrajte si s možnostmi filtru, abyste viděli trendy prodeje pro jiná místa.

Pohledy na vzorce prodeje vstupenek by mohly vést Wingtip Tickets k optimalizaci jejich obchodního modelu. Namísto účtování všech klientů stejně, možná Wingtip by měl zavést úrovně služeb s různými výpočetními velikostmi. Větší místa, která potřebují prodat více vstupenek za den, by mohla být nabídnuta vyšší úroveň se smlouvou o vyšší úrovni služeb (SLA). Tato místa by mohla mít své databáze umístěny do fondu s vyšší limity prostředků pro databázi. Každá úroveň služby může mít hodinové přidělení prodeje s dodatečnými poplatky účtovanými za překročení přidělení. Větší místa, která mají periodické výbuchy prodeje, by měla prospěch z vyšších úrovní a vstupenky Wingtip mohou efektivněji zpeněžit své služby.

Mezitím, někteří zákazníci Wingtip Vstupenky si stěžují, že se snaží prodat dostatek vstupenek ospravedlnit náklady na služby. Možná, že v těchto postřehů je možnost zvýšit prodej vstupenek v rámci plnění míst. Vyšší tržby by zvýšily vnímanou hodnotu služby. Klepněte pravým tlačítkem myši fact_Tickets a vyberte **možnost Nový rozměr**. Zadejte následující výraz pro nový rozměr s názvem **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Vyberte následující možnosti vizualizace a vykreslete procentuální lístky prodané jednotlivými místy, abyste zjistili jejich relativní úspěch.

![analyticsZobrazení](media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Předchozí pozemek ukazuje, že i když většina míst prodávat více než 80% svých vstupenek, někteří se snaží zaplnit více než polovinu míst. Pohrajte si s Hodnotami Dobře a vyberte maximální nebo minimální procento vstupenek prodaných pro každé místo.

Dříve jste prohloubili analýzu, abyste zjistili, že prodej vstupenek má tendenci sledovat předvídatelné vzory. Toto zjištění by mohlo nechat Wingtip Tickets pomoci nevýkonným místům zvýšit prodej vstupenek tím, že doporučí dynamické ceny. Tento objev by mohl odhalit příležitost využít techniky strojového učení k předvídání prodeje vstupenek pro každou událost. Předpovědi by mohly být také pro dopad na příjmy z nabídky slev na prodej vstupenek. Power BI Embedded může být integrován do aplikace pro správu událostí. Integrace by mohla pomoci vizualizovat předpokládané prodeje a účinek různých slev. Aplikace by mohla pomoci navrhnout optimální slevu, která bude použita přímo z analytického displeje.

Jste pozorovali trendy v datech klienta z Wingtip Tickets SaaS víceklientské databázové aplikace. Můžete uvažovat o dalších způsobech, jak může aplikace informovat obchodní rozhodnutí pro dodavatele aplikací SaaS. Prodejci mohou lépe uspokojit potřeby svých nájemníků. Doufejme, že vás tento kurz vybaví nástroji potřebnými k provádění analýz dat klientů, které umožní vašim firmám činit rozhodnutí založená na datech.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Nasazení databáze analýzy klienta s předdefinovanými tabulkami schématu hvězd
> - Použití elastických úloh k extrahování dat ze všech databází klienta
> - Sloučení extrahovaných dat do tabulek ve hvězdném schématu určeném pro analýzu
> - Dotaz na analytickou databázi 
> - Použití Power BI pro vizualizaci dat ke sledování trendů v datech tenanta 

Blahopřejeme!

## <a name="additional-resources"></a>Další zdroje

Další [výukové programy, které vycházejí z aplikace Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Elastické úlohy](elastic-jobs-overview.md).
- [Analýzy napříč tenanty pomocí extrahovaných dat – aplikace s jedním tenantem](saas-tenancy-tenant-analytics.md) 