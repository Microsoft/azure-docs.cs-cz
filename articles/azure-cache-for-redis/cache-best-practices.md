---
title: Osvědčené postupy pro Azure cache pro Redis
description: Pomocí těchto osvědčených postupů se dozvíte, jak efektivně používat službu Azure cache pro Redis.
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: 29e5a81c438a7aa834fc002b916739a952c9a270
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72785874"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Osvědčené postupy pro Azure cache pro Redis 
Pomocí těchto osvědčených postupů můžete maximalizovat výkon a nákladově efektivní využití vaší instance Azure cache pro Redis.

## <a name="configuration-and-concepts"></a>Konfigurace a koncepty
 * **Pro produkční systémy použijte úroveň Standard nebo Premium.**  Úroveň Basic je systém s jedním uzlem bez replikace dat a smlouvou SLA. Použijte také alespoň mezipaměť C1.  Mezipaměti C0 jsou určeny pro jednoduché scénáře pro vývoj a testování, protože mají sdílený procesor, málo paměti a jsou náchylné k problémům s vysokou úrovní šumu.

 * **Pamatujte, že Redis je úložiště dat v paměti.**  [Tento článek](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) popisuje některé scénáře, kdy může dojít ke ztrátě dat.

 * **Vytvořte svůj systém tak, aby mohl zpracovávat připojení výkyvů** [kvůli opravám a převzetí služeb při selhání](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Nakonfigurujte [Nastavení rezervované pro maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , aby se zlepšila odezva systému** v podmínkách pro tlak paměti.  Toto nastavení je zvlášť důležité pro úlohy náročné na zápis, nebo pokud ukládáte větší hodnoty (100 KB nebo více) v Redis. Doporučuje se začít s 10% velikosti mezipaměti a potom zvýšit procento, pokud máte zatížení náročné na zápis.

 * **Redis funguje nejlépe s menšími hodnotami**, takže zvažte možnost roztrhané větší data do více klíčů.  V [této diskuzi Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)jsou uvedeny některé okolnosti, které byste měli pečlivě zvážit.  V [tomto článku](cache-troubleshoot-client.md#large-request-or-response-size) najdete příklad problému, který může být způsoben velkými objemy dat.

 * **Vyhledejte instanci mezipaměti a aplikaci ve stejné oblasti.**  Připojení k mezipaměti v jiné oblasti může významně zvýšit latenci a snížit spolehlivost.  I když se můžete připojit mimo Azure, nedoporučuje se *obzvláště při použití Redis jako mezipaměti*.  Pokud používáte Redis jako úložiště typu klíč/hodnota, latence nemusí být primárním problémem. 

 * **Opakované použití připojení** – vytváření nových připojení je náročné a zvyšuje se latence, aby bylo možné znovu použít připojení co nejvíc. Pokud se rozhodnete vytvořit nová připojení, ujistěte se, že jste staré připojení zavřeli předtím, než je uvolníte (i ve spravovaných paměťových jazycích, jako je .NET nebo Java).

 * **Nakonfigurujte klientskou knihovnu tak, aby používala *časový limit připojení* aspoň 15 sekund**, a systémovým časem umožní připojit se i v rámci vyšších procesorových podmínek.  Hodnota časového limitu malého připojení nezaručuje, že v daném časovém rámci bude vytvořeno připojení.  Pokud dojde k nějakému problému (vysoký procesor procesoru, vysoký procesor serveru atd.), pak hodnota krátkého časového limitu připojení způsobí selhání pokusu o připojení. Toto chování často způsobuje horší špatnou situaci.  Namísto zvýšení časových limitů tím, že se postará o problém, vynutí systém restartování procesu pokusu o opětovné připojení, což může vést k *selhání > typu Connect->* . Obecně doporučujeme, abyste časový limit připojení nechali 15 sekund nebo vyšší. Je lepší podařit, aby se Váš pokus o připojení uspěl po 15 až 20 sekundách, než by bylo možné ho rychle opakovat. Tato smyčka opakování může způsobit, že výpadek bude trvat déle, než když necháte systém jenom na začátku.  
     > [!NOTE]
     > Tyto doprovodné materiály jsou specifické pro *pokus o připojení* a nesouvisejí s časem, na který jste ochotni čekat na *operaci* , jako je získání nebo nastavení dokončení.
 
 * **Nepoužívejte nákladné operace** – některé operace Redis, jako je příkaz [klíče](https://redis.io/commands/keys) , jsou *velmi* nákladné a měly by se jim vyhnout.  Další informace najdete v tématu některé okolnosti týkající se [dlouho běžících příkazů](cache-troubleshoot-server.md#long-running-commands) .

 * **Použití šifrování TLS** – služba Azure cache pro Redis vyžaduje ve výchozím nastavení ŠIFROVANOU komunikaci TLS.  V tuto chvíli jsou podporované TLS verze 1,0, 1,1 a 1,2.  TLS 1,0 a 1,1 ale mají cestu k vyřazení celého oboru, takže pokud je to možné, použijte protokol TLS 1,2.  Pokud vaše Klientská knihovna nebo nástroj nepodporuje protokol TLS, je možné povolit nešifrovaná připojení [pomocí Azure Portal](cache-configure.md#access-ports) nebo [rozhraní API pro správu](https://docs.microsoft.com/rest/api/redis/redis/update).  V takových případech, kde nejsou šifrovaná připojení možná, se doporučuje umístění mezipaměti a klientské aplikace do virtuální sítě.  Podrobnosti o tom, které porty se používají pro 
 
## <a name="memory-management"></a>Správa paměti
Existuje několik věcí, které souvisí s využitím paměti v rámci instance serveru Redis, kterou byste chtěli zvážit.  Tady je několik příkladů:

 * **Vyberte [zásadu vyřazení](https://redis.io/topics/lru-cache) , která je pro vaši aplikaci vhodná.**  Výchozí zásada pro Azure Redis je *volatile-LRU*, což znamená, že pro vyřazení bude možné využívat jenom klíče, které mají nastavenou hodnotu TTL.  Pokud žádné klíče neobsahují hodnotu TTL, nebude systém vyřadit žádné klíče.  Pokud chcete, aby systém povoloval vyřazení jakékoli klíče v případě přetížení paměti, můžete zvážit zásady *AllKeys-LRU* .

 * **Nastavte hodnotu vypršení platnosti klíčů.**  Tím se odeberou aktivní klíče místo čekání, dokud nedojde k přetížení paměti.  Při vyřazení z důvodu přetížení paměti může dojít k dalšímu zatížení serveru.  Další informace najdete v dokumentaci k příkazům [vypršení platnosti](https://redis.io/commands/expire) a [ExpireAt](https://redis.io/commands/expireat) .
 
## <a name="client-library-specific-guidance"></a>Doprovodné materiály ke klientské knihovně
 * [StackExchange. Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Jak mám použít klienta Java?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lettuce (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Zprostředkovatel stavu relace Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Kdy je bezpečné to zkusit znovu?
Bohužel neexistuje žádná jednoduchá odpověď.  Každá aplikace musí rozhodnout, jaké operace se můžou opakovat a které nemůžou.  Každá operace má jiné požadavky a závislosti mezi klíči.  Tady je několik věcí, které byste mohli zvážit:

 * Můžete získat chyby na straně klienta, i když Redis úspěšně spustil příkaz, který jste si vyžádali ke spuštění.  Například:
     - Časové limity jsou koncept na straně klienta.  Pokud operace dosáhla serveru, Server spustí příkaz i v případě, že klient čeká na vyčekání.  
     - Pokud dojde k chybě v připojení soketu, není možné zjistit, zda na serveru skutečně běžela operace.  Například Chyba připojení může nastat po zpracování požadavku serverem, ale před tím, než klient obdrží odpověď.
 *  Jak aplikace reaguje, když omylem spustím stejnou operaci dvakrát?  Například, co když odrůst Integer dvakrát místo pouze jednou?  Zapisuje moje aplikace do stejného klíče z více míst?  Co když logika opakování přepíše hodnotu nastavenou některou jinou část mé aplikace?

Pokud chcete otestovat, jak váš kód funguje v chybových podmínkách, zvažte použití [funkce restartu](cache-administration.md#reboot). To vám umožní zjistit, jak připojení výkyvů ovlivní vaši aplikaci.

## <a name="performance-testing"></a>Testování výkonu
 * **Začněte tím, že použijete `redis-benchmark.exe`** k dosažení možné propustnosti/latence před zápisem vlastních testů výkonu.  Redis-srovnávací dokumentaci najdete [tady](https://redis.io/topics/benchmarks).  Upozorňujeme, že Redis-test nepodporuje SSL, takže před spuštěním testu budete muset [Povolit port bez SSL přes portál](cache-configure.md#access-ports) .  [Verzi Redis-benchmark. exe kompatibilní s Windows najdete tady.](https://github.com/MSOpenTech/redis/releases)
 * Virtuální počítač klienta, který se používá pro testování, by měl být **ve stejné oblasti** jako instance služby Redis Cache.
 * Pro vašeho klienta **doporučujeme používat pro Dv2 řadu virtuálních počítačů** , protože mají lepší hardware a poskytnou nejlepší výsledky.
 * Ujistěte se, že virtuální počítač klienta, který používáte, má při testování mezipaměti*aspoň tolik výpočetních a šířek pásma* . 
 * Pokud pracujete v systému Windows, **Povolte VRSS** na klientském počítači.  [Podrobnosti najdete tady](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Ukázkový skript prostředí PowerShell:
     >PowerShell – ExecutionPolicy bez omezení Enable-NetAdapterRSS-Name (Get-NetAdapter). Jméno 
     
 * **Zvažte použití instancí Redis úrovně Premium**.  Tyto velikosti mezipaměti budou mít lepší latenci a propustnost sítě, protože jsou spuštěné na lepším hardwaru pro procesor i síť.
 
     > [!NOTE]
     > Výsledky našich pozorovaných výsledků jsou [publikovány zde](cache-faq.md#azure-cache-for-redis-performance) pro váš odkaz.   Také mějte na paměti, že protokol SSL/TLS přináší režijní náklady, takže pokud používáte šifrování přenosu, můžete získat různé latence a propustnost.
 
### <a name="redis-benchmark-examples"></a>Redis – příklady srovnávacích testů
**Nastavení před testováním**: tím se připraví instance mezipaměti s daty potřebnými pro níže uvedené příkazy pro latenci a testování propustnosti.
> Redis-benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t SET-n 10-d 1024 

**Testování latence**: test požadavků GET pomocí datové části 1 tisíc
> Redis-benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-d 1024-P 50-c 4

**Postup testování propustnosti:** Používá se k tomu požadavky GET v kanálu s 1 tisíc datovou částí.
> Redis-benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-n 1000000-d 1024-P 50-c 50
