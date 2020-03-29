---
title: Vytváření sestav dotazů ve více databázích
description: Sestavy mezi tenanty pomocí distribuovaných dotazů.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
ms.date: 01/25/2019
ms.openlocfilehash: c863946934df9990c14e49ef1a0a82bbc55b27c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822086"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Vytváření sestav mezi klienty pomocí distribuovaných dotazů

V tomto kurzu spustíte distribuované dotazy napříč celou sadou databází klientů pro vytváření sestav. Tyto dotazy můžete extrahovat přehledy pohřben v každodenní provozní data wingtip vstupenky SaaS nájemníků. Chcete-li to provést, nasadíte další databázi sestav na server katalogu a pomocí elastického dotazu povolíte distribuované dotazy.


V tomto kurzu se dozvíte:

> [!div class="checklist"]
> 
> * Jak nasadit databázi sestav
> * Jak spustit distribuované dotazy ve všech databázích klienta
> * Jak globální zobrazení v každé databázi může umožnit efektivní dotazování mezi klienty


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:


* Wingtip Vstupenky SaaS databáze na klienta aplikace se nasadí. Pokud chcete nasadit za méně než pět minut, [přečtěte si informace o nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database SaaS Na klienta.](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Je nainstalována aplikace SQL Server Management Studio (SSMS). Informace o stažení a instalaci služby SSMS naleznete [v tématu Stažení aplikace SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Vzor vytváření sestav napříč tenanty

![vzor distribuovaného dotazu mezi tenanty](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Jednou z příležitostí s aplikacemi SaaS je využití obrovského množství dat klienta uložených v cloudu k získání přehledu o provozu a využití vaší aplikace. Tyto přehledy mohou vést vývoj funkcí, vylepšení použitelnosti a další investice do vašich aplikací a služeb.

V jedné databázi s více tenanty je přístup k těmto datům jednoduchý, ale třeba v případě distribuce mezi tisícovky databází se situace komplikuje. Jedním z přístupů je použití [elastického dotazu](sql-database-elastic-query-overview.md), který umožňuje dotazování napříč distribuovanou sadou databází s běžným schématem. Tyto databáze lze distribuovat mezi různými skupinami prostředků a předplatnými, ale je třeba sdílet společné přihlášení. Elasticquery používá databázi s jednou *hlavou,* ve které jsou definovány externí tabulky, které zrcadlí tabulky nebo zobrazení v distribuovaných (tenantských) databázích. Dotazy odeslané do této hlavní databáze se kompilují a vzniká plán distribuovaného dotazu, který zajistí předávání částí dotazu potřebným tenantským databázím. Elastický dotaz používá mapování úlomků v databázi katalogu k určení umístění všech databází klienta. Nastavení a dotaz hlavní databáze jsou jednoduché pomocí standardního [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)a podporují dotazování z nástrojů, jako je Power BI a Excel.

Distribuce dotazů napříč databázemi klienta elastický dotaz poskytuje okamžitý přehled o živých produkčních datech. Jako elastický dotaz získává data z potenciálně mnoha databází, latence dotazu může být vyšší než ekvivalentní dotazy odeslané do jedné databáze s více klienty. Návrh dotazů minimalizovat data, která je vrácena do hlavní databáze. Elastický dotaz je často nejvhodnější pro dotazování malých množství dat v reálném čase, na rozdíl od vytváření často používaných nebo složitých analytických dotazů nebo sestav. Pokud dotazy nefungují dobře, podívejte se na [plán spuštění](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) a zjistěte, která část dotazu je posunuta do vzdálené databáze a kolik dat je vráceno. Dotazy, které vyžadují komplexní agregaci nebo analytické zpracování může být lepší zpracování extrahováním dat klienta do databáze nebo datového skladu optimalizované pro analytické dotazy. Tento vzor je vysvětlen v [kurzu analýzy klienta](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat wingtip vstupenky SaaS databáze na klienta aplikační skripty

Wingtip Tickets SaaS Víceklientské databázové skripty a zdrojový kód aplikace jsou k dispozici v úložišti [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování wingtip vstupenky SaaS skripty.

## <a name="create-ticket-sales-data"></a>Vytvořit data o prodeji vstupenek

Chcete-li spustit dotazy proti zajímavější sadu dat, vytvořte data o prodeji vstupenek spuštěním generátoru lístků.

1. V *prostředí PowerShell ISE*otevřete ... \\Učící\\se moduly Provozní\\analýza\\Adhoc Reporting*Demo-AdhocReporting.ps1* skript a nastavit následující hodnotu:
   * **$DemoScenario** = 1, **Nákup vstupenek na akce na všech místech**.
2. Stisknutím **klávesy F5** spusťte skript a vygenerujte prodej vstupenek. Během spuštění skriptu pokračujte v krocích v tomto kurzu. Data lístku jsou dotazována v části *Spustit ad hoc distribuovaných dotazů,* takže počkejte na dokončení generátoru lístku.

## <a name="explore-the-global-views"></a>Prozkoumejte globální zobrazení

V wingtip tickets SaaS databáze na klienta aplikace, každý klient je uveden a databáze. Proto data obsažená v databázových tabulkách je vymezena do perspektivy jednoho klienta. Však při dotazování napříč všemi databázemi, je důležité, aby elastický dotaz můžete zacházet s daty, jako kdyby je součástí jedné logické databáze, která je řízena tenantem. 

Chcete-li simulovat tento vzor, sada 'globální' zobrazení jsou přidány do databáze klienta, které projekt ID klienta do každé z tabulek, které jsou dotazovány globálně. Například *VenueEvents* zobrazení přidá vypočítané *VenueId* do sloupců promítaných z tabulky *Události.* Podobně *venueTicketPurchases* a *VenueTickets* zobrazení přidat vypočítané *VenueId* sloupec promítá z jejich příslušných tabulek. Tato zobrazení jsou používány elastické dotazu paralelizovat dotazy a push je dolů do příslušné databáze vzdáleného klienta, když *venueId* sloupec je k dispozici. To výrazně snižuje množství dat, která je vrácena a má za následek podstatné zvýšení výkonu pro mnoho dotazů. Tato globální zobrazení byla předem vytvořena ve všech databázích klientů.

1. Otevřete SSMS a [připojte se&lt;&gt; k tenants1- USER server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Rozbalte **položku Databáze**, klepněte pravým tlačítkem myši na _contosoconcerthall_a vyberte **možnost Nový dotaz**.
1. Spusťte následující dotazy a prozkoumejte rozdíl mezi tabulkami s jedním tenantem a globálními zobrazeními:

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

V těchto zobrazeních *VenueId* je vypočítána jako hash název místo, ale jakýkoli přístup lze použít k zavedení jedinečnou hodnotu. Tento přístup je podobný způsobu, jakým je vypočítán klíč klienta pro použití v katalogu.

Chcete-li prozkoumat definici zobrazení *Místa:*

1. V **Průzkumníkovi objektů**rozbalte**zobrazení** **contosoconcerthall** > :

   ![zobrazení](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Klikněte pravým tlačítkem myši **na dbo. Místa konání**.
3. Vybrat **zobrazení skriptu jako** > **okno VYTVOŘIT do** > **nového editoru dotazů**

Skript některou z ostatních *zobrazení místo* zobrazíte, jak se přidat *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Nasazení databáze používané pro distribuované dotazy

Toto cvičení nasazuje _databázi adhocreporting._ Toto je hlavní databáze, která obsahuje schéma používané pro dotazování ve všech databázích klienta. Databáze se nasadí na existující server katalogu, což je server používaný pro všechny databáze související se správou v ukázkové aplikaci.

1. v *prostředí PowerShell ISE*, otevřít ... \\\\Výukové moduly Provozní\\analýza Adhoc Reporting\\*Demo-AdhocReporting.ps1*. 

1. Nastavte **$DemoScenario = 2**, _Nasadit databázi sestav ad hoc_.

1. Stisknutím **klávesy F5** spusťte skript a vytvořte databázi *adhocreportingu.*

V další části přidáte schéma do databáze, aby jej bylo možné použít ke spuštění distribuovaných dotazů.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurace databáze "head" pro spouštění distribuovaných dotazů

Toto cvičení přidá schéma (externí zdroj dat a externí definice tabulek) do databáze _adhocreportingu,_ aby bylo možné dotazovat ve všech databázích klienta.

1. Otevřete SQL Server Management Studio a připojte se k databázi Adhoc Reporting, kterou jste vytvořili v předchozím kroku. Název databáze je *adhocreporting*.
2. Otevřete ...\Výukové moduly\Provozní analýza\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ v SSMS.
3. Prohlédněte si skript SQL a poznamenejte si:

   Elastický dotaz používá pověření s rozsahem databáze pro přístup ke každé z databází klienta. Toto pověření musí být k dispozici ve všech databázích a obvykle by měla být udělena minimální práva potřebná k povolení těchto dotazů.

    ![vytvoření přihlašovacích údajů](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   S databází katalogu jako externí zdroj dat, dotazy jsou distribuovány do všech databází registrovaných v katalogu v době spuštění dotazu. Vzhledem k tomu, že názvy serverů se pro každé nasazení liší, získá tento skript umístění databáze katalogu z aktuálního serveru (@@servername), kde je skript spuštěn.

    ![vytvoření externího zdroje dat](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Externí tabulky, které odkazují na globální pohledy popsané v předchozí části a definované **s DISTRIBUTION = SHARDED(VenueId)**. Vzhledem k tomu, že každý *VenueId* mapuje na jednotlivé databáze, to zlepšuje výkon pro mnoho scénářů, jak je znázorněno v další části.

    ![vytvoření externích tabulek](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Místní tabulka _VenueTypes,_ který je vytvořen a naplněn. Tato tabulka referenčních dat je běžná ve všech databázích klientů, takže může být zde reprezentována jako místní tabulka a naplněna společnými daty. U některých dotazů může definice této tabulky v hlavní databázi snížit množství dat, která je třeba přesunout do hlavní databáze.

    ![vytvořit tabulku](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Pokud tímto způsobem zahrnete referenční tabulky, nezapomeňte aktualizovat schéma tabulky a data při každé aktualizaci databází klienta.

4. Stisknutím **klávesy F5** spusťte skript a inicializujte databázi *adhocreportingu.* 

Nyní můžete spouštět distribuované dotazy a shromažďovat přehledy napříč všemi tenanty!

## <a name="run-distributed-queries"></a>Spuštění distribuovaných dotazů

Teď, když je nastavena databáze *adhocreporting,* pokračujte a spusťte některé distribuované dotazy. Zahrnout plán spuštění pro lepší pochopení, kde se provádí zpracování dotazu. 

Při kontrole plánu spuštění, najeďte na ikony plánu pro podrobnosti. 

Důležité si uvědomit, je, že nastavení **DISTRIBUTION = SHARDED(VenueId)** při definování externího zdroje dat zlepšuje výkon pro mnoho scénářů. Jako každý *VenueId* mapuje na jednotlivé databáze, filtrování se snadno provádí na dálku, vrací pouze data potřebná.

1. Otevřít... \\\\Výukové moduly Provozní\\analýza Adhoc Reporting\\*Demo-AdhocReportingQueries.sql* v SSMS.
2. Ujistěte se, že jste připojeni k databázi **adhocreporting.**
3. Vyberte nabídku **Dotaz** a klikněte na **Zahrnout plán skutečného spuštění.**
4. Zvýrazněte *dotaz, která místa jsou aktuálně zaregistrována,* a stiskněte **klávesu F5**.

   Dotaz vrátí celý seznam míst konání, ilustrující, jak rychle a snadno je dotazovat se napříč všemi tenanty a vracet data z každého klienta.

   Zkontrolujte plán a uvidíte, že všechny náklady jsou ve vzdáleném dotazu. Každá databáze klienta spustí dotaz vzdáleně a vrátí informace o místě do hlavní databáze.

   ![VYBERTE * Z dbo. Místech](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Vyberte další dotaz a stiskněte **klávesu F5**.

   Tento dotaz spojuje data z databází klienta a místní *VenueTypes* tabulka (místní, jako je tabulka v databázi *adhocreporting).*

   Zkontrolujte plán a uvidíte, že většina nákladů je vzdálený dotaz. Každá databáze klienta vrátí informace o místě a provede místní spojení s místní *venuetypes* tabulky zobrazit popisný název.

   ![Připojení ke vzdáleným a místním datům](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Nyní vyberte *dotaz Na který den bylo nejvíce prodaných vstupenek* a stiskněte **klávesu F5**.

   Tento dotaz provádí trochu složitější spojení a agregaci. Většina zpracování probíhá vzdáleně.  Do hlavní databáze jsou vráceny pouze jednotlivé řádky, které obsahují denní počet vstupenek na jednotlivá místa za den.

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> 
> * Spouštění distribuovaných dotazů ve všech tenantských databázích
> * Nasaďte databázi sestav a definujte schéma potřebné ke spuštění distribuovaných dotazů.


Teď vyzkoušejte [kurz Tenant Analytics](saas-tenancy-tenant-analytics.md) a prozkoumejte extrahování dat do samostatné analytické databáze pro složitější zpracování analýzy.

## <a name="additional-resources"></a>Další zdroje

* Další [kurzy, které staví na wingtip tickets SaaS databáze na klienta aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
