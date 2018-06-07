---
title: Elastické škálování Azure SQL – nejčastější dotazy | Microsoft Docs
description: Časté otázky k Azure SQL Database elastické škálování.
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 94ae9549bb5e09c80703a7db316675bff1272372
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647472"
---
# <a name="elastic-database-tools-faq"></a>Nástroje elastické databáze – nejčastější dotazy
#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Jak se pokud je nutné klienta jednou za horizontálního oddílu a žádný klíč horizontálního dělení, naplnit horizontálního dělení klíč pro informací o schématu?
Objekt informace o schématu slouží pouze k rozdělení sloučení scénáře. Pokud aplikace je ze své podstaty jednoho klienta, nevyžaduje Nástroj sloučení rozdělení a proto není nutné k naplnění objektu informace o schématu.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Jste vybaveny databáze a už mám manažera mapování horizontálních, jak zaregistrovat novou databázi jako horizontálního oddílu?
Najdete v tématu  **[přidání do aplikace pomocí klientské knihovny elastické databáze ID horizontálního oddílu](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Kolik nástroje elastické databáze stojí?
Pomocí klientské knihovny elastické databáze není nesnižuje veškeré náklady. Pouze pro databáze Azure SQL, které používáte pro horizontálních oddílů a správce mapy horizontálního oddílu, stejně jako role web nebo worker, které zřídíte pro nástroj sloučení rozdělení naběhnou první náklady.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Proč po přidání horizontálního oddílu z jiného serveru nejsou k práci pomocí pověření?
Nepoužívejte přihlašovací údaje ve tvaru "ID uživatele =username@servername", jednoduše použijte "ID uživatele = uživatelské jméno".  Být také, zda přihlášení "username" má oprávnění na horizontálního oddílu.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Je třeba vytvořit správce mapy horizontálního oddílu a naplnit horizontálních oddílů při každém spuštění aplikace?
Ne – vytvoření mapy správce horizontálního oddílu (například  **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) se o jednorázovou operaci.  Vaše aplikace by měla používat volání **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** v době spuštění aplikace.  Existuje má pouze jedno takové volání za domény aplikace.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Otázky týkající se používání nástroje elastické databáze je nutné, jak je odpovědi získat?
Kontaktujte nás na [fórum Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Při připojení k databázi pomocí klíče horizontálního dělení doručení, I můžete stále dotaz na data pro další horizontálního dělení klíče na stejné ID horizontálního oddílu.  Je to záměrné?
Rozhraní API elastické škálování získáte připojení ke správné databázi pro klíč horizontálního dělení, ale neposkytuje klíče filtrování horizontálního dělení.  Přidat **kde** klauzule do dotazu omezit obor na horizontálního dělení zadaný klíč, v případě potřeby.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Lze použít na jinou edici databáze Azure pro každý horizontálního oddílu v sadě horizontálního oddílu?
Ano, horizontálního oddílu je jednotlivé databáze a jeden horizontálních proto může být na edici Premium, zatímco jiné se edice Standard. Edice horizontálního oddílu můžete navíc škálovat nahoru nebo dolů vícekrát po dobu platnosti horizontálního oddílu.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Ukládá zřídit nástroj sloučení rozdělení (nebo odstranit) databáze během operace rozdělení nebo sloučení?
Ne. Pro **rozdělení** operace, je cílová databáze musí existovat pomocí příslušného schématu a zaregistrovat u správce mapy horizontálního oddílu.  Pro **sloučení** operace, je potřeba odstranit horizontálního oddílu ze správce mapy horizontálního oddílu a potom odstraňte tuto databázi.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

