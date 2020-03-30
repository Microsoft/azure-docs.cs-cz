---
title: Přidání šimetu pomocí elastických databázových nástrojů
description: Jak použít elastická škálovací api k přidání nových horizontálních oddílů do sady horizontálních oddílů.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 4043fd374a314735173a1f07f46c8394592b81e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823718"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Přidání šimetu pomocí nástrojů elastické databáze

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Přidání úlomku pro novou oblast nebo klíč

Aplikace často potřebují přidat nové oddíly pro zpracování dat, která se očekává od nových klíčů nebo rozsahů klíčů, pro mapu šicího dříče, která již existuje. Například aplikace, která je rozdělena podle ID klienta, může potřebovat zřídit nový oddíl pro nového klienta nebo data rozdělená měsíčně může potřebovat nový oddíl zřízeného před začátkem každého nového měsíce.

Pokud nový rozsah hodnot klíčů již není součástí existujícího mapování, je jednoduché přidat nový oddíl a přidružit nový klíč nebo rozsah k tomuto oddílu.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Příklad: přidání úložníku a jeho rozsahu do existující mapy úlomku

Tato ukázka používá TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) metody a vytvoří instanci ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)třídy. V následující ukázce byla vytvořena databáze s názvem **sample_shard_2** a všechny objekty nezbytné ho schématu uvnitř [300, 400).  

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

Pro verzi .NET můžete také použít Prostředí PowerShell jako alternativu k vytvoření nového Správce map sypu. Příklad je k dispozici [zde](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Přidání úlomku pro prázdnou část existující oblasti

V některých případech jste již pravděpodobně namapovali oblast na šitr a částečně ji vyplnili daty, ale nyní chcete, aby nadcházející data byla směrována na jiný úlomek. Například šikřík podle rozsahu dne a již přiděleno 50 dní na šitr, ale v den 24, chcete budoucí data přistát v jiném úlomku. Nástroj [rozdělení sloučení](sql-database-elastic-scale-overview-split-and-merge.md) elastické databáze může tuto operaci provést, ale pokud přesun dat není nutný (například data pro rozsah dnů [25, 50), to znamená, že den 25 včetně až 50 výhradních, ještě neexistuje), můžete to provést zcela pomocí rozhraní API správy mapy svižných pásů přímo.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Příklad: rozdělení rozsahu a přiřazení prázdné části k nově přidanému oddílu

Byla vytvořena databáze s názvem "sample_shard_2" a všechny potřebné objekty schématu uvnitř.  

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

**Důležité:** Tuto techniku použijte pouze v případě, že jste si jisti, že rozsah pro aktualizované mapování je prázdný.  Předchozí metody nekontrolují data pro oblast přesunutou, takže je nejlepší zahrnout kontroly ve vašem kódu.  Pokud existují řádky v oblasti přesunutí, skutečné rozdělení dat nebude odpovídat aktualizované mapě oddílu. Místo toho v těchto případech použijte [nástroj rozdělení sloučení](sql-database-elastic-scale-overview-split-and-merge.md) k provedení operace.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
