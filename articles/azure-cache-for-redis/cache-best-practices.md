---
title: Osvědčené postupy pro Azure Cache for Redis
description: Zjistěte, jak efektivně používat azure cache pro Redis pomocí těchto doporučených postupů.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 105a3996753a1d1c2d71846cc8bad574e4498acf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478604"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Osvědčené postupy pro Azure Cache for Redis 
Dodržováním těchto doporučených postupů můžete maximalizovat výkon a nákladově efektivní využití instance Azure Cache for Redis.

## <a name="configuration-and-concepts"></a>Konfigurace a koncepty
 * **Pro produkční systémy použijte úroveň Standard nebo Premium.**  Úroveň Basic je systém jednoho uzlu bez replikace dat a bez sla. Jako mezipaměť použijte aspoň C1.  C0 mezipaměti jsou určeny pro jednoduché scénáře dev/test, protože mají sdílené jádro procesoru, málo paměti a jsou náchylné k problémům s "hlučným sousedem".

 * **Nezapomeňte, že Redis je úložiště dat v paměti.**  [Tento článek](cache-troubleshoot-data-loss.md) popisuje některé scénáře, kde může dojít ke ztrátě dat.

 * **Vyvíjejte systém tak, aby zvládal chyby připojení** [z důvodu opravy a převzetí služeb při selhání](cache-failover.md).

 * **Nakonfigurujte [nastavení vyhrazené pro maximální paměť,](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) abyste zlepšili odezvu systému** za podmínek tlaku v paměti.  Dostatečné nastavení rezervace je obzvláště důležité pro úlohy náročné na zápis nebo pokud ukládáte větší hodnoty (100 kB nebo více) v Redisu. Měli byste začít s 10 % velikosti mezipaměti a zvýšit toto procento, pokud máte zatížení s vysokým obsahem zápisu.

 * **Redis funguje nejlépe s menšími hodnotami**, takže zvažte sekání větších dat do více klíčů.  V [této diskusi Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), některé úvahy jsou uvedeny, že byste měli pečlivě zvážit.  Přečtěte si [tento článek](cache-troubleshoot-client.md#large-request-or-response-size) příklad problému, který může být způsoben velkými hodnotami.

 * **Vyhledejte instanci mezipaměti a aplikaci ve stejné oblasti.**  Pokud byste se připojovali k mezipaměti v jiné oblasti, výrazně byste tím zvýšili latenci a snížili spolehlivost.  I když se můžete připojit z vnějšku Azure, nedoporučuje se *zejména při použití Redis jako mezipaměti*.  Pokud používáte Redis jako úložiště klíč/hodnota, latence nemusí být primární problém. 

 * **Znovu použít připojení.**  Vytváření nových připojení je nákladné a zvyšuje latenci, takže připojení můžete co nejvíce znovu použít. Pokud se rozhodnete vytvořit nová připojení, nezapomeňte před uvolněním starých připojení zavřít (i v jazycích spravované paměti, jako je .NET nebo Java).

 * **Nakonfigurujte klientskou knihovnu tak, aby *používala časový limit připojení* v šakadu alespoň 15 sekund**, což poskytuje systému čas na připojení i za vyšších podmínek procesoru.  Malá hodnota časového limitu připojení nezaručuje, že připojení je navázáno v tomto časovém rámci.  Pokud se něco pokazí (vysoký procesor klienta, vysoký procesor serveru a tak dále), pak krátká hodnota časového limitu připojení způsobí selhání pokusu o připojení. Toto chování často zhoršuje špatnou situaci.  Místo pomoci, kratší časové toky zhoršit problém vynucením systému restartovat proces pokusu o opětovné připojení, což může vést k *připojení -> selhání -> opakování* smyčky. Obecně doporučujeme ponechat časový limit připojení na 15 sekund nebo vyšší. Je lepší nechat pokus o připojení uspět po 15 nebo 20 sekundách, než aby se rychle nezdaří, pouze opakovat. Taková opakování smyčky může způsobit, že výpadek vydrží déle, než když necháte systém jen trvat déle zpočátku.  
     > [!NOTE]
     > Tento návod je specifický pro *pokus o připojení* a nesouvisí s časem, který jste ochotni čekat na *operaci,* jako je GET nebo SET k dokončení.
 
 * **Vyhněte se nákladným operacím** - Některé operace Redis, jako je příkaz [KEYS,](https://redis.io/commands/keys) jsou *velmi* drahé a je třeba se jim vyhnout.  Další informace naleznete v některých aspektech týkajících se [dlouhotrvajících příkazů.](cache-troubleshoot-server.md#long-running-commands)

 * **Použití šifrování TLS** – Azure Cache for Redis vyžaduje ve výchozím nastavení šifrovanou komunikaci TLS.  V současné době jsou podporovány verze TLS 1.0, 1.1 a 1.2.  TLS 1.0 a 1.1 jsou však na cestě k vyřazení v celém odvětví, takže pokud je to možné, použijte TLS 1.2.  Pokud vaše klientská knihovna nebo nástroj nepodporuje TLS, pak povolení nešifrovaných připojení lze provést [prostřednictvím portálu Azure](cache-configure.md#access-ports) nebo [správu API](https://docs.microsoft.com/rest/api/redis/redis/update).  V takových případech, kdy šifrovaná připojení nejsou možná, umístění mezipaměti a klientské aplikace do virtuální sítě by se doporučuje.  Další informace o tom, které porty se používají ve scénáři mezipaměti virtuální sítě, naleznete v této [tabulce](cache-how-to-premium-vnet.md#outbound-port-requirements).
 
## <a name="memory-management"></a>Správa paměti
Existuje několik věcí souvisejících s využitím paměti v rámci instance serveru Redis, které můžete chtít zvážit.  Zde je několik:

 * **Zvolte [zásady vyřazení,](https://redis.io/topics/lru-cache) které fungují pro vaši aplikaci.**  Výchozí zásada pro Azure Redis je *volatile-lru*, což znamená, že pouze klíče, které mají sadu hodnot TTL budou způsobilé pro vyřazení.  Pokud žádné klíče nemají hodnotu TTL, systém nebude vyřazovat žádné klíče.  Pokud chcete, aby systém povolit libovolný klíč, který má být vystěhován, pokud pod tlakem paměti, pak můžete zvážit *allkeys-lru* zásady.

 * **Nastavte hodnotu vypršení platnosti klíčů.**  Vypršení platnosti odebere klíče proaktivně namísto čekání, dokud není tlak paměti.  Při vyřazení se kop z důvodu tlaku paměti, může způsobit další zatížení serveru.  Další informace naleznete v dokumentaci k příkazům [EXPIRE](https://redis.io/commands/expire) a [EXPIREAT.](https://redis.io/commands/expireat)
 
## <a name="client-library-specific-guidance"></a>Pokyny specifické pro klientskou knihovnu
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - Kterého klienta mám použít?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Hlávkový salát (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jediové (Jáva)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net zprostředkovatele stavu relace](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Kdy je bezpečné to zopakovat?
Bohužel, neexistuje snadná odpověď.  Každá aplikace se musí rozhodnout, jaké operace lze opakovat a které ne.  Každá operace má jiné požadavky a meziklíčové závislosti.  Zde je několik věcí, které byste mohli zvážit:

 * Chyby na straně klienta můžete získat, i když redis úspěšně spustil příkaz, o jeho žonoch, o jeho žoncha.  Například:
     - Časové opony jsou koncept na straně klienta.  Pokud se operace dostala na server, server spustí příkaz i v případě, že klient vzdá čekání.  
     - Dojde-li k chybě v připojení soketu, není možné zjistit, zda operace skutečně spuštěna na serveru.  Například chyba připojení může dojít poté, co server zpracován požadavek, ale dříve, než klient obdrží odpověď.
 *  Jak moje aplikace reaguje, když omylem spustím stejnou operaci dvakrát?  Co když například převýtčím celé číslo dvakrát místo jednou?  Je moje aplikace zápisu do stejného klíče z více míst?  Co když logika opakování přepíše hodnotu nastavenou jinou částí aplikace?

Pokud chcete otestovat, jak váš kód funguje v chybových podmínkách, zvažte použití [funkce Restartovat](cache-administration.md#reboot). Restartování umožňuje zjistit, jak spojení blips vliv na vaši aplikaci.

## <a name="performance-testing"></a>Testování výkonu
 * **Začněte `redis-benchmark.exe` pomocí** získat pocit pro možné propustnost/latence před psaním vlastní perf testy.  Redis-benchmark dokumentace lze [nalézt zde](https://redis.io/topics/benchmarks).  Všimněte si, že redis-benchmark nepodporuje TLS, takže budete muset [povolit port non-TLS prostřednictvím portálu](cache-configure.md#access-ports) před spuštěním testu.  [Windows kompatibilní verze redis-benchmark.exe lze nalézt zde](https://github.com/MSOpenTech/redis/releases)
 * Virtuální počítač klienta používaný pro testování by měl být **ve stejné oblasti** jako instance mezipaměti Redis.
 * **Doporučujeme používat dv2 VM Series** pro vašeho klienta, protože mají lepší hardware a poskytne nejlepší výsledky.
 * Ujistěte se, že klientský virtuální počítač, který používáte, má **alespoň tolik výpočetních prostředků a šířky pásma* jako testovaná mezipaměť. 
 * Pokud jste v systému Windows, **povolte v** klientském počítači v počítači.  [Podrobnosti naleznete zde](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Příklad powershellový skript:
     >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdapterRSS -Name ( Get-NetAdapter). Jméno 
     
 * **Zvažte použití instancí Úrovně Premium Redis**.  Tyto velikosti mezipaměti budou mít lepší latenci sítě a propustnost, protože běží na lepším hardwaru pro procesor i síť.
 
     > [!NOTE]
     > Naše pozorované výsledky výkonu jsou [zveřejněny zde](cache-faq.md#azure-cache-for-redis-performance) pro vaši referenci.   Uvědomte si také, že SSL/TLS přidává některé režii, takže můžete získat různé latence a/nebo propustnost, pokud používáte šifrování přenosu.
 
### <a name="redis-benchmark-examples"></a>Příklady redis-benchmarku
**Nastavení před testováním**: Připravte instanci mezipaměti s daty požadovanými pro níže uvedené příkazy pro testování latence a propustnosti.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**Testování latence**: Testování požadavků GET pomocí datové části 1 kS.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Testování propustnost:** Pipelined GET požadavky s 1k datové části.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50
