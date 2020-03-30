---
title: Správa schématu v aplikaci s jedním klientem
description: Správa schématu pro více klientů v aplikaci s jedním klientem, která používá Azure SQL Database
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
ms.openlocfilehash: b6802d97b964b8863f6c2fce0cebfe16782b46fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269208"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Správa schématu v aplikaci SaaS pomocí vzoru databáze na klienta pomocí Azure SQL Database
 
Jak se vyvíjí databázová aplikace, je nevyhnutelně nutné provést změny ve schématu databáze nebo referenčních datech.  Úlohy údržby databáze jsou také potřeba pravidelně. Správa aplikace, která používá databázi na vzor klienta vyžaduje, abyste tyto změny nebo úlohy údržby v rámci vozového parku databází klientů.

Tento kurz zkoumá dva scénáře – nasazení aktualizací referenčních dat pro všechny klienty a opětovné sestavení indexu v tabulce obsahující referenční data. Funkce [Elastické úlohy](elastic-jobs-overview.md) se používá k provádění těchto akcí ve všech databázích klientů a v databázi šablon, která se používá k vytvoření nových databází klientů.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> 
> * Vytvoření agenta úlohy
> * Způsobit spuštění úloh T-SQL ve všech databázích klientů
> * Aktualizace referenčních dat ve všech databázích klientů
> * Vytvořit index tabulky ve všech databázích tenantů


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Wingtip Vstupenky SaaS databáze na klienta aplikace se nasadí. Pokud chcete nasadit za méně než pět minut, [přečtěte si informace o nasazení a prozkoumání databáze Wingtip Tickets SaaS na klienta.](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Je nainstalovaná nejnovější verze SQL Server Management Studia (SSMS). [Stažení a instalace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Tento kurz používá funkce služby SQL Database, které fungují ve verzi Limited Preview (úlohy služby Elastic Database). Pokud chcete provést tento kurz, zadejte SaaSFeedback@microsoft.com id předplatného s subject=Elastic Jobs Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). Tato předběžná verze SaaSFeedback@microsoft.com je omezená, proto se obraťte na související dotazy nebo podporu.

## <a name="introduction-to-saas-schema-management-patterns"></a>Úvod do vzorců správy schématu SaaS

Databáze na vzorek klienta izoluje data klienta efektivně, ale zvyšuje počet databází pro správu a údržbu. [Elastické úlohy](elastic-jobs-overview.md) usnadňují správu a správu databází SQL. Úlohy umožňují bezpečně a spolehlivě spouštět úlohy (skripty T-SQL) proti skupině databází. Úlohy můžete nasadit schéma a běžné změny referenčních dat ve všech databázích klienta v aplikaci. Elastické úlohy lze také použít k údržbě databáze *šablony* slouží k vytvoření nových klientů, zajištění, že má vždy nejnovější schématu a referenční data.

![obrazovka](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Elastic Jobs verze Limited Preview

Je tu nová verze elastické úlohy, která je teď integrovanou funkcí Azure SQL Database. Tato nová verze služby Elastic Jobs je v současnosti ve verzi Limited Preview. Tato omezená verze preview aktuálně podporuje použití prostředí PowerShell k vytvoření agenta úlohy a T-SQL k vytváření a správě úloh.

> [!NOTE]
> Tento kurz používá funkce služby SQL Database, které fungují ve verzi Limited Preview (úlohy služby Elastic Database). Pokud chcete provést tento kurz, zadejte SaaSFeedback@microsoft.com id předplatného s subject=Elastic Jobs Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). Tato předběžná verze SaaSFeedback@microsoft.com je omezená, proto se obraťte na související dotazy nebo podporu.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání databáze Wingtip Tickets SaaS pro jednotlivé skripty aplikací klienta

Zdrojový kód aplikace a skripty pro správu jsou k dispozici v úložišti [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování wingtip vstupenky SaaS skripty.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Vytvoření databáze agenta úlohy a nového agenta úlohy

Tento kurz vyžaduje, abyste pomocí prostředí PowerShell vytvořili agenta úloh a jeho záložní databázi agenta úloh. Databáze agenta úlohobsahuje definice úloh, stav úlohy a historii. Po vytvoření agenta úlohy a jeho databáze můžete okamžitě vytvořit a sledovat úlohy.

1. **V prostředí PowerShell ISE**otevřete ... \\Výukové moduly\\Správa\\*schématu Demo-SchemaManagement.ps1*.
1. Stisknutím klávesy **F5** spusťte skript.

Skript *Demo-SchemaManagement.ps1* volá skript *Deploy-SchemaManagement.ps1* a vytvoří databázi SQL s názvem *osagent* na serveru katalogu. Potom vytvoří agenta úlohy pomocí databáze jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Vytvoření úlohy, která u všech tenantů nasadí nová referenční data

V aplikaci Wingtip Tickets obsahuje každá databáze klienta sadu podporovaných typů míst. Každé místo konání je určitého typu místa konání, které definuje druh událostí, které mohou být hostovány, a určuje obrázek pozadí použitý v aplikaci. Aby aplikace podporovala nové druhy událostí, musí být tato referenční data aktualizována a přidány nové typy míst.  V tomto příkladu nasadíte aktualizaci u všech databází tenantů. Aktualizace přidá dva další typy míst: *Motorcycle Racing* (Motocyklové závody) a *Swimming Club* (Plavecký klub).

Nejprve zkontrolujte typy míst zahrnuté v každé databázi klienta. Připojte se k jedné z databází klienta v SQL Server Management Studio (SSMS) a zkontrolujte VenueTypes tabulka.  Tuto tabulku můžete také zadat v editoru dotazů na webu Azure Portal, ke které se přistupuje ze stránky databáze. 

1. Otevřete SSMS a připojte se k serveru klienta: *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Chcete-li ověřit, že *motocyklové závody* a *plavecký klub* **nejsou** aktuálně zahrnuty, přejděte do databáze _contosoconcerthall_ na *serveru klienty1-dpt-&lt;user&gt; * a dotaz na *venuetypes* tabulka.

Nyní vytvoříme úlohu pro aktualizaci tabulky *VenueTypes* ve všech databázích tenanta a přidejte nové typy míst.

Chcete-li vytvořit novou úlohu, použijte sadu úloh systému uložené procedury vytvořené v databázi _pracovních míst_ při vytvoření agenta úlohy.

1. V SSMS se připojte k serveru katalogu: *catalog-dpt-&lt;&gt;user .database.windows.net* server 
1. V SSMS otevřete soubor ... \\Správa schématu schématu\\\\učení DeployReferenceData.sql
1. Změnit příkaz: @wtpUser SET &lt;&gt; = uživatel a nahradit uživatelskou hodnotu použitou při nasazení wingtip tickets Saadatabáze databáze na klienta aplikace
1. Ujistěte se, že jste připojeni k databázi _pracovních míst_ a stisknutím **klávesy F5** spustíte skript

Ve skriptu *DeployReferenceData.sql* dodržujte následující prvky:
* **sp\_\_add\_cílová skupina** vytvoří název cílové skupiny DemoServerGroup.
* **sp\_\_přidat\_\_člen cílové skupiny** se používá k definování sady cílových databází.  Nejprve _&lt;tenants1-dpt-&gt; uživatelský_ server je přidán.  Přidání serveru jako cíle způsobí, že databáze v tomto serveru v době spuštění úlohy budou zahrnuty do úlohy. Potom _basetenantdb_ databáze a databáze *adhocreporting* (používá se v pozdější kurz) jsou přidány jako cíle.
* **sp\_\_add úloha** vytvoří úlohu s názvem _Nasazení referenčních dat_.
* **sp\_\_add jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL pro aktualizaci referenční tabulky VenueTypes.
* Zbývající pohledy ve skriptu zobrazují existující objekty a monitorují provádění úlohy. Pomocí těchto dotazů zkontrolujte hodnotu stavu ve **sloupci životního cyklu** a určete, kdy byla úloha dokončena ve všech cílových databázích.

Po dokončení skriptu můžete ověřit, zda byla referenční data aktualizována.  V SSMS přejděte do databáze *contosoconcerthall* na *serveru&lt;klienty1-dpt- user&gt; * a dotaz na *VenueTypes* tabulka.  Zkontrolujte, zda *motocyklové závody* a *plavecký klub* **jsou** nyní přítomny.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Vytvoření úlohy pro správu indexu referenční tabulky

Toto cvičení používá úlohu k opětovnému sestavení indexu v primárním klíči referenční tabulky.  Jedná se o typickou operaci údržby databáze, která může být provedena po načtení velkého množství dat.

K vytvoření úlohy použijeme stejné úlohy uložených procedur „system“.

1. Otevřete SSMS a připojte se k _serveru .database.windows.net uživatele&lt;&gt;katalogu-dpt_
1. Otevřete soubor _... Výukové moduly\\Schema Management\\OnlineReindex.sql \\_
1. Klikněte pravým tlačítkem myši, vyberte možnost Připojení a připojte se k serveru _.database.windows.net&lt;&gt;uživatele katalogu,_ pokud již není připojen.
1. Ujistěte se, že jste připojeni k databázi _pracovních míst_ a stisknutím **klávesy F5** spustíte skript

Ve skriptu _OnlineReindex.sql_ dodržujte následující prvky:
* **sp\_\_přidat úlohu** vytvoří novou úlohu\_\_s\_názvem "Online Reindex PK VenueTyp\_265E44FD7FD4C885"
* **sp\_\_přidat jobstep** vytvoří krok úlohy obsahující T-SQL příkaz text aktualizovat index
* Zbývající zobrazení ve spuštění úlohy sledování skriptu. Pomocí těchto dotazů zkontrolujte hodnotu stavu ve **sloupci životního cyklu** a určete, kdy byla úloha úspěšně dokončena u všech členů cílové skupiny.



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> 
> * Vytvoření agenta úlohpro spuštění více databází t-SQL
> * Aktualizace referenčních dat ve všech databázích klientů
> * Vytvořit index tabulky ve všech databázích tenantů

Dále zkuste [kurz ad hoc vykazování](saas-tenancy-cross-tenant-reporting.md) prozkoumat spuštěné distribuované dotazy napříč databázemi klientů.


## <a name="additional-resources"></a>Další zdroje

* [Další kurzy, které vycházejí z nasazení aplikace Wingtip Tickets SaaS Database SaaS na klienta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Správa cloudových databází s horizontálním navýšením kapacity](elastic-jobs-overview.md)