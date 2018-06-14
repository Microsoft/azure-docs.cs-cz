---
title: Ukázek Azure Redis Cache | Microsoft Docs
description: Další informace o použití Azure Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: 0b6f89807d3252b750bd5208a7f758a06c9903d6
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27909692"
---
# <a name="azure-redis-cache-samples"></a>Ukázek Azure Redis Cache
Toto téma obsahuje seznam ukázek Azure Redis Cache, pokrývajících scénáře, jako je připojení k mezipaměti, čtení a zápisu dat do a z mezipaměti a pomocí poskytovatelů ASP.NET Redis Cache. Některé z ukázky ke stažení projekty jsou a některé poskytují podrobné pokyny a součástí jsou fragmenty kódu ale nejsou připojeny k dispozici ke stažení projektu.

## <a name="hello-world-samples"></a>Hello world ukázky
V této části Ukázky klientů Redis a zobrazit základní informace o připojení k instanci služby Azure Redis Cache a čtení a zapisování dat do mezipaměti pomocí různých jazycích.

[Hello, world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) příklad ukazuje, jak provádět různé operace mezipaměti pomocí [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) klient .NET.

Tento příklad ukazuje postup:

* Používat různé možnosti připojení
* Číst a zapisovat objekty do a z mezipaměti pomocí synchronní a asynchronní operace
* Návratové hodnoty zadaného klíče pomocí příkazů Redis MGET/MSET
* Provádění operací transakční Redis
* Práce s Redis seznamy a seřazené sady
* Ukládání objektů .NET pomocí JsonConvert serializátorů
* Pomocí sad Redis k implementaci označování
* Práce s clusteru Redis

Další informace najdete v tématu [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) najdete v dokumentaci na githubu a další scénáře použití [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) testování částí.

[Postup použití Azure Redis Cache s Pythonem](cache-python-get-started.md) ukazuje, jak začít pracovat s Azure Redis Cache pomocí Pythonu a [redis-py](https://github.com/andymccurdy/redis-py) klienta.

[Práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) ukazuje jeden způsob, jak serializovat objekty .NET, abyste mohli zapíše je a přečtěte si je z instance Azure Redis Cache. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Použití mezipaměti Redis jako propojovací rozhraní škálování pro ASP.NET SignalR
[Použijte Redis Cache jako horizontální navýšení kapacity propojovacího rozhraní pro ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) příklad ukazuje, jak můžete použít Azure Redis Cache jako propojovací rozhraní systému SignalR. Další informace o propojovací rozhraní systému najdete v tématu [škálování SignalR s Redisem](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Ukázkový dotaz zákazníka mezipaměti redis
Tento příklad znázorňuje výkon porovnává mezi přístup k datům z mezipaměti a přístup k datům z úložiště trvalosti. Tato ukázka má dva projekty.

* [Ukázka, jak lze Redis Cache vylepšit výkon ukládaní dat do mezipaměti](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Počáteční hodnoty databáze a mezipaměti pro ukázku](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Stavu relace ASP.NET a ukládání výstupu do mezipaměti
[Pomocí Azure Redis Cache k ukládání ASP.NET SessionState a OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) příklad ukazuje způsob použití Azure Redis Cache k ukládání relace ASP.NET a výstupní mezipaměti pomocí zprostředkovatele SessionState a OutputCache pro Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Spravovat MAML mezipaměť Redis systému Azure
[Spravovat Azure Redis Cache pomocí knihovny správy Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) ukázka znázorňuje, jak můžete pomocí správy knihovny Azure ke správě - (Vytvořit / aktualizovat / delete) vaší mezipaměti. 

## <a name="custom-monitoring-sample"></a>Vlastní monitorování ukázka
[Data přístup Redis Cache monitorování](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) příklad ukazuje, jak můžete přistupovat k datům monitorování pro mezipaměť Azure Redis mimo portál Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Klon stylu služby Twitter vytvořené pomocí PHP a Redis
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) ukázka je Redis Hello World. Je minimální klon stylu služby Twitter sociální sítě vytvořené pomocí Redis a PHP, pomocí [Predis](https://github.com/nrk/predis) klienta. Zdrojový kód je určen velmi jednoduché a současně zobrazit různé Redis datové struktury.

## <a name="bandwidth-monitor"></a>Monitorování šířky pásma
[Šířky pásma monitorování](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) ukázka umožňuje sledovat šířku pásma používá na klientovi. K měření šířky pásma, spuštění vzorového v klientském počítači mezipaměti, provádět volání do mezipaměti a sledovat šířku pásma hlášené ukázka monitorování šířky pásma.

