---
title: Dotazy ad hoc pro vytváření sestav napříč více databázemi
description: Spuštění dotazů na generování sestav ad hoc napříč několika databázemi SQL Azure v příkladech víceklientské aplikace
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/30/2018
ms.openlocfilehash: 098ac343885db3e267dcefb3785f5abd55d17ee2
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441030"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-databases-azure-sql-database"></a>Spouštění analytických dotazů ad hoc napříč více databázemi (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu spustíte distribuované dotazy napříč celou sadou databází tenanta a povolíte tak interaktivní vytváření sestav ad hoc. Tyto dotazy mohou extrahovat přehledy ukryto v každodenních provozních datech aplikace Wingtip Lístks SaaS. K provedení těchto extrakcí nasadíte další analytickou databázi na Server katalogu a použijete elastický dotaz k povolení distribuovaných dotazů.


V tomto kurzu se dozvíte:

> [!div class="checklist"]
> 
> * Nasazení databáze sestav ad hoc
> * Spuštění distribuovaných dotazů napříč všemi databázemi tenantů


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasadí se aplikace SaaS pro víceklientské klienty. Nasazení za méně než pět minut najdete v tématu [nasazení a prozkoumání SaaS aplikace pro více tenantů](saas-multitenantdb-get-started-deploy.md) .
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* SQL Server Management Studio (SSMS) je nainstalován. Pokud si chcete stáhnout a nainstalovat SSMS, přečtěte si téma [stažení SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Model generování sestav ad hoc

![vzor vytváření sestav ad hoc](./media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Aplikace SaaS můžou analyzovat obrovské množství dat tenantů, která se ukládají centrálně v cloudu. Analýzy odhalí přehled o provozu a využití vaší aplikace. Tyto přehledy můžou seřizovat vývoj funkcí, vylepšení použitelnosti a další investice do vašich aplikací a služeb.

V jedné databázi s více tenanty je přístup k těmto datům jednoduchý, ale třeba v případě distribuce mezi tisícovky databází se situace komplikuje. Jedním z přístupů je použití [elastického dotazu](elastic-query-overview.md), který umožňuje dotazování napříč distribuovanou sadou databází se společným schématem. Tyto databáze je možné distribuovat v různých skupinách prostředků a předplatných. Ještě jedno společné přihlášení musí mít přístup k extrakci dat ze všech databází. Elastický dotaz používá jednu *hlavu* databáze, ve které jsou definovány externí tabulky, které zrcadlí tabulky nebo zobrazení v distribuovaných databázích (tenant). Dotazy odeslané do této hlavní databáze se kompilují a vzniká plán distribuovaného dotazu, který zajistí předávání částí dotazu potřebným tenantským databázím. Elastický dotaz používá k určení umístění všech databází tenanta mapu horizontálních oddílů v databázi katalogu. Nastavení a dotaz jsou jednoduché pomocí [jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)Standard a podporují dotazování ad hoc z nástrojů, jako je Power BI a Excel.

Díky distribuci dotazů napříč databázemi klientů poskytuje elastický dotaz okamžitý přehled o živých provozních datech. Jelikož však elastický dotaz vyžádá data z potenciálně velkého počtu databází, může být v některých případech vyšší latence než u ekvivalentních dotazů odeslaných do jediné víceklientské databáze. Nezapomeňte navrhnout dotazy pro minimalizaci vrácených dat. Elastický dotaz je často vhodný pro dotazování malých objemů dat v reálném čase, a to na rozdíl od vytváření často používaných nebo složitých analytických dotazů nebo sestav. Pokud dotazy nebudou dobře fungovat, podívejte se na [plán spuštění](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) , kde zjistíte, jaká část dotazu byla do vzdálené databáze přesunuta. A vyhodnoťte, kolik dat je vráceno. Dotazy, které vyžadují složité analytické zpracování, mohou být lépe obsluhovány uložením extrahovaných dat tenanta do databáze optimalizované pro analytické dotazy. SQL Database a Azure synapse Analytics (dříve SQL Data Warehouse) by mohli hostovat analytickou databázi.

Tento vzor analýzy je vysvětlen v [kurzu analýzy tenanta](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Získat lístky Wingtip Tickets SaaS Database Code a Script aplikace pro více tenantů

V úložišti GitHubu [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) jsou k dispozici skripty SaaS s více klienty a zdrojový kód aplikace. Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů Wingtip Tickets SaaS.

## <a name="create-ticket-sales-data"></a>Vytvořit prodejní data lístků

Pokud chcete spouštět dotazy pro zajímavější datovou sadu, vytvořte pomocí generátoru lístků data o prodeji lístku.

1. V *prostředí POWERSHELL ISE*otevřete... \\ Výukové moduly \\ provozní analýzy \\ ad hoc vytváření sestav \\ *Demo-AdhocReporting.ps1* skriptu a nastavte následující hodnoty:
   * **$DemoScenario** = 1, **vykoupit lístky pro události na všech místě**.
2. Stisknutím klávesy **F5** spusťte skript a vygenerujte prodej lístku. Po spuštění skriptu pokračujte postupem v tomto kurzu. Data lístku se dotazují v části *Run ad hoc distribuované dotazy* , takže počkejte, než se generátor lístků dokončí.

## <a name="explore-the-tenant-tables"></a>Prozkoumat tabulky tenantů 

V aplikaci databáze SaaS s více klienty se klienti ukládají do hybridního modelu správy tenanta – Pokud jsou data tenanta uložená v databázi s více klienty nebo v jedné databázi tenanta a dají se mezi nimi přesunout. Při dotazování napříč všemi databázemi tenantů je důležité, aby elastický dotaz mohl data zacházet, jako by byl součástí jedné logické databáze horizontálně dělené podle tenanta. 

Pro dosažení tohoto modelu všechny tabulky tenantů obsahují sloupec *VenueId* , který určuje, do kterého tenanta patří data. *VenueId* se vypočítává jako hodnota hash názvu místa, ale libovolný přístup se dá použít k zavedení jedinečné hodnoty pro tento sloupec. Tento přístup je podobný způsobu, jakým se klíč tenanta počítá pro použití v katalogu. Tabulky obsahující *VenueId* jsou používány elastickým dotazem k paralelizovat dotazů a jejich vložení do příslušné vzdálené databáze tenanta. To výrazně snižuje objem vrácených dat a vede ke zvýšení výkonu, zejména pokud existuje více tenantů, jejichž data jsou uložena v jednom tenantovi databáze.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Nasazení databáze používané pro ad hoc distribuované dotazy

Toto cvičení nasadí databázi *adhocreporting* . Toto je hlavní databáze, která obsahuje schéma používané pro dotazování napříč všemi databázemi tenanta. Databáze je nasazená na stávající Server katalogu, což je server, který se používá pro všechny databáze související se správou v ukázkové aplikaci.

1. Otevřít... \\ Výukové moduly \\ provozní analýzy \\ ad hoc vytváření sestav \\ *Demo-AdhocReporting.ps1* v *PowerShellu ISE* a nastavte následující hodnoty:
   * **$DemoScenario** = 2, **nasadit databázi ad hoc Analytics**.

2. Stisknutím klávesy **F5** spusťte skript a vytvořte databázi *adhocreporting* .

V další části přidáte schéma do databáze, aby bylo možné je použít ke spouštění distribuovaných dotazů.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurace hlavní databáze pro spouštění distribuovaných dotazů

Tento cvičení přidá schéma (externí definice zdroje dat a externí tabulky) do databáze generování sestav ad hoc, která umožňuje dotazování napříč všemi databázemi tenanta.

1. Otevřete SQL Server Management Studio a připojte se k databázi vytváření sestav ad hoc, kterou jste vytvořili v předchozím kroku. Název databáze je *adhocreporting*.
2. Otevřete. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ *Initialize-AdhocReportingDB. SQL* v SSMS.
3. Zkontrolujte skript SQL a Všimněte si následujícího:

   Elastický dotaz používá pro přístup ke všem databázím tenanta přihlašovací údaje v rámci databáze. Toto pověření musí být k dispozici ve všech databázích a mělo by se jim normálně udělit minimální oprávnění potřebná k povolení těchto dotazů ad hoc.

    ![vytvoření přihlašovacích údajů](./media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Když použijete databázi katalogu jako externí zdroj dat, budou se dotazy distribuovat do všech databází zaregistrovaných v katalogu při spuštění dotazu. Vzhledem k tomu, že názvy serverů jsou pro každé nasazení odlišné, tento inicializační skript Získá umístění databáze katalogu načtením aktuálního serveru (@ @servername ), ve kterém se skript spustí.

    ![vytvořit externí zdroj dat](./media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Externí tabulky, které odkazují na tabulky tenantů, jsou definovány s **distribucí = horizontálně dělené (VenueId)**. Tato funkce směruje dotaz na konkrétní *VenueId* do příslušné databáze a vylepšuje výkon pro mnoho scénářů, jak je znázorněno v další části.

    ![Vytvoření externích tabulek](./media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Místní tabulka *VenueTypes* , která je vytvořena a naplněna. Tato tabulka referenčních dat je společná ve všech databázích tenanta, takže ji můžete reprezentovat jako místní tabulka a naplnit se společnými daty. U některých dotazů to může snížit množství dat přesunutých mezi databázemi klientů a databází *adhocreporting* .

    ![vytvořit tabulku](./media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Pokud tímto způsobem zahrnete referenční tabulky, nezapomeňte aktualizovat schéma tabulky a data při každé aktualizaci databáze klienta.

4. Stisknutím klávesy **F5** spusťte skript a inicializujte databázi *adhocreporting* . 

Nyní můžete spouštět distribuované dotazy a shromažďovat přehledy napříč všemi klienty.

## <a name="run-ad-hoc-distributed-queries"></a>Spustit distribuované dotazy ad hoc

Teď, když je nastavená databáze *adhocreporting* , pokračujte a spusťte některé distribuované dotazy. Zahrňte plán spuštění pro lepší porozumění, kde se děje zpracování dotazu. 

Po kontrole plánu spuštění najeďte na ikony plánu a vyhledejte podrobnosti. 

1. V *SSMS*otevřete... \\ Výukové moduly \\ provozní analýzy \\ ad hoc vytváření sestav \\ *demo-AdhocReportingQueries. SQL*.
2. Ujistěte se, že jste připojení k databázi **adhocreporting** .
3. Vyberte nabídku **dotazu** a klikněte na **Zahrnout skutečný plán spuštění** .
4. Zvýrazněte, *která místa jsou aktuálně registrována?* dotaz a stiskněte klávesu **F5**.

   Dotaz vrátí celý seznam míst, který ilustruje, jak rychle a snadno se má dotazovat napříč všemi klienty a vracet data z každého tenanta.

   Prohlédněte si plán a podívejte se, že všechny náklady jsou vzdálený dotaz, protože jednoduše nacházíme na každou databázi tenanta a vyberete informace o místu.

   ![Vyberte * z dbo. Místa](./media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Vyberte další dotaz a stiskněte klávesu **F5**.

   Tento dotaz spojuje data z databází tenanta a místní tabulky *VenueTypes* (místní, protože se jedná o tabulku v databázi *adhocreporting* ).

   Zkontrolujte plán a podívejte se, že většina nákladů je vzdálený dotaz, protože dotazování na informace o místu každého tenanta (dbo). Místo) a pak proveďte rychlé místní spojení s místní tabulkou *VenueTypes* , aby se zobrazil popisný název.

   ![Spojit se se vzdálenými a místními daty](./media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Teď vyberte, *ve kterém dnu byly nejvíc prodávané lístky?* dotaz, a stiskněte klávesu **F5**.

   Tento dotaz provede trochu složitější spojování a agregaci. Je důležité si uvědomit, že většina zpracování je prováděna vzdáleně a znovu se vrátí pouze ty řádky, které potřebujeme, a vrátíme jenom jeden řádek pro součtový počet prodejů v rámci lístku pro každý den.

   ![query](./media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> 
> * Spouštění distribuovaných dotazů ve všech tenantských databázích
> * Nasaďte databázi ad hoc pro generování sestav a přidejte do ní schéma pro spouštění distribuovaných dotazů.

Teď Vyzkoušejte [kurz analýzy tenantů](saas-multitenantdb-tenant-analytics.md) , kde můžete prozkoumat extrakci dat do samostatné analytické databáze pro složitější zpracování analýz.

## <a name="additional-resources"></a>Další zdroje

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastický dotaz](elastic-query-overview.md)
