---
title: Časté otázky o elastickém škálování
description: Nejčastější dotazy týkající se Azure SQL Database elastického škálování.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 8998f03fa44529a5f006936a01f711a279178245
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84045680"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Nejčastější dotazy k nástrojům elastické databáze (FAQ)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Pokud mám jeden tenant na horizontálních oddílů a žádný horizontálního dělení klíč, jak naplnit klíč horizontálního dělení pro informace o schématu

Objekt informace o schématu slouží pouze k rozdělení scénářů sloučení. Je-li aplikace v podstatě jediného tenanta, nevyžaduje Nástroj pro rozdělení a proto není nutné naplnit objekt informace o schématu.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Mám zřídit databázi a už mám správce map horizontálních oddílů, jak tuto novou databázi zaregistrovat jako horizontálních oddílů

Viz [Přidání horizontálních oddílů do aplikace pomocí klientské knihovny elastické databáze](elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Kolik stojí nástroje elastické databáze

Pomocí klientské knihovny elastické databáze se neúčtují žádné náklady. Náklady se účtují pouze pro databáze v Azure SQL Database, které používáte pro horizontálních oddílů a správce map horizontálních oddílů, a také pro webové a pracovní role, které zřídíte pro nástroj pro sloučení.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Proč moje přihlašovací údaje nefungují při přidávání horizontálních oddílů z jiného serveru

Nepoužívejte přihlašovací údaje ve formátu "User ID = username@servername ", místo toho stačí použít "User ID = username".  Ujistěte se také, že přihlašovací jméno uživatele má oprávnění k horizontálních oddílů.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Potřebuji vytvořit správce map horizontálních oddílů a naplnit horizontálních oddílů při každém spuštění aplikací

Ne – vytvoření správce map horizontálních oddílů (například [ShardMapManagerFactory. CreateSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) je jednorázová operace.  Vaše aplikace by měla při spuštění aplikace použít volání [ShardMapManagerFactory. TryGetSqlShardMapManager ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) .  Každé doméně aplikace by mělo být pouze jedno takové volání.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Mám dotazy týkající se používání nástrojů elastické databáze, jak se jim zobrazí odpovědi

Pokud chcete SQL Database, obraťte se na nás na [stránce s dotazem na Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-sql-database.html).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Když získám připojení k databázi pomocí horizontálního dělení klíče, pořád se dají dotazovat na data dalších klíčů horizontálního dělení na stejném horizontálních oddílů.  Je to záměrné

Rozhraní API elastického škálování poskytují připojení ke správné databázi pro horizontálního dělení klíč, ale neposkytují filtrování klíčů horizontálního dělení.  V případě potřeby přidejte do dotazu klauzule **WHERE** pro omezení oboru na poskytnutý horizontálního dělení klíč.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Je možné použít jinou edici SQL Database pro každý horizontálních oddílů ve horizontálních oddílů sadě

Ano, horizontálních oddílů je samostatná databáze a proto jedna horizontálních oddílů může být edice Premium, zatímco jiná je edice Standard. Kromě toho může být edice horizontálních oddílů ve více časech během životnosti horizontálních oddílů.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Poskytuje nástroj rozdělit sloučení (nebo odstraní) databázi během operace rozdělení nebo sloučení.

Ne. Pro **rozdělené** operace musí cílová databáze existovat s příslušným schématem a musí být zaregistrovaná ve Správci map horizontálních oddílů.  Pro operace **sloučení** je nutné odstranit horizontálních oddílů ze Správce map horizontálních oddílů a pak odstranit databázi.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]