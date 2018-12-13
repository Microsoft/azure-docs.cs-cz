---
title: Kód cyklické vyrovnávací paměti XEvent pro SQL Database | Dokumentace Microsoftu
description: Obsahuje ukázku kódu jazyka Transact-SQL, který je k rychlé a snadné použití cíle cyklické vyrovnávací paměti, ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 522561d8afcf6fe1bdd2887f7fd5180b79565a61
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874613"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Kód cyklické vyrovnávací paměti cílového pro rozšířené události ve službě SQL Database

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Chcete ukázku dokončení kódu pro rychlé nejsnadněji se sběr dat a sestavy informace o rozšířené události během testu. Nejjednodušší cíl pro data rozšířených událostí je [cyklické vyrovnávací paměti cílového](https://msdn.microsoft.com/library/ff878182.aspx).

Toto téma představuje ukázku kódu jazyka Transact-SQL, který:

1. Vytvoří tabulku s daty k předvedení s.
2. Vytvoří relaci pro existující rozšířené události, a to **sqlserver.sql_statement_starting**.
   
   * Událost je omezená na příkazy SQL, které obsahují určitý řetězec aktualizace: **příkaz jako "aktualizace tabEmployee %"**.
   * Zvolí možnost posílat události do cíle typu cyklické vyrovnávací paměti, a to **package0.ring_buffer**.
3. Spustí relaci události.
4. Problémy s několika jednoduchých příkazů SQL pro sadu VS11.
5. Vydá příkaz SQL SELECT k načtení událostí výstup z cyklické vyrovnávací paměti.
   
   * **Sys.dm_xe_database_session_targets** a se propojují s dalšími zobrazeními dynamické správy (DMV).
6. Ukončí relaci události.
7. Zahodí cíle cyklické vyrovnávací paměti, a uvolnit jeho prostředky.
8. Zahodí relace události a ukázkové tabulky.

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Můžete si zaregistrovat i [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Všechny databáze, kterou vytvoříte tabulku v.
  
  * Volitelně můžete [vytvořit **AdventureWorksLT** ukázkovou databázi](sql-database-get-started.md) během několika minut.
* SQL Server Management Studio (ssms.exe), v ideálním případě jeho nejnovější měsíční aktualizovanou verzi. 
  Si můžete stáhnout nejnovější ssms.exe od:
  
  * Téma s názvem [stažení aplikace SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Přímý odkaz na stažení.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Ukázka kódu

S velmi malé změny můžete spustit následující vzorový kód cyklické vyrovnávací paměti na Azure SQL Database nebo Microsoft SQL Server. Rozdíl spočívá v přítomnost uzel "_databáze: název některá další zobrazení dynamické správy (DMV), použitý v klauzuli FROM v kroku 5. Příklad:

* sys.dm_xe **_database**_session_targets
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

## <a name="ring-buffer-contents"></a>Cyklické vyrovnávací paměti obsah

Jsme použili ssms.exe ke spuštění ukázky kódu.

Chcete-li zobrazit výsledky, jsme kliknutí na buňku pod záhlavím sloupce **target_data_XML**.

Potom v podokně výsledků jsme kliknutí na buňku pod záhlavím sloupce **target_data_XML**. Klikněte na tlačítko vytvořit jiný karta soubor v ssms.exe ve kterém byla zobrazena obsah buňky výsledek ve formátu XML.

Výstup je uveden v následující bloku. Vyhledá dlouhý, ale je právě dvě **<event>** elementy.

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


#### <a name="release-resources-held-by-your-ring-buffer"></a>Uvolněte prostředky držené vaše cyklické vyrovnávací paměti

Jakmile budete hotovi s vaší cyklické vyrovnávací paměti, můžete ho odebrat a uvolnit jeho prostředky vydání **ALTER** podobně jako následující:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Definice relace události je aktualizovat, ale není vyřazen. Později můžete přidat další instanci Cyklická vyrovnávací paměť do relace události:

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

Primární téma pro rozšířené události pro Azure SQL Database je:

* [Rozšířené události aspekty ve službě SQL Database](sql-database-xevent-db-diff-from-svr.md), která se liší od některé aspekty rozšířené události, které se liší mezi Azure SQL Database a systému Microsoft SQL Server.

Další témata ukázkový kód pro rozšířené události jsou k dispozici prostřednictvím následujících odkazů. Však musí pravidelně zkontrolujte všechny ukázky a zda vzorku, zaměřuje Microsoft SQL Server a Azure SQL Database. Pak se můžete rozhodnout, zda jsou menší změny potřebné ke spuštění ukázky.

* Ukázka kódu pro Azure SQL Database: [cílový kód souboru události pro rozšířené události ve službě SQL Database](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
