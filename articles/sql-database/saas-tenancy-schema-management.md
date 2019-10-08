---
title: Správa schématu Azure SQL Database v aplikaci s jedním tenantům | Microsoft Docs
description: Správa schématu pro více tenantů v aplikaci s jedním tenantů, která používá Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/19/2018
ms.openlocfilehash: 95d13c997d3871815ebd541e5985eb9fef726a76
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029756"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Správa schématu v aplikaci SaaS pomocí modelu databáze na tenanta s Azure SQL Database
 
V rámci vývoje databázových aplikací je u schématu databáze nebo referenčních dat nevyhnutelná potřeba provést změny.  Úlohy údržby databáze se také pravidelně vyžadují. Správa aplikace, která používá databázi na model tenanta, vyžaduje, abyste tyto změny nebo úlohy údržby použili v rámci loďstva databází tenantů.

V tomto kurzu se seznámíte se dvěma scénáři – nasazení aktualizací referenčních dat pro všechny klienty a opětovné sestavení indexu v tabulce obsahující referenční data. Funkce [elastické úlohy](elastic-jobs-overview.md) se používá ke spouštění těchto akcí ve všech databázích tenanta a v databázi šablon použité k vytváření nových databází tenanta.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> 
> * Vytvoření agenta úloh
> * Spustí úlohy T-SQL, které se mají spustit na všech databázích tenanta.
> * Aktualizace referenčních dat ve všech databázích tenanta
> * Vytvoření indexu v tabulce ve všech databázích tenanta


Pro dokončení tohoto kurzu se ujistěte, že jsou splněné následující předpoklady:

