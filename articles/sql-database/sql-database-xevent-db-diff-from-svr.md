---
title: Rozšířené události v SQL Database
description: Popisuje rozšířené události (XEvents) v Azure SQL Database a způsob, jakým se relace událostí mírně liší od relací událostí v Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 64cfcd9451416a6eb35301268b285bd00cf0cad4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686776"
---
# <a name="extended-events-in-sql-database"></a>Rozšířené události v SQL Database
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Toto téma vysvětluje, jak se v porovnání s rozšířenými událostmi v Microsoft SQL Server mírně liší implementace rozšířených událostí v Azure SQL Database.

- SQL Database V12 získala funkci rozšířené události v druhé polovině kalendáře 2015.
- SQL Server měly rozšířené události od 2008.
- Sada funkcí rozšířených událostí v SQL Database je robustní podmnožinou funkcí na SQL Server.

*XEvents* je neoficiální Přezdívka, která se někdy používá pro rozšířené události v blogůch a jiných neformálních umístěních.

Další informace o rozšířených událostech pro Azure SQL Database a Microsoft SQL Server jsou k dispozici na adrese:

- [Rychlé zprovoznění: rozšířené události v SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Rozšířené události](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Požadavky

V tomto tématu se předpokládá, že už máte znalosti:

- [Služba Azure SQL Database](https://azure.microsoft.com/services/sql-database/).
- [Rozšířené události](https://msdn.microsoft.com/library/bb630282.aspx) v Microsoft SQL Server.

- Hromadná dokumentace o rozšířených událostech se týká SQL Server i SQL Database.

Předchozí expozici následujících položek je užitečná při výběru souboru události jako [cíle](#AzureXEventsTargets):

- [Služba Azure Storage](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Použití Azure PowerShell s Azure Storage](../storage/common/storage-powershell-guide-full.md) – poskytuje komplexní informace o PowerShellu a službě Azure Storage.

## <a name="code-samples"></a>Ukázky kódů

Související témata poskytují dva ukázky kódu:


- [Cílový kód cyklické vyrovnávací paměti pro rozšířené události v SQL Database](sql-database-xevent-code-ring-buffer.md)
    - Krátký jednoduchý skript Transact-SQL.
    - V tématu vzor kódu jsme zdůrazněni, že po dokončení práce s cílem kruhové vyrovnávací paměti byste měli uvolnit své prostředky spuštěním příkazu ALTER-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;`. Později můžete přidat další instanci kruhové vyrovnávací paměti pomocí `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Cílový kód souboru události pro rozšířené události v SQL Database](sql-database-xevent-code-event-file.md)
    - Fáze 1 je PowerShell pro vytvoření kontejneru Azure Storage.
    - Fáze 2 je Transact-SQL, který používá kontejner Azure Storage.

## <a name="transact-sql-differences"></a>Rozdíly v Transact-SQL


- Při spuštění příkazu [vytvořit relaci události](https://msdn.microsoft.com/library/bb677289.aspx) na SQL Server použijete klauzuli **on serveru** . Ale na SQL Database místo toho použijete klauzuli **on Database** .


- Klauzule **on Database** se vztahuje také na příkazy jazyka Transact-SQL v [relaci události ALTER](https://msdn.microsoft.com/library/bb630368.aspx) a [drop](https://msdn.microsoft.com/library/bb630257.aspx) .


- Osvědčeným postupem je zahrnout možnost relace události **STARTUP_STATE = on** ve vaší **relaci vytvoření události** nebo příkazy pro **změnu relace události** .
    - Hodnota **= on** podporuje automatické restartování po opětovné konfiguraci logické databáze z důvodu převzetí služeb při selhání.

## <a name="new-catalog-views"></a>Nové zobrazení katalogu

Funkce Rozšířené události je podporována v několika [zobrazeních katalogu](https://msdn.microsoft.com/library/ms174365.aspx). Zobrazení katalogu informují o *metadatech a definicích* relací událostí vytvořených uživatelem v aktuální databázi. Zobrazení nevrací informace o instancích aktivních relací událostí.

| Název<br/>zobrazení katalogu | Popis |
|:--- |:--- |
| **sys. database_event_session_actions** |Vrátí řádek pro každou akci každé události relace události. |
| **sys. database_event_session_events** |Vrátí řádek pro každou událost v relaci události. |
| **sys. database_event_session_fields** |Vrátí řádek pro každý sloupec s vlastním nastavením, který byl explicitně nastaven pro události a cíle. |
| **sys. database_event_session_targets** |Vrátí řádek pro každý cíl události pro relaci události. |
| **sys. database_event_sessions** |Vrátí řádek pro každou relaci události v databázi SQL Database. |

V Microsoft SQL Server podobné pohledy v katalogu mají názvy, které obsahují *. Server\_* namísto *\_. Database* . Vzor názvu je jako **Sys. server_event_%** .

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Nová zobrazení dynamické správy [(zobrazení dynamické správy)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database má [zobrazení dynamické správy (zobrazení dynamické správy)](https://msdn.microsoft.com/library/bb677293.aspx) , které podporují rozšířené události. Zobrazení dynamické správy vás informuje o *aktivních* relacích událostí.

| Název DMV | Popis |
|:--- |:--- |
| **sys. DM _xe_database_session_event_actions** |Vrátí informace o akcích relace události. |
| **sys. DM _xe_database_session_events** |Vrátí informace o událostech relace. |
| **sys. DM _xe_database_session_object_columns** |Zobrazuje konfigurační hodnoty pro objekty, které jsou vázány na relaci. |
| **sys. DM _xe_database_session_targets** |Vrátí informace o cílech relace. |
| **sys. DM _xe_database_sessions** |Vrátí řádek pro každou relaci události, která je vymezena na aktuální databázi. |

V Microsoft SQL Server jsou podobná zobrazení katalogu pojmenována bez\_část názvu *databáze* , například:

- **Sys. DM _xe_sessions**místo názvu<br/>**Sys. DM _xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Zobrazení dynamické správy společné pro obojí
Pro rozšířené události jsou k dispozici další zobrazení dynamické správy, které jsou společné pro Azure SQL Database i Microsoft SQL Server:

- **sys. DM _xe_map_values**
- **sys. DM _xe_object_columns**
- **sys. DM _xe_objects**
- **sys. DM _xe_packages**

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Cíle pro relace událostí SQL Database

Tady jsou cíle, které můžou zachytit výsledky z relací událostí na SQL Database:

- [Ring – cíl vyrovnávací paměti](https://msdn.microsoft.com/library/ff878182.aspx) – krátce uchovává data událostí v paměti.
- [Cílová hodnota čítače událostí](https://msdn.microsoft.com/library/ff878025.aspx) – spočítá všechny události, ke kterým dojde během relace rozšířených událostí.
- [Cíl souboru události](https://msdn.microsoft.com/library/ff878115.aspx) – zapisuje kompletní vyrovnávací paměti do kontejneru Azure Storage.

Rozhraní API [pro trasování událostí pro Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) není k dispozici pro rozšířené události v SQL Database.

## <a name="restrictions"></a>Omezení

Befitting cloudové SQL Database prostředí nabízí několik rozdílů týkajících se zabezpečení:

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

Existují situace, kdy náročné použití rozšířených událostí může nashromáždit více aktivních paměti, než je v pořádku pro celkový systém. Proto systém Azure SQL Database dynamicky nastavuje a upravuje omezení velikosti aktivní paměti, kterou lze shromáždit pomocí relace události. Mnoho faktorů přechází do dynamického výpočtu.

Pokud se zobrazí chybová zpráva oznamující, že byla vynutila maximální velikost paměti, můžete provést následující kroky:

- Spusťte méně souběžných relací událostí.
- Pomocí příkazů **Create** a **ALTER** pro relace událostí Snižte velikost paměti, kterou zadáte v klauzuli **Max\_paměti** .

### <a name="network-latency"></a>Latence sítě

Cílem **souboru událostí** může být latence sítě nebo selhání při trvalém ukládání dat do objektů blob Azure Storage. Další události v SQL Database mohou být zpožděny, když čekají na dokončení síťové komunikace. Tato prodleva může zpomalit vaše zatížení.

- Pokud chcete toto riziko snížit, vyhněte se nastavení možnosti **EVENT_RETENTION_MODE** na **NO_EVENT_LOSS** v definicích relace události.

## <a name="related-links"></a>Související odkazy

- [Použití Azure PowerShell s Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Rutiny Azure Storage](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Použití Azure PowerShell s Azure Storage](../storage/common/storage-powershell-guide-full.md) – poskytuje komplexní informace o PowerShellu a službě Azure Storage.
- [Jak používat úložiště objektů BLOB z .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [Vytvoření relace události (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Blogové příspěvky Jonathana Kehayias o rozšířených událostech v Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Webová stránka *aktualizace služby* Azure, která je zúžená podle parametru Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Další témata s ukázkami kódu pro rozšířené události jsou k dispozici na následujících odkazech. Je však nutné rutinu, která je v ukázce, kontrolovat, zda Microsoft SQL Server a Azure SQL Database. Pak se můžete rozhodnout, zda ke spuštění ukázky potřebujete drobné změny.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
