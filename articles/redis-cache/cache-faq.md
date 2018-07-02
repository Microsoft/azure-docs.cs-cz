---
title: Azure Redis Cache – nejčastější dotazy | Microsoft Docs
description: Další odpovědi na časté otázky, vzory a osvědčené postupy pro Azure Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: wesmc
ms.openlocfilehash: 6c308205c5adb05f4c7e1668c67adea414020ea2
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113268"
---
# <a name="azure-redis-cache-faq"></a>Nejčastější dotazy k Azure Redis Cache
Další odpovědi na časté otázky, vzorce a osvědčené postupy pro Azure Redis Cache.

## <a name="what-if-my-question-isnt-answered-here"></a>Co když není zde odpovědi můj dotaz?
Pokud váš dotaz není zde uvedeno, dejte nám vědět, a pomůžeme vám najít odpověď.

* Můžete odeslat dotaz v komentářích na konci tohoto článku a spojte s týmem Azure Cache a ostatními členy komunity o tomto článku.
* K dosažení širší cílové skupiny, můžete odeslat dotaz na [fórum MSDN mezipaměti Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) a spojte s týmem mezipaměti Azure a ostatní členové komunity.
* Pokud chcete, aby se žádosti o funkci, můžete odeslat požadavky a nápady, jak [Azure Redis Cache User Voice](https://feedback.azure.com/forums/169382-cache).
* Můžete také odeslat e-mailu na nás na adrese [externí zpětnou vazbu mezipaměti Azure](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Základy služby Azure Redis Cache
Nejčastější dotazy v této části se věnují některé základní informace o Azure Redis Cache.

* [Co je Azure Redis Cache?](#what-is-azure-redis-cache)
* [Jak můžete můžu začít pracovat s Azure Redis Cache?](#how-can-i-get-started-with-azure-redis-cache)

Následující nejčastější dotazy zahrnují základní koncepty a otázky týkající se Azure Redis Cache a jsou zodpovězeny v některé z dalších částí – nejčastější dotazy.

* [Jaké nabídky a velikosti Redis Cache mám použít?](#what-redis-cache-offering-and-size-should-i-use)
* [Jaké klientů Redis cache mám použít?](#what-redis-cache-clients-can-i-use)
* [Je k dispozici místní emulátor pro Azure Redis Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Jak monitorovat stav a výkon Moje mezipaměti?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Plánování nejčastější dotazy
* [Jaké nabídky a velikosti Redis Cache mám použít?](#what-redis-cache-offering-and-size-should-i-use)
* [Azure Redis Cache výkonu](#azure-redis-cache-performance)
* [V oblasti, které by měl I najít Můj mezipaměť?](#in-what-region-should-i-locate-my-cache)
* [Jak se fakturuje pro Azure Redis Cache?](#how-am-i-billed-for-azure-redis-cache)
* [Můžete použít Azure Redis Cache pomocí Azure Cloud vlády, Čína cloudu Azure nebo Microsoft Azure v Německu?](#can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Vývoj nejčastější dotazy
* [Co udělat možnosti konfigurace StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Jaké klientů Redis cache mám použít?](#what-redis-cache-clients-can-i-use)
* [Je k dispozici místní emulátor pro Azure Redis Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Jak můžete spouštět příkazy Redis?](#how-can-i-run-redis-commands)
* [Proč Azure Redis Cache nemá webu MSDN knihovny tříd jako část jinými službami Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Můžete použít Azure Redis Cache jako mezipaměť relace PHP?](#can-i-use-azure-redis-cache-as-a-php-session-cache)
* [Jaké jsou databáze Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Nejčastější dotazy k zabezpečení
* [Pokud by měl povolit port bez SSL pro připojení k Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Nejčastější dotazy k produkční
* [Jaké jsou některé z osvědčených postupů produkční?](#what-are-some-production-best-practices)
* [Jaké jsou některé aspekty při pomocí běžných příkazů Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak můžete otestovat a testování výkonu Moje mezipaměti?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Důležité podrobnosti o fondu růst](#important-details-about-threadpool-growth)
* [Povolit serveru globálního katalogu získat další propustnosti na straně klienta při používání StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Faktory ovlivňující výkon kolem připojení](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorování a řešení potíží s nejčastější dotazy
Nejčastější dotazy v této části se věnují běžné monitorování a řešení potíží s dotazy. Další informace o monitorování a řešení potíží s vaší instancí Azure Redis Cache najdete v tématu [jak monitorovat Azure Redis Cache](cache-how-to-monitor.md) a [řešení potíží s Azure Redis Cache](cache-how-to-troubleshoot.md).

* [Jak monitorovat stav a výkon Moje mezipaměti?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Proč se zobrazuje časové limity?](#why-am-i-seeing-timeouts)
* [Proč byl klient odpojen z mezipaměti?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Nejčastější dotazy k předchozí mezipaměti nabídky
* [Jaké nabídky Azure Cache je pro mě nejlepší?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-redis-cache"></a>Co je Azure Redis Cache?
Služba Azure Redis Cache je založená na oblíbené opensourcové [mezipaměti Redis Cache](http://redis.io). Umožňuje přístup do zabezpečené, vyhrazené mezipaměti Redis, spravované společností Microsoft a přístupná ze všech aplikací v rámci Azure. Podrobnější přehled najdete [Azure Redis Cache](https://azure.microsoft.com/services/cache/) stránky produktu na Azure.com.

### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Jak můžete můžu začít pracovat s Azure Redis Cache?
Existuje několik způsobů, které můžete začít používat s Azure Redis Cache.

* Můžete zkontrolovat jednou z našich kurzů k dispozici pro [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), a [Python](cache-python-get-started.md).
* Můžete sledovat [postup vytvoření vysoce výkonné aplikace pomocí Microsoft Azure Redis Cache](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Můžete zkontrolovat na dokumentaci klienta pro klienty, kteří s jazykem vývoj projektu pro naleznete v části Použití Redis. Existuje mnoho klientů Redis, které lze použít s Azure Redis Cache. Seznam klientů Redis, naleznete v části [ http://redis.io/clients ](http://redis.io/clients).

Pokud účet Azure nemáte, můžete:

* [Otevřít bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Získáte kredity, které můžete použít k vyzkoušení placených služeb Azure. I po vyčerpání kreditů si můžete účet ponechat a používat bezplatné funkce a služby Azure.
* [Aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure.

<a name="cache-size"></a>

### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Jakou velikost a kterou nabídku Redis Cache mám použít?
Každou nabídku Azure Redis Cache poskytuje různé úrovně **velikost**, **šířky pásma**, **vysokou dostupnost**, a **SLA** možnosti.

Níže jsou faktory pro výběr nabídku mezipaměti.

* **Paměť**: základní a Standard vrstev nabízejí 250 MB – 53 GB. Úroveň Premium nabízí až 530 GB. Další informace najdete v tématu [Azure Redis Cache ceny](https://azure.microsoft.com/pricing/details/cache/).
* **Výkon sítě**: Pokud máte zatížení, která vyžaduje vysokou propustnost, úroveň Premium nabízí větší šířku pásma ve srovnání s Standard nebo Basic. Také v jednotlivých vrstvách mít větší velikost mezipaměti větší šířku pásma z důvodu základní virtuální počítač, který je hostitelem mezipaměti. Najdete v článku [následující tabulky](#cache-performance) Další informace.
* **Propustnost**: úroveň Premium poskytuje maximální propustnost k dispozici. Pokud mezipaměti serveru nebo klienta dosáhne omezení šířky pásma, může se zobrazit časové limity na straně klienta. Další informace najdete v následující tabulce.
* **Vysoká dostupnost nebo SLA**: Azure Redis Cache zaručuje, že mezipaměť Standard nebo Premium je k dispozici minimálně 99,9 % času. Další informace o naší smlouvě SLA najdete v tématu [Azure Redis Cache ceny](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Smlouva SLA se vztahuje pouze na připojení ke koncovým bodům mezipaměti. Součástí smlouvy SLA není ochrana proti ztrátě dat. Doporučujeme používat funkce trvalosti dat Redis zvyšte odolnost proti ztrátě dat v úrovni Premium.
* **Trvalosti dat redis**: úroveň Premium vám umožňuje zachovat data v mezipaměti v účtu Azure Storage. V mezipaměti Basic nebo Standard všechna data uložena pouze v paměti. Pokud jsou základní problémy infrastruktury může být potenciální ztrátě dat. Doporučujeme používat funkce trvalosti dat Redis zvyšte odolnost proti ztrátě dat v úrovni Premium. Azure Redis Cache nabízí RDB a AOF (už brzy) možnosti v trvalosti Redis. Další informace najdete v tématu [postup konfigurace trvalosti pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-persistence.md).
* **Redis Cluster**: Chcete-li vytvořit ukládá do mezipaměti větší než 53 GB nebo sdílení dat mezi různými uzly Redis, můžete použít Redis clustering, která je dostupná v úrovni Premium. Každý uzel se skládá z dvojice primární/replika mezipaměti pro vysokou dostupnost. Další informace najdete v článku [Postup konfigurace clusterů pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-clustering.md).
* **Rozšířené zabezpečení a sítě izolace**: nasazení virtuální sítě Azure (VNET) poskytuje lepší zabezpečení a izolaci pro vaši Azure Redis Cache, stejně jako podsítě, zásady řízení přístupu a další funkce k dalšímu omezení přístupu. Další informace najdete v článku [Postup konfigurace podpory služby Virtual Network pro mezipaměť Azure Redis Cache Premium](cache-how-to-premium-vnet.md).
* **Konfigurace Redis**: na úrovních Standard a Premium, můžete nakonfigurovat pro oznámení Keyspace Redis.
* **Maximální počet připojení klienta**: Premium úroveň nabízí maximální počet klientů, které se můžou připojit k Redis, s vyšší počet připojení pro větší velikosti mezipaměti. Clustering nezvyšuje počet připojení, které jsou k dispozici pro Clusterované mezipaměti. Další informace najdete v tématu [cenách Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).
* **Vyhrazené jádra serveru Redis**: V úrovni Premium všech velikostí mezipaměti mají vyhrazené jádra pro Redis. Na úrovních Basic nebo Standard, velikost C1 a vyšší máte vyhrazený jádro serveru Redis.
* **Redis je jedním podprocesem** tak s více než dvě jádra neposkytuje Další výhodou oproti má jenom dvě jádra, ale větší velikosti virtuálních počítačů obvykle mají větší šířku pásma než menší velikost. Pokud mezipaměti serveru nebo klienta dosáhne omezení šířky pásma, obdržíte vypršení časových limitů na straně klienta.
* **Vylepšení výkonu**: mezipaměti v úrovni Premium jsou nasazeny na hardware, který má rychlejších procesorů, která poskytuje lepší výkon ve srovnání s úroveň Basic nebo Standard. Premium úroveň mezipaměti mají vyšší propustnost a nižší latenci.

<a name="cache-performance"></a>

### <a name="azure-redis-cache-performance"></a>Azure Redis Cache výkonu
Následující tabulka uvádí maximální šířka pásma hodnotami zjištěnými při testování různých velikostí Standard a Premium ukládá do mezipaměti pomocí `redis-benchmark.exe` z virtuálního počítače Iaas na koncový bod Azure Redis Cache. Pro SSL propustnost redis-srovnávacího testu se používá s stunnel pro připojení ke koncovému bodu Azure Redis Cache.

>[!NOTE] 
>Tyto hodnoty se nezaručuje a neexistuje žádný SLA pro tyto čísla, ale musí být typické. By se měly načíst otestovat vlastní aplikaci k určení velikosti mezipaměti správné pro vaši aplikaci.
>Tato čísla může změnit, protože pravidelně zanesení novější výsledky.
>

Z této tabulky jsme kreslení následující závěry:

* Propustnost, pro které mají stejnou velikost mezipaměti je vyšší ve vrstvě | Premium porovnání na plán úrovně Standard. Například s 6 GB mezipaměti, je propustnost P1 180 000 RPS ve srovnání se 100 000 pro C3.
* S Redisem clustering propustnost zvyšuje lineárně zvýšit počet horizontálních oddílů (uzlů) v clusteru. Například pokud vytvoříte cluster P4 10 horizontálními oddíly, pak k dispozici propustnost je 400,000 * 10 = 4 miliony RPS.
* Propustnost pro větší velikosti klíče je vyšší ve vrstvě | Premium porovnání na plán úrovně Standard.

| Cenová úroveň | Velikost | Procesorová jádra | Dostupná šířka pásma | Velikost hodnoty 1 KB | Velikost hodnoty 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Standardní mezipaměti velikosti** | | |**Megabity za sekundu (Mb/s) nebo megabajtů za sekundu (MB/s)** |**Požadavky na druhý bez SSL (RPS)** |**Požadavky na druhý protokol SSL (RPS)** |
| C0 |250 MB |Shared |100 / 12.5 |15,000 |7 500 |
| C1 |1 GB |1 |500 / 62.5 |38,000 |20,720 |
| C2 |2,5 GB |2 |500 / 62.5 |41,000 |37 000 |
| C3 |6 GB |4 |1000 / 125 |100,000 |90,000 |
| C4 |13 GB |2 |500 / 62.5 |60,000 |55,000 |
| C5 |26 GB |4 |1,000 / 125 |102 000 |93,000 |
| C6 |53 GB |8 |2,000 / 250 |126,000 |120,000 |
| **Velikost mezipaměti Premium** | |**Jader procesoru na horizontální oddíl** | **Megabity za sekundu (Mb/s) nebo megabajtů za sekundu (MB/s)** |**Počet požadavků za druhé (RPS) bez SSL, na horizontální oddíl** |**Požadavky na druhý protokol SSL (RPS), na horizontální oddíl** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |172,000 |
| P2 |13 GB |4 |3,000 / 375 |350,000 |341,000 |
| P3 |26 GB |4 |3,000 / 375 |350,000 |341,000 |
| P4 |53 GB |8 |6,000 / 750 |400 000 |373,000 |

Pokyny k nastavení stunnel nebo stahování nástroje Redis jako `redis-benchmark.exe`, najdete v článku [jak můžete spouštět příkazy Redis?](#cache-commands) části.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>V oblasti, které by měl I najít Můj mezipaměť?
Pro nejlepší výkon a co nejnižší latencí vyhledejte ve stejné oblasti jako vaše klientská aplikace mezipaměti Azure Redis Cache.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-redis-cache"></a>Jak se fakturuje pro Azure Redis Cache?
Ceny služby Azure Redis Cache je [zde](https://azure.microsoft.com/pricing/details/cache/). Na stránce s cenami uvádí ceny jako hodinové sazby. Mezipaměti se účtují na základě za minutu od okamžiku vytvoření mezipaměti až do mezipaměti se odstraní. Neexistuje žádná možnost pro zastavení nebo pozastavení fakturace mezipaměti.

### <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Můžete použít Azure Redis Cache pomocí Azure Cloud vlády, Čína cloudu Azure nebo Microsoft Azure v Německu?
Ano, Azure Redis Cache je dostupná v cloudu Azure Government, Čína cloudu Azure a Microsoft Azure v Německu. Adresy URL pro přístup a správu Azure Redis Cache se liší v těchto cloudech ve srovnání s veřejném cloudu Azure. 

| Cloud   | Přípona DNS pro Redis            |
|---------|---------------------------------|
| Public  | *.redis.cache.windows.net       |
| Vláda USA  | *.redis.cache.usgovcloudapi.net |
| Německo | *.redis.cache.cloudapi.de       |
| Čína   | *.redis.cache.chinacloudapi.cn  |

Další informace o informace týkající se použití Azure Redis Cache s ostatních cloudů najdete v následujících tématech.

- [Azure Government databází – mezipaměť Redis systému Azure](../azure-government/documentation-government-services-database.md#azure-redis-cache)
- [Cloud Azure Čína - mezipaměť Redis systému Azure](https://www.azure.cn/documentation/services/redis-cache/)
- [Microsoft Azure v Německu](https://azure.microsoft.com/overview/clouds/germany/)

Informace o používání Azure Redis Cache pomocí prostředí PowerShell v cloudu Azure Government, Čína cloudu Azure a Microsoft Azure v Německu najdete v tématu [jak se připojit k ostatních cloudů - Azure Redis Cache PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Co udělat možnosti konfigurace StackExchange.Redis?
StackExchange.Redis má mnoho možností. Tato část pojednává o některých běžných nastavení. Podrobné informace o možnostech StackExchange.Redis, najdete v části [StackExchange.Redis konfigurace](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Popis | Doporučení |
| --- | --- | --- |
| AbortOnConnectFail |Pokud nebude nastaven na hodnotu true, připojení znovu po selhání sítě. |Nastavte na hodnotu false a nechat StackExchange.Redis automaticky znovu připojit. |
| ConnectRetry |Počet opakování pokusů o připojení během počáteční připojení. |Viz následující poznámky k pokyny. |
| ConnectTimeout |Časový limit v ms pro operace connect. |Viz následující poznámky k pokyny. |

Výchozí hodnoty klienta jsou obvykle dostatečná. Můžete upřesnit možnosti založené na úlohu.

* **Opakování**
  * ConnectRetry a ConnectTimeout najdete obecné pokyny je služeb při selhání rychle a akci opakujte. V tomto návodu je založena na úlohy a jak dlouho na průměrném trvá pro vašeho klienta a vydejte příkaz Redis, obdrží odpověď.
  * Umožní StackExchange.Redis automaticky znovu připojila, namísto Kontrola stavu připojení a připojení sami. **Nepoužívejte vlastnost ConnectionMultiplexer.IsConnected**.
  * Snowballing – někdy se může spustit na problém, kde jsou opakování a rozrůst opakované pokusy a nikdy dojde k jeho obnovení. Pokud dojde k snowballing, měli byste zvážit použití algoritmu opakování exponenciálního omezení rychlosti jak je popsáno v [opakujte obecné pokyny](../best-practices-retry-general.md) publikována skupina Microsoft Patterns & postupy.
* **Hodnoty časového limitu**
  * Vezměte v úvahu vaše úlohy a odpovídajícím způsobem nastavit hodnoty. Pokud ukládáte velké hodnoty, nastavte časový limit na vyšší hodnotu.
  * Nastavit `AbortOnConnectFail` na hodnotu false a umožňují StackExchange.Redis znovu připojit za vás.
  * Používala jedna instance ConnectionMultiplexer pro aplikaci. LazyConnection můžete použít k vytvoření jediné instance, který je vrácen vlastností připojení, jak je znázorněno v [připojení k mezipaměti pomocí třídy ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Nastavte `ConnectionMultiplexer.ClientName` vlastnost, která má jedinečný název instance aplikace k diagnostickým účelům.
  * Použití více `ConnectionMultiplexer` instance pro vlastní úlohy.
      * Tento model můžete provést, pokud máte různých zatížení ve vaší aplikaci. Příklad:
      * Můžete mít jednu multiplexor pro práci s velké klíče.
      * Můžete mít jednu multiplexor pro práci s malé klíče.
      * Můžete nastavit různé hodnoty pro vypršení časových limitů připojení a logika opakovaných pokusů pro každou ConnectionMultiplexer, který používáte.
      * Nastavte `ClientName` vlastnost na každém multiplexor pomohou s diagnostikou.
      * Tyto pokyny mohou vést k více zjednodušený latence za `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Jaké klientů Redis cache mám použít?
Jedním z užitečných funkcí Redis je, že existuje mnoho klientů podporuje mnoho různých programovacích jazyků. Aktuální seznam klientů najdete v tématu [Redis klienti](http://redis.io/clients). Podrobné pokyny, které zahrnují několik různých jazycích a klienty, najdete v části [použití Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) a klikněte na požadovaný jazyk od jazyka přepínači v horní části článku.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Je k dispozici místní emulátor pro Azure Redis Cache?
Neexistuje žádné místní emulátor pro Azure Redis Cache, ale může spouštět MSOpenTech verzi redis-server.exe z [nástroje příkazového řádku Redis](https://github.com/MSOpenTech/redis/releases/) ve vašem místním počítač a připojte se k němu získat na podobném principu do místní mezipaměti emulátoru, jak je znázorněno v následujícím příkladu:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Volitelně můžete nakonfigurovat [redis.conf](http://redis.io/topics/config) souboru tak, aby lépe odpovídaly [výchozí nastavení mezipaměti](cache-configure.md#default-redis-server-configuration) pro vaše online Azure Redis Cache v případě potřeby.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Jak můžete spouštět příkazy Redis?
Můžete používat kterýkoli z příkazů uvedený na [Redis příkazy](http://redis.io/commands#) s výjimkou příkazy uvedené v [Redis příkazy nejsou podporované ve službě Azure Redis Cache](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Máte několik možností ke spuštění příkazů Redis.

* Pokud máte mezipaměti Standard nebo Premium, můžete spouštět příkazy Redis pomocí [konzola Redis](cache-configure.md#redis-console). Konzola Redis poskytuje bezpečný způsob ke spuštění příkazů Redis na portálu Azure.
* Můžete také použít nástroje příkazového řádku Redis. Používat, proveďte následující kroky:
* Stažení [nástroje příkazového řádku Redis](https://github.com/MSOpenTech/redis/releases/).
* Připojení k mezipaměti pomocí `redis-cli.exe`. Předejte mezipaměti koncový bod pomocí -h přepínač a klíč pomocí - a jak je znázorněno v následujícím příkladu:
* `redis-cli -h <redis cache name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Nástroje příkazového řádku Redis nefungují s portem SSL, ale můžete pomocí nástroje, jako `stunnel` bezpečně připojit nástroje podle pokynů v SSL port [uvedení ASP.NET poskytovatele stavu relace pro Redis Preview Verze](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) příspěvku na blogu.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Proč Azure Redis Cache nemá webu MSDN knihovny tříd jako část jinými službami Azure?
Microsoft Azure Redis Cache je založená na populární open source Redis Cache a je přístupný pomocí široké škály [Redis klienti](http://redis.io/clients) pro řadě programovacích jazyků. Každý klient má svou vlastní rozhraní API, které provádí volání pro instanci mezipaměti Redis pomocí [Redis příkazy](http://redis.io/commands).

Protože každého klienta se liší, není jeden centralizované třída odkaz na webu MSDN a každý klient udržuje vlastní referenční dokumentaci k nástroji. Kromě referenční dokumentaci k nástroji existuje několik kurzy znázorňující postup Začínáme s Azure Redis Cache pomocí různých jazycích a klientů mezipaměti. Tyto kurzy přístup, najdete v části [použití Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) a klikněte na požadovaný jazyk od jazyka přepínači v horní části článku.

### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Můžete použít Azure Redis Cache jako mezipaměť relace PHP?
Ano, použití Azure Redis Cache jako mezipaměť relace PHP, zadejte připojovací řetězec k vaší instanci Azure Redis Cache v `session.save_path`.

> [!IMPORTANT]
> Při použití Azure Redis Cache jako mezipaměť relace PHP, je nutné, aby adresa URL kódování klíč zabezpečení použít pro připojení k mezipaměti, jak je znázorněno v následujícím příkladu:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Pokud není klíč kódovaná adresou URL, se může zobrazit výjimka zprávu jako: `Failed to parse session.save_path`
>
>

Další informace o používání Redis Cache jako mezipaměť relace PHP s klientem PhpRedis najdete v tématu [obslužné rutiny PHP relace](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Jaké jsou databáze Redis?

Databáze redis jsou právě logického oddělení dat v rámci stejné instance Redis. Velikost mezipaměti paměti jsou sdílena mezi všechny databáze a skutečný paměti, že spotřeba na danou databázi závisí na klíče nebo hodnotami uloženými v databázi. Například C6 mezipaměti má 53 GB paměti. Můžete uvést všechny 53 GB do jedné databáze nebo je možné ho rozdělit mezi více databází. 

> [!NOTE]
> Při použití mezipaměť Azure Redis Cache Premium s povoleným clusteringem, je k dispozici pouze databázi 0. Toto omezení vnitřní omezení Redis a není specifická pro Azure Redis Cache. Další informace najdete v tématu [muset provést jakékoli změny Moje aplikace klienta použít clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Pokud by měl povolit port bez SSL pro připojení k Redis?
Redis serveru nativně nepodporuje protokol SSL, ale v Azure Redis Cache. Pokud se připojujete k Azure Redis Cache a váš klient podporuje protokol SSL, jako je StackExchange.Redis, měli byste použít protokol SSL.

>[!NOTE]
>Port bez SSL je ve výchozím nastavení pro nové instance služby Azure Redis Cache zakázán. Pokud váš klient nepodporuje protokol SSL, pak je nutné povolit port bez SSL podle pokynů v [přístup k portům](cache-configure.md#access-ports) části [konfigurace mezipaměti ve službě Azure Redis Cache](cache-configure.md) článku.
>
>

Nástroje, jako redis `redis-cli` nefungují s portem SSL, ale můžete pomocí nástroje, jako `stunnel` bezpečně připojit nástroje SSL port podle pokynů v [uvedení ASP.NET poskytovatele stavu relace pro Redis Verze Preview](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) příspěvku na blogu.

Pokyny ke stahování nástroje Redis, najdete v článku [jak můžete spouštět příkazy Redis?](#cache-commands) části.

### <a name="what-are-some-production-best-practices"></a>Jaké jsou některé z osvědčených postupů produkční?
* [Osvědčené postupy StackExchange.Redis](#stackexchangeredis-best-practices)
* [Konfigurace a koncepty](#configuration-and-concepts)
* [Testování výkonu](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Osvědčené postupy StackExchange.Redis
* Nastavit `AbortConnect` na hodnotu false, nechte ConnectionMultiplexer, automaticky se znovu nepřipojí. [Podrobnosti najdete v](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Znovu použít ConnectionMultiplexer – nevytvářejte novou pro každý požadavek. `Lazy<ConnectionMultiplexer>` Vzor [tady uvedené](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) se doporučuje.
* Redis funguje nejlépe s menší hodnoty, takže zvažte dělení, jsou větší data do více klíčů. V [toto pojednání Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb se považuje za velké. Čtení [v tomto článku](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) pro problém s příklad, který může být způsobeno velké hodnoty.
* Konfigurace vaší [nastavení fondu podprocesů](#important-details-about-threadpool-growth) aby se zabránilo vypršení časových limitů.
* Použijte alespoň connectTimeout výchozí 5 sekund. Tento interval, získáte dostatek času StackExchange.Redis znovu navázat připojení, v případě blip sítě.
* Pamatujte na výkon nákladů spojených se různé operace, které používáte. Například `KEYS` příkaz je operace O(n) a je nutno. [Redis.io lokality](http://redis.io/commands/) obsahuje podrobnosti kolem složitost čas pro každou operaci, kterou podporuje. Klikněte na každý příkaz zobrazíte složitost pro každou operaci.

#### <a name="configuration-and-concepts"></a>Konfigurace a koncepty
* Použijte Standard nebo Premium vrstvy pro produkční systémy. Úroveň Basic je systém jednoho uzlu se žádná data replikace a žádné SLA. Rovněž použijte alespoň C1 mezipaměti. C0 mezipamětí jsou obvykle používány pro scénáře vývoje/testování jednoduché.
* Mějte na paměti, že je Redis **v paměti** úložišti. Čtení [v tomto článku](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) tak, že jste si vědomi scénáře, kde může dojít ke ztrátě dat.
* Vývoj systému tak, aby ji může zpracovat připojení blips [z důvodu opravy a převzetí služeb při selhání](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testování výkonu
* Začněte tím, že pomocí `redis-benchmark.exe` podívat pro možné propustnost před zápisem vlastního výkonu testy. Protože `redis-benchmark` nepodporuje protokol SSL, musíte [povolit port bez SSL prostřednictvím portálu Azure](cache-configure.md#access-ports) předtím, než spustíte test. Příklady najdete v tématu [jak mohu srovnávací test a testování výkonu Moje mezipaměti?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Klient použít pro testování virtuální počítač musí být ve stejné oblasti jako instance mezipaměti Redis.
* Doporučujeme používat pro vašeho klienta, dokud budou mít lepší hardwaru a získat nejlepších výsledků dosáhnete pomocí řady Dv2 virtuálních počítačů.
* Zajistěte, aby váš klient zvolíte virtuální počítač má alespoň tolik výpočetních a testování schopnosti šířky pásma jako mezipaměť.
* Povolte VRSS v klientském počítači. Pokud jste v systému Windows. [Podrobnosti najdete v](https://technet.microsoft.com/library/dn383582.aspx).
* Úroveň Premium Redis instance mít lepší sítě latence a propustnosti vzhledem k tomu, že jsou spuštěny v lepší hardwaru pro procesoru a sítě.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jaké jsou některé aspekty při pomocí běžných příkazů Redis?
* Nespouštějte určité Redis příkazy, které trvat dlouhou dobu pro dokončení bez porozumění vlivu těchto příkazů.
  * Například se nespustí [klíče](http://redis.io/commands/keys) příkazů v produkčním prostředí, protože to může trvat dlouhou dobu vrátit v závislosti na počet klíčů. Redis je server jednovláknové a zpracovává příkazy jeden najednou. Pokud máte další příkazy vydané po klíče, nebudou zpracovány až Redis zpracuje příkaz klíče. [Redis.io lokality](http://redis.io/commands/) obsahuje podrobnosti kolem složitost čas pro každou operaci, kterou podporuje. Klikněte na každý příkaz zobrazíte složitost pro každou operaci.
* Velikosti klíče - použít malé klíč/hodnota nebo velké klíč/hodnota? Obecně platí závisí na scénáři. Pokud váš scénář vyžaduje větší klíčů, můžete upravit ConnectionTimeout a opakujte hodnot a upravit logika opakovaných pokusů. Z hlediska serveru Redis jsou dodržovány menší hodnoty do mají lepší výkon.
* Tyto aspekty nemáte znamenat vyšší hodnoty nelze uložit do Redis; je potřeba vědět následující aspekty. Latence bude vyšší. Pokud máte jednu sadu dat, která je větší a ten, který je menší, můžete použít několik instancí ConnectionMultiplexer, každý nakonfigurován s jinou sadu hodnot časového limitu a zkuste to znovu, jak je popsáno v předchozí [co dělat StackExchange.Redis Možnosti konfigurace provést](#cache-configuration) části.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak můžete otestovat a testování výkonu Moje mezipaměti?
* [Povolte diagnostiku mezipaměti](cache-how-to-monitor.md#enable-cache-diagnostics), abyste mohli [monitorovat](cache-how-to-monitor.md) stav svojí mezipaměti. Metriky lze zobrazit na webu Azure Portal a můžete je také [stáhnout a revidovat](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) pomocí nástrojů dle vašeho výběru.
* Redis benchmark.exe můžete zátěžový test serveru Redis.
* Zajistěte, aby zatížení testování klienta a mezipaměť Redis ve stejné oblasti.
* Použijte redis cli.exe a monitorovat mezipaměti pomocí příkazu informace.
* Pokud zatížení způsobuje fragmentace velkého množství paměti, měli škálovat na větší velikost mezipaměti.
* Pokyny ke stahování nástroje Redis, najdete v článku [jak můžete spouštět příkazy Redis?](#cache-commands) části.

Následující příkazy uveďte příklad použití redis benchmark.exe. Pro přesné výsledky spusťte tyto příkazy z virtuálního počítače ve stejné oblasti jako vaše mezipaměť.

* Požadavky testovacího zřetězena SET pomocí datovou část 1 kB

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Test zřetězena získat požadavků pomocí datovou část 1 kB.
  Poznámka: Spustit sadu testů uvedené výše nejprve k naplnění mezipaměti

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Důležité podrobnosti o fondu růst
Zachovalo CLR má dva typy vláken - "Pracovník" a "Portu dokončení vstupně-výstupních operací" (neboli portu IOCP) vláken.

* Pracovní vlákna se používají při pro takové věci, jako zpracování `Task.Run(…)` nebo `ThreadPool.QueueUserWorkItem(…)` metody. Tyto vláken používají různé součásti v modulu CLR taky, když pracovní musí provést na vlákna na pozadí.
* Vlákna portu IOCP se používají při asynchronní vstupně-výstupní operace se stane (např. čtení ze sítě).

Fond vláken poskytuje nové pracovních vláken nebo vláken dokončení vstupně-výstupních operací na vyžádání (bez žádné omezení) dokud nedosáhne nastavení "Minimální" pro každý typ přístup z více vláken. Ve výchozím nastavení minimální počet vláken, nastavena na počet procesorů v systému.

Jakmile se počet vláken, existující (zaneprázdněn) dotkne "minimální" počet vláken, zachovalo omezení rychlost, jakou se vloží nový vláken na jedno vlákno na jeden 500 milisekund Obvykle se váš systém získá shluku práce portu IOCP přístup z více vláken, která potřebuje, zpracována bude tento pracovní velmi rychle. Ale pokud shluků práce je větší než nakonfigurované nastavení "Minimální", budou existovat některé zpoždění při zpracování některé úkoly, jako zachovalo čeká na jednu ze dvou akcí provést.

1. Existující vlákno neuvolní zpracovat práce.
2. Žádné existující vlákno neuvolní pro 500ms, takže se vytvoří nové vlákno.

V zásadě platí znamená to, že když počet vytížených vláken je vyšší než minimální vláken, pravděpodobně platíte 500ms zpoždění dříve, než je pomocí aplikace síťový provoz. Je také důležité si uvědomit, že když existující vlákno zůstává nečinné po dobu delší než 15 sekund (podle I zapamatovat), se vyčistí a tento cyklus růstu a redukce, můžete opakovat.

Pokud se podíváme na příklad chybovou zprávu z StackExchange.Redis (sestavení 1.0.450 nebo novější), zobrazí se, že nyní vytiskne statistiky fondu (viz níže portu IOCP a pracovní podrobnosti).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

V předchozím příkladu uvidíte, že pro přístup z více vláken portu IOCP jsou 6 zaneprázdněn vláken a systém je nakonfigurován umožňující 4 minimální počet vláken činí. V tomto případě klient by pravděpodobně viděli jste dva zpoždění 500 ms protože 6 > 4.

Všimněte si, že můžete StackExchange.Redis Pokud získá omezuje růst portu IOCP nebo pracovních vláken dosáhl vypršení časových limitů.

### <a name="recommendation"></a>Doporučení
Zvážení všech informací, důrazně doporučujeme, aby zákazníci nastavte hodnotu minimální konfigurace pro portu IOCP a pracovních vláken na něco větší než je výchozí hodnota. Jsme nelze udělit univerzálně pokyny k co tato hodnota by měla být hodnota pravé pro jednu aplikaci proto bude příliš vysokou a nízkým pro jinou aplikaci. Tato nastavení mohou ovlivnit výkon dalších částí složitých aplikací, takže každý zákazník musí a systém doladit toto nastavení pro jejich konkrétní potřeby. Je dobré počáteční se 200 nebo 300, pak otestovat a upravit podle potřeby.

Postup konfigurace tohoto nastavení:

* V technologii ASP.NET, použijte ["minIoThreads" nebo "minWorkerThreads" nastavení konfigurace] [ "minIoThreads" configuration setting] pod `<processModel>` konfiguračního prvku v souboru web.config. Pokud používáte uvnitř weby Azure, toto nastavení nebude vystavena prostřednictvím možnosti konfigurace. Ale nyní byste měli stále mít ke konfiguraci tohoto nastavení prostřednictvím kódu programu (viz níže) z metodu Application_Start v global.asax.cs.

  > [!NOTE] 
  > Hodnota zadaná v tomto elementu konfigurace je *za jádra* nastavení. Například pokud máte 4jádrový počítač a chcete nastavení minIOThreads na 200 za běhu, byste použili `<processModel minIoThreads="50"/>`.
  >

* Mimo technologii ASP.NET a soubor global.asax weby Azure, použijte [ThreadPool.SetMinThreads (...)] (https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) Rozhraní API.

  > [!NOTE]
  > Hodnotu zadanou pomocí toto rozhraní API je globální nastavení, které mají vliv na celý domény aplikace. Pokud máte 4jádrový počítač a chcete nastavit minWorkerThreads a minIOThreads na 50 za využití procesoru při běhu, využije ThreadPool.SetMinThreads (200, 200).

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Povolit serveru globálního katalogu získat další propustnosti na straně klienta při používání StackExchange.Redis
Povolení serveru globálního katalogu, můžete optimalizovat klienta a poskytují lepší výkon a propustnost při používání StackExchange.Redis. Další informace o serveru globálního katalogu a jak ji povolit najdete v následujících článcích:

* [Chcete-li povolit serveru globálního katalogu](https://msdn.microsoft.com/library/ms229357.aspx)
* [Základy kolekce paměti](https://msdn.microsoft.com/library/ee787088.aspx)
* [Uvolňování paměti a výkon](https://msdn.microsoft.com/library/ee851764.aspx)


### <a name="performance-considerations-around-connections"></a>Faktory ovlivňující výkon kolem připojení

Každá cenová úroveň má jiné limity pro připojení klientů, paměti a šířky pásma. Při každém velikost mezipaměti umožňuje *až* určitý počet připojení, každé připojení k Redis má režie spojená s ním spojená. Příkladem takových režie může být využití procesoru a paměti v důsledku šifrování pomocí protokolu TLS/SSL. Připojení maximální limit pro velikost daného mezipaměti předpokládá lehkým načíst mezipaměti. Pokud načíst z režijní náklady na připojení *plus* zatížení z klientské operace překročí kapacitu pro systém, mezipaměti můžete mít problémy kapacitu, i pokud nebyl překročen limit připojení pro aktuální velikost mezipaměti.

Další informace o omezeních různá připojení pro každou vrstvu najdete v tématu [cenách Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/). Další informace o připojení a další výchozí konfigurace najdete v tématu [Redis výchozí konfigurace serveru](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Jak monitorovat stav a výkon Moje mezipaměti?
Instance služby Microsoft Azure Redis Cache lze ji monitorovat na [portál Azure](https://portal.azure.com). Můžete zobrazit metriky, připnout grafy metriky na úvodní panel, přizpůsobení datum a čas rozsah monitorování grafy, přidat a metriky odebrání grafy a nastavit upozornění, pokud jsou splněny určité podmínky. Další informace najdete v tématu [monitorování Azure Redis Cache](cache-how-to-monitor.md).

Mezipaměť Redis **prostředků nabídky** také obsahuje několik nástrojů pro monitorování a řešení potíží s své mezipaměti.

* **Diagnostika a řešení problémů** poskytuje informace o běžných problémech a strategie pro jejich řešení.
* **Stav prostředku** sleduje prostředku a zjistíte, zda je spuštěn podle očekávání. Další informace o službě stavu prostředků Azure najdete v tématu [přehled stavu prostředků Azure](../resource-health/resource-health-overview.md).
* **Nová žádost o podporu** nabízí možnosti pro otevření žádosti o podporu pro mezipaměť.

Tyto nástroje vám umožňují sledovat stav vaší instance služby Azure Redis Cache a vám pomohou při správě aplikace ukládání do mezipaměti. Další informace najdete v sekci "Podpory a řešení potíží nastavení" [postup konfigurace Azure Redis Cache](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Proč se zobrazuje časové limity?
Časové limity dojde v klientovi, který můžete použít ke komunikaci s Redis. Příkaz je odeslán na serveru Redis, příkaz zařazen do fronty a nakonec převezme příkaz serveru Redis a provede ji. Ale klient může vypršení časového limitu během tohoto procesu a pokud ano výjimku se vyvolá při volání straně. Další informace o řešení potíží s vypršení časového limitu, najdete v části [řešení potíží s klientskou](cache-how-to-troubleshoot.md#client-side-troubleshooting) a [výjimkám časového limitu StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Proč byl klient odpojen z mezipaměti?
Níže jsou uvedeny některé běžným důvodem pro odpojení mezipaměti.

* Způsobí, že na straně klienta
  * Klientská aplikace byla znovu nasazena.
  * Klientská aplikace provedla operaci škálování.
    * V případě cloudové služby nebo webové aplikace může to být způsobeno automatické škálování.
  * Změnit síťovou vrstvou na straně klienta.
  * Přechodné chyby v klientovi nebo na uzlech sítě mezi klientem a serverem došlo k chybě.
  * Bylo dosaženo prahové limity šířky pásma.
  * Procesor vázaný operace trvalo příliš dlouho dokončení.
* Způsobí, že na straně serveru
  * U standardní mezipaměti nabídky inicializovat službu Azure Redis Cache selhání z primárního uzlu sekundárního uzlu.
  * Azure byla opravy instance, kde byla nasazena do mezipaměti
    * To může být pro aktualizace serveru Redis nebo Obecná údržba virtuálního počítače.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Kterou z variant Mezipaměti Azure si mám vybrat?
> [!IMPORTANT]
> Podle poslední rok [oznámení](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), služby Azure spravované mezipaměti Service a Azure v roli Cache **mít bylo vyřazeno z provozu** 30. listopadu 2016. Naše doporučení je použití [Azure Redis Cache](https://azure.microsoft.com/services/cache/). Informace o migraci naleznete v tématu [migrace ze spravované služby mezipaměti Azure Redis Cache](cache-migrate-to-redis.md).
>
>

### <a name="azure-redis-cache"></a>Azure Redis Cache
Azure Redis Cache je všeobecně dostupná v velikostí až do 53 GB a má SLA 99,9 % dostupnosti. Nové [úroveň premium](cache-premium-tier-intro.md) nabízí velikosti až 530 GB a podpora pro clustering, virtuální síť a trvalost s SLA 99,9 %.

Azure Redis Cache vám umožňuje používat zabezpečené, vyhrazené mezipaměti Redis spravované microsoftem zákazníků. S touto nabídkou získáte využívat bohaté sadě funkcí a ekosystém poskytované Redis a spolehlivého hostování a monitorování od Microsoftu.

Na rozdíl od tradičních mezipaměti, která se týkají jenom s páry klíč hodnota je pro jeho vysokou původce datové typy Redis. Redis také podporuje běh atomické operací pro tyto typy, jako je připojení k řetězec; přírůstkovou hodnotou v hodnotu hash; vkládání do seznamu; výpočetní průnik sady, union a rozdíl; nebo člen s nejvyšším pořadím v seřazené sady. Další funkce zahrnují podporu pro transakce, pub nebo sub, Lua skriptování, klíče s omezenou time-to-live a nastavení konfigurace, aby se chovat spíše jako tradiční mezipaměti Redis.

Další aspekt klíče na úspěšné provedení Redis je v pořádku, živoucí s otevřeným zdrojem ekosystému vytvořené kolem něj. To se projeví v sadě různých klientů Redis k dispozici v několika jazycích. Tento ekosystém a širokou škálu klientům povolit Azure Redis Cache má být používána skoro všechny úlohy, jež by sestavení v rámci Azure.

Další informace o seznámení se s Azure Redis Cache najdete v tématu [postup použití Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) a [dokumentace k Azure Redis Cache](index.md).

### <a name="managed-cache-service"></a>Spravované služby mezipaměti
[Spravované mezipaměti služby vyřazenou 30. listopadu 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Archivované dokumentaci najdete v tématu [archivovaný spravované dokumentaci mezipaměti služby](https://msdn.microsoft.com/library/azure/dn386094.aspx).

### <a name="in-role-cache"></a>Mezipaměť hostovaná v instanci role
[Mezipaměť hostovaná v instanci Role vyřazenou 30. listopadu 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Archivované dokumentaci najdete v tématu [archivovaný dokumentace mezipaměť hostovaná v instanci Role](https://msdn.microsoft.com/library/azure/dn386103.aspx).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
