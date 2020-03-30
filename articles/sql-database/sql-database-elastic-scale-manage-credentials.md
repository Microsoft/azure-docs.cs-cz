---
title: Správa pověření v klientské knihovně elastické databáze
description: Jak nastavit správnou úroveň přihlašovacích údajů, admin jen pro čtení, pro elastické databázové aplikace
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
ms.openlocfilehash: 91689a32a128584aade8081905e3d1aa3ecb0a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823582"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Pověření používaná pro přístup ke klientské knihovně elastické databáze

Klientská [knihovna elastická databáze](sql-database-elastic-database-client-library.md) používá tři různé druhy pověření pro přístup správce [mapy svižného oddílu](sql-database-elastic-scale-shard-map-management.md). V závislosti na potřebě použijte pověření s nejnižší možnou úrovní přístupu.

* **Pověření pro správu**: pro vytvoření nebo manipulaci s správcem mapy sypu. (Viz [glosář](sql-database-elastic-scale-glossary.md).)
* **Přístupová pověření**: pro přístup k existujícímu správci mapy svižné části a získání informací o úlomcích.
* **Pověření pro připojení**: pro připojení ke střepům.

Viz taky [Správa databází a přihlášení v Azure SQL Database](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Pověření pro správu

Pověření pro správu se používají k vytvoření objektu **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)pro aplikace, které manipulují s mapami úlomků. (Například viz [Přidání šiřidla pomocí nástrojů elastické databáze](sql-database-elastic-scale-add-a-shard.md) a směrování závislé na [datech).](sql-database-elastic-scale-data-dependent-routing.md) Uživatel knihovny klienta s elastickým škálovacím měřítkem vytvoří uživatele SQL a přihlášení SQL a zajistí, aby každému z nich byla udělena oprávnění pro čtení a zápis v databázi globálních map horizontálních oddílů a také ve všech databázích horizontálních oddílů. Tato pověření se používají k udržování globální mapy štřepů a mapy místního oddílu při provádění změn mapy šikméru. Pomocí pověření pro správu můžete například vytvořit objekt správce map svižné části (pomocí **getsqlshardmapmanageru** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Proměnná **smmAdminConnectionString** je připojovací řetězec, který obsahuje pověření pro správu. ID uživatele a heslo poskytují přístup pro čtení a zápis do databáze mapy střepů a jednotlivých oddílů. Připojovací řetězec pro správu také obsahuje název serveru a název databáze k identifikaci globální databáze mapy svižné bitové okno. Zde je typický připojovací řetězec pro tento účel:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Nepoužívejte hodnoty ve forměusername@server" "— místo toho stačí použít hodnotu "uživatelské jméno".  Důvodem je, že pověření musí pracovat proti databázi správce mapy schrupu a jednotlivé oddíly, které mohou být na různých serverech.

## <a name="access-credentials"></a>Přístupová pověření

Při vytváření správce mapy sytry v aplikaci, která nespravuje mapy šmejdů, použijte pověření, která mají oprávnění jen pro čtení na globální mapě oddílu. Informace načtené z mapy globálního oddílu pod těmito pověřeními se používají pro [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md) a k naplnění mezipaměti mapy skřítek na straně klienta. Přihlašovací údaje jsou poskytovány prostřednictvím stejného vzoru volání **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Všimněte si použití **smmReadOnlyConnectionString** odrážet použití různých pověření pro tento přístup jménem uživatelů **bez správce:** tato pověření by neměla poskytovat oprávnění k zápisu na globální mapě shard.

## <a name="connection-credentials"></a>Přihlašovací údaje připojení

Další pověření jsou potřeba při použití **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)metoda pro přístup k oddílu přidruženého ke klíči srážlivým mysem. Tato pověření musí poskytnout oprávnění pro přístup jen pro čtení k místní tabulky mapy šmejdu s bydlištěm na šiřidlo. To je nutné provést ověření připojení pro směrování závislé na datech na úlomku. Tento fragment kódu umožňuje přístup k datům v kontextu směrování závislého na datech:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

V tomto příkladu **smmUserConnectionString** obsahuje připojovací řetězec pro pověření uživatele. Pro Azure SQL DB je zde typický připojovací řetězec pro pověření uživatele:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Stejně jako u pověření správce nepoužívejte hodnotyusername@serverve formě " ". Místo toho stačí použít "uživatelské jméno".  Všimněte si také, že připojovací řetězec neobsahuje název serveru a název databáze. Důvodem je, že volání **OpenConnectionForKey** automaticky přesměruje připojení ke správnému oddílu oddílu na základě klíče. Proto název databáze a název serveru nejsou k dispozici.

## <a name="see-also"></a>Viz také

[Správa databází a přihlášení ve službě Azure SQL Database](sql-database-manage-logins.md)

[Zabezpečení SQL Database](sql-database-security-overview.md)

[Úlohy elastické databáze](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
