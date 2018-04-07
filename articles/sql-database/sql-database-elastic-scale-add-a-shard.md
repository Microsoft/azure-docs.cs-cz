---
title: Přidání horizontálního oddílu pomocí nástroje elastické databáze | Microsoft Docs
description: Nastavit jak přidat nové horizontálních oddílů horizontálního oddílu pomocí rozhraní API elastické škálování.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: b9cdfc492740a73d3181ea8c4265f6c7db80c5d2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Přidání horizontálního oddílu pomocí nástroje elastické databáze
## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Chcete-li přidat horizontálního oddílu pro nový rozsah nebo klíč
Aplikace často potřebují přidat nové horizontálních oddílů pro zpracování dat, která je očekávána z nových klíčů nebo rozsahy klíčů pro mapu horizontálního oddílu, který již existuje. Například aplikace horizontálně dělené podle ID klienta muset zřídit nové horizontálního oddílu pro nového klienta, nebo horizontálně dělené měsíční dat může být nutné nové horizontálních zřídit před zahájením každý nový měsíc. 

Pokud nový rozsah hodnot klíče již není součástí existující mapování, se snadno přidat nové horizontálního oddílu a přidružit nový klíč nebo rozsahu do této horizontálního oddílu. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Příklad: Přidání horizontálního oddílu a její rozsah do existující mapu horizontálního oddílu
V tomto příkladu TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.trygetshard), [.NET](https://msdn.microsoft.com/library/azure/dn823929.aspx)) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)), CreateRangeMapping ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping\(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0}\))) metody a vytvoří instanci ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base._shard_location), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.)) třída. V ukázce níže, databáze s názvem **sample_shard_2** a všechny objekty nezbytné schématu do něj byly vytvořeny pro uložení rozsah [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added. 
Shard shard2 = null; 

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
{ 
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
} 

// Create the mapping and associate it with the new shard 
sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 
```

Pro verzi rozhraní .NET můžete také použít PowerShell jako alternativu k vytvoření nového správce mapy horizontálního oddílu. Příkladem je k dispozici [zde](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Chcete-li přidat horizontálního oddílu pro prázdný součástí stávající rozsah
V některých případech je již namapován rozsah horizontálního oddílu a částečně vyplněnou data, ale teď chcete nadcházející data přesměrováni na různých horizontálního oddílu. Například můžete horizontálního oddílu podle rozsahu den a muset horizontálního oddílu již přidělené 50 dnů, ale dne 24 chcete zobrazovat v různých horizontálního oddílu budoucí data. Elastické databáze [nástroji pro sloučení rozdělení](sql-database-elastic-scale-overview-split-and-merge.md) mohou provést tuto operaci, ale pokud přesun dat není potřebné (například data pro rozsah dnů [25, 50), který je, den 25 včetně na 50 vylučují, ještě neexistuje) můžete to provést zcela přímo pomocí rozhraní API pro správu horizontálního oddílu mapy.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Příklad: rozdělení rozsah a prázdný část přiřazování k nově přidané horizontálního oddílu
Databáze s názvem "sample_shard_2" a všechny objekty nezbytné schématu do něj byly vytvořeny.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move 
Shard shard2 = null; 

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
{ 
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
} 

// Split the Range holding Key 25 
sm.SplitMapping(sm.GetMappingForKey(25), 25); 

// Map new range holding [25-50) to different shard: 
// first take existing mapping offline 
sm.MarkMappingOffline(sm.GetMappingForKey(25)); 

// now map while offline to a different shard and take online 
RangeMappingUpdate upd = new RangeMappingUpdate(); 
upd.Shard = shard2; 
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 
```

**Důležité**: použijte tento postup, jen pokud jste si jisti, že rozsah aktualizované mapování je prázdný.  Předchozí metody Neprovádět kontrolu dat pro rozsah přesouvání, proto je nejlepší zahrnout kontroly v kódu.  Pokud řádky existují v rozsahu přesouvání, nebude odpovídat distribuční skutečná data mapy aktualizované horizontálního oddílu. Použití [nástroji pro sloučení rozdělení](sql-database-elastic-scale-overview-split-and-merge.md) k provedení operace místo v těchto případech.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

