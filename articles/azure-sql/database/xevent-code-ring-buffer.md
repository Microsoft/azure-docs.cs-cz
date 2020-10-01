---
title: Kód vyrovnávací paměti XEvent Ring
description: Poskytuje ukázku kódu Transact-SQL, který je v Azure SQL Database snadný a rychlý při použití cíle kruhové vyrovnávací paměti.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 57449b0bbd39b6ea04ecae5a3ad766ae5687ca0b
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619827"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Cílový kód cyklické vyrovnávací paměti pro rozšířené události v Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Chcete vytvořit kompletní ukázku kódu pro nejsnadnější rychlý způsob, jak zachytit a ohlásit informace pro rozšířenou událost během testu. Nejjednodušší cíl pro rozšířená data události je [cíl kruhové vyrovnávací paměti](https://msdn.microsoft.com/library/ff878182.aspx).

Toto téma představuje ukázku kódu Transact-SQL, který:

1. Vytvoří tabulku s daty, která se mají demonstrovat.
2. Vytvoří relaci pro existující rozšířenou událost, konkrétně **SqlServer. sql_statement_starting**.

   * Událost je omezená na příkazy SQL, které obsahují konkrétní řetězec aktualizace: **příkaz jako% Update tabEmployee%**.
   * Rozhodne odeslat výstup události do cíle typu Ring buffer, jmenovitě  **package0. ring_buffer**.
3. Spustí relaci události.
4. Vydává několik jednoduchých příkazů SQL UPDATE.
5. Vydá příkaz SQL SELECT, který načte výstup události z kruhové vyrovnávací paměti.

   * jsou připojeni **Sys. dm_xe_database_session_targets** a další zobrazení dynamické správy (zobrazení dynamické správy).
6. Zastaví relaci události.
7. Uvolní cíl kruhové vyrovnávací paměti pro uvolnění prostředků.
8. Zruší relaci události a ukázkovou tabulku.

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Můžete si zaregistrovat i [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Všechny databáze, na kterých můžete vytvořit tabulku.
  
  * Volitelně můžete [vytvořit ukázkovou databázi **AdventureWorksLT** ](single-database-create-quickstart.md) během několika minut.
* SQL Server Management Studio (ssms.exe), v ideálním případě podle nejnovější měsíční verze aktualizace.
  Nejnovější ssms.exe si můžete stáhnout z těchto:
  
  * Téma s názvem [stažení SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Přímý odkaz na stažení.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Ukázka kódu

S velmi drobnými úpravami se dá spustit následující ukázka kódu Ring bufferu buď Azure SQL Database, nebo Microsoft SQL Server. Rozdíl je přítomnost uzlu ' _database ' v názvu některých zobrazení dynamické správy (zobrazení dynamické správy), která se používá v klauzuli FROM v kroku 5. Například:

* sys. dm_xe<strong>_database</strong>_session_targets
* sys. dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Obsah cyklické vyrovnávací paměti

Použili jsme `ssms.exe` ke spuštění ukázky kódu.

Pokud chcete zobrazit výsledky, klikneme na buňku pod záhlavím sloupce **target_data_XML**.

Pak v podokně výsledků kliknete na buňku pod záhlavím sloupce **target_data_XML**. Tím kliknete na tlačítko vytvořit další kartu soubor v ssms.exe, ve které byl obsah buňky výsledku zobrazen jako XML.

Výstup je zobrazen v následujícím bloku. Vypadá to dlouhou dobu, ale je to jen dva **\<event>** prvky.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>Uvolnění prostředků držených cyklickou vyrovnávací pamětí

Až budete s Cyklovou vyrovnávací pamětí hotovi, můžete ji odebrat a uvolnit její prostředky vyvoláním podobného **příkazu** :

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

Definice relace události je aktualizována, ale nebyla vyřazena. Později můžete do relace události přidat další instanci kruhové vyrovnávací paměti:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>Další informace

Primárním tématem pro rozšířené události v Azure SQL Database je:

* [Rozšířené aspekty událostí v Azure SQL Database](xevent-db-diff-from-svr.md), které odlišují některé aspekty rozšířených událostí, které se liší od Azure SQL Database oproti Microsoft SQL Server.

Další témata s ukázkami kódu pro rozšířené události jsou k dispozici na následujících odkazech. Je však nutné rutinu, která je v ukázce, kontrolovat, zda Microsoft SQL Server a Azure SQL Database. Pak se můžete rozhodnout, zda ke spuštění ukázky potřebujete drobné změny.

* Ukázka kódu pro Azure SQL Database: [kód cílového souboru události pro rozšířené události v Azure SQL Database](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
