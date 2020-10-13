---
title: Migrace existujících databází pro horizontální navýšení kapacity
description: Převod databází horizontálně dělené pro použití Elastic Databasech nástrojů vytvořením správce map horizontálních oddílů
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 95afd0b9117caca4b531c453417217d8a8de7c7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443466"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrace existujících databází pro horizontální navýšení kapacity
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pomocí nástrojů (například [klientské knihovny elastic Database](elastic-database-client-library.md)) můžete snadno spravovat svoje stávající databáze horizontálně dělené s více instancemi. Nejprve převeďte existující sadu databází na použití [správce map horizontálních oddílů](elastic-scale-shard-map-management.md).

## <a name="overview"></a>Přehled

Migrace stávající databáze horizontálně dělené:

1. Připravte [databázi správce mapy horizontálních oddílů](elastic-scale-shard-map-management.md).
2. Vytvořte mapu horizontálních oddílů.
3. Připravte jednotlivé horizontálních oddílůy.  
4. Přidejte mapování na mapu horizontálních oddílů.

Tyto techniky lze implementovat buď pomocí [klientské knihovny .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), nebo pomocí skriptů PowerShellu, které se nacházejí ve [skriptech nástrojů Azure SQL Database-elastic Database](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Příklady používají skripty prostředí PowerShell.

Další informace o ShardMapManager najdete v tématu [Správa map horizontálních oddílů](elastic-scale-shard-map-management.md). Přehled nástrojů pro Elastic Database najdete v tématu [Přehled funkcí elastic Database](elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Příprava databáze správce map horizontálních oddílů

Správce map horizontálních oddílů je speciální databáze, která obsahuje data pro správu databází s horizontálním škálováním kapacity. Můžete použít existující databázi nebo vytvořit novou databázi. Databáze fungující jako správce map horizontálních oddílů by neměla být stejná jako databáze horizontálních oddílů. PowerShellový skript nevytvoří pro vás databázi.

## <a name="step-1-create-a-shard-map-manager"></a>Krok 1: Vytvoření správce map horizontálních oddílů

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Načtení správce map horizontálních oddílů

Po vytvoření můžete načíst správce map horizontálních oddílů pomocí této rutiny. Tento krok je potřeba vždy, když potřebujete použít objekt ShardMapManager.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Krok 2: vytvoření mapy horizontálních oddílů

Vyberte typ mapy horizontálních oddílů, kterou chcete vytvořit. Volba závisí na architektuře databáze:

1. Jeden tenant na databázi (pro výrazy najdete v [glosáři](elastic-scale-glossary.md))
2. Více tenantů na databázi (dva typy):
   1. Mapování seznamu
   2. Mapování rozsahu

V případě modelu s jedním nájemcem vytvořte **mapování seznamu mapování** horizontálních oddílů. Model jednoho tenanta přiřadí jednu databázi na každého tenanta. Toto je efektivní model pro vývojáře v SaaS, který zjednodušuje správu.

![Mapování seznamu][1]

Model víceklientské aplikace přiřadí několik tenantů jednotlivým databázím (a můžete distribuovat skupiny klientů do více databází). Tento model použijte, pokud očekáváte, že každý tenant bude mít malé datové potřeby. V tomto modelu přiřaďte k databázi rozsah klientů pomocí **mapování rozsahu**.

![Mapování rozsahu][2]

Nebo můžete implementovat model víceklientské databáze pomocí *mapování seznamu* , aby bylo možné přiřadit více tenantů k individuální databázi. Například DB1 se používá k ukládání informací o klientech s ID 1 a 5 a DB2 ukládá data pro klienta 7 a klienta 10.

![Více tenantů v jedné databázi][3]

**Podle vašeho výběru vyberte jednu z těchto možností:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Možnost 1: vytvoření mapy horizontálních oddílů pro mapování seznamu

Vytvořte mapu horizontálních oddílů pomocí objektu ShardMapManager.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Možnost 2: vytvoření mapy horizontálních oddílů pro mapování rozsahu

Aby bylo možné použít tento vzor mapování, musí být hodnoty ID tenanta souvislé rozsahy a je přijatelné, aby bylo mezera v rozsahu vynecháno při vytváření databází.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Možnost 3: mapování seznamu pro jednotlivé databáze

Nastavení tohoto modelu také vyžaduje vytvoření mapy seznamu, jak je znázorněno v kroku 2, Option 1.

## <a name="step-3-prepare-individual-shards"></a>Krok 3: příprava jednotlivých horizontálních oddílů

Přidejte jednotlivé horizontálních oddílů (databáze) do správce map horizontálních oddílů. Tím se připraví jednotlivé databáze pro ukládání informací o mapování. Tuto metodu spusťte na každém horizontálních oddílů.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Krok 4: přidání mapování

Přidání mapování závisí na druhu mapy horizontálních oddílů, kterou jste vytvořili. Pokud jste vytvořili mapu seznamu, přidejte mapování seznamu. Pokud jste vytvořili mapu rozsahu, přidejte mapování rozsahů.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Možnost 1: mapování dat pro mapování seznamu

Namapujte data přidáním mapování seznamu pro každého tenanta.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Možnost 2: mapování dat pro mapování rozsahu

Přidejte mapování rozsahu pro všechny rozsahy ID tenanta – přidružení databáze:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Krok 4 – možnost 3: mapování dat pro více tenantů v jednotlivých databázích

Pro každého tenanta spusťte Add-ListMapping (možnost 1).

## <a name="checking-the-mappings"></a>Kontrola mapování

Informace o existujících horizontálních oddílů a mapováních, která jsou k nim přidružená, se dají dotazovat pomocí následujících příkazů:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Shrnutí

Po dokončení instalace můžete začít používat klientskou knihovnu Elastic Database. Můžete také použít [Směrování závislé na datech](elastic-scale-data-dependent-routing.md) a [dotaz multi-horizontálních oddílů](elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Další kroky

Získejte skripty PowerShellu z [skriptů Azure SQL Database-Elastic Database Tools](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Nástroje jsou také na GitHubu: [Azure nebo elastické databáze-Tools](https://github.com/Azure/elastic-db-tools).

Pomocí nástroje pro dělení k přesunu dat do nebo z modelu více tenantů na jeden model tenanta. Viz [Nástroj rozdělit sloučení](elastic-scale-get-started.md).

## <a name="additional-resources"></a>Další zdroje

Informace o běžných vzorech architektury dat databázových aplikací softwaru s více tenanty jako služby (SaaS) naleznete v části [Vzory návrhu pro aplikace SaaS s více tenanty s databází Azure SQL Database](saas-tenancy-app-design-patterns.md).

## <a name="questions-and-feature-requests"></a>Dotazy a žádosti o funkce

V případě otázek použijte [stránku s otázkou Microsoft Q&SQL Database](https://docs.microsoft.com/answers/topics/azure-sql-database.html) a pro žádosti o funkce, přidejte je do [fóra SQL Database Feedback](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png
