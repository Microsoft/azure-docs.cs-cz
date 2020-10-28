---
title: Rozšířené události
description: Popisuje rozšířené události (XEvents) v Azure SQL Database a způsob, jakým se relace událostí mírně liší od relací událostí v Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 0c89dc28a330e319e18a6289e5f6759c56e46ae8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791269"
---
# <a name="extended-events-in-azure-sql-database"></a>Rozšířené události v Azure SQL Database 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Sada funkcí rozšířených událostí v Azure SQL Database je robustní podmnožinou funkcí na SQL Server a spravované instanci Azure SQL.

*XEvents* je neoficiální Přezdívka, která se někdy používá pro rozšířené události v blogůch a jiných neformálních umístěních.

Další informace o rozšířených událostech jsou k dispozici na adrese:

- [Rychlé zprovoznění: rozšířené události v SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Rozšířené události](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Předpoklady

V tomto tématu se předpokládá, že už máte znalosti:

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Rozšířené události](/sql/relational-databases/extended-events/extended-events)

- Hromadná dokumentace o rozšířených událostech se týká SQL Server, Azure SQL Database a spravované instance Azure SQL.

Předchozí expozici následujících položek je užitečná při výběru souboru události jako [cíle](#AzureXEventsTargets):

- [Služba Azure Storage](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell s Azure Storage](/powershell/module/az.storage/)

## <a name="code-samples"></a>Ukázky kódů

Související témata poskytují dva ukázky kódu:

- [Cílový kód cyklické vyrovnávací paměti pro rozšířené události v Azure SQL Database](xevent-code-ring-buffer.md)

  - Krátký jednoduchý skript Transact-SQL.
  - V tématu vzor kódu jsme zdůrazněni, že po dokončení práce s cílem kruhové vyrovnávací paměti byste měli uvolnit své prostředky spuštěním příkazu ALTER-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` . Později můžete přidat další instanci kruhové vyrovnávací paměti pomocí `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...` .

- [Cílový kód souboru události pro rozšířené události v Azure SQL Database](xevent-code-event-file.md)

  - Fáze 1 je PowerShell pro vytvoření kontejneru Azure Storage.
  - Fáze 2 je Transact-SQL, který používá kontejner Azure Storage.

## <a name="transact-sql-differences"></a>Rozdíly v Transact-SQL

- Při spuštění příkazu [vytvořit relaci události](/sql/t-sql/statements/create-event-session-transact-sql) na SQL Server použijete klauzuli **on serveru** . Ale na Azure SQL Database místo toho použijete klauzuli **on Database** .
- Klauzule **on Database** se vztahuje také na příkazy jazyka Transact-SQL v [relaci události ALTER](/sql/t-sql/statements/alter-event-session-transact-sql) a [drop](/sql/t-sql/statements/drop-event-session-transact-sql) .

- Osvědčeným postupem je zahrnout možnost relace události **STARTUP_STATE = on** v **relaci vytvoření události**  nebo příkazy pro **změnu relace události** .
  - Hodnota **= on** podporuje automatické restartování po opětovné konfiguraci logické databáze z důvodu převzetí služeb při selhání.

## <a name="new-catalog-views"></a>Nové zobrazení katalogu

Funkce Rozšířené události je podporována v několika [zobrazeních katalogu](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql). Zobrazení katalogu informují o *metadatech a definicích* relací událostí vytvořených uživatelem v aktuální databázi. Zobrazení nevrací informace o instancích aktivních relací událostí.

| Název<br/>zobrazení katalogu | Popis |
|:--- |:--- |
| **sys.database_event_session_actions** |Vrátí řádek pro každou akci každé události relace události. |
| **sys.database_event_session_events** |Vrátí řádek pro každou událost v relaci události. |
| **sys.database_event_session_fields** |Vrátí řádek pro každý sloupec s vlastním nastavením, který byl explicitně nastaven pro události a cíle. |
| **sys.database_event_session_targets** |Vrátí řádek pro každý cíl události pro relaci události. |
| **sys.database_event_sessions** |Vrátí řádek pro každou relaci události v databázi. |

V Microsoft SQL Server podobné pohledy v katalogu mají názvy, které obsahují *. \_ Server* místo *. Database \_* . Vzor názvu je jako **Sys.server_event_%** .

## <a name="new-dynamic-management-views-dmvs"></a>Nová zobrazení dynamické správy [(zobrazení dynamické správy)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

Azure SQL Database má [zobrazení dynamické správy (zobrazení dynamické správy)](/sql/relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views) , které podporují rozšířené události. Zobrazení dynamické správy vás informuje o *aktivních* relacích událostí.

| Název DMV | Popis |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Vrátí informace o akcích relace události. |
| **sys.dm_xe_database_session_events** |Vrátí informace o událostech relace. |
| **sys.dm_xe_database_session_object_columns** |Zobrazuje konfigurační hodnoty pro objekty, které jsou vázány na relaci. |
| **sys.dm_xe_database_session_targets** |Vrátí informace o cílech relace. |
| **sys.dm_xe_database_sessions** |Vrátí řádek pro každou relaci události, která je vymezena na aktuální databázi. |

V Microsoft SQL Server jsou podobná zobrazení katalogu pojmenována bez *\_ databázové* části názvu, například:

- místo názvu **Sys.dm_xe_sessions** .<br/>**Sys.dm_xe_database_sessions** .

### <a name="dmvs-common-to-both"></a>Zobrazení dynamické správy společné pro obojí

Pro rozšířené události jsou k dispozici další zobrazení dynamické správy, které jsou společné pro Azure SQL Database, Azure SQL Managed instance a Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Najít dostupné rozšířené události, akce a cíle

Pokud chcete získat seznam dostupných událostí, akcí a cílů, můžete spustit jednoduchý příkaz SQL **Select** .

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

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Cíle pro relace událostí Azure SQL Database

Tady jsou cíle, které můžou zachytit výsledky z relací událostí na Azure SQL Database:

- [Ring – cíl vyrovnávací paměti](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)) – krátce uchovává data událostí v paměti.
- [Cílová hodnota čítače událostí](/previous-versions/sql/sql-server-2016/ff878025(v=sql.130)) – spočítá všechny události, ke kterým dojde během relace rozšířených událostí.
- [Cíl souboru události](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) – zapisuje kompletní vyrovnávací paměti do kontejneru Azure Storage.

Rozhraní API [pro trasování událostí pro Windows (ETW)](/dotnet/framework/wcf/samples/etw-tracing) není k dispozici pro rozšířené události v Azure SQL Database.

## <a name="restrictions"></a>Omezení

Befitting cloudové Azure SQL Database prostředí nabízí několik rozdílů týkajících se zabezpečení:

- Rozšířené události jsou založené na modelu izolace jednoho tenanta. Relace události v jedné databázi nemá přístup k datům nebo událostem z jiné databáze.
- Nelze vydat příkaz **vytvořit relaci události** v kontextu **hlavní** databáze.

## <a name="permission-model"></a>Model oprávnění

Aby bylo možné vystavit příkaz **vytvořit relaci události** , je nutné mít oprávnění k **řízení** databáze. Vlastník databáze (dbo) má oprávnění **Control** .

### <a name="storage-container-authorizations"></a>Autorizace kontejneru úložiště

Token SAS, který vygenerujete pro kontejner Azure Storage, musí pro oprávnění zadat **RWL** . Hodnota **RWL** poskytuje následující oprávnění:

- Čtení
- Zápis
- Seznam

## <a name="performance-considerations"></a>Otázky výkonu

Existují situace, kdy náročné použití rozšířených událostí může nashromáždit více aktivních paměti, než je v pořádku pro celkový systém. Proto Azure SQL Database dynamicky nastaví a upraví omezení velikosti aktivní paměti, kterou lze shromáždit pomocí relace události. Mnoho faktorů přechází do dynamického výpočtu.

Pokud se zobrazí chybová zpráva oznamující, že byla vynutila maximální velikost paměti, můžete provést následující kroky:

- Spusťte méně souběžných relací událostí.
- Pomocí příkazů **Create** a **ALTER** pro relace událostí Snižte velikost paměti, kterou zadáte v klauzuli **Max \_ Memory** .

### <a name="network-latency"></a>Latence sítě

Cílem **souboru událostí** může být latence sítě nebo selhání při trvalém ukládání dat do objektů blob Azure Storage. Další události v Azure SQL Database mohou být zpožděny, když čekají na dokončení síťové komunikace. Tato prodleva může zpomalit vaše zatížení.

- Pokud chcete toto riziko snížit, vyhněte se nastavení **EVENT_RETENTION_MODE** možnost **NO_EVENT_LOSS** v definicích relace události.

## <a name="related-links"></a>Související odkazy

- [Použití Azure PowerShell s Azure Storage](/powershell/module/az.storage/).
- [Rutiny Azure Storage](/powershell/module/Azure.Storage)
- [Použití Azure Powershell s Azure Storage](/powershell/module/az.storage/)
- [Jak používat úložiště objektů BLOB z .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql)
- [Vytvoření relace události (Transact-SQL)](/sql/t-sql/statements/create-event-session-transact-sql)
- [Blogové příspěvky Jonathana Kehayias o rozšířených událostech v Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- Webová stránka *aktualizace služby* Azure, která je zúžená podle parametru Azure SQL Database:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->