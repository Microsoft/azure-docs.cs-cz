---
title: "Správa schématu Azure SQL Database v aplikaci s více tenanty | Dokumentace Microsoftu"
description: "Správa schématu pro více tenantů v aplikaci s více tenanty využívající službu Azure SQL Database"
keywords: kurz k sql database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 474b7842c8295be0dd30c2c03b92dc68ce40630d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Správa schématu v aplikaci SaaS, která používá horizontálně dělené víceklientské databáze SQL

V tomto kurzu prověří výzvy zachování firemního vozového databází v aplikace Software jako služba (SaaS). Řešení je ukázán pro ventilační na změny schématu napříč firemního vozového databází.

Podobně jako všechny aplikace aplikace SaaS lístky Wingtip bude v průběhu času vyvíjejí a bude vyžadovat změny databáze. Změny může mít vliv na data schématu nebo odkaz, nebo použít úlohy údržby databáze. S aplikací SaaS pomocí databáze za vzor klienta musí být změny koordinované přes potenciálně masivní firemního vozového klienta databází. Kromě toho musí obsahovat tyto změny do databáze zřizování Ujistěte se, že jsou zahrnuty do nových databází při jejich vytvoření.

#### <a name="two-scenarios"></a>Dva scénáře

V tomto kurzu zde popsány v následujících dvou scénářích:
- Nasaďte aktualizace dat odkaz pro všechny klienty.
- Znovu sestavte index pro tabulku, která obsahuje referenční data.

[Elastické úlohy](sql-database-elastic-jobs-overview.md) funkce databáze SQL Azure se používá k provedení těchto operací mezi databázemi klienta. Úlohy také v databázi 'šablony' klienta fungovat. Ve Wingtip lístky ukázkovou aplikaci Tato šablona databáze zkopíruje zřídit novou databázi klienta.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte úlohy agenta.
> * Spuštění dotazu T-SQL u několika databází klienta.
> * Aktualizujte referenční data v databázích všechny klienta.
> * Vytvoření indexu pro tabulku v všechny databáze klienta.

## <a name="prerequisites"></a>Požadavky

