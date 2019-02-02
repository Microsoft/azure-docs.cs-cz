---
title: Přidání horizontálního oddílu pomocí nástrojů pro elastické databáze | Dokumentace Microsoftu
description: Nastavena tom, jak používat rozhraní API pružné škálování pro přidání nových horizontálních oddílů do horizontálního oddílu.
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
ms.openlocfilehash: dda3c34dccfdaa041cf9f547244d5529482a3138
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662546"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Přidání horizontálního oddílu pomocí nástrojů pro elastické databáze

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Přidání horizontálního oddílu pro novou oblast nebo klíč

Aplikace často potřebují pro přidání nových horizontálních oddílů pro zpracování dat, která se očekává z nové klíče nebo klíče oblastí mapy horizontálních oddílů, který již existuje. Například horizontálně dělené aplikace podle ID Tenanta může být nutné poskytnout nový horizontální oddíl pro nového klienta, nebo měsíční horizontálně dělených dat může být nutné nový horizontální oddíl zřídit před zahájením nové měsíčně.

Nový rozsah hodnot klíčů již není součástí mapování stávající, jde snadno přidat nový horizontální oddíl a přidružit nový klíč a rozsah na daném horizontálním oddílu.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Příklad: Přidání horizontálního oddílu a rozsah jeho do existující mapy horizontálních oddílů

Tento příklad používá TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) metody a vytvoří instanci ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) třídy. V ukázce níže, databázi s názvem **sample_shard_2** a všechny objekty nezbytné schématu uvnitř této byly vytvořeny pro uložení rozsah [300, 400).  

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

Pro verzi rozhraní .NET můžete také pomocí prostředí PowerShell jako alternativu k vytvoření nového správce mapování horizontálních oddílů. Příkladem je k dispozici [tady](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Přidání horizontálního oddílu pro prázdnou částí stávající rozsah

V některých případech je již namapována na horizontální oddíl rozsahu a částečně naplněný daty, ale nyní chcete nadcházející data směrovat do jiného horizontálního oddílu. Například můžete horizontálních oddílů v rozmezí dnů a obsahovat již přidělené 50 dnů do horizontálního oddílu, ale dne 24 chcete dat objevil v rozdílných horizontálních oddílech. Elastické databáze [nástroj split-merge](sql-database-elastic-scale-overview-split-and-merge.md) mohou provést tuto operaci, ale pokud přesun dat není nutné (například data pro rozsah dnů [25, 50), to znamená, dne 25 (včetně) až 50 exkluzivní, ještě neexistuje) můžete to provést zcela přímo pomocí rozhraní API Správce mapování horizontálních oddílů.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Příklad: rozdělením rozsah a prázdnou část přiřazování k nově přidané horizontálních oddílů

Databázi s názvem "sample_shard_2" a všechny objekty nezbytné schématu uvnitř této byly vytvořeny.  

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

**Důležité**:  Tento postup použijte, pouze pokud jste si jisti, že rozsah aktualizovaná mapování je prázdný.  Předchozí metody nezaškrtávejte políčko dat pro rozsah přesouvaných, takže je vhodné zahrnout kontroly ve vašem kódu.  Pokud je řádků v rozsahu, který se přesouvá, distribuce skutečná data nebudou odpovídat mapa aktualizovaná horizontálních oddílů. Použití [nástroj split-merge](sql-database-elastic-scale-overview-split-and-merge.md) místo toho provádět operace v těchto případech.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
