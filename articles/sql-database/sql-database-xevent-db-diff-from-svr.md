---
title: Rozšířené události do databáze SQL | Microsoft Docs
description: Popisuje rozšířené události (XEvents) ve službě Azure SQL Database a jak relace události mírně lišit od relace události v systému Microsoft SQL Server.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 9c0115254fc3368868584e76ead8da812656e4d1
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028842"
---
# <a name="extended-events-in-sql-database"></a>Rozšířené události v databázi SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Toto téma vysvětluje, jak se mírně liší implementace rozšířených událostí ve službě Azure SQL Database ve srovnání s rozšířené události v systému Microsoft SQL Server.

- Databáze SQL verze 12 získávají funkci rozšířených událostí za sekundu polovinu kalendáře 2015.
- SQL Server došlo rozšířené události od 2008.
- Sada funkcí rozšířených událostí na SQL Database je robustní podmnožinu funkcí v systému SQL Server.

*Události Xevent* je neformální přezdívka, která se někdy používá pro parametr 'rozšířené události: v blogy a jiných neformální umístění.

Další informace o rozšířených událostí, Azure SQL Database a Microsoft SQL Server, je k dispozici na:

- [Rychlé zahájení: Rozšířených událostí v systému SQL Server](http://msdn.microsoft.com/library/mt733217.aspx)
- [Rozšířené události](http://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Požadavky

Toto téma předpokládá, že již máte některé znalosti:

- [Služba Azure SQL Database](https://azure.microsoft.com/services/sql-database/).
- [Rozšířené události](http://msdn.microsoft.com/library/bb630282.aspx) v systému Microsoft SQL Server.

- Hromadným naší dokumentaci o rozšířených událostech, se vztahuje na systém SQL Server a databáze SQL.

Předchozí ohrožení k následujícím položkám je užitečné, pokud vyberete soubor událostí jako [cíl](#AzureXEventsTargets):

- [Služba Azure Storage](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Použití Azure PowerShell s Azure Storage](../storage/common/storage-powershell-guide-full.md) – poskytuje podrobné informace o prostředí PowerShell a službu úložiště Azure.

## <a name="code-samples"></a>Ukázky kódů

Související témata obsahují dva ukázky kódu:


- [Prstence kódu cílové vyrovnávací paměti pro rozšířené události v databázi SQL](sql-database-xevent-code-ring-buffer.md)
    - Krátký jednoduchý skript Transact-SQL.
    - Jsme zdůraznil v tématu ukázkový kód, že až skončíte s cílem cyklické vyrovnávací paměti, měli uvolnění jeho prostředků spuštěním alter myší `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` příkaz. Později můžete přidat další instanci cyklické vyrovnávací paměti podle `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Kód cílový soubor události pro rozšířené události v databázi SQL](sql-database-xevent-code-event-file.md)
    - Fáze 1 je prostředí PowerShell vytvořit kontejner úložiště Azure.
    - Fáze 2 je Transact-SQL, který používá kontejner úložiště Azure.

## <a name="transact-sql-differences"></a>Rozdíly v Transact-SQL


- Při spuštění [vytvořit událost relace](http://msdn.microsoft.com/library/bb677289.aspx) příkaz na serveru SQL Server, můžete použít **ON SERVER** klauzule. Ale v databázi SQL můžete použít **ON databáze** klauzule místo.


- **ON databáze** klauzule platí také pro [ALTER událostí relace](http://msdn.microsoft.com/library/bb630368.aspx) a [VYŘADIT událostí relace](http://msdn.microsoft.com/library/bb630257.aspx) příkazy jazyka Transact-SQL.


- Osvědčeným postupem je zahrnují možnost relace události **STARTUP_STATE = ON** ve vaší **vytvořit událost relace** nebo **ALTER událostí relace** příkazy.
    - **= ON** hodnotu podporuje automatické restartování po Rekonfigurace logické databáze z důvodu selhání.

## <a name="new-catalog-views"></a>Nová zobrazení katalogu

Funkce Rozšířené události podporuje několik [katalogu zobrazení](http://msdn.microsoft.com/library/ms174365.aspx). Zobrazení katalogu říct o *metadata nebo definice* relací vytvořené uživatelem událostí v aktuální databázi. Zobrazení nevrátí informace o instancích relace aktivní události.

| Název<br/>zobrazení katalogu | Popis |
|:--- |:--- |
| **sys.database_event_session_actions** |Vrátí řádek pro každou akci v každé události relace události. |
| **sys.database_event_session_events** |Vrátí řádek pro každou jednotlivou událost v relaci události. |
| **sys.database_event_session_fields** |Vrátí řádek pro každý možné přizpůsobit sloupec, který byl explicitně nastavit na události a cíle. |
| **sys.database_event_session_targets** |Vrátí řádek pro každý cíl události pro relace události. |
| **sys.database_event_sessions** |Vrátí řádek pro každou relaci události v databázi SQL Database. |

V systému Microsoft SQL Server, podobně jako zobrazení katalogu mít názvy, které zahrnují *server\_*  místo *.database\_*. Vzor názvů je jako **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Nové zobrazení dynamické správy [(zobrazení dynamické správy)](http://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database má [zobrazení dynamické správy (zobrazení dynamické správy)](http://msdn.microsoft.com/library/bb677293.aspx) podporující rozšířené události. Zobrazení dynamické správy říct o *active* relace události.

| Název DMV | Popis |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Vrátí informace o akcích relace události. |
| **sys.dm_xe_database_session_events** |Vrací informace o události relací. |
| **sys.dm_xe_database_session_object_columns** |Zobrazuje hodnoty konfigurace pro objekty, které jsou vázány na relaci. |
| **sys.dm_xe_database_session_targets** |Vrací informace o relaci cíle. |
| **sys.dm_xe_database_sessions** |Vrátí řádek pro každou relaci události, které budou platit na aktuální databázi. |

V systému Microsoft SQL Server, jsou podobné zobrazení katalogu pojmenované bez  *\_databáze* část názvu, jako například:

- **Sys.dm_xe_sessions**, místo názvu<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Zobrazení dynamické správy společné pro objekty
Rozšířené události existují další zobrazení dynamické správy, které jsou společné pro Azure SQL Database a serveru Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Najít dostupných rozšířených událostí, akcí a cíle

Můžete spustit jednoduché SQL **vyberte** získat seznam dostupných událostí, akcí a cíle.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Cíle pro relace události vaší databáze SQL

Zde jsou cíle, které můžete zaznamenat výsledky z vaší relace události v databázi SQL:

- [Cílové vyrovnávací paměti prstenec](http://msdn.microsoft.com/library/ff878182.aspx) -stručně uchovává data události v paměti.
- [Cíl události čítače](http://msdn.microsoft.com/library/ff878025.aspx) – počty všechny události, ke kterým došlo během relace rozšířených událostí.
- [Cíl souboru událostí](http://msdn.microsoft.com/library/ff878115.aspx) -zapíše dokončení vyrovnávací paměti s kontejnerem Azure Storage.

[Trasování událostí pro Windows (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) rozhraní API není k dispozici pro rozšířené události v databázi SQL.

## <a name="restrictions"></a>Omezení

Existuje několik rozdílů související se zabezpečením befitting prostředí cloudu SQL Database:

- Rozšířené události vznikla na izolaci klientů jeden model. Relace události v jedné databáze nelze přistupovat k datům nebo události z jiné databáze.
- Nemůžou vystavovat **vytvořit událost relace** příkaz v kontextu **hlavní** databáze.

## <a name="permission-model"></a>Oprávnění modelu

Musíte mít **řízení** oprávnění v databázi k vydávání **vytvořit událost relace** příkaz. Vlastník databáze (dbo) má **řízení** oprávnění.

### <a name="storage-container-authorizations"></a>Povolení kontejneru úložiště

Tokenu SAS můžete vygenerovat pro vaše kontejner úložiště Azure, musíte zadat **rwl** oprávnění. **Rwl** hodnota poskytuje následující oprávnění:

- Čtení
- Zápis
- Seznam

## <a name="performance-considerations"></a>Otázky výkonu

Existují scénáře, kde můžete náročné pomocí rozšířených událostí hromadit aktivnější paměti, než je v pořádku pro celého systému. Proto systém Azure SQL Database dynamicky nastaví a upraví omezení pro aktivní paměti, která může být shromážděných řešením relace události. Mnoha faktorech, přejděte do dynamické výpočtu.

Pokud se zobrazí chybová zpráva s upozorněním, že byla vynucená maximální paměť, jsou některé opravné akce, které můžete provést:

- Spuštění relace méně souběžných události.
- Prostřednictvím vaší **vytvořit** a **ALTER** příkazy pro relace události, snížit množství paměti, které jste zadali na **maximální\_paměti** klauzule.

### <a name="network-latency"></a>Latence sítě

**Soubor událostí** cíl setkat latence sítě nebo selhání při zachování dat na objekty BLOB úložiště Azure. Při čekání komunikaci sítě s k dokončení může být zpoždění další události v databázi SQL. Toto zpoždění může dojít ke snížení velikosti pracovní zátěže.

- Pro zmírnění rizik tento výkon, vyhněte se nastavení **EVENT_RETENTION_MODE** možnost k **NO_EVENT_LOSS** v definic relace události.

## <a name="related-links"></a>Související odkazy

- [Použití Azure PowerShell s Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Rutiny úložiště Azure](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Použití Azure PowerShell s Azure Storage](../storage/common/storage-powershell-guide-full.md) – poskytuje podrobné informace o prostředí PowerShell a službu úložiště Azure.
- [Používání úložiště Blob z rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [Vytvoření relace události (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias příspěvky o rozšířených událostí v systému Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Azure *aktualizace služby* webovou stránku, co nejlépe určen parametrem do Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Další témata ukázkový kód pro rozšířené události jsou dostupné prostřednictvím následujících odkazů. Ale je nutné pravidelně zkontrolovat všechny ukázkové zobrazíte zda ukázku cílem Microsoft SQL Server a databáze SQL Azure. Potom můžete rozhodnout, zda jsou mírně potřebné ke spuštění ukázky.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
