---
title: Osvědčené postupy pro Synchronizaci dat SQL Azure
description: Seznamte se s osvědčenými postupy pro konfiguraci a provozování Azure Synchronizace dat SQL.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: fd9bc17db3eccc64f35d7295d57dc120364481dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332980"
---
# <a name="best-practices-for-azure-sql-data-sync"></a>Osvědčené postupy pro Synchronizaci dat SQL Azure 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek popisuje osvědčené postupy pro Azure Synchronizace dat SQL.

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> Azure Synchronizace dat SQL v tuto **chvíli nepodporuje spravovanou** instanci SQL Azure.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a> Zabezpečení a spolehlivost

### <a name="client-agent"></a>Agent klienta

-   Nainstalujte klientského agenta pomocí nejmenšího privilegovaného uživatelského účtu, který má přístup k síťové službě.  
-   Do počítače, který není SQL Server počítač, nainstalujte agenta klienta.  
-   Neregistrujte místní databázi s více než jedním agentem.    
    -   Vyhněte se tomu i v případě, že synchronizujete různé tabulky pro různé skupiny synchronizace.  
    -   Registrace místní databáze s více agenty klienta představuje problémy při odstraňování jedné ze skupin synchronizace.

### <a name="database-accounts-with-least-required-privileges"></a>Účty databáze s minimálními požadovanými oprávněními

-   **Pro nastavení synchronizace**. Vytvořit nebo změnit tabulku; ALTER DATABASE; Vytvořit proceduru; Vybrat/změnit schéma; Vytvoří User-Defined typ.

-   **Pro průběžnou synchronizaci**. Výběr/vložení/aktualizace/odstranění u tabulek, které jsou vybrány pro synchronizaci a pro synchronizaci metadat a sledovacích tabulek; Oprávnění EXECUTE pro uložené procedury vytvořené službou; Oprávnění EXECUTE pro uživatelsky definované typy tabulek.

-   **Pro zrušení zřízení**. Upravit v tabulkách, které jsou součástí synchronizace; Vybrat/odstranit při synchronizaci tabulek metadat; Řízení pro tabulky sledování synchronizace, uložené procedury a uživatelsky definované typy.

Azure SQL Database podporuje pouze jednu sadu přihlašovacích údajů. K provedení těchto úloh v rámci tohoto omezení Vezměte v úvahu následující možnosti:

-   Změňte přihlašovací údaje pro různé fáze (například *credentials1* pro nastavení a *credentials2* pro průběžné).  
-   Změňte oprávnění přihlašovacích údajů (to znamená změnit oprávnění po nastavení synchronizace).

## <a name="setup"></a>Nastavení

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a> Hlediska databáze a omezení

#### <a name="database-size"></a>Velikost databáze

Když vytváříte novou databázi, nastavte maximální velikost tak, aby byla vždy větší než databáze, kterou nasazujete. Pokud nenastavíte maximální velikost na větší než nasazenou databázi, synchronizace se nezdařila. I když Synchronizace dat SQL nenabízí Automatický růst, můžete spuštěním `ALTER DATABASE` příkazu zvětšit velikost databáze po jejím vytvoření. Zajistěte, abyste zůstali v rámci omezení velikosti databáze.

> [!IMPORTANT]
> Synchronizace dat SQL ukládá další metadata s každou databází. Ujistěte se, že při výpočtu potřebného místa budete mít k těmto metadatům účet. Množství přidaných režijních nákladů se vztahuje k šířce tabulek (například úzká tabulka vyžaduje větší režii) a množství provozu.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a> Požadavky na tabulku a omezení

#### <a name="selecting-tables"></a>Výběr tabulek

Nemusíte zahrnovat všechny tabulky, které jsou v databázi ve skupině synchronizace. Tabulky, které zahrnete do skupiny synchronizace, mají vliv na efektivitu a náklady. Zahrnutí tabulek a tabulek, na kterých jsou závislé, ve skupině synchronizace pouze v případě, že je vyžaduje obchodní potřeby.

#### <a name="primary-keys"></a>Primární klíče

Každá tabulka ve skupině synchronizace musí mít primární klíč. Synchronizace dat SQL nemůže synchronizovat tabulku, která nemá primární klíč.

