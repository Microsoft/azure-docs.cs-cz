---
title: Správa schématu v aplikaci s více klienty
description: Správa schématu pro více tenantů v aplikaci s více tenanty využívající službu Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6f660426c41b37dd27438c28cbf603bdbf1e58b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269195"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Správa schématu v aplikaci SaaS, která používá vrstvené víceklientské databáze SQL

Tento kurz zkoumá problémy při udržování flotily databází v aplikaci Software jako služba (SaaS). Řešení jsou demonstrována pro rozdmýchávání změn schématu napříč vozovým parkem databází.

Stejně jako každá aplikace, aplikace Wingtip Tickets SaaS se bude v průběhu času vyvíjet a bude vyžadovat změny v databázi. Změny mohou mít vliv na schéma nebo referenční data nebo použít úlohy údržby databáze. S aplikací SaaS pomocí databáze na vzorek klienta, změny musí být koordinovány v rámci potenciálně masivní flotilu databází klientů. Kromě toho je nutné začlenit tyto změny do procesu zřizování databáze, abyste zajistili, že jsou zahrnuty do nových databází při jejich vytváření.

#### <a name="two-scenarios"></a>Dva scénáře

Tento kurz zkoumá následující dva scénáře:
- Nasaďte aktualizace referenčních dat pro všechny klienty.
- Znovu sestavte index v tabulce, která obsahuje referenční data.

Funkce [Elastické úlohy](elastic-jobs-overview.md) azure SQL database se používá k provádění těchto operací napříč databázemi klientů. Úlohy také pracovat na databázi klienta šablony. V ukázkové aplikaci Wingtip Tickets se tato databáze šablon zkopíruje do zřízení nové databáze klienta.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvořte agenta úlohy.
> * Spusťte dotaz T-SQL ve více databázích klientů.
> * Aktualizujte referenční data ve všech databázích klienta.
> * Vytvořte index v tabulce ve všech databázích klienta.

## <a name="prerequisites"></a>Požadavky

