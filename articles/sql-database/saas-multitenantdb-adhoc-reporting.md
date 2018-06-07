---
title: Spuštění dotazů ad hoc vytváření sestav napříč více databází Azure SQL | Microsoft Docs
description: Spuštění dotazů ad hoc vytváření sestav napříč více databází SQL v příkladu víceklientské aplikace.
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: AyoOlubeko
ms.openlocfilehash: 95ea5f2a40bd629384e1bc1f7d707eb30d9eaf5f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644269"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Spuštění dotazů ad hoc analytics napříč více databází Azure SQL

V tomto kurzu spustíte distribuované dotazy napříč celou sadu klienta databáze a umožnit tak vykazování ad hoc interaktivní. Tyto dotazy můžete extrahovat Statistika schovaný v každodenní provozních dat aplikace SaaS Wingtip lístků. Pokud chcete provést tyto extrakce, nasaďte další analýze databáze na server katalogu a můžete povolit distribuované dotazy pomocí elastické dotazu.


V tomto kurzu se dozvíte:

> [!div class="checklist"]

> * Postup nasazení databáze služby ad hoc generování sestav
> * Postup spuštění distribuovaných dotazů mezi všechny databáze klienta


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace Wingtip lístky SaaS víceklientské databáze. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s aplikací Wingtip lístky SaaS víceklientské databáze](saas-multitenantdb-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* SQL Server Management Studio (SSMS) je nainstalována. Chcete-li stáhnout a nainstalovat aplikace SSMS, přečtěte si téma [stáhnout SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Vzor vykazování ad hoc

![generování sestav vzor ad hoc](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Aplikace SaaS můžete analyzovat velká množství dat klienta, které jsou centrálně uložené v cloudu. Analýz odhalit přehledy operace a využití vaší aplikace. Funkce vývoj, vylepšení použitelnost a další investice do vaší aplikace a služby, můžete tyto přehledy průvodce.

V jedné databázi s více tenanty je přístup k těmto datům jednoduchý, ale třeba v případě distribuce mezi tisícovky databází se situace komplikuje. Jeden z přístupů je použít [elastické dotazu](sql-database-elastic-query-overview.md), což umožňuje dotazování na distribuovanou sadu databází s společné schéma. Tyto databáze mohou být distribuovány na různé skupiny prostředků a předplatná. Ještě jeden běžné přihlášení musí mít přístup extrahovat data z všechny databáze. Elastické dotaz používá jeden *head* databáze, ve kterém jsou definovány externí tabulky, zrcadlení tabulky a zobrazení v databázích distribuované (klientů). Dotazy odeslané do této hlavní databáze se kompilují a vzniká plán distribuovaného dotazu, který zajistí předávání částí dotazu potřebným tenantským databázím. Elastické dotaz používá k určení umístění všech databází klienta mapy horizontálního oddílu v databázi katalogu. Instalační program a dotaz jsou jednoduché, pomocí standardní [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)a podporují ad hoc dotazy z nástroje, například Power BI a Excel.

Elastické dotazu distribucí dotazy mezi databázemi klienta, poskytuje okamžitý přehled o provozu provozními daty. Však jako elastické dotaz získává data ze potenciálně velký počet databází, latence dotazu může někdy být vyšší než pro odeslání do jedné databáze víceklientské ekvivalentní dotazy. Nezapomeňte návrhu dotazů minimalizovat objem dat, která je vrácena. Elastické dotazu je často nejvhodnější pro dotazy malých objemů dat v reálném čase a sestavování často používané nebo komplexní analytické dotazy nebo sestavy. Pokud dotazy neprovádět dobře, podívejte se na [plán spuštění](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) zobrazíte, jaká část dotazu bylo posunuto do vzdálené databáze. A vyhodnocení, kolik dat se vrací. Dotazy, které vyžadují komplexní analytického zpracování může být lepší obsluhuje ukládání dat extrahovaných klienta do databáze, která je optimalizovaná pro analytické dotazy. SQL Database a SQL Data Warehouse může hostovat takové databázi analýzy.

Tento vzor pro analýzu je podrobně popsaný [kurzu analýza klienta](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Zdrojový kód Wingtip lístky SaaS víceklientské databáze aplikace a skripty

Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS.

## <a name="create-ticket-sales-data"></a>Vytvořit data prodeje lístků

Ke spouštění dotazů na zajímavějšího datové sady, vytvořte spuštěním lístku generátor prodejní data lístku.

1. V *prostředí PowerShell ISE*, otevřete... \\Learning moduly\\provozní Analytics\\vykazování ad hoc\\*ukázku AdhocReporting.ps1* skript a nastavte následující hodnoty:
   * **$DemoScenario** = 1, **zakoupit lístky pro události na všechna místa**.
2. Stiskněte klávesu **F5** generovat prodeje lístků a spusťte skript. Je spuštěn skript, pokračujte kroky v tomto kurzu. Data lístku je dotazován v *spustit ad hoc distribuované dotazy* část, proto počkejte generátoru lístek pro dokončení.

## <a name="explore-the-tenant-tables"></a>Prozkoumejte tabulky klienta 

V aplikaci Wingtip lístky SaaS víceklientské databáze klientů jsou uloženy v hybridního klienta správy modelu – kde dat klienta je buď uložené v databázi více klientů nebo v databázi jednoho klienta a lze přesunout mezi nimi. Při dotazování mezi všechny databáze klienta, je důležité, aby elastické dotazu lze považovat data, pokud je součástí jedné logické databáze horizontálně dělené klientem. 

K dosažení tohoto vzoru, zahrnují všechny tabulky klienta *VenueId* sloupec, který identifikuje, který klienta data patří. *VenueId* se vypočítá jako zavádět jedinečnou hodnotu pro tento sloupec může použít hodnotu hash místo názvu, ale žádné přístup. Tento přístup je podobným způsobem, který je počítaný klíč klienta pro použití v katalogu. Tabulky obsahující *VenueId* jsou používány elastické dotazu pro paralelní dotazy a vložit je na databázi odpovídající vzdáleného klienta. Tím se výrazně snižuje množství dat, která je vrácena a výsledkem zvýšení výkonu, zejména v případě, že existuje více klientů, jejichž data jsou uloženy v databázi jednoho klienta.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Nasazení databáze používá pro distribuované dotazy ad hoc

Toto cvičení nasadí *adhocreporting* databáze. Toto je head databáze, která obsahuje schéma použitého k dotazování mezi všechny databáze klienta. Databáze je nasazený na existující server katalogu, které je server používá pro všechny databáze v ukázkové aplikace týkajících se správy.

1. Otevřete... \\Learning moduly\\provozní Analytics\\vykazování ad hoc\\*ukázku AdhocReporting.ps1* v *prostředí PowerShell ISE* a nastavte následující hodnoty:
   * **$DemoScenario** = 2, **databáze analýzy nasazení Ad hoc**.

2. Stiskněte klávesu **F5** spusťte skript a vytvořit *adhocreporting* databáze.

V další části přidáte schématu do databáze, může sloužit ke spuštění distribuovaných dotazů.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfiguraci databáze, head, pro spuštění distribuovaných dotazů

Tento postup přidá schématu (externí zdroj dat a definice externí tabulky) pro databázi ad hoc vytváření sestav, který umožňuje dotazování mezi všechny databáze klienta.

1. Otevřete SQL Server Management Studio a připojte k databázi ad hoc vytváření sestav, kterou jste vytvořili v předchozím kroku. Název databáze je *adhocreporting*.
2. Otevřete ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *inicializovat AdhocReportingDB.sql* v aplikaci SSMS.
3. Zkontrolujte skript SQL a vezměte na vědomí následující:

   Elastické dotazu pomocí přihlašovacích údajů platných pro databázi přístup ke každé databáze klienta. Tato pověření musí být k dispozici ve všech databází a by měl obvykle být uděleno minimální práva potřebná k povolení těchto ad hoc dotazy.

    ![Vytvoření pověření](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Pomocí databáze katalogu jako zdroj externích dat jsou dotazy distribuován do všech databází, které jsou zaregistrovány v katalogu při spuštění dotazu. Vzhledem k tomu, že názvy serverů se liší pro každé nasazení, získá tento skript inicializace umístění databáze katalogu načtením aktuální server (@@servername) němž se skript spustí.

    ![Vytvoření externího zdroje dat](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Externí tabulky, které odkazují na tabulky klienta jsou definovány s **distribuční = SHARDED(VenueId)**. To směruje dotazu pro konkrétní *VenueId* k příslušné databázi a zvyšuje výkon pro mnoho scénářů, jak je znázorněno v následujícím oddílu.

    ![Vytvoření externí tabulky](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Místní tabulky *VenueTypes* , je vytvořeny a naplněny. Tuto referenční tabulku dat je běžné v všechny klienta databáze, tak může být reprezentován jako na místní tabulku a vyplněná běžné daty. Pro některé dotazy, to může snížit množství dat přesunout mezi databázemi klienta a *adhocreporting* databáze.

    ![Vytvoření tabulky](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Pokud zahrnete referenční tabulky tímto způsobem, nezapomeňte aktualizovat schéma tabulky a data při každé aktualizaci databáze klienta.

4. Stiskněte klávesu **F5** inicializaci a spuštění skriptu *adhocreporting* databáze. 

Nyní můžete spustit distribuované dotazy a shromažďovat statistiky napříč všichni klienti!

## <a name="run-ad-hoc-distributed-queries"></a>Spuštění distribuovaných dotazů ad hoc

Teď, když *adhocreporting* databáze je nastavit, aby ihned začít a spustit některé distribuované dotazy. Zahrnout plán spuštění lépe porozumět tomu, kde se zpracování dotazu děje. 

Při kontrole plán spuštění, pozastavte ukazatel myši nad ikony plán podrobnosti. 

1. V *SSMS*, otevřete... \\Učení moduly\\provozní Analytics\\vykazování ad hoc\\*ukázku AdhocReportingQueries.sql*.
2. Zkontrolujte připojení k **adhocreporting** databáze.
3. Vyberte **dotazu** nabídky a klikněte na tlačítko **zahrnují skutečné naplánovat spuštění**
4. Zvýrazněte *místa, které jsou aktuálně registrované?* dotazu a stiskněte klávesu **F5**.

   Dotaz vrátí seznamu celý místo ilustrující způsob rychlé a snadné je k dotazování mezi všechny klienty a vrátit data z každého klienta.

   Zkontrolujte plán a zjistíte, že náklady je vzdálený dotaz, protože jsme jednoduše přejdete na každou databázi klienta a výběrem informace místo.

   ![Vybrat * z dbo. Místa](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Vyberte další dotaz a stiskněte klávesu **F5**.

   Tento dotaz připojí data z databáze klienta a místní *VenueTypes* tabulky (místní počítač, protože je tabulka *adhocreporting* databáze).

   Zkontrolujte plán a zjistíte, že většina náklady je vzdálený dotaz, protože jsme dotaz na informace místo každého klienta (dbo. Místa), a poté proveďte rychlé místní spojení s místní *VenueTypes* tabulka, která se zobrazí popisný název.

   ![Připojení k vzdálené a místní data](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Nyní vybrat *den, kdy byly nejvíce lístků prodaných?* dotazu a stiskněte klávesu **F5**.

   Tento dotaz nemá trochu složitější spojování a agregaci. Co je důležité si uvědomit, je, že se vzdáleně provádí většinu zpracování a ještě jednou budeme navrácení pouze řádky, které potřebujeme, vrácení pouze jeden řádek pro každý místo agregační lístku prodej počet za den.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]

> * Spouštění distribuovaných dotazů ve všech tenantských databázích
> * Nasazení databáze služby ad hoc generování sestav a přidejte do jeho spuštění distribuované dotazy schématu.

Nyní zkuste [klienta Analytics kurzu](saas-multitenantdb-tenant-analytics.md) prozkoumat extrakce dat k databázi samostatné analytics pro zpracování složitějších analýzy.

## <a name="additional-resources"></a>Další zdroje informací:

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastic Query](sql-database-elastic-query-overview.md)
