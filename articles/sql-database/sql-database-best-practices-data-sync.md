---
title: Osvědčené postupy pro synchronizaci dat SQL Azure | Dokumentace Microsoftu
description: Další informace o osvědčené postupy pro konfiguraci a spuštění synchronizace dat SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: a7289ba5ab1f4e94adc099978591d69cac6ff786
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721593"
---
# <a name="best-practices-for-sql-data-sync"></a>Osvědčené postupy pro synchronizaci dat SQL 

Tento článek popisuje osvědčené postupy pro synchronizaci dat SQL Azure.

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data synchronizace provádí vložení změn **není** v tuto chvíli podporován Azure SQL Database Managed Instance.

## <a name="security-and-reliability"></a> Zabezpečení a spolehlivost

### <a name="client-agent"></a>Klientský agent aktualizace softwaru

-   Nainstalujte agenta klienta s použitím nejméně privilegovaný uživatelský účet, který má přístup k síťové službě.  
-   Nainstalujte agenta klienta na počítač, který není v místním počítači systému SQL Server.  
-   Nezaregistrujete místní databáze s více než jednoho agenta.    
    -   Vyhněte se to i v případě, že se synchronizují různých tabulek pro různé synchronizace skupiny.  
    -   Registrace v místní databázi s více výzev představuje agenty klientů při odstranění skupiny synchronizace.

### <a name="database-accounts-with-least-required-privileges"></a>Účty databází s nejnižší požadovaná oprávnění

-   **Pro nastavení synchronizace**. Vytvořit nebo Alter Table; Příkaz ALTER Database; Vytvořit proceduru; Vybrat / Alter schématu; Vytvořte uživatelsky definovaný typ.

-   **Pro průběžnou synchronizaci**. Vyberte / Vložit / aktualizace / odstranění tabulek, které jsou vybrány pro synchronizaci a synchronizovat metadata a sledování tabulkách. Oprávnění spouštět na uložené procedury, které jsou vytvořené službou. Spusťte oprávnění pro uživatelem definované typy.

-   **Pro zrušení zřízení**. Příkaz ALTER na tabulky součást synchronizace; Vyberte / odstranit v tabulkách metadat synchronizace. Ovládací prvek při synchronizaci sledování tabulky, uložených procedur a uživatelem definovaných typů.

Azure SQL Database podporuje pouze jedinou sadu přihlašovacích údajů. K provedení těchto úloh v rámci tohoto omezení, zvažte následující možnosti:

-   Změna přihlašovacích údajů pro jednotlivé fáze (například *credentials1* pro instalační program a *credentials2* pro probíhající).  
-   Změnit oprávnění přihlašovací údaje (to znamená, změňte oprávnění po nastavení synchronizace).

## <a name="setup"></a>Nastavení

### <a name="database-considerations-and-constraints"></a> Důležité informace o databázi a omezení

#### <a name="sql-database-instance-size"></a>Velikost instance SQL Database

Když vytvoříte novou instanci SQL Database, nastavte maximální velikost tak, aby se vždy větší než databáze, kterou nasadíte. Pokud nenastavíte maximální velikost pro větší než nasazené databáze, synchronizace se nezdaří. I když se synchronizací dat SQL nenabízí automatického zvětšování, můžete spustit `ALTER DATABASE` příkaz zvýšit velikost databáze po jeho vytvoření. Ujistěte se, že se vejdete do omezení velikosti instance SQL Database.

> [!IMPORTANT]
> Synchronizace dat SQL ukládá další metadata s každou databázi. Ujistěte se, když počítáte potřebné místo na účet pro tato metadata. Přidání množství režie související s šířka tabulky (například úzký tabulky vyžadují vyšší mírou režie) a objemu provozu.

### <a name="table-considerations-and-constraints"></a> Tabulka aspektů a omezení

#### <a name="selecting-tables"></a>Výběr tabulky

