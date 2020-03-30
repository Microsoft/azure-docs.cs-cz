---
title: Migrace existujících databází za účelem horizontálního navýšení kapacity | Dokumenty společnosti Microsoft
description: Převést stočené databáze tak, aby používaly nástroje elastické databáze vytvořením správce map svižných pásů
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c776f4ac09626f0abd1eb754cde391a1c5447627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421217"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrace existujících databází za účelem horizontálního navýšení kapacity

Snadno spravujte vaše stávající horizontální databáze s horizontálním navýšením kapacity pomocí databázových nástrojů Azure SQL Database (například [klientská knihovna elastická databáze).](sql-database-elastic-database-client-library.md) Nejprve převeďte existující sadu databází tak, aby [používala správce map svižných bitových částí](sql-database-elastic-scale-shard-map-management.md).

## <a name="overview"></a>Přehled

Migrace existující databáze s oddíly:

1. Připravte [databázi správce map skřípků](sql-database-elastic-scale-shard-map-management.md).
2. Vytvořte mapu úlomků.
3. Připravte jednotlivé střepy.  
4. Přidejte mapování do mapy úlomků.

Tyto techniky lze implementovat pomocí [klientské knihovny rozhraní .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)nebo skriptů prostředí PowerShell, které se nacházejí ve [skriptech nástrojů Nástroje azure SQL DB – elastická databáze](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Příklady zde používají skripty prostředí PowerShell.

Další informace o shardmapmanageru naleznete v tématu [Správa map svižné nástroje Shard](sql-database-elastic-scale-shard-map-management.md). Přehled nástrojů elastické databáze najdete v [tématu Přehled prvků elastické databáze](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Příprava databáze správce map skřípků

Správce map horizontálního oddílu je speciální databáze, která obsahuje data pro správu škálovaných databází. Můžete použít existující databázi nebo vytvořit novou databázi. Databáze, která funguje jako správce map síní, by neměla být stejná jako střep. Skript prostředí PowerShell nevytvoří databázi za vás.

## <a name="step-1-create-a-shard-map-manager"></a>Krok 1: vytvoření správce mapy střepů

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Načtení správce mapy skřípků

Po vytvoření můžete načíst správce mapy skřítek s touto rutinou. Tento krok je potřeba pokaždé, když potřebujete použít objekt ShardMapManager.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Krok 2: vytvoření mapy úlomků

Vyberte typ mapy úlomků, který chcete vytvořit. Volba závisí na architektuře databáze:

1. Jeden klient na databázi (Termíny naleznete v [glosáři](sql-database-elastic-scale-glossary.md).)
2. Více klientů na databázi (dva typy):
   1. Mapování seznamu
   2. Mapování rozsahu

Pro model s jedním tenantem vytvořte mapu šiřidla **mapování seznamu.** Model s jedním tenantem přiřadí jednu databázi na klienta. Jedná se o efektivní model pro vývojáře SaaS, protože zjednodušuje správu.

![Mapování seznamu][1]

Víceklientský model přiřadí několik klientů k individuální databázi (a můžete distribuovat skupiny klientů napříč více databázemi). Tento model použijte, pokud očekáváte, že každý klient bude mít malé datové potřeby. V tomto modelu přiřaďte k databázi řadu klientů pomocí **mapování rozsahu**.

![Mapování rozsahu][2]

Nebo můžete implementovat model databáze s více klienty pomocí *mapování seznamu* přiřadit více klientů k jednotlivé databázi. Například DB1 se používá k ukládání informací o ID klienta 1 a 5 a DB2 ukládá data pro klienta 7 a klienta 10.

![Více klientů na jedné DB][3]

**Na základě vašeho výběru zvolte jednu z těchto možností:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Možnost 1: vytvoření mapy úlomků pro mapování seznamu

Vytvořte mapu úlomků pomocí objektu ShardMapManager.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Možnost 2: vytvoření mapy úlomků pro mapování rozsahu

Chcete-li využít tento vzor mapování, hodnoty ID klienta musí být souvislé rozsahy a je přijatelné mít mezeru v rozsahu přeskočením rozsahu při vytváření databází.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Možnost 3: Mapování seznamů v jednotlivých databázích

Nastavení tohoto vzoru také vyžaduje vytvoření mapy seznamu, jak je znázorněno v kroku 2, možnost 1.

## <a name="step-3-prepare-individual-shards"></a>Krok 3: Příprava jednotlivých úlomků

Přidejte každý úlomek (databáze) do správce mapy skřítek. Tím připravíte jednotlivé databáze pro ukládání informací o mapování. Spusťte tuto metodu na každém úlovku.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Krok 4: Přidání mapování

Přidání mapování závisí na druhu mapy šmejdů, které jste vytvořili. Pokud jste vytvořili mapu seznamu, přidáte mapování seznamu. Pokud jste vytvořili mapu rozsahu, přidáte mapování rozsahu.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Možnost 1: mapování dat pro mapování seznamu

Mapování dat přidáním mapování seznamu pro každého klienta.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Možnost 2: mapování dat pro mapování rozsahu

Přidejte mapování rozsahu pro všechny rozsah ID klienta - přidružení databáze:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Možnost kroku 4 3: mapování dat pro více klientů v individuální databázi

Pro každého klienta spusťte Add-ListMapping (možnost 1).

## <a name="checking-the-mappings"></a>Kontrola mapování

Informace o existujících úlomcích a mapováních s nimi spojených lze dotazovat pomocí následujících příkazů:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Souhrn

Po dokončení instalace můžete začít používat klientskou knihovnu elastické databáze. Můžete také použít [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md) a [dotaz s více násobným svižně](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Další kroky

Získejte skripty PowerShellu z [nástrojů nástroje Azure SQL DB-Elastic Database .](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)

Nástroje jsou také na GitHubu: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Pomocí nástroje rozdělení sloučení přesunout data do nebo z modelu více klientů do jednoho modelu klienta. Viz [Nástroj rozdělení sloučení](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Další zdroje

Informace o běžných vzorech architektury dat databázových aplikací softwaru s více tenanty jako služby (SaaS) naleznete v části [Vzory návrhu pro aplikace SaaS s více tenanty s databází Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Otázky a žádosti o funkce

Chcete-li se dotazovat, použijte [fórum databáze SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) a pro požadavky na funkce je přidejte do [fóra pro zpětnou vazbu databáze SQL .](https://feedback.azure.com/forums/217321-sql-database/)

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
