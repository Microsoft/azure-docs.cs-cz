---
title: Správa schématu Azure SQL Database v aplikaci s více tenanty | Dokumentace Microsoftu
description: Správa schématu pro více tenantů v aplikaci s více tenanty využívající službu Azure SQL Database
keywords: kurz k sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 026f3450535e4bed6a636fc5ae6ee9d821dbbb72
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247662"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Správa schématu v aplikaci SaaS, která používá horizontálně dělené databáze SQL více tenantů

Tento kurz zkoumá výzvy zachování počtu databází ve Software jako služba (SaaS) aplikací. Řešení je ukázán pro ventilační si změn schématu napříč flotilou databází.

Jako každá aplikace SaaS aplikace Wingtip Tickets aplikace bude časem vyvíjet a bude vyžadovat změny v databázi. Změny může mít vliv na data schématu nebo odkaz, nebo použít úlohy údržby databáze. U SaaS aplikace pomocí databáze na tenanta vzor musí být změny koordinovat napříč potenciálně velkého počtu databází tenantů. Kromě toho musí obsahovat tyto změny do databáze, ujistěte se, že jsou zahrnuty do nových databází při jejich vytvoření procesu zřizování.

#### <a name="two-scenarios"></a>Dva scénáře

Tento kurz se věnuje v následujících dvou scénářích:
- Nasazení aktualizací referenčních dat pro všechny tenanty.
- Opětovné sestavení indexu pro tabulku, která obsahuje referenční data.

[Elastic Jobs](sql-database-elastic-jobs-overview.md) funkce služby Azure SQL Database slouží k provádění těchto operací u databází tenantů. Úlohy, které se také používat pro databázi tenanta "Šablona". V ukázkové aplikace Wingtip Tickets je zkopírován tuto šablonu databázi zřídit nové databáze tenanta.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte úlohu agenta.
> * Provedení dotazu T-SQL ve více databázích tenantů.
> * Aktualizace referenčních dat ve všech databázích tenantů.
> * Vytvoření indexu pro tabulku ve všech databázích tenantů.

## <a name="prerequisites"></a>Požadavky