Před použitím Synchronizace dat SQL v produkčním prostředí, počátečním a probíhajícím výkonem synchronizace.

#### <a name="empty-tables-provide-the-best-performance"></a>Prázdné tabulky poskytují nejlepší výkon

Prázdné tabulky poskytují nejlepší výkon v době inicializace. Pokud je cílová tabulka prázdná, synchronizace dat načte data pomocí hromadného vložení. V opačném případě synchronizace dat provede porovnání řádek po řádku a vložení pro kontrolu konfliktů. Pokud se ale výkon netýká, můžete nastavit synchronizaci mezi tabulkami, které už data obsahují.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a> Zřizování cílových databází

Synchronizace dat SQL poskytuje základní zřizování databáze.

Tato část popisuje omezení zřizování v Synchronizace dat SQL.

#### <a name="autoprovisioning-limitations"></a>Omezení pro autozřizování

Synchronizace dat SQL má při autozřizování následující omezení:

-   Vyberte pouze sloupce, které jsou vytvořeny v cílové tabulce. Všechny sloupce, které nejsou součástí skupiny synchronizace, nejsou v cílových tabulkách zřízeny.
-   Indexy jsou vytvořeny pouze pro vybrané sloupce. Pokud má index zdrojové tabulky sloupce, které nejsou součástí skupiny synchronizace, tyto indexy nejsou v cílových tabulkách zřízeny.  
-   Indexy ve sloupcích typu XML nejsou zřízeny.  
-   KONTROLNÍ omezení nejsou zřízená.  
-   Existující triggery ve zdrojových tabulkách nejsou zřízené.  
-   Zobrazení a uložené procedury nejsou v cílové databázi vytvořeny.
-   Při aktualizaci na sebe a při odstraňování KASKÁDových akcí u omezení cizího klíče se v cílových tabulkách znovu nevytvoří.
-   Pokud máte desetinné nebo číselné sloupce s přesností větší než 28, Synchronizace dat SQL může při synchronizaci dojít k potížím s přetečením převodu. Doporučujeme, abyste omezili přesnost desetinných nebo číselných sloupců na 28 nebo méně.

#### <a name="recommendations"></a>Doporučení

-   Možnost autozřizování Synchronizace dat SQL použijte pouze při vyzkoušení služby.  
-   V produkčním prostředí zřiďte schéma databáze.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a> Kde najít databázi centra

#### <a name="enterprise-to-cloud-scenario"></a>Scénář pro použití v rámci cloudu

Aby se minimalizovala latence, udržujte databázi centra blízkou největší koncentraci provozu databáze skupiny synchronizace.

#### <a name="cloud-to-cloud-scenario"></a>Scénář cloudu do cloudu

-   Pokud jsou všechny databáze ve skupině synchronizace v jednom datovém centru, má se centrum nacházet ve stejném datovém centru. Tato konfigurace snižuje latenci a náklady na přenos dat mezi datacentry.
-   Pokud jsou databáze ve skupině synchronizace v několika datových centrech, musí být rozbočovač umístěný ve stejném datovém centru jako většina databází a přenosů databáze.

#### <a name="mixed-scenarios"></a>Smíšené scénáře

Použijte předchozí pokyny ke komplexním konfiguracím skupin synchronizace, jako jsou ty, které jsou kombinací podnikových scénářů a scénářů cloudu do cloudu.

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a> Vyhněte se pomalé a nákladné počáteční synchronizaci

V této části se podíváme na počáteční synchronizaci skupiny synchronizace. Naučte se, jak zajistit, aby počáteční synchronizace trvala déle, než je potřeba.

#### <a name="how-initial-sync-works"></a>Jak funguje úvodní synchronizace

Když vytvoříte skupinu synchronizace, začněte s daty jenom v jedné databázi. Pokud máte data ve více databázích, Synchronizace dat SQL považuje každý řádek za konflikt, který je potřeba vyřešit. Toto řešení konfliktů způsobuje zpomalení počáteční synchronizace. Pokud máte data ve více databázích, počáteční synchronizace může trvat několik dní a několik měsíců v závislosti na velikosti databáze.

