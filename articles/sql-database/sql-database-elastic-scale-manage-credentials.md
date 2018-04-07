---
title: Správa přihlašovacích údajů v knihovně klienta elastické databáze | Microsoft Docs
description: Jak nastavit správnou úroveň přihlašovací údaje správce jen pro čtení, pro elastické databáze aplikace
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c18e5da04fd92fed2cb9e93d13b5f3ecdeaac1a1
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Pověření použitá pro přístup k klientské knihovny pro elastické databáze
[Klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md) používá tři různé druhy přihlašovací údaje pro přístup [správce mapy horizontálního oddílu](sql-database-elastic-scale-shard-map-management.md). Podle potřeb s nejnižší úroveň přístupu je možné použijte přihlašovací údaje.

* **Přihlašovacích údajů pro správu**: pro vytvoření nebo manipulace s manažera mapy horizontálního oddílu. (Viz [Glosář](sql-database-elastic-scale-glossary.md).) 
* **Přístup k pověření**: přístup k existující správce mapy horizontálního oddílu k získání informací o horizontálních oddílů.
* **Přihlašovací údaje pro připojení**: pro připojení k horizontálních oddílů. 

Viz také [Správa databází a přihlašovacích údajů ve službě Azure SQL Database](sql-database-manage-logins.md). 

## <a name="about-management-credentials"></a>O přihlašovacích údajů pro správu
Přihlašovacích údajů pro správu se používají k vytváření **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)) objekt pro aplikace, které pracují s horizontálního oddílu mapy. (Například najdete v části [přidání horizontálních, pomocí nástroje elastické databáze](sql-database-elastic-scale-add-a-shard.md) a [závislé na data směrování](sql-database-elastic-scale-data-dependent-routing.md)). Vytvoří uživatele SQL a SQL přihlášení a zajistí, že každý jsou udělena oprávnění pro čtení a zápis na globální horizontálních mapy databáze a také všechny databáze horizontálního oddílu uživatele Klientská knihovna pro elastické škálování. Tyto přihlašovací údaje se používají k udržování mapy horizontálního oddílu globální a místní horizontálních mapy při provádění změny mapy horizontálního oddílu jsou. Například použít k vytvoření objekt horizontálního oddílu mapa správce přihlašovacích údajů pro správu (pomocí **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)): 

```
// Obtain a shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy); 
```

Proměnná **smmAdminConnectionString** je připojovací řetězec, který obsahuje přihlašovacích údajů pro správu. ID uživatele a heslo poskytují přístup pro čtení a zápis do databáze mapy horizontálního oddílu a jednotlivých horizontálních oddílů. Připojovací řetězec správu také zahrnuje název serveru a název databáze pro identifikaci databázi mapy globální horizontálního oddílu. Zde je typické připojovací řetězec pro tento účel:

```
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 
```

Nepoužívejte hodnoty ve formátu "username@server" – místo toho použijte jen hodnotu "username".  Je to proto, že přihlašovací údaje musí pracovat v databázi manager mapy horizontálního oddílu i jednotlivé horizontálních oddílů, které mohou být na různých serverech.

## <a name="access-credentials"></a>Přihlašovací údaje pro přístup
Při vytváření horizontálního oddílu správce mapy v aplikaci, která není spravovat horizontálního oddílu mapy, použijte přihlašovací údaje, které mají oprávnění jen pro čtení na mapě globální horizontálního oddílu. Informace získané z globální horizontálních mapy pod tyto přihlašovací údaje se používá pro [závislé na data směrování](sql-database-elastic-scale-data-dependent-routing.md) a k naplnění mezipaměti mapování horizontálních na straně klienta. Přihlašovací údaje je zajišťována prostřednictvím stejného vzoru volání do **GetSqlShardMapManager**: 

```
// Obtain shard map manager. 
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Všimněte si použití **smmReadOnlyConnectionString** tak, aby odrážela použít různé přihlašovací údaje pro tento přístup jménem **bez oprávnění správce** uživatele: Tato pověření by nemělo zadejte oprávnění k zápisu na Mapa globální horizontálního oddílu. 

## <a name="connection-credentials"></a>Přihlašovací údaje pro připojení
Další přihlašovací údaje jsou potřeba při používání **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)) metody přístup horizontálního oddílu přidružené k klíči horizontálního dělení. Tyto přihlašovací údaje musí poskytnout oprávnění pro přístup jen pro čtení k místní horizontálního oddílu tabulky mapy umístěný na horizontálního oddílu. To je nutné k provedení ověření platnosti připojení pro směrování dat závisí na horizontálního oddílu. Tento fragment kódu umožňuje přístup k datům v kontextu směrování závislé na data: 

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 
```

V tomto příkladu **smmUserConnectionString** obsahuje připojovací řetězec pro přihlašovací údaje uživatele. Pro databáze SQL Azure zde je typické připojovací řetězec pro přihlašovací údaje uživatele: 

```
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Jak se pomocí přihlašovacích údajů správce, nepoužívejte hodnoty ve formátu "username@server". Místo toho použijte "username".  Všimněte si také, že připojovací řetězec neobsahuje název serveru a název databáze. Důvodem je, že **OpenConnectionForKey** volání automaticky přesměruje připojení k správné horizontálního oddílu na základě klíče. Proto nejsou zadány název databáze a název serveru. 

## <a name="see-also"></a>Další informace najdete v tématech
[Správa databází a přihlášení ve službě Azure SQL Database](sql-database-manage-logins.md)

[Zabezpečení služby SQL Database](sql-database-security-overview.md)

[Začínáme s úlohami elastické databáze](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