Není nutné zahrnout všechny tabulky, které jsou v databázi ve skupině synchronizace. Tabulky, které patří do skupiny synchronizace ovlivnit efektivitu a náklady. Zahrnují tabulky a tabulky, které jsou závislé na, ve skupině synchronizace pouze v případě, že obchodní potřeby vyžadují ho.

#### <a name="primary-keys"></a>Primární klíče

Každá tabulka ve skupině synchronizace musí mít primární klíč. Synchronizace dat SQL service nemůže synchronizovat tabulku, která nemá primární klíč.

Než začnete používat synchronizaci dat SQL v produkčním prostředí, testování výkonu počáteční a průběžné synchronizace.

#### <a name="empty-tables-provide-the-best-performance"></a>Prázdné tabulky poskytují nejlepší výkon

Prázdné tabulky poskytují nejlepší výkon během inicializace. Pokud cílová tabulka je prázdná, synchronizaci dat používá příkaz bulk insert a načíst data. V opačném případě synchronizace dat provádí porovnání řádek po řádku a vložení ke kontrole konfliktů. Pokud výkon není žádný problém, ale je můžete nastavit synchronizaci mezi tabulkami, které již obsahují data.

### <a name="provisioning-destination-databases"></a> Zřizování cílové databáze

Synchronizace dat SQL poskytuje autoprovisioning databáze basic.

Tato část popisuje omezení pro zřizování v synchronizaci dat SQL.

#### <a name="autoprovisioning-limitations"></a>Omezení Autoprovisioning

Synchronizace dat SQL má následující omezení pro autoprovisioning:

-   Vyberte sloupce, které jsou vytvořeny v cílové tabulce. Všechny sloupce, které nejsou součástí skupiny synchronizace nezřídil do cílových tabulek.
-   Indexy jsou vytvořeny pouze pro vybrané sloupce. Pokud zdrojové tabulky indexu má sloupce, které nejsou součástí skupiny synchronizace, tyto indexy nejsou zřízené v cílových tabulek.  
-   Indexy na sloupce typu XML nejsou zřízené.  
-   ZKONTROLUJTE, že omezení není zřízený.  
-   Existující aktivačních událostí zdrojových tabulkách nejsou zřízené.  
-   Zobrazení a uložených procedur nejsou vytvořeny v cílové databázi.
-   NA UPDATE CASCADE a ON DELETE CASCADE akce na omezení cizího klíče nejsou znovu vytvořit v cílových tabulek.
-   Pokud máte sloupce desítkový nebo číselný s přesností vetší než 28, synchronizaci dat SQL setkat problému Přetečení převodu během synchronizace. Doporučujeme omezit přesnosti desítkový nebo číselný sloupce na 28 nebo méně.

#### <a name="recommendations"></a>Doporučení

-   Použijte funkci autoprovisioning synchronizace dat SQL pouze v případě, že jsou vyzkoušejte si službu.  
-   Pro produkční prostředí zřizování schématu databáze.

### <a name="locate-hub"></a> Umístění databáze centra

#### <a name="enterprise-to-cloud-scenario"></a>Scénáře podnikového cloudu

Abyste minimalizovali latenci, ponechte databázi centra blízko největší zaměření provozu databáze skupinu synchronizace.

#### <a name="cloud-to-cloud-scenario"></a>Scénář cloud-to-cloud

-   Pokud jsou všechny databáze ve skupině synchronizace v jednom datacentru, rozbočovače musí nacházet ve stejném datacentru. Tato konfigurace snižuje latence a nákladů za přenos dat mezi datovými centry.
-   Jsou-li databáze ve skupině synchronizace v několika datových centrech, rozbočovače musí nacházet ve stejném datacentru jako většina databází a provozu databáze.

#### <a name="mixed-scenarios"></a>Hybridní scénáře

Platí výše uvedených pokynů pro komplexní synchronizace konfigurace skupiny, jako jsou ty, které jsou kombinací scénářích podnikový cloud a cloud-to-cloud.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Vyhněte se pomalé a nákladná počáteční synchronizace

