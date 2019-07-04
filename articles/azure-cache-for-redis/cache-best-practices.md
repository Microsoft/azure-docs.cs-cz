---
title: Osvědčené postupy pro mezipaměť Azure Redis
description: Zjistěte, jak používat Azure Cache pro Redis efektivně řídit těmito osvědčenými postupy.
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
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452459"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Osvědčené postupy pro mezipaměť Azure Redis 
Podle těchto osvědčených postupů, můžete pomoct maximalizovat výkon a nákladově efektivní používání mezipaměti Azure pro Redis instance.

## <a name="configuration-and-concepts"></a>Konfigurace a koncepty
 * **Použijte úroveň Standard nebo Premium pro produkční systémy.**  Úroveň Basic je jeden uzel systém se žádná data replikace a žádná smlouva SLA. Také můžete použijte mezipaměť o velikosti alespoň C1.  C0 mezipaměti jsou určené pro scénáře vývoje/testování jednoduché vzhledem k tomu, že mají sdílené jádro procesoru, málo paměti a jsou náchylné k problémům. "hlučného souseda".

 * **Mějte na paměti, že Redis je úložiště dat v paměti.**  [Tento článek](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) popisuje některé scénáře, kde může dojít ke ztrátě dat.

 * **Vývoj systému tak, že dokáže zpracovat připojení blips** [z důvodu opravy chyb a převzetí služeb při selhání](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Konfigurace vašeho [vyhrazené maxmemory nastavení](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) zrychlení odezvy systému** podmínek přetížení paměti.  Toto nastavení je obzvláště důležité pro úlohy náročné na zápis nebo pokud ukládáte vyšší hodnoty (100 KB nebo déle) v Redis.  Můžu by doporučujeme počínaje 10 % velikosti mezipaměti a pak zvýšení, pokud máte zatížení náročné na zápis. Zobrazit [některé aspekty](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) při výběru hodnoty.

 * **Redis funguje nejlépe s menší hodnoty**, proto zvažte dělení větší data do více klíčů.  V [této diskuse Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), některé důležité informace jsou uvedeny, měli byste pečlivě zvážit.  Čtení [v tomto článku](cache-how-to-troubleshoot.md#large-requestresponse-size) pro problém s příklad, který může být způsobeno velké hodnoty.

 * **Vyhledejte instance mezipaměti a aplikace ve stejné oblasti.**  Připojení k mezipaměti v jiné oblasti může výrazně zvýšit latenci a snížení spolehlivosti.  Když se můžete připojit z mimo Azure, není doporučeno *zvláště při použití jako mezipaměť Redis*.  Pokud používáte Redis jako jenom úložiště dvojic klíč/hodnota, nemusí být latence prvořadým zájmem. 

 * **Opakované použití připojení** – vytvoření nových připojení je nákladné a zvyšuje latenci, proto znovu použít připojení co největší míře. Pokud budete chtít vytvořit nová připojení, ujistěte se, že ukončete původní připojení ještě před vydáním (i v paměti pro spravované jazyky, jako je .NET nebo Javě).

 * **Konfigurovat vaše Klientská knihovna pro použití *časový limit připojení* alespoň 15 sekund**, poskytuje systémový čas připojit i podmínek vyšší využití procesoru.  Hodnota časového limitu připojení malé nezaručuje, že se připojení v tomto časovém rámci.  Pokud se něco přejde nesprávné (klienta vysoké využití procesoru, procesoru vysokou serveru a tak dále), pak hodnota časového limitu připojení krátký způsobí pokus o připojení selže. Toto chování je často ještě hůř chybný stav.  Místo pomoci, kratší časové limity zhoršit problém tak, že vynucuje se restartování procesu pokusu o opětovné připojení, což může vést k *připojení -> selhání -> Zkuste to znovu* smyčky. Obecně doporučujeme ponechat připojení k vypršení časového limitu po 15 sekundách nebo vyšší. Je lepší umožní úspěšné po 15 nebo 20 sekund, než je jeho selhání pokus o připojení jenom k opakování. Smyčka opakování může způsobit vaše výpadek delší, než Pokud necháte systém právě trvat déle původně.  
     > [!NOTE]
     > Tyto doprovodné materiály jsou specifické pro *pokus o připojení* a vzájemně nesouvisejí na dobu, kterou jste ochotní počkat *operace* například GET nebo SET na dokončení.
 

 * **Vyhněte se náročné příkazy** – některé operace, jako je třeba redis [příkazu klíče](https://redis.io/commands/keys), jsou *velmi* nákladné a mělo by se vyhnout.  Další informace najdete v tématu [některé aspekty kolem náročné příkazy](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>Správa paměti
Existují související s využití paměti v rámci vaší instance serveru Redis, která můžete chtít zvážit několik věcí.  Tady je několik:

 * **Zvolte [zásadu vyřazení](https://redis.io/topics/lru-cache) , který pracuje pro vaši aplikaci.**  Výchozí zásady pro Azure redis cache je *volatile lru*, což znamená, že hodnota jenom klíče, které mají hodnotu TTL sada bude mít nárok na vyřazení.  Pokud žádné klíče mají hodnotu TTL, systém nebude vyřaďte všechny klíče.  Pokud chcete, aby systém umožňující libovolné klávesy vyřazení Pokud přetížena paměť, pak můžete chtít zvážit *allkeys lru* zásad.

 * **Nastavte hodnotu vypršení platnosti na vaše klíče.**  Tato akce odebere klíče proaktivně namísto čekání, dokud nedojde k přetížení paměti.  Při vyřazení rozjíždí z důvodu tlaku na paměť, může to způsobit zvýšenou zátěž na vašem serveru.  Další informace najdete v tématu v dokumentaci [vypršení platnosti](https://redis.io/commands/expire) a [ExpireAt](https://redis.io/commands/expireat) příkazy.
 
## <a name="client-library-specific-guidance"></a>Konkrétní pokyny klientské knihovny
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java – kterého klienta použít?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Salát (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Zprostředkovatel stavu relací Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Když je bezpečně opakovat?
Bohužel neexistuje žádný snadný odpovědí.  Každá aplikace je potřeba rozhodnout, jaké operace lze opakovat a které nemohou.  Každá operace má jiné požadavky a závislosti mezi klíče.  Tady je pár věcí, které můžete zvážit:

 * I v případě, že Redis úspěšně spustil příkaz, který se zobrazí výzva ho ke spuštění, může docházet k chybám na straně klienta.  Příklad:
     - Vypršení časového limitu jsou koncept na straně klienta.  Pokud operace dorazila na server, server spusťte příkaz i v případě, že klient vrací čekání.  
     - Když dojde k chybě na připojení soketu, není možné vědět, pokud operace ve skutečnosti byl na serveru.  Chyba připojení může dojít například po zpracování požadavku server, ale předtím, než se klient obdrží odpověď.
 *  Jak Moje aplikace react I omylem spuštění operace dvakrát?  Co když pro instanci můžu zvýšit celé dvakrát místo jen jednou?  Moje aplikace zapisuje do stejného klíče z více míst?  Co když Moje logiku opakování přepíše hodnoty nastavené v jiné části Moje aplikace?

Pokud chcete otestovat fungování kódu v rámci chybové stavy, zvažte použití [restartování funkce](cache-administration.md#reboot). To umožňuje zobrazit způsob připojení blips ovlivnit vaši aplikaci.

## <a name="performance-testing"></a>Testování výkonu
 * **Začněte tím, že pomocí `redis-benchmark.exe`**  získat představu možné propustnost a latence před zápisem vlastní výkonu testy.  Dokumentace ke službě redis srovnávacího testu může být [tady](http://redis.io/topics/benchmarks).  Všimněte si, že redis srovnávacích testů nepodporuje SSL, proto budete muset [povolit port bez SSL prostřednictvím portálu](cache-configure.md#access-ports) před spuštěním testu.  [Kompatibilní verze windows redis benchmark.exe najdete tady](https://github.com/MSOpenTech/redis/releases)
 * Klient použít pro testování virtuální počítač by měl být **ve stejné oblasti** jako vaší instanci služby Redis cache.
 * **Doporučujeme použít virtuální počítač řady Dv2** pro vašeho klienta, dokud budou mít lepší hardwaru a poskytne nejlepší výsledky.
 * Ujistěte se, že klient má virtuální počítač používáte **alespoň tolik výpočetní prostředky a šířky pásma* jako mezipaměť testován. 
 * **Povolit VRSS** v klientském počítači. Pokud jste na Windows.  [Podrobnosti najdete tady](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Ukázkový skript prostředí powershell:
     >Prostředí PowerShell - ExecutionPolicy Unrestricted Enable-NetAdapterRSS – název (Get-NetAdapter). Jméno 
     
 * **Zvažte použití instance Redis na úrovni Premium**.  Tyto velikosti mezipaměti nebude mít lepší síťová latence a propustnosti, protože běží na lepší hardwaru pro procesoru a sítě.
 
     > [!NOTE]
     > Naše pozorované výsledky jsou [publikované](cache-faq.md#azure-cache-for-redis-performance) pro vaši informaci.   Také mějte na paměti, že SSL/TLS přidává režijní náklady, proto může získat různé latence nebo propustnost, pokud používáte šifrování přenosu.
 
### <a name="redis-benchmark-examples"></a>Příklady redis srovnávacích testů
**Předběžný test nastavení**: To připraví instance mezipaměti s údaje požadované pro latenci a propustnost testování následující příkazy.
> NASTAVIT n - 10 - d 1024 redis benchmark.exe - h yourcache.redis.cache.windows.net - yourAccesskey -t 

**K testování latence**: Tento příkaz otestuje požadavků GET pomocí datové části je 1 kB.
> ZÍSKAT redis benchmark.exe - h yourcache.redis.cache.windows.net - yourAccesskey – t -d 1024 - P 50 -c 4

**Testování propustnosti:** Tato funkce využívá požadavky zřetězena GET s datovou částí 1 kB.
> ZÍSKAT redis benchmark.exe - h yourcache.redis.cache.windows.net - yourAccesskey – t -n 1000000 - d 1024 - P 50 - c 50
