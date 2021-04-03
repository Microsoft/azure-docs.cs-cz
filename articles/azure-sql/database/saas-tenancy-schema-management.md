---
title: Správa schématu v aplikaci s jedním tenantů
description: Správa schématu pro více tenantů v aplikaci s jedním tenantů, která používá Azure SQL Database
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
ms.openlocfilehash: e4328be0aade0658dedb034dbbb6980b810f771a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793190"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Správa schématu v aplikaci SaaS pomocí modelu databáze na tenanta s Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
V rámci vývoje databázových aplikací je u schématu databáze nebo referenčních dat nevyhnutelná potřeba provést změny.  Úlohy údržby databáze se také pravidelně vyžadují. Správa aplikace, která používá databázi na model tenanta, vyžaduje, abyste tyto změny nebo úlohy údržby použili v rámci loďstva databází tenantů.

V tomto kurzu se seznámíte se dvěma scénáři – nasazení aktualizací referenčních dat pro všechny klienty a opětovné sestavení indexu v tabulce obsahující referenční data. Funkce [elastické úlohy](./elastic-jobs-overview.md) se používá ke spouštění těchto akcí ve všech databázích tenanta a v databázi šablon použité k vytváření nových databází tenanta.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> 
> * Vytvoření agenta úloh
> * Spustí úlohy T-SQL, které se mají spustit na všech databázích tenanta.
> * Aktualizace referenčních dat ve všech databázích tenanta
> * Vytvořit index tabulky ve všech databázích tenantů


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Aplikace Wingtip Tickets SaaS Database na tenanta je nasazená. Postup nasazení za méně než pět minut najdete v tématu [nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database na klienta](./saas-dbpertenant-get-started-deploy.md) .
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](/powershell/azure/get-started-azureps).
* Je nainstalovaná nejnovější verze SQL Server Management Studia (SSMS). [Stažení a instalace SSMS](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="introduction-to-saas-schema-management-patterns"></a>Seznámení se vzory správy schématu SaaS

Model databáze na tenanta izoluje data tenanta efektivně, ale zvyšuje počet databází, které se mají spravovat a udržovat. [Elastické úlohy](./elastic-jobs-overview.md) usnadňují správu a správu více databází. Úlohy umožňují bezpečně a spolehlivě spouštět úlohy (skripty T-SQL) pro skupinu databází. Úlohy mohou nasadit schéma a běžné referenční datové změny napříč všemi databázemi klienta v aplikaci. Elastické úlohy je také možné použít k údržbě *šablony* , která se používá k vytváření nových tenantů, a zajišťuje tak, že má vždy k dispozici nejnovější schéma a referenční data.

![obrazovka](./media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-public-preview"></a>Elastické úlohy – verze Public Preview

K dispozici je nová verze elastických úloh, která je teď integrovanou funkcí Azure SQL Database. Tato nová verze elastických úloh je momentálně ve verzi Public Preview. Tato verze Public Preview aktuálně podporuje použití PowerShellu k vytvoření agenta úloh a T-SQL k vytváření a správě úloh.
Další informace najdete v článku o [úlohách elastic Database](./elastic-jobs-overview.md) .

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace Wingtip Tickets SaaS Database na klientské aplikace

Zdrojový kód aplikace a skripty pro správu jsou k dispozici v úložišti GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů Wingtip Tickets SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Vytvoření databáze agenta úloh a nového agenta úloh

Tento kurz vyžaduje, abyste pomocí PowerShellu vytvořili agenta úloh a jeho záložní databázi agenta úloh. Databáze agenta úloh obsahuje definice úloh, stav úlohy a historii. Po vytvoření agenta úlohy a jeho databáze můžete okamžitě vytvořit a monitorovat úlohy.

1. **V prostředí POWERSHELL ISE** otevřete... \\Demo-SchemaManagement.ps1Správa schématu ve výukových modulech \\ \\ **.
1. Stisknutím klávesy **F5** spusťte skript.

Skript *Demo-SchemaManagement.ps1* volá skript *Deploy-SchemaManagement.ps1* , aby vytvořil databázi s názvem *osagent* na serveru katalogu. Pak vytvoří agenta úloh pomocí databáze jako parametru.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Vytvoření úlohy, která u všech tenantů nasadí nová referenční data

V aplikaci Wingtip Tickets obsahuje každá databáze tenanta sadu podporovaných typů míst. Každé místo má konkrétní typ místa, který definuje druh událostí, které mohou být hostovány, a určuje obrázek pozadí použitý v aplikaci. Aby aplikace podporovala nové druhy událostí, je nutné aktualizovat Tato referenční data a přidat nové typy míst.  V tomto příkladu nasadíte aktualizaci u všech databází tenantů. Aktualizace přidá dva další typy míst: *Motorcycle Racing* (Motocyklové závody) a *Swimming Club* (Plavecký klub).

Nejprve zkontrolujte typy míst, které jsou součástí každé databáze tenanta. Připojte se k jedné z databází tenantů v SQL Server Management Studio (SSMS) a prozkoumejte tabulku VenueTypes.  Tuto tabulku můžete také dotazovat v editoru dotazů v Azure Portal, ke kterému se dostanete ze stránky databáze. 

1. Otevřete SSMS a připojte se k tenantovi serveru: *tenants1-DPT- &lt; user &gt; . Database.Windows.NET*
1. Pokud si chcete ověřit, že  **se** *motocykly na motocyklu* a v tuto chvíli nezahrnují, přejděte k databázi _contosoconcerthall_ na serveru *tenants1-DPT- &lt; User &gt;* a Dotazujte se na tabulku *VenueTypes* .

Teď vytvoříme úlohu, která aktualizuje tabulku *VenueTypes* ve všech databázích tenantů, aby se přidaly nové typy míst konání.

Chcete-li vytvořit novou úlohu, použijte sadu systémových uložených procedur úloh vytvořených v databázi _Služba jobagent_ při vytvoření agenta úlohy.

1. V SSMS se připojte k serveru katalogu: *Catalog-DPT- &lt; user &gt; . Database.Windows.NET* Server 
1. V SSMS otevřete soubor... \\ Výukové moduly \\ schéma Správa schématu \\ DeployReferenceData. SQL
1. Upravte příkaz: SET @wtpUser = &lt; User &gt; a nahraďte hodnotu uživatele, která se používá při nasazení aplikace Wingtip Tickets SaaS Database na klienta.
1. Ujistěte se, že jste připojení k databázi _Služba jobagent_ , a stisknutím klávesy **F5** spusťte skript.

Ve skriptu *DeployReferenceData. SQL* Sledujte následující prvky:
* **SP \_ Přidat \_ cílovou \_ skupinu** vytvoří název cílové skupiny DemoServerGroup.
* v části **\_ Přidat \_ \_ \_ člena cílové skupiny** se používá k definování sady cílových databází.  Nejprve se přidá server _tenants1-DPT- &lt; User &gt;_ .  Přidání serveru jako cíle způsobí, že databáze na tomto serveru v době spuštění úlohy budou zahrnuty do úlohy. Pak se databáze _basetenantdb_ a databáze *adhocreporting* (použité v pozdějším kurzu) přidají jako cíle.
* **SP \_ Add \_ Job** vytvoří úlohu s názvem _nasazení referenčních dat_.
* **SP \_ Add \_ jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL, který aktualizuje referenční tabulku VenueTypes.
* Zbývající pohledy ve skriptu zobrazují existující objekty a monitorují provádění úlohy. Pomocí těchto dotazů můžete zkontrolovat hodnotu stavu ve sloupci **životní cyklus** a zjistit, kdy se úloha dokončila ve všech cílových databázích.

Po dokončení skriptu můžete ověřit, zda byla referenční data aktualizována.  V SSMS přejděte k databázi *contosoconcerthall* na serveru *tenants1-DPT- &lt; &gt; User* a Dotazujte tabulku *VenueTypes* .  Ověřte, že **je teď k** dispozici *motocykl* a *plavecký klub* .


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Vytvoření úlohy pro správu indexu referenční tabulky

Tento cvičení používá úlohu k opětovnému sestavení indexu v primárním klíči referenční tabulky.  Toto je typická operace údržby databáze, kterou je možné provést po načtení velkých objemů dat.

K vytvoření úlohy použijeme stejné úlohy uložených procedur „system“.

1. Otevřete SSMS a připojte se ke _katalogu – DPT- &lt; user &gt; . Database.Windows.NET_ Server
1. Otevřít soubor _... \\ Výukové moduly \\ schéma Správa schématu \\ OnlineReindex. SQL_
1. Klikněte pravým tlačítkem myši, vyberte připojení a připojte se ke _katalogu – DPT- &lt; user &gt; . Database.Windows.NET_ Server (Pokud ještě není připojený).
1. Ujistěte se, že jste připojení k databázi _Služba jobagent_ , a stisknutím klávesy **F5** spusťte skript.

Ve skriptu _OnlineReindex. SQL_ Sledujte následující prvky:
* **SP \_ Add \_ Job** vytvoří novou úlohu s názvem "online reindex PK \_ \_ VenueTyp \_ \_ 265E44FD7FD4C885".
* **SP \_ Add \_ jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL, který aktualizuje index.
* Zbývající zobrazení ve spuštění úlohy monitoru skriptu. Pomocí těchto dotazů můžete zkontrolovat hodnotu stavu ve sloupci **životní cyklus** a zjistit, kdy byla úloha úspěšně dokončena u všech členů cílové skupiny.



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> 
> * Vytvoření agenta úlohy pro spuštění napříč úlohami T-SQL s více databázemi
> * Aktualizace referenčních dat ve všech databázích tenanta
> * Vytvořit index tabulky ve všech databázích tenantů

Potom si v [kurzu vytváření sestav ad hoc](./saas-tenancy-cross-tenant-reporting.md) Prozkoumejte spouštění distribuovaných dotazů napříč databázemi klientů.


## <a name="additional-resources"></a>Další zdroje informací

* [Další kurzy, které se sestavují na základě SaaS databáze Wingtip Tickets pro každé klientské nasazení aplikace](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Správa cloudových databází s horizontálním navýšením kapacity](./elastic-jobs-overview.md)