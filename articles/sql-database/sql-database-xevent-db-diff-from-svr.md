---
title: Rozšířené události ve službě SQL Database | Dokumentace Microsoftu
description: Popisuje rozšířených událostí (XEvents) ve službě Azure SQL Database a jak relací událostí se poněkud liší od událostí relací v systému Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: a4971d680994c9d81e86a4070ea79365ac8ec4dd
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606482"
---
# <a name="extended-events-in-sql-database"></a>Rozšířené události ve službě SQL Database
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Toto téma vysvětluje, jak se mírně liší implementace rozšířeného počtu událostí ve službě Azure SQL Database ve srovnání s rozšířenými událostmi v systému Microsoft SQL Server.

- SQL Database V12 získané funkce rozšířeného počtu událostí ve druhé polovině kalendářního roku 2015.
- SQL Server došlo oproti 2008 rozšířené události.
- Funkce sady rozšířeného počtu událostí pro službu SQL Database je robustní podmnožinu funkcí v systému SQL Server.

*XEvents* je neformální pojmenování, někdy se používá pro "rozšířené události" prostřednictvím blogů a dalších neformální umístění.

Další informace o rozšířených událostech pro Azure SQL Database a Microsoft SQL Server, je k dispozici na:

- [Rychlý Start: Rozšířené události v systému SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Rozšířené události](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Požadavky

Toto téma předpokládá, že již máte základní znalost:

- [Služba Azure SQL Database](https://azure.microsoft.com/services/sql-database/).
- [Rozšířené události](https://msdn.microsoft.com/library/bb630282.aspx) v systému Microsoft SQL Server.

- Hromadné naší dokumentaci informace o rozšířených událostí se vztahuje na systém SQL Server a SQL Database.

Předchozí vystavení následujících položek je užitečný, pokud zvolíte soubor událostí jako [cílové](#AzureXEventsTargets):

- [Služba Azure Storage](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Pomocí Azure Powershellu s Azure Storage](../storage/common/storage-powershell-guide-full.md) – poskytuje podrobné informace o prostředí PowerShell a službu Azure Storage.

## <a name="code-samples"></a>Ukázky kódů

Související témata obsahují dvě ukázky kódu:


- [Kód cyklické vyrovnávací paměti cílového pro rozšířené události ve službě SQL Database](sql-database-xevent-code-ring-buffer.md)
    - Krátký jednoduchý skript Transact-SQL.
    - Budeme v tomto tématu ukázky kódu, jakmile budete hotovi s cílem cyklické vyrovnávací paměti by měla uvolnit její prostředky pomocí provádí alter myší zvýraznit `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` příkazu. Později můžete přidat další instanci cyklické vyrovnávací paměti podle `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Cílový kód souboru události pro rozšířené události ve službě SQL Database](sql-database-xevent-code-event-file.md)
    - Fáze 1 se prostředí PowerShell k vytvoření kontejneru služby Azure Storage.
    - Fáze 2 je příkazů jazyka Transact-SQL, který používá kontejner Azure Storage.

## <a name="transact-sql-differences"></a>Rozdíly v Transact-SQL


- Při spuštění [vytvořit událost relace](https://msdn.microsoft.com/library/bb677289.aspx) příkaz na SQL serveru, je použít **ON SERVER** klauzuli. Ale v databázi SQL používáte **databáze dále** klauzule místo.


- **Databáze dále** klauzule platí také pro [ALTER události relace](https://msdn.microsoft.com/library/bb630368.aspx) a [VYŘADIT události relace](https://msdn.microsoft.com/library/bb630257.aspx) příkazů jazyka Transact-SQL.


- Osvědčeným postupem je zahrnují možnost relace události **STARTUP_STATE = ON** ve vaší **vytvořit událost relace** nebo **ALTER události relace** příkazy.
    - **= ON** hodnota podporuje automatické restartování po rekonfiguraci logickou databázi z důvodu převzetí služeb při selhání.

## <a name="new-catalog-views"></a>Nová zobrazení katalogu

Funkce Rozšířené události podporuje několik [zobrazení katalogu](https://msdn.microsoft.com/library/ms174365.aspx). Informace o zobrazení katalogu *metadat nebo definice* z relace události vytvořené uživatelem v aktuální databázi. Zobrazení informací o instancích relace aktivní události nevrátí.

| Název<br/>Zobrazení katalogu | Popis |
|:--- |:--- |
| **sys.database_event_session_actions** |Vrátí řádek pro každou akci u každé události relace události. |
| **sys.database_event_session_events** |Vrátí řádek pro každou jednotlivou událost v relaci události. |
| **sys.database_event_session_fields** |Vrátí řádek pro každý určujte sloupec, který byl explicitně nastavit na události a cíle. |
| **sys.database_event_session_targets** |Vrátí řádek pro každý cíl události pro relace události. |
| **sys.database_event_sessions** |Vrátí řádek pro každou relaci události v databázi SQL Database. |

V systému Microsoft SQL Server, podobně jako zobrazení katalogu mít názvy, které zahrnují *server\_*  místo *.database\_*. Vzor názvů je třeba **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Nové zobrazení dynamické správy [(DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database má [zobrazení dynamické správy (DMV)](https://msdn.microsoft.com/library/bb677293.aspx) , které podporují rozšířené události. Zobrazení dynamické správy informovat o *aktivní* relace události.

| Název zobrazení dynamické správy | Popis |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Vrátí informace o akcích relace události. |
| **sys.dm_xe_database_session_events** |Vrátí informace o relaci události. |
| **sys.dm_xe_database_session_object_columns** |Ukazuje konfigurační hodnoty pro objekty, které jsou vázány na relaci. |
| **sys.dm_xe_database_session_targets** |Vrátí informace o cíle relace. |
| **sys.dm_xe_database_sessions** |Vrátí řádek pro každou relaci události, které působí na aktuální databázi. |

V systému Microsoft SQL Server, jsou pojmenované podobné zobrazení katalogu bez  *\_databáze* část názvu, jako například:

- **Sys.dm_xe_sessions**, místo názvu<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Společné pro zobrazení dynamické správy
Rozšířené události existují další zobrazení dynamické správy, které jsou společné pro Azure SQL Database a systému Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Vyhledání dostupných rozšířených událostí, akcí a cíle

Můžete spouštět jednoduché SQL **vyberte** získat seznam dostupných událostí, akcí a cíle.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Cíle pro vaše relace událostí databáze SQL

Tady jsou cíle, které můžete zaznamenat výsledky z relací prostředí událostí pro službu SQL Database:

- [Cíle cyklické vyrovnávací paměti](https://msdn.microsoft.com/library/ff878182.aspx) -stručně uchovává data událostí v paměti.
- [Cíl události čítače](https://msdn.microsoft.com/library/ff878025.aspx) – spočítá všechny události, ke kterým dochází během relace rozšířených událostí.
- [Cíle souboru událostí](https://msdn.microsoft.com/library/ff878115.aspx) -zapíše kompletní vyrovnávací paměti do kontejneru služby Azure Storage.

[Trasování událostí pro Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) rozhraní API není k dispozici pro rozšířené události v databázi SQL.

## <a name="restrictions"></a>Omezení

Existuje několik rozdílů související se zabezpečením befitting cloudovém prostředí služby SQL Database:

- Rozšířené události jsou založena na modelu izolace jednoho tenanta. Relace události v jedné databázi nelze přistupovat k události nebo data z jiné databáze.
- Nemůžou vystavovat **vytvořit událost relace** příkaz v kontextu **hlavní** databáze.

## <a name="permission-model"></a>Oprávnění modelu

Musíte mít **ovládací prvek** v databázi oprávnění k vydávání **vytvořit událost relace** příkazu. Vlastník databáze (dbo) má **ovládací prvek** oprávnění.

### <a name="storage-container-authorizations"></a>Povolení kontejner úložiště

SAS token můžete vygenerovat pro váš kontejner Azure Storage, musíte zadat **rwl** oprávnění. **Rwl** hodnota poskytuje následující oprávnění:

- Čtení
- Zápis
- Seznam

## <a name="performance-considerations"></a>Otázky výkonu

Existují scénáře, ve kterém náročné na použití rozšířených událostí lze nashromáždit aktivnější paměti, než je v pořádku pro celý systém. Systém Azure SQL Database proto dynamicky nastaví a nastavuje omezení pro objem aktivní paměti, která může být shromážděných řešením relace události. Řada faktorů, přejděte do dynamický výpočet.

Pokud se zobrazí chybová zpráva s upozorněním, že maximální paměti bylo vynuceno, jsou některé opravné akce, které si můžete:

- Spuštění relace méně souběžných události.
- Prostřednictvím vaší **vytvořit** a **ALTER** příkazy pro relace události, snížit množství paměti, které jste zadali na **maximální\_paměti** klauzuli.

### <a name="network-latency"></a>Latence sítě

**Soubor událostí** cílové zaznamenat latence sítě nebo selhání při zachování dat pro objekty BLOB služby Azure Storage. Další události ve službě SQL Database můžou být zpožděné čekají na síťovou komunikaci na dokončení. Toto zpoždění může zpomalit vaši úlohu.

- Ke zmírnění tohoto rizika výkonu, nenastavujte **EVENT_RETENTION_MODE** umožňuje **NO_EVENT_LOSS** ve svých definicích relace události.

## <a name="related-links"></a>Související odkazy

- [Použití Azure Powershellu s Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Rutiny služby Azure Storage](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Pomocí Azure Powershellu s Azure Storage](../storage/common/storage-powershell-guide-full.md) – poskytuje podrobné informace o prostředí PowerShell a službu Azure Storage.
- [Použití Blob storage pomocí technologie .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [Vytvoření relace události (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias příspěvky o rozšířené události v systému Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Azure *aktualizace služeb* webovou stránku, zúžit parametrem ke službě Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Další témata ukázkový kód pro rozšířené události jsou k dispozici prostřednictvím následujících odkazů. Však musí pravidelně zkontrolujte všechny ukázky a zda vzorku, zaměřuje Microsoft SQL Server a Azure SQL Database. Pak se můžete rozhodnout, zda jsou menší změny potřebné ke spuštění ukázky.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
