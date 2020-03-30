---
title: Dotazy na vytváření sestav ad hoc ve více databázích
description: Spouštět dotazy ad hoc vytváření sestav napříč více databázemi SQL v příkladu víceklientské aplikace.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 10/30/2018
ms.openlocfilehash: c0d1829c52041446b4feb43d8af262265e2680fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822174"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Spouštění ad hoc analytických dotazů ve více databázích Azure SQL

V tomto kurzu spustíte distribuované dotazy napříč celou sadou databází klientů, abyste povolili interaktivní vytváření sestav ad hoc. Tyto dotazy mohou extrahovat přehledy pohřbené v každodenních provozních datech aplikace Wingtip Tickets SaaS. Chcete-li provést tyto extrakce, nasadit další analytickou databázi na server katalogu a použít elastický dotaz povolit distribuované dotazy.


V tomto kurzu se dozvíte:

> [!div class="checklist"]
> 
> * Jak nasadit databázi sestav ad hoc
> * Jak spustit distribuované dotazy ve všech databázích klienta


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Wingtip Vstupenky SaaS Víceklientdatabáze aplikace je nasazena. Informace o nasazení za méně než pět minut najdete v [tématu Nasazení a prozkoumání aplikace Wingtip Tickets SaaS Multi-tenant Database](saas-multitenantdb-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Je nainstalována aplikace SQL Server Management Studio (SSMS). Informace o stažení a instalaci služby SSMS naleznete [v tématu Stažení aplikace SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Vzor vykazování ad hoc

![adhoc vzor vykazování](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Aplikace SaaS můžete analyzovat obrovské množství dat klienta, který je uložen centrálně v cloudu. Analýzy odhalují přehled o fungování a používání vaší aplikace. Tyto přehledy mohou vést vývoj funkcí, vylepšení použitelnosti a další investice do vašich aplikací a služeb.

V jedné databázi s více tenanty je přístup k těmto datům jednoduchý, ale třeba v případě distribuce mezi tisícovky databází se situace komplikuje. Jedním z přístupů je použití [elastického dotazu](sql-database-elastic-query-overview.md), který umožňuje dotazování napříč distribuovanou sadou databází s běžným schématem. Tyto databáze lze distribuovat mezi různými skupinami prostředků a odběry. Přesto jedno společné přihlášení musí mít přístup k extrahování dat ze všech databází. Elasticquery používá databázi s jednou *hlavou,* ve které jsou definovány externí tabulky, které zrcadlí tabulky nebo zobrazení v distribuovaných (tenantských) databázích. Dotazy odeslané do této hlavní databáze se kompilují a vzniká plán distribuovaného dotazu, který zajistí předávání částí dotazu potřebným tenantským databázím. Elastický dotaz používá mapování úlomků v databázi katalogu k určení umístění všech databází klienta. Nastavení a dotaz jsou jednoduché pomocí [standardního Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)a podporují dotazování ad hoc z nástrojů, jako je Power BI a Excel.

Distribuce dotazů napříč databázemi klienta elastický dotaz poskytuje okamžitý přehled o živých produkčních datech. Však jako elastický dotaz natáká data z potenciálně mnoho databází, latence dotazu může být někdy vyšší než u ekvivalentních dotazů odeslaných do jedné databáze s více klienty. Nezapomeňte navrhnout dotazy minimalizovat data, která je vrácena. Elastický dotaz je často nejvhodnější pro dotazování malých množství dat v reálném čase, na rozdíl od vytváření často používaných nebo složitých analytických dotazů nebo sestav. Pokud dotazy nefungují dobře, podívejte se na [plán spuštění](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) a zjistěte, která část dotazu byla posunuta do vzdálené databáze. A posoudit, kolik dat se vrací. Dotazy, které vyžadují komplexní analytické zpracování může být lépe obsluhovat uložením extrahovaných dat klienta do databáze, která je optimalizována pro analytické dotazy. SQL Database a SQL Data Warehouse by mohly být hostitelem takové analytické databáze.

Tento vzor pro analýzu je vysvětleno v [kurzu analýzy klienta](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Získejte Wingtip Tickets SaaS Víceklientský databázový kód aplikace a skripty

Wingtip Tickets SaaS Multi-tenant databázové skripty a zdrojový kód aplikace jsou k dispozici v [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) Úložiště GitHub. Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování wingtip vstupenky SaaS skripty.

## <a name="create-ticket-sales-data"></a>Vytvořit data o prodeji vstupenek

Chcete-li spustit dotazy proti zajímavější sadu dat, vytvořte data o prodeji vstupenek spuštěním generátoru lístků.

1. V *prostředí PowerShell ISE*otevřete ... \\Učící\\se moduly Provozní\\analýza\\Adhoc Reporting*Demo-AdhocReporting.ps1* skript a nastavit následující hodnoty:
   * **$DemoScenario** = 1, **Nákup vstupenek na akce na všech místech**.
2. Stisknutím **klávesy F5** spusťte skript a vygenerujte prodej vstupenek. Během spuštění skriptu pokračujte v krocích v tomto kurzu. Data lístku jsou dotazována v části *Spustit ad hoc distribuovaných dotazů,* takže počkejte na dokončení generátoru lístku.

## <a name="explore-the-tenant-tables"></a>Prozkoumejte tabulky tenantů 

V wingtip tickets saas multitenant databáze aplikace tenantů jsou uloženy v modelu správy hybridního klienta – kde jsou data klienta uložena v databázi s více klienty nebo v databázi jednoho klienta a lze je přesunout mezi těmito dvěma. Při dotazování napříč všemi databázemi klienta je důležité, aby elastický dotaz můžete zacházet s daty, jako kdyby je součástí jedné logické databáze, která je řízena klientem. 

K dosažení tohoto vzoru všechny tabulky tenanta obsahují sloupec *VenueId,* který identifikuje, ke kterému tenantovi data patří. *VenueId* se vypočítá jako hash název místo, ale jakýkoli přístup lze použít k zavedení jedinečnou hodnotu pro tento sloupec. Tento přístup je podobný způsobu, jakým je vypočítán klíč klienta pro použití v katalogu. Tabulky obsahující *VenueId* se používají elastické dotazy paralelizovat dotazy a push je dolů do příslušné databáze vzdáleného klienta. To výrazně snižuje množství dat, která je vrácena a má za následek zvýšení výkonu, zejména v případě, že existuje více klientů, jejichž data jsou uložena v databázích jednoho klienta.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Nasazení databáze používané pro distribuované dotazy ad hoc

Toto cvičení nasazuje *databázi adhocreporting.* Toto je hlavní databáze, která obsahuje schéma používané pro dotazování ve všech databázích klienta. Databáze se nasadí na existující server katalogu, což je server používaný pro všechny databáze související se správou v ukázkové aplikaci.

1. Otevřít... \\Výukové\\moduly Provozní\\analýza Adhoc Reporting\\*Demo-AdhocReporting.ps1* v prostředí *PowerShell ISE* a nastavte následující hodnoty:
   * **$DemoScenario** = 2, **Nasadit ad hoc analytics databázi**.

2. Stisknutím **klávesy F5** spusťte skript a vytvořte databázi *adhocreportingu.*

V další části přidáte schéma do databáze, aby jej bylo možné použít ke spuštění distribuovaných dotazů.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurace databáze "head" pro spouštění distribuovaných dotazů

Toto cvičení přidá schéma (externí zdroj dat a externí definice tabulek) do databáze sestav ad hoc, která umožňuje dotazování napříč všemi databázemi klienta.

1. Otevřete SQL Server Management Studio a připojte se k databázi sestav Adhoc, kterou jste vytvořili v předchozím kroku. Název databáze je *adhocreporting*.
2. Otevřete ...\Výukové moduly\Provozní analýza\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* v SSMS.
3. Zkontrolujte skript SQL a poznamenejte si následující:

   Elastický dotaz používá pověření s rozsahem databáze pro přístup ke každé z databází klienta. Toto pověření musí být k dispozici ve všech databázích a obvykle by měla být udělena minimální práva potřebná k povolení těchto dotazů ad hoc.

    ![vytvoření přihlašovacích údajů](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Pomocí databáze katalogu jako externízdroj dat jsou dotazy distribuovány do všech databází registrovaných v katalogu při spuštění dotazu. Vzhledem k tomu, že názvy serverů se pro každé nasazení liší, získá@servernametento inicializační skript umístění databáze katalogu načtením aktuálního serveru (@ ), kde je skript spuštěn.

    ![vytvoření externího zdroje dat](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Externí tabulky, které odkazují na tabulky tenantů jsou definovány s **DISTRIBUTION = SHARDED(VenueId)**. To směruje dotaz pro konkrétní *VenueId* do příslušné databáze a zlepšuje výkon pro mnoho scénářů, jak je znázorněno v další části.

    ![vytvoření externích tabulek](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Místní tabulka *VenueTypes,* který je vytvořen a naplněn. Tato tabulka referenčních dat je běžná ve všech databázích klientů, takže může být zde reprezentována jako místní tabulka a naplněna společnými daty. U některých dotazů to může snížit množství dat přesunutých mezi databázemi klienta a *adhocreporting* databáze.

    ![vytvořit tabulku](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Pokud tímto způsobem zahrnete referenční tabulky, nezapomeňte aktualizovat schéma tabulky a data při každé aktualizaci databází klienta.

4. Stisknutím **klávesy F5** spusťte skript a inicializujte databázi *adhocreportingu.* 

Nyní můžete spouštět distribuované dotazy a shromažďovat přehledy napříč všemi tenanty!

## <a name="run-ad-hoc-distributed-queries"></a>Spuštění distribuovaných dotazů ad hoc

Teď, když je nastavena databáze *adhocreporting,* pokračujte a spusťte některé distribuované dotazy. Zahrnout plán spuštění pro lepší pochopení, kde se provádí zpracování dotazu. 

Při kontrole plánu spuštění, najeďte na ikony plánu pro podrobnosti. 

1. V *SSMS*, otevřít ... \\\\Výukové moduly Provozní\\analýza Adhoc Reporting\\*Demo-AdhocReportingQueries.sql*.
2. Ujistěte se, že jste připojeni k databázi **adhocreporting.**
3. Vyberte nabídku **Dotaz** a klikněte na **Zahrnout plán skutečného spuštění.**
4. Zvýrazněte *dotaz, která místa jsou aktuálně zaregistrována,* a stiskněte **klávesu F5**.

   Dotaz vrátí celý seznam míst konání, ilustrující, jak rychlé a snadné je dotazovat se napříč všemi tenanty a vracet data z každého klienta.

   Zkontrolujte plán a uvidíte, že všechny náklady jsou vzdálený dotaz, protože jsme prostě jít do každé databáze klienta a výběru informace o místě.

   ![VYBERTE * Z dbo. Místech](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Vyberte další dotaz a stiskněte **klávesu F5**.

   Tento dotaz spojuje data z databází klienta a místní *VenueTypes* tabulka (místní, jako je tabulka v databázi *adhocreporting).*

   Zkontrolujte plán a uvidíte, že většina nákladů je vzdálený dotaz, protože jsme dotaz každého klienta místo informace (dbo. Místa) a potom proveďte rychlé místní spojení s místní tabulkou *VenueTypes,* abyste zobrazili popisný název.

   ![Připojení ke vzdáleným a místním datům](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Nyní vyberte *dotaz Na který den bylo nejvíce prodaných vstupenek* a stiskněte **klávesu F5**.

   Tento dotaz provádí trochu složitější spojení a agregaci. Co je důležité si uvědomit, je, že většina zpracování se provádí na dálku, a opět přinášíme zpět pouze řádky, které potřebujeme, vrací pouze jeden řádek pro každé místo je celkový počet vstupenek za den.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> 
> * Spouštění distribuovaných dotazů ve všech tenantských databázích
> * Nasaďte databázi sestav ad hoc a přidejte do ní schéma pro spouštění distribuovaných dotazů.

Teď vyzkoušejte [kurz Tenant Analytics](saas-multitenantdb-tenant-analytics.md) a prozkoumejte extrahování dat do samostatné analytické databáze pro složitější zpracování analýzy.

## <a name="additional-resources"></a>Další zdroje

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastic Query](sql-database-elastic-query-overview.md)
