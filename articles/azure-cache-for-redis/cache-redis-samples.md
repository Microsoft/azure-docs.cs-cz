---
title: Mezipaměť Redis ukázek Azure | Dokumentace Microsoftu
description: Další informace o použití služby Azure Cache for Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: 8d31e8909a6c06fc324fef95907f0617a6723236
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019361"
---
# <a name="azure-cache-for-redis-samples"></a>Mezipaměť Redis ukázek Azure
Toto téma obsahuje seznam mezipaměti Azure Redis ukázek, pokrývající scénářů, jako je připojení k mezipaměti, čtení a zápis dat do a z mezipaměti a pomocí ukládání do mezipaměti Azure technologie ASP.NET pro poskytovatele Redis. Některé z ukázek jsou ke stažení projekty a některé poskytují podrobné pokyny a zahrnovat fragmenty kódu, ale není odkaz na projekt ke stažení.

## <a name="hello-world-samples"></a>Ukázky Hello world
Příklady v tomto oddíle ukazují základní informace o připojení k Azure pro instanci Redis Cache a čtení a zápis dat do mezipaměti pomocí různých jazycích a klienti Redis.

[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) příklad ukazuje, jak provádět různé operace mezipaměti pomocí [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET klienta.

Tato ukázka předvádí, jak:

* Používat různé možnosti připojení
* Čtení a zápis objektů do a z mezipaměti pomocí synchronní a asynchronní operace
* Příkazy Redis MGET a MSET vracet hodnotu zadaného klíče
* Provedení transakčního operací Redis
* Práce s využitím Redisu seznamy a seřazené sady
* Store pomocí JsonConvert serializátory objektů .NET
* Implementace označování pomocí sad Redis
* Práce s clusterem Redis

Další informace najdete v tématu [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) najdete v dokumentaci na githubu a další scénáře využití [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) testování částí.

[Použití mezipaměti Azure pro Redis s Pythonem](cache-python-get-started.md) ukazuje, jak začít pracovat s mezipamětí Azure pro Redis pomocí Pythonu a [redis-py](https://github.com/andymccurdy/redis-py) klienta.

[Práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) ukazuje jeden způsob, jak k serializaci objektů .NET vám umožní jejich k zápisu a čtení z Azure pro instanci Redis Cache. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Použití Azure Cache pro Redis jako propojovací rozhraní škálování pro funkci SignalR technologie ASP.NET
[Použití mezipaměti Azure Redis jako propojovací rozhraní škálování pro funkce SignalR technologie ASP.NET](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) ukázka demonstruje, jak používat Azure Cache pro Redis jako propojovací rozhraní systému SignalR. Další informace o propojovací rozhraní systému najdete v tématu [škálování aplikace SignalR službou Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Mezipaměť Azure Redis zákazníka dotazu ukázku
Tato ukázka předvádí, porovná výkon mezi přístup k datům z mezipaměti a přístup k datům z trvalého úložiště. Tato ukázka má dva projekty.

* [Ukázka, jak lze mezipaměti Azure Redis vylepšit výkon ukládání dat do mezipaměti](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Počáteční hodnota databáze a mezipaměti pro tuto ukázku](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Stavu relace ASP.NET a ukládání výstupu do mezipaměti
[Použití mezipaměti Azure Redis k uložení SessionState technologie ASP.NET a OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) Ukázka předvádí, jak můžete používat k ukládání relaci ASP.NET a výstupní mezipaměti pro Redis pomocí zprostředkovatele SessionState a OutputCache mezipaměti Azure Redis .

## <a name="manage-azure-cache-for-redis-with-maml"></a>Správa mezipaměti Azure pro Redis s MAML
[Správa mezipaměti Azure Redis pomocí knihoven správy Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) ukázka demonstruje, jak můžete pomocí knihovny správy Azure spravovat - (Vytvořit / aktualizovat / odstranit) vaší mezipaměti. 

## <a name="custom-monitoring-sample"></a>Vlastní sledování vzorku
[Přístup k mezipaměti Azure Redis monitorování dat](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) ukázka demonstruje, jak lze použít monitorování dat pro Azure Cache pro Redis mimo web Azure Portal.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Klon Twitteru – vizuální styl napsané s využitím PHP a Redis
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) ukázka je Redis Hello World. Je minimální klon Twitteru – vizuální styl sociálních sítí napsané s využitím Redisu a PHP pomocí [Predis](https://github.com/nrk/predis) klienta. Zdrojový kód je určen velmi jednoduché a současně k zobrazení různých Redis datové struktury.

## <a name="bandwidth-monitor"></a>Monitorování šířky pásma
[Šířky pásma monitorování](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) ukázky vám umožní monitorovat šířku pásma použít na straně klienta. K měření šířky pásma, spusťte ukázku v mezipaměti klientského počítače, provádět volání do mezipaměti a sledujte šířky pásma nahlášený ukázky monitorování šířky pásma.