- Adresář Wingtip lístků, které musí být nasazený již databáze víceklientské aplikace:
    - Pokyny najdete v tématu první kurz, který představuje databáze víceklientské aplikace SaaS lístky Wingtip:<br />[Nasazení a prozkoumejte horizontálně dělené víceklientské aplikace, která používá Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - Spuštění procesu nasazení pro méně než pět minut.
    - Musíte mít *horizontálně dělené víceklientské* verzi Wingtip nainstalována. Verze pro *samostatné* a *databáze za klienta* nepodporují v tomto kurzu.

- Musí být nainstalována nejnovější verze systému SQL Server Management Studio (SSMS). [Stažení a instalace aplikace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Musí být nainstalovaný Azure PowerShell. Podrobnosti najdete v tématu [Začínáme s Azure Powershellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Tento kurz používá funkce služby Azure SQL Database, které jsou v omezené preview ([úlohy elastické databáze](sql-database-elastic-database-client-library.md)). Pokud chcete provést v tomto kurzu, zadejte svoje ID předplatného pro  *SaaSFeedback@microsoft.com*  s předmětem = elastické úlohy Preview. Jakmile dostanete potvrzení o aktivaci vašeho předplatného, [stáhněte a nainstalujte si nejnovější předběžnou verzi rutin úloh](https://github.com/jaredmoo/azure-powershell/releases). Tato předběžná verze je omezená, takže obraťte se na  *SaaSFeedback@microsoft.com*  pro dotazy související s ani nepodporuje.

## <a name="introduction-to-saas-schema-management-patterns"></a>Úvod k modelům správy schématu SaaS

Model databáze. horizontálně dělené víceklientské použít v této ukázce povoluje klientům databázi tak, aby obsahovala jeden nebo více klientů. Tato ukázka prozkoumá potenciálně použít kombinaci mnoho klienta a jedna klienta databáze, povolení *hybridní* modelu správy klienta. Správa změn tyto databáze může být složité. [Elastické úlohy](sql-database-elastic-jobs-overview.md) usnadňuje správu velkého počtu databáze a. Úlohy umožňují bezpečně a spolehlivě spuštění skriptů jazyka Transact-SQL jako úloh pro skupinu databází klienta. Úkoly jsou nezávislé na vstupu nebo interakci uživatelů. Tato metoda slouží k nasazení změny schématu nebo běžné referenční data v rámci všech klientů v aplikaci. Elastické úlohy lze také udržovat zlaté šablony kopii databáze. Šablona se používá k vytvoření nové klienty, vždy zajistíte nejnovější schéma a referenční data jsou používány.

![obrazovka](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastic Jobs verze Limited Preview

Není k dispozici nová verze elastické úlohy, který je teď integrované funkce databáze SQL Azure. Tato nová verze služby Elastic Jobs je v současnosti ve verzi Limited Preview. Omezené náhled aktuálně podporuje vytváření úlohy agenta a T-SQL, vytvářet a spravovat úlohy pomocí prostředí PowerShell.
> [!NOTE] 
> Tento kurz používá funkce služby SQL Database, které jsou v omezené preview (úlohy elastické databáze). Pokud chcete provést v tomto kurzu, zadejte svoje ID předplatného pro SaaSFeedback@microsoft.com s předmětem = elastické úlohy Preview. Jakmile se zobrazí potvrzení, že vaše předplatné je povoleno, stáhněte a nainstalujte rutiny nejnovější úlohy předběžné verze. Tato předběžná verze je omezená, takže obraťte se na SaaSFeedback@microsoft.com pro dotazy související s ani nepodporuje.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Zdrojový kód Wingtip lístky SaaS víceklientské databáze aplikace a skripty

Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) úložišti na Githubu. Najdete v článku [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS. 

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Vytvoření úlohy agenta, databáze a nové úlohy agenta

Tento kurz vyžaduje pomocí prostředí PowerShell k vytvoření databáze agenta úlohy a úlohy agenta. Jako databázi MSDB používá Agent serveru SQL používá úlohy agenta Azure SQL database k uložení definice úlohy, stav úlohy a historie. Po vytvoření úlohy agenta, můžete vytvořit a monitorujte úlohy, okamžitě.

1. V **prostředí PowerShell ISE**, otevřete *... \\Učení moduly\\Správa schématu\\ukázku SchemaManagement.ps1*.
2. Stisknutím klávesy **F5** spusťte skript.

*Ukázku SchemaManagement.ps1* skript volání *nasadit SchemaManagement.ps1* skript pro vytvoření databáze s názvem _jobagent_ na serveru katalogu. Skript potom vytvoří úlohu agenta předávání _jobagent_ databáze jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Vytvoření úlohy, která u všech tenantů nasadí nová referenční data

#### <a name="prepare"></a>Příprava

Zahrnuje sadu typy místo v databázi každého klienta **VenueTypes** tabulky. Každý typ místo definuje typ události, které může být hostovaný na místo. Tyto typy místo odpovídají obrázků na pozadí, které se zobrazí v aplikaci klienta události.  V tomto cvičení nasadit aktualizace pro všechny databáze přidat dva typy další místo: *Motorky Racing* a *křížovou kartou plaveckých*. 

Přečtěte si nejprve, místo typy součástí každou databázi klienta. Připojit k jedné z klienta databází v SQL Server Management Studio (SSMS) a zkontrolujte tabulku VenueTypes.  Můžete taky zadat dotaz této tabulky v editoru dotazů na portálu Azure k němu přistupovat z stránky databáze. 

1. Otevřete aplikaci SSMS a připojení k serveru klienta: *tenants1-dpt -&lt;uživatele&gt;. database.windows.net*
1. Zkontrolujte, že *Motorky Racing* a *křížovou kartou plaveckých* **nejsou** momentálně zahrnutá, vyhledejte *contosoconcerthall* databáze na *tenants1-dpt -&lt;uživatele&gt;*  serveru a dotaz *VenueTypes* tabulky.



#### <a name="steps"></a>Kroky

Teď vytvoříte úlohu, která aktualizuje **VenueTypes** tabulky v databázi jednotlivých klientů, přidáním dva nové typy místo.

Pokud chcete vytvořit novou úlohu, použijete sadu úloh systémové uložené procedury, které byly vytvořeny _jobagent_ databáze. Uložené procedury byly vytvořeny, když byla vytvořena úloha agenta.

1. V aplikaci SSMS, připojení k serveru klienta: tenants1-mt -&lt;uživatele&gt;. database.windows.net

2. Vyhledejte *tenants1* databáze.

3. Dotaz *VenueTypes* tabulka, která se potvrďte, že *Motorky Racing* a *křížovou kartou plaveckých* nejsou ještě v seznamu výsledků.

4. Připojení k serveru katalogu, což je *katalogu-mt -&lt;uživatele&gt;. database.windows.net*.

5. Připojení k _jobagent_ databáze na serveru katalogu.

6. V aplikaci SSMS, otevřete soubor *... \\Učení moduly\\Správa schématu\\DeployReferenceData.sql*.

7. Upravit příkaz: nastavte @User = &lt;uživatele&gt; a nahraďte hodnotu uživatele použít při nasazení aplikace Wingtip lístky SaaS víceklientské databáze.

8. Stisknutím klávesy **F5** spusťte skript.

#### <a name="observe"></a>Pozorovat

Zkontrolujte následující položky *DeployReferenceData.sql* skriptu:

- **SP\_přidat\_cíl\_skupiny** vytvoří cílová skupina *DemoServerGroup*, a přidá cílový členy do skupiny.

- **SP\_přidat\_cíl\_skupiny\_člen** přidá následující položky:
    - A *server* cíle typ člena.
        - Toto je *tenants1-mt -&lt;uživatele&gt;*  serveru, který obsahuje klienty databáze.
        - Včetně serveru zahrnuje klienta databáze, které existují v době, kdy úloha spustí.
    - A *databáze* cíle typ člena pro šablonu databáze ze (*basetenantdb*), kde je umístěná *katalogu-mt –&lt;uživatele&gt;*  serveru
    - A *databáze* cíle typ člena zahrnout *adhocreporting* databázi, která se používá novější kurzu.

- **SP\_přidat\_úlohy** vytvoří úlohu názvem *referenční Data nasazení*.

- **SP\_přidat\_krok úlohy** vytvoří krok úlohy obsahující text příkazů T-SQL aktualizovat na referenční tabulku VenueTypes.

- Zbývající pohledy ve skriptu zobrazují existující objekty a monitorují provádění úlohy. Ke kontrole hodnota stavu v použijte tyto dotazy **životního cyklu** sloupec k určení, kdy má úloha dokončena. Úloha aktualizace databáze klientů a aktualizuje dva další databáze, které obsahují na referenční tabulku.

V aplikaci SSMS, přejděte do databáze klienta na *tenants1-mt –&lt;uživatele&gt;*  serveru. Dotaz *VenueTypes* tabulka, která se potvrďte, že *Motorky Racing* a *křížovou kartou plaveckých* jsou nyní přidat do tabulky. Celkový počet místo typy by měl mít zvýšila dvakrát.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Vytvoření úlohy pro správu indexu referenční tabulky

Tento postup vytvoří úlohu znovu sestavit index primární klíč tabulky odkaz na všechny databáze klienta. Opětovné sestavení indexu je operace správy typické databáze, která se může spouštět správce po načtení velké množství dat zatížení ke zlepšení výkonu.

1. V aplikaci SSMS, připojte k _jobagent_ databáze v *katalogu-mt -&lt;uživatele&gt;. database.windows.net* serveru.

2. V aplikaci SSMS, otevřete *... \\Učení moduly\\Správa schématu\\OnlineReindex.sql*.

3. Stisknutím klávesy **F5** spusťte skript.

#### <a name="observe"></a>Pozorovat

Zkontrolujte následující položky *OnlineReindex.sql* skriptu:

* **SP\_přidat\_úlohy** vytvoří novou úlohu názvem *Online přeindexování Primárníklíč\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_přidat\_krok úlohy** vytvoří krok úlohy obsahující text T-SQL příkaz k aktualizaci indexu.

* Zbývající zobrazení ve skriptu monitorování provádění úlohy. Použít tyto dotazy ke kontrole hodnota stavu v **životního cyklu** k určení, když úloha úspěšně dokončil na všechny členy skupiny cílový sloupec.

## <a name="additional-resources"></a>Další zdroje informací:

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Správa cloudových databází s horizontálním navýšením kapacity](sql-database-elastic-jobs-overview.md)
* [Vytvoření a správa databází s horizontálním navýšením kapacity](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
.
> * Vytvořit úlohy agenta ke spuštění úloh T-SQL napříč více databází
> * Aktualizace referenční data v databázích všechny klienta
> * Vytvořit index tabulky ve všech databázích tenantů

Zkuste [Ad-hoc reporting kurzu] (saas-multitenantdb-ad hoc reporting.md) a prozkoumejte spuštění distribuované dotazy pro klienta databází.