- Aplikace Wingtip Tickets databázi s více tenanty musí již být nasazen:
    - Pokyny najdete v první kurz, který představuje databáze víceklientské aplikace SaaS aplikace Wingtip Tickets:<br />[Nasazení a zkoumání horizontálně dělené aplikace více tenanty využívající Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - Procesu nasazení se spustí pro méně než pět minut.
    - Musíte mít *horizontálně dělené víceklientské* verzi Wingtip nainstalované. Verze pro *samostatné* a *databáze na tenanta* nepodporují v tomto kurzu.

- Musí být nainstalovaná nejnovější verze SQL Server Management Studio (SSMS). [Stažení a instalace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Musí být nainstalované prostředí Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure Powershellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Tento kurz používá funkce služby Azure SQL Database, které jsou ve verzi limited preview ([úlohy elastické databáze](sql-database-elastic-database-client-library.md)). Pokud chcete provést v tomto kurzu, zadejte ID svého předplatného na *SaaSFeedback@microsoft.com* předmět uveďte Elastic Jobs Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). V této verzi preview je omezená, takže obraťte se na *SaaSFeedback@microsoft.com* pro dotazy související s hesly nebo podpory.

## <a name="introduction-to-saas-schema-management-patterns"></a>Úvod do principu správy schématu SaaS

Model horizontálně dělené databázi s více tenanty používané v tomto příkladu umožňuje databáze tenantů tak, aby obsahovala jeden nebo více tenantů. Tato ukázka zkoumá schopnost používat kombinaci více tenantů a jedním klientem databází, povolení *hybridní* model správy tenanta. Správa změn tyto databáze můžou být složité. [Elastické úlohy](sql-database-elastic-jobs-overview.md) usnadňuje správu a správu velkého počtu databází. Úlohy umožňují bezpečně a spolehlivě spouštět skripty jazyka Transact-SQL jako úkoly pro skupinu databází tenantů. Úlohy jsou nezávislé na vstupu nebo interakci uživatelů. Tuto metodu lze použít k nasazení změny schématu nebo běžné referenčních dat u všech tenantů v aplikaci. Elastické úlohy lze také udržovat zlaté šablony kopie databáze. Šablona se používá k vytvoření nových tenantů, aby vždy nejnovější schéma a referenčních dat se používají.

![obrazovka](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastic Jobs verze Limited Preview

Existuje nová verze služby Elastic Jobs je teď integrované funkce služby Azure SQL Database. Tato nová verze služby Elastic Jobs je v současnosti ve verzi Limited Preview. Omezenému ve verzi preview momentálně podporuje použití Powershellu k vytvoření úlohy agenta a T-SQL k vytvoření a Správa úloh.
> [!NOTE] 
> Tento kurz používá funkce služby SQL Database, které jsou ve verzi limited preview (úlohy elastické databáze). Pokud chcete provést v tomto kurzu, zadejte ID svého předplatného na SaaSFeedback@microsoft.com předmět uveďte Elastic Jobs Preview. Jakmile dostanete potvrzení, že vaše předplatné se povolila, stáhněte a nainstalujte nejnovější předběžnou verzi rutin úloh. V této verzi preview je omezená, takže obraťte se na SaaSFeedback@microsoft.com pro dotazy související s hesly nebo podpory.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Získat zdrojový kód aplikace Wingtip Tickets SaaS databázi s více tenanty a skripty

Jsou k dispozici v databázi s více tenanty SaaS aplikace Wingtip Tickets skripty a zdrojový kód aplikace [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) úložišti na Githubu. Zobrazit [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skripty. 

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Vytvoření úlohy agenta, databáze a nové úlohy agenta

Tento kurz vyžaduje použití Powershellu k vytvoření databáze agenta úloh a úlohy agenta. Jako databáze MSDB, která používá SQL Agent používá agent úlohy Azure SQL database k ukládání definice úloh, stavy úloh a historii. Po vytvoření úlohy agenta můžete vytvořit a monitorovat úlohy okamžitě.

1. V **prostředí PowerShell ISE**, otevřete *... \\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1*.
2. Stisknutím klávesy **F5** spusťte skript.

*Demo-SchemaManagement.ps1* volání skriptu *Deploy-SchemaManagement.ps1* skript k vytvoření databáze s názvem _jobagent_ na server katalogu. Tento skript vytvoří úlohu agenta a předá _jobagent_ databáze jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Vytvoření úlohy, která u všech tenantů nasadí nová referenční data

#### <a name="prepare"></a>Příprava

Databáze každého tenanta zahrnuje sadu typů míst v **VenueTypes** tabulky. Každý typ místa definuje typ události, které může být hostovaných jako místo. Tyto typy míst odpovídají obrázky na pozadí, které se zobrazí v aplikaci událostí tenanta.  V tomto cvičení nasadíte aktualizaci u všech databází, které chcete přidat dva další typy míst: *Motorcycle Racing* a *Swimming Club*. 

Nejprve si přečtěte typy míst zahrnuta v každé databázi tenantů. Připojte se k jedné z databází tenantů v SQL Server Management Studio (SSMS) a zkontrolujte tabulku VenueTypes.  Můžete také zadávat dotazy této tabulky v editoru dotazů na webu Azure Portal k němu přistupovat z databáze. 

1. Otevřete SSMS a připojte se k serveru tenanta: *tenants1-dpt -&lt;uživatele&gt;. database.windows.net*
1. Zkontrolujte, že *Motorcycle Racing* a *Swimming Club* **nejsou** momentálně zahrnutá, přejděte *contosoconcerthall* databáze na *tenants1-dpt -&lt;uživatele&gt;*  serveru a dotazování *VenueTypes* tabulky.



#### <a name="steps"></a>Kroky

Nyní můžete vytvořit úlohy pro aktualizaci **VenueTypes** tabulku v každé databázi tenantů, přidejte dva nové typy míst.

Chcete-li vytvořit novou úlohu, použijte sadu úloh systémové uložené procedury, které byly vytvořeny v _jobagent_ databáze. Uložené procedury byly vytvořeny při vytvoření úlohy agenta.

1. V aplikaci SSMS připojit k serveru tenanta: tenants1-mt –&lt;uživatele&gt;. database.windows.net

2. Přejděte *tenants1* databáze.

3. Dotaz *VenueTypes* tabulky, abyste potvrdili, že *Motorcycle Racing* a *Swimming Club* nejsou ještě v seznamu výsledků.

4. Připojení k serveru katalogu, což je *katalogu-mt –&lt;uživatele&gt;. database.windows.net*.

5. Připojte se k _jobagent_ databázi na serveru katalogu.

6. V aplikaci SSMS otevřete soubor *... \\Learning Modules\\Schema Management\\DeployReferenceData.sql*.

7. Upravit příkaz: nastavte @User = &lt;uživatele&gt; a nahraďte hodnotu uživatelského jste použili při nasazení aplikace Wingtip Tickets SaaS databázi s více tenanty.

8. Stisknutím klávesy **F5** spusťte skript.

#### <a name="observe"></a>Pozorovat

Podívejte se následující položky *DeployReferenceData.sql* skriptu:

- **SP\_přidat\_cílové\_skupiny** vytvoří cílovou skupinu s názvem *DemoServerGroup*, a přidá členy v cílové skupině.

- **SP\_přidat\_cílové\_skupiny\_člen** přidá následující položky:
    - A *server* typ člena.
        - Toto je *tenants1-mt –&lt;uživatele&gt;*  server obsahující databáze tenantů.
        - Včetně serveru obsahuje databáze tenantů, které existují v okamžiku, kdy úloha spustí.
    - A *databáze* typ člena pro nevyužije šablonu databáze (*basetenantdb*), který se nachází na *katalogu-mt –&lt;uživatele&gt;*  serveru
    - A *databáze* typ člena zahrnout *adhocreporting* databázi, která se používá v pozdějších kurzech.

- **SP\_přidat\_úlohy** vytvoří úlohu s názvem *Reference Data Deployment*.

- **SP\_přidat\_jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL k aktualizaci referenční tabulku VenueTypes.

- Zbývající pohledy ve skriptu zobrazují existující objekty a monitorují provádění úlohy. Zkontrolujte stavovou hodnotu v pomocí těchto dotazů **životního cyklu** sloupec k určení, kdy úloha dokončí. Úloha aktualizace databáze tenantů a aktualizuje na dvou dalších databázích obsahujících referenční tabulku.

V SSMS přejděte k databázi tenanta na *tenants1-mt –&lt;uživatele&gt;*  serveru. Dotaz *VenueTypes* tabulky, abyste potvrdili, že *Motorcycle Racing* a *Swimming Club* jsou teď přidané do tabulky. Celkový počet typů míst by měl mít zvýšila dvakrát.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Vytvoření úlohy pro správu indexu referenční tabulky

V tomto cvičení vytvoříme úlohu, která znovu sestaví index primárního klíče referenční tabulky ve všech databázích tenantů. Operace správy typické databáze, které by mohly správce je opětovné sestavení indexu po načtení velkého počtu načtení dat, ke zlepšení výkonu.

1. V aplikaci SSMS připojit k _jobagent_ databáze v *katalogu-mt –&lt;uživatele&gt;. database.windows.net* serveru.

2. V aplikaci SSMS otevřete *... \\Learning Modules\\Schema Management\\OnlineReindex.sql*.

3. Stisknutím klávesy **F5** spusťte skript.

#### <a name="observe"></a>Pozorovat

Podívejte se následující položky *OnlineReindex.sql* skriptu:

* **SP\_přidat\_úlohy** vytvoří novou úlohu nazvanou *Online přeindexování PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_přidat\_jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL k aktualizaci indexu.

* Zbývající pohledy ve skriptu monitorují provádění úlohy. Zkontrolujte stavovou hodnotu v pomocí těchto dotazů **životního cyklu** sloupec k určení, kdy úloha byla úspěšně dokončena na všechny cílové skupiny členy.

## <a name="additional-resources"></a>Další zdroje informací:

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Správa cloudových databází s horizontálním navýšením kapacity](sql-database-elastic-jobs-overview.md)
* [Vytvoření a správa databází s horizontálním navýšením kapacity](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit úlohu agenta ke spuštění T-SQL úloh napříč několika databázemi
> * Aktualizace referenčních dat ve všech databázích tenantů
> * Vytvořit index tabulky ve všech databázích tenantů

Zkuste [Ad-hoc, generování sestav kurzu](saas-multitenantdb-adhoc-reporting.md) prozkoumat spouštění distribuovaných vzorců napříč tenanta databází.