V této části se podíváme na počáteční synchronizace skupiny synchronizace. Zjistěte, jak zabránit počáteční synchronizaci ze služeb trvá déle a jsou dražší než nezbytné.

#### <a name="how-initial-sync-works"></a>Funguje jak počáteční synchronizace

Když vytvoříte skupinu synchronizace, začněte s daty v jediné databázi. Pokud máte data ve více databázích, synchronizaci dat SQL považuje za každý řádek, který je potřeba vyřešit konflikt. Toto řešení konfliktů způsobí, že počáteční synchronizace přejít pomalu. Pokud máte data ve více databázích, počáteční synchronizace může trvat několik dní až několik měsíců, v závislosti na velikosti databáze.

Pokud jsou databáze v různých datových centrech, musí projít každý řádek mezi různých datových centrech. Tím se zvyšuje náklady na počáteční synchronizace.

#### <a name="recommendation"></a>Doporučení

Pokud je to možné začněte s data pouze v jedné skupině synchronizace databáze.

### <a name="design-to-avoid-synchronization-loops"></a> Návrh předejděte smyčkám synchronizace

Synchronizace smyčky nastane, pokud jsou cyklické odkazy v rámci skupiny synchronizace. V takovém scénáři každou změnu v jedné databázi se nekonečna a sama na sebe replikuje prostřednictvím databázemi ve skupině synchronizace.   

Ujistěte se vyhnout smyčky synchronizace, protože způsobit snížení výkonu a může se výrazně zvýšit náklady.

### <a name="handling-changes-that-fail-to-propagate"></a> Změny, které se nepodařilo rozšířit

#### <a name="reasons-that-changes-fail-to-propagate"></a>Z důvodů, které se nepodařilo šíření změn

Změny se nemusí podařit šířit z jednoho z následujících důvodů:

-   Nekompatibilita schématu/datového typu.
-   Vkládání do sloupce nepřipouštějící hodnotu null.
-   Porušení omezení cizího klíče.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Co se stane, když dojde k selhání změny rozšíření?

-   Synchronizovat skupiny odhalí se **upozornění** stavu.
-   Podrobnosti jsou uvedené v portálu log vieweru uživatelského rozhraní.
-   Pokud se problém nevyřeší 45 dní, se změní aktuální databázi.

> [!NOTE]
> Tyto změny se nikdy rozšíří. Jediný způsob, jak obnovit v tomto scénáři je znovu vytvořit skupinu synchronizace.

#### <a name="recommendation"></a>Doporučení

Monitorování stavu skupiny a databáze synchronizace pravidelně prostřednictvím rozhraní portálu a protokolu.


## <a name="maintenance"></a>Údržba

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Vyhněte se zastaralý databází a synchronizovat skupiny

Skupina synchronizace nebo databáze ve skupině synchronizace může být zastaralá. Pokud je stav skupiny synchronizace **zastaralé**, přestane fungovat. Pokud je stav vaší databáze **zastaralé**, data budou ztracena. Doporučujeme, aby tento scénář namísto pokusu o obnovení z něj.

#### <a name="avoid-out-of-date-databases"></a>Vyhněte se zastaralý databází

Stav databáze je nastaven na **zastaralé** při jejím offline pro 45 dnů nebo déle. Aby se zabránilo **zastaralé** stav na databázi, zajistěte, aby žádná z databází byly offline pro 45 dnů nebo déle.

#### <a name="avoid-out-of-date-sync-groups"></a>Vyhněte se skupiny synchronizace. zastaralé

Skupina synchronizace stav nastaven na **zastaralé** případě změny ve skupině synchronizace nezdaří rozšíří na zbytek skupiny synchronizace pro 45 dnů nebo déle. Aby se zabránilo **zastaralé** skupinu synchronizace historie protokolu pravidelně Kontrola stavu pro skupinu synchronizace. Ujistěte se, že jsou vyřešeny všechny konflikty. proto, že se změny úspěšně rozšíří v rámci databáze skupiny synchronizace.

Skupina synchronizace se nemusí podařit použít ke změně jednoho z těchto důvodů:

