---
title: Ukázky služby Azure Cache for Redis
description: 'Naučte se používat Azure cache pro Redis s těmito ukázkami kódu: připojení k mezipaměti, čtení a zápis dat do mezipaměti, ASP.NET mezipaměti Azure pro poskytovatele Redis.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 553850173f463a05b13768eb3b9e17703bfa2886
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212299"
---
# <a name="azure-cache-for-redis-samples"></a>Ukázky služby Azure Cache for Redis
Toto téma poskytuje seznam mezipaměti Azure pro ukázky Redis, které se týkají scénářů, jako je připojení k mezipaměti, čtení a zápis dat do a z mezipaměti, a použití ASP.NET mezipaměti Azure pro poskytovatele Redis. Některé ukázky jsou projekty ke stažení a některé poskytují podrobné pokyny a obsahují fragmenty kódu, ale neodkazování na projekt ke stažení.

## <a name="hello-world-samples"></a>Ukázky Hello World
Ukázky v této části ukazují základy připojení ke službě Azure cache pro instanci Redis a čtení a zápis dat do mezipaměti pomocí různých jazyků a klientů Redis.

Ukázka [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) ukazuje, jak provádět různé operace s mezipamětí pomocí klienta .NET [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) .

V této ukázce se dozvíte, jak:

* Použít různé možnosti připojení
* Čtení a zápis objektů do a z mezipaměti pomocí synchronních a asynchronních operací
* Pro návrat hodnot zadaných klíčů použít příkazy Redis MGET/MSET
* Provádění transakčních operací Redis
* Práce se seznamy Redis a seřazenými sadami
* Ukládání objektů .NET pomocí serializátorů JsonConvert
* Použití Redis sad k implementaci tagování
* Práce s clusterem Redis

Další informace najdete v dokumentaci k [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) na GitHubu a pro další scénáře použití najdete v testech jednotky [stackexchange. Redis. Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) .

[Jak používat Azure cache pro Redis s Pythonem](cache-python-get-started.md) ukazuje, jak začít s Azure cache pro Redis pomocí Pythonu a klienta [Redis-py](https://github.com/andymccurdy/redis-py) .

[Práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) ukazuje jeden ze způsobů serializace objektů .NET, abyste je mohli napsat a číst z mezipaměti Azure pro instanci Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Použití mezipaměti Azure pro Redis jako plán pro horizontální navýšení kapacity pro ASP.NET signál
Možnost [použít mezipaměť Azure pro Redis jako plán pro horizontální navýšení kapacity pro ASP.NET signalizaci](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) ukazuje, jak můžete použít mezipaměť Azure pro Redis jako plán pro vyzkoušení signálu. Další informace o replánování najdete v tématu [škálování signálu pomocí Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Ukázka dotazů zákazníků Azure cache pro Redis
Tato ukázka ukazuje, jak porovnává výkon při přístupu k datům z mezipaměti a přístup k datům z úložiště trvalosti. Tato ukázka má dva projekty.

* [Ukázka způsobu, jakým Azure mezipaměť pro Redis může zlepšit výkon ukládáním dat do mezipaměti](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Dosazení databáze a mezipaměti pro ukázku](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Stav relace ASP.NET a ukládání výstupu do mezipaměti
Ukázka [použití mezipaměti Azure pro Redis k ukládání ASP.NET sessionState a OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) demonstruje, jak můžete pomocí Azure cache pro Redis ukládat relaci ASP.NET a výstupní mezipaměť pomocí zprostředkovatelů sessionState a OutputCache pro Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Správa Azure cache pro Redis pomocí MAML
Ukázka [Správa Azure cache pro Redis pomocí knihoven správy Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) ukazuje, jak můžete pomocí knihoven správy Azure spravovat – (vytvořit, aktualizovat nebo odstranit) mezipaměť. 

## <a name="custom-monitoring-sample"></a>Ukázka vlastního monitorování
Ukázka [přístup k Azure cache pro Redis monitoring data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) ukazuje, jak můžete získat přístup k datům monitorování mezipaměti Azure pro Redis mimo portál Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Klon ve stylu Twitteru napsaný pomocí PHP a Redis
Ukázka [ReTWis](https://github.com/SyntaxC4-MSFT/retwis) je Redis Hello World. Jedná se o minimální klon ze sociální sítě ve stylu Twitteru napsaný pomocí Redis a PHP pomocí klienta [Predis](https://github.com/nrk/predis) . Zdrojový kód je navržený tak, aby byl velmi jednoduchý a současně pro zobrazení různých Redis datových struktur.

## <a name="bandwidth-monitor"></a>Monitorování šířky pásma
Ukázka [monitorování šířky pásma](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) umožňuje monitorovat šířku pásma použitou v klientovi. Chcete-li změřit šířku pásma, spusťte ukázku v klientském počítači mezipaměti, proveďte volání do mezipaměti a sledujte šířku pásma nahlášenou ukázkou monitorování šířky pásma.
