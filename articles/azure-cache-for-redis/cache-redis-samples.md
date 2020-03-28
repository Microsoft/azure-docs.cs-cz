---
title: Ukázky služby Azure Cache for Redis
description: 'Zjistěte, jak používat Azure Cache for Redis s těmito ukázkami kódu: připojení k mezipaměti, čtení a zápis dat v mezipaměti, ASP.NET Azure Cache pro poskytovatele Redis.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 8057a7db3d1a2db0e51ec15e4966ed0b31c84226
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433406"
---
# <a name="azure-cache-for-redis-samples"></a>Ukázky služby Azure Cache for Redis
Toto téma obsahuje seznam ukázek Azure Cache for Redis, které zahrnují scénáře, jako je připojení k mezipaměti, čtení a zápis dat do a z mezipaměti a použití ASP.NET Azure Cache pro poskytovatele Redis. Některé ukázky jsou projekty ke stažení a některé poskytují podrobné pokyny a obsahují fragmenty kódu, ale neodkazují na projekt ke stažení.

## <a name="hello-world-samples"></a>Dobrý den, svět vzorky
Ukázky v této části ukazují základy připojení k azure cache pro instanci Redis a čtení a zápis dat do mezipaměti pomocí různých jazyků a klientů Redis.

Ukázka [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) ukazuje, jak provádět různé operace mezipaměti pomocí klienta [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET.

Tato ukázka ukazuje, jak:

* Použití různých možností připojení
* Čtení a zápis objektů do mezipaměti a z mezipaměti pomocí synchronních a asynchronních operací
* Použití příkazů Redis MGET/MSET k vrácení hodnot zadaných klíčů
* Provádět transakční operace Redis
* Práce se seznamy Redis a seřazenými sadami
* Uložení objektů .NET pomocí serializátorů JsonConvert
* Použití sad Redis k implementaci tagování
* Práce s clusterem Redis

Další informace naleznete v dokumentaci [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) na GitHubu a další scénáře použití naleznete v testech částí [StackExchange.Redis.Tests.](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests)

[Jak používat Azure Cache pro Redis s Pythonem](cache-python-get-started.md) ukazuje, jak začít s Azure Cache pro Redis pomocí Pythonu a [redis-py klienta.](https://github.com/andymccurdy/redis-py)

[Práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) ukazuje jeden způsob serializace objektů .NET, takže je můžete zapsat a číst je z instance Azure Cache for Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Použití Azure Cache for Redis jako horizontálnínavýšení kapacity pro ASP.NET SignalR
[Použití azure cache pro redis jako horizontální navýšení kapacity backplane pro ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) ukázka ukazuje, jak můžete použít Azure Cache pro Redis jako signalr backplane. Další informace o backplane, naleznete [v tématu SignalR Scaleout with Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Ukázka dotazu zákazníka Azure Cache for Redis
Tato ukázka ukazuje porovnává výkon mezi přístupem k datům z mezipaměti a přístup k datům z úložiště trvalosti. Tato ukázka má dva projekty.

* [Ukázka toho, jak může Azure Cache for Redis zlepšit výkon ukládáním dat do mezipaměti](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Osivo databáze a cache pro ukázku](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET stavu relace a ukládání výstupu do mezipaměti
[Ukázka Použití mezipaměti Azure pro redis k uložení ASP.NET SessionState a OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) ukázka ukazuje, jak použít Azure Cache pro Redis k ukládání ASP.NET session a výstupní mezipaměti pomocí SessionState a OutputCache zprostředkovatelů pro Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Správa azure mezipaměti pro redis pomocí MAML
Ukázka [Správy mezipaměti Azure pro Redis pomocí knihoven Azure ukazuje,](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) jak můžete pomocí knihoven Azure Management spravovat – (Vytvořit/ Aktualizovat/odstranit) vaši mezipaměť. 

## <a name="custom-monitoring-sample"></a>Vlastní ukázka monitorování
Ukázka [dat access azure cache for Redis Monitoring](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) ukazuje, jak můžete získat přístup k datům monitorování pro vaši Azure Cache for Redis mimo Azure Portal.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Twitter-styl klon napsaný pomocí PHP a Redis
Vzorek [Retwis](https://github.com/SyntaxC4-MSFT/retwis) je Redis Hello World. Jedná se o minimální Twitter-styl sociální sítě klon napsaný pomocí Redis a PHP pomocí [predis klienta.](https://github.com/nrk/predis) Zdrojový kód je navržen tak, aby byl velmi jednoduchý a zároveň zobrazoval různé datové struktury Redis.

## <a name="bandwidth-monitor"></a>Monitor šířky pásma
Ukázka [sledování šířky pásma](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) umožňuje sledovat šířku pásma používanou v klientovi. Chcete-li měřit šířku pásma, spusťte ukázku v klientském počítači mezipaměti, uskutečujte do mezipaměti volání a sledujte šířku pásma hlášenou ukázkou sledování šířky pásma.
