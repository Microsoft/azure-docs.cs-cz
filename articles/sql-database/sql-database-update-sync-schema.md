---
title: Automatizace replikace změn schématu synchronizace dat SQL Azure | Dokumentace Microsoftu
description: Zjistěte, jak automatizovat replikaci změn schématu synchronizace dat SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 11/14/2018
ms.openlocfilehash: 058c759e77c0d9cab3bd9a114a2d22606046390e
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563316"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatizace replikace změn schématu synchronizace dat SQL Azure

Synchronizace dat SQL umožňuje uživatelům synchronizovat data mezi databázemi Azure SQL a místním SQL serverem v jednom směru nebo v obou směrech. Jednou z aktuálních omezení synchronizace dat SQL je chybějící podpora jazyků replikace změn schématu. Pokaždé, když změníte schéma tabulky, budete muset použít změny ručně pro všechny koncové body, včetně centra a všechny členy a potom aktualizovat schéma synchronizace.

Tento článek představuje řešení, které automaticky replikovat změny schématu pro všechny koncové body synchronizace dat SQL.
1. Toto řešení používá ke sledování změn schématu aktivační událost jazyka DDL.
1. Aktivační událost vloží příkazy změna schématu tabulky sledování.
1. Tato tabulka sledování se synchronizuje s všechny koncové body pomocí synchronizace dat služby.
1. Triggery DML po vložení se používají k použití změn schématu v dalších koncových bodů.

Tento článek používá příkaz ALTER TABLE s ukázkovým změnu schématu, ale toto řešení funguje i pro jiné typy změn schématu.

