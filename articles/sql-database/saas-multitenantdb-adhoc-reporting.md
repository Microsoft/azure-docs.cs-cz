---
title: Spouštění dotazů ad hoc vytváření sestav napříč několika databázemi Azure SQL | Dokumentace Microsoftu
description: Spouštění dotazů ad hoc vytváření sestav napříč několika databázemi SQL v příkladu aplikace s více tenanty.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: AyoOlubeko
ms.reviewer: sstein
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 5b1ceeac5d3887d5c199e642a0e26aa4ffab4452
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055426"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Spouštění dotazů ad hoc analýzy napříč několika databázemi Azure SQL

V tomto kurzu jste spouštění distribuovaných dotazů ve celá sada tenanta databázích tak, aby ad hoc vytváření interaktivních sestav. Tyto dotazy dokážou extrahovat statistiky ukryté v běžných provozních datech aplikace SaaS aplikace Wingtip Tickets. Provedete tyto extrakce nasadit další analytické databáze na server katalogu a použít nástroj Elastic Query umožňující distribuovaných dotazů.


V tomto kurzu se dozvíte:

> [!div class="checklist"]

> * Jak nasadit databázi ad hoc sestav
> * Postup spouštění distribuovaných dotazů ve všech databázích tenantů


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Databázi s více tenanty SaaS aplikace Wingtip Tickets aplikace je nasazená. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání aplikace Wingtip Tickets SaaS víceklientskou databází](saas-multitenantdb-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* SQL Server Management Studio (SSMS) je nainstalována. Stáhnout a nainstalovat aplikaci SSMS najdete v tématu [stáhnout SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Model vykazování ad hoc

![model vytváření sestav ad hoc](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Aplikace SaaS může analyzovat obrovské množství dat tenantů, která se ukládají centrálně v cloudu. Analýzy odhalit přehledy o provozu a využití vaší aplikace. Tyto přehledy můžete Průvodce vývoj funkcí, vylepšení použitelnosti a další investice do vašich aplikací a služeb.

V jedné databázi s více tenanty je přístup k těmto datům jednoduchý, ale třeba v případě distribuce mezi tisícovky databází se situace komplikuje. Jedním z přístupů je použít [Elastic Query](sql-database-elastic-query-overview.md), která umožňuje dotazování v distribuované sadě databází se společným schématem. Tyto databáze mohou být distribuovány v různých skupinách prostředků a předplatných. Ještě jedno společné přihlášení musí mít přístup k extrakci dat ze všech databází. Elastic Query používá jednu *head* databáze definovanými externími tabulkami, které zrcadlí tabulky a zobrazení v distribuovaných (tenantských) databázích. Dotazy odeslané do této hlavní databáze se kompilují a vzniká plán distribuovaného dotazu, který zajistí předávání částí dotazu potřebným tenantským databázím. Elastic Query používá k určení umístění všech databázích tenantů mapy horizontálních oddílů v databázi katalogu. Vytvoření i dotazování probíhá jednoduše pomocí standardní [příkazů jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)a podporují ad hoc dotazování z nástrojů, jako je Power BI a Excel.

Díky distribuci dotazy napříč databázemi tenantů, poskytuje Elastic Query okamžitý přehled o aktivní produkční data. Ale protože elastický dotaz načte data z potenciálně velkého počtu databází, latence dotazu může někdy být vyšší než pro ekvivalentní dotazy odeslané do jedné databázi s více tenanty. Nezapomeňte si návrhu dotazů minimalizovat objem dat, která je vrácena. Elastický dotaz je často nejvhodnější pro dotazování na malé množství dat v reálném čase, na rozdíl od vytváření často používaných nebo komplexních analytických dotazů nebo sestav. Pokud dotazy neprovádějte dobře, podívejte se na [plán provádění](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) zobrazíte, jaká část dotazu má prosadila do vzdálené databáze. A posoudit, kolik dat se vrací. Dotazy, které vyžadují komplexní analytické zpracování může být lepší obsluhovat ukládání dat extrahovaných tenanta do databáze, která je optimalizovaná pro analytické dotazy. SQL Database a SQL Data Warehouse může hostovat analytickou databázi.

Tento model pro analýzy je podrobně [kurz analýza tenanta](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Získat zdrojový kód aplikace Wingtip Tickets SaaS databázi s více tenanty a skripty

Jsou k dispozici v databázi s více tenanty SaaS aplikace Wingtip Tickets skripty a zdrojový kód aplikace [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skripty.

## <a name="create-ticket-sales-data"></a>Vytvoření lístku prodejních dat

Ke spouštění dotazů na zajímavější datové sady, vytvořte spuštěním generátoru lístek prodejní data lístku.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning Modules\\Operational Analytics\\vykazování ad hoc\\*Demo-AdhocReporting.ps1* skriptu a nastavte následující hodnoty:
   * **$DemoScenario** = 1, **nákup lístků na akce na všech místech**.
2. Stisknutím klávesy **F5** ke spuštění skriptu a zvyšovaly prodej lístků. Když je spuštěný skript, pokračujte kroky v tomto kurzu. Data lístku je dotazován v průběhu *spuštění ad hoc distribuované dotazy* části, proto počkejte generátor lístků na dokončení.

## <a name="explore-the-tenant-tables"></a>Prozkoumejte tabulky tenanta 

V aplikaci pro databázi s více tenanty SaaS aplikace Wingtip Tickets tenantů ukládají do správy modelu hybridního tenanta – kde data tenanta je buď uloženy v databázi s více tenanty nebo v databázi jednoho tenanta a lze přesunout mezi nimi. Při dotazování napříč všemi databázemi tenantů, je důležité, že elastický dotaz můžete zpracovávat data, pokud je součástí jedné logické databáze horizontálně dělené tenantem. 

K dosažení tohoto modelu patří všechny tabulky tenanta *VenueId* sloupec, který identifikuje, který tenant data patří. *VenueId* je vypočítán jako hodnotu hash názvem míst, ale žádné přístup může použít k zavedení jedinečnou hodnotu pro tento sloupec. Tento přístup je podobný stejným způsobem jako klíč tenanta je vypočítán pro použití v katalogu. Tabulky obsahující *VenueId* Elastic Query používá pro paralelní zpracování dotazů a vložit je do databáze odpovídající vzdáleného klienta. To výrazně snižuje množství dat, která je vrácena a výsledkem zvýšení výkonu, zvlášť pokud máte více tenantů, jejichž data se ukládají do databáze jednoho tenanta.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Nasazení databáze používané pro distribuované dotazy ad hoc

V tomto cvičení nasadíte *adhocreporting* databáze. Toto je hlavní databázi, která obsahuje schéma používané pro dotazování napříč všemi databázemi tenantů. Databáze je nasazený na existující server katalogu, který se používá pro všechny databáze v ukázkové aplikaci souvisejících se správou serveru.

1. Otevřete... \\Learning Modules\\Operational Analytics\\vykazování ad hoc\\*Demo-AdhocReporting.ps1* v *prostředí PowerShell ISE* a nastavit následující hodnoty:
   * **$DemoScenario** = 2, **nasazení Ad hoc analytics database**.

2. Stisknutím klávesy **F5** spusťte skript a vytvořit *adhocreporting* databáze.

V další části přidáte schéma do databáze, je možné ke spouštění distribuovaných dotazů.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Nakonfigurujte databázi "hlavní" pro spouštění distribuovaných dotazů

V tomto cvičení přidá schématu (externí zdroj dat a definice externí tabulky) pro databázi ad hoc sestav, který umožňuje dotazování napříč všemi databázemi tenantů.

1. Otevřete SQL Server Management Studio a připojte se k databázi vytváření sestav ad hoc, kterou jste vytvořili v předchozím kroku. Název databáze je *adhocreporting*.
2. Otevřete ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *inicializace AdhocReportingDB.sql* v aplikaci SSMS.
3. Zkontrolujte skript SQL a mějte na paměti následující:

   Elastic Query používá pro přístup k všech databázích tenantů přihlašovacích údajů s rozsahem databáze. Tyto přihlašovací údaje musí být k dispozici ve všech databázích a by měl obvykle být udělen minimální práva potřebná k povolení těchto dotazů ad hoc.

    ![vytvoření přihlašovacích údajů](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Pomocí databáze katalogu jako externí zdroj dat, jsou distribuované dotazy na všechny databáze zaregistrované v katalogu při spuštění dotazu. Protože se liší u každého nasazení názvy serverů, tento skript inicializace získá umístění databáze katalogu načtením aktuální server (@@servername) ve kterých se spouští skript.

    ![Vytvoření externího zdroje dat](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   S definovanými externími tabulkami, odkazující na tabulky tenanta **distribuce = SHARDED(VenueId)**. Ten přesměruje dotazu pro konkrétní *VenueId* k příslušné databázi a zvyšuje výkon pro řadu scénářů, jak je znázorněno v následující části.

    ![Vytvoření externích tabulek](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Místní tabulky *VenueTypes* , který se vytvoří a naplní. Tuto datovou tabulku odkaz je společné ve všech databázích tenantů, takže může být reprezentována jako místní tabulky a mají údaj vyplněný s common data. U některých dotazů, to může snížit objem dat přesouvat mezi databází tenantů a *adhocreporting* databáze.

    ![Vytvoření tabulky](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Zadáte-li referenční tabulky tímto způsobem, je potřeba aktualizovat schéma tabulky a data při každé aktualizaci databáze tenantů.

4. Stisknutím klávesy **F5** spusťte skript a inicializovat *adhocreporting* databáze. 

Můžete teď spouštění distribuovaných dotazů a shromažďování přehledů ve všech tenantech!

## <a name="run-ad-hoc-distributed-queries"></a>Spouštění distribuovaných dotazů ad hoc

Teď, když *adhocreporting* databáze je nastaven, pokračujte a spustit některé distribuovaných dotazů. Zahrnout plán provádění lépe pochopit, kde se děje zpracování dotazů. 

Při kontrole plánu spuštění, najeďte myší plán ikony podrobnosti. 

1. V *SSMS*, otevřete... \\Learning Modules\\Operational Analytics\\vykazování ad hoc\\*Demo-AdhocReportingQueries.sql*.
2. Zkontrolujte, že se připojení k **adhocreporting** databáze.
3. Vyberte **dotazu** nabídky a klikněte na tlačítko **patří skutečné Execution Plan**
4. Zvýrazněte *místa, které jsou aktuálně registrované?* dotazu a stiskněte klávesu **F5**.

   Dotaz vrátí seznam celý míst, ilustrující jak rychlé a snadné je dotazování všech tenantů a vrátit data z každého tenanta.

   Zkontrolujte plán a podívejte se, že veškeré náklady je vzdálený dotaz, protože jsme jednoduše přejde na každou databázi tenantů a výběrem informací míst.

   ![Vybrat * z dbo. Místa](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Vyberte další dotaz a stiskněte klávesu **F5**.

   Tento dotaz připojí data z databáze tenantů a místní *VenueTypes* tabulky (místní, protože je tabulka *adhocreporting* databáze).

   Kontrola plánu a zjistíte, že většina nákladů je vzdálený dotaz, protože jsme dotazují na informace každého tenanta míst (dbo. Místa), a poté proveďte rychlé místní síti pomocí místní *VenueTypes* tabulka, která se zobrazí popisný název.

   ![Připojte se k vzdálené a místní data](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Teď vyberte *den, kdy nejvíce lístky prodalo?* dotazu a stiskněte klávesu **F5**.

   Tento dotaz nepodporuje o něco složitější spojování a agregaci. Co je důležité si uvědomit je, že většinu zpracování je provést vzdáleně, a zase jsme vrácení pouze řádky, které potřebujeme, vrací pouze jeden řádek pro každé místo agregační lístek prodej počet za den.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Spouštění distribuovaných dotazů ve všech tenantských databázích
> * Nasadit databázi vytváření sestav ad hoc a přidejte do ní ke spouštění distribuovaných dotazů schématu.

Teď si vyzkoušejte [kurz analýza Tenanta](saas-multitenantdb-tenant-analytics.md) zkoumání extrahování dat do samostatné analytické databáze pro zpracování složitější analýzy.

## <a name="additional-resources"></a>Další zdroje informací:

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastic Query](sql-database-elastic-query-overview.md)
