---
title: Spuštění sestav dotazy napříč více databází Azure SQL | Microsoft Docs
description: Vytváření sestav pomocí cross klienta distribuované dotazy.
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: articles
ms.date: 04/01/2018
ms.author: billgib
ms.reviewer: sstein; AyoOlubeko
ms.openlocfilehash: 9ea308cb933948d22c7b9b14b031b9fa15af9c88
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Vytváření sestav pomocí cross klienta distribuované dotazy

V tomto kurzu spustíte distribuované dotazy napříč celou sadu klienta databází pro vytváření sestav. Tyto dotazy můžete extrahovat Statistika schovaný v každodenní provozních dat klientů Wingtip lístky SaaS. K tomuto účelu nasazení databázi další generování sestav na server katalogu a můžete povolit distribuované dotazy pomocí elastické dotazu.


V tomto kurzu se dozvíte:

> [!div class="checklist"]

> * Postup nasazení databáze služby generování sestav
> * Postup spuštění distribuovaných dotazů mezi všechny databáze klienta
> * Jak globální zobrazení v každé databázi můžete povolit efektivní dotazování mezi klienty


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:


* Nasazení aplikace Wingtip lístky SaaS databáze za klienta. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s aplikací Wingtip lístky SaaS databáze za klienta](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* SQL Server Management Studio (SSMS) je nainstalována. Chcete-li stáhnout a nainstalovat aplikace SSMS, přečtěte si téma [stáhnout SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Generování sestav vzor mezi klienta

![vzor distribuovaného dotazu mezi klienta](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Jednu možnost s aplikacemi SaaS, je použití velká množství dat klienta uložená v cloudu a získáte přehled o operaci a využití vaší aplikace. Funkce vývoj, vylepšení použitelnost a další investice do vaší aplikace a služby, můžete tyto přehledy průvodce.

V jedné databázi s více tenanty je přístup k těmto datům jednoduchý, ale třeba v případě distribuce mezi tisícovky databází se situace komplikuje. Jeden z přístupů je použít [elastické dotazu](sql-database-elastic-query-overview.md), což umožňuje dotazování na distribuovanou sadu databází s společné schéma. Tyto databáze mohou být distribuovány na různé skupiny prostředků a předplatná, ale potřeba sdílet běžné přihlášení. Elastické dotaz používá jeden *head* databáze, ve kterém jsou definovány externí tabulky, zrcadlení tabulky a zobrazení v databázích distribuované (klientů). Dotazy odeslané do této hlavní databáze se kompilují a vzniká plán distribuovaného dotazu, který zajistí předávání částí dotazu potřebným tenantským databázím. Elastické dotaz používá k určení umístění všech databází klienta mapy horizontálního oddílu v databázi katalogu. Instalační program a dotaz head databáze jsou jednoduché, pomocí standardní [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)a podporují dotazu z nástroje, například Power BI a Excel.

Elastické dotazu distribucí dotazy mezi databázemi klienta, poskytuje okamžitý přehled o provozu provozními daty. Jako elastické dotaz získává data ze potenciálně velký počet databází, může být vyšší než ekvivalentní dotazy odeslané na jedné databáze víceklientské latence dotazu. Návrh dotazy minimalizovat objem dat, který je vrácen do hlavního databáze. Elastické dotazu je často nejvhodnější pro dotazy malých objemů dat v reálném čase a sestavování často používané nebo komplexní analytické dotazy nebo sestavy. Pokud dotazy neprovádí dobře, podívejte se na [plán spuštění](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) jaká část dotazu vložena do vzdálené databáze a kolik dat se vrací. Dotazy, které vyžadují komplexní agregaci nebo analytického zpracování může být lepší popisovače extrahování dat klienta do databáze nebo data warehouse optimalizované pro analytické dotazy. Tento vzor je podrobně popsaný [kurzu analýza klienta](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat aplikační skripty Wingtip lístky SaaS databáze za klienta

Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS.

## <a name="create-ticket-sales-data"></a>Vytvořit data prodeje lístků

Ke spouštění dotazů na zajímavějšího datové sady, vytvořte spuštěním lístku generátor prodejní data lístku.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning moduly\\provozní Analytics\\vykazování ad hoc\\*ukázku AdhocReporting.ps1* skript a nastavte následující hodnotu:
   * **$DemoScenario** = 1, **zakoupit lístky pro události na všechna místa**.
2. Stiskněte klávesu **F5** generovat prodeje lístků a spusťte skript. Je spuštěn skript, pokračujte kroky v tomto kurzu. Data lístku je dotazován v *spouštění dotazů ad-hoc distribuované* část, proto počkejte generátoru lístek pro dokončení.

## <a name="explore-the-global-views"></a>Prozkoumejte globální zobrazení

V aplikaci Wingtip lístky SaaS databáze za klienta každý klient je zadána databáze. Proto data obsažená v tabulkách databáze je vymezen na perspektivu jednoho klienta. Ale při dotazování mezi všechny databáze, je důležité, aby elastické dotazu lze považovat data, pokud je součástí jedné logické databáze horizontálně dělené klientem. 

K simulaci tento vzor, jsou sadu 'globální' zobrazení přidat do databáze klienta daného projektu ID klienta do každé z tabulek, které jsou předmětem dotazování globálně. Například *VenueEvents* zobrazení přidá počítaný *VenueId* na sloupce ze k projekci *události* tabulky. Podobně *VenueTicketPurchases* a *VenueTickets* zobrazení přidat počítaný *VenueId* sloupec projektovat z jejich odpovídajících tabulek. Tato zobrazení jsou používány elastické dotazu učinit paralelní dotazy a nabízená dolů odpovídající vzdáleného klienta je při zpracování databáze *VenueId* sloupec je k dispozici. Tím se výrazně snižuje množství dat, která je vrácena a výsledkem podstatné zvýšení výkonu pro mnoho dotazů. Tato globální zobrazení byly předem vytvořené v všechny databáze klienta.

1. Otevřete aplikaci SSMS a [připojit k tenants1 -&lt;uživatele&gt; server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Rozbalte položku **databáze**, klikněte pravým tlačítkem na _contosoconcerthall_a vyberte **nový dotaz**.
1. Spusťte následující dotazy a prozkoumejte rozdíl mezi tabulkami jednoho klienta a globální zobrazení:

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

V těchto zobrazeních *VenueId* se vypočítá jako hodnota hash místo názvu, ale žádné přístup může znamenat jedinečnou hodnotu. Tento přístup je podobným způsobem, který je počítaný klíč klienta pro použití v katalogu.

K prozkoumání definice *místa* zobrazení:

1. V **Průzkumník objektů**, rozbalte položku **contosoconcerthall** > **zobrazení**:

   ![zobrazení](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Klikněte pravým tlačítkem na **dbo. Místa**.
3. Vyberte **skript zobrazení jako** > **vytvořit** > **nové okno editoru dotazů**

Skript žádnému jinému *místo* zobrazení pro zobrazení, jak přidat *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Nasazení databáze používá pro distribuované dotazy

Toto cvičení nasadí _adhocreporting_ databáze. Toto je head databáze, která obsahuje schéma použitého k dotazování mezi všechny databáze klienta. Databáze je nasazený na existující server katalogu, které je server používá pro všechny databáze v ukázkové aplikace týkajících se správy.

1. v *prostředí PowerShell ISE*, otevřete... \\Učení moduly\\provozní Analytics\\vykazování ad hoc\\*ukázku AdhocReporting.ps1*. 

1. Nastavit **$DemoScenario = 2**, _databázi sestav nasadit Ad-hoc_.

1. Stiskněte klávesu **F5** spusťte skript a vytvořit *adhocreporting* databáze.

V další části přidáte schématu do databáze, může sloužit ke spuštění distribuovaných dotazů.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfiguraci databáze, head, pro spuštění distribuovaných dotazů

Tento postup přidá schématu (externí zdroj dat a externí tabulky definice) _adhocreporting_ databáze umožňující dotazování mezi všechny databáze klienta.

1. Otevřete SQL Server Management Studio a připojte k databázi vykazování ad hoc, kterou jste vytvořili v předchozím kroku. Název databáze je *adhocreporting*.
2. Otevřete ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _inicializovat AdhocReportingDB.sql_ v aplikaci SSMS.
3. Zkontrolujte skript SQL a Poznámka:

   Elastické dotazu pomocí přihlašovacích údajů platných pro databázi přístup ke každé databáze klienta. Tato pověření musí být k dispozici ve všech databází a by měl obvykle být uděleno minimální práva potřebná k povolení těchto dotazů.

    ![Vytvoření pověření](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   S databází katalogu jako zdroj externích dat jsou dotazy distribuovány do všech databází, které jsou zaregistrovány v katalogu v době spuštění dotazu. Jako název serveru se liší pro každé nasazení, získá tento skript umístění databáze katalogu z aktuálního serveru (@@servername) němž se skript spustí.

    ![Vytvoření externího zdroje dat](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Externí tabulky, které odkazují na globální zobrazení popsané v předchozí části a definovaná pomocí **distribuční = SHARDED(VenueId)**. Protože každý *VenueId* mapuje jedné databáze, to zvyšuje výkon pro mnoho scénářů, jak je znázorněno v následujícím oddílu.

    ![Vytvoření externí tabulky](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Místní tabulky _VenueTypes_ , je vytvořeny a naplněny. Tuto referenční tabulku dat je běžné v všechny klienta databáze, tak může být reprezentován jako na místní tabulku a vyplněná běžné daty. Pro některé dotazy s této tabulky definované v databázi head může snížit množství dat, který potřebujete přesunout do hlavního databáze.

    ![Vytvoření tabulky](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Pokud zahrnete referenční tabulky tímto způsobem, nezapomeňte aktualizovat schéma tabulky a data při každé aktualizaci databáze klienta.

4. Stiskněte klávesu **F5** inicializaci a spuštění skriptu *adhocreporting* databáze. 

Nyní můžete spustit distribuované dotazy a shromažďovat statistiky napříč všichni klienti!

## <a name="run-distributed-queries"></a>Spuštění distribuovaných dotazů

Teď, když *adhocreporting* databáze je nastavit, aby ihned začít a spustit některé distribuované dotazy. Zahrnout plán spuštění lépe porozumět tomu, kde se zpracování dotazu děje. 

Při kontrole plán spuštění, pozastavte ukazatel myši nad ikony plán podrobnosti. 

Je důležité si uvědomit, že nastavení **distribuční = SHARDED(VenueId)** zvyšuje výkon pro mnoho scénářů, kdy externí zdroj dat je definován. Protože každému *VenueId* mapuje jedné databáze, filtrování je snadno provést vzdáleně, vrací pouze data potřebná.

1. Otevřete... \\Učení moduly\\provozní Analytics\\vykazování ad hoc\\*ukázku AdhocReportingQueries.sql* v aplikaci SSMS.
2. Zkontrolujte připojení k **adhocreporting** databáze.
3. Vyberte **dotazu** nabídky a klikněte na tlačítko **zahrnují skutečné naplánovat spuštění**
4. Zvýrazněte *místa, které jsou aktuálně registrované?* dotazu a stiskněte klávesu **F5**.

   Dotaz vrátí seznamu celý místo ilustrující způsob rychlé a snadné je k dotazování mezi všechny klienty a vrátit data z každého klienta.

   Zkontrolujte plán a zjistíte, že náklady v vzdálený dotaz. Každá databáze klienta vzdáleně provede daný dotaz a vrátí informace o jeho místo k databázi head.

   ![Vybrat * z dbo. Místa](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Vyberte další dotaz a stiskněte klávesu **F5**.

   Tento dotaz připojí data z databáze klienta a místní *VenueTypes* tabulky (místní počítač, protože je tabulka *adhocreporting* databáze).

   Zkontrolujte plán a zjistíte, že většina náklady na vzdálený dotaz. Vrátí informace o jeho místo každou databázi klienta a provede místní spojení s místní *VenueTypes* tabulka, která se zobrazí popisný název.

   ![Připojení k vzdálené a místní data](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Nyní vybrat *den, kdy byly nejvíce lístků prodaných?* dotazu a stiskněte klávesu **F5**.

   Tento dotaz nemá trochu složitější spojování a agregaci. Většinu zpracování nastane vzdáleně.  Pouze jeden řádky, obsahující každý místo denní lístku prodej počet za den, se vrátí k databázi head.

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Spouštění distribuovaných dotazů ve všech tenantských databázích
> * Nasadit databázi vytváření sestav a definovat schéma potřebné ke spuštění distribuovaných dotazů.


Nyní zkuste [klienta Analytics kurzu](saas-tenancy-tenant-analytics.md) prozkoumat extrakce dat k databázi samostatné analytics pro zpracování složitějších analýzy.

## <a name="additional-resources"></a>Další zdroje informací:

* Další [návodů, které aplikace pro adresář Wingtip lístky SaaS databáze za klienta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
