---
title: Automatizovat replikaci změny schématu v synchronizaci dat SQL Azure | Microsoft Docs
description: Informace o automatizaci replikace změny schématu v synchronizaci dat SQL Azure.
services: sql-database
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: a39e060708514fdca11a5d89858486b442a18309
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019583"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatizovat replikaci změny schématu v synchronizaci dat SQL Azure

Synchronizaci dat SQL umožňuje uživatelům synchronizovat data mezi databázemi SQL Azure a místní systém SQL Server v jednom směru nebo v obou směrech. Jedním z aktuálních omezení synchronizaci dat SQL je chybějících podpory pro replikaci změny schématu. Pokaždé, když změníte schématu tabulky, budete muset použít změny na všechny koncové body, včetně rozbočovače a všechny členy, ručně a pak aktualizujte schématu synchronizace.

Tento článek představuje řešení automaticky replikovat změny schématu pro všechny koncové body synchronizaci dat SQL.
1. Toto řešení používá aktivační událost jazyka DDL sledovat změny schématu.
2. Aktivační událost vloží příkazy změna schématu tabulky sledování.
3. Toto sledování tabulku se synchronizuje všechny koncové body pomocí synchronizaci dat služby.
4. Aktivační události DML po vložení se používá k aplikování změny schématu na dalších koncových bodů.

Tento článek používá příkaz ALTER TABLE jako příklad změny schématu, ale toto řešení funguje i pro jiné typy změny schématu.

