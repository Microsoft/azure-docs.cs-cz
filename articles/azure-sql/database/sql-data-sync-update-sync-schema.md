---
title: Automatizujte replikaci změn schématu v Synchronizace dat SQL
description: Naučte se automatizovat replikaci změn schématu v Azure Synchronizace dat SQL.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/14/2018
ms.openlocfilehash: f9997ea737b96185b31a7f51996a396fb5fc46c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790181"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatizace replikace změn schématu v Azure Synchronizace dat SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Synchronizace dat SQL umožňuje uživatelům synchronizovat data mezi databázemi v Azure SQL Database a instancích SQL Server v jednom směru nebo v obou směrech. Jedním z aktuálních omezení Synchronizace dat SQL je nedostatek podpory pro replikaci změn schématu. Pokaždé, když změníte schéma tabulky, je nutné použít změny ručně u všech koncových bodů, včetně rozbočovače a všech členů, a pak aktualizovat schéma synchronizace.

Tento článek představuje řešení, které automaticky replikuje změny schématu do všech koncových bodů Synchronizace dat SQL.

1. Toto řešení používá ke sledování změn schématu TRIGGER DDL.
1. Aktivační událost vloží příkazy pro změnu schématu do sledovací tabulky.
1. Tato sledovací tabulka je synchronizovaná se všemi koncovými body pomocí služby synchronizace dat.
1. Příkazy DML po vložení slouží k aplikování změn schématu na ostatních koncových bodech.

Tento článek používá jako příklad změny schématu příkaz ALTER TABLE, ale toto řešení funguje i pro jiné typy změn schématu.

