---
title: Přidání horizontálních oddílů pomocí nástrojů elastické databáze
description: Jak používat rozhraní API pro elastické škálování k přidávání nových horizontálních oddílů do horizontálních oddílů sady.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: efab0234d428a8283845946289cdd1e8a17ded26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792051"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Přidání horizontálních oddílů pomocí nástrojů Elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Přidání horizontálních oddílůu pro nový rozsah nebo klíč

Aplikace často potřebují přidat novou horizontálních oddílů pro zpracování dat očekávaných z nových klíčů nebo rozsahů klíčů pro mapu horizontálních oddílů, která už existuje. Například aplikace horizontálně dělené podle ID tenanta může potřebovat zřídit nové horizontálních oddílů pro nového tenanta, jinak bude horizontálně dělené data měsíčně vyžadovat novou horizontálních oddílů zřízenou před začátkem každého nového měsíce.

Pokud nový rozsah hodnot klíče již není součástí stávajícího mapování, je jednoduché přidat nový horizontálních oddílů a přidružit nový klíč nebo rozsah k tomuto horizontálních oddílů.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Příklad: Přidání horizontálních oddílů a jeho rozsahu do existující mapy horizontálních oddílů

Tato ukázka používá TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](/previous-versions/azure/dn823929(v=azure.100))) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) , metody a vytvoří instanci třídy ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)). V níže uvedené ukázce se vytvořila databáze s názvem **sample_shard_2** a všechny potřebné objekty schématu v ní, aby se nastavil rozsah [300, 400).  

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

Pro verzi rozhraní .NET můžete také použít PowerShell jako alternativu pro vytvoření nového správce mapy horizontálních oddílů. Příklad je k dispozici [zde](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Přidání horizontálních oddílůu pro prázdnou část existujícího rozsahu

V některých případech jste už možná namapovali rozsah na horizontálních oddílů a částečně vyplnili je daty, ale teď chcete, aby se nadcházející data směrovala na jiný horizontálních oddílů. Například jste horizontálních oddílů podle rozsahu dnů a již jste jim přidělili 50 dní do horizontálních oddílů, ale denně 24 chcete, aby se budoucí data vystavila v jiném horizontálních oddílů. [Nástroj pro dělení a slučování](elastic-scale-overview-split-and-merge.md) elastické databáze může tuto operaci provést, ale pokud přesun dat není nutný (například data pro rozsah dnů [25, 50), tj. den 25 včetně až 50 exkluzivní, ještě neexistuje), můžete to udělat zcela pomocí rozhraní API pro správu map horizontálních oddílů přímo.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Příklad: rozdělení rozsahu a přiřazení prázdné části k nově přidanému horizontálních oddílů

Byla vytvořena databáze s názvem "sample_shard_2" a všechny potřebné objekty schématu v ní.  

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

**Důležité**: tuto techniku použijte pouze v případě, že jste si jisti, že rozsah aktualizovaných mapování je prázdný.  Předchozí metody nekontrolují data pro rozsah, který se přesouvá, takže je nejlepší zahrnout do kódu kontroly.  Pokud řádky existují v převáděném rozsahu, skutečná distribuce dat se neshoduje s aktualizovanou mapou horizontálních oddílů. K provedení této operace použijte [Nástroj pro dělení a slučování](elastic-scale-overview-split-and-merge.md) , a to místo v těchto případech.  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]