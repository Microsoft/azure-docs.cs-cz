---
title: Dotaz na databáze s oddíly
description: Spouštět dotazy napříč úlomky pomocí klientské knihovny elastické databáze.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: ae14a9fd8fc8479eac596fb694e12e3e0a9027f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067315"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Dotazování s více střepy pomocí nástrojů elastické databáze

## <a name="overview"></a>Přehled

Pomocí [nástrojů elastická databáze](sql-database-elastic-scale-introduction.md)můžete vytvářet řešení databáze s oddíly. **Dotazování s více oddíly** se používá pro úlohy, jako je shromažďování nebo vytváření sestav dat, které vyžadují spuštění dotazu, který se roztáhne přes několik oddílů. (Kontrast to [na směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md), který provádí všechny práce na jeden střep.)

1. Získejte **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) nebo **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) pomocí **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET),](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap) **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) nebo **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metoda. Viz [Vytvoření ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) a [Získat RangeShardMap nebo ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Vytvořte objekt **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)
3. Vytvořte **MultiShardStatement nebo MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Nastavte **vlastnost CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) na příkaz T-SQL.
5. Spusťte příkaz voláním metody **ExecuteQueryAsync nebo ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)
6. Zobrazení výsledků pomocí třídy **MultiShardResultSet nebo MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)

## <a name="example"></a>Příklad

Následující kód ilustruje použití dotazování s více násobnými střepy pomocí daného **shardmap** s názvem *myShardMap*.

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

Klíčovým rozdílem je konstrukce vícestřepových spojů. Kde **SqlConnection** pracuje na jednotlivé databáze, **MultiShardConnection** trvá ***kolekce oddílů jako*** jeho vstup. Naplňte kolekci štřepů z mapy štřepů. Dotaz je pak spuštěn na kolekci střepů pomocí **UNION ALL** sémantiku sestavit jeden celkový výsledek. Volitelně název střepu, kde řádek pochází z lze přidat do výstupu pomocí **ExecutionOptions** vlastnost na příkaz.

Všimněte si volání **myShardMap.GetShards()**. Tato metoda načte všechny oddíly z mapy šikříku a poskytuje snadný způsob, jak spustit dotaz ve všech relevantních databázích. Kolekce střepů pro dotaz s více oddíly lze dále upřesnit provedením linq dotaz přes kolekci vrácené z volání **myShardMap.GetShards()**. V kombinaci s zásady částečné výsledky aktuální funkce v dotazování s více oddíly byl navržen tak, aby dobře fungovat pro desítky až stovky úlomků.

Omezení s dotazováním s více oddíly je aktuálně nedostatek ověření pro shards a shardlets, které jsou dotazovány. Zatímco směrování závislé na datech ověří, zda je daný úlomek součástí mapy střepu v době dotazování, dotazy s více oddíly tuto kontrolu neprovádějí. To může vést k více střepové dotazy spuštěny v databázích, které byly odebrány z mapy střepů.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Vícestřešné dotazy a operace rozděleného sloučení

Dotazy s více oddíly neověřují, zda se shardlety v dotazované databázi účastní probíhajících operací rozdělení sloučení. (Viz [Změna měřítka pomocí nástroje rozdělení sloučení elastické databáze](sql-database-elastic-scale-overview-split-and-merge.md).) To může vést k nekonzistencím, kde řádky ze stejného shardlet zobrazit pro více databází ve stejném dotazu s více oddíly. Uvědomte si tato omezení a zvažte vyprázdnění probíhajícíoperace rozdělení sloučení a změny mapy střepů při provádění dotazů s více oddíly.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]