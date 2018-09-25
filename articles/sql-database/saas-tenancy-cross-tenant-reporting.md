---
title: Spuštění dotazů vytváření sestav napříč několika databázemi Azure SQL | Dokumentace Microsoftu
description: Vytváření sestav pomocí služby napříč tenanty distribuovaných dotazů.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,AyoOlubeko
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 3bba0eb552338f1b436ea25306a84029d352f1f3
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055284"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Vytváření sestav pomocí služby napříč tenanty distribuovaných dotazů

V tomto kurzu jste spouštění distribuovaných dotazů ve celá sada tenanta databází pro vytváření sestav. Tyto dotazy dokážou extrahovat statistiky ukryté v běžných provozních datech SaaS aplikace Wingtip Tickets tenantů. K tomuto účelu nasazení další databázi vytváření sestav na server katalogu a použít nástroj Elastic Query umožňující distribuovaných dotazů.


V tomto kurzu se dozvíte:

> [!div class="checklist"]

> * Jak nasadit databázi vytváření sestav
> * Postup spouštění distribuovaných dotazů ve všech databázích tenantů
> * Jak globální zobrazení v každé databázi můžete povolit efektivní dotazování napříč tenanty


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:


* Nasazení aplikace Wingtip Tickets SaaS databáze na Tenanta. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání aplikace Wingtip Tickets SaaS databáze na Tenanta](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* SQL Server Management Studio (SSMS) je nainstalována. Stáhnout a nainstalovat aplikaci SSMS najdete v tématu [stáhnout SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Model vytváření sestav napříč tenanty

![Model distribuované dotazy mezi tenanty](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Jednu příležitost nabízejí aplikace SaaS je použití obrovské množství dat tenantů, které jsou uložené v cloudu a získejte přehled o provozu a využití vaší aplikace. Tyto přehledy můžete Průvodce vývoj funkcí, vylepšení použitelnosti a další investice do vašich aplikací a služeb.

V jedné databázi s více tenanty je přístup k těmto datům jednoduchý, ale třeba v případě distribuce mezi tisícovky databází se situace komplikuje. Jedním z přístupů je použít [Elastic Query](sql-database-elastic-query-overview.md), která umožňuje dotazování v distribuované sadě databází se společným schématem. Tyto databáze mohou být distribuovány na různých skupin prostředků a předplatných, ale muset sdílet společný přihlášení. Elastic Query používá jednu *head* databáze definovanými externími tabulkami, které zrcadlí tabulky a zobrazení v distribuovaných (tenantských) databázích. Dotazy odeslané do této hlavní databáze se kompilují a vzniká plán distribuovaného dotazu, který zajistí předávání částí dotazu potřebným tenantským databázím. Elastic Query používá k určení umístění všech databázích tenantů mapy horizontálních oddílů v databázi katalogu. Vytvoření i dotazování hlavní databáze se jednoduše pomocí standardní [příkazů jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)a podporují dotazování z nástrojů, jako je Power BI a Excel.

Díky distribuci dotazy napříč databázemi tenantů, poskytuje Elastic Query okamžitý přehled o aktivní produkční data. Jak elastický dotaz načte data z potenciálně velkého počtu databází, latence dotazu může být vyšší než ekvivalentní dotazy odeslané do jedné databázi s více tenanty. Navrhování dotazů minimalizovat objem dat, který je vrácen do hlavní databáze. Elastický dotaz je často nejvhodnější pro dotazování na malé množství dat v reálném čase, na rozdíl od vytváření často používaných nebo komplexních analytických dotazů nebo sestav. Pokud dotazy neprovádí dobře, podívejte se na [plán provádění](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) jaká část dotazu je posunutý dolů ke vzdálené databázi a kolik dat se vrací. Dotazy, které vyžadují komplexní agregace nebo analytické zpracování může být lepší popisovače extrahováním dat tenanta na databázi ani na datový sklad optimalizovaný pro analytické dotazy. Tento model je podrobně [kurz analýza tenanta](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace Wingtip Tickets SaaS databáze na Tenanta

Jsou k dispozici v databázi s více tenanty SaaS aplikace Wingtip Tickets skripty a zdrojový kód aplikace [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skripty.

## <a name="create-ticket-sales-data"></a>Vytvoření lístku prodejních dat

Ke spouštění dotazů na zajímavější datové sady, vytvořte spuštěním generátoru lístek prodejní data lístku.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning Modules\\Operational Analytics\\vykazování ad hoc\\*Demo-AdhocReporting.ps1* skriptu a nastavte následující hodnotu:
   * **$DemoScenario** = 1, **nákup lístků na akce na všech místech**.
2. Stisknutím klávesy **F5** ke spuštění skriptu a zvyšovaly prodej lístků. Když je spuštěný skript, pokračujte kroky v tomto kurzu. Data lístku je dotazován v průběhu *spouštění dotazů ad-hoc distribuované* části, proto počkejte generátor lístků na dokončení.

## <a name="explore-the-global-views"></a>Prozkoumejte globální zobrazení

V aplikaci Wingtip Tickets SaaS databáze na Tenanta každý tenant dostane databázi. Proto je vymezen data obsažená v tabulkách databáze pohledu jednoho tenanta. Ale při dotazování napříč všemi databázemi, je důležité, že elastický dotaz můžete zpracovávat data, pokud je součástí jedné logické databáze horizontálně dělené tenantem. 

Simulace tohoto modelu, přidají sadu "globální" zobrazení databáze tenanta tohoto projektu a ID tenanta do každé tabulky, které jsou globálně dotazovat. Například *VenueEvents* zobrazení se přidá vypočítaného *VenueId* na předpokládané ze sloupce *události* tabulky. Podobně *VenueTicketPurchases* a *VenueTickets* zobrazení přidat počítaný *VenueId* sloupec plánovaných z jejich odpovídajících tabulek. Tato zobrazení jsou používány elastický dotaz pro paralelní zpracování dotazů a nabízených oznámení je na příslušný vzdáleného klienta při databáze *VenueId* sloupec je k dispozici. To významně snižuje množství dat, která je vrácena, jejichž výsledkem podstatné zvýšení výkonu pro mnoho dotazů. Tato globální zobrazení byly předem vytvořené ve všech databázích tenantů.

1. Otevřete SSMS a [připojit k serveru tenants1 -&lt;uživatele&gt; server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Rozbalte **databází**, klikněte pravým tlačítkem na _contosoconcerthall_a vyberte **nový dotaz**.
1. Spusťte následující dotazy a prozkoumejte rozdíl mezi tabulkami jednoho tenanta a globální zobrazení:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

V těchto zobrazeních *VenueId* je vypočítán jako hodnotu hash názvem míst, ale žádné přístup může použít k zavedení jedinečnou hodnotu. Tento přístup je podobný stejným způsobem jako klíč tenanta je vypočítán pro použití v katalogu.

K prozkoumání definice *Venues* zobrazení:

1. V **Průzkumník objektů**, rozbalte **contosoconcerthall** > **zobrazení**:

   ![zobrazení](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Klikněte pravým tlačítkem na **dbo. Venues**.
3. Vyberte **skriptu zobrazení jako** > **vytvořit** > **nové okno editoru dotazů**

Skript žádnému jinému *příslušností* zobrazení zobrazíte, jak přidat *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Nasazení databáze používané k distribuovaným dotazům

V tomto cvičení nasadíte _adhocreporting_ databáze. Toto je hlavní databázi, která obsahuje schéma používané pro dotazování napříč všemi databázemi tenantů. Databáze je nasazený na existující server katalogu, který se používá pro všechny databáze v ukázkové aplikaci souvisejících se správou serveru.

1. v *prostředí PowerShell ISE*, otevřete... \\Learning Modules\\Operational Analytics\\vykazování ad hoc\\*Demo-AdhocReporting.ps1*. 

1. Nastavte **$DemoScenario = 2**, _databázi vytváření sestav Deploy Ad-hoc_.

1. Stisknutím klávesy **F5** spusťte skript a vytvořit *adhocreporting* databáze.

V další části přidáte schéma do databáze, je možné ke spouštění distribuovaných dotazů.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Nakonfigurujte databázi "hlavní" pro spouštění distribuovaných dotazů

V tomto cvičení přidá do schématu (externí zdroj dat a definice externí tabulky) _adhocreporting_ databáze, která umožňuje dotazování napříč všemi databázemi tenantů.

1. Otevřete SQL Server Management Studio a připojte se k databázi ad hoc, generování sestav, které jste vytvořili v předchozím kroku. Název databáze je *adhocreporting*.
2. Otevřete ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _inicializace AdhocReportingDB.sql_ v aplikaci SSMS.
3. Zkontrolujte skript SQL a Všimněte si:

   Elastic Query používá pro přístup k všech databázích tenantů přihlašovacích údajů s rozsahem databáze. Tyto přihlašovací údaje musí být k dispozici ve všech databázích a obvykle poskytuje minimální práva je třeba povolit tyto dotazy.

    ![vytvoření přihlašovacích údajů](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   S databází katalogu jako externí zdroj dat jsou distribuované dotazy na všechny databáze zaregistrované v katalogu v době spuštění dotazu. Jako název serveru se liší u každého nasazení, tento skript získá umístění databáze katalogu z aktuálního serveru (@@servername) ve kterých se spouští skript.

    ![Vytvoření externího zdroje dat](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Externí tabulky, které odkazují na globální zobrazení je popsáno v předchozí části a definované pomocí **distribuce = SHARDED(VenueId)**. Protože každý *VenueId* mapuje na jednu databázi, zlepší se výkon pro řadu scénářů, jak je znázorněno v následující části.

    ![Vytvoření externích tabulek](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Místní tabulky _VenueTypes_ , který se vytvoří a naplní. Tuto datovou tabulku odkaz je společné ve všech databázích tenantů, takže může být reprezentována jako místní tabulky a mají údaj vyplněný s common data. U některých dotazů s této tabulce jsou definované v hlavní databázi můžete snížit množství dat, které je třeba přesunout do hlavní databáze.

    ![Vytvoření tabulky](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Zadáte-li referenční tabulky tímto způsobem, je potřeba aktualizovat schéma tabulky a data při každé aktualizaci databáze tenantů.

4. Stisknutím klávesy **F5** spusťte skript a inicializovat *adhocreporting* databáze. 

Můžete teď spouštění distribuovaných dotazů a shromažďování přehledů ve všech tenantech!

## <a name="run-distributed-queries"></a>Spouštění distribuovaných dotazů

Teď, když *adhocreporting* databáze je nastaven, pokračujte a spustit některé distribuovaných dotazů. Zahrnout plán provádění lépe pochopit, kde se děje zpracování dotazů. 

Při kontrole plánu spuštění, najeďte myší plán ikony podrobnosti. 

Je důležité si uvědomit, že nastavení **distribuce = SHARDED(VenueId)** když je definována externí zdroj dat zlepšuje výkon pro řadu scénářů. Protože každému *VenueId* mapuje na jednu databázi, filtrování je snadno provést vzdáleně, vrací pouze data potřebná.

1. Otevřete... \\Learning Modules\\Operational Analytics\\vykazování ad hoc\\*Demo-AdhocReportingQueries.sql* v aplikaci SSMS.
2. Zkontrolujte, že se připojení k **adhocreporting** databáze.
3. Vyberte **dotazu** nabídky a klikněte na tlačítko **patří skutečné Execution Plan**
4. Zvýrazněte *místa, které jsou aktuálně registrované?* dotazu a stiskněte klávesu **F5**.

   Dotaz vrátí seznam celý míst, ilustrující jak rychlé a snadné je dotazování všech tenantů a vrátit data z každého tenanta.

   Kontrola plánu a zjistit, že je veškeré náklady v vzdálený dotaz. Každá databáze tenanta vzdáleně spouští dotaz a vrátí informace o jeho místní příslušností k hlavní databázi.

   ![Vybrat * z dbo. Místa](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Vyberte další dotaz a stiskněte klávesu **F5**.

   Tento dotaz připojí data z databáze tenantů a místní *VenueTypes* tabulky (místní, protože je tabulka *adhocreporting* databáze).

   Kontrola plánu a podívejte se, že většina nákladů je vzdálený dotaz. Každá databáze tenanta vrátí informace o jeho míst a provádí místní síti pomocí místní *VenueTypes* tabulka, která se zobrazí popisný název.

   ![Připojte se k vzdálené a místní data](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Teď vyberte *den, kdy nejvíce lístky prodalo?* dotazu a stiskněte klávesu **F5**.

   Tento dotaz nepodporuje o něco složitější spojování a agregaci. Většina zpracovává vyvolá vzdáleně.  Pouze jeden řádky obsahující každé místo denní lístek prodej počet za den, jsou vráceny do hlavní databáze.

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Spouštění distribuovaných dotazů ve všech tenantských databázích
> * Nasazení databáze vytváření sestav a definovat schéma potřebné ke spouštění distribuovaných dotazů.


Teď si vyzkoušejte [kurz analýza Tenanta](saas-tenancy-tenant-analytics.md) zkoumání extrahování dat do samostatné analytické databáze pro zpracování složitější analýzy.

## <a name="additional-resources"></a>Další zdroje informací:

* Další [kurzy, které vycházejí z aplikace Wingtip Tickets SaaS databáze na Tenanta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
