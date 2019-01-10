---
title: Dotazování dělených databází Azure SQL | Dokumentace Microsoftu
description: Spouštějte dotazy napříč horizontálními oddíly vytvořeného v klientské knihovně elastic database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: ed7e8346cba2a2243ef71cb9782219fb26481dc7
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190045"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Dotazování více horizontálních oddílů pomocí nástrojů pro elastické databáze

## <a name="overview"></a>Přehled

S [nástrojů Elastic Database](sql-database-elastic-scale-introduction.md), můžete vytvořit řešení horizontálně dělené databáze. **Dotazování více horizontálních oddílů** se používá pro úkoly, jako je shromažďování a generování sestav dat, které vyžadují spuštění dotazu, který roztáhne mezi několika horizontálními oddíly. (Tuto hodnotu na kontrast [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md), který provádí veškerou práci na jeden horizontální oddíl.)

1. Získání **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) nebo **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) pomocí **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), nebo **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metody. Zobrazit [sestavením ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) a [získat RangeShardMap nebo ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Vytvoření **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) objektu.
3. Vytvoření **MultiShardStatement nebo MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Nastavte **Vlastnost CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) k příkazu T-SQL.
5. Spusťte příkaz voláním **ExecuteQueryAsync nebo ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) metody.
6. Zobrazit výsledky pomocí **MultiShardResultSet nebo MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) třídy.

## <a name="example"></a>Příklad:

Následující kód ukazuje použití více horizontálních oddílů pomocí dotazování daný **ShardMap** s názvem *myShardMap*.

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

Klíčovým rozdílem je konstrukce připojení více horizontálních oddílů. Kde **SqlConnection** funguje v izolované databáze **MultiShardConnection** přijímá ***sadu horizontálních oddílů*** jako vstup. Naplnění kolekce horizontálních oddílů z mapy horizontálních oddílů. Potom spuštění dotazu na sadu horizontálních oddílů pomocí **UNION ALL** sémantiku sestavit jeden celkový výsledek. Volitelně můžete do výstupu pomocí přidali jméno horizontálních oddílů, odkud pochází řádku z **ExecutionOptions** vlastnost příkazu.

Všimněte si volání **myShardMap.GetShards()**. Tato metoda načte všechny horizontální oddíly z mapy horizontálních oddílů a poskytuje snadný způsob, jak spustit dotaz napříč všemi databázemi relevantní. Kolekce horizontálních oddílů pro dotazování více horizontálních oddílů může být kontrast dále provedením dotazu LINQ nad shromažďováním vrácená z volání **myShardMap.GetShards()**. V kombinaci se zásadou částečných výsledků byla navržena tak, aby fungovat dobře pro desítky až stovek horizontálních oddílů aktuální funkce v dotazování více horizontálních oddílů.

Omezení týkajícího se dotazování více horizontálních oddílů je aktuálně chybějící ověření pro horizontální oddíly a shardlety, které se generuje dotaz. Zatímco směrování závislé na datech ověří, jestli daný horizontálních oddílů je součástí mapy horizontálních oddílů v době dotazu, neprovádějte více horizontálních oddílů dotazy tato kontrola. To může vést k více horizontálních oddílů dotazy, které běží na databáze, které byly odebrány z mapy horizontálních oddílů.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Více horizontálních oddílů dotazy a operace dělení a slučování

Dotazy na více horizontálních oddílů není ověřte, zda shardletů poslal dotaz databáze se účastní probíhající operace dělení a slučování. (Viz [škálování s využitím nástroje Elastic Database dělení a slučování](sql-database-elastic-scale-overview-split-and-merge.md).) To může vést k nekonzistencím kde zobrazit řádky ze stejného shardletu pro více databází ve stejném dotazu více horizontálních oddílů. Mějte na paměti těchto omezení a vyprázdnění probíhající operace dělení a slučování a změny mapování horizontálních oddílů v úvahu při provádění dotazů více horizontálních oddílů.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]