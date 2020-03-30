---
title: Automatizace replikace změn schématu v synchronizaci dat SQL
description: Zjistěte, jak automatizovat replikaci změn schématu v Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/14/2018
ms.openlocfilehash: 639901975bbb66b9f410bea297d9e48cd96d6d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822437"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatizace replikace změn schématu v azure SQL data sync

Synchronizace dat SQL umožňuje uživatelům synchronizovat data mezi databázemi Azure SQL a místním SQL Serverem v jednom směru nebo v obou směrech. Jedním z aktuálních omezení synchronizace dat SQL je nedostatečná podpora replikace změn schématu. Pokaždé, když změníte schéma tabulky, budete muset použít změny ručně na všechny koncové body, včetně rozbočovače a všechny členy a potom aktualizovat schéma synchronizace.

Tento článek představuje řešení pro automatickou replikaci změn schématu do všech koncových bodů synchronizace dat SQL.
1. Toto řešení používá aktivační událost DDL ke sledování změn schématu.
1. Aktivační událost vloží příkazy změny schématu do tabulky sledování.
1. Tato tabulka sledování se synchronizuje se všemi koncovými body pomocí služby Synchronizace dat.
1. Aktivační události DML po vložení se používají k použití změn schématu na ostatní koncové body.

Tento článek používá ALTER TABLE jako příklad změny schématu, ale toto řešení funguje také pro jiné typy změn schématu.

