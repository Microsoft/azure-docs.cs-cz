---
title: Pružné škálování Azure SQL – nejčastější dotazy | Dokumentace Microsoftu
description: Nejčastější dotazy ohledně pružné škálování Azure SQL Database.
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
ms.openlocfilehash: f3192aafb6f19695bc99310dd980382510bc633b
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188109"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Nejčastější dotazy (FAQ) nástrojů pro elastické databáze

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Jak se mám jedním tenantem každý horizontální oddíl a žádný klíč horizontálního dělení, naplnění klíč horizontálního dělení informace schématu

Informace o objektu schématu slouží pouze k rozdělení sloučení scénáře. Pokud aplikace je ze své podstaty jednoho tenanta, nevyžaduje nástroji sloučení rozdělení a proto není nutné k naplnění objektu informací o schématu.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Vybaveny databázi a už mám správce mapování horizontálních oddílů, jak se dá zaregistrovat novou databázi jako horizontální oddíl

Podrobnosti najdete na [přidání horizontální oddíl do aplikace pomocí Klientská knihovna elastic database](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Kolik nástrojů elastic database stát

Klientská knihovna elastic database pomocí nejsou spojené žádné náklady. Náklady se účtují jenom za databází Azure SQL, které používáte pro horizontálních oddílů a správce mapování horizontálních oddílů, jakož i webové nebo pracovní role, které zřídíte pro nástroj Split sloučení.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Proč nejsou přihlašovací údaje pracovat, když je možné přidat horizontálních oddílů z jiného serveru

Nepoužívejte přihlašovací údaje ve formě "ID uživatele =username@servername", jednoduše použijte "ID uživatele = username".  Taky se ujistěte, že přihlášení "username" má oprávnění pro horizontálního oddílu.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Je nutné vytvořit správce mapování horizontálních oddílů a naplňte jimi horizontálních oddílů pokaždé, když začnu v mé aplikace

Ne – vytvoření správce mapování horizontálních oddílů (například [ShardMapManagerFactory.CreateSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) se o jednorázovou operaci.  Aby aplikace používala volání [ShardMapManagerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) v době spuštění aplikace.  By měla pouze jedno takové volání pro doménu aplikace.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Mám otázky týkající se používání nástrojů elastic database, jak je odpovědi získat

Kontaktujte nás na [fórum pro Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Když obdržím připojení k databázi pomocí klíče horizontálního dělení, můžu dál dotazy na data pro další klíče horizontálního dělení ve stejném horizontálním oddílu.  Toto chování je záměrné

Rozhraní API pružné škálování umožňují připojení ke správné databázi pro klíč horizontálního dělení, ale neposkytuje filtrování klíče horizontálního dělení.  Přidat **kde** klauzule dotazu v případě potřeby omezit obor ke klíči poskytnutý horizontálního dělení.

## <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Můžu použít jinou edici databáze Azure pro každý horizontální oddíl v sadě horizontálních oddílů

Ano, horizontální oddíl představuje jednotlivé databáze, a proto jeden horizontální oddíl může být Premium edition, zatímco jiné se Standard edition. Edice horizontálního oddílu dál, můžete škálovat směrem nahoru nebo dolů více než jednou po dobu životnosti horizontálního oddílu.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Provede nástroj zřízení dělení slučování (nebo odstranění) databáze během operace dělené tunelové propojení nebo sloučení

Ne. Pro **rozdělit** operace, cílová databáze pomocí příslušného schématu, musí existovat a být registrována pomocí Správce mapování horizontálních oddílů.  Pro **sloučení** operace, je potřeba odstranit horizontální oddíl od správce mapování horizontálních oddílů a odstraňte databázi.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]