> [!IMPORTANT]
> Doporučujeme, abyste si přečetli to článek pečlivě, zejména v částech o [Poradce při potížích s](#troubleshoot) a [další důležité informace](#other), než začnete provádět automatizované schématu replikace změn v prostředí synchronizace. Doporučujeme také, abyste si přečetli [synchronizaci dat napříč několika cloudu a místními databázemi pomocí synchronizace dat SQL](sql-database-sync-data.md). Některé operace databáze způsobit nefunkčnost řešení popsaných v tomto článku. Další znalosti SQL serveru a jazyka Transact-SQL, může být nutné tyto problémy vyřešit.

![Automatizace replikace změn schématu](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Nastavení replikace změnit automatizované schématu

### <a name="create-a-table-to-track-schema-changes"></a>Vytvoření tabulky sledování změn schématu

Vytvořte tabulku pro sledování změn schématu ve všech databází ve skupině synchronizace:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Tato tabulka obsahuje sloupec identity ke sledování pořadí změny schématu. Můžete přidat další pole do protokolu informace v případě potřeby.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Vytvořte tabulku pro sledovat historii změn schématu

Pro všechny koncové body vytvořte tabulku pro sledování ID příkazu nedávno použité změnu schématu.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Vytvoření triggeru příkaz ALTER TABLE DDL v databázi, pokud dojde ke změně schématu

Vytvořte aktivační událost jazyka DDL pro operace ALTER TABLE. Potřebujete jenom k vytvoření této aktivační události v databázi, pokud dojde ke změně schématu. Aby nedocházelo ke konfliktům, povolit pouze změny schématu v jedné databázi ve skupině synchronizace.

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

Aktivační událost vloží záznam v tabulce pro každý příkaz ALTER TABLE sledování změn schémat. V tomto příkladu přidá filtr, aby replikace změn schématu provedených v rámci schématu **DataSync**, protože ty budou s největší pravděpodobností provedené při synchronizaci dat služby. Pokud chcete replikovat určité typy změn schématu, přidáte další filtry.

Můžete také přidat zobrazíte další aktivační události pro replikaci jiné typy změn schématu. Například vytvoření aktivační události CREATE_PROCEDURE, ALTER_PROCEDURE a DROP_PROCEDURE replikovat změny uložené procedury.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Vytvoření aktivační události na ostatní koncové body k použití změn schématu při vložení

Tato aktivační událost provede příkaz změnu schématu při se synchronizují do dalších koncových bodů. Je potřeba vytvořit tento aktivační událost pro všechny koncové body, s výjimkou toho, kdy dojde ke změně schématu (to znamená, že v databázi, ve kterém jazyka DDL aktivovat `AlterTableDDLTrigger` vytvořili v předchozím kroku).

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

Tato aktivační událost se spustí po vložení a zkontroluje, zda má aktuální příkaz spustit další. Logika kódu zajistí, že žádný příkaz změny schématu se přeskočilo, a všechny změny se použijí i v případě, že je kurzor na místo mimo pořadí.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synchronizovat změny schématu tabulky sledování pro všechny koncové body

Můžete synchronizovat sledování tabulku pro všechny koncové body pomocí stávající skupině synchronizace nebo nová skupina synchronizace změn schématu. Ujistěte se, že změny v tabulce sledování se synchronizují všechny koncové body, zvláště když používáte jeden směr synchronizace.

Nesynchronizují tabulky historie změn schématu, protože danou tabulku udržuje jiný stav na různých koncových bodů.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Použít změny schématu ve skupině synchronizace

Se replikují jenom změny schématu provedených v databázi, kde se vytvoří aktivační událost jazyka DDL. Nebudou se replikovat změn schématu provedených v jiných databázích.

Po změny schématu se replikují do všech koncových bodů, musíte také provést další kroky a aktualizovat schéma synchronizace, spuštění nebo zastavení synchronizace nové sloupce.

#### <a name="add-new-columns"></a>Přidat nové sloupce

1.  Ujistěte se, změňte schéma.

1.  Vyhněte se všechny změny dat, ve kterém se podílejí nové sloupce, dokud nedokončíte krok, který vytvoří aktivační událost.

1.  Počkejte, dokud změny schématu se použijí pro všechny koncové body.

1.  Aktualizovat schéma databáze a přidat nový sloupec do schématu synchronizace.

1.  Data v tomto novém sloupci se synchronizuje během další operace synchronizace.

#### <a name="remove-columns"></a>Odebrání sloupců

1.  Odeberte sloupce ze schématu synchronizace. Synchronizace dat se zastaví synchronizují se data v těchto sloupcích.

1.  Ujistěte se, změňte schéma.

1.  Aktualizujte schéma databáze.

#### <a name="update-data-types"></a>Aktualizace datové typy

1.  Ujistěte se, změňte schéma.

1.  Počkejte, dokud změny schématu se použijí pro všechny koncové body.

1.  Aktualizujte schéma databáze.

1.  Pokud novém i starém datové typy, které nejsou plně kompatibilní – například pokud změníte z `int` k `bigint` – synchronizace mohou selhat, aby dokončení kroků, které vytvářejí aktivačních událostí. Synchronizace zdaří po zkuste to znovu.

#### <a name="rename-columns-or-tables"></a>Přejmenování sloupců nebo tabulek

Přejmenování sloupců nebo tabulek provede synchronizaci dat přestanou fungovat. Vytvořit novou tabulku nebo sloupec, obnovení dat data a pak odstraňte původní tabulky nebo sloupce místo přejmenování.

#### <a name="other-types-of-schema-changes"></a>Jiné typy změn schématu

U jiných typů změn schématu – například uložené procedury vytváří nebo vyřazuje indexu – aktualizace schématu synchronizace se nevyžaduje.

## <a name="troubleshoot"></a> Řešení potíží s replikace změn automatizované schématu

Logika replikace je popsáno v tomto článku přestane fungovat v některých situacích – například pokud jste schéma změna v místní databázi, což není podporováno ve službě Azure SQL Database. V takovém případě tabulky sledování změn schématu synchronizace se nezdaří. Tento problém potřebujete vyřešit ručně:

1.  Zakázat aktivační událost jazyka DDL a vyhnout se žádné další změny schématu, dokud nebude problém vyřešen.

1.  V databázi koncový bod, ve kterém dochází k problému zakažte aktivační události AFTER INSERT na koncový bod, ve kterém nelze provést změnu schématu. Tato akce umožní příkaz změny schématu se.

1.  Aktivovat synchronizaci pro synchronizaci tabulky sledování změn schémat.

1.  V databázi koncový bod, ve kterém dochází k problému změňte dotaz schématu tabulky historie k získání ID poslední příkaz změnu použité schéma.

1.  Dotaz na tabulku na seznam všech příkazů s ID větší než hodnota ID, že který jste získali v předchozím kroku sledování změn schémat.

    a.  Ignorujte příkazy, které nelze provést, v databázi koncový bod. Je potřeba řešit nekonzistence schématu. Vrátit zpět původní změny schématu, pokud nekonzistencí má vliv na vaše aplikace.

    b.  Příkazy, které bude použito aplikovat ručně.

1.  Aktualizovat tabulku historie změn schématu a ID poslední použité na správnou hodnotu.

1.  Zkontrolujte, zda je aktuální schéma.

1.  Znovu povolte trigger AFTER INSERT zakázaná v druhém kroku.

1.  Znovu povolte aktivační událost jazyka DDL zakázaná v prvním kroku.

Pokud chcete vyčistit záznamy v tabulce pro sledování změn schématu, použijte místo TRUNCATE odstranit. Nikdy reseed sloupec identity v řešení change tracking tabulky pomocí příkazu DBCC CHECKIDENT schématu. Můžete vytvořit novou změnu schématu sledovacích tabulek a pokud reseeding je potřeba aktualizovat název tabulky v aktivační událost jazyka DDL.

## <a name="other"></a> Další důležité informace

-   Uživatelé databáze, kteří konfigurují databáze centra a člen musí mít dostatečná oprávnění ke spuštění příkazů změnu schématu.

-   V aktivační událost jazyka DDL pouze replikovat změny schématu vybrané tabulky nebo operace můžete přidat další filtry.

-   Změny schématu lze vytvořit pouze v databázi, kde se vytvoří aktivační událost jazyka DDL.

-   Jestliže provádíte změnu v databázi SQL serveru v místním, ujistěte se, že změny schématu se podporuje ve službě Azure SQL Database.

-   Pokud dojde ke změně schématu v databázích než databáze, kde se vytvoří aktivační událost jazyka DDL, změny se replikují. K tomuto problému vyhnout, můžete vytvořit aktivační procedury DDL zablokovat změny v ostatních koncových bodů.

-   Pokud potřebujete změnit schéma tabulky sledování změn schématu, zakažte aktivační událost jazyka DDL před změnou a ručně tato změna platí pro všechny koncové body. Aktualizace schématu v aktivační událost po vložení ve stejné tabulce nefunguje.

-   Pomocí příkazu DBCC CHECKIDENT není reseed sloupec identity.

-   Nepoužívejte TRUNCATE vyčistit data v tabulce pro sledování změn schémat.

## <a name="next-steps"></a>Další postup

Další informace o Synchronizaci dat SQL:

-   Přehled – [synchronizaci dat napříč několika cloudu a místními databázemi pomocí synchronizace dat SQL Azure](sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurzu: Nastavení synchronizace dat SQL, synchronizaci dat mezi Azure SQL Database a SQL Server v místním](sql-database-get-started-sql-data-sync.md)
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent – synchronizace dat [Data synchronizovat Agent pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)
-   Osvědčené postupy – [osvědčené postupy pro synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)
-   Monitorování – [monitorování synchronizace dat SQL s využitím Log Analytics](sql-database-sync-monitor-oms.md)
-   Řešení potíží – [řešení potíží se synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)
-   Aktualizovat schéma synchronizace
    -   Pomocí Powershellu – [aktualizovat schéma synchronizace ve stávající skupině synchronizace pomocí Powershellu](scripts/sql-database-sync-update-schema.md)
