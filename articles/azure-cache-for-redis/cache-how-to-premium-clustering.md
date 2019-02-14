---
title: Konfigurace Redis clusteringu pro Azure Cache úrovně Premium pro Redis | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a spravovat clustering Redis vaší úrovně Premium mezipaměti Azure pro instance Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: yegu
ms.openlocfilehash: 602d77f3d4e8ed10c2c964462bc2dc21240cef5c
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235712"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Konfigurace Redis clusteringu pro Azure Cache úrovně Premium pro Redis
Mezipaměti Redis Azure má různé mezipaměti nabídek, které poskytují flexibilitu při výběru velikosti mezipaměti a funkcí, včetně novými funkcemi úrovně Premium jako je clustering, trvalé a podpory služby virtual network. Tento článek popisuje, jak nakonfigurovat řízení clusterů v Azure Cache úrovně premium pro instanci Redis.

Informace o dalších prémiových funkcí mezipaměti, naleznete v tématu [Úvod do mezipaměti Azure Redis na úrovni Premium](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Co je Cluster redis Cache?
Cluster Redis jako nabízí Azure mezipaměti Redis [implementované v Redis](https://redis.io/topics/cluster-tutorial). S clusterem Redis získáte následující výhody: 

* Možnost automaticky rozdělí vaši datovou sadu mezi více uzlů. 
* Možnost pokračovat v operaci při podmnožině uzlů dochází k chybám nebo se nám navázat komunikaci s ostatními členy clusteru. 
* Větší propustnost: Propustnost se lineárně zvyšuje, zvýšit počet horizontálních oddílů. 
* Další velikosti paměti: Zvyšuje lineárně zvýšit počet horizontálních oddílů.  

Clustering nezvyšuje počet připojení, které jsou k dispozici pro clusterové mezipaměti. Další informace o velikosti, propustnosti a šířky pásma u prémiových mezipamětí najdete v tématu [pro velikost a kterou nabídku Redis jaké Azure Cache mám použít?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

Redis cluster se v Azure, nabízí jako primární/replika model, kde každý horizontální oddíl nemá dvojici primární/replika s replikací, kde replikaci spravuje Azure mezipaměti pro službu Redis. 

## <a name="clustering"></a>Clustering
Clustering je zapnutá **nové mezipaměti Redis Azure** okno během vytváření mezipaměti. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Clustering je nakonfigurovaná na **Redis Cluster** okno.

![Clustering][redis-cache-clustering]

Můžete mít až 10 horizontálními oddíly v clusteru. Klikněte na tlačítko **povoleno** a posuňte jezdec nebo zadejte číslo mezi 1 a 10 pro **počet horizontálních oddílů** a klikněte na tlačítko **OK**.

Každý horizontální oddíl je pár mezipaměti primární/replika spravuje Azure a celková velikost mezipaměti se počítá vynásobením počtu horizontálních oddílů vybraná v cenové úrovni velikost mezipaměti. 

![Clustering][redis-cache-clustering-selected]

Po vytvoření mezipaměti k němu připojíte a distribuuje dat napříč horizontálními oddíly mezipaměti použít pouze jako mezipaměť neclusterovaný a Redis. Pokud je Diagnostika [povolené](cache-how-to-monitor.md#enable-cache-diagnostics), metrik se zachycuje, samostatně pro každý horizontální oddíl a může být [zobrazit](cache-how-to-monitor.md) v mezipaměti Azure Redis okno. 

> [!NOTE]
> 
> Existují nějaké drobné rozdíly v klientské aplikace vyžaduje, pokud clustering je nakonfigurovaná. Další informace najdete v tématu [potřeba dělat žádné změny Moje klientská aplikace použít clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Ukázkový kód pro práci s clusteringem s klient StackExchange.Redis, najdete v článku [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) část [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) vzorku.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Změnit velikost clusteru na průběžný mezipaměť premium
Chcete-li změnit velikost clusteru na průběžný premium mezipaměť pomocí clusterování povolené, klikněte na tlačítko **velikost clusteru Redis** z **nabídce prostředků**.

> [!NOTE]
> Zatímco byla uvolněna mezipaměti Azure Redis na úrovni Premium obecně dostupná, velikost clusteru Redis funkce je aktuálně ve verzi preview.
> 
> 

![Velikost clusteru redis][redis-cache-redis-cluster-size]

Chcete-li změnit velikost clusteru pomocí posuvníku nebo zadejte číslo z rozsahu 1 až 10 v **počet horizontálních oddílů** textového pole a klikněte na tlačítko **OK** uložte.

Zvýšením velikosti clusteru zvyšuje maximální propustnosti a velikosti mezipaměti. Zvýšením velikosti clusteru není zvýšit maximální. připojení k dispozici pro klienty.

> [!NOTE]
> Škálování clusteru běží [migrace](https://redis.io/commands/migrate) příkaz, který je nákladné příkaz, tak pro minimální dopad, zvažte spuštění této operace během hodiny mimo špičku. Během procesu migrace se zobrazí nárůst zatížení serveru. Škálování clusteru běží dlouho procesu a množství doba závisí na počtu klíčů a velikosti hodnot spojené s těmito klíči.
> 
> 

## <a name="clustering-faq"></a>Nejčastější dotazy týkající se vytváření clusterů
Následující seznam obsahuje odpovědi na nejčastější dotazy o mezipaměti Azure pro Redis clustering.

* [Je potřeba dělat žádné změny Moje klientská aplikace použít clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Jak se distribuují klíče v clusteru?](#how-are-keys-distributed-in-a-cluster)
* [Co je největší velikost mezipaměti, kterou můžu vytvořit?](#what-is-the-largest-cache-size-i-can-create)
* [Všichni klienti Redis podporují clustering?](#do-all-redis-clients-support-clustering)
* [Jak se připojit k mezipaměť při zapnuté funkci clustering?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Můžu přímo se připojit do jednotlivých horizontálních oddílů Moje mezipaměti?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Můžete nakonfigurovat clusterování pro mezipaměť dříve vytvořeného?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Můžete nakonfigurovat clusterování pro mezipaměť basic nebo standard?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Můžete použít clustering s zprostředkovatele stavu relace ASP.NET Redis a ukládání výstupu do mezipaměti?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Při použití StackExchange.Redis přesun výjimek dochází a clustering, co mám dělat?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Je potřeba dělat žádné změny Moje klientská aplikace použít clustering?
* Pokud je povoleno clustering, je k dispozici pouze databáze 0. Pokud vaše klientská aplikace používá několik databází a pokusu o čtení nebo zápis do jiné databáze než 0, je vyvolána následující výjimka. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Další informace najdete v tématu [specifikaci clusteru Redis - implementované dílčí](https://redis.io/topics/cluster-spec#implemented-subset).
* Pokud používáte [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), je nutné použít 1.0.481 nebo novější. Připojení k mezipaměti pomocí stejných [koncových bodů, porty a klíče](cache-configure.md#properties) , který používáte při připojení k mezipaměti, ve kterém není povoleným clusteringem. Jediným rozdílem je, že všechny operace čtení a zápisů je třeba provést pro databázi 0.
  
  * Jiní klienti můžou mít různé požadavky. Zobrazit [všichni klienti Redis nepodporují clustering?](#do-all-redis-clients-support-clustering)
* Pokud vaše aplikace používá více klíčové operace dávkovány do jediného příkazu, všechny klíče musí nacházet ve stejném horizontálním oddílu. Vyhledejte klíče ve stejném horizontálním oddílu, naleznete v tématu [klíče rozdělení v clusteru?](#how-are-keys-distributed-in-a-cluster)
* Pokud používáte zprostředkovatel stavu relací ASP.NET Redis je nutné použít 2.0.1 nebo vyšší. Zobrazit [slouží vytváření clusterů se zprostředkovatele stavu relace ASP.NET Redis a ukládání výstupu do mezipaměti?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Jak se distribuují klíče v clusteru?
Za Redis [modelu distribučního klíče](https://redis.io/topics/cluster-spec#keys-distribution-model) dokumentaci: Klíče místo je rozděleno do 16384 sloty. Každý klíč je mají hodnotu hash a přiřadí k jednomu z těchto slotů, které jsou distribuovány napříč uzly clusteru. Můžete nakonfigurovat, které součástí klíče se po zahašování použije k zajištění, že více klíčů jsou umístěné ve stejném horizontálním oddílu pomocí značek hash.

* Klíče hash značkou – libovolná součást klíč je uzavřen do `{` a `}`, pouze tuto část klíče se po zahašování použije pro účely stanovení slotu hash klíče. Například následující 3 klíče by nacházet ve stejném horizontálním oddílu: `{key}1`, `{key}2`, a `{key}3` protože pouze `key` se po zahašování použije část názvu. Úplný seznam klíčů hash značky specifikace, naleznete v tématu [klíče hash značky](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Klíče bez značky hash - celý název klíče se používá k výpočtu hodnoty hash. Výsledkem je statisticky rovnoměrná distribuce napříč horizontálními oddíly mezipaměti.

Pro nejlepší výkon a propustnosti doporučujeme rovnoměrně distribuovat klíče. Pokud používáte klíče jsou rovnoměrně distribuovaných zodpovídá aplikace zajistit klíče hash značku.

Další informace najdete v tématu [modelu distribučního klíče](https://redis.io/topics/cluster-spec#keys-distribution-model), [horizontální dělení dat Redis Cluster](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), a [klíče hash značky](https://redis.io/topics/cluster-spec#keys-hash-tags).

Ukázkový kód pro práci s clustering a nalezení klíče ve stejném horizontálním oddílu s klient StackExchange.Redis, najdete v článku [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) část [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) vzorku.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Co je největší velikost mezipaměti, kterou můžu vytvořit?
Maximální velikost mezipaměti úrovně premium je 53 GB. Můžete vytvořit až 10 horizontálními oddíly, získáte tak maximální velikost 530 GB. Pokud potřebujete větší velikost můžete [požádat o další](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Další informace najdete v tématu [mezipaměti Azure redis Cache ceny](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Všichni klienti Redis podporují clustering?
V současné době není veškerou podporu klientů Redis, clustering. StackExchange.Redis je ten, který podporuje pro něj. Další informace o jiných klientů najdete v článku [přehrávání s clusterem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) část [kurzu ke clusteru Redis](https://redis.io/topics/cluster-tutorial). 

Protokol clusteringu Redis vyžaduje každého klienta pro připojení k každý horizontální oddíl v režimu clusteru. Pokus o použití klienta, který nepodporuje clusteringu pravděpodobně bude mít v spoustu [PŘESUNUTÍ přesměrování výjimky](https://redis.io/topics/cluster-spec#moved-redirection).

> [!NOTE]
> Pokud používáte jako váš klient StackExchange.Redis, zkontrolujte, že používáte nejnovější verzi [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 nebo novější pro clustering fungovat správně. Pokud máte problémy s výjimkami přesunu, přečtěte si téma [přesun výjimek](#move-exceptions) Další informace.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Jak se připojit k mezipaměť při zapnuté funkci clustering?
Můžete se připojit k mezipaměti pomocí stejných [koncové body](cache-configure.md#properties), [porty](cache-configure.md#properties), a [klíče](cache-configure.md#access-keys) , který používáte při připojení k mezipaměti, ve kterém není povoleným clusteringem. Redis spravuje clustering pro back-endu, takže není nutné spravovat z vašeho klienta.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Můžu přímo se připojit do jednotlivých horizontálních oddílů Moje mezipaměti?
Clusteringu protokol vyžaduje, aby klient připojení správné horizontálních oddílů. Proto klienta by měl udělat správně za vás. To ale nutné dodat každý horizontální oddíl se skládá z dvojice primární/replika mezipaměti, souhrnně označované jako instanci mezipaměti. Můžete se připojit k tyto instance mezipaměti pomocí nástroje rozhraní příkazového řádku redis v [nestabilní](https://redis.io/download) větev úložiště Redis v Githubu. Implementuje základní podpory při spuštění s touto verzí `-c` přepnout. Další informace najdete v části [přehrávání s clusterem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) na [ https://redis.io ](https://redis.io) v [kurzu ke clusteru Redis](https://redis.io/topics/cluster-tutorial).

Bez ssl použijte následující příkazy.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Pro protokol ssl, nahraďte `1300N` s `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Můžete nakonfigurovat clusterování pro mezipaměť dříve vytvořeného?
Nyní lze povolit pouze clustering při vytvoření mezipaměti. Po vytvoření mezipaměti, ale nemůže přidat clustering cache ve verzi premium nebo odebrání clustering z mezipaměti úrovně premium po vytvoření mezipaměti můžete změnit velikost clusteru. Mezipaměti úrovně premium s aktivovaným clusteringem a pouze jeden horizontální oddíl se liší od cache ve verzi premium stejné velikosti s bez clusteringu.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Můžete nakonfigurovat clusterování pro mezipaměť basic nebo standard?
Clustering je k dispozici pouze pro úrovni Premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Můžete použít clustering s zprostředkovatele stavu relace ASP.NET Redis a ukládání výstupu do mezipaměti?
* **Poskytovatel výstupní mezipaměti redis** – bez nutnosti změn.
* **Zprostředkovatel stavu relace redis** – Pokud chcete použít clustering, je nutné použít [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 nebo vyšší nebo výjimka je vyvolána výjimka. Toto je zásadní změnu; Další informace najdete v části [v2.0.0 nejnovější podrobnosti o změnách](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Při použití StackExchange.Redis přesun výjimek dochází a clustering, co mám dělat?
Pokud používáte StackExchange.Redis a přijímat `MOVE` výjimek při použití clusteringu, ujistěte se, že používáte [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) nebo novější. Pokyny ke konfiguraci aplikací .NET pro používání StackExchange.Redis, naleznete v tématu [konfigurace klientů mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Další postup
Další informace o použití další prémiových funkcí mezipaměti.

* [Úvod do mezipaměti Azure Redis na úrovni Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