- Víceklientská databázová aplikace Wingtip Tickets musí být již nasazena:
    - Pokyny naleznete v prvním kurzu, který představuje wingtip vstupenky SaaS víceklientské databázové aplikace:<br />[Nasazujte a prozkoumejte rozdělenou víceklientská aplikace, která používá Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - Proces nasazení běží méně než pět minut.
    - Musíte mít nainstalována *víceklientská* verze wingtipu. Verze pro *samostatné* a *databáze na klienta* nepodporují tento kurz.

- Musí být nainstalována nejnovější verze aplikace SQL Server Management Studio (SSMS). [Stáhnout a nainstalovat SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell musí být nainstalovaný. Podrobnosti najdete [v tématu Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Tento kurz používá funkce služby Azure SQL Database, které jsou v omezeném náhledu[(úlohy elastické databáze).](sql-database-elastic-database-client-library.md) Pokud chcete provést tento kurz, zadejte ID předplatného *sSFeedback\@microsoft.com* s subject=Elastic Jobs Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). Tato verze preview je omezená, proto se obraťte na *\@saaSFeedback microsoft.com* pro související dotazy nebo podporu.

## <a name="introduction-to-saas-schema-management-patterns"></a>Úvod do vzorců správy schématu SaaS

Oborový model databáze s více klienty použitý v této ukázce umožňuje databázi klientů obsahovat jednoho nebo více klientů. Tato ukázka zkoumá potenciál použít kombinaci databází ntenant a jeden tenant, povolení modelu správy *hybridního* klienta. Správa změn v těchto databázích může být komplikovaná. [Elastic Jobs](elastic-jobs-overview.md) usnadňuje správu a správu velkého počtu databází. Úlohy umožňují bezpečně a spolehlivě spouštět skripty Transact-SQL jako úlohy proti skupině databází klientů. Úkoly jsou nezávislé na interakci uživatele nebo vstupu. Tuto metodu lze použít k nasazení změny schématu nebo společných referenčních dat, napříč všemi klienty v aplikaci. Elastické úlohy lze také udržovat zlatou šablonu kopii databáze. Šablona se používá k vytvoření nových klientů, vždy zajistit nejnovější schéma a referenční data jsou používány.

![obrazovka](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastic Jobs verze Limited Preview

K dispozici je nová verze elastické úlohy, která je nyní integrovanou funkcí Azure SQL Database. Tato nová verze služby Elastic Jobs je v současnosti ve verzi Limited Preview. Omezená verze preview aktuálně podporuje použití prostředí PowerShell k vytvoření agenta úlohy a T-SQL k vytváření a správě úloh.
> [!NOTE]
> Tento kurz používá funkce služby SQL Database, které fungují ve verzi Limited Preview (úlohy služby Elastic Database). Pokud chcete provést tento kurz, zadejte SaaSFeedback@microsoft.com id předplatného s subject=Elastic Jobs Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh. Tato předběžná verze SaaSFeedback@microsoft.com je omezená, proto se obraťte na související dotazy nebo podporu.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Získejte Wingtip Tickets SaaS Víceklientský databázový kód aplikace a skripty

Wingtip Tickets SaaS Multi-tenant databázové skripty a zdrojový kód aplikace jsou k dispozici v úložišti [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) na GitHubu. Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování wingtip vstupenky SaaS skripty.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Vytvoření databáze agenta úlohy a nového agenta úlohy

Tento kurz vyžaduje, abyste pomocí prostředí PowerShell vytvořili databázi a agenta úloh. Podobně jako databáze MSDB používaná agentem SQL používá agent úlohy databázi Azure SQL k ukládání definic úloh, stavu úlohy a historie. Po vytvoření agenta úlohy můžete okamžitě vytvořit a sledovat úlohy.

1. V **prostředí PowerShell ISE**otevřete *... Výukové moduly\\Správa schématu\\Demo-SchemaManagement.ps1 . \\*
2. Stisknutím klávesy **F5** spusťte skript.

Skript *Demo-SchemaManagement.ps1* volá skript *Deploy-SchemaManagement.ps1* a vytvoří databázi s názvem _jobagent_ na serveru katalogu. Skript pak vytvoří agenta úlohy, předávání databáze _pracovních míst_ jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Vytvoření úlohy, která u všech tenantů nasadí nová referenční data

#### <a name="prepare"></a>Příprava

Databáze každého klienta obsahuje sadu typů míst v tabulce **VenueTypes.** Každý typ místa definuje druh událostí, které mohou být hostovány na místě. Tyto typy míst odpovídají obrázky na pozadí, které vidíte v aplikaci události klienta.  V tomto cvičení nasadíte aktualizaci do všech databází a přidáte dva další typy míst konání: *Motocyklové závody* a *Plavecký klub*.

Nejprve zkontrolujte typy míst zahrnuté v každé databázi klienta. Připojte se k jedné z databází klienta v SQL Server Management Studio (SSMS) a zkontrolujte VenueTypes tabulka.  Tuto tabulku můžete také zadat v editoru dotazů na webu Azure Portal, ke které se přistupuje ze stránky databáze.

1. Otevřete SSMS a připojte se k serveru klienta: *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Chcete-li ověřit, že *motocyklové závody* a *plavecký klub* **nejsou** aktuálně zahrnuty, přejděte do databáze *contosoconcerthall* na *serveru klienty1-dpt-&lt;user&gt; * a dotaz na *venuetypes* tabulka.



#### <a name="steps"></a>Kroky

Nyní vytvoříte úlohu pro aktualizaci tabulky **VenueTypes** v každé databázi klientů přidáním dvou nových typů míst.

Chcete-li vytvořit novou úlohu, použijte sadu úloh systému uložené procedury, které byly vytvořeny v databázi _jobagent._ Uložené procedury byly vytvořeny při vytvoření agenta úlohy.

1. V SSMS se připojte k serveru klienta:&lt;tenants1-mt- user&gt;.database.windows.net

2. Přejděte do databáze *tenantů1.*

3. Dotaz *VenueTypes* tabulka potvrdit, že *motocyklové závody* a *plavecký klub* ještě nejsou v seznamu výsledků.

4. Připojte se k serveru katalogu, což je *katalog-mt-&lt;&gt;user .database.windows.net*.

5. Připojte se k databázi _pracovních míst_ na serveru katalogu.

6. V SSMS otevřete soubor *... Správa schématu\\učení DeployReferenceData.sql .\\ \\*

7. Upravte příkaz: @User &lt;set&gt; = uživatel a nahraďte hodnotu Uživatele použitou při nasazení aplikace Wingtip Tickets SaaS Multi-tenant Database.

8. Stisknutím klávesy **F5** spusťte skript.

#### <a name="observe"></a>Pozorovat

Ve skriptu *DeployReferenceData.sql* dodržujte následující položky:

- **sp\_\_add\_cílová skupina** vytvoří název cílové skupiny *DemoServerGroup*a přidá cílové členy do skupiny.

- **sp\_\_přidat\_\_člen cílové skupiny** přidá následující položky:
    - Typ cílového člena *serveru.*
        - Toto je *&lt;tenants1-mt- uživatelský&gt; * server, který obsahuje databáze klientů.
        - Včetně serveru zahrnuje databáze klienta, které existují v době spuštění úlohy.
    - Typ cílového člena *databáze* pro databázi šablon *(basetenantdb),* který je umístěn na uživatelském *&lt;&gt; serveru catalog-mt-* user,
    - Typ cílového člena *databáze* zahrnout *adhocreporting* databáze, která se používá v pozdější kurz.

- **sp\_\_add úloha** vytvoří úlohu s názvem *Reference Data Deployment*.

- **sp\_\_add jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL pro aktualizaci referenční tabulky VenueTypes.

- Zbývající pohledy ve skriptu zobrazují existující objekty a monitorují provádění úlohy. Pomocí těchto dotazů zkontrolujte hodnotu stavu ve **sloupci životního cyklu** a určete, kdy je úloha dokončena. Úloha aktualizuje databázi klientů a aktualizuje dvě další databáze, které obsahují referenční tabulku.

V SSMS přejděte do databáze klienta na *&lt;tenants1-mt- uživatelský&gt; * server. Dotaz *VenueTypes* tabulka pro potvrzení, že *motocyklové závody* a *plavecký klub* jsou nyní přidány do tabulky. Celkový počet typů míst by se měl zvýšit o dvě.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Vytvoření úlohy pro správu indexu referenční tabulky

Toto cvičení vytvoří úlohu k opětovnému sestavení indexu v primárním klíči referenční tabulky ve všech databázích klienta. Obnovení indexu je typická operace správy databáze, kterou může správce spustit po načtení velkého množství zatížení dat, aby se zlepšil výkon.

1. V SSMS se připojte k databázi _jobagent_ v *katalogu-mt-&lt;&gt;User .database.windows.net* server.

2. V SSMS, otevřít *... Výukové moduly\\Schema Management\\OnlineReindex.sql . \\*

3. Stisknutím klávesy **F5** spusťte skript.

#### <a name="observe"></a>Pozorovat

Ve skriptu *OnlineReindex.sql* dodržujte následující položky:

* **sp\_\_přidat úlohu** vytvoří novou úlohu s názvem *Online\_\_Reindex PK VenueTyp\_\_265E44FD7FD4C885*.

* **sp\_\_add jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL pro aktualizaci indexu.

* Zbývající zobrazení ve spuštění úlohy sledování skriptu. Pomocí těchto dotazů zkontrolujte hodnotu stavu ve **sloupci životního cyklu** a určete, kdy byla úloha úspěšně dokončena u všech členů cílové skupiny.

## <a name="additional-resources"></a>Další zdroje

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Správa cloudových databází s horizontálním navýšením kapacity](elastic-jobs-overview.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření agenta úloh pro spuštění úloh T-SQL ve více databázích
> * Aktualizace referenčních dat ve všech databázích klientů
> * Vytvořit index tabulky ve všech databázích tenantů

Dále zkuste [kurz ad hoc vykazování](saas-multitenantdb-adhoc-reporting.md) prozkoumat spuštěné distribuované dotazy napříč databázemi klientů.

