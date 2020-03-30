---
title: Rozšířené události
description: Popisuje rozšířené události (XEvents) v Azure SQL Database a jak se relace událostí mírně liší od relací událostí na Microsoft SQL Serveru.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213950"
---
# <a name="extended-events-in-sql-database"></a>Rozšířené události v databázi SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Toto téma vysvětluje, jak se implementace rozšířených událostí v Azure SQL Database mírně liší ve srovnání s rozšířenými událostmi na Microsoft SQL Serveru.

- SQL Database V12 získal rozšířené události funkce v druhé polovině kalendáře 2015.
- SQL Server má rozšířené události od roku 2008.
- Sada funkcí rozšířených událostí v databázi SQL je robustní podmnožinou funkcí na serveru SQL Server.

*XEvents* je neformální přezdívka, která se někdy používá pro "rozšířené události" v blozích a jiných neformálních místech.

Další informace o rozšířených událostech pro Azure SQL Database a Microsoft SQL Server jsou k dispozici na adrese:

- [Rychlý start: Rozšířené události na serveru SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Rozšířené události](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Požadavky

Toto téma předpokládá, že již máte nějaké znalosti:

- [Služba Azure SQL Database](https://azure.microsoft.com/services/sql-database/).
- [Rozšířené události](https://msdn.microsoft.com/library/bb630282.aspx) v microsoft SQL Serveru.

- Převážná část naší dokumentace o rozšířené události platí pro SQL Server a SQL Database.

Předchozí vystavení následujícím položkám je užitečné při výběru souboru událostí jako [cíle](#AzureXEventsTargets):

- [Služba Azure Storage](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Používání Azure PowerShellu s Azure Storage](../storage/common/storage-powershell-guide-full.md) – poskytuje komplexní informace o PowerShellu a službě Azure Storage.

## <a name="code-samples"></a>Ukázky kódů

Související témata poskytují dva ukázky kódu:


- [Cílový kód kruhové vyrovnávací paměti pro rozšířené události v databázi SQL](sql-database-xevent-code-ring-buffer.md)
    - Krátký jednoduchý Skript Transact-SQL.
    - Zdůrazňujeme v tématu ukázkový kód, že po dokončení s cíl vyrovnávací paměti ring, `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` měli byste uvolnit své prostředky spuštěním příkazu alter-drop. Později můžete přidat další instanci Ring Buffer podle `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Cílový kód souboru událostí pro rozšířené události v databázi SQL](sql-database-xevent-code-event-file.md)
    - Fáze 1 je PowerShell k vytvoření kontejneru úložiště Azure.
    - Fáze 2 je Transact-SQL, který používá kontejner úložiště Azure.

## <a name="transact-sql-differences"></a>Rozdíly v transakt-SQL


- Při spuštění příkazu [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) na serveru SQL Server použijete klauzuli ON **SERVER.** Ale v databázi SQL místo toho použijete **klauzuli ON DATABASE.**


- On **DATABASE** klauzule platí také [pro alter event session](https://msdn.microsoft.com/library/bb630368.aspx) a drop event [session](https://msdn.microsoft.com/library/bb630257.aspx) transakt-SQL příkazy.


- Osvědčeným postupem je zahrnout možnost relace události **STARTUP_STATE = ZAPNUTO** do příkazů **CREATE EVENT SESSION** nebo ALTER EVENT **SESSION.**
    - Hodnota **= ON** podporuje automatické restartování po rekonfiguraci logické databáze z důvodu převzetí služeb při selhání.

## <a name="new-catalog-views"></a>Nová zobrazení katalogu

Funkce rozšířených událostí je podporována několika [zobrazeními katalogu](https://msdn.microsoft.com/library/ms174365.aspx). Zobrazení katalogu vás našlápnou na *metadata nebo definice* uživatelem vytvořených relací událostí v aktuální databázi. Zobrazení nevracejí informace o instancích aktivních relací událostí.

| Název<br/>zobrazení katalogu | Popis |
|:--- |:--- |
| **sys.database_event_session_actions** |Vrátí řádek pro každou akci pro každou událost relace události. |
| **sys.database_event_session_events** |Vrátí řádek pro každou událost v relaci události. |
| **sys.database_event_session_fields** |Vrátí řádek pro každý sloupec přizpůsobitelný, který byl explicitně nastaven na události a cíle. |
| **sys.database_event_session_targets** |Vrátí řádek pro každý cíl události pro relaci události. |
| **sys.database_event_sessions** |Vrátí řádek pro každou relaci událostí v databázi SQL Database. |

V systému Microsoft SQL Server mají podobná zobrazení katalogu názvy, které obsahují *.server\_ * namísto *.database\_*. Název vzor je jako **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Nová zobrazení dynamické správy [(DMVs)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database má [zobrazení dynamické správy (DMVs),](https://msdn.microsoft.com/library/bb677293.aspx) které podporují rozšířené události. DMVs vám o *aktivní* relace událostí.

| Název DMV | Popis |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Vrátí informace o akcích relace události. |
| **sys.dm_xe_database_session_events** |Vrátí informace o událostech relace. |
| **sys.dm_xe_database_session_object_columns** |Zobrazuje hodnoty konfigurace pro objekty, které jsou vázány na relaci. |
| **sys.dm_xe_database_session_targets** |Vrátí informace o cílech relace. |
| **sys.dm_xe_database_sessions** |Vrátí řádek pro každou relaci události, která je vymezena na aktuální databázi. |

V microsoft sql serveru jsou podobná * \_* zobrazení katalogu pojmenována bez databázové části názvu, například:

- **sys.dm_xe_sessions**, místo názvu<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMV společné pro oba
Pro rozšířené události existují další dmvs, které jsou společné pro Azure SQL Database a Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Vyhledání dostupných rozšířených událostí, akcí a cílů

Můžete spustit jednoduchý SQL **SELECT** získat seznam dostupných událostí, akcí a cíl.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Cíle pro relace událostí databáze SQL

Zde jsou cíle, které mohou zachytit výsledky z vašich relací událostí v databázi SQL:

- [Cíl vyrovnávací paměti vyzvánění](https://msdn.microsoft.com/library/ff878182.aspx) – krátce uchovává data událostí v paměti.
- [Cíl čítače událostí](https://msdn.microsoft.com/library/ff878025.aspx) – spočítá všechny události, ke kterým dojde během relace rozšířené události.
- [Cíl souboru událostí](https://msdn.microsoft.com/library/ff878115.aspx) – zapíše úplné vyrovnávací paměti do kontejneru úložiště Azure.

Rozhraní [API pro trasování událostí pro Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) není k dispozici pro rozšířené události v databázi SQL.

## <a name="restrictions"></a>Omezení

Existuje několik rozdílů souvisejících se zabezpečením, které odpovídají cloudovému prostředí databáze SQL:

- Rozšířené události jsou založeny na modelu izolace jednoho tenanta. Relace událostí v jedné databázi nemá přístup k datům nebo událostem z jiné databáze.
- Příkaz CREATE **EVENT SESSION** nelze vydat v kontextu **hlavní** databáze.

## <a name="permission-model"></a>Model oprávnění

Chcete-li vydat příkaz **CREATE EVENT SESSION,** musíte mít oprávnění **k řízení** v databázi. Vlastník databáze (dbo) má oprávnění **control.**

### <a name="storage-container-authorizations"></a>Autorizace skladovacích kontejnerů

Token SAS, který vygenerujete pro kontejner úložiště Azure, musí určit **rwl** pro oprávnění. Hodnota **RWL** poskytuje následující oprávnění:

- Čtení
- Zápis
- Seznam

## <a name="performance-considerations"></a>Otázky výkonu

Existují scénáře, kde intenzivní využití rozšířené události mohou akumulovat více aktivní paměti, než je v pořádku pro celý systém. Proto systém Azure SQL Database dynamicky nastavuje a upravuje limity na množství aktivní paměti, které lze nahromadit relace událostí. Do dynamického výpočtu vstupuje mnoho faktorů.

Pokud se zobrazí chybová zpráva, že bylo vynuceno maximum paměti, můžete provést některé nápravné kroky:

- Spusťte méně souběžných relací událostí.
- Prostřednictvím **příkazy CREATE** a **ALTER** pro relace událostí snižte množství paměti, kterou zadáte v klauzuli **\_MAX MEMORY.**

### <a name="network-latency"></a>Latence sítě

Cíl **souborudálostí** může dojít k latenci sítě nebo selhání při uchování dat do objektů blob služby Azure Storage. Jiné události v databázi SQL může být zpožděno při čekání na dokončení síťové komunikace. Toto zpoždění může zpomalit vaše pracovní vytížení.

- Chcete-li toto riziko výkonu zmírnit, vyhněte se nastavení **možnosti EVENT_RETENTION_MODE** **NO_EVENT_LOSS** v definicích relace událostí.

## <a name="related-links"></a>Související odkazy

- [Používání Azure PowerShellu s Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Rutiny úložiště Azure](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Používání Azure PowerShellu s Azure Storage](../storage/common/storage-powershell-guide-full.md) – poskytuje komplexní informace o PowerShellu a službě Azure Storage.
- [Použití úložiště objektů Blob z rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [VYTVOŘIT RELACI UDÁLOSTÍ (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias 'blogu o rozšířené události v Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Webová stránka *Aktualizace služby* Azure, zúžená parametrem na Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Další ukázková témata kódu pro rozšířené události jsou k dispozici na následujících odkazech. Je však nutné pravidelně kontrolovat všechny ukázky, zda ukázka cíle Microsoft SQL Server versus Azure SQL Database. Pak se můžete rozhodnout, zda jsou potřebné menší změny ke spuštění ukázky.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
