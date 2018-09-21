---
title: Správa schématu Azure SQL Database v aplikaci s jedním tenantem | Dokumentace Microsoftu
description: Správa schématu pro více tenantů v aplikace s jedním tenantem, která používá Azure SQL Database
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 6a37bdd6e8b7893da5df1113f5f3f153148cb1b0
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498075"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Správa schématu v SaaS aplikaci pomocí vzoru databáze na tenanta s Azure SQL Database
 
Jak databázi aplikace vyvíjí, změny nevyhnutelně musí být provedené v datech schématu nebo odkaz na databázi.  Úlohy údržby databáze je potřeba také pravidelně. Správa aplikace, která používá databázi za vzor tenanta vyžaduje použití těchto změn nebo úlohy údržby v počtu klientských databází.

Tento kurz zkoumá dva scénáře: nasazení aktualizací referenčních dat u všech tenantů a nové sestavení indexu pro tabulku obsahující referenční data. [Elastic jobs](sql-database-elastic-jobs-overview.md) funkce se používá k provedení těchto akcí ve všech databázích tenantů a v databázi šablony použité k vytvoření nového tenanta databází.

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Vytvořit úlohu agenta
> * Způsobit, že úlohy T-SQL ke spuštění ve všech databázích tenantů
> * Aktualizace referenčních dat ve všech databázích tenantů
> * Vytvořit index tabulky ve všech databázích tenantů


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace Wingtip Tickets SaaS databáze na Tenanta. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání SaaS aplikace Wingtip Tickets databáze na tenanta aplikaci.](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Je nainstalovaná nejnovější verze SQL Server Management Studia (SSMS). [Stažení a instalace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Tento kurz používá funkce služby SQL Database, které jsou ve verzi limited preview (úlohy elastické databáze). Pokud chcete provést v tomto kurzu, zadejte ID svého předplatného na SaaSFeedback@microsoft.com předmět uveďte Elastic Jobs Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). V této verzi preview je omezená, takže obraťte se na SaaSFeedback@microsoft.com pro dotazy související s hesly nebo podpory.

## <a name="introduction-to-saas-schema-management-patterns"></a>Úvod do principu správy schématu SaaS

Databáze na tenanta vzor efektivně izoluje dat tenantů, ale zvyšuje počet databází pro správu a údržbu. [Elastické úlohy](sql-database-elastic-jobs-overview.md) usnadňuje správu a Správa databází SQL. Úlohy umožňují bezpečně a spolehlivě spouštět úlohy (skriptů T-SQL) pro skupinu databází. Úlohy můžete nasadit schéma a běžným změnám referenčních dat napříč všemi databázemi tenantů v aplikaci. Elastické úlohy může také sloužit k udržování *šablony* databáze používaná k vytváření nových tenantů, aby se vždy obsahuje nejnovější schéma a referenční data.

![obrazovka](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Elastic Jobs verze Limited Preview

Existuje nová verze služby Elastic Jobs je teď integrované funkce služby Azure SQL Database. Tato nová verze služby Elastic Jobs je v současnosti ve verzi Limited Preview. Verze limited preview momentálně podporuje použití Powershellu k vytvoření úlohy agenta a T-SQL k vytvoření a Správa úloh.

> [!NOTE]
> Tento kurz používá funkce služby SQL Database, které jsou ve verzi limited preview (úlohy elastické databáze). Pokud chcete provést v tomto kurzu, zadejte ID svého předplatného na SaaSFeedback@microsoft.com předmět uveďte Elastic Jobs Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). V této verzi preview je omezená, takže obraťte se na SaaSFeedback@microsoft.com pro dotazy související s hesly nebo podpory.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat databázi SaaS aplikace Wingtip Tickets za skriptů aplikace tenanta

Zdrojový kód a správu skriptů aplikace jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skripty.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Vytvoření úlohy agenta, databáze a nové úlohy agenta

Tento kurz vyžaduje použití Powershellu k vytvoření úlohy agenta a jeho databáze agenta úloh zálohování. Databáze agenta úloh obsahuje definice úloh, stavy úloh a historii. Jakmile se vytvoří úloha agenta a jeho databáze, můžete vytvořit a monitorovat úlohy okamžitě.

1. **V prostředí PowerShell ISE**, otevřete... \\Learning Modules\\Schema Management\\*Demo-SchemaManagement.ps1*.
1. Stisknutím klávesy **F5** spusťte skript.

*Demo-SchemaManagement.ps1* volání skriptu *Deploy-SchemaManagement.ps1* skript k vytvoření databáze SQL s názvem *osagent* na server katalogu. Potom vytvoří úlohu agenta, pomocí databáze jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Vytvoření úlohy, která u všech tenantů nasadí nová referenční data

V aplikaci Wingtip Tickets Každá databáze tenanta zahrnuje sadu typů míst podporované. Každé místo je místo konkrétní typ, který definuje typ události, které je možné hostovat a určuje obrázku pozadí používané v aplikaci. Pro aplikaci, aby podporovala nové typy událostí musí být těmto referenčním datům typy míst aktualizované a nové přidán.  V tomto příkladu nasadíte aktualizaci u všech databází tenantů. Aktualizace přidá dva další typy míst: *Motorcycle Racing* (Motocyklové závody) a *Swimming Club* (Plavecký klub).

Nejprve si přečtěte typy míst zahrnuta v každé databázi tenantů. Připojte se k jedné z databází tenantů v SQL Server Management Studio (SSMS) a zkontrolujte tabulku VenueTypes.  Můžete také zadávat dotazy této tabulky v editoru dotazů na webu Azure Portal k němu přistupovat z databáze. 

1. Otevřete SSMS a připojte se k serveru tenanta: *tenants1-dpt -&lt;uživatele&gt;. database.windows.net*
1. Zkontrolujte, že *Motorcycle Racing* a *Swimming Club* **nejsou** momentálně zahrnutá, přejděte _contosoconcerthall_ databáze na *tenants1-dpt -&lt;uživatele&gt;*  serveru a dotazování *VenueTypes* tabulky.

Teď vytvoříme úlohu, která aktualizuje *VenueTypes* tabulky ve všech databázích tenantů pro přidání nové typy míst.

Chcete-li vytvořit novou úlohu, používáte sadu úloh systémové uložené procedury vytvořené v _jobagent_ databáze při vytváření úlohy agenta.

1. V aplikaci SSMS připojit k serveru katalogu: *katalogu-dpt -&lt;uživatele&gt;. database.windows.net* serveru 
1. V aplikaci SSMS otevřete soubor... \\Learning Modules\\Schema Management\\DeployReferenceData.sql
1. Upravit příkaz: nastavte @wtpUser = &lt;uživatele&gt; a nahraďte hodnotu uživatelského jste použili při nasazení aplikace Wingtip Tickets SaaS databáze na Tenanta
1. Zkontrolujte, že se připojení k _jobagent_ databáze a stisknutím klávesy **F5** pro spuštění skriptu

Podívejte se následující prvky *DeployReferenceData.sql* skriptu:
* **SP\_přidat\_cílové\_skupiny** vytvoří cílovou skupinu s názvem DemoServerGroup.
* **SP\_přidat\_cílové\_skupiny\_člen** slouží k definování sady cílové databáze.  První _tenants1-dpt -&lt;uživatele&gt;_  přidání serveru.  Přidání serveru jako cíl způsobí, že databáze v tomto serveru v době provádění úlohy, které mají být zahrnuty do úlohy. Pak bude _basetenantdb_ databáze a *adhocreporting* databáze (používaná v pozdějších kurzech) jsou přidány jako cíle.
* **SP\_přidat\_úlohy** vytvoří úlohu s názvem _Reference Data Deployment_.
* **SP\_přidat\_jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL k aktualizaci referenční tabulku VenueTypes.
* Zbývající pohledy ve skriptu zobrazují existující objekty a monitorují provádění úlohy. Zkontrolujte stavovou hodnotu v pomocí těchto dotazů **životního cyklu** sloupec k určení, kdy dokončení úlohy na všechny cílové databáze.

Po dokončení skriptu můžete ověřit, že byl aktualizován referenční data.  V SSMS přejděte k *contosoconcerthall* databáze na *tenants1-dpt -&lt;uživatele&gt;*  serveru a dotazování *VenueTypes* tabulky.  Zkontrolujte, že *Motorcycle Racing* a *Swimming Club* **jsou** nyní k dispozici.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Vytvoření úlohy pro správu indexu referenční tabulky

Toto cvičení používá úlohu chcete-li znovu sestaví index primárního klíče referenční tabulky.  Jedná se o operaci údržby typické databáze, který může být provedeno po načtení velkého objemu dat.

K vytvoření úlohy použijeme stejné úlohy uložených procedur „system“.

1. Otevřete SSMS a připojte se k _katalogu-dpt -&lt;uživatele&gt;. database.windows.net_ serveru
1. Otevřete soubor _... \\Learning Modules\\Schema Management\\OnlineReindex.sql_
1. Klikněte pravým tlačítkem, vyberte připojení a připojte se k _katalogu-dpt -&lt;uživatele&gt;. database.windows.net_ serveru, pokud již nejste připojeni
1. Zkontrolujte, že se připojení k _jobagent_ databáze a stisknutím klávesy **F5** pro spuštění skriptu

Podívejte se následující prvky _OnlineReindex.sql_ skriptu:
* **SP\_přidat\_úlohy** vytvoří novou úlohu s názvem "Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885"
* **SP\_přidat\_jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL k aktualizaci indexu
* Zbývající pohledy ve skriptu monitorují provádění úlohy. Zkontrolujte stavovou hodnotu v pomocí těchto dotazů **životního cyklu** sloupec k určení, kdy úloha byla úspěšně dokončena na všechny cílové skupiny členy.



## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Vytvořit úlohu agenta ke spuštění více databází mezi úlohami T-SQL
> * Aktualizace referenčních dat ve všech databázích tenantů
> * Vytvořit index tabulky ve všech databázích tenantů

Zkuste [Ad-hoc, generování sestav kurzu](saas-tenancy-cross-tenant-reporting.md) prozkoumat spouštění distribuovaných vzorců napříč tenanta databází.


## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které vycházejí z nasazení aplikace Wingtip Tickets SaaS databáze na Tenanta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Správa cloudových databází s horizontálním navýšením kapacity](sql-database-elastic-jobs-overview.md)
* [Vytvoření a správa databází s horizontálním navýšením kapacity](sql-database-elastic-jobs-create-and-manage.md)