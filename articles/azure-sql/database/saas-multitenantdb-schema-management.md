---
title: Správa schématu v aplikaci s více klienty
description: Správa schématu pro více tenantů v aplikaci s více tenanty využívající službu Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: d222234cd6ff3d910e6dbc51a394695ce467edce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011846"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-databases"></a>Správa schématu v SaaS aplikaci, která používá horizontálně dělené víceklientské databáze
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu se podíváme na výzvy k údržbě loďstva databází v aplikaci software jako služba (SaaS). Pro Fanning se změny schématu v rámci loďstva databází ukázala řešení.

Stejně jako u jakékoli aplikace se aplikace Wingtip Tickets SaaS v průběhu času vyvíjí a bude vyžadovat změny v databázi. Změny mohou ovlivnit schéma nebo referenční data nebo provádět úlohy údržby databáze. U aplikace SaaS s využitím modelu databáze na tenanta se změny musí koordinovat v rámci potenciálně obrovského loďstva databází klientů. Kromě toho musíte tyto změny začlenit do procesu zřizování databáze, abyste se ujistili, že jsou zahrnuté do nových databází při jejich vytváření.

#### <a name="two-scenarios"></a>Dva scénáře

V tomto kurzu se seznámíte s následujícími dvěma scénáři:
- Nasaďte aktualizace referenčních dat pro všechny klienty.
- Znovu sestavte index v tabulce, která obsahuje referenční data.

Funkce [elastické úlohy](./elastic-jobs-overview.md) Azure SQL Database slouží ke spouštění těchto operací mezi databázemi klientů. Úlohy také fungují v databázi tenanta Template. V ukázkové aplikaci Wingtip Tickets se tato šablona databáze zkopíruje, aby se zřídila nová databáze tenanta.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvořte agenta úloh.
> * Spustí dotaz T-SQL ve více databázích tenanta.
> * Aktualizuje referenční data ve všech databázích tenanta.
> * Vytvoří index tabulky ve všech databázích tenantů.

## <a name="prerequisites"></a>Předpoklady

- Aplikace víceklientské lístky Wingtip již musí být nasazené:
    - Pokyny najdete v prvním kurzu, který zavádí společnost Wingtip Tickets SaaS multi-tenant Database App:<br />[Nasazení a zkoumání horizontálně dělené aplikace s více klienty, která používá Azure SQL Database](./saas-multitenantdb-get-started-deploy.md).
        - Proces nasazení se spouští po dobu kratší než pět minut.
    - Musíte mít nainstalovanou *horizontálně dělené verzi klienta* Wingtip. Verze pro *samostatnou* databázi a pro *databáze na klienta* tento kurz nepodporují.

- Musí být nainstalovaná nejnovější verze SQL Server Management Studio (SSMS). [Stáhněte a nainstalujte SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell musí být nainstalované. Podrobnosti najdete v tématu [Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps).

> [!NOTE]
> V tomto kurzu se používají funkce služby Azure SQL Database, které jsou ve verzi omezené verze Preview ([úlohy elastické databáze](elastic-database-client-library.md)). Pokud chcete tento kurz udělat, zadejte ID předplatného a *SaaSFeedback \@ Microsoft.com* s předmětem = elastické úlohy ve verzi Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). Tato verze Preview je omezená, takže pro související otázky nebo podporu se obraťte na *SaaSFeedback \@ Microsoft.com* .

## <a name="introduction-to-saas-schema-management-patterns"></a>Seznámení se vzory správy schématu SaaS

Model víceklientské databáze horizontálně dělené použitý v této ukázce umožňuje, aby databáze tenantů obsahovala jednoho nebo více tenantů. Tato ukázka zkoumá potenciál pro použití kombinace mnoha databází tenantů a jednoho tenanta a umožňuje model správy *hybridního* tenanta. Správa změn v těchto databázích může být složitá. [Elastické úlohy](./elastic-jobs-overview.md) usnadňují správu a správu velkého počtu databází. Úlohy umožňují bezpečně a spolehlivě spouštět skripty Transact-SQL jako úlohy, a to na skupinu databází tenanta. Úkoly jsou nezávislé na interakci nebo vstupu uživatele. Tuto metodu lze použít k nasazení změn schématu nebo běžných referenčních dat napříč všemi klienty v aplikaci. Elastické úlohy je také možné použít k údržbě zlatých kopií šablon databáze. Šablona se používá k vytváření nových tenantů. vždycky se zajišťují, že se používají nejnovější schéma a referenční data.

