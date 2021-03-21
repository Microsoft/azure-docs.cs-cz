---
title: Správa přihlašovacích údajů v klientské knihovně elastické databáze
description: Nastavení správné úrovně přihlašovacích údajů, správce pro aplikace elastické databáze jen pro čtení
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
ms.openlocfilehash: 5b91986d4f94861b87e413c9ff781107c3ed04a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92786594"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Přihlašovací údaje použité pro přístup k klientské knihovně Elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Klientská knihovna elastic Database](elastic-database-client-library.md) používá pro přístup ke [Správci map horizontálních oddílů](elastic-scale-shard-map-management.md)tři různé druhy přihlašovacích údajů. V závislosti na potřebě použijte přihlašovací údaje s nejnižší úrovní přístupu.

* **Přihlašovací údaje pro správu**: pro vytvoření nebo manipulaci se správcem mapy horizontálních oddílů. (Podívejte se na [Glosář](elastic-scale-glossary.md).)
* **Přihlašovací údaje pro přístup**: pro přístup k existujícímu správci map horizontálních oddílů pro získání informací o horizontálních oddílů.
* **Přihlašovací údaje pro připojení**: pro připojení k horizontálních oddílů.

Viz také [Správa databází a přihlášení v Azure SQL Database](logins-create-manage.md).

## <a name="about-management-credentials"></a>Informace o přihlašovacích údajích pro správu

Přihlašovací údaje pro správu se používají k vytvoření objektu **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) pro aplikace, které pracují s mapami horizontálních oddílů. (Například viz [Přidání horizontálních oddílů pomocí nástrojů elastic Database](elastic-scale-add-a-shard.md) a [Směrování závislého na datech](elastic-scale-data-dependent-routing.md)). Uživatel klientské knihovny pro elastické škálování vytvoří uživatele SQL a přihlášení SQL a zajistí, aby každému z nich bylo uděleno oprávnění ke čtení a zápisu v globální databázi horizontálních oddílů map a všech databázích horizontálních oddílů. Tyto přihlašovací údaje se používají k údržbě globálního mapování horizontálních oddílů a mapování místních horizontálních oddílů při provádění změn v mapě horizontálních oddílů. Například použijte pověření pro správu k vytvoření objektu Správce map horizontálních oddílů (pomocí **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Proměnná **smmAdminConnectionString** je připojovací řetězec, který obsahuje přihlašovací údaje pro správu. ID uživatele a heslo poskytují přístup pro čtení a zápis jak pro databázi map horizontálních oddílů, tak pro jednotlivé horizontálních oddílů. Připojovací řetězec pro správu také obsahuje název serveru a název databáze pro identifikaci globální databáze mapy horizontálních oddílů. Zde je typický připojovací řetězec pro tento účel:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Nepoužívejte hodnoty ve formě " username@server " – místo toho stačí použít hodnotu username.  Je to proto, že přihlašovací údaje musí fungovat jak pro databázi horizontálních oddílů Map Manager, tak pro jednotlivé horizontálních oddílů, které se můžou nacházet na různých serverech.

## <a name="access-credentials"></a>Přístup k přihlašovacím údajům

Při vytváření správce map horizontálních oddílů v aplikaci, která nespravuje mapy horizontálních oddílů, použijte přihlašovací údaje, které mají oprávnění jen pro čtení na globální mapě horizontálních oddílů. Informace načtené z globální mapy horizontálních oddílů v rámci těchto přihlašovacích údajů se používají pro [Směrování závislé na datech](elastic-scale-data-dependent-routing.md) a naplnění mezipaměti map horizontálních oddílů v klientovi. Přihlašovací údaje jsou k dispozici prostřednictvím stejného vzoru volání jako **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Všimněte si použití **smmReadOnlyConnectionString** k tomu, aby odráželo používání různých přihlašovacích údajů jménem uživatelů **bez oprávnění správce** : tyto přihlašovací údaje by neměly poskytovat oprávnění k zápisu na globální mapě horizontálních oddílů.

## <a name="connection-credentials"></a>Přihlašovací údaje připojení

Při použití metody **OpenConnectionForKey**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) k přístupu k horizontálních oddílů přidruženého k klíči horizontálního dělení jsou potřeba další přihlašovací údaje. Tyto přihlašovací údaje musí poskytovat oprávnění pro přístup jen pro čtení k místním tabulkám mapy horizontálních oddílů umístěným na horizontálních oddílů. To je nutné k provedení ověřování připojení pro směrování závislé na datech na horizontálních oddílů. Tento fragment kódu umožňuje přístup k datům v kontextu směrování závislého na datech:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

V tomto příkladu **smmUserConnectionString** obsahuje připojovací řetězec pro přihlašovací údaje uživatele. Pro Azure SQL Database je tady typický připojovací řetězec pro přihlašovací údaje uživatele:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Stejně jako u přihlašovacích údajů správce nepoužívejte hodnoty ve formě " username@server ". Místo toho stačí použít "username".  Všimněte si také, že připojovací řetězec neobsahuje název serveru a název databáze. Důvodem je, že volání **OpenConnectionForKey** automaticky směruje připojení ke správnému horizontálních oddílů na základě klíče. Název databáze a název serveru proto nejsou k dispozici.

## <a name="see-also"></a>Viz také

[Správa databází a přihlášení ve službě Azure SQL Database](logins-create-manage.md)

[Zabezpečení SQL Database](security-overview.md)

[úlohy elastické databáze](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]