---
title: Dotaz horizontálně dělené databází Azure SQL | Microsoft Docs
description: Spuštění dotazů napříč horizontálních oddílů pomocí klientské knihovny elastické databáze.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 13d62250112d47ce79f0345828ba69358f153c2d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="multi-shard-querying"></a>Víc horizontálních dotazování
## <a name="overview"></a>Přehled
Pomocí [nástroje elastické databáze](sql-database-elastic-scale-introduction.md), můžete vytvořit řešení horizontálně dělené databáze. **Dotazování víc horizontálních** se používá pro úlohy, jako je kolekce nebo generování sestav dat vyžadující spuštění dotazu roztahovány napříč několika horizontálních oddílů. (K kontrastu [závislé na data směrování](sql-database-elastic-scale-data-dependent-routing.md), který provede všechny práci na jednom horizontálního oddílu.) 

1. Získání **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) nebo **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [rozhraní .NET ](https://msdn.microsoft.com/library/azure/dn807370.aspx)) pomocí **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), nebo **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metoda. V tématu **[vytváření ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)** a  **[získat RangeShardMap nebo ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Vytvoření **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)) objektu.
3. Vytvoření **MultiShardStatement nebo MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Nastavte **Vlastnost CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) k příkazu T-SQL.
5. Spusťte příkaz voláním **ExecuteQueryAsync nebo ExecuteReader** ([Java](), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)) metoda.
6. Zobrazit výsledky pomocí **MultiShardResultSet nebo MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)) třída. 

## <a name="example"></a>Příklad:
Následující kód ukazuje použití více horizontálního oddílu dotazování pomocí danou **ShardMap** s názvem *myShardMap*. 

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString)) 
{ 
    using (MultiShardCommand cmd = conn.CreateCommand())
    { 
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
        cmd.CommandType = CommandType.Text; 
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

        using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
        { 
            while (sdr.Read())
            { 
                var c1Field = sdr.GetString(0); 
                var c2Field = sdr.GetFieldValue<int>(1); 
                var c3Field = sdr.GetFieldValue<Int64>(2);
            } 
        } 
    } 
} 
```

Klíčovým rozdílem je, vytváření připojení více horizontálního oddílu. Kde **SqlConnection** funguje na jedné databáze, **MultiShardConnection** trvá ***kolekce horizontálních oddílů*** jako vstup. Naplnění kolekce horizontálních oddílů z mapy horizontálního oddílu. Pak je spustit dotaz na kolekce horizontálních oddílů pomocí **UNION ALL** sémantiku ke kompilaci jeden celkový výsledek. Volitelně lze přidat název horizontálního oddílu, kde řádek pochází z výstupu pomocí **ExecutionOptions** na příkaz Vlastnosti. 

Všimněte si volání **myShardMap.GetShards()**. Tato metoda načte všechny horizontálních oddílů z mapování horizontálních a poskytuje snadný způsob, jak spustit dotaz napříč všechny příslušné databáze. Kolekce horizontálních oddílů pro více horizontálního oddílu dotazu může být přesnějších další provedením dotaz LINQ nad shromažďováním vrácená z volání **myShardMap.GetShards()**. V kombinaci se zásadou částečné výsledky aktuální možnosti v dotazování víc horizontálních byl navržen tak, aby dobře hodí desítkami až stovky horizontálních oddílů.

Omezení s více horizontálních dotazování právě nedostatek ověření horizontálních oddílů a shardlets, který je dotazován. Při závislé na data směrování ověřuje, že dané horizontálního oddílu je součástí mapy horizontálního oddílu v době dotazování, neprovádějte dotazy víc horizontálních této kontroly. To může vést k víc horizontálních dotazy spuštěnými na databáze, které byly odebrány z mapy horizontálního oddílu.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Víc horizontálních dotazy a operace sloučení rozdělení
Dotazy víc horizontálních Neověřovat, zda shardlets v databázi předmětem dotazu se účastní probíhající operace sloučení rozdělení. (Viz [škálování pomocí nástroje elastické databáze rozdělení sloučení](sql-database-elastic-scale-overview-split-and-merge.md).) To může vést k nekonzistenci kde řádky ze stejné shardlet zobrazit pro více databází ve stejném dotazu více horizontálního oddílu. Mějte na paměti tyto omezení a zvažte vyprázdnění probíhající operace sloučení rozdělení a změny horizontálního oddílu mapy při provádění více horizontálních dotazy.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


