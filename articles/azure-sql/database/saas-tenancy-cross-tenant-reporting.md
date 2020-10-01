---
title: Vytváření sestav dotazů napříč více databázemi
description: Generování sestav mezi klienty pomocí distribuovaných dotazů.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewers: ''
ms.date: 01/25/2019
ms.openlocfilehash: 03e8719b256fc758874bd7375deed0637da9447e
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91620303"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Generování sestav mezi klienty pomocí distribuovaných dotazů
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu spouštíte distribuované dotazy napříč celou sadou databází klientů pro vytváření sestav. Tyto dotazy mohou extrahovat přehledy ukryto v každodenních provozních datech klientů Wingtip Tickets SaaS. K tomu nasadíte další databázi pro vytváření sestav na Server katalogu a pomocí elastického dotazu povolte distribuované dotazy.


V tomto kurzu se dozvíte:

> [!div class="checklist"]
> 
> * Nasazení databáze sestav
> * Spuštění distribuovaných dotazů napříč všemi databázemi tenantů
> * Jak globální zobrazení v jednotlivých databázích umožňují efektivní dotazování napříč klienty


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:


* Aplikace Wingtip Tickets SaaS Database na tenanta je nasazená. Postup nasazení za méně než pět minut najdete v tématu [nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database na klienta](../../sql-database/saas-dbpertenant-get-started-deploy.md) .
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* SQL Server Management Studio (SSMS) je nainstalován. Pokud si chcete stáhnout a nainstalovat SSMS, přečtěte si téma [stažení SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Model vytváření sestav mezi klienty

![model distribuovaného dotazu mezi klienty](./media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Jednou z příležitostí k aplikacím SaaS je použití obrovského množství dat tenantů uložených v cloudu, které vám umožní získat přehled o provozu a využití vaší aplikace. Tyto přehledy můžou seřizovat vývoj funkcí, vylepšení použitelnosti a další investice do vašich aplikací a služeb.

V jedné databázi s více tenanty je přístup k těmto datům jednoduchý, ale třeba v případě distribuce mezi tisícovky databází se situace komplikuje. Jedním z přístupů je použití [elastického dotazu](elastic-query-overview.md), který umožňuje dotazování napříč distribuovanou sadou databází se společným schématem. Tyto databáze je možné distribuovat napříč různými skupinami prostředků a předplatnými, ale je potřeba, abyste nasdíleli společné přihlášení. Elastický dotaz používá jednu *hlavu* databáze, ve které jsou definovány externí tabulky, které zrcadlí tabulky nebo zobrazení v distribuovaných databázích (tenant). Dotazy odeslané do této hlavní databáze se kompilují a vzniká plán distribuovaného dotazu, který zajistí předávání částí dotazu potřebným tenantským databázím. Elastický dotaz používá k určení umístění všech databází tenanta mapu horizontálních oddílů v databázi katalogu. Instalační program a dotaz na hlavní databázi jsou jednoduché pomocí [jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)Standard a podporují dotazování z nástrojů, jako je Power BI a Excel.

Díky distribuci dotazů napříč databázemi klientů poskytuje elastický dotaz okamžitý přehled o živých provozních datech. V případě, že elastický dotaz získává data z potenciálně mnoha databází, může být latence dotazů vyšší než ekvivalentní dotazy odeslané do jediné víceklientské databáze. Navrhněte dotazy pro minimalizaci dat vrácených do hlavní databáze. Elastický dotaz je často vhodný pro dotazování malých objemů dat v reálném čase, a to na rozdíl od vytváření často používaných nebo složitých analytických dotazů nebo sestav. Pokud dotazy nefungují dobře, podívejte se na [plán spuštění](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) , kde zjistíte, jaká část dotazu je přesunutá do vzdálené databáze a kolik dat se vrací. Dotazy, které vyžadují komplexní agregaci nebo analytické zpracování, můžou být lepšími popisovači extrakcí dat tenanta do databáze nebo datového skladu optimalizovaného pro analytické dotazy. Tento vzor je vysvětlen v [kurzu analýzy tenanta](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace Wingtip Tickets SaaS Database na klientské aplikace

V úložišti GitHubu [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) jsou k dispozici skripty SaaS s více klienty a zdrojový kód aplikace. Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů Wingtip Tickets SaaS.

## <a name="create-ticket-sales-data"></a>Vytvořit prodejní data lístků

Pokud chcete spouštět dotazy pro zajímavější datovou sadu, vytvořte pomocí generátoru lístků data o prodeji lístku.

1. V *prostředí POWERSHELL ISE*otevřete... \\ Výukové moduly \\ provozní analýzy \\ ad hoc vytváření sestav \\ *Demo-AdhocReporting.ps1* skriptu a nastavení následující hodnoty:
   * **$DemoScenario** = 1, **vykoupit lístky pro události na všech místě**.
2. Stisknutím klávesy **F5** spusťte skript a vygenerujte prodej lístku. Po spuštění skriptu pokračujte postupem v tomto kurzu. Data lístku se dotazují v části *Run ad hoc distribuované dotazy* , takže počkejte, než se generátor lístků dokončí.

## <a name="explore-the-global-views"></a>Prozkoumat globální zobrazení

V rámci aplikace Wingtip Tickets SaaS Database na tenanta je každému klientovi přidělená databáze. Proto jsou data obsažená v databázových tabulkách vymezena v perspektivě jednoho tenanta. Při dotazování napříč všemi databázemi je ale důležité, aby elastický dotaz mohl data zacházet, jako by byl součástí jedné logické databáze horizontálně dělené v tenantovi. 

Chcete-li tento model simulovat, je do databáze klienta přidaná sada zobrazení typu globální, která projektuje ID tenanta do každé tabulky, na kterou se dotazuje globálně. Například zobrazení *VenueEvents* přidá vypočítané *VenueId* k sloupcům, které jsou z tabulky *events* propočítány. Podobně zobrazení *VenueTicketPurchases* a *VenueTickets* přidávají vypočítaný sloupec *VenueId* , který je propočítán z příslušných tabulek. Tyto pohledy používá elastický dotaz k paralelizovat dotazů a jejich vložení do příslušné vzdálené databáze tenanta, když je přítomen sloupec *VenueId* . To významně snižuje množství vrácených dat a vede k výraznému nárůstu výkonu mnoha dotazů. Tato globální zobrazení byla předem vytvořena ve všech databázích tenanta.

1. Otevřete SSMS a [Připojte se k &lt; &gt; serveru tenants1-User](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Rozbalte **databáze**, klikněte pravým tlačítkem na _Contosoconcerthall_a vyberte **Nový dotaz**.
1. Spuštěním následujících dotazů Prozkoumejte rozdíl mezi tabulkami s jedním klientem a globálním zobrazením:

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

V těchto zobrazeních je *VenueId* vypočítán jako hodnota hash názvu místa, ale jakýkoli přístup lze použít k zavedení jedinečné hodnoty. Tento přístup je podobný způsobu, jakým se klíč tenanta počítá pro použití v katalogu.

Kontrola *definice zobrazení míst* :

1. V **Průzkumník objektů**rozbalte zobrazení **contosoconcerthall**  >  **Views**:

   ![Snímek obrazovky zobrazuje obsah uzlu zobrazení, včetně čtyř typů místa d b o.](./media/saas-tenancy-cross-tenant-reporting/views.png)

2. Klikněte pravým tlačítkem na **dbo. Místo**.
3. Vybrat **zobrazení skriptu jako**  >  **vytvořit pro**  >  **nové okno editoru dotazů**

Pokud chcete zjistit, jak přidat *VenueId*, proveďte skript kteréhokoli *z ostatních zobrazení* místa.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Nasazení databáze používané pro distribuované dotazy

Toto cvičení nasadí databázi _adhocreporting_ . Toto je hlavní databáze, která obsahuje schéma používané pro dotazování napříč všemi databázemi tenanta. Databáze je nasazená na stávající Server katalogu, což je server, který se používá pro všechny databáze související se správou v ukázkové aplikaci.

1. v *prostředí POWERSHELL ISE*otevřete... \\ Výukové moduly \\ Operational Analytics \\Demo-AdhocReporting.ps1vytváření sestav ad hoc \\ * *. 

1. Nastavte **$DemoScenario = 2**, _Nasaďte databázi ad hoc Reporting_.

1. Stisknutím klávesy **F5** spusťte skript a vytvořte databázi *adhocreporting* .

V další části přidáte schéma do databáze, aby bylo možné je použít ke spouštění distribuovaných dotazů.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurace hlavní databáze pro spouštění distribuovaných dotazů

Tento cvičení přidá do databáze _adhocreporting_ schéma (externí definice zdroje dat a externí tabulky) a povolí dotazování napříč všemi databázemi tenantů.

1. Otevřete SQL Server Management Studio a připojte se k databázi vytváření sestav ad hoc, kterou jste vytvořili v předchozím kroku. Název databáze je *adhocreporting*.
2. Otevřete. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ _Initialize-AdhocReportingDB. SQL_ v SSMS.
3. Zkontrolujte skript SQL a Všimněte si:

   Elastický dotaz používá pro přístup ke všem databázím tenanta přihlašovací údaje v rámci databáze. Toto pověření musí být k dispozici ve všech databázích a mělo by se jim normálně udělit minimální oprávnění potřebná k povolení těchto dotazů.

    ![vytvoření přihlašovacích údajů](./media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Díky databázi katalogu jako externímu zdroji dat jsou dotazy distribuovány do všech databází zaregistrovaných v katalogu v době, kdy se dotaz spouští. Jelikož jsou názvy serverů pro každé nasazení odlišné, tento skript Získá umístění databáze katalogu z aktuálního serveru (@ @servername ), ve kterém se skript spustí.

    ![vytvořit externí zdroj dat](./media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Externí tabulky, které odkazují na globální zobrazení popsaná v předchozí části, a jsou definovány pomocí **DISTRIBUTION = horizontálně dělené (VenueId)**. Vzhledem k tomu, že každý *VenueId* mapuje na jednotlivé databáze, vylepšuje výkon mnoha scénářů, jak je znázorněno v další části.

    ![Vytvoření externích tabulek](./media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Místní tabulka _VenueTypes_ , která je vytvořena a naplněna. Tato tabulka referenčních dat je společná ve všech databázích tenanta, takže ji můžete reprezentovat jako místní tabulka a naplnit se společnými daty. U některých dotazů může tato tabulka definovaná v hlavní databázi snížit množství dat, která je potřeba přesunout do databáze Head.

    ![vytvořit tabulku](./media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Pokud tímto způsobem zahrnete referenční tabulky, nezapomeňte aktualizovat schéma tabulky a data při každé aktualizaci databáze klienta.

4. Stisknutím klávesy **F5** spusťte skript a inicializujte databázi *adhocreporting* . 

Nyní můžete spouštět distribuované dotazy a shromažďovat přehledy napříč všemi klienty.

## <a name="run-distributed-queries"></a>Spustit distribuované dotazy

Teď, když je nastavená databáze *adhocreporting* , pokračujte a spusťte některé distribuované dotazy. Zahrňte plán spuštění pro lepší porozumění, kde se děje zpracování dotazu. 

Po kontrole plánu spuštění najeďte na ikony plánu a vyhledejte podrobnosti. 

Je důležité si uvědomit, že nastavení **distribuce = horizontálně dělené (VenueId)** , když je definovaný externí zdroj dat, zlepšuje výkon pro mnoho scénářů. Vzhledem k tomu, že se každý *VenueId* mapuje na jednotlivou databázi, je filtrování snadné provést vzdáleně a vrátí pouze potřebná data.

1. Otevřít... \\ Výukové moduly \\ provozní analýzy \\ ad hoc vytváření sestav \\ *demo-ADHOCREPORTINGQUERIES. SQL* v SSMS.
2. Ujistěte se, že jste připojení k databázi **adhocreporting** .
3. Vyberte nabídku **dotazu** a klikněte na **Zahrnout skutečný plán spuštění** .
4. Zvýrazněte, *která místa jsou aktuálně registrována?* dotaz a stiskněte klávesu **F5**.

   Dotaz vrátí celý seznam míst, který ilustruje, jak rychle a snadno se dotazuje na všech klientech a vrací data z každého tenanta.

   Zkontrolujte plán a podívejte se, že jsou všechny náklady ve vzdáleném dotazu. Každá databáze tenanta spustí dotaz vzdáleně a vrátí informace o jeho místu do hlavní databáze.

   ![Vyberte * z dbo. Místa](./media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Vyberte další dotaz a stiskněte klávesu **F5**.

   Tento dotaz spojuje data z databází tenanta a místní tabulky *VenueTypes* (místní, protože se jedná o tabulku v databázi *adhocreporting* ).

   Zkontrolujte plán a podívejte se, že většina nákladů je vzdálený dotaz. Každá databáze tenanta vrátí informace o svém místu a provede místní spojení s místní tabulkou *VenueTypes* , aby se zobrazil popisný název.

   ![Spojit se se vzdálenými a místními daty](./media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Teď vyberte, *ve kterém dnu byly nejvíc prodávané lístky?* dotaz, a stiskněte klávesu **F5**.

   Tento dotaz provede trochu složitější spojování a agregaci. Většina zpracování probíhá vzdáleně.  Do hlavní databáze se vrátí jenom jeden řádek, který obsahuje denní počet měsíčních pokusů o odeslání lístků pro každý den.

   ![query](./media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> 
> * Spouštění distribuovaných dotazů ve všech tenantských databázích
> * Nasaďte databázi vytváření sestav a definujte schéma potřebné ke spouštění distribuovaných dotazů.


Teď Vyzkoušejte [kurz analýzy tenantů](saas-tenancy-tenant-analytics.md) , kde můžete prozkoumat extrakci dat do samostatné analytické databáze pro složitější zpracování analýz.

## <a name="additional-resources"></a>Další zdroje informací

* Další [kurzy, které se sestavují na základě aplikace Wingtip Tickets SaaS Database na klienta](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastický dotaz](elastic-query-overview.md)