Pokud jsou databáze v různých datových centrech, musí každý řádek cestovat mezi různými datacentry. Tím se zvýší náklady na počáteční synchronizaci.

#### <a name="recommendation"></a>Doporučení

Pokud je to možné, začněte s daty jenom v jedné z databází skupiny synchronizace.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a> Návrh, aby nedocházelo ke smyčkám synchronizace

Ke smyčce synchronizace dojde v případě, že v rámci skupiny synchronizace existují cyklické odkazy. V takovém případě jsou všechny změny v jedné databázi nekonečné a cyklicky replikovány prostřednictvím databází ve skupině synchronizace.   

Ujistěte se, že se vyhnete smyčkám synchronizace, protože způsobují snížení výkonu a můžou významně zvýšit náklady.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a> Změny, které se nedaří rozšířit

#### <a name="reasons-that-changes-fail-to-propagate"></a>Důvody selhání rozšíření změn

Změny se můžou nedaří rozšířit z některého z následujících důvodů:

-   Nekompatibilita schématu/datového typu.
-   Vkládání hodnoty null do sloupců, které nejsou null.
-   Porušení omezení cizího klíče.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Co se stane, když se změny nepodaří rozšířit?

-   Skupina synchronizace ukazuje, že je ve stavu **Upozornění** .
-   Podrobnosti jsou uvedeny v prohlížeči protokolu uživatelského rozhraní portálu.
-   Pokud se problém nevyřeší po 45 dnů, bude databáze zastaralá.

> [!NOTE]
> Tyto změny se nikdy nešíří. Jediným způsobem, jak obnovit tento scénář, je znovu vytvořit skupinu synchronizace.

#### <a name="recommendation"></a>Doporučení

Pravidelně monitorujte skupinu synchronizace a stav databáze prostřednictvím portálu a rozhraní protokolů.


## <a name="maintenance"></a>Údržba

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a> Vyhněte se neaktuálním databázím a skupinám synchronizace

Skupina synchronizace nebo databáze ve skupině synchronizace může být zastaralá. Když je stav skupiny synchronizace **neaktuální**, přestane fungovat. Je-li stav databáze **zastaralá**, může dojít ke ztrátě dat. Doporučujeme vyhnout se tomuto scénáři místo toho, abyste se museli pokoušet o zotavení z něj.

#### <a name="avoid-out-of-date-databases"></a>Vyhněte se zastaralým databázím

Stav databáze je nastaven na **neaktuální** , pokud byl offline až 45 dní nebo déle. Abyste se vyhnuli **zastaralému** stavu v databázi, zajistěte, aby žádná z databází nebyla v režimu 45 dní nebo déle offline.

#### <a name="avoid-out-of-date-sync-groups"></a>Vyhněte se neaktuálním skupinám synchronizace

Stav skupiny synchronizace je nastaven na hodnotu **zastaralé** , pokud se kterákoli změna ve skupině synchronizace nedokáže rozšířit do zbytku skupiny synchronizace po dobu 45 nebo více dní. Aby se zabránilo **zastaralému** stavu ve skupině synchronizace, pravidelně ověřte protokol historie synchronizační skupiny. Zajistěte, aby byly vyřešeny všechny konflikty, a zda byly změny úspěšně šířeny v rámci databáze skupiny synchronizace.

Skupina synchronizace může selhat při použití změny jednoho z těchto důvodů:

-   Nekompatibilita schématu mezi tabulkami.
-   Nekompatibilita dat mezi tabulkami.
-   Vložení řádku s hodnotou null ve sloupci, který nepovoluje hodnoty null.
-   Aktualizace řádku s hodnotou, která je v rozporu s omezením cizího klíče.

Chcete-li zabránit neaktuálním skupinám synchronizace:

-   Aktualizujte schéma tak, aby povolovalo hodnoty, které jsou obsaženy v neúspěšných řádcích.
-   Aktualizujte hodnoty cizího klíče tak, aby zahrnovaly hodnoty, které jsou obsaženy v neúspěšných řádcích.
-   Aktualizujte hodnoty dat v neúspěšném řádku tak, aby byly kompatibilní se schématem nebo cizími klíči v cílové databázi.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a> Vyhněte se problémům s rušením zřizování