> [!IMPORTANT]
> Doporučujeme, abyste si pozorně přečetli tento článek, zejména části o [řešení potíží](#troubleshoot) a [další aspekty](#other), než začnete implementovat automatické replikace změn schématu v synchronizačním prostředí. Doporučujeme také, abyste si [přečetli data synchronizace ve více cloudových a místních databázích pomocí synchronizace dat SQL](sql-database-sync-data.md). Některé databázové operace může přerušit řešení popsané v tomto článku. Další znalost domény SQL Server a Transact-SQL může být nutné k řešení těchto problémů.

![Automatizace replikace změn schématu](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Nastavení automatické replikace změn schématu

### <a name="create-a-table-to-track-schema-changes"></a>Vytvoření tabulky pro sledování změn schématu

Vytvořte tabulku pro sledování změn schématu ve všech databázích ve skupině synchronizace:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Tato tabulka obsahuje sloupec identity pro sledování pořadí změn schématu. V případě potřeby můžete přidat další pole pro protokolování dalších informací.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Vytvoření tabulky pro sledování historie změn schématu

Ve všech koncových bodech vytvořte tabulku pro sledování ID naposledy použitého příkazu změny schématu.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Vytvoření aktivační události ALTER TABLE DDL v databázi, kde jsou provedeny změny schématu

Vytvořte aktivační událost DDL pro operace ALTER TABLE. Tuto aktivační událost je třeba vytvořit pouze v databázi, kde jsou provedeny změny schématu. Chcete-li se vyhnout konfliktům, povolte pouze změny schématu v jedné databázi ve skupině synchronizace.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

Aktivační událost vloží záznam do tabulky sledování změn schématu pro každý příkaz ALTER TABLE. Tento příklad přidá filtr, aby se zabránilo replikaci změny schématu provedené v rámci schématu **DataSync**, protože tyto jsou s největší pravděpodobností provedené službou Synchronizace dat. Další filtry přidejte, pokud chcete replikovat pouze určité typy změn schématu.

Můžete také přidat další aktivační události replikovat jiné typy změn schématu. Můžete například vytvořit CREATE_PROCEDURE, ALTER_PROCEDURE a DROP_PROCEDURE aktivační události replikovat změny uložené procedury.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Vytvoření aktivační události u jiných koncových bodů pro použití změn schématu během vložení

Tato aktivační událost spustí příkaz změny schématu při synchronizaci s jinými koncovými body. Tuto aktivační událost je třeba vytvořit ve všech koncových bodech, s výjimkou té, kde jsou provedeny `AlterTableDDLTrigger` změny schématu (to znamená v databázi, kde je aktivační událost DDL vytvořena v předchozím kroku).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Tato aktivační událost se spustí po vložení a zkontroluje, zda má být aktuální příkaz spuštěn jako další. Logika kódu zajišťuje, že není přeskočen žádný příkaz změny schématu a všechny změny jsou použity i v případě, že vložení je mimo pořadí.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synchronizace tabulky sledování změn schématu se všemi koncovými body

Tabulku sledování změn schématu můžete synchronizovat se všemi koncovými body pomocí existující skupiny synchronizace nebo nové skupiny synchronizace. Ujistěte se, že změny v tabulce sledování lze synchronizovat do všech koncových bodů, zejména při použití synchronizace jedním směrem.

Nesynchronizujte tabulku historie změn schématu, protože tato tabulka udržuje jiný stav v různých koncových bodech.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Použití změn schématu ve skupině synchronizace

Replikují se pouze změny schématu provedené v databázi, kde je vytvořena aktivační událost DDL. Změny schématu provedené v jiných databázích nejsou replikovány.

Po změny schématu jsou replikovány do všech koncových bodů, je také nutné provést další kroky k aktualizaci schématu synchronizace spustit nebo zastavit synchronizaci nových sloupců.

#### <a name="add-new-columns"></a>Přidání nových sloupců

1.  Proveďte změnu schématu.

1.  Vyhněte se jakékoli změně dat, kde se jedná o nové sloupce, dokud nedokončíte krok, který vytvoří aktivační událost.

1.  Počkejte, dokud změny schématu se použijí na všechny koncové body.

1.  Aktualizujte schéma databáze a přidejte nový sloupec do schématu synchronizace.

1.  Data v novém sloupci se synchronizují během další operace synchronizace.

#### <a name="remove-columns"></a>Odebrání sloupců

1.  Odeberte sloupce ze schématu synchronizace. Synchronizace dat zastaví synchronizaci dat v těchto sloupcích.

1.  Proveďte změnu schématu.

1.  Aktualizujte schéma databáze.

#### <a name="update-data-types"></a>Aktualizace datových typů

1.  Proveďte změnu schématu.

1.  Počkejte, dokud změny schématu se použijí na všechny koncové body.

1.  Aktualizujte schéma databáze.

1.  Pokud nové a staré datové typy nejsou plně kompatibilní – `int` `bigint` například pokud změníte z na - synchronizace může selhat před kroky, které vytvářejí aktivační události jsou dokončeny. Synchronizace proběhne úspěšně po opakování.

#### <a name="rename-columns-or-tables"></a>Přejmenování sloupců nebo tabulek

Přejmenování sloupců nebo tabulek způsobí, že synchronizace dat přestane fungovat. Vytvořte novou tabulku nebo sloupec, vyplníte data a místo přejmenování odstraníte starou tabulku nebo sloupec.

#### <a name="other-types-of-schema-changes"></a>Jiné typy změn schématu

Pro jiné typy změn schématu – například vytváření uložených procedur nebo uvolnění indexu – není aktualizace schématu synchronizace vyžadována.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a>Poradce při potížích s automatickou replikací změn schématu

Logika replikace popsané v tomto článku přestane fungovat v některých situacích– například pokud jste provedli změnu schématu v místní databázi, která není podporována v Azure SQL Database. V takovém případě se nezdaří synchronizace tabulky sledování změn schématu. Tento problém je třeba vyřešit ručně:

1.  Zakažte aktivační událost DDL a vyhněte se dalším změnám schématu, dokud nebude problém vyřešen.

1.  V databázi koncového bodu, kde k problému dochází, zakažte aktivační událost AFTER INSERT v koncovém bodě, kde nelze provést změnu schématu. Tato akce umožňuje synchronizovat příkaz změny schématu.

1.  Aktivace synchronizace tabulky sledování změn schématu

1.  V databázi koncového bodu, kde k problému dochází, dotaz tabulka historie změny schématu získat ID poslední použité schéma změnit příkaz.

1.  Dotaz na tabulku sledování změn schématu zobrazí seznam všech příkazů s ID větším, než je hodnota ID, kterou jste načetli v předchozím kroku.

    a.  Ignorujte ty příkazy, které nelze provést v databázi koncových bodů. Musíte se vypořádat s nekonzistencí schématu. Vrátit původní změny schématu, pokud nekonzistence ovlivňuje vaši aplikaci.

    b.  Ručně použít ty příkazy, které by měly být použity.

1.  Aktualizujte tabulku historie změn schématu a nastavte poslední aplikované ID na správnou hodnotu.

1.  Zkontrolujte, zda je schéma aktuální.

1.  Znovu povolte aktivační událost AFTER INSERT, která je ve druhém kroku zakázána.

1.  Znovu povolte aktivační událost DDL zakázáno v prvním kroku.

Pokud chcete vyčistit záznamy v tabulce sledování změn schématu, použijte místo Funkce Zkrátit klávesu DELETE. Nikdy reseed sloupec identity v tabulce sledování změny schématu pomocí DBCC CHECKIDENT. Můžete vytvořit nové tabulky sledování změn schématu a aktualizovat název tabulky v aktivační události DDL, pokud je vyžadováno prosazení.

## <a name="other-considerations"></a><a name="other"></a>Další aspekty

-   Uživatelé databáze, kteří konfigurují databáze rozbočovače a členů, musí mít dostatečná oprávnění ke spuštění příkazů pro změnu schématu.

-   Do aktivační události DDL můžete přidat další filtry, které budou replikovat pouze změnu schématu ve vybraných tabulkách nebo operacích.

-   Změny schématu lze provádět pouze v databázi, kde je vytvořena aktivační událost DDL.

-   Pokud provádíte změnu v místní databázi SQL Serveru, ujistěte se, že změna schématu je podporována v Azure SQL Database.

-   Pokud jsou provedeny změny schématu v databázích než v databázi, kde je vytvořena aktivační událost DDL, změny nejsou replikovány. Chcete-li se tomuto problému vyhnout, můžete vytvořit aktivační události DDL blokovat změny na jiných koncových bodech.

-   Pokud potřebujete změnit schéma tabulky sledování změn schématu, zakažte aktivační událost DDL před změnou a potom ručně aplikujte změnu na všechny koncové body. Aktualizace schématu v aktivační události AFTER INSERT ve stejné tabulce nefunguje.

-   Nepoužívejte reseed sloupec identity pomocí DBCC CHECKIDENT.

-   Nepoužívejte Funkce Zkrátit k vyčištění dat v tabulce sledování změn schématu.

## <a name="next-steps"></a>Další kroky

Další informace o Synchronizaci dat SQL:

-   Přehled – [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Azure SQL Data Sync](sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: Nastavení synchronizace dat SQL pro synchronizaci dat mezi databází Azure SQL a SQL Server em i v místním prostředí](sql-database-get-started-sql-data-sync.md)
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizace dat – [agent synchronizace dat pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)
-   Doporučené postupy – [doporučené postupy pro Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Monitor – [monitorování synchronizace dat SQL pomocí protokolů Azure Monitoru](sql-database-sync-monitor-oms.md)
-   Poradce při potížích – [řešení problémů se synchronizací dat Azure SQL](sql-database-troubleshoot-data-sync.md)
-   Aktualizace schématu synchronizace
    -   S PowerShellem – [k aktualizaci schématu synchronizace v existující skupině synchronizace použijte PowerShell](scripts/sql-database-sync-update-schema.md)