-   Schéma nekompatibility mezi tabulkami.
-   Data nekompatibility mezi tabulkami.
-   Vložení řádků s hodnotou null ve sloupci, který nepovoluje hodnoty null.
-   Aktualizuje se řádek s hodnotou, která porušuje omezení pro cizí klíč.

Aby se zabránilo skupiny synchronizace. aktuální:

-   Aktualizujte schéma pro povolení hodnot, které jsou obsaženy v řádcích se nezdařilo.
-   Aktualizujte hodnoty cizího klíče zahrnout hodnoty, které jsou obsaženy v řádcích se nezdařilo.
-   Aktualizujte hodnoty data v řádku selhání tak, aby byly kompatibilní s schématu nebo cizí klíče v cílové databázi.

### <a name="avoid-deprovisioning-issues"></a> Vyhněte se zrušení zřízení problémy

V některých případech zrušení registrace databáze s Klientský agent může dojít k selhání synchronizace.

#### <a name="scenario"></a>Scénář

1. Skupina synchronizace A byl vytvořen pomocí instance SQL Database a k místní databázi SQL serveru, který je přidružený k místní agent je 1.
2. Místní agent 2 (Tento agent není přidružené žádné skupiny synchronizace) je zaregistrován stejné místní databáze.
3. Zrušení registrace v místní databázi z místní agent 2 odebere sledování a synchronizovat metadata tabulky pro skupiny A pro místní databáze.
4. Operace A skupiny synchronizace selhat s touto chybou: "Aktuální operaci nelze dokončit, protože databáze není zřízený pro synchronizaci, nebo nemáte oprávnění k tabulkám, konfigurace synchronizace."

#### <a name="solution"></a>Řešení

Abyste předešli této situaci, nezaregistrujete databáze s více než jednoho agenta.

Obnovit z tohoto scénáře:

1. Odebrání všech skupin synchronizace, který patří do databáze.  
2. Přidejte databázi zpět do všech skupin synchronizace, který jste odebrali z.  
3. Nasazení všech skupin ovlivněné synchronizace (Tato akce zřizuje databázi).  

### <a name="modifying-your-sync-group"></a> Úprava skupiny synchronizace

Nepokoušejte se odebrat databázi ze skupiny synchronizace a pak upravte skupinu synchronizace bez první nasazování změn.

Nejprve odeberte místo toho databázi ze skupiny synchronizace. Potom nasaďte změnu a počkejte na dokončení zrušení zřízení. Po dokončení zrušení zřízení můžete upravit skupinu synchronizace a změny nasazení.

Při pokusu o odebrání databáze a potom upravit skupinu synchronizace bez první nasazování změn, jeden nebo jiné operace selže. Rozhraní portálu může být nekonzistentní. Pokud k tomu dojde, aktualizujte stránku k obnovení stavu správné.

## <a name="next-steps"></a>Další postup
Další informace o synchronizaci dat SQL najdete v tématu:

-   Přehled – [synchronizaci dat napříč několika cloudu a místními databázemi pomocí synchronizace dat SQL Azure](sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurzu: Nastavení synchronizace dat SQL, synchronizaci dat mezi Azure SQL Database a SQL Server v místním](sql-database-get-started-sql-data-sync.md)
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent – synchronizace dat [Data synchronizovat Agent pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)
-   Monitorování – [monitorování synchronizace dat SQL s využitím Log Analytics](sql-database-sync-monitor-oms.md)
-   Řešení potíží – [řešení potíží se synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)
-   Aktualizovat schéma synchronizace
    -   Pomocí příkazů jazyka Transact-SQL - [automatizace replikace změn schématu synchronizace dat SQL Azure](sql-database-update-sync-schema.md)
    -   Pomocí Powershellu – [aktualizovat schéma synchronizace ve stávající skupině synchronizace pomocí Powershellu](scripts/sql-database-sync-update-schema.md)

Další informace o službě SQL Database najdete v tématu:

-   [Přehled SQL Database](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