![obrazovka](./media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastic Jobs verze Limited Preview

K dispozici je nová verze elastických úloh, která je teď integrovanou funkcí Azure SQL Database. Tato nová verze služby Elastic Jobs je v současnosti ve verzi Limited Preview. Omezená verze Preview aktuálně podporuje použití PowerShellu k vytvoření agenta úloh a T-SQL pro vytváření a správu úloh.
> [!NOTE]
> Tento kurz používá funkce služby SQL Database, které fungují ve verzi Limited Preview (úlohy služby Elastic Database). Pokud chcete tento kurz udělat, zadejte ID předplatného do složky SaaSFeedback@microsoft.com Předmět = elastické úlohy ve verzi Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh. Tato verze Preview je omezená, takže se obraťte SaaSFeedback@microsoft.com na související otázky nebo podporu.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Získat lístky Wingtip Tickets SaaS Database Code a Script aplikace pro více tenantů

V úložišti [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) na GitHubu jsou k dispozici skripty SaaS s více klienty a zdrojový kód aplikace. Přečtěte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů Wingtip lístky SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Vytvoření databáze agenta úloh a nového agenta úloh

Tento kurz vyžaduje, abyste k vytvoření databáze agenta úloh a agenta úloh použili PowerShell. Podobně jako databáze MSDB, kterou používá Agent SQL, používá Agent úlohy v Azure SQL Database databázi k ukládání definic úloh, stavu úlohy a historie. Po vytvoření agenta úlohy můžete okamžitě vytvořit a monitorovat úlohy.

1. V **prostředí POWERSHELL ISE** otevřete *... \\ \\ \\Demo-SchemaManagement.ps1Správa schématu ve výukových modulech*.
2. Stisknutím klávesy **F5** spusťte skript.

Skript *Demo-SchemaManagement.ps1* volá skript *Deploy-SchemaManagement.ps1* , aby vytvořil databázi s názvem _Služba jobagent_ na serveru katalogu. Skript potom vytvoří agenta úloh a předá databázi _Služba jobagent_ jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Vytvoření úlohy, která u všech tenantů nasadí nová referenční data

#### <a name="prepare"></a>Příprava

Databáze každého tenanta obsahuje sadu typů míst v tabulce **VenueTypes** . Každý typ místa určuje druh událostí, které mohou být hostovány na místo. Tyto typy míst odpovídají obrázkům na pozadí, které vidíte v aplikaci události tenanta.  V tomto cvičení nasadíte aktualizaci do všech databází a přidáte do nich dva další typy míst: *motocykl* a svrchovaný *klub*.

Nejprve zkontrolujte typy míst, které jsou součástí každé databáze tenanta. Připojte se k jedné z databází tenantů v SQL Server Management Studio (SSMS) a prozkoumejte tabulku VenueTypes.  Tuto tabulku můžete také dotazovat v editoru dotazů v Azure Portal, ke kterému se dostanete ze stránky databáze.

1. Otevřete SSMS a připojte se k tenantovi serveru: *tenants1-DPT- &lt; user &gt; . Database.Windows.NET*
1. Pokud si chcete ověřit, že  **se** *motocykly na motocyklu* a v tuto chvíli nezahrnují, přejděte k databázi *contosoconcerthall* na serveru *tenants1-DPT- &lt; User &gt;* a Dotazujte se na tabulku *VenueTypes* .



#### <a name="steps"></a>Postup

Nyní vytvoříte úlohu pro aktualizaci tabulky **VenueTypes** v každé databázi tenantů přidáním dvou nových typů místa.

K vytvoření nové úlohy použijete sadu uložených procedur systému úloh, které byly vytvořeny v databázi _Služba jobagent_ . Uložené procedury byly vytvořeny při vytvoření agenta úlohy.

1. V SSMS se připojte k serveru tenanta: tenants1-MT- &lt; User &gt; . Database.Windows.NET

2. Přejděte do databáze *tenants1* .

3. Dotaz na tabulku *VenueTypes* , aby se ověřilo, že se v seznamu výsledků nacházejí *motocykly* a moje *klubu* ještě neexistují.

4. Připojte se k serveru katalogu, který je *Catalog-MT- &lt; user &gt; . Database.Windows.NET*.

5. Připojte se k databázi _Služba jobagent_ na serveru katalogu.

6. V SSMS otevřete soubor *... \\ Výukové moduly \\ : Správa schématu \\ DeployReferenceData. SQL*.

7. Upravte příkaz: Set @User = &lt; User &gt; a nahraďte hodnotu uživatele, která se používá při nasazení aplikace SaaS multi-tenant Database aplikace Wingtip lístky.

8. Stisknutím klávesy **F5** spusťte skript.

#### <a name="observe"></a>Pozor

Ve skriptu *DeployReferenceData. SQL* Sledujte následující položky:

- **SP \_ Přidat \_ cílovou \_ skupinu** vytvoří název cílové skupiny *DemoServerGroup* a do skupiny přidá cílové členy.

- **aktualizace \_ SP \_ Přidat \_ \_ člen cílové skupiny** přidá tyto položky:
    - Typ cílového člena *serveru* .
        - Jedná se o server *tenants1-MT &lt; &gt;* , který obsahuje databáze tenantů.
        - Zahrnutí serveru zahrnuje databáze tenantů, které existují v době, kdy se úloha spustí.
    - Typ cílového člena *databáze* pro šablonu databáze (*basetenantdb*), který se nachází v *katalogu-MT- &lt; User &gt;* Server,
    - Cílový členský typ *databáze* pro zahrnutí databáze *adhocreporting* , která se používá v pozdějším kurzu.

- **SP \_ Add \_ Job** vytvoří úlohu s názvem *nasazení referenčních dat*.

- **SP \_ Add \_ jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL, který aktualizuje referenční tabulku VenueTypes.

- Zbývající pohledy ve skriptu zobrazují existující objekty a monitorují provádění úlohy. Pomocí těchto dotazů můžete zkontrolovat hodnotu stavu ve sloupci **životní cyklus** a zjistit, kdy se úloha dokončila. Úloha aktualizuje databázi tenantů a aktualizuje dvě další databáze, které obsahují referenční tabulku.

V SSMS přejděte do databáze tenanta na serveru *tenants1-MT- &lt; User &gt;* . Dotaz na tabulku *VenueTypes* , aby se ověřilo, že se do tabulky přidaly *motocykly* a moje *kluby klubu* . Celkový počet typů místa konání by měl být zvýšen o dva.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Vytvoření úlohy pro správu indexu referenční tabulky

Toto cvičení vytvoří úlohu pro opětovné sestavení indexu v primárním klíči referenční tabulky ve všech databázích tenantů. Opětovné sestavení indexu je typickou operací správy databáze, kterou může správce spustit po načtení velkého množství dat, aby se zlepšil výkon.

1. V SSMS se připojte k databázi _Služba jobagent_ v *katalogu – MT- &lt; User &gt; . Database.Windows.NET* Server.

2. V SSMS otevřete *... \\ Výukové moduly \\ : Správa schématu \\ OnlineReindex. SQL*.

3. Stisknutím klávesy **F5** spusťte skript.

#### <a name="observe"></a>Pozor

Ve skriptu *OnlineReindex. SQL* Sledujte následující položky:

* **SP \_ Add \_ Job** vytvoří novou úlohu s názvem *online reindexation PK \_ \_ VenueTyp \_ \_ 265E44FD7FD4C885*.

* **SP \_ Add \_ jobstep** vytvoří krok úlohy obsahující text příkazu T-SQL, který aktualizuje index.

* Zbývající zobrazení ve spuštění úlohy monitoru skriptu. Pomocí těchto dotazů můžete zkontrolovat hodnotu stavu ve sloupci **životní cyklus** a zjistit, kdy byla úloha úspěšně dokončena u všech členů cílové skupiny.

## <a name="additional-resources"></a>Další zdroje informací

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Správa cloudových databází s horizontálním navýšením kapacity](./elastic-jobs-overview.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření agenta úloh pro spouštění úloh T-SQL napříč více databázemi
> * Aktualizace referenčních dat ve všech databázích tenanta
> * Vytvořit index tabulky ve všech databázích tenantů

Potom si v [kurzu vytváření sestav ad hoc](./saas-multitenantdb-adhoc-reporting.md) Prozkoumejte spouštění distribuovaných dotazů napříč databázemi klientů.