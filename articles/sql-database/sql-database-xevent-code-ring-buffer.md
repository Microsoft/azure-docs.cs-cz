---
title: Kód vyrovnávací paměti xeventového vyzvánění
description: Poskytuje ukázku kódu Transact-SQL, která je snadno a rychle pomocí cíle ring buffer v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: ad98b61d6339388551af93671b3d4d892942f4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213968"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Cílový kód kruhové vyrovnávací paměti pro rozšířené události v databázi SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Chcete kompletní ukázku kódu pro nejjednodušší rychlý způsob, jak zachytit a sestavy informace pro rozšířené události během testu. Nejjednodušším cílem pro rozšířená data událostí je [cíl ring bufferu](https://msdn.microsoft.com/library/ff878182.aspx).

Toto téma představuje ukázku kódu Transact-SQL, která:

1. Vytvoří tabulku s daty, která mají být zrekonstruována.
2. Vytvoří relaci pro existující rozšířenou událost, konkrétně **sqlserver.sql_statement_starting**.
   
   * Událost je omezena na příkazy SQL, které obsahují konkrétní řetězec aktualizace: **příkaz LIKE '%UPDATE tabEmployee%'**.
   * Zvolí odeslání výstupu události do cíle typu Ring Buffer, konkrétně **package0.ring_buffer**.
3. Spustí relaci události.
4. Vydává několik jednoduchých příkazů SQL UPDATE.
5. Vydá příkaz SQL SELECT k načtení výstupu události z ring bufferu.
   
   * **sys.dm_xe_database_session_targets** a další pohledy dynamické správy (DMV) jsou spojeny.
6. Zastaví relaci události.
7. Klesne cíl ring buffer, uvolnit své prostředky.
8. Zahodí relaci události a ukázkovou tabulku.

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Můžete si zaregistrovat i [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Libovolná databáze, ve které můžete vytvořit tabulku.
  
  * Volitelně můžete [vytvořit demonstrační databázi **AdventureWorksLT** ](sql-database-get-started.md) během několika minut.
* SQL Server Management Studio (ssms.exe), v ideálním případě jeho nejnovější měsíční aktualizace verze. 
  Zde si můžete stáhnout nejnovější ssms.exe z:
  
  * Téma s názvem [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Přímý odkaz na stažení.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Ukázka kódu

S velmi drobné změny, následující ukázka kódu ring buffer lze spustit na Azure SQL Database nebo Microsoft SQL Server. Rozdíl je přítomnost uzlu '_database' v názvu některé dynamické řízení zobrazení (DMVs), který se používá v from klauzule v kroku 5. Například:

* _session_targets<strong>_database</strong>sys.dm_xe
* sys.dm_xe_session_targets

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

## <a name="ring-buffer-contents"></a>Obsah vyrovnávací paměti vyzvánění

Použili jsme ssms.exe ke spuštění ukázky kódu.

Chcete-li zobrazit výsledky, klikli jsme na buňku pod záhlavím sloupce **target_data_XML**.

Pak jsme v podokně výsledků klikli na buňku pod záhlavím sloupce **target_data_XML**. Klepnutím na tlačítko bylo vytvořeno další karta souboru v souboru ssms.exe, ve kterém byl obsah výsledné buňky zobrazen jako XML.

Výstup je zobrazen v následujícím bloku. Vypadá to dlouho, ale ** \<** je to jen dvě události>prvky.

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


#### <a name="release-resources-held-by-your-ring-buffer"></a>Uvolnění prostředků v držení ringbufferu

Po dokončení s prstenvyrovnávací paměť, můžete ji odebrat a uvolnit jeho prostředky vydávání **ALTER,** jako je následující:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Definice relace události je aktualizována, ale není vynechána. Později můžete do relace události přidat další instanci ringbufferu:

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

Primární téma pro rozšířené události v Azure SQL Database je:

* [Rozšířené aspekty událostí v databázi SQL ,](sql-database-xevent-db-diff-from-svr.md)která kontrastuje některé aspekty rozšířených událostí, které se liší mezi Azure SQL Database versus Microsoft SQL Server.

Další ukázková témata kódu pro rozšířené události jsou k dispozici na následujících odkazech. Je však nutné pravidelně kontrolovat všechny ukázky, zda ukázka cíle Microsoft SQL Server versus Azure SQL Database. Pak se můžete rozhodnout, zda jsou potřebné menší změny ke spuštění ukázky.

* Ukázka kódu pro Azure SQL Database: [Cílový kód souboru událostí pro rozšířené události v databázi SQL](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
