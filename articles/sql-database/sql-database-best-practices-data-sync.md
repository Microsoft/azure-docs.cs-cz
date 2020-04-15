---
title: Osvědčené postupy pro synchronizaci dat
description: Přečtěte si o doporučených postupech pro konfiguraci a spuštění Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 2b72d52463164c2a059fce316cc11a63aad62e7c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380937"
---
# <a name="best-practices-for-sql-data-sync"></a>Osvědčené postupy pro synchronizaci dat SQL 

Tento článek popisuje osvědčené postupy pro Azure SQL Data Sync.

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync v tuto chvíli **nepodporuje** spravovanou instanci Azure SQL Database.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a>Bezpečnost a spolehlivost

### <a name="client-agent"></a>Agent klienta

-   Nainstalujte klientského agenta pomocí nejméně privilegovaného uživatelského účtu, který má přístup k síťové službě.  
-   Nainstalujte klientského agenta do počítače, který není místním počítačem SQL Server.  
-   Neregistrujte místní databázi s více než jedním agentem.    
    -   Vyhněte se tomu, i když synchronizujete různé tabulky pro různé skupiny synchronizace.  
    -   Registrace místní databáze s více agenty klienta představuje problémy při odstranění jedné ze skupin synchronizace.

### <a name="database-accounts-with-least-required-privileges"></a>Databázové účty s nejméně požadovanými oprávněními

-   **Pro nastavení synchronizace**. Vytvořit nebo změnit tabulku; Změnit databázi; Vytvořit proceduru; Vybrat nebo změnit schéma; Vytvořte typ definovaný uživatelem.

-   **Pro probíhající synchronizaci**. Vybrat/ Vložit/ Aktualizovat/ Odstranit v tabulkách, které jsou vybrány pro synchronizaci, a na synchronizačních metadatech a sledovacích tabulkách; Spouštět oprávnění na uložené procedury vytvořené službou; Spusťte oprávnění pro uživatelem definované typy tabulek.

-   **Pro zrušení zřízení**. Změnit na tabulkách část synchronizace; Vybrat/ odstranit v tabulkách synchronizačních metadat; Řízení na tabulky sledování synchronizace, uložené procedury a uživatelem definované typy.

Azure SQL Database podporuje jenom jednu sadu přihlašovacích údajů. Chcete-li provést tyto úkoly v rámci tohoto omezení, zvažte následující možnosti:

-   Změňte pověření pro různé fáze (například *pověření1* pro nastavení a *pověření2* pro probíhající).  
-   Změňte oprávnění pověření (to znamená, že po nastavení synchronizace změňte oprávnění).

## <a name="setup"></a>Nastavení

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a>Aspekty a omezení databáze

#### <a name="sql-database-instance-size"></a>Velikost instance databáze SQL

Při vytváření nové instance databáze SQL nastavte maximální velikost tak, aby byla vždy větší než naváděná databáze. Pokud nenastavíte maximální velikost na větší než nasazená databáze, synchronizace se nezdaří. Přestože SQL Data Sync nenabízí automatický růst, můžete spustit `ALTER DATABASE` příkaz pro zvětšení velikosti databáze po jeho vytvoření. Ujistěte se, že zůstanete v rámci omezení velikosti instance databáze SQL.

> [!IMPORTANT]
> Sql Data Sync ukládá další metadata s každou databázi. Ujistěte se, že účet pro tato metadata při výpočtu potřebné místo. Množství přidané režie souvisí s šířkou tabulek (například úzké tabulky vyžadují větší režii) a množství provozu.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a>Aspekty a omezení tabulky

#### <a name="selecting-tables"></a>Výběr tabulek

Není třeba zahrnout všechny tabulky, které jsou v databázi ve skupině synchronizace. Tabulky, které zahrnete do synchronizační skupiny, ovlivňují efektivitu a náklady. Tabulky a tabulky, na kterých jsou závislé, zahrňte do synchronizační skupiny pouze v případě, že to vyžadují obchodní potřeby.

#### <a name="primary-keys"></a>Primární klíče

