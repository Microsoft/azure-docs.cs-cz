---
title: "Správa schématu Azure SQL Database v aplikaci s více tenanty | Dokumentace Microsoftu"
description: "Správa schématu pro více tenantů v aplikaci s více tenanty využívající službu Azure SQL Database"
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 07/28/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: a473e87934ba573cc22b2c248ea0398bc5a4c29b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Správa schématu v aplikaci SaaS vzoru databáze za klienta pomocí databáze SQL Azure

Jako databáze aplikace zpracovaní změny nevyhnutelnou nutné provést k datům databáze schématu nebo odkaz.  Úlohy údržby databáze, je také potřeba pravidelně. Správa aplikace, která používá databázi za klienta vzor vyžaduje napříč firemního vozového databází klienta použít tyto změny, nebo úlohy údržby.

Jsou zde popsány v tomto kurzu dva scénáře - nasazení aktualizací referenční data pro všechny klienty a nové sestavení indexu pro tabulku obsahující referenční data. [Elastické úlohy](sql-database-elastic-jobs-overview.md) funkce se používá k provedení těchto akcí na všechny databáze klienta a na šablonu databáze ze použít k vytvoření nového klienta databází.

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Vytvořit úlohy agenta
> * Dojít T-SQL úlohy ke spuštění na všechny databáze klienta
> * Aktualizace referenční data v databázích všechny klienta
> * Vytvořit index tabulky ve všech databázích tenantů


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace Wingtip lístky SaaS databáze za klienta. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s SaaS lístky Wingtip databáze za klienta aplikace](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Je nainstalovaná nejnovější verze SQL Server Management Studia (SSMS). [Stažení a instalace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Tento kurz používá funkce služby SQL Database, které jsou v omezené preview (úlohy elastické databáze). Pokud chcete provést v tomto kurzu, zadejte svoje ID předplatného pro SaaSFeedback@microsoft.com s předmětem = elastické úlohy Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). Tato předběžná verze je omezená, takže obraťte se na SaaSFeedback@microsoft.com pro dotazy související s ani nepodporuje.

## <a name="introduction-to-saas-schema-management-patterns"></a>Úvod k modelům správy schématu SaaS

Databáze za klienta vzor efektivně izoluje dat klienta, ale zvyšuje počet databází pro správu a údržbu. [Elastické úlohy](sql-database-elastic-jobs-overview.md) usnadňuje administraci a správu databází SQL. Úlohy umožňují bezpečně a spolehlivě, spouštět úlohy (skriptů T-SQL) pro skupinu databází. Úlohy můžete nasadit schéma a běžných změn referenčních dat napříč všechny databáze klienta v aplikaci. Elastické úlohy lze také použít k udržování *šablony* databáze používaná k vytvoření nové klienty, je vždy zajištění byla nejnovější schématu a referenční data.

![obrazovka](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Elastic Jobs verze Limited Preview

Není k dispozici nová verze elastické úlohy, který je teď integrované funkce databáze SQL Azure. Tato nová verze služby Elastic Jobs je v současnosti ve verzi Limited Preview. Tato omezená verze preview aktuálně podporuje vytváření úlohy agenta a T-SQL, vytvářet a spravovat úlohy pomocí prostředí PowerShell.

> [!NOTE]
> Tento kurz používá funkce služby SQL Database, které jsou v omezené preview (úlohy elastické databáze). Pokud chcete provést v tomto kurzu, zadejte svoje ID předplatného pro SaaSFeedback@microsoft.com s předmětem = elastické úlohy Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). Tato předběžná verze je omezená, takže obraťte se na SaaSFeedback@microsoft.com pro dotazy související s ani nepodporuje.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat adresář Wingtip lístky SaaS databáze za skripty aplikace klienta

Zdrojový kód a správu skripty aplikace jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Vytvoření úlohy agenta, databáze a nové úlohy agenta

Tento kurz vyžaduje že pomocí prostředí PowerShell vytvořit úlohy agenta a jeho základní úlohy agenta databáze. Úlohy agenta databáze obsahuje definice úlohy, stav úlohy a historie. Po vytvoření úlohy agenta a její databází můžete vytvořit a monitorujte úlohy, okamžitě.

1. **V prostředí PowerShell ISE**, otevřete... \\Učení moduly\\Správa schématu\\*ukázku SchemaManagement.ps1*.
1. Stisknutím klávesy **F5** spusťte skript.

*Ukázku SchemaManagement.ps1* skript volání *nasadit SchemaManagement.ps1* skript pro vytvoření databáze SQL s názvem *osagent* na serveru katalogu. Pak vytvoří úlohu agenta, pomocí databáze jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Vytvoření úlohy, která u všech tenantů nasadí nová referenční data

V aplikaci Wingtip lístky každou databázi klienta obsahuje sadu typů podporovaných místo. Každý místo je konkrétní místo typu, který definuje typ události, které se dají hostovat a určuje obrázek pozadí použitý v aplikaci. Pro aplikaci, aby podporovala nové druhy událostí musí být tato referenční data místo aktualizované a nové typy přidat.  V tomto příkladu nasadíte aktualizaci u všech databází tenantů. Aktualizace přidá dva další typy míst: *Motorcycle Racing* (Motocyklové závody) a *Swimming Club* (Plavecký klub).

Přečtěte si nejprve, místo typy součástí každou databázi klienta. Připojit k jedné z klienta databází v SQL Server Management Studio (SSMS) a zkontrolujte tabulku VenueTypes.  Můžete taky zadat dotaz této tabulky v editoru dotazů na portálu Azure k němu přistupovat z stránky databáze. 

1. Otevřete aplikaci SSMS a připojení k serveru klienta: *tenants1-dpt -&lt;uživatele&gt;. database.windows.net*
1. Zkontrolujte, že *Motorky Racing* a *křížovou kartou plaveckých* **nejsou** momentálně zahrnutá, vyhledejte _contosoconcerthall_ databáze na *tenants1-dpt -&lt;uživatele&gt;*  serveru a dotaz *VenueTypes* tabulky.

Teď vytvoříme úlohu, která aktualizuje *VenueTypes* tabulky v všechny databáze klienta přidávání nových typů místo.

Pokud chcete vytvořit novou úlohu, pomocí sady uložené procedury vytvořené v systému úlohy _jobagent_ databáze při vytvoření úlohy agenta.

1. V aplikaci SSMS, připojení k serveru katalogu: *katalogu-dpt -&lt;uživatele&gt;. database.windows.net* serveru 
1. V aplikaci SSMS otevřete soubor... \\Učení moduly\\Správa schématu\\DeployReferenceData.sql
1. Upravit příkaz: nastavte @wtpUser = &lt;uživatele&gt; a nahraďte hodnotu uživatele použít při nasazení aplikace Wingtip lístky SaaS databáze za klienta
1. Zkontrolujte připojení k _jobagent_ databáze a stiskněte klávesu **F5** pro spuštění skriptu

Sledovat následující prvky *DeployReferenceData.sql* skriptu:
* **SP\_přidat\_cíl\_skupiny** vytvoří cílová skupina DemoServerGroup.
* **SP\_přidat\_cíl\_skupiny\_člen** se používá k definování sady cílovým databázím.  První _tenants1-dpt -&lt;uživatele&gt;_  přidat server.  Přidání serveru jako cíl způsobí, že databáze v tomto serveru při provádění úlohy, které mají být zahrnuty do úlohy. Pak se _basetenantdb_ databáze a *adhocreporting* databáze (používá se v pozdější kurzu) jsou přidány jako cíle.
* **SP\_přidat\_úlohy** vytvoří úlohu s názvem _referenční Data nasazení_.
* **SP\_přidat\_krok úlohy** vytvoří krok úlohy obsahující text příkazů T-SQL aktualizovat na referenční tabulku VenueTypes.
* Zbývající pohledy ve skriptu zobrazují existující objekty a monitorují provádění úlohy. Ke kontrole hodnota stavu v použijte tyto dotazy **životního cyklu** sloupec k určení, kdy má úloha dokončena na všechny cílové databáze.

Po dokončení skriptu, můžete ověřit, že je aktualizovaná referenční data.  V aplikaci SSMS, přejděte do *contosoconcerthall* databáze na *tenants1-dpt -&lt;uživatele&gt;*  serveru a dotaz *VenueTypes* tabulky.  Zkontrolujte, zda *Motorky Racing* a *křížovou kartou plaveckých* **jsou** nyní k dispozici.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Vytvoření úlohy pro správu indexu referenční tabulky

Tento postup používá úlohu znovu sestavit index referenční primární klíč tabulky.  Toto je operace údržby typické databáze, které může být provedeno po načtení velkých objemů dat.

K vytvoření úlohy použijeme stejné úlohy uložených procedur „system“.

1. Otevřete aplikaci SSMS a připojte se k _katalogu-dpt -&lt;uživatele&gt;. database.windows.net_ serveru
1. Otevřete soubor _... \\Učení moduly\\Správa schématu\\OnlineReindex.sql_
1. Klikněte pravým tlačítkem, vyberte připojení a připojení k _katalogu-dpt -&lt;uživatele&gt;. database.windows.net_ serveru, pokud ještě není připojen.
1. Zkontrolujte připojení k _jobagent_ databáze a stiskněte klávesu **F5** pro spuštění skriptu

Sledovat následující prvky _OnlineReindex.sql_ skriptu:
* **SP\_přidat\_úlohy** vytvoří novou úlohu názvem "Online nové indexování Primárníklíč\_\_VenueTyp\_\_265E44FD7FD4C885"
* **SP\_přidat\_krok úlohy** vytvoří krok úlohy obsahující text T-SQL příkaz k aktualizaci indexu
* Zbývající zobrazení ve skriptu monitorování provádění úlohy. Použít tyto dotazy ke kontrole hodnota stavu v **životního cyklu** k určení, když úloha úspěšně dokončil na všechny členy skupiny cílový sloupec.



## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Vytvořit úlohy agenta ke spuštění více databází mezi úlohami T-SQL
> * Aktualizace referenční data v databázích všechny klienta
> * Vytvořit index tabulky ve všech databázích tenantů

Zkuste [Ad-hoc reporting kurzu](saas-tenancy-cross-tenant-reporting.md) prozkoumat spuštění distribuované dotazy pro klienta databází.


## <a name="additional-resources"></a>Další zdroje informací:

* [Další kurzy, které stavět na adresář Wingtip lístky SaaS databáze za klienta nasazení aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Správa cloudových databází s horizontálním navýšením kapacity](sql-database-elastic-jobs-overview.md)
* [Vytvoření a správa databází s horizontálním navýšením kapacity](sql-database-elastic-jobs-create-and-manage.md)