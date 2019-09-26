---
title: Jak nakonfigurovat cluster Redis pro mezipaměť Azure úrovně Premium pro Redis | Microsoft Docs
description: Naučte se vytvářet a spravovat clustery Redis pro mezipaměť Azure úrovně Premium pro instance Redis.
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
ms.openlocfilehash: a919ccd2a23acf6e1bd04cda8a5dd18782ff31b0
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315980"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Postup konfigurace clusteringu Redis pro mezipaměť Azure úrovně Premium pro Redis
Azure cache pro Redis má různé nabídky mezipaměti, které poskytují flexibilitu v výběru velikosti a funkcí mezipaměti, včetně funkcí úrovně Premium, jako je podpora clusteringu, trvalosti a virtuální sítě. Tento článek popisuje, jak nakonfigurovat clustering v mezipaměti Azure Premium pro instanci Redis.

Informace o dalších funkcích mezipaměti Premium najdete v tématu [Úvod do mezipaměti Azure pro Redis úrovně Premium](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Co je cluster Redis?
Azure cache for Redis nabízí cluster Redis, jak je [implementován v Redis](https://redis.io/topics/cluster-tutorial). S clusterem Redis získáte následující výhody: 

* Schopnost automaticky rozdělit datovou sadu mezi více uzlů. 
* Možnost pokračovat v operacích, když dojde k selhání podmnožiny uzlů nebo nelze komunikovat se zbytkem clusteru. 
* Další propustnost: Propustnost se při zvýšení počtu horizontálních oddílů zvyšuje lineárně. 
* Větší velikost paměti: Se při zvýšení počtu horizontálních oddílů zvyšuje lineárně.  

Clustering nezvyšuje počet připojení dostupných pro clusterovou mezipaměť. Další informace o velikosti, propustnosti a šířce pásma pomocí prémiových mezipamětí najdete v tématu [co je to Azure cache pro nabídku Redis a velikost mám použít?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

V Azure se cluster Redis nabízí jako model primární/repliky, kde každý horizontálních oddílů má dvojici primárního/repliky s replikací, kde je replikace spravovaná službou Azure cache pro službu Redis. 

## <a name="clustering"></a>Clustering
Clustering je povolený v **nové službě Azure cache pro Redis** během vytváření mezipaměti. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Clustering se konfiguruje v okně **clusteru Redis** .

![Clustering][redis-cache-clustering]

V clusteru můžete mít až 10 horizontálních oddílů. Klikněte na **povoleno** a posunutí posuvníku nebo zadejte číslo od 1 do 10 pro **horizontálních oddílů Count** a klikněte na **OK**.

Každý horizontálních oddílů je pár mezipaměti primárního/repliky, který spravuje Azure, a celková velikost mezipaměti se počítá vynásobením počtu horizontálních oddílů velikostí mezipaměti vybranou v cenové úrovni. 

![Clustering][redis-cache-clustering-selected]

Po vytvoření mezipaměti se k ní připojíte a použijete ji stejně jako mezipaměť bez clusterů a Redis distribuuje data v celé mezipaměti horizontálních oddílů. Pokud je [povolená](cache-how-to-monitor.md#enable-cache-diagnostics)diagnostika, jsou metriky zachyceny samostatně pro každý horizontálních oddílů a lze je [Zobrazit](cache-how-to-monitor.md) v okně Azure cache pro Redis. 

> [!NOTE]
> 
> Při konfiguraci clusteringu se v klientské aplikaci vyžadují některé menší rozdíly. Další informace najdete v tématu musím dělat [změny v klientské aplikaci, aby používaly clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Vzorový kód při práci s Clustering s klientem StackExchange. Redis najdete v části [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) v ukázce [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Změna velikosti clusteru v běžící mezipaměti Premium
Pokud chcete změnit velikost clusteru běžící mezipaměti Premium s povoleným clusteringem, klikněte na **Velikost clusteru Redis** v **nabídce prostředky**.

> [!NOTE]
> I když je Azure cache pro Redis úrovně Premium vydaný pro obecnou dostupnost, funkce velikosti clusterů Redis je momentálně ve verzi Preview.
> 
> 

![Velikost clusteru Redis][redis-cache-redis-cluster-size]

Chcete-li změnit velikost clusteru, použijte posuvník nebo zadejte číslo v rozmezí 1 až 10 v textovém poli **horizontálních oddílů Count** a kliknutím na tlačítko **OK** uložte.

Zvýšení velikosti clusteru zvyšuje maximální propustnost a velikost mezipaměti. Zvýšení velikosti clusteru nezvyšuje maximální počet. připojení dostupná pro klienty.

> [!NOTE]
> Při škálování clusteru se spustí příkaz [migrace](https://redis.io/commands/migrate) , což je nákladný příkaz, takže pro minimální dopad zvažte spuštění této operace v době mimo špičku. Během procesu migrace se zobrazí špička zatížení serveru. Škálování clusteru je dlouhotrvající proces a doba trvání závisí na počtu klíčů a velikosti hodnot přidružených k těmto klíčům.
> 
> 

## <a name="clustering-faq"></a>Nejčastější dotazy týkající se clusteringu
Následující seznam obsahuje odpovědi na nejčastější dotazy týkající se clusteringu Azure cache pro Redis.

* [Musím v klientské aplikaci dělat nějaké změny, aby používaly clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Jak jsou klíče distribuované v clusteru?](#how-are-keys-distributed-in-a-cluster)
* [Jaká je největší velikost mezipaměti, kterou můžu vytvořit?](#what-is-the-largest-cache-size-i-can-create)
* [Podporují clustering všichni klienti Redis?](#do-all-redis-clients-support-clustering)
* [Návody se připojit k mezipaměti, když je clustering povolený?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Můžu se přímo připojit k jednotlivým horizontálních oddílů své mezipaměti?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Můžu nakonfigurovat clustering pro dříve vytvořenou mezipaměť?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Můžu nakonfigurovat clustering pro mezipaměť Basic nebo Standard?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Můžu používat clusteringu se stavem relace Redis ASP.NET a poskytovateli ukládání výstupu do mezipaměti?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Jak mám při používání StackExchange. Redis a clusteringu dělat výjimky, co mám dělat?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Musím v klientské aplikaci dělat nějaké změny, aby používaly clustering?
* Když je clustering povolený, k dispozici je jenom databáze 0. Pokud klientská aplikace používá více databází a pokusí se číst nebo zapisovat do jiné databáze než 0, je vyvolána následující výjimka. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->``StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Další informace najdete v části [Redis cluster Specification – implementovaná](https://redis.io/topics/cluster-spec#implemented-subset)podmnožina.
* Pokud používáte [stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/), musíte použít 1.0.481 nebo novější. K mezipaměti se připojíte pomocí stejných [koncových bodů, portů a klíčů](cache-configure.md#properties) , které použijete při připojování k mezipaměti, u které není povolený clusteringu. Jediným rozdílem je, že všechny operace čtení a zápisu musí být provedeny do databáze 0.
  
  * Ostatní klienti mohou mít různé požadavky. Viz téma [podpora clusteringu u všech klientů Redis?](#do-all-redis-clients-support-clustering)
* Pokud vaše aplikace používá více klíčových operací dávkování do jednoho příkazu, všechny klíče musí být umístěny ve stejném horizontálních oddílů. Pokud chcete najít klíče ve stejném horizontálních oddílů, přečtěte si téma [jak jsou klíče distribuované v clusteru?](#how-are-keys-distributed-in-a-cluster)
* Pokud používáte poskytovatele stavu relace ASP.NET Redis, musíte použít 2.0.1 nebo vyšší. Viz téma [můžu použít clusteringu se stavem relace Redis ASP.NET a poskytovateli ukládání výstupu do mezipaměti?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Jak jsou klíče distribuované v clusteru?
Dokumentace k [modelu distribuce](https://redis.io/topics/cluster-spec#keys-distribution-model) pro Redis klíče: Klíčové místo je rozdělené do 16384 slotů. Každý klíč se vyhodnotí jako hash a přiřadí se k jednomu z těchto slotů, které se distribuují napříč uzly clusteru. Můžete nakonfigurovat, která část klíče má hodnotu hash, aby bylo zajištěno, že se ve stejném horizontálních oddílů pomocí značek hash nachází více klíčů.

* Klíče se značkami hash – Pokud je libovolná část klíče uzavřená v `{` a `}`, pro účely určení slotu hodnoty hash klíče se hodnota hash vyhodnotí jenom pro tuto část klíče. Například následující 3 klíče by se nacházely ve stejném horizontálních oddílů `{key}1`:, `{key}2`a `{key}3` vzhledem k tomu, že pouze `key` část názvu je nastavena na hodnotu hash. Úplný seznam klíčových specifikací hash klíčů najdete v tématu [klíče hash klíčů](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Klíče bez značky hash – pro použití algoritmu hash se používá celý název klíče. Výsledkem je statistická i distribuce napříč horizontálních oddílů mezipaměti.

Pro dosažení nejlepšího výkonu a propustnosti doporučujeme, aby byly klíče rovnoměrně distribuovány. Pokud používáte klíče s tagem hash, jedná se o zodpovědnost aplikace za účelem zajištění rovnoměrné distribuce klíčů.

Další informace najdete v tématech [model distribuce klíčů](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis data horizontálního dělení clusteru](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)a [klíče hash klíčů](https://redis.io/topics/cluster-spec#keys-hash-tags).

Vzorový kód pro práci s Clustering a hledání klíčů ve stejném horizontálních oddílů s klientem StackExchange. Redis najdete v části [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) ukázky [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Jaká je největší velikost mezipaměti, kterou můžu vytvořit?
Největší velikost mezipaměti Premium je 120 GB. Můžete vytvořit až 10 horizontálních oddílů a poskytnout tak maximální velikost 1,2 TB GB. Pokud potřebujete větší velikost, můžete [požádat o další](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Další informace najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Podporují clustering všichni klienti Redis?
V současné době ne všichni klienti podporují clusterování Redis. StackExchange. Redis je ten, který pro něj podporuje. Další informace o dalších klientech naleznete v části [přehrávání v clusteru](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) v [kurzu cluster Redis](https://redis.io/topics/cluster-tutorial). 

Protokol clusteringu Redis vyžaduje, aby se každý klient připojoval ke každému horizontálních oddílů přímo v režimu clusteringu. Při pokusu o použití klienta, který nepodporuje clusteringu, bude nejspíš vyplynout z většího počtu [přesunutých výjimek přesměrování](https://redis.io/topics/cluster-spec#moved-redirection).

> [!NOTE]
> Pokud jako klienta používáte StackExchange. Redis, ujistěte se, že pro správné fungování clusteringu používáte nejnovější verzi [stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 nebo novější. Pokud máte nějaké problémy s výjimkami přesunutí, přečtěte si téma [přesunutí výjimek](#move-exceptions) , kde najdete další informace.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Návody se připojit k mezipaměti, když je clustering povolený?
Ke své mezipaměti se můžete připojit pomocí stejných [koncových bodů](cache-configure.md#properties), [portů](cache-configure.md#properties)a [klíčů](cache-configure.md#access-keys) , které používáte při připojování k mezipaměti, u které není povolený clusteringu. Redis spravuje clustering v back-endu, takže je nemusíte spravovat od svého klienta.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Můžu se přímo připojit k jednotlivým horizontálních oddílů své mezipaměti?
Protokol clusteringu vyžaduje, aby klient provedl správná připojení horizontálních oddílů. Proto by to klient měl provést správně. V takovém případě se každý horizontálních oddílů skládá z dvojice mezipaměti primárního/repliky, která je souhrnně známá jako instance mezipaměti. K těmto instancím mezipaměti se můžete připojit pomocí nástroje Redis-CLI v [nestabilní](https://redis.io/download) větvi úložiště Redis na GitHubu. Tato verze implementuje základní podporu při spuštění s `-c` přepínačem. Další informace najdete v tématu věnovaném [přehrávání clusteru](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [https://redis.io](https://redis.io) v v [kurzu cluster Redis](https://redis.io/topics/cluster-tutorial).

Pro jiný protokol než SSL použijte následující příkazy.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

V případě protokolu SSL `1300N` Nahraďte parametr `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Můžu nakonfigurovat clustering pro dříve vytvořenou mezipaměť?
V současné době můžete povolit clusteringu pouze při vytváření mezipaměti. Velikost clusteru můžete změnit po vytvoření mezipaměti, ale nemůžete přidat clustering do mezipaměti Premium nebo odebrat Clustering z mezipaměti Premium po vytvoření mezipaměti. Mezipaměť Premium s povoleným clusteringem a jenom jedna horizontálních oddílů se liší od mezipaměti Premium stejné velikosti bez clusteringu.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Můžu nakonfigurovat clustering pro mezipaměť Basic nebo Standard?
Clustering je k dispozici jenom pro mezipaměti úrovně Premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Můžu používat clusteringu se stavem relace Redis ASP.NET a poskytovateli ukládání výstupu do mezipaměti?
* **Poskytovatel výstupní mezipaměti Redis** – nevyžadují se žádné změny.
* **Zprostředkovatel stavu relace Redis** – Chcete-li použít clusteringu, je nutné použít [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 nebo vyšší nebo je vyvolána výjimka. Toto je zásadní změna. Další informace najdete [v části 2.0.0 – Podrobnosti o zásadní změně](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Jak mám při používání StackExchange. Redis a clusteringu dělat výjimky, co mám dělat?
Pokud používáte stackexchange. Redis a při použití clusteringu `MOVE` přijímají výjimky, ujistěte se, že používáte [stackexchange. Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) nebo novější. Pokyny ke konfiguraci aplikací .NET pro použití StackExchange. Redis najdete v tématu [Konfigurace klientů mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Další kroky
Naučte se používat víc funkcí mezipaměti Premium.

* [Seznámení s mezipamětí Azure pro Redis úrovně Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







