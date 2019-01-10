---
title: Správa přihlašovacích údajů v klientské knihovně elastic database | Dokumentace Microsoftu
description: Jak nastavit správnou úroveň přihlašovací údaje správce jen pro čtení, pro elastické databáze aplikace
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
ms.openlocfilehash: e6c7d682d9b16266208baeeff14168b3da157251
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54186615"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Přihlašovací údaje použité pro přístup ke klientské knihovně Elastic Database

[Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md) používá tři různé typy přihlašovacích údajů pro přístup k [správce mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md). Podle potřeby použijte přihlašovací údaje s nejnižší úroveň přístupu je to možné.

* **Přihlašovacích údajů pro správu**: pro vytvoření nebo manipulace s správce mapování horizontálních oddílů. (Viz [Glosář](sql-database-elastic-scale-glossary.md).)
* **Přístup k přihlašovacím údajům**: přístup k stávající správce mapování horizontálních oddílů k získání informací o horizontálních oddílů.
* **Přihlašovací údaje pro připojení**: pro připojení do horizontálních oddílů.

Viz také [Správa databází a přihlášení ve službě Azure SQL Database](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Informace o přihlašovacích údajů pro správu

Přihlašovacích údajů pro správu se používají k vytváření **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) objektu pro aplikace, které pracují s mapy horizontálních oddílů. (Viz například [přidání horizontálního oddílu používání nástrojů Elastic Database](sql-database-elastic-scale-add-a-shard.md) a [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md)). Uživatel klientské knihovny pro elastické škálování vytvoří SQL uživatelé a přihlašování SQL a zajistí, že každý jsou udělena oprávnění pro čtení a zápis na databázi mapování globální horizontálních oddílů a také všechny databáze horizontálních oddílů. Tyto přihlašovací údaje se používají k zachování mapy horizontálních oddílů globální a místní horizontálními oddíly mapy při jsou prováděny změny mapování horizontálních oddílů. Například vytvořte objekt správce mapování horizontálních oddílů pomocí přihlašovacích údajů pro správu (pomocí **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Proměnná **smmAdminConnectionString** je připojovací řetězec, který obsahuje přihlašovacích údajů pro správu. ID uživatele a heslo poskytují přístup pro čtení a zápis k databázi mapování horizontálních oddílů a jednotlivé horizontální oddíly. Připojovací řetězec pro správu také obsahuje název serveru a název databáze pro identifikaci databáze mapování globální horizontálních oddílů. Tady je připojovací řetězec pro tento účel:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Nepoužívejte hodnot ve formě "username@server" – místo toho použijte hodnotu "username".  Je to proto, že přihlašovací údaje musí fungovat proti databáze správce mapování horizontálních oddílů a jednotlivé horizontální oddíly, které mohou být na různých serverech.

## <a name="access-credentials"></a>Přihlašovací údaje pro přístup

Při vytváření správce mapování horizontálního oddílu v aplikaci, která není spravovat mapy horizontálních oddílů, použijte přihlašovací údaje, které mají oprávnění jen pro čtení na mapě globální horizontálních oddílů. Informace získané z mapy horizontálních oddílů globální v rámci těchto přihlašovacích údajů se používá pro [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md) a naplňte mezipaměť mapy horizontálního dělení na straně klienta. Přihlašovací údaje, které jsou poskytované prostřednictvím stejný vzor volání do **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Všimněte si použití **smmReadOnlyConnectionString** tak, aby odrážely použití jiných přihlašovacích údajů pro tento přístup jménem **bez oprávnění správce** uživatelé: nesmí tyto přihlašovací údaje poskytnout oprávnění k zápisu na mapy horizontálních oddílů globální.

## <a name="connection-credentials"></a>Přihlašovací údaje pro připojení

Další přihlašovací údaje jsou potřeba při používání **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) metody pro přístup k horizontálním oddílu přidružený klíč horizontálního dělení. Tyto přihlašovací údaje musí poskytnout oprávnění pro přístup jen pro čtení k tabulkám, mapování místní horizontálních oddílů umístěný v horizontálním oddílu. To je potřeba k ověření připojení pro směrování závislé na datech v horizontálním oddílu. Tento fragment kódu umožňuje přístup k datům v rámci směrování závislé na datech:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

V tomto příkladu **smmUserConnectionString** obsahuje připojovací řetězec pro přihlašovací údaje uživatele. Pro službu Azure SQL DB tady je připojovací řetězec pro přihlašovací údaje uživatele:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Jak se pomocí přihlašovacích údajů správce, nepoužívejte hodnot ve formě "username@server". Místo toho použijte "username".  Všimněte si také, že připojovací řetězec neobsahuje název serveru a název databáze. Důvodem je, **OpenConnectionForKey** volání automaticky směruje připojení ke správné horizontálních oddílů na základě klíče. Název databáze a název serveru, proto nejsou zadány.

## <a name="see-also"></a>Další informace najdete v tématech

[Správa databází a přihlášení ve službě Azure SQL Database](sql-database-manage-logins.md)

[Zabezpečení služby SQL Database](sql-database-security-overview.md)

[Začínáme s úlohy elastické databáze](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