Každá tabulka ve skupině synchronizace musí mít primární klíč. Služba SQL Data Sync nemůže synchronizovat tabulku, která nemá primární klíč.

Před použitím synchronizace dat SQL v produkčním prostředí otestujte počáteční a průběžný výkon synchronizace.

#### <a name="empty-tables-provide-the-best-performance"></a>Prázdné tabulky poskytují nejlepší výkon

Prázdné tabulky poskytují nejlepší výkon v době inicializace. Pokud je cílová tabulka prázdná, aplikace Data Sync načte data hromadnou vložkou. V opačném případě synchronizace dat provádí porovnání řádek po řádku a vložení ke kontrole konfliktů. Pokud však výkon není problém, můžete nastavit synchronizaci mezi tabulkami, které již obsahují data.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a>Zřizování cílových databází

SQL Data Sync poskytuje základní databáze automatické zřizování.

Tato část popisuje omezení zřizování v synchronizaci dat SQL.

#### <a name="autoprovisioning-limitations"></a>Omezení automatického zřizování

Sql Data Sync má následující omezení pro automatické zřizování:

-   Vyberte pouze sloupce, které jsou vytvořeny v cílové tabulce. Všechny sloupce, které nejsou součástí skupiny synchronizace, se v cílových tabulkách nezřídí.
-   Indexy jsou vytvořeny pouze pro vybrané sloupce. Pokud index zdrojové tabulky obsahuje sloupce, které nejsou součástí skupiny synchronizace, tyto indexy nejsou zřízeny v cílových tabulkách.  
-   Indexy ve sloupcích typu XML se nezřídí.  
-   Omezení CHECK nejsou zřízena.  
-   Existující aktivační události ve zdrojových tabulkách nejsou zřízeny.  
-   Zobrazení a uložené procedury nejsou vytvořeny v cílové databázi.
-   AKCE NA AKTUALIZACI KASKÁDY a PŘI ODSTRANĚNÍ NA Omezení cizího klíče nejsou znovu vytvořeny v cílových tabulkách.
-   Pokud máte desetinné nebo číselné sloupce s přesností větší než 28, sql data sync může dojít k problému přetečení převodu během synchronizace. Doporučujeme omezit přesnost desetinných nebo číselných sloupců na 28 nebo méně.

#### <a name="recommendations"></a>Doporučení

-   Funkci automatického zřizování synchronizace dat SQL používejte pouze v případě, že zkoušíte službu.  
-   Pro produkční prostředí zřizte schéma databáze.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a>Kde najít databázi rozbočovačů

#### <a name="enterprise-to-cloud-scenario"></a>Scénář mezi podniky a cloudem

Chcete-li minimalizovat latenci, udržujte databázi rozbočovače blízko k největší koncentraci databázového provozu skupiny synchronizace.

#### <a name="cloud-to-cloud-scenario"></a>Scénář cloud-cloud

-   Když jsou všechny databáze ve skupině synchronizace v jednom datovém centru, centrum by mělo být umístěno ve stejném datovém centru. Tato konfigurace snižuje latenci a náklady na přenos dat mezi datovými centry.
-   Když jsou databáze ve skupině synchronizace ve více datových centrech, rozbočovač by měl být umístěn ve stejném datovém centru jako většina databází a databázového provozu.

#### <a name="mixed-scenarios"></a>Smíšené scénáře

Předchozí pokyny použijte pro složité konfigurace skupin synchronizačnískupiny, jako jsou ty, které jsou kombinací scénářů enterprise-to-cloud a cloud-to-cloud.

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a>Vyhněte se pomalé a nákladné počáteční synchronizaci

V této části budeme diskutovat o počáteční synchronizaci skupiny synchronizace. Přečtěte si, jak zabránit tomu, aby počáteční synchronizace trvat déle a byla nákladnější, než je nutné.

#### <a name="how-initial-sync-works"></a>Jak funguje počáteční synchronizace

