---
title: Postup konfigurace Redis clusterů pro mezipaměť Azure Redis Cache Premium | Microsoft Docs
description: Naučte se vytvářet a spravovat Redis clusteringu pro vaše úroveň Premium instance služby Azure Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: wesmc
ms.openlocfilehash: 4af6545058ab0031d7cd1b38618b6d80204f83b9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-redis-cache"></a>Postup konfigurace Redis clusterů pro mezipaměť Azure Redis Cache Premium
Azure Redis Cache má jiný mezipaměti nabídky, které poskytují flexibilitu při výběru velikost mezipaměti a funkce, včetně funkce úrovně Premium, jako je clustering, trvalosti a podpory služby virtual network. Tento článek popisuje postup konfigurace clusterů v instanci služby Azure Redis Cache premium.

Informace o dalších funkcí mezipaměti premium najdete v tématu [Úvod do Azure Redis Cache Premium vrstvy](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Co je Redis clusteru?
Azure Redis Cache nabízí clusteru Redis jako [implementované v Redis](http://redis.io/topics/cluster-tutorial). S Redis clusteru získáte následující výhody: 

* Možnost automaticky rozdělí datovou sadu mezi více uzly. 
* Možnost pokračovat v operacích při podmnožina uzlů dochází k selhání nebo nemůže komunikovat s ostatními členy clusteru. 
* Další propustnost: zvyšuje propustnost lineárně zvýšit počet horizontálních oddílů. 
* Další velikost paměti: lineárně zvyšuje zvýšit počet horizontálních oddílů.  

Clustering nezvyšuje počet připojení, které jsou k dispozici pro Clusterované mezipaměti. Další informace o velikosti, propustnosti a šířky pásma u prémiových mezipamětí najdete v tématu [jaké mezipaměť Redis nabídky a velikosti použít?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

Ve službě Azure Redis clusteru je poskytován jako primární/replika modelu, kde každý horizontálního oddílu má pár primární/replika s replikací, kde je replikace spravované službou Azure Redis Cache. 

## <a name="clustering"></a>Clustering
Clustering je zapnuta **nová mezipaměť Redis** okno při vytvoření mezipaměti. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Clustering je nakonfigurovaná na **Redis clusteru** okno.

![Clustering][redis-cache-clustering]

Může mít až 10 horizontálními oddíly v clusteru. Klikněte na tlačítko **povoleno** a posuňte jezdec nebo zadejte číslo mezi 1 a 10 pro **počet horizontálních** a klikněte na tlačítko **OK**.

Každý horizontálního oddílu je pár mezipaměti primární/replika spravuje Azure a vynásobením počet horizontálních oddílů vybraná v cenové úrovně velikost mezipaměti je vypočítána celková velikost mezipaměti. 

![Clustering][redis-cache-clustering-selected]

Po vytvoření mezipaměti připojíte k němu a použijte ho stejně jako mezipaměť bez clusterů a Redis distribuuje data v celé mezipaměti horizontálních oddílů. Pokud je diagnostiky [povoleno](cache-how-to-monitor.md#enable-cache-diagnostics), metriky zaznamenání samostatně pro každou horizontálního oddílu a může být [zobrazit](cache-how-to-monitor.md) v okně Redis Cache. 

> [!NOTE]
> 
> Existují některé malé rozdíly při clustering je nakonfigurovaný vyžaduje v klientské aplikace. Další informace najdete v tématu [muset provést jakékoli změny Moje aplikace klienta použít clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Ukázkový kód pro práci s clusteringem s klienta StackExchange.Redis, najdete [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) část [Hello, World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) ukázka.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Změnit velikost clusteru na spuštěný premium mezipaměti
Chcete-li změnit velikost clusteru na spuštěný premium mezipaměti s clusteringem povoleno, klikněte na tlačítko **Redis velikost clusteru** z **prostředků nabídky**.

> [!NOTE]
> Při obecné dostupnosti bylo vydáno Azure Redis Cache na úrovni Premium, velikost clusteru Redis funkce je aktuálně ve verzi preview.
> 
> 

![Velikost clusteru redis][redis-cache-redis-cluster-size]

Chcete-li změnit velikost clusteru, posuvníkem nebo zadejte číslo mezi 1 a 10 v **počet horizontálních** textového pole a klikněte na tlačítko **OK** uložit.

Zvětšení velikosti clusteru zvyšuje maximální propustnost a velikost mezipaměti. Zvětšení velikosti clusteru není zvýšit maximální. připojení k dispozici pro klienty.

> [!NOTE]
> Škálování clusteru běží [MIGRACÍ](https://redis.io/commands/migrate) příkaz, který je nákladné příkaz, takže na minimální, vezměte v úvahu spuštěním této operace v době mimo špičku. Během procesu migrace se zobrazí Špička při zatížení serveru. Škálování clusteru běží s dlouhým proces a množství doba závisí na počet klíčů a velikost hodnoty související s těmito klíči.
> 
> 

## <a name="clustering-faq"></a>Clustering – nejčastější dotazy
Následující seznam obsahuje odpovědi na nejčastější dotazy týkající se clusteringu Azure Redis Cache.

* [Je potřeba provést jakékoli změny Moje aplikace klienta použít clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Klíče rozdělení v clusteru?](#how-are-keys-distributed-in-a-cluster)
* [Co je největší velikost mezipaměti, které lze vytvořit?](#what-is-the-largest-cache-size-i-can-create)
* [Všichni klienti Redis podporují clustering?](#do-all-redis-clients-support-clustering)
* [Postup připojení do mé mezipaměti při clustering je povoleno?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Můžete přímo připojit k jednotlivých horizontálních oddílů Moje mezipaměti?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Můžete nakonfigurovat clustering pro dříve vytvořenou mezipaměti?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Můžete nakonfigurovat clustering pro základní nebo standardní úroveň mezipaměti?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Můžete použít clustering zprostředkovatelům stavu relace ASP.NET Redis a ukládání výstupu do mezipaměti?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Zobrazuje PŘESUNUTÍ výjimky při používání StackExchange.Redis a clusteringu, co dělat?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Je potřeba provést jakékoli změny Moje aplikace klienta použít clustering?
* Pokud je povoleno clustering, je k dispozici pouze databázi 0. Pokud vaše aplikace klienta používá více databází a pokusí se číst nebo zapisovat do jiné databáze než 0, je vyvolána následující výjimka. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Další informace najdete v tématu [specifikace Cluster Redis - implementovaná podmnožina](http://redis.io/topics/cluster-spec#implemented-subset).
* Pokud používáte [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), je nutné použít 1.0.481 nebo novější. Připojení k mezipaměti pomocí stejné [koncové body, porty a klíčů](cache-configure.md#properties) používat při připojování do mezipaměti, která nemá povoleným clusteringem. Jediným rozdílem je, že všechny čtení a zápisu je třeba provést pro databázi 0.
  
  * Ostatní klienty může mít jiné požadavky. V tématu [všech klientů Redis podporují clustering?](#do-all-redis-clients-support-clustering)
* Pokud vaše aplikace používá více operace s klíčem zpracovat v dávce do jednoho příkazu, všechny klíče musí nacházet ve stejné horizontálního oddílu. Vyhledejte klíče v stejné ID horizontálního oddílu, najdete v tématu [klíče rozdělení v clusteru?](#how-are-keys-distributed-in-a-cluster)
* Pokud používáte poskytovatele stavu relace ASP.NET Redis musí používat 2.0.1 nebo vyšší. V tématu [použitím clusteringu s zprostředkovatele stavu relace ASP.NET Redis a ukládání výstupu do mezipaměti?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Klíče rozdělení v clusteru?
Na Redis [modelu distribučního klíče](http://redis.io/topics/cluster-spec#keys-distribution-model) dokumentace: klíče místo je rozděleno do 16384 sloty. Každý klíč je použita hodnota hash a přiřadí k jednomu z těchto sloty, které jsou rozdělené mezi uzly clusteru. Můžete nakonfigurovat, které součástí klíče se rozdělí Ujistěte se, že více klíčů nacházejí ve stejné ID horizontálního oddílu pomocí značek hash.

* Klíče s hash značkou - li jakékoli součástí klíče je uzavřena v `{` a `}`, rozdělí pouze část klíče pro účely určení slotu hash klíče. Například následující 3 klíče umístěna na stejné ID horizontálního oddílu: `{key}1`, `{key}2`, a `{key}3` vzhledem k tomu jenom `key` rozdělí část názvu. Úplný seznam klíčů hash značky specifikace, najdete v části [klíče hash značky](http://redis.io/topics/cluster-spec#keys-hash-tags).
* Klíče bez značky hash - celý název klíče se používá k výpočtu hodnoty hash. Výsledkem je statisticky rovnoměrné rozdělení napříč horizontálních oddílů mezipaměti.

Pro nejlepší výkon a propustnost doporučujeme rovnoměrně distribuci klíčů. Pokud používáte klíče hash značku aplikace odpovídá zajistit klíče rovnoměrně.

Další informace najdete v tématu [modelu distribučního klíče](http://redis.io/topics/cluster-spec#keys-distribution-model), [horizontálního dělení dat Redis clusteru](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), a [klíče hash značky](http://redis.io/topics/cluster-spec#keys-hash-tags).

Ukázkový kód pro práci s clustering a vyhledáním klíčů v stejné ID horizontálního oddílu pomocí klienta StackExchange.Redis, najdete [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) část [Hello, World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) ukázka.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Co je největší velikost mezipaměti, které lze vytvořit?
Největší velikost mezipaměti premium je 53 GB. Můžete vytvořit až 10 horizontálními oddíly, která poskytuje maximální velikost 530 GB. Pokud potřebujete větší velikost můžete [požádat o další](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Další informace najdete v tématu [Azure Redis Cache ceny](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Všichni klienti Redis podporují clustering?
V současné době podporu klientů Redis, clustering. StackExchange.Redis je ten, který podporuje pro ni. Další informace o jiných klientů najdete v tématu [přehrávání s clusterem](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) části [kurzu cluster Redis](http://redis.io/topics/cluster-tutorial).

> [!NOTE]
> Pokud používáte StackExchange.Redis jako váš klient, zkontrolujte, že používáte nejnovější verzi [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 nebo novější pro clustering fungovala správně. Pokud máte problémy s výjimkami přesunutí, přečtěte si téma [přesun výjimek](#move-exceptions) Další informace.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Postup připojení do mé mezipaměti při clustering je povoleno?
Můžete připojit ke své mezipaměti pomocí stejné [koncové body](cache-configure.md#properties), [porty](cache-configure.md#properties), a [klíče](cache-configure.md#access-keys) používat při připojování do mezipaměti, která nemá povoleným clusteringem. Redis spravuje clustering pro back-end, takže není nutné spravovat z vašeho klienta.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Můžete přímo připojit k jednotlivých horizontálních oddílů Moje mezipaměti?
Toto není oficiálně podporován. S třídou uvedená každý horizontálního oddílu se skládá z mezipaměti pár primární/replika souhrnně označované jako instance mezipaměti. Můžete připojit k tyto instance mezipaměti pomocí nástroje rozhraní příkazového řádku redis v [nestabilním](http://redis.io/download) větev úložiště Redis v Githubu. Tato verze implementuje základní podpora při spuštění s `-c` přepínače. Další informace najdete v části [přehrávání s clusterem](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) na [ http://redis.io ](http://redis.io) v [kurzu cluster Redis](http://redis.io/topics/cluster-tutorial).

Bez ssl použijte následující příkazy.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Pro protokol ssl, nahraďte `1300N` s `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Můžete nakonfigurovat clustering pro dříve vytvořenou mezipaměti?
Aktuálně lze povolit pouze clustering při vytvoření mezipaměti. Velikost clusteru můžete změnit po vytvoření mezipaměti, ale nelze přidat clustering do mezipaměti premium nebo odebrání clustering z mezipaměti premium po vytvoření mezipaměti. Cache ve verzi premium s povoleným clusteringem a jedinou horizontálních se liší od cache ve verzi premium stejné velikosti s bez clusteringu.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Můžete nakonfigurovat clustering pro základní nebo standardní úroveň mezipaměti?
Clustering je dostupná jenom pro prémiových mezipamětí.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Můžete použít clustering zprostředkovatelům stavu relace ASP.NET Redis a ukládání výstupu do mezipaměti?
* **Poskytovatel výstupní mezipaměti redis** – bez nutnosti změn.
* **Zprostředkovatel stavu relace redis** – Pokud chcete použít clustering, je nutné použít [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 nebo vyšší nebo výjimku, je vyvolána. Toto je narušující změně; Další informace najdete v části [v2.0.0 nejnovější podrobností o změně](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Zobrazuje PŘESUNUTÍ výjimky při používání StackExchange.Redis a clusteringu, co dělat?
Pokud používáte StackExchange.Redis a přijímat `MOVE` výjimky při použití clusteringu, ujistěte se, že používáte [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) nebo novější. Pokyny týkající se konfigurace aplikace .NET pro používání StackExchange.Redis najdete v tématu [konfigurace klientů mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Další postup
Naučte se používat další funkce mezipaměti premium.

* [Úvod do Azure Redis Cache na úrovni Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







