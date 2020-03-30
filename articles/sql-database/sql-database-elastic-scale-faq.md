---
title: Nejčastější dotazy k elastickému měřítku
description: Nejčastější dotazy týkající se elastického škálování databáze Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 3eedfb1e9ec59fbe12ee94a65d3702a7ef8ca95a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823645"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Nejčastější dotazy týkající se nástrojů pro elastické databáze

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Pokud mám jednoklienta na střep a žádný klíč srážlivým snímáním, jak naplnit klíč sružkou pro informace o schématu

Objekt informace o schématu se používá pouze k rozdělení scénáře sloučení. Pokud je aplikace ze své podstaty jednoklient, pak nevyžaduje nástroj rozdělení sloučení a proto není nutné naplnit objekt informace o schématu.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Zřizovat databázi a již mám Správce map střepů, jak mohu zaregistrovat tuto novou databázi jako úlomek

Přečtěte si informace [o přidání šiřidla do aplikace pomocí klientské knihovny elastické databáze](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Kolik stojí elastické databázové nástroje

Použití klientské knihovny elastické databáze nevznikne žádné náklady. Náklady narůstají pouze pro databáze Azure SQL, které používáte pro úlomky a Správce map sypu, stejně jako webové/pracovní role, které zřídíte pro nástroj rozdělení sloučení.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Proč moje přihlašovací údaje nefungují, když přidám úlomek z jiného serveru

Nepoužívejte pověření ve formě "ID uživatele=username@servername", místo toho jednoduše použijte "ID uživatele = uživatelské jméno".  Také se ujistěte, že "uživatelské jméno" přihlášení má oprávnění na střep.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Musím vytvořit Správce map sípů a naplnit střepy při každém spuštění aplikací

Ne – vytvoření Správce mapy střepu (například [ShardMapManagerFactory.CreateSqlShardMapManager)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)je jednorázová operace.  Vaše aplikace by měla používat volání [ShardMapManagerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) při spuštění aplikace.  V doméně aplikace by mělo být volání pouze jedno.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Mám otázky týkající se používání elastických databázových nástrojů, jak je mohu získat zodpovězeny

Prosím, kontaktujte nás na [fóru SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Když získám připojení k databázi pomocí klíče s ráždí, stále můžete dotazovat data pro jiné klíče s náložemi na stejném oddílu.  Je to záměrné

Elastické škálování API poskytují připojení ke správné databázi pro klíč horizontálního oddílu, ale neposkytují filtrování klíčů horizontálního horizontálního oddílu.  V případě potřeby přidejte do dotazu klauzule **WHERE,** chcete-li omezit obor na zadaný klíč srážlivých užitků.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Lze použít jinou edici databáze SQL pro každý úlomek v sadě úlomků

Ano, střep je individuální databáze, a proto jeden úlomek může být edice Premium, zatímco jiný je standardní vydání. Dále vydání horizontálního oddílu můžete vertikálně navýšit nebo snížit kapacitu vícekrát během životnosti horizontálního oddílu.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Má rozdělit sloučení nástroj zřizování (nebo odstranit) databáze během operace rozdělení nebo sloučení

Ne. Pro **operace rozdělení** musí cílová databáze existovat s příslušným schématem a musí být registrována u Správce mapy storu.  Pro **operace sloučení** je nutné odstranit šitr ze správce mapy střepu a potom odstranit databázi.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]