Při vytváření skupiny synchronizace začněte s daty pouze v jedné databázi. Pokud máte data ve více databázích, SQL Data Sync považuje každý řádek za konflikt, který je třeba vyřešit. Toto řešení konfliktů způsobí, že počáteční synchronizace jít pomalu. Pokud máte data ve více databázích, může počáteční synchronizace trvat několik dní až několik měsíců, v závislosti na velikosti databáze.

Pokud jsou databáze v různých datových centrech, musí každý řádek cestovat mezi různými datovými centry. To zvyšuje náklady na počáteční synchronizaci.

#### <a name="recommendation"></a>Doporučení

Pokud je to možné, začněte s daty pouze v jedné z databází skupiny synchronizace.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a>Návrh, aby se zabránilo synchronizaci smyček

Synchronizační smyčka nastane, když existují cyklické odkazy v rámci skupiny synchronizace. V tomto scénáři každá změna v jedné databázi je donekonečna a cyklicky replikovány prostřednictvím databází ve skupině synchronizace.   

Ujistěte se, že se vyhnete synchronizačním smyčkám, protože způsobují snížení výkonu a mohou výrazně zvýšit náklady.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a>Změny, které se nepodaří šířit

#### <a name="reasons-that-changes-fail-to-propagate"></a>Důvody, proč se změny nerozšíří

Změny se nemusí množit z jednoho z následujících důvodů:

-   Nekompatibilita schématu/datového typu.
-   Vložení null do sloupců, které by neuplatňovaly.
-   Porušení omezení cizího klíče.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Co se stane, když se změny nerozšíří?

-   Skupina synchronizace ukazuje, že je ve stavu **Upozornění.**
-   Podrobnosti jsou uvedeny v prohlížeči protokolů portálu ui.
-   Pokud problém není vyřešen po dobu 45 dnů, databáze se stane zastaralá.

> [!NOTE]
> Tyto změny se nikdy nerozšíří. Jediný způsob, jak obnovit v tomto scénáři je znovu vytvořit skupinu synchronizace.

#### <a name="recommendation"></a>Doporučení

Pravidelně monitorujte stav skupiny synchronizace a databáze prostřednictvím portálu a rozhraní protokolu.


## <a name="maintenance"></a>Údržba

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a>Vyhněte se zastaralým databázím a skupinám synchronizace

Skupina synchronizace nebo databáze ve skupině synchronizace může být zastaralá. Pokud je stav synchronizační skupiny **zastaralý**, přestane fungovat. Pokud je stav databáze zastaralý , může dojít **ke**ztrátě dat. Je nejlepší vyhnout se tomuto scénáři namísto pokusu o zotavení z něj.

#### <a name="avoid-out-of-date-databases"></a>Vyhněte se zastaralým databázím

Stav databáze je nastaven a je **zastaralý,** pokud je offline po dobu 45 dnů nebo déle. Chcete-li se vyhnout **zastaralému** stavu v databázi, ujistěte se, že žádná z databází není v podobu 45 dnů nebo déle.

#### <a name="avoid-out-of-date-sync-groups"></a>Vyhněte se zastaralým skupinám synchronizace

Stav skupiny synchronizace je nastaven na **zastaralé,** když se nepodaří šířit do zbytku skupiny synchronizace po dobu 45 dnů nebo déle. Chcete-li se vyhnout **zastaralému** stavu ve skupině synchronizace, pravidelně kontrolujte protokol historie skupiny synchronizace. Ujistěte se, že jsou vyřešeny všechny konflikty a že změny jsou úspěšně šířeny v databázích skupiny synchronizace.

Skupině synchronizace se nemusí podaje použít změnu z jednoho z těchto důvodů:

-   Nekompatibilita schématu mezi tabulkami.
-   Nekompatibilita dat mezi tabulkami.
-   Vložení řádku s nulovou hodnotou do sloupce, který neumožňuje hodnoty null.
-   Aktualizace řádku s hodnotou, která porušuje omezení cizího klíče.

Chcete-li zabránit zastaralým skupinám synchronizace:

