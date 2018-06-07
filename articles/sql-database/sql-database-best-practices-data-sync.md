---
title: Osvědčené postupy pro synchronizaci dat SQL Azure (Preview) | Microsoft Docs
description: Další informace o osvědčené postupy pro konfiguraci a spuštění synchronizaci dat SQL Azure (Preview).
services: sql-database
ms.date: 04/01/2018
ms.topic: conceptual
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 683cf1426f01b3ab495b2380612dbf37342fc27a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646003"
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Osvědčené postupy pro synchronizaci dat SQL (Preview) 

Tento článek popisuje osvědčené postupy pro synchronizaci dat SQL Azure (Preview).

Přehled synchronizaci dat SQL (Preview) najdete v tématu [synchronizaci dat mezi několika databází cloudu a místně s synchronizaci dat SQL Azure (Preview)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a> Zabezpečení a spolehlivost

### <a name="client-agent"></a>Klientský agent

-   Nainstalujte agenta klienta pomocí nejnižšími oprávněními uživatelský účet, který má přístup k síťové službě.  
-   Nainstalujte agenta klienta na počítači, který není místním počítači systému SQL Server.  
-   Nezaregistrujete místní databázi s více než jeden agentem.    
    -   Vyhněte se to i v případě, že se synchronizuje různých tabulek pro různé synchronizace skupiny.  
    -   Registrace místní databázi s více klientských agentů představuje problémy při odstranění skupiny pro synchronizaci.

### <a name="database-accounts-with-least-required-privileges"></a>Databáze účtů s nejnižší požadovaná oprávnění

-   **Pro nastavení synchronizace**. Vytvořit nebo Alter Table; Příkaz ALTER Database; Vytvořit proceduru; Vybrat / Alter schématu; Vytvořte uživatelsky definovaný typ.

-   **Pro účely trvalé synchronizace**. Vyberte / Vložit / aktualizovat / odstranit u tabulek, které jsou vybrány pro synchronizaci a na synchronizovat metadata a sledování tabulky; Oprávnění spouštět na uložené procedury vytvořených službou; Proveďte oprávnění uživatele definovaných typů tabulek.

-   **Pro zrušení zřízení**. Příkaz ALTER na tabulky součástí synchronizace; Vyberte / Delete pro synchronizaci metadat tabulky; Řízení na synchronizaci sledování tabulky a uložené procedury, uživatelem definované typy.

Azure SQL Database podporuje pouze jedinou sadu přihlašovacích údajů. Aby bylo možné tyto úlohy v rámci tohoto omezení, zvažte následující možnosti:

-   Změnit pověření pro různé fáze (například *credentials1* pro instalační program a *credentials2* pro probíhající).  
-   Změnit oprávnění přihlašovací údaje (tedy změnit oprávnění po synchronizaci nastavení).

## <a name="setup"></a>Nastavení

### <a name="database-considerations-and-constraints"></a> Informace se databází a omezení

#### <a name="sql-database-instance-size"></a>Velikost instance databáze SQL

Když vytvoříte novou instanci databáze SQL, nastavte maximální velikost tak, aby vždycky je větší než databázi, kterou nasazujete. Pokud nemáte nastavit maximální velikost na větší než nasazené databáze, synchronizace se nezdaří. I když synchronizaci dat SQL (Preview) nenabízí automatického zvětšování, můžete spustit `ALTER DATABASE` příkaz pro zvětšení velikosti databáze po jeho vytvoření. Zajistěte, abyste zůstat v rámci omezení velikosti instance databáze SQL.

> [!IMPORTANT]
> Synchronizaci dat SQL (Preview) ukládá další metadata s každou databázi. Ujistěte se, když vypočítat místo potřeby účtu pro tato metadata. Množství přidat režijní náklady související s šířku tabulky (například úzké tabulky vyžadují další režii) a objemu provozu.

### <a name="table-considerations-and-constraints"></a> Aspekty tabulky a omezení

#### <a name="selecting-tables"></a>Výběr tabulek

Nemusíte zahrnovat všechny tabulky, které jsou v databázi ve skupině pro synchronizaci. Tabulky, které můžete zahrnout do skupiny synchronizace ovlivnit efektivitu a nákladů. Zahrňte do tabulky a tabulky, které jsou závislé na, ve skupině synchronizaci pouze v případě, že obchodní potřeby vyžadují.

#### <a name="primary-keys"></a>Primární klíče

Každá tabulka ve skupině synchronizace musí mít primární klíč. Službu synchronizaci dat SQL (Preview) se nemůže synchronizovat tabulku, která nemá primární klíč.

Než začnete používat synchronizaci dat SQL (Preview) v provozním prostředí, testování výkonu počátečních a pokračujících synchronizace.

### <a name="provisioning-destination-databases"></a> Zřizování cílové databáze

Náhled synchronizaci dat SQL (Preview) poskytuje autoprovisioning databáze basic.

Tato část popisuje omezení zřizování v synchronizaci dat SQL (Preview).

#### <a name="autoprovisioning-limitations"></a>Omezení Autoprovisioning

Synchronizaci dat SQL (Preview) má následující omezení na autoprovisioning:

-   Vyberte pouze sloupce, které jsou vytvořeny v cílové tabulky.  
    Všech sloupců, které nejsou součástí skupiny synchronizace nebyly zřízené v cílových tabulkách.
-   Indexy jsou vytvářeny pouze pro vybrané sloupce.  
    Pokud má index tabulky zdrojového sloupce, které nejsou součástí skupiny synchronizace, tyto indexy nejsou zřízené v cílových tabulkách.  
-   Indexy na sloupce typu XML nejsou zřízené.  
-   ZKONTROLUJTE, že omezení není zřízený.  
-   Existující aktivační události na zdrojové tabulky nejsou zřízené.  
-   Zobrazení a uložených procedur nejsou vytvořeny v cílové databázi.

#### <a name="recommendations"></a>Doporučení

-   Jenom v případě, že se pokoušíte na službu, použijte možnost autoprovisioning synchronizaci dat SQL (Preview).  
-   V produkčním prostředí zřídit schéma databáze.

### <a name="locate-hub"></a> Umístění databáze rozbočovače

#### <a name="enterprise-to-cloud-scenario"></a>Scénář Enterprise cloudu

Pro zajištění minimální latence, zachovat databázi rozbočovače blízko největší koncentrace provozu databáze synchronizace skupiny.

#### <a name="cloud-to-cloud-scenario"></a>Scénář cloudu cloudu

-   Když se všechny databáze ve skupině synchronizace jednoho datového centra, rozbočovače musí nacházet ve stejném datovém centru. Tato konfigurace snižuje latenci a náklady na přenos dat mezi datovými centry.
-   Když se databáze ve skupině synchronizace několik datových center, rozbočovače musí nacházet ve stejném datovém centru jako většinu databáze a provozu databáze.

#### <a name="mixed-scenarios"></a>Smíšená scénáře

Podle předchozích pokynů týkají konfigurace skupiny komplexní synchronizace, jako jsou ty, které jsou směs scénáře enterprise do cloudu a z cloudu do cloudu.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Vyhněte se pomalé a nákladná počáteční synchronizaci

V této části probereme počáteční synchronizace synchronizace skupiny. Zjistěte, jak pomoci zabránit počáteční synchronizaci z trvá déle a probíhá dražší než nezbytné.

#### <a name="how-initial-sync-works"></a>Jak počáteční synchronizace funguje

Když vytvoříte skupinu synchronizace, spusťte s daty v pouze jednu databázi. Pokud máte data ve více databází, synchronizaci dat SQL (Preview). každý řádek považuje za konflikt, který musí být vyřešen. Toto řešení konfliktů způsobí, že počáteční synchronizace přejdete pomalu. Pokud máte data ve více databází, počáteční synchronizace může trvat několik dní až několik měsíců, v závislosti na velikosti databáze.

Pokud jsou databáze v různých datových centrech, musí každý řádek přenosu mezi různých datových centrech. Tím se zvyšuje náklady na počáteční synchronizaci.

#### <a name="recommendation"></a>Doporučení

Pokud je to možné spusťte s daty v jenom jedna z databází synchronizace skupiny.

### <a name="design-to-avoid-synchronization-loops"></a> Návrh, aby se zabránilo synchronizace smyčky

Smyčku synchronizace nastane, když jsou cyklické odkazy v rámci synchronizace skupiny. V tomto scénáři každé změně v jedné databáze se do nekonečna a pravidelného replikuje prostřednictvím databází ve skupině synchronizace.   

Ujistěte se vyhnout smyčky synchronizace, protože způsobit snížení výkonu a může se výrazně zvýšit náklady.

### <a name="handling-changes-that-fail-to-propagate"></a> Změny, které se nepodařilo rozšířit

#### <a name="reasons-that-changes-fail-to-propagate"></a>Z důvodů, které se nepodařilo rozšířit změny

Změny se nemusí podařit šířit z jednoho z následujících důvodů:

-   Nekompatibilita schématu nebo datového typu.
-   Vkládání v nevynulovatelném sloupci hodnotu null.
-   Narušení omezení cizího klíče.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Co se stane, pokud se nedaří rozšíří změny?

-   Zobrazí skupiny, které je v synchronizaci **upozornění** stavu.
-   Podrobnosti jsou uvedeny v portálu Prohlížeč protokolu uživatelského rozhraní.
-   Pokud není problém vyřešen 45 dní, stane aktuální databázi.

> [!NOTE]
> Tyto změny nikdy rozšíří. Jediným způsobem, jak obnovit v tomto scénáři je znovu vytvořit skupiny synchronizace.

#### <a name="recommendation"></a>Doporučení

Monitorování stavu synchronizace skupiny a databáze pravidelně přes rozhraní portálu a protokolu.


## <a name="maintenance"></a>Údržba

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Vyhněte se zastaralé databáze a synchronizaci skupin

Synchronizace skupiny nebo databáze ve skupině synchronizace se může stát zastaralý. Pokud je stav skupiny synchronizace **zastaralé**, přestane fungovat. Pokud je stav databáze **zastaralé**, data budou ztracena. Je vyhýbat se tento scénář namísto pokusu o obnovení z něj.

#### <a name="avoid-out-of-date-databases"></a>Vyhněte se zastaralé databáze

Stav do databáze nastaven na **zastaralé** při jejím offline pro 45 dnů nebo déle. Aby nedošlo **zastaralé** stavu v databázi, zajistěte, aby žádná z databází offline pro 45 dnů nebo déle.

#### <a name="avoid-out-of-date-sync-groups"></a>Vyhněte se zastaralé synchronizace skupiny

Stav skupiny synchronizace je nastaven na **zastaralé** případě všechny změny ve skupině synchronizace nezdaří rozšíří na zbytek skupiny synchronizace pro 45 dnů nebo déle. Aby nedošlo **zastaralé** stav pro skupinu synchronizace pravidelně Zkontrolujte skupiny synchronizace historie protokolu. Zkontrolujte, že se vyřeší všechny konflikty a, aby se změny rozšířeny úspěšně v rámci databáze synchronizace skupiny.

Skupiny synchronizace se pravděpodobně nezdaří použít ke změně jednoho z těchto důvodů:

-   Schéma nekompatibilita mezi tabulkami.
-   Nekompatibilita data mezi tabulkami.
-   Vložení řádků s hodnotou null ve sloupci, který nepovoluje hodnoty null.
-   Probíhá aktualizace řádek hodnotu, která porušuje omezení cizího klíče.

Aby se zabránilo zastaralé synchronizace skupiny:

-   Aktualizujte schéma tak, aby povolují hodnoty, které jsou obsaženy v řádcích se nezdařilo.
-   Aktualizujte hodnoty cizího klíče zahrnout hodnoty, které jsou obsaženy v řádcích se nezdařilo.
-   Hodnoty data v řádku se nezdařilo aktualizujte tak, aby byly kompatibilní s schématu nebo cizí klíče v cílové databázi.

### <a name="avoid-deprovisioning-issues"></a> Vyhněte se zrušení zřízení problémy

V některých případech rušení registrace databáze s klientským agentem může dojít k synchronizaci, aby se nezdaří.

#### <a name="scenario"></a>Scénář

1. Synchronizace skupiny A byl vytvořen pomocí instance databáze SQL a místní databázi systému SQL Server, který je přidružen ke místní agent 1.
2. Stejnou místní databázi není registrována s místní agent 2 (Tento agent není přidružené k žádné skupině sync).
3. Zrušení registrace místní databázi z místního agenta 2 odebere sledování a tabulky metadat pro synchronizaci A skupiny pro místní databázi.
4. Synchronizace skupiny nezdaří, k této chybě: "aktuální operaci nelze dokončit, protože databáze není zřízený pro synchronizaci nebo nemáte oprávnění k synchronizaci konfigurace tabulky."

#### <a name="solution"></a>Řešení

Chcete-li zamezit tomuto scénáři, nezaregistrujete databáze s více než jeden agentem.

Pokud chcete obnovit z tento scénář:

1. Odeberte z každé synchronizace skupiny, který patří do databáze.  
2. Přidejte databázi zpět do každé skupiny synchronizace, který jste odebrali z.  
3. Nasaďte každou skupinu ovlivněných synchronizace (Tato akce zřídí databázi).  

### <a name="modifying-your-sync-group"></a> Úprava skupiny synchronizace

Nemáte pokus o odebrání databáze ze skupiny pro synchronizaci a pak upravte skupiny synchronizace bez nasazení první z nich změny.

Místo toho odeberte nejprve databázi ze skupiny pro synchronizaci. Potom nasaďte změn a počkejte na dokončení zrušení zřízení. Po dokončení zrušení zřízení, můžete upravit skupiny synchronizace a změny nasazení.

Pokud se pokusíte odebrat databázi a pak upravte skupinu synchronizace bez nasazení první z nich změny, jeden nebo jiná operace selže. Rozhraní portálu, mohou být nekonzistentní. Pokud k tomu dojde, aktualizujte stránku k obnovení stavu.

## <a name="next-steps"></a>Další postup
Další informace o synchronizaci dat SQL (Preview) najdete v tématu:

-   [Synchronizaci dat mezi několika databází cloudu a místně s synchronizaci dat SQL Azure (Preview)](sql-database-sync-data.md)
-   [Nastavit synchronizaci dat SQL Azure (Preview)](sql-database-get-started-sql-data-sync.md)
-   [Synchronizaci dat Azure SQL (Preview) monitorování s analýzy protokolů](sql-database-sync-monitor-oms.md)
-   [Řešení problémů s synchronizaci dat SQL Azure (Preview)](sql-database-troubleshoot-data-sync.md)  
-   Dokončete příklady prostředí PowerShell, které ukazují, jak nakonfigurovat synchronizaci dat SQL (Preview):  
    -   [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Stáhněte si dokumentaci rozhraní API REST synchronizaci dat SQL (Preview)](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)  

Další informace o databázi SQL najdete v tématu:

-   [Databáze SQL – přehled](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