* Aplikace Wingtip Tickets SaaS Database na tenanta je nasazená. Postup nasazení za méně než pět minut najdete v tématu [nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database na klienta](saas-dbpertenant-get-started-deploy.md) .
* Azure PowerShell je nainstalována. Podrobnosti najdete v tématu [Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) .
* Je nainstalovaná nejnovější verze SQL Server Management Studio (SSMS). [Stažení a instalace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> V tomto kurzu se používají funkce služby SQL Database, které jsou ve verzi omezené verze Preview (úlohy elastické databáze). Pokud chcete tento kurz provést, zadejte ID předplatného, abyste SaaSFeedback@microsoft.com s předmětem = elastické úlohy ve verzi Preview. Po obdržení potvrzení o povolení předplatného [si stáhněte a nainstalujte nejnovější rutiny pro úlohy předběžného vydání](https://github.com/jaredmoo/azure-powershell/releases). Tato verze Preview je omezená, takže pro související otázky nebo podporu se obraťte na SaaSFeedback@microsoft.com.

## <a name="introduction-to-saas-schema-management-patterns"></a>Seznámení se vzory správy schématu SaaS

Model databáze na tenanta izoluje data tenanta efektivně, ale zvyšuje počet databází, které se mají spravovat a udržovat. [Elastické úlohy](elastic-jobs-overview.md) usnadňují správu a správu databází SQL. Úlohy umožňují bezpečně a spolehlivě spouštět úlohy (skripty T-SQL) pro skupinu databází. Úlohy mohou nasadit schéma a běžné referenční datové změny napříč všemi databázemi klienta v aplikaci. Elastické úlohy je také možné použít k údržbě *šablony* , která se používá k vytváření nových tenantů, a zajišťuje tak, že má vždy k dispozici nejnovější schéma a referenční data.

![Obnovovací](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Elastické úlohy s omezeným náhledem

K dispozici je nová verze elastických úloh, která je teď integrovanou funkcí Azure SQL Database. Tato nová verze elastických úloh je momentálně ve verzi omezené verze Preview. Tato omezená verze Preview aktuálně podporuje použití PowerShellu k vytvoření agenta úloh a T-SQL k vytváření a správě úloh.

> [!NOTE]
> V tomto kurzu se používají funkce služby SQL Database, které jsou ve verzi omezené verze Preview (úlohy elastické databáze). Pokud chcete tento kurz provést, zadejte ID předplatného, abyste SaaSFeedback@microsoft.com s předmětem = elastické úlohy ve verzi Preview. Po obdržení potvrzení o povolení předplatného [si stáhněte a nainstalujte nejnovější rutiny pro úlohy předběžného vydání](https://github.com/jaredmoo/azure-powershell/releases). Tato verze Preview je omezená, takže pro související otázky nebo podporu se obraťte na SaaSFeedback@microsoft.com.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace Wingtip Tickets SaaS Database na klientské aplikace

Zdrojový kód aplikace a skripty pro správu jsou k dispozici v úložišti GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů Wingtip Tickets SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Vytvoření databáze agenta úloh a nového agenta úloh

Tento kurz vyžaduje, abyste pomocí PowerShellu vytvořili agenta úloh a jeho záložní databázi agenta úloh. Databáze agenta úloh obsahuje definice úloh, stav úlohy a historii. Po vytvoření agenta úlohy a jeho databáze můžete okamžitě vytvořit a monitorovat úlohy.

1. **V PowerShellu ISE**otevřete... \\Learning moduly @ No__t-2Schema Management @ no__t-3*demo-SchemaManagement. ps1*.
1. Stisknutím klávesy **F5** spusťte skript.

Skript *demo-SchemaManagement. ps1* volá skript *Deploy-SchemaManagement. ps1* , který vytvoří databázi SQL s názvem *osagent* na serveru katalogu. Pak vytvoří agenta úloh pomocí databáze jako parametru.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Vytvoření úlohy pro nasazení nových referenčních dat do všech tenantů

V aplikaci Wingtip Tickets obsahuje každá databáze tenanta sadu podporovaných typů míst. Každé místo má konkrétní typ místa, který definuje druh událostí, které mohou být hostovány, a určuje obrázek pozadí použitý v aplikaci. Aby aplikace podporovala nové druhy událostí, je nutné aktualizovat Tato referenční data a přidat nové typy míst.  V tomto cvičení nasadíte aktualizaci do všech databází tenantů, abyste přidali dva další typy míst: *motocykl* a svrchovaného *klubu*.

Nejprve zkontrolujte typy míst, které jsou součástí každé databáze tenanta. Připojte se k jedné z databází tenantů v SQL Server Management Studio (SSMS) a prozkoumejte tabulku VenueTypes.  Tuto tabulku můžete také dotazovat v editoru dotazů v Azure Portal, ke kterému se dostanete ze stránky databáze. 

1. Otevřete SSMS a připojte se k tenantovi serveru: *tenants1-DPT-@no__t -1User&gt;.database.windows.net*
1. Pokud si chcete ověřit, že **se** *motocykly na motocyklu* a v tuto chvíli nezahrnují, přejděte k databázi _contosoconcerthall_ na serveru *tenants1-DPT-&lt;User @ no__t-6* a Dotazujte na *VenueTypes* . stolní.

Teď vytvoříme úlohu, která aktualizuje tabulku *VenueTypes* ve všech databázích tenantů, aby se přidaly nové typy míst konání.

Chcete-li vytvořit novou úlohu, použijte sadu systémových uložených procedur úloh vytvořených v databázi _Služba jobagent_ při vytvoření agenta úlohy.

1. V SSMS se připojte k serveru katalogu: *Catalog-DPT-@no__t server -1User&gt;.database.windows.net* 
1. V SSMS otevřete soubor... \\Learning moduly @ no__t-1Schema Management\\DeployReferenceData.sql
1. Upravte příkaz: SET @wtpUser = &lt;User @ no__t-2 a nahraďte hodnotu uživatele, která se používá při nasazování aplikace SaaS Database pro společnost Wingtip Tickets na klienta.
1. Ujistěte se, že jste připojení k databázi _Služba jobagent_ , a stisknutím klávesy **F5** spusťte skript.

Ve skriptu *DeployReferenceData. SQL* Sledujte následující prvky:
* **SP @ no__t-1add @ no__t-2target @ no__t-3group** vytvoří název cílové skupiny DemoServerGroup.
* **SP @ no__t-1add @ no__t-2target @ no__t-3group @ no__t-4member** slouží k definování sady cílových databází.  Nejprve se přidá server _tenants1-DPT-&lt;User @ no__t-2_ .  Přidání serveru jako cíle způsobí, že databáze na tomto serveru v době spuštění úlohy budou zahrnuty do úlohy. Pak se databáze _basetenantdb_ a databáze *adhocreporting* (použité v pozdějším kurzu) přidají jako cíle.
* **SP @ no__t-1add @ no__t-2job** vytvoří úlohu s názvem _nasazení referenčních dat_.
* **SP @ no__t-1add @ no__t-2jobstep** vytvoří krok úlohy obsahující text příkazu t-SQL, který aktualizuje referenční tabulku, VenueTypes.
* Zbývající zobrazení ve skriptu zobrazují existenci objektů a monitorují provádění úlohy. Pomocí těchto dotazů můžete zkontrolovat hodnotu stavu ve sloupci **životní cyklus** a zjistit, kdy se úloha dokončila ve všech cílových databázích.

Po dokončení skriptu můžete ověřit, zda byla referenční data aktualizována.  V SSMS přejděte k databázi *contosoconcerthall* na serveru *tenants1-DPT-&lt;user @ no__t-3* a Dotazujte tabulku *VenueTypes* .  Ověřte, že **je teď k** dispozici *motocykl* a *plavecký klub* .


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Vytvoření úlohy pro správu indexu referenční tabulky

Tento cvičení používá úlohu k opětovnému sestavení indexu v primárním klíči referenční tabulky.  Toto je typická operace údržby databáze, kterou je možné provést po načtení velkých objemů dat.

Vytvořte úlohu pomocí uložených procedur "System" v úlohách.

1. Otevřete SSMS a připojte se ke _katalogu – DPT-@no__t server -1User&gt;.database.windows.net_ .
1. Otevřete soubor _... \\Learning moduly @ no__t-2Schema Management\\OnlineReindex.sql_
1. Klikněte pravým tlačítkem myši, vyberte připojení a připojte se ke _katalogu – DPT-@no__t -1User&gt;.database.windows.net_ Server, pokud ještě není připojený.
1. Ujistěte se, že jste připojení k databázi _Služba jobagent_ , a stisknutím klávesy **F5** spusťte skript.

Ve skriptu _OnlineReindex. SQL_ Sledujte následující prvky:
* **SP @ no__t-1add @ no__t-2job** vytvoří novou úlohu s názvem "online Reindexovat PK @ no__t-3 @ No__t-4VenueTyp @ no__t-5 @ NO__T-6265E44FD7FD4C885"
* **SP @ no__t-1add @ no__t-2jobstep** vytvoří krok úlohy obsahující text příkazu t-SQL, který aktualizuje index.
* Zbývající zobrazení ve spuštění úlohy monitoru skriptu. Pomocí těchto dotazů můžete zkontrolovat hodnotu stavu ve sloupci **životní cyklus** a zjistit, kdy byla úloha úspěšně dokončena u všech členů cílové skupiny.



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak:

> [!div class="checklist"]
> 
> * Vytvoření agenta úlohy pro spuštění napříč úlohami T-SQL s více databázemi
> * Aktualizace referenčních dat ve všech databázích tenanta
> * Vytvoření indexu v tabulce ve všech databázích tenanta

Potom si v [kurzu vytváření sestav ad hoc](saas-tenancy-cross-tenant-reporting.md) Prozkoumejte spouštění distribuovaných dotazů napříč databázemi klientů.


## <a name="additional-resources"></a>Další zdroje

* [Další kurzy, které se sestavují na základě SaaS databáze Wingtip Tickets pro každé klientské nasazení aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Správa cloudových databází s horizontálním škálováním kapacity](elastic-jobs-overview.md)