-   Aktualizujte schéma povolit hodnoty, které jsou obsaženy v řádcích se nezdařilo.
-   Aktualizujte hodnoty cizího klíče tak, aby zahrnovaly hodnoty obsažené v neúspěšných řádcích.
-   Aktualizujte hodnoty dat v řádku se nezdařilo tak, aby byly kompatibilní se schématem nebo cizíklíče v cílové databázi.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a>Vyhněte se problémům s zrušením poskytování služeb

V některých případech zrušení registrace databáze s agentem klienta může způsobit selhání synchronizace.

#### <a name="scenario"></a>Scénář

1. Skupina synchronizace A byla vytvořena pomocí instance databáze SQL a místní databáze serveru SQL Server, která je přidružena k místnímu agentovi 1.
2. Stejná místní databáze je registrována u místního agenta 2 (tento agent není přidružen k žádné skupině synchronizace).
3. Zrušení registrace místní databáze od místního agenta 2 odebere tabulky sledování a meta pro synchronizační skupinu A pro místní databázi.
4. Synchronizace skupiny A operace nezdaří, s touto chybou: "Aktuální operace nelze dokončit, protože databáze není zřízena pro synchronizaci nebo nemáte oprávnění k tabulky konfigurace synchronizace."

#### <a name="solution"></a>Řešení

Chcete-li se tomuto scénáři vyhnout, neregistrujte databázi s více než jedním agentem.

Chcete-li obnovit z tohoto scénáře:

1. Odeberte databázi z každé skupiny synchronizace, do které patří.  
2. Přidejte databázi zpět do každé skupiny synchronizace, ze které jste ji odebrali.  
3. Nasazení každé ohrožené skupiny synchronizace (tato akce zřizuje databázi).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a>Úprava skupiny synchronizace

Nepokoušejte se odebrat databázi ze skupiny synchronizace a potom upravit skupinu synchronizace bez předchozího nasazení jedné ze změn.

Místo toho nejprve odeberte databázi ze skupiny synchronizace. Potom nasadit změnu a počkejte na dokončení zrušení zřizování. Po dokončení zrušení zřízení můžete upravit skupinu synchronizace a nasadit změny.

Pokud se pokusíte odebrat databázi a potom upravit skupinu synchronizace bez předchozího nasazení jedné ze změn, jedna nebo druhá operace se nezdaří. Rozhraní portálu může být nekonzistentní. Pokud k tomu dojde, aktualizujte stránku a obnovte správný stav.

### <a name="avoid-schema-refresh-timeout"></a>Vyhněte se časový mno žeprodej i schématu

Pokud máte složité schéma k synchronizaci, může dojít k "časový časový interval operace" během aktualizace schématu, pokud databáze metadat synchronizace má nižší skladovou položku (příklad: basic). 

#### <a name="solution"></a>Řešení

Chcete-li tento problém zmírnit, vertikálně navažte databázi metadat synchronizace, abyste měli vyšší skladovou položku, například S3. 

## <a name="next-steps"></a>Další kroky
Další informace o synchronizaci dat SQL naleznete v tématu:

-   Přehled – [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Azure SQL Data Sync](sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: Nastavení synchronizace dat SQL pro synchronizaci dat mezi databází Azure SQL a SQL Server em i v místním prostředí](sql-database-get-started-sql-data-sync.md)
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizace dat – [agent synchronizace dat pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)
-   Monitor – [monitorování synchronizace dat SQL pomocí protokolů Azure Monitoru](sql-database-sync-monitor-oms.md)
-   Poradce při potížích – [řešení problémů se synchronizací dat Azure SQL](sql-database-troubleshoot-data-sync.md)
-   Aktualizace schématu synchronizace
    -   S Transact-SQL – [automatizace replikace změn schématu v Synchronizaci dat Azure SQL](sql-database-update-sync-schema.md)
    -   S PowerShellem – [k aktualizaci schématu synchronizace v existující skupině synchronizace použijte PowerShell](scripts/sql-database-sync-update-schema.md)

Další informace o databázi SQL naleznete v tématu:

-   [Přehled databáze SQL](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