V některých případech může zrušení registrace databáze u agenta klienta způsobit selhání synchronizace.

#### <a name="scenario"></a>Scénář

1. Skupina synchronizace A byla vytvořena pomocí instance SQL Database a databáze SQL Server, která je přidružena k místnímu agentovi 1.
2. Stejná místní databáze je zaregistrovaná u místního agenta 2 (Tento agent není přidružený k žádné skupině synchronizace).
3. Zrušení registrace místní databáze z místního agenta 2 odebere sledování a meta tabulky pro skupinu synchronizace a pro místní databázi.
4. Skupina synchronizace A operace se nezdaří, s touto chybou: "aktuální operace nemohla být dokončena, protože databáze není zřízená pro synchronizaci nebo nemáte oprávnění k tabulkám konfigurace synchronizace."

#### <a name="solution"></a>Řešení

Chcete-li se tomuto scénáři vyhnout, neregistrujte databázi s více než jedním agentem.

Postup obnovení z tohoto scénáře:

1. Odeberte databázi ze všech skupin synchronizace, do kterých patří.  
2. Přidejte databázi zpátky do každé skupiny synchronizace, ze které jste ji odebrali.  
3. Nasaďte každou ovlivněnou skupinu synchronizace (Tato akce zřídí databázi).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a> Úprava skupiny synchronizace

Nepokoušejte se odebrat databázi ze skupiny synchronizace a pak upravit skupinu synchronizace, aniž byste nejdřív nasadili jednu ze změn.

Místo toho nejprve odeberte databázi ze skupiny synchronizace. Pak nasaďte změnu a počkejte na dokončení zrušení zřízení. Po dokončení zrušení zřízení můžete upravit skupinu synchronizace a nasadit změny.

Pokud se pokusíte odebrat databázi a pak upravit skupinu synchronizace, aniž byste nejdřív nasadili jednu ze změn, jedna nebo jiná operace se nezdaří. Rozhraní portálu může být nekonzistentní. Pokud k tomu dojde, aktualizujte stránku, aby obnovila správný stav.

### <a name="avoid-schema-refresh-timeout"></a>Vyhnout se časovému limitu aktualizace schématu

Pokud máte komplexní schéma, které se má synchronizovat, můžete při aktualizaci schématu zaznamenat "časový limit operace", pokud má databáze metadat synchronizace nižší SKU (příklad: Basic). 

#### <a name="solution"></a>Řešení

Pokud chcete tento problém zmírnit, nahorizontální navýšení kapacity databáze metadat synchronizace můžete mít vyšší SKU, například S3. 

## <a name="next-steps"></a>Další kroky
Další informace o Synchronizace dat SQL najdete v tématech:

-   Přehled – [synchronizace dat napříč několika cloudy a místními databázemi pomocí Azure synchronizace dat SQL](sql-data-sync-data-sql-server-sql-database.md)
-   Nastavení synchronizace dat SQL
    - Na portálu – [kurz: nastavení synchronizace dat SQL pro synchronizaci dat mezi Azure SQL Database a SQL Server](sql-data-sync-sql-server-configure.md)
    - S využitím PowerShellu
        -  [Použití PowerShellu k synchronizaci mezi několika databázemi v Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi databází v SQL Database a databází v instanci SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Agent synchronizace dat – [Agent synchronizace dat pro Azure synchronizace dat SQL](sql-data-sync-agent-overview.md)
-   Monitorování – [monitorování synchronizace dat SQL pomocí protokolů Azure monitor](sql-data-sync-monitor-sync.md)
-   Řešení potíží – [řešení potíží s Azure synchronizace dat SQL](sql-data-sync-troubleshoot.md)
-   Aktualizace schématu synchronizace
    -   Pomocí jazyka Transact-SQL – [Automatizace replikace změn schématu v Azure synchronizace dat SQL](sql-data-sync-update-sync-schema.md)
    -   Prostředí PowerShell – [použití PowerShellu k aktualizaci schématu synchronizace v existující skupině synchronizace](scripts/update-sync-schema-in-sync-group.md)

Další informace o SQL Database najdete v tématech:

-   [Přehled SQL Database](sql-database-paas-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