> [!IMPORTANT]
> Doporučujeme, abyste si tento článek důkladně přečetli, zejména v oddílech týkajících se [řešení potíží](#troubleshoot) a [dalších informací](#other), než začnete implementovat automatickou replikaci změn schématu v synchronizačním prostředí. Doporučujeme také číst [data synchronizace napříč několika cloudy a místními databázemi pomocí synchronizace dat SQL](sql-data-sync-data-sql-server-sql-database.md). Některé databázové operace mohou poškodit řešení popsané v tomto článku. K řešení těchto problémů může být potřeba další znalosti v doméně SQL Server a Transact-SQL.

![Automatizace replikace změn schématu](./media/sql-data-sync-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Nastavte automatickou replikaci změn schématu.

### <a name="create-a-table-to-track-schema-changes"></a>Vytvoření tabulky pro sledování změn schématu

Vytvořte tabulku pro sledování změn schématu ve všech databázích ve skupině synchronizace:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Tato tabulka obsahuje sloupec identity, který sleduje pořadí změn schématu. V případě potřeby můžete přidat další pole pro protokolování dalších informací.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Vytvoření tabulky pro sledování historie změn schématu

U všech koncových bodů vytvořte tabulku pro sledování ID naposledy použitého příkazu pro změnu schématu.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Vytvoření triggeru příkazu DDL tabulky v databázi, kde jsou provedeny změny schématu

Vytvořte TRIGGER DDL pro operace ALTER TABLE. Tuto aktivační událost stačí vytvořit pouze v databázi, kde jsou provedeny změny schématu. Aby nedocházelo ke konfliktům, povolte pouze změny schématu v jedné databázi ve skupině synchronizace.

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

Aktivační událost vloží záznam do tabulky sledování změn schématu pro každý příkaz ALTER TABLE. Tento příklad přidá filtr, aby se zabránilo replikaci změn schématu provedených v rámci schématu data **Sync**, protože tyto změny jsou pravděpodobně provedeny službou synchronizace dat. Pokud chcete replikovat pouze určité typy změn schématu, přidejte další filtry.

Můžete také přidat další aktivační události pro replikaci jiných typů změn schématu. Můžete například vytvořit CREATE_PROCEDURE, ALTER_PROCEDURE a DROP_PROCEDURE triggery pro replikaci změn uložených procedur.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Vytvořit Trigger na jiných koncových bodech pro použití změn schématu během vložení

Tato aktivační událost spustí příkaz změny schématu při synchronizaci do jiných koncových bodů. Tuto aktivační událost je nutné vytvořit ve všech koncových bodech s výjimkou toho, kde jsou provedeny změny schématu (tj. v databázi, kde je v `AlterTableDDLTrigger` předchozím kroku vytvořen TRIGGER DDL).

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

Tato aktivační událost se spustí po vložení a zkontroluje, jestli se má aktuální příkaz Spustit jako další. Logika kódu zajišťuje, že žádný příkaz ke změně schématu není vynechán, a všechny změny jsou aplikovány i v případě, že vložení je mimo pořadí.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synchronizovat tabulku sledování změn schématu se všemi koncovými body

Tabulku sledování změn schématu můžete synchronizovat se všemi koncovými body pomocí existující skupiny synchronizace nebo nové skupiny synchronizace. Ujistěte se, že změny v tabulce sledování mohou být synchronizovány do všech koncových bodů, zejména v případě, že používáte jednu směrovou synchronizaci.

Nesynchronizuje tabulku historie změn schématu, protože tato tabulka udržuje různý stav na různých koncových bodech.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Použít změny schématu ve skupině synchronizace

Replikují se pouze změny schématu provedené v databázi, kde je vytvořen TRIGGER DDL. Změny schématu provedené v jiných databázích se nereplikují.

Po dokončení replikace změn schématu do všech koncových bodů je také nutné provést další kroky k aktualizaci schématu synchronizace, aby bylo možné spustit nebo zastavit synchronizaci nových sloupců.

#### <a name="add-new-columns"></a>Přidat nové sloupce

1.  Proveďte změnu schématu.

1.  Vyhněte se změnám dat, kde jsou tyto nové sloupce zapojeny, dokud nedokončíte krok, který aktivační událost vytvořil.

1.  Počkejte, dokud nebudou změny schématu aplikovány na všechny koncové body.

1.  Aktualizujte schéma databáze a přidejte nový sloupec do schématu synchronizace.

1.  Data v novém sloupci jsou synchronizovaná během další operace synchronizace.

#### <a name="remove-columns"></a>Odebrání sloupců

1.  Odeberte sloupce ze schématu synchronizace. Synchronizace dat zastaví synchronizaci dat v těchto sloupcích.

1.  Proveďte změnu schématu.

1.  Aktualizuje schéma databáze.

#### <a name="update-data-types"></a>Aktualizace datových typů

1.  Proveďte změnu schématu.

1.  Počkejte, dokud nebudou změny schématu aplikovány na všechny koncové body.

1.  Aktualizuje schéma databáze.

1.  Pokud nové a staré datové typy nejsou plně kompatibilní – například pokud změníte z `int` typu na synchronizaci, může se `bigint` stát, že se dokončí kroky pro vytvoření aktivačních událostí. Po opakovaném pokusu synchronizace proběhne úspěšně.

#### <a name="rename-columns-or-tables"></a>Přejmenování sloupců nebo tabulek

Při přejmenování sloupců nebo tabulek se synchronizace dat přestane fungovat. Vytvořte novou tabulku nebo sloupec, data vyplňte a místo přejmenování odstraňte starou tabulku nebo sloupec.

#### <a name="other-types-of-schema-changes"></a>Jiné typy změn schématu

Pro jiné typy změn schématu – například vytváření uložených procedur nebo odstranění indexu – aktualizace schématu synchronizace není vyžadována.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a> Řešení potíží s automatickou replikací změn schématu

Logika replikace popsaná v tomto článku v některých situacích přestane fungovat – například pokud jste provedli změnu schématu v místní databázi, která není v Azure SQL Database podporovaná. V takovém případě se synchronizace tabulky sledování změn schématu nezdařila. Tento problém je potřeba vyřešit ručně:

1.  Zakažte TRIGGER DDL a zabraňte jakýmkoli dalším změnám schématu, dokud problém nebude vyřešen.

1.  V databázi koncového bodu, kde se problém děje, zakažte aktivační událost po vložení na koncovém bodu, kde nelze provést změnu schématu. Tato akce umožňuje synchronizaci příkazu změny schématu.

1.  Spusťte synchronizaci pro synchronizaci tabulky sledování změn schématu.

1.  V databázi koncových bodů, kde se problém děje, zadejte dotaz na tabulku historie změn schématu, abyste získali ID posledního použitého příkazu pro změnu schématu.

1.  Pomocí dotazu na tabulku sledování změn schématu vypíšete všechny příkazy s ID větší než hodnota ID, kterou jste získali v předchozím kroku.

    a.  Ignorujte tyto příkazy, které se nedají spustit v databázi koncových bodů. Musíte se zabývat nekonzistencí schématu. Vrátí původní změny schématu, pokud nekonzistence ovlivní vaši aplikaci.

    b.  Ručně použijte tyto příkazy, které se mají použít.

1.  Aktualizujte tabulku historie změn schématu a nastavte poslední použité ID na správnou hodnotu.

1.  Dvakrát ověřte, zda je schéma aktuální.

1.  V druhém kroku znovu povolte aktivační událost po vložení zakázané.

1.  V prvním kroku znovu povolte TRIGGER DDL zakázaný.

Chcete-li vyčistit záznamy v tabulce sledování změn schématu, použijte příkaz DELETE místo ZKRÁCENí. Nikdy neměňte základ sloupce identity v tabulce sledování změn schématu pomocí DBCC CHECKIDENT. Pokud je požadováno opětovné osazení, můžete vytvořit nové tabulky sledování změn schématu a aktualizovat název tabulky v triggeru DDL.

## <a name="other-considerations"></a><a name="other"></a> Další požadavky

-   Uživatelé databáze, kteří konfigurují centrum a členské databáze, musí mít dostatečná oprávnění ke spuštění příkazů změny schématu.

-   Do triggeru DDL můžete přidat další filtry, aby se změny schématu prováděly jenom ve vybraných tabulkách nebo operacích.

-   Změny schématu lze provádět pouze v databázi, ve které je vytvořen TRIGGER DDL.

-   Pokud provádíte změnu v databázi SQL Server, ujistěte se, že je změna schématu v Azure SQL Database podporovaná.

-   Pokud se změny schématu provádějí v databázích, které nejsou v databázi, ve které je vytvořený TRIGGER DDL, změny se nereplikují. Chcete-li se tomuto problému vyhnout, můžete vytvořit triggery DDL pro blokování změn v jiných koncových bodech.

-   Pokud potřebujete změnit schéma tabulky sledování změn schématu, před provedením změny zakažte TRIGGER DDL a pak tuto změnu použijte ručně u všech koncových bodů. Aktualizace schématu v rámci triggeru vložení ve stejné tabulce nefunguje.

-   Nepoužívejte znovu základ sloupce identity pomocí DBCC CHECKIDENT.

-   Nepoužívejte zkrátit k vyčištění dat v tabulce sledování změn schématu.

## <a name="next-steps"></a>Další kroky

Další informace o Synchronizaci dat SQL:

-   Přehled – [synchronizace dat napříč několika cloudy a místními databázemi pomocí Azure synchronizace dat SQL](sql-data-sync-data-sql-server-sql-database.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: nastavení synchronizace dat SQL pro synchronizaci dat mezi Azure SQL Database a SQL Server](sql-data-sync-sql-server-configure.md)
    - S využitím PowerShellu
        -  [Použití PowerShellu k synchronizaci mezi několika databázemi v Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi databází v Azure SQL Database a databází v instanci SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Agent synchronizace dat – [Agent synchronizace dat pro Azure synchronizace dat SQL](sql-data-sync-agent-overview.md)
-   Osvědčené postupy – [osvědčené postupy pro Azure synchronizace dat SQL](sql-data-sync-best-practices.md)
-   Monitorování – [monitorování synchronizace dat SQL pomocí protokolů Azure monitor](./monitor-tune-overview.md)
-   Řešení potíží – [řešení potíží s Azure synchronizace dat SQL]()
-   Aktualizace schématu synchronizace
    -   Prostředí PowerShell – [použití PowerShellu k aktualizaci schématu synchronizace v existující skupině synchronizace](scripts/update-sync-schema-in-sync-group.md)