---
title: Konfigurace clusteringu Redis – premium Azure Cache pro Redis
description: Zjistěte, jak vytvořit a spravovat clustering Redis pro vaše instance Azure Cache úrovně Premium pro instance Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: 4a0e5b0c18264e1f7a98e81bcdfd56a7159235da
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010915"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Jak nakonfigurovat clustering Redis pro premium Azure Cache pro Redis
Azure Cache for Redis má různé nabídky mezipaměti, které poskytují flexibilitu při výběru velikosti mezipaměti a funkcí, včetně funkcí úrovně Premium, jako je clustering, trvalost a podpora virtuálních sítí. Tento článek popisuje, jak nakonfigurovat clustering v prémiové azure cache pro redis instance.

Informace o dalších funkcích mezipaměti premium najdete [v tématu Úvod do azure cache pro úroveň Redis Premium](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Co je redis cluster?
Azure Cache for Redis nabízí cluster Redis jako [implementovaný v Redisu](https://redis.io/topics/cluster-tutorial). S Redis Cluster získáte následující výhody: 

* Možnost automaticky rozdělit datovou sadu mezi více uzlů. 
* Možnost pokračovat v operacích v případě, že podmnožina uzlů dochází k chybám nebo není schopna komunikovat se zbytkem clusteru. 
* Větší propustnost: Propustnost se lineárně zvyšuje, když zvýšíte počet úlomků. 
* Větší velikost paměti: Zvyšuje lineárně, jak zvýšit počet úlomků.  

Clustering nezvyšuje počet připojení dostupných pro clusterovnou mezipaměť. Další informace o velikosti, propustnosti a šířce pásma s prémiovými mezipamětmi najdete v tématu [Co mám použít jakou a velikost Azure Cache for Redis?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

V Azure je cluster Redis nabízen jako primární model repliky, kde každý oddíl oddílu má primární a replikový pár s replikací, kde je replikace spravována službou Azure Cache for Redis. 

## <a name="clustering"></a>Clustering
Clustering je povolena na **nové mezipaměti Azure pro Redis** okno při vytváření mezipaměti. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Clustering je nakonfigurován v okně **clusteru Redis.**

![Clustering][redis-cache-clustering]

V clusteru můžete mít až 10 štřepů. Klepněte na **tlačítko Povoleno** a posuňte jezdec nebo zadejte číslo mezi 1 a 10 pro **počet úlomků** a klepněte na **tlačítko OK**.

Každý oddíl je dvojice mezipaměti primární nebo replika spravované Azure a celková velikost mezipaměti se vypočítá vynásobením počtu úlomků velikostí mezipaměti vybranou v cenové vrstvě. 

![Clustering][redis-cache-clustering-selected]

Po vytvoření mezipaměti se k ní připojíte a použijete ji stejně jako neclusterovaná mezipaměť a Redis distribuuje data v rámci střepů mezipaměti. Pokud je [povolena](cache-how-to-monitor.md#enable-cache-diagnostics)diagnostika , metriky jsou zachyceny samostatně pro každý úlomek a lze [zobrazit](cache-how-to-monitor.md) v okně Azure Cache for Redis. 

> [!NOTE]
> 
> Při konfiguraci clusteringu jsou v klientské aplikaci vyžadovány drobné rozdíly. Další informace naleznete v [tématu Je nutné provést nějaké změny v klientské aplikaci, aby bylo účelem použití clusteringu?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Ukázkový kód pro práci s clustering s klientem StackExchange.Redis, naleznete [v clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) část [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) vzorku.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Změna velikosti clusteru v běžící mezipaměti premium
Chcete-li změnit velikost clusteru v běžící mezipaměti premium s povoleným clusterováním, klepněte v **nabídce Prostředky**na **položku Velikost clusteru** .

![Velikost clusteru Redis][redis-cache-redis-cluster-size]

Chcete-li změnit velikost clusteru, použijte posuvník nebo zadejte číslo mezi 1 a 10 do textového pole **Počet úlomků** a klepnutím na **tlačítko OK** jej uložte.

Zvětšení velikosti clusteru zvyšuje maximální propustnost a velikost mezipaměti. Zvětšení velikosti clusteru nezvýší max. připojení dostupná klientům.

> [!NOTE]
> Změna velikosti clusteru spustí příkaz [MIGRATE,](https://redis.io/commands/migrate) což je nákladný příkaz, takže pro minimální dopad zvažte spuštění této operace v době mimo špičku. Během procesu migrace se zobrazí špička zatížení serveru. Škálování clusteru je dlouho běžící proces a doba, která je doba, závisí na počtu klíčů a velikosti hodnot přidružených k těmto klíčům.
> 
> 

## <a name="clustering-faq"></a>Nejčastější dotazy k clusteringu
Následující seznam obsahuje odpovědi na běžně kladené otázky týkající se Azure Cache pro redis clustering.

* [Musím provést nějaké změny v klientské aplikaci, abych mohl používat clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Jak jsou klíče distribuovány v clusteru?](#how-are-keys-distributed-in-a-cluster)
* [Jaká je největší velikost mezipaměti, kterou mohu vytvořit?](#what-is-the-largest-cache-size-i-can-create)
* [Podporují clustering všichni klienti Redis?](#do-all-redis-clients-support-clustering)
* [Jak se lze připojit ke své mezipaměti, když je povoleno clustering?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Mohu se přímo připojit k jednotlivým úlomkům mezipaměti?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Lze nakonfigurovat clustering pro dříve vytvořenou mezipaměť?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Lze nakonfigurovat clustering pro základní nebo standardní mezipaměť?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Lze použít clustering s zprostředkovateli redis ASP.NET stavu relace a výstupu ukládání do mezipaměti?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Dostávám výjimky MOVE při použití StackExchange.Redis a clustering, co mám dělat?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Musím provést nějaké změny v klientské aplikaci, abych mohl používat clustering?
* Je-li povoleno clustering, je k dispozici pouze databáze 0. Pokud klientská aplikace používá více databází a pokusí se číst nebo zapisovat do jiné databáze než 0, je vyvolána následující výjimka. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Další informace naleznete v tématu [Specifikace clusteru Redis – implementovaná podmnožina](https://redis.io/topics/cluster-spec#implemented-subset).
* Pokud používáte [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), musíte použít 1.0.481 nebo novější. Ke mezipaměti se připojujete pomocí [stejných koncových bodů, portů a klíčů,](cache-configure.md#properties) které používáte při připojování ke mezipaměti, která nemá povoleno clustering. Jediným rozdílem je, že všechny čtení a zápisy musí být provedeno do databáze 0.
  
  * Ostatní klienti mohou mít různé požadavky. Viz [Podpora clusterů všemi klienty Redis?](#do-all-redis-clients-support-clustering)
* Pokud vaše aplikace používá více operací klíčů dávkově do jednoho příkazu, všechny klíče musí být umístěny ve stejném oddílu. Chcete-li vyhledat klíče ve stejném úlomku, přečtěte si informace o [tom, jak jsou klíče distribuovány v clusteru?](#how-are-keys-distributed-in-a-cluster)
* Pokud používáte Redis ASP.NET poskytovatel stavu relace, musíte použít 2.0.1 nebo vyšší. Viz [Můžete použít clustering s redis ASP.NET stavu relace a výstupní ukládání do mezipaměti zprostředkovatelů?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Jak jsou klíče distribuovány v clusteru?
Podle dokumentace [distribučního modelu](https://redis.io/topics/cluster-spec#keys-distribution-model) Redis Keys: Místo klíče je rozděleno na 16384 slotů. Každý klíč je zakódován a přiřazen k jedné z těchto slotů, které jsou distribuovány mezi uzly clusteru. Můžete nakonfigurovat, která část klíče je zajistěte, aby bylo zajištěno, že více klíčů je umístěno ve stejném úlomku pomocí značek hash.

* Klíče se značkou hash - pokud je některá `{` `}`část klíče uzavřena v a , pouze tato část klíče je zahashována pro účely určení bloku hash klíče. Například následující 3 klíče by byly umístěny `{key}1`ve `{key}2`stejném `{key}3` šiřidlo: , a vzhledem k tomu, že je zahashována pouze `key` část názvu. Úplný seznam specifikací značek hash klíčů naleznete v tématu [Značky hash klíčů](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Klíče bez značky hash – celý název klíče se používá pro hašování. Výsledkem je statisticky rovnoměrné rozdělení mezi oddíly mezipaměti.

Pro dosažení nejlepšího výkonu a propustnost doporučujeme distribuovat klíče rovnoměrně. Pokud používáte klíče se značkou hash, je odpovědností aplikace zajistit rovnoměrné rozmístění klíčů.

Další informace naleznete v [tématech Model distribuce klíčů](https://redis.io/topics/cluster-spec#keys-distribution-model), [Rzoť clusteru rozdělení dat](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)a klíče [hash značky](https://redis.io/topics/cluster-spec#keys-hash-tags).

Ukázkový kód pro práci s clustering a lokalizace klíčů ve stejném oddílu s stackexchange.redis klienta, naleznete [v clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) část [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) vzorku.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Jaká je největší velikost mezipaměti, kterou mohu vytvořit?
Největší velikost prémiové mezipaměti je 120 GB. Můžete vytvořit až 10 úlomků, které vám poskytnou maximální velikost 1,2 TB GB. Pokud potřebujete větší velikost, můžete [požádat o další](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Další informace najdete v [tématu Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Podporují clustering všichni klienti Redis?
Ne všichni klienti podporují redis clustering! Zkontrolujte dokumentaci ke knihovně, kterou používáte, a ověřte, zda používáte knihovnu a verzi, která podporuje clustering. StackExchange.Redis je jedna knihovna, která podporuje clustering v novějších verzích. Další informace o ostatních klientech naleznete v části [Hraní s clusterem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [v kurzu clusteru Redis](https://redis.io/topics/cluster-tutorial). 

Protokol clustering Redis vyžaduje, aby se každý klient připojil ke každému úlomku přímo v režimu clusteringu a také definuje nové chybové odpovědi, jako je například "MOVED" na 'CROSSSLOTS'. Pokus o použití klienta, který nepodporuje clustering s mezipamětí režimu clusteru může mít za následek velké množství [výjimek přesměrování PŘESUNUTO](https://redis.io/topics/cluster-spec#moved-redirection), nebo jen přerušit aplikaci, pokud provádíte požadavky více kláves pro více kláves.

> [!NOTE]
> Pokud používáte StackExchange.Redis jako klient, ujistěte se, že používáte nejnovější verzi [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 nebo novější pro clustering pracovat správně. Pokud máte nějaké problémy s výjimkami přesunutí, přečtěte si další informace najdete v tématu [přesunout výjimky.](#move-exceptions)
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Jak se lze připojit ke své mezipaměti, když je povoleno clustering?
Ke mezipaměti se můžete připojit pomocí [stejných koncových bodů](cache-configure.md#properties), [portů](cache-configure.md#properties)a [klíčů,](cache-configure.md#access-keys) které používáte při připojování ke mezipaměti, která nemá povoleno clustering. Redis spravuje clustering na back-endu, takže není nutné spravovat z vašeho klienta.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Mohu se přímo připojit k jednotlivým úlomkům mezipaměti?
Clustering protocol vyžaduje, aby klient provést správné připojení šitrů. Takže klient by měl udělat správně za vás. S tím řekl, každý oddíl se skládá z dvojice mezipaměti primární/replika, souhrnně známý jako instance mezipaměti. K těmto instancím mezipaměti se můžete připojit pomocí nástroje redis-cli v [nestabilní](https://redis.io/download) větvi repozitáře Redis na GitHubu. Tato verze implementuje základní `-c` podporu při spuštění s přepínačem. Další informace naleznete v [tématu Přehrávání s clusterem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) v [https://redis.io](https://redis.io) [kurzu clusteru Redis](https://redis.io/topics/cluster-tutorial).

Pro jiné než TLS použijte následující příkazy.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

V případě TLS `1500N`nahraďte . `1300N`

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Lze nakonfigurovat clustering pro dříve vytvořenou mezipaměť?
Ano. Nejprve se ujistěte, že vaše mezipaměť je prémiová, škálováním, pokud není. Dále byste měli být schopni zobrazit možnosti konfigurace clusteru, včetně možnosti povolit cluster. Velikost clusteru můžete změnit po vytvoření mezipaměti nebo po prvním povolení clusteringu.

   >[!IMPORTANT]
   >Povolení clusteringu nelze vrátit. A mezipaměť s povoleným clusteringem a pouze jedním úlomkem se chová *jinak* než mezipaměť stejné velikosti *bez* clusteringu.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Lze nakonfigurovat clustering pro základní nebo standardní mezipaměť?
Clustering je k dispozici pouze pro prémiové mezipaměti.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Lze použít clustering s zprostředkovateli redis ASP.NET stavu relace a výstupu ukládání do mezipaměti?
* **Zprostředkovatel výstupní mezipaměti Redis** - nejsou vyžadovány žádné změny.
* **Zprostředkovatel stavu relace Redis** - chcete-li použít clustering, musíte použít [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 nebo vyšší nebo je vyvolána výjimka. Jedná se o zlomovou změnu; Další informace naleznete [v tématu podrobnosti o změně změny v 2.0.0](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Dostávám výjimky MOVE při použití StackExchange.Redis a clustering, co mám dělat?
Pokud používáte StackExchange.Redis `MOVE` a přijímat výjimky při použití clustering, ujistěte se, že používáte [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) nebo novější. Pokyny ke konfiguraci aplikací .NET pro použití souboru StackExchange.Redis naleznete [v tématu Konfigurace klientů mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak používat více funkcí prémiové mezipaměti.

* [Úvod do azure cache pro úroveň Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