> [!IMPORTANT]
> Doporučujeme, abyste si přečetli Tento článek pečlivě, zejména v částech o [Poradce při potížích s](#troubleshooting) a [další důležité informace](#other), než můžete začít provádět automatizované schématu replikace změn v prostředí synchronizace. Doporučujeme, abyste si přečetli [synchronizaci dat mezi několika databází cloudu a místně s synchronizaci dat SQL](sql-database-sync-data.md). Některé operace databáze je možné ukončit řešení popsaných v tomto článku. Další domény znalosti jazyka Transact-SQL a SQL serveru může být nutné tyto problémy vyřešit.

![Automatizaci replikace změny schématu](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Nastavení replikace změn automatizované schématu

### <a name="create-a-table-to-track-schema-changes"></a>Vytvořte tabulku pro sledování změn schématu

Vytvořte tabulku pro sledování změn schématu do všechny databáze ve skupině synchronizace:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Tato tabulka obsahuje sloupec identity ke sledování pořadí změny schématu. Můžete přidat další pole do protokolu informace v případě potřeby.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Vytvořte tabulku pro sledování historie změn schématu

U všech koncových bodů vytvořte tabulku pro sledování ID použité jako poslední příkaz změnu schématu.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Vytvořit aktivační událost INSTEAD ALTER DDL tabulky v databázi, ve kterém jsou provedeny změny schématu

Vytvořte aktivační událost jazyka DDL pro operace ALTER TABLE. Potřebujete vytvořit této aktivační události v databázi, ve kterém jsou provedeny změny schématu. Aby nedocházelo ke konfliktům, povolíte jenom změny schématu v jedna databáze ve skupině pro synchronizaci.

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

Aktivační událost vloží záznam v tabulce pro každý příkaz ALTER TABLE sledování změn schémat. Tento příklad přidá filtr předejdete replikace schématu změny provedené v schématu **DataSync**, protože se jedná nejpravděpodobnější, provedené synchronizaci dat služby. Pokud chcete replikovat určitých typů změny schématu, přidejte další filtry.

Můžete také přidat další aktivační události pro replikaci jiných typů změny schématu. Můžete například vytvořte CREATE_PROCEDURE, ALTER_PROCEDURE a DROP_PROCEDURE aktivační události k replikaci změn k uloženým procedurám.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Vytvořit aktivační událost na dalších koncových bodů během vkládání použít změny schématu

Této aktivační události provede příkaz změnu schématu, když je synchronizovat s další koncové body. Je potřeba vytvořit této aktivační události na všechny koncové body, s výjimkou toho, kde jsou provedeny změny schématu (to znamená, v databázi, kde DDL aktivovat `AlterTableDDLTrigger` je vytvořen v předchozím kroku).

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

Této aktivační události spustí po vložení a zkontroluje, zda se má aktuální příkaz spustit další. Kód logiku zajistí, že žádný příkaz změnu schématu bylo přeskočeno, a všechny změny se použijí, i když je vložení mimo pořadí.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synchronizovat změny schématu sledování tabulky pro všechny koncové body

Můžete synchronizovat sledování tabulky pro všechny koncové body pomocí stávající skupiny synchronizace nebo novou skupinu synchronizace změn schémat. Zajistěte, aby se že změny v tabulce sledování mohou být synchronizovány pro všechny koncové body, zejména v případě, že používáte jeden směr synchronizace.

Vzhledem k tomu, že tato tabulka udržuje jiný stav na různými koncovými body se nesynchronizují v tabulce historie změn schématu.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Použít změny schématu ve skupině pro synchronizaci

Se replikují jenom schématu změny provedené v databázi, kde se má vytvořit aktivační událost jazyka DDL. Schéma změny v ostatních databázích nejsou replikovány.

Po změny schématu se replikují do všech koncových bodů, musíte také provést další kroky pro aktualizaci schématu synchronizace spuštění nebo zastavení synchronizuje nové sloupce.

#### <a name="add-new-columns"></a>Přidat nové sloupce

1.  Ujistěte se, schéma změnit.

2.  Vyhněte se všechny změny dat, které se podílejí nové sloupce, dokud nedokončíte krok, který vytvoří aktivační událost.

3.  Počkejte, dokud změny schématu jsou použity na všechny koncové body.

4.  Aktualizujte schéma databáze a přidat nový sloupec schématu synchronizace.

5.  Během další operace synchronizace je synchronizovat data do nového sloupce.

#### <a name="remove-columns"></a>Odebrání sloupců

1.  Sloupce odeberte z schématu synchronizace. Synchronizaci dat zastaví synchronizuje data v těchto sloupcích.

2.  Ujistěte se, schéma změnit.

3.  Aktualizace schématu databáze.

#### <a name="update-data-types"></a>Aktualizace datové typy

1.  Ujistěte se, schéma změnit.

2.  Počkejte, dokud změny schématu jsou použity na všechny koncové body.

3.  Aktualizace schématu databáze.

4.  Pokud novém i starém datové typy nejsou plně kompatibilní – například pokud se změní z `int` k `bigint` -synchronizace mohou selhat, než dokončení kroků, které vytvářejí aktivačních událostí. Synchronizace se podaří po opakovaném pokusu.

#### <a name="rename-columns-or-tables"></a>Přejmenování sloupce nebo tabulky

Přejmenování sloupce nebo tabulky provede synchronizaci dat přestat pracovat. Vytvoření nové tabulky nebo sloupce, obnovení dat dat a pak odstraňte původní tabulky nebo sloupce místo přejmenování.

#### <a name="other-types-of-schema-changes"></a>Jiné typy změny schématu

U jiných typů změny schématu – například uložené procedury vytváří nebo vyřazuje indexu - aktualizace schématu synchronizace se nevyžaduje.

## <a name="troubleshoot"></a> Řešení potíží s replikace změn automatizované schématu

Replikace logiku popsané v tomto článku přestane fungovat v některých situacích – například pokud jste provedli schéma změnit v místní databázi, což není podporováno ve službě Azure SQL Database. V takovém případě synchronizuje tabulka sledování změn schématu se nezdaří. Tento problém je nutné opravit ručně:

1.  Zakázat aktivační událost jazyka DDL a vyhnout se žádné další změny schématu, dokud nebude problém vyřešen.

2.  V databázi koncový bod, kde se děje problém zakažte aktivační události AFTER INSERT v koncovém bodě, kde nelze provést změny schématu. Tato akce umožňuje příkaz změnu schématu možné synchronizovat.

3.  Spouštění synchronizace pro synchronizaci tabulky sledování změn schémat.

4.  V databázi koncový bod, kde se děje problém změnit dotaz schématu tabulky historie získat ID poslední příkaz změnu použité schéma.

5.  Dotaz na tabulku na seznam všech příkazů s ID větší než hodnota ID, že jste získali v předchozím kroku sledování změn schémat.

    a.  Ignorujte příkazy, které nelze v databázi koncový bod. Budete muset řešit nekonzistence schématu. Vrátit se původní změny schématu, pokud nekonzistence ovlivní vaše aplikace.

    b.  Použít tyto příkazy, které bude použito ručně.

6.  Aktualizovat tabulky historie změn schématu a nastavit ID poslední použité na správnou hodnotu.

7.  Zkontrolujte, zda je aktuální schéma.

8.  Znovu povolte po vložení aktivační událost v druhém kroku zakázáno.

9.  Znovu povolte aktivační událost jazyka DDL zakázaná v prvním kroku.

Pokud chcete vyčistit záznamy v tabulky sledování změn schématu, použijte místo TRUNCATE odstranit. Nikdy opakovaným přidáním sloupce identity v sledování tabulky pomocí příkazu DBCC CHECKIDENT změn schématu. Můžete vytvořit nové změny schématu tabulky sledování a pokud reseeding je potřeba aktualizovat název tabulky v aktivační událost jazyka DDL.

## <a name="other"></a> Další důležité informace

-   Databáze uživatelé, kteří konfigurovat databáze rozbočovače a člen musí mít dostatečná oprávnění ke spuštění příkazů změny schématu.

-   Můžete přidat další filtry v aktivační událost jazyka DDL pouze replikovat změny schématu ve vybraných tabulkách nebo operace.

-   Změny schématu lze vytvořit pouze v databázi, kde se má vytvořit aktivační událost jazyka DDL.

-   Pokud jsou provedení změny v databázi SQL serveru místní, ujistěte se, že změny schématu je podporována v Azure SQL Database.

-   Pokud změny schématu jsou provedeny v databázích než databáze, kde se má vytvořit aktivační událost jazyka DDL, nejsou replikovat změny. K tomuto problému vyhnout, můžete vytvořit aktivační události DDL blokování změny na ostatní koncové body.

-   Pokud potřebujete změnit schéma tabulky sledování změn schématu, zakažte aktivační událost jazyka DDL před změnou a pak ručně použití změny na všechny koncové body. Aktualizace schématu v aktivační události AFTER INSERT ve stejné tabulce nefunguje.

-   Pomocí příkazu DBCC CHECKIDENT si opakovaným přidáním sloupce identity.

-   Nepoužívejte TRUNCATE vyčistit data v tabulce sledování změn schémat.
