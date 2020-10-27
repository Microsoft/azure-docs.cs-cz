---
title: Nejčastější dotazy ke správě Azure cache pro Redis
description: Přečtěte si odpovědi na nejčastější dotazy, které vám pomůžou spravovat Azure cache pro Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537656"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Nejčastější dotazy ke správě Azure cache pro Redis
Tento článek obsahuje odpovědi na běžné dotazy týkající se správy mezipaměti Azure pro Redis.

## <a name="common-questions-and-answers"></a>Časté otázky a odpovědi
Tato část obsahuje následující Nejčastější dotazy:

* [Kdy je vhodné povolit port bez TLS/SSL pro připojení k Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [Jaké jsou osvědčené postupy pro produkci?](#what-are-some-production-best-practices)
* [Jaké jsou některé důležité informace při použití běžných příkazů Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak mohu srovnávací testy a testovat výkon své mezipaměti?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Důležité podrobnosti o růstu fondu vláken](#important-details-about-threadpool-growth)
* [Povolit GC serveru pro získání větší propustnosti klienta při používání StackExchange. Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Požadavky na výkon kolem připojení](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Kdy je vhodné povolit port bez TLS/SSL pro připojení k Redis?
Redis Server neprovádí nativně podporu TLS, ale mezipaměť Azure pro Redis. Pokud se připojujete ke službě Azure cache pro Redis a váš klient podporuje protokol TLS, jako je StackExchange. Redis, pak byste měli použít protokol TLS.

>[!NOTE]
>Port bez protokolu TLS je ve výchozím nastavení pro novou mezipaměť Azure pro instance Redis zakázaný. Pokud váš klient nepodporuje protokol TLS, musíte povolit port bez TLS podle pokynů v části [přístupové porty](cache-configure.md#access-ports) v tématu [Konfigurace mezipaměti v mezipaměti Azure pro Redis](cache-configure.md) .
>
>

Nástroje Redis, jako `redis-cli` je například nefungují s portem TLS, ale můžete použít nástroj, jako je například `stunnel` k bezpečnému připojení nástrojů k portu TLS podle pokynů v tomto příspěvku na Blogový příspěvek [pro vydání služby ASP.NET pro Redis verze Preview](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/) .

Pokyny ke stažení nástrojů Redis naleznete v části How to [Run Redis Commands?](cache-development-faq.md#how-can-i-run-redis-commands)

### <a name="what-are-some-production-best-practices"></a>Jaké jsou osvědčené postupy pro produkci?
* [Osvědčené postupy pro StackExchange. Redis](#stackexchangeredis-best-practices)
* [Konfigurace a koncepty](#configuration-and-concepts)
* [Testování výkonu](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Osvědčené postupy pro StackExchange. Redis
* Nastavte `AbortConnect` na false, aby se ConnectionMultiplexer automaticky znovu připojil. [Podrobnosti najdete tady](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Znovu použijte ConnectionMultiplexer – nevytvářejte nové pro každý požadavek. `Lazy<ConnectionMultiplexer>`Je doporučený vzor, který je [zde zobrazen](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) .
* Redis funguje nejlépe s menšími hodnotami, takže zvažte možnost roztrhané větší data do více klíčů. V [této diskuzi Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)se 100 KB považuje za velký. V [tomto článku](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) najdete příklad problému, který může být způsoben velkými objemy dat.
* Nakonfigurujte [nastavení fondu vláken](#important-details-about-threadpool-growth) , aby nedocházelo k časovým limitům.
* Použijte minimálně výchozí connectTimeout o 5 sekund. Tento interval poskytuje StackExchange. Redis dostatek času k opětovnému navázání spojení v případě Blip sítě.
* Pamatujte na náklady na výkon spojené s různými operacemi, které používáte. Například `KEYS` příkaz je operace o (n), která by se měla vyhnout. [Web Redis.IO](https://redis.io/commands/) obsahuje podrobnosti o časové složitosti každé podporované operace. Pro zobrazení složitosti jednotlivých operací klikněte na jednotlivé příkazy.

#### <a name="configuration-and-concepts"></a>Konfigurace a koncepty
* Pro produkční systémy použijte úroveň Standard nebo Premium. Úroveň Basic odpovídá systému s jedním uzlem bez replikace dat a smlouvy SLA. Jako mezipaměť použijte aspoň C1. Mezipaměti C0 jsou obvykle používány pro jednoduché scénáře vývoje a testování.
* Pamatujte, že Redis je úložiště dat **v paměti** . Přečtěte si [Tento článek](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , abyste se dozvěděli o scénářích, kdy může dojít ke ztrátě dat.
* Vytvořte svůj systém tak, aby mohl zpracovávat připojení výkyvů [z důvodu oprav a převzetí služeb při selhání](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testování výkonu
* Začněte tím, že použijete `redis-benchmark.exe` k dosažení možné propustnosti před zápisem vlastních testů výkonu. Protože nepodporuje protokol `redis-benchmark` TLS, je nutné [Povolit port bez TLS prostřednictvím Azure Portal](cache-configure.md#access-ports) před spuštěním testu. Příklady najdete v tématu [Jak mohu srovnávací testy a testovat výkon moje mezipaměti?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Virtuální počítač klienta, který se používá pro testování, by měl být ve stejné oblasti jako instance Azure cache pro Redis.
* Pro vašeho klienta doporučujeme používat pro Dv2 řadu virtuálních počítačů, protože mají lepší hardware a měli by poskytovat nejlepší výsledky.
* Ujistěte se, že váš virtuální počítač klienta, který jste zvolili, má minimálně tolik možností výpočetního využití a šířky pásma jako mezipaměť, kterou testujete.
* Pokud pracujete v systému Windows, povolte VRSS na klientském počítači. [Podrobnosti najdete tady](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).
* Instance Redis úrovně Premium mají lepší latenci a propustnost sítě, protože jsou spuštěné na lepším hardwaru pro procesor i síť.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jaké jsou některé důležité informace při použití běžných příkazů Redis?

* Vyhněte se použití určitých příkazů Redis, jejichž dokončení trvá dlouhou dobu, pokud plně nerozumíte dopadu těchto příkazů. Nespouštějte například příkaz [Keys](https://redis.io/commands/keys) v produkčním prostředí. V závislosti na počtu klíčů může vrácení zpět trvat dlouhou dobu. Redis je jeden podproces serveru a zpracovává příkazy po jednom. Pokud máte další příkazy vydané po KLÍČích, nebudou zpracovány, dokud Redis nezpracuje příkaz KEYS. [Web Redis.IO](https://redis.io/commands/) obsahuje podrobnosti o časové složitosti každé podporované operace. Pro zobrazení složitosti jednotlivých operací klikněte na jednotlivé příkazy.
* Velikosti klíčů – mám použít malé hodnoty a hodnoty nebo velký klíč/hodnoty? Závisí na scénáři. Pokud váš scénář vyžaduje větší klíče, můžete upravit ConnectionTimeout a pak opakovat hodnoty a upravit logiku opakování. V perspektivě serveru Redis menší hodnoty poskytují lepší výkon.
* Tato doporučení neznamenají, že v Redis nemůžete ukládat větší hodnoty; je nutné vzít v úvahu následující skutečnosti. Latence budou vyšší. Pokud máte jednu sadu dat, která je větší a menší, můžete použít několik instancí ConnectionMultiplexer, z nichž každá je nakonfigurovaná s jinou sadou časových limitů a opakováním, jak je popsáno v předchozím oddílu věnovaném [možnostem konfigurace stackexchange. Redis](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) .

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak mohu srovnávací testy a testovat výkon své mezipaměti?
* [Povolte diagnostiku mezipaměti](cache-how-to-monitor.md#enable-cache-diagnostics), abyste mohli [monitorovat](cache-how-to-monitor.md) stav svojí mezipaměti. Metriky můžete zobrazit v Azure Portal a můžete je také [Stáhnout a revidovat](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) pomocí nástrojů podle vašeho výběru.
* Pro zátěžový test serveru Redis můžete použít redis-benchmark.exe.
* Ujistěte se, že klient zátěžového testování a mezipaměť Azure pro Redis jsou ve stejné oblasti.
* Použijte redis-cli.exe a monitorujte mezipaměť pomocí příkazu INFO.
* Pokud zatížení způsobuje velkou fragmentaci paměti, měli byste škálovat až na větší velikost mezipaměti.
* Pokyny ke stažení nástrojů Redis naleznete v části How to [Run Redis Commands?](cache-development-faq.md#how-can-i-run-redis-commands)

Následující příkazy poskytují příklad použití redis-benchmark.exe. Pro přesné výsledky spusťte tyto příkazy z virtuálního počítače ve stejné oblasti, ve které je vaše mezipaměť.

* Testování požadavků SET nastavených v kanálu pomocí datové části 1 tisíc

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testování požadavků GET v kanálu pomocí datové části 1 tisíc

>[!NOTE]
> Před naplněním mezipaměti spusťte výše uvedený test, aby se naplnila mezipaměť.
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>Důležité podrobnosti o růstu fondu vláken
Podprocesy CLR mají dva typy vláken – "pracovní proces" a "vstupně-výstupní" port pro dokončení (IOCP).

* Pracovní vlákna se používají pro věci, jako je zpracování `Task.Run(…)` nebo `ThreadPool.QueueUserWorkItem(…)` metody. Tato vlákna jsou používána také v různých komponentách CLR v případě, že je potřeba pracovat ve vlákně na pozadí.
* Vlákna IOCP se používají, když dojde k asynchronní vstupně-výstupní situaci, například při čtení ze sítě.

Fond vláken poskytuje nová pracovní vlákna nebo vstupně-výstupní vlákna na vyžádání (bez omezení), dokud nedosáhne minimálního nastavení pro každý typ vlákna. Ve výchozím nastavení je minimální počet vláken nastavený na počet procesorů v systému.

Jakmile počet stávajících (zaneprázdněných) vláken dosáhne minimálního počtu vláken, vystavení bude omezovat rychlost, s jakou vloží nová vlákna do jednoho vlákna na 500 milisekund. V případě, že váš systém získá nárůst práce, která vyžaduje vlákno IOCP, bude proces práce zpracovávat rychle. Pokud je však nárůst práce více než nakonfigurované "minimální" nastavení, dojde ke zpoždění při zpracovávání některé práce, protože podprocesní operace čekají na jednu ze dvou věcí, které mají být provedeny.

* Stávající vlákno bude pro zpracování práce zadarmo.
* Žádné existující vlákno se neuvolní po 500 ms, takže se vytvoří nové vlákno.

V podstatě to znamená, že pokud je počet zaneprázdněných vláken větší než minimální počet vláken, pravděpodobně platíte zpoždění 500 ms před tím, než aplikace zpracuje síťový provoz. Je důležité si uvědomit, že když existující vlákno zůstane nečinné déle než 15 sekund (na základě toho, co jsem si pamatuje), vyčištění se vyčistí a tento cyklus růstu a zmenšení se může opakovat.

Pokud se podíváme na ukázkovou chybovou zprávu z StackExchange. Redis (Build 1.0.450 nebo novější), uvidíte, že teď vytisknou statistiky fondu (podrobnosti najdete v podrobnostech o IOCP a PRACOVNÍKovi níže).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

V předchozím příkladu vidíte, že pro vlákno IOCP je šest zaneprázdněných vláken a systém je nakonfigurován tak, aby umožňoval čtyři minimální vlákna. V takovém případě by se u klienta pravděpodobně zobrazilo zpoždění 2 500 ms, protože 6 > 4.

Všimněte si, že StackExchange. Redis může mít vypršení časových limitů, pokud růst buď IOCP, nebo pracovní vlákna budou omezená.

#### <a name="recommendation"></a>Doporučení

S těmito informacemi doporučujeme, aby zákazníci nastavili minimální hodnotu konfigurace pro IOCP a pracovní vlákna na něco většího než výchozí hodnota. Nemůžeme nám dát k dispozici všechny pokyny, jak by tato hodnota měla být, protože správná hodnota pro jednu aplikaci bude pravděpodobně pro jinou aplikaci příliš vysoká nebo nízká. Toto nastavení může mít vliv i na výkon jiných částí složitých aplikací, takže každý zákazník musí toto nastavení vyladit podle svých konkrétních potřeb. Dobrým počátečním místem je 200 nebo 300 a pak podle potřeby proveďte testování a vylepšení.

Jak nakonfigurovat toto nastavení:

* Toto nastavení doporučujeme změnit programově pomocí metody [fondu vláken. SetMinThreads – (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) v `global.asax.cs` . Příklad:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > Hodnota zadaná touto metodou je globální nastavení, které ovlivňuje celou doménu AppDomain. Pokud máte například počítač se 4 jádry a chcete nastavit *MinWorkerThreads* a *MINIOTHREADS* 50 na procesor za běhu za běhu, použijte auto. **SetMinThreads – (200, 200)** .

* Je také možné zadat minimální nastavení vláken pomocí [nastavení konfigurace *MinIoThreads* nebo *MinWorkerThreads*](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100)) v rámci `<processModel>` konfiguračního prvku v `Machine.config` , obvykle se nachází v `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **Nastavení počtu minimálních vláken tímto způsobem se obecně nedoporučuje, protože se jedná o nastavení v rámci systému.**

  > [!NOTE]
  > Hodnota zadaná v tomto konfiguračním elementu je nastavení *pro jádro* . Pokud máte například počítač se 4 jádry a chcete, aby nastavení *minIoThreads* bylo za běhu 200, použijte `<processModel minIoThreads="50"/>` .
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Povolit GC serveru pro získání větší propustnosti klienta při používání StackExchange. Redis
Povolením GC serveru můžete optimalizovat klienta a zajistit lepší výkon a propustnost při používání StackExchange. Redis. Další informace o nástroji GC serveru a jeho povolení najdete v následujících článcích:

* [Povolení serveru GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Základy kolekce paměti](/dotnet/standard/garbage-collection/fundamentals)
* [Uvolňování paměti a výkon](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>Požadavky na výkon kolem připojení

Každá cenová úroveň má odlišná omezení pro připojení klientů, paměť a šířku pásma. I když každá velikost mezipaměti umožňuje *až* určitý počet připojení, každé připojení k Redis má spojenou režii. Příkladem takové režie by bylo využití CPU a paměti v důsledku šifrování TLS/SSL. Maximální limit připojení pro danou velikost mezipaměti předpokládá lehce načtenou mezipaměť. Pokud načtení z režie připojení *plus* zatížení z operací klienta překročí kapacitu systému, může mezipaměť zaznamenat problémy s kapacitou i v případě, že jste nepřekročili limit připojení pro aktuální velikost mezipaměti.

Další informace o různých omezeních připojení pro jednotlivé úrovně najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/). Další informace o připojeních a dalších výchozích konfiguracích najdete v tématu [výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration).

## <a name="next-steps"></a>Další kroky

Přečtěte si o dalších [nejčastějších dotazech k mezipaměti Azure pro Redis](cache-faq.md).