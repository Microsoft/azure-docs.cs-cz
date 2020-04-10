---
title: Nejčastější dotazy ke službě Azure Cache for Redis
description: Seznamte se s odpověďmi na běžné otázky, vzorce a osvědčené postupy pro Azure Cache for Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 6ba292850c057284fff265c8a77386d21374942a
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010218"
---
# <a name="azure-cache-for-redis-faq"></a>Nejčastější dotazy ke službě Azure Cache for Redis
Seznamte se s odpověďmi na běžné otázky, vzory a osvědčené postupy pro Azure Cache for Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Co když moje otázka není zodpovězena tady?
Pokud zde vaše otázka není uvedena, dejte nám vědět a my vám pomůžeme najít odpověď.

* Můžete odeslat dotaz v komentářích na konci tohoto faq a komunikovat s týmem Azure Cache a dalšími členy komunity o tomto článku.
* Chcete-li oslovit širší okruh uživatelů, můžete odeslat otázku na [fóru MSDN Azure Cache](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) a zapojit se do týmu Azure Cache a dalších členů komunity.
* Pokud chcete podat žádost o funkci, můžete odeslat své požadavky a nápady do [služby Azure Cache for Redis User Voice](https://feedback.azure.com/forums/169382-cache).
* Můžete nám taky poslat e-mail na adresu [Azure Cache External Feedback](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Základy Azure Cache for Redis
Časté otázky v této části pokrývají některé základy Azure Cache pro Redis.

* [Co je Azure Cache for Redis?](#what-is-azure-cache-for-redis)
* [Jak můžu začít s Azure Cache pro Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

Následující časté dotazy popisují základní koncepty a otázky týkající se Azure Cache for Redis a jsou zodpovězeny v jedné z dalších částí nejčastějších dotazů.

* [Jakou mezipaměť Azure pro Redis mám použít?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Jakou mezipaměť Azure pro klienty Redis umíte používat?](#what-azure-cache-for-redis-clients-can-i-use)
* [Existuje místní emulátor pro Azure Cache pro Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak lze sledovat stav a výkon mezipaměti?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Nejčastější dotazy k plánování
* [Jakou mezipaměť Azure pro Redis mám použít?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Výkon Azure Cache for Redis](#azure-cache-for-redis-performance)
* [V jaké oblasti mám najít svou mezipaměť?](#in-what-region-should-i-locate-my-cache)
* [Jak se mi účtují Azure Cache pro Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Můžu používat Azure Cache for Redis s Azure Government Cloud, Azure China Cloud nebo Microsoft Azure Germany?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Časté otázky k rozvoji
* [K čemu se dělají možnosti konfigurace stackexchange.redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Jakou mezipaměť Azure pro klienty Redis umíte používat?](#what-azure-cache-for-redis-clients-can-i-use)
* [Existuje místní emulátor pro Azure Cache pro Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak lze spustit příkazy Redis?](#how-can-i-run-redis-commands)
* [Proč azure cache pro redis nemá odkaz na knihovnu tříd MSDN jako některé jiné služby Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Můžu použít Azure Cache pro Redis jako mezipaměť relací PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Co jsou databáze Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Nejčastější dotazy k zabezpečení
* [Kdy mám povolit port non-TLS/SSL pro připojení k Redisu?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Časté otázky k výrobě
* [Jaké jsou některé výrobní osvědčené postupy?](#what-are-some-production-best-practices)
* [Jaké jsou některé důležité informace při použití běžných příkazů Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak mohu porovnat a otestovat výkon mezipaměti?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Důležité podrobnosti o růstu Fondu vláken](#important-details-about-threadpool-growth)
* [Povolit server GC získat větší propustnost na straně klienta při použití StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Aspekty výkonu kolem připojení](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorování a řešení problémů s nejčastějšími dotazy
Časté otázky v této části se týkají běžných otázek týkajících se monitorování a řešení problémů. Další informace o monitorování a řešení potíží s instancemi Azure Cache for Redis najdete v tématu [Monitorování mezipaměti Azure pro Redis](cache-how-to-monitor.md) a v různých příručkách pro řešení potíží.

* [Jak lze sledovat stav a výkon mezipaměti?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Proč se mi zobrazují timeouty?](#why-am-i-seeing-timeouts)
* [Proč byl můj klient odpojen od mezipaměti?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Nejčastější dotazy k nabídce předchozí mezipaměti
* [Která nabídka Azure Cache je pro mě ta pravá?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Co je Azure Cache for Redis?
Azure Cache for Redis je založen na oblíbeném open source softwaru [Redis](https://redis.io/). Poskytuje vám přístup k zabezpečené, vyhrazené mezipaměti Azure pro Redis, kterou spravuje Microsoft a je přístupná z libovolné aplikace v rámci Azure. Podrobnější přehled najdete na stránce produktu [Azure Cache for Redis](https://azure.microsoft.com/services/cache/) na Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Jak můžu začít s Azure Cache pro Redis?
Existuje několik způsobů, jak můžete začít s Azure Cache pro Redis.

* Můžete se podívat na jeden z našich výukových programů, které jsou k dispozici pro [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)a [Python](cache-python-get-started.md).
* Můžete [sledovat, jak vytvářet vysoce výkonné aplikace pomocí mezipaměti Microsoft Azure pro Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Můžete se podívat na dokumentaci klienta pro klienty, které odpovídají vývojový jazyk vašeho projektu zobrazíte, jak používat Redis. Existuje mnoho klientů Redis, které lze použít s Azure Cache pro Redis. Seznam klientů Redis naleznete [https://redis.io/clients](https://redis.io/clients)v tématu .

Pokud ještě nemáte účet Azure, můžete:

* [Otevřít bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Získáte kredity, které můžete použít k vyzkoušení placených služeb Azure. I po vyčerpání kreditů si můžete účet ponechat a používat bezplatné funkce a služby Azure.
* [Aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Jakou mezipaměť Azure pro Redis mám použít?
Každá služba Azure Cache for Redis nabízí různé úrovně **velikosti**, **šířky pásma**, **vysokou dostupnost**a možnosti **s la.**

Níže jsou uvedeny důležité informace pro výběr nabídky mezipaměti.

* **Paměť:** Úrovně Basic a Standard nabízejí 250 MB – 53 GB. Úroveň Premium nabízí až 1,2 TB (jako cluster) nebo 120 GB (bez clusteru). Další informace najdete v [tématu Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Výkon sítě:** Pokud máte zatížení, které vyžaduje vysokou propustnost, úroveň Premium nabízí větší šířku pásma ve srovnání se standardem nebo basicem. Také v rámci každé vrstvy větší velikost mezipaměti mají větší šířku pásma z důvodu základní virtuální počítače, který hostuje mezipaměť. Další informace naleznete v [následující tabulce](#cache-performance).
* **Propustnost**: Úroveň Premium nabízí maximální dostupnou propustnost. Pokud server mezipaměti nebo klient dosáhne omezení šířky pásma, můžete obdržet časové limity na straně klienta. Další informace najdete v následující tabulce.
* **Vysoká dostupnost/sla:** Azure Cache for Redis zaručuje, že mezipaměť Standard/Premium je k dispozici alespoň v 99,9 % času. Další informace o naší sla najdete v [tématu Azure Cache for Redis Pricing](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). SLA zahrnuje pouze připojení ke koncovým bodům mezipaměti. SLA nepokrývá ochranu před ztrátou dat. Doporučujeme používat funkci trvalost dat Redis ve vrstvě Premium ke zvýšení odolnosti proti ztrátě dat.
* **Trvalost redis dat:** Úroveň Premium umožňuje zachovat data mezipaměti v účtu Azure Storage. V mezipaměti Basic/Standard jsou všechna data uložena pouze v paměti. Základní problémy s infrastrukturou může mít za následek potenciální ztrátu dat. Doporučujeme používat funkci trvalost dat Redis ve vrstvě Premium ke zvýšení odolnosti proti ztrátě dat. Azure Cache for Redis nabízí možnosti RDB a AOF (již brzy) v trvalosti Redis. Další informace naleznete v tématu [Jak nakonfigurovat trvalost pro premium Azure Cache pro Redis](cache-how-to-premium-persistence.md).
* **Cluster Redis**: Chcete-li vytvořit mezipaměti větší než 120 GB nebo pro prolomení dat mezi více uzly Redis, můžete použít clustering Redis, který je k dispozici na úrovni Premium. Každý uzel se skládá z dvojice mezipaměti primární repliky pro vysokou dostupnost. Další informace naleznete v tématu [Jak nakonfigurovat clustering pro premium Azure Cache pro Redis](cache-how-to-premium-clustering.md).
* **Rozšířené zabezpečení a izolace sítě:** Nasazení virtuální sítě Azure (VNET) poskytuje rozšířené zabezpečení a izolaci pro vaši mezipaměť Azure pro Redis, stejně jako podsítě, zásady řízení přístupu a další funkce pro další omezení přístupu. Další informace najdete v tématu [Konfigurace podpory virtuální sítě pro mezipaměť Premium Azure pro redis](cache-how-to-premium-vnet.md).
* **Konfigurace redisu**: Na úrovni Standard i Premium můžete nakonfigurovat redis pro oznámení keyspace.
* **Maximální počet připojení klientů**: Úroveň Premium nabízí maximální počet klientů, kteří se mohou připojit k redisu, s vyšším počtem připojení pro větší mezipaměti. Clustering nezvyšuje počet připojení dostupných pro clusterovnou mezipaměť. Další informace najdete v tématu [Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Vyhrazené jádro pro Redis Server:** Na úrovni Premium mají všechny velikosti mezipaměti vyhrazené jádro pro Redis. V úrovních Basic/Standard mají velikost C1 a vyšší vyhrazené jádro pro server Redis.
* **Redis je jednovláknový,** takže mít více než dvě jádra neposkytuje další výhody oproti tomu, že máte pouze dvě jádra, ale větší velikosti virtuálních počítačů mají obvykle větší šířku pásma než menší velikosti. Pokud server mezipaměti nebo klient dosáhne omezení šířky pásma, obdržíte časové limity na straně klienta.
* **Vylepšení výkonu**: Mezipaměti na úrovni Premium se nasazují na hardware, který má rychlejší procesory, což poskytuje lepší výkon ve srovnání s úrovní Basic nebo Standard. Mezipaměť úrovně Premium mají vyšší propustnost a nižší latenci.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Výkon Azure Cache for Redis
V následující tabulce jsou uvedeny maximální hodnoty šířky pásma `redis-benchmark.exe` pozorované při testování různých velikostí mezipamětí Standard a Premium pomocí z virtuálního počítače IaaS proti koncovému bodu Azure Cache for Redis. Pro propustnost TLS redis benchmark se používá s stunnel pro připojení ke koncovému bodu Azure Cache pro Redis.

>[!NOTE] 
>Tyto hodnoty nejsou zaručeny a neexistuje žádná sla pro tato čísla, ale by měla být typické. Měli byste načíst test vlastní aplikace k určení správné velikosti mezipaměti pro vaši aplikaci.
>Tato čísla se mohou měnit, protože pravidelně zveřejňujeme novější výsledky.
>

Z této tabulky můžeme vyvodit následující závěry:

* Propustnost pro mezipaměti, které mají stejnou velikost, je vyšší ve vrstvě Premium ve srovnání s úrovní Standard. Například s 6 GB mezipaměti, propustnost P1 je 180 000 požadavků za sekundu (RPS) ve srovnání s 100 000 RPS pro C3.
* S Redis clustering, propustnost zvyšuje lineárně jako zvýšení počtu úlomků (uzlů) v clusteru. Pokud například vytvoříte cluster P4 s 10 úlomky, je dostupná propustnost 400 000 * 10 = 4 miliony RPS.
* Propustnost pro větší velikosti klíčů je vyšší ve vyšší úrovni Premium ve srovnání se standardní úrovní.

| Cenová úroveň | Velikost | Procesorová jádra | Dostupná šířka pásma | Velikost hodnoty 1 KB | Velikost hodnoty 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Standardní velikosti mezipaměti** | | |**Megabity za sekundu (Mb/s) / Megabajty za sekundu (MB/s)** |**Požadavky za sekundu (RPS) Non-SSL** |**Požadavky za sekundu (RPS) SSL** |
| C0 | 250 MB | Sdílená | 100 / 12.5  |  15 000 |   7 500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100 000 |  90,000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60 000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Prémiové velikosti mezipaměti** | |**Jádra procesoru na střep** | **Megabity za sekundu (Mb/s) / Megabajty za sekundu (MB/s)** |**Požadavky za sekundu (RPS) Non-SSL, na střep** |**Požadavky za sekundu (RPS) SSL, na střep** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400 000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400 000 | 373,000 |

Pokyny k nastavení stunnel nebo stahování nástrojů Redis, jako `redis-benchmark.exe`je například , naleznete v části Jak lze spustit [příkazy Redis?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>V jaké oblasti mám najít svou mezipaměť?
Pokud máte nejlepší výkon a nejnižší latenci, vyhledejte azure cache pro Redis ve stejné oblasti jako klientská aplikace mezipaměti.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Jak se mi účtují Azure Cache pro Redis?
Ceny Azure Cache for Redis jsou [tady](https://azure.microsoft.com/pricing/details/cache/). Na stránce s cenami jsou uvedeny ceny jako hodinová sazba. Mezipaměti se účtují za minutu od okamžiku vytvoření mezipaměti až do doby, kdy je mezipaměť odstraněna. Neexistuje žádná možnost pro zastavení nebo pozastavení fakturace mezipaměti.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Můžu používat Azure Cache for Redis s Azure Government Cloud, Azure China Cloud nebo Microsoft Azure Germany?
Ano, Azure Cache for Redis je dostupná v Azure Government Cloud, Azure China 21Vianet Cloud a Microsoft Azure Germany. Adresy URL pro přístup a správu Azure Cache pro Redis se v těchto cloudech liší ve srovnání s Azure Public Cloud.

| Cloud   | Přípona DNS pro Redis            |
|---------|---------------------------------|
| Public  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Německo | *.redis.cache.cloudapi.de       |
| Čína   | *.redis.cache.chinacloudapi.cn  |

Další informace o aspektech při použití Azure Cache pro Redis s jinými cloudy najdete v následujících odkazech.

- [Azure Government Databases – Azure Cache for Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud – Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

Informace o používání Azure Cache for Redis s PowerShellem v Azure Government Cloud, Azure China 21Vianet Cloud a Microsoft Azure Germany najdete v tématu [Jak se připojit k jiným cloudům – Azure Cache for Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>K čemu se dělají možnosti konfigurace stackexchange.redis?
StackExchange.Redis má mnoho možností. V této části se hovoří o některých běžných nastaveních. Podrobnější informace o možnostech StackExchange.Redis naleznete v [tématu Konfigurace StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Popis | Doporučení |
| --- | --- | --- |
| AbortOnConnectFail |Pokud je nastavena hodnota true, připojení se po selhání sítě znovu nepřipojí. |Nastavte na false a nechte StackExchange.Redis znovu automaticky připojit. |
| ConnectRetry |Počet opakování pokusů o připojení během počátečního připojení. |Pokyny naleznete v následujících poznámkách. |
| ConnectTimeout |Časový čas v ms pro operace připojení. |Pokyny naleznete v následujících poznámkách. |

Obvykle jsou postačující výchozí hodnoty klienta. Možnosti můžete doladit na základě vašeho pracovního vytížení.

* **Opakování**
  * Pro ConnectRetry a ConnectTimeout obecné pokyny je selhání rychle a opakujte akci. Tyto pokyny jsou založeny na vaší pracovní zátěži a na tom, kolik času v průměru trvá, než klient vydá příkaz Redis a obdrží odpověď.
  * Nechte StackExchange.Redis automaticky znovu připojit místo kontroly stavu připojení a opětovné připojení sami. **Nepoužívejte vlastnost ConnectionMultiplexer.IsConnected**.
  * Snowballing - někdy můžete narazit na problém, kdy se opakujete a opakuje težce a nikdy se nezotavíte. Pokud dojde k sněhové kouli, měli byste zvážit použití exponenciálního algoritmu opakování backoff, jak je popsáno v [obecných pokynech opakování](../best-practices-retry-general.md) publikovaných skupinou Microsoft Patterns & Practices.
  
* **Hodnoty časového opojení**
  * Zvažte své zatížení a nastavte hodnoty odpovídajícím způsobem. Pokud ukládáte velké hodnoty, nastavte časový čas na vyšší hodnotu.
  * Nastavte `AbortOnConnectFail` na false a nechte StackExchange.Redis znovu připojit za vás.
  * Pro aplikaci použijte jednu instanci ConnectionMultiplexer. LazyConnection můžete použít k vytvoření jedné instance, která je vrácena vlastností Connection, jak je znázorněno v [části Připojit ke mezipaměti pomocí třídy ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Nastavte `ConnectionMultiplexer.ClientName` vlastnost na jedinečný název instance aplikace pro diagnostické účely.
  * Pro `ConnectionMultiplexer` vlastní úlohy použijte více instancí.
      * Tento model můžete sledovat, pokud máte různé zatížení v aplikaci. Příklad:
      * Můžete mít jeden multiplexer pro práci s velkými klíči.
      * Můžete mít jeden multiplexer pro práci s malými klíči.
      * Můžete nastavit různé hodnoty pro časové limity připojení a logiku opakování pro každý ConnectionMultiplexer, který používáte.
      * Nastavte `ClientName` vlastnost na každém multiplexeru, abyste pomohli s diagnostikou.
      * Tyto pokyny mohou vést k efektivnější `ConnectionMultiplexer`latenci na .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Jakou mezipaměť Azure pro klienty Redis umíte používat?
Jednou z velkých věcí, o Redis je, že existuje mnoho klientů, kteří podporují mnoho různých vývojových jazyků. Aktuální seznam klientů naleznete v tématu [Redis clients](https://redis.io/clients). Kurzy, které pokrývají několik různých jazyků a klientů, najdete v [tématu Jak používat Azure Cache pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a je na stejné úrovni články v obsahu.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Existuje místní emulátor pro Azure Cache pro Redis?
Neexistuje žádný místní emulátor pro Azure Cache pro Redis, ale můžete spustit verzi MSOpenTech redis-server.exe z [nástrojů příkazového řádku Redis](https://github.com/MSOpenTech/redis/releases/) v místním počítači a připojit se k němu, abyste získali podobné prostředí jako emulátor místní mezipaměti, jak je znázorněno v následujícím příkladu:

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


Volitelně můžete nakonfigurovat soubor [redis.conf](https://redis.io/topics/config) tak, aby lépe odpovídal [výchozímu nastavení mezipaměti pro](cache-configure.md#default-redis-server-configuration) online mezipaměť Azure pro Redis, pokud je to žádoucí.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Jak lze spustit příkazy Redis?
Můžete použít libovolný z příkazů uvedených na [příkazech Redis](https://redis.io/commands#) s výjimkou příkazů uvedených na [příkazech Redis, které nejsou podporované v azure cache pro Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Máte několik možností pro spuštění příkazů Redis.

* Pokud máte mezipaměť Standard nebo Premium, můžete příkazy Redis spustit pomocí [konzoly Redis](cache-configure.md#redis-console)Console . Konzole Redis poskytuje bezpečný způsob, jak spouštět příkazy Redis na webu Azure Portal.
* Můžete také použít nástroje příkazového řádku Redis. Chcete-li je použít, proveďte následující kroky:
* Stáhněte si [nástroje příkazového řádku Redis](https://github.com/MSOpenTech/redis/releases/).
* Připojte se ke `redis-cli.exe`mezipaměti pomocí aplikace . Předavte koncový bod mezipaměti pomocí přepínače -h a klíče pomocí -a, jak je znázorněno v následujícím příkladu:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Nástroje příkazového řádku Redis nefungují s portem TLS, ale `stunnel` můžete použít nástroj, jako je například bezpečně připojit nástroje k portu TLS podle pokynů v [jak používat nástroj příkazového řádku Redis s Azure Cache pro Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) článku.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Proč azure cache pro redis nemá odkaz na knihovnu tříd MSDN jako některé jiné služby Azure?
Mezipaměť Microsoft Azure pro Redis je založená na oblíbené open source mezipaměti Azure pro Redis. To je možné přistupovat širokou škálu [klientů Redis](https://redis.io/clients) pro mnoho programovacích jazyků. Každý klient má vlastní rozhraní API, které provádí volání instance Azure Cache for Redis pomocí [příkazů Redis](https://redis.io/commands).

Vzhledem k tomu, že každý klient je jiný, neexistuje jeden odkaz na centralizovanou třídu na MSDN a každý klient udržuje vlastní referenční dokumentaci. Kromě referenční dokumentace existuje několik kurzů, které ukazují, jak začít s Azure Cache for Redis pomocí různých jazyků a klientů mezipaměti. Přístup k těmto kurzům najdete v [tématu Jak používat Azure Cache pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a je na stejné úrovni články v obsahu.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Můžu použít Azure Cache pro Redis jako mezipaměť relací PHP?
Ano, pokud chcete azure cache pro Redis použít jako mezipaměť relací PHP, zadejte připojovací řetězec k instanci Azure Cache for Redis v . `session.save_path`

> [!IMPORTANT]
> Při použití Azure Cache for Redis jako mezipaměti relací PHP je nutné kódovat klíč zabezpečení používaný k připojení k mezipaměti pomocí mezipaměti, jak je znázorněno v následujícím příkladu:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Pokud klíč není kódován adresou URL, může se zobrazit výjimka se zprávou jako:`Failed to parse session.save_path`
>
>

Další informace o použití Azure Cache for Redis jako mezipaměti relace PHP s klientem PhpRedis najdete v [tématu php session handler](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Co jsou databáze Redis?

Redis Databases jsou jen logické oddělení dat v rámci stejné instance Redis. Paměť mezipaměti je sdílena mezi všemi databázemi a skutečná spotřeba paměti dané databáze závisí na klíčích nebo hodnotách uložených v této databázi. Například mezipaměť C6 má 53 GB paměti a P5 má 120 GB. Můžete si vybrat, aby všechny 53 GB / 120 GB do jedné databáze, nebo si můžete rozdělit mezi více databází. 

> [!NOTE]
> Při použití premium Azure Cache pro Redis s povolenou clustering, je k dispozici pouze databáze 0. Toto omezení je vnitřní omezení Redis a není specifické pro Azure Cache pro Redis. Další informace naleznete v [tématu Je nutné provést nějaké změny v klientské aplikaci, aby bylo účelem použití clusteringu?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Kdy mám povolit port non-TLS/SSL pro připojení k Redisu?
Redis server nepodporuje nativně TLS, ale Azure Cache pro Redis dělá. Pokud se připojujete k Azure Cache pro Redis a váš klient podporuje TLS, jako stackexchange.Redis, pak byste měli použít TLS.

>[!NOTE]
>Port bez TLS je ve výchozím nastavení zakázán pro nové instance Azure Cache for Redis. Pokud váš klient nepodporuje TLS, musíte povolit port bez TLS podle pokynů v části [Porty Přístup](cache-configure.md#access-ports) u článku [Konfigurace mezipaměti v mezipaměti Azure Cache for Redis.](cache-configure.md)
>
>

Nástroje Redis, `redis-cli` jako je například nefungují s portem TLS, ale můžete použít nástroj, například `stunnel` bezpečně připojit nástroje k portu TLS podle pokynů v oznámení ASP.NET stavu relace [zprostředkovatele pro Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogu.

Pokyny ke stažení nástrojů Redis naleznete v části [Jak lze spustit příkazy Redis?](#cache-commands)

### <a name="what-are-some-production-best-practices"></a>Jaké jsou některé výrobní osvědčené postupy?
* [StackExchange.Redis osvědčené postupy](#stackexchangeredis-best-practices)
* [Konfigurace a koncepty](#configuration-and-concepts)
* [Testování výkonu](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis osvědčené postupy
* Nastavte `AbortConnect` na hodnotu false a nechte connectionmultiplexer automaticky znovu připojit. [Podrobnosti naleznete zde](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Znovu použít ConnectionMultiplexer - nevytvářejte nový pro každý požadavek. Zde `Lazy<ConnectionMultiplexer>` [je uveden](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) vzor.
* Redis funguje nejlépe s menšími hodnotami, proto zvažte sekání větších dat do více klíčů. V [této diskusi Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb je považováno za velké. Přečtěte si [tento článek](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) příklad problému, který může být způsoben velkými hodnotami.
* Nakonfigurujte [nastavení fondu Vláken,](#important-details-about-threadpool-growth) abyste se vyhnuli časovým časům.
* Použijte alespoň výchozí connectTimeout 5 sekund. Tento interval poskytuje StackExchange.Redis dostatek času k obnovení připojení v případě výkyvu sítě.
* Uvědomte si náklady na výkon spojené s různými operacemi, které používáte. Například `KEYS` příkaz je operace O(n) a je třeba se mu vyhnout. Web [redis.io](https://redis.io/commands/) obsahuje podrobnosti o časové složitosti pro každou operaci, kterou podporuje. Kliknutím na jednotlivé příkazy zobrazíte složitost jednotlivých operací.

#### <a name="configuration-and-concepts"></a>Konfigurace a koncepty
* Pro produkční systémy používejte standardní nebo prémiovou úroveň. Úroveň Basic odpovídá systému s jedním uzlem, bez replikace dat a smlouvy SLA. Jako mezipaměť použijte aspoň C1. Mezipaměti C0 se obvykle používají pro jednoduché scénáře pro vývoj a testování.
* Nezapomeňte, že Redis je úložiště dat **v paměti.** Přečtěte si [tento článek,](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) abyste si byli vědomi scénářů, kde může dojít ke ztrátě dat.
* Rozvíjet systém tak, že může zvládnout připojení výkyvy [v důsledku opravy a převzetí služeb při selhání](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testování výkonu
* Začněte `redis-benchmark.exe` pomocí získat pocit pro možnou propustnost před psaním vlastní perf testy. Vzhledem k tomu, že `redis-benchmark` nepodporuje TLS, musíte [povolit port non-TLS prostřednictvím portálu Azure](cache-configure.md#access-ports) před spuštěním testu. Příklady najdete v tématu [Jak můžu porovnat a otestovat výkon mezipaměti?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Virtuální počítač klienta používaný pro testování by měl být ve stejné oblasti jako vaše instance Azure Cache for Redis.
* Doporučujeme používat dv2 VM Series pro vašeho klienta, protože mají lepší hardware a měl by poskytnout nejlepší výsledky.
* Ujistěte se, že váš klientský virtuální počítač, který zvolíte, má alespoň tolik výpočetní ch od kapacity a šířky pásma jako testoce mezipaměti.
* Pokud jste v systému Windows, povolte v klientském počítači v počítači. [Podrobnosti naleznete zde](https://technet.microsoft.com/library/dn383582.aspx).
* Instance Redis úrovně Premium mají lepší latenci a propustnost sítě, protože běží na lepším hardwaru pro procesor i síť.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jaké jsou některé důležité informace při použití běžných příkazů Redis?

* Nepoužívejte určité příkazy Redis, které trvá dlouhou dobu, pokud plně neporozumíte dopadu těchto příkazů. Například nespouštějte příkaz [KEYS](https://redis.io/commands/keys) v produkčním prostředí. V závislosti na počtu klíčů může trvat dlouhou dobu, než se vrátíte. Redis je server s jedním vláknem a zpracovává příkazy jeden po druhém. Pokud máte další příkazy vydané po klávesy, nebudou zpracovány, dokud Redis zpracuje příkaz KEYS. Web [redis.io](https://redis.io/commands/) obsahuje podrobnosti o časové složitosti pro každou operaci, kterou podporuje. Kliknutím na jednotlivé příkazy zobrazíte složitost jednotlivých operací.
* Velikosti klíčů - mám použít malý klíč/hodnoty nebo velký klíč/hodnoty? Záleží na scénáři. Pokud váš scénář vyžaduje větší klíče, můžete upravit ConnectionTimeout, potom opakovat hodnoty a upravit logiku opakování. Z hlediska serveru Redis poskytují menší hodnoty lepší výkon.
* Tyto aspekty neznamenají, že v Redisu nelze ukládat větší hodnoty; musíte si být vědomi následujících úvah. Latence budou vyšší. Pokud máte jednu sadu dat, která je větší a jeden, který je menší, můžete použít více ConnectionMultiplexer instance, každý nakonfigurován s jinou sadou timeout a opakovat hodnoty, jak je popsáno v předchozím [Co stackExchange.Redis možnosti konfigurace do.](#cache-configuration)

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak mohu porovnat a otestovat výkon mezipaměti?
* [Povolte diagnostiku mezipaměti](cache-how-to-monitor.md#enable-cache-diagnostics), abyste mohli [monitorovat](cache-how-to-monitor.md) stav svojí mezipaměti. Metriky si můžete zobrazit na webu Azure Portal a můžete si je taky [stáhnout a zkontrolovat](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) pomocí nástrojů podle vašeho výběru.
* Redis-benchmark.exe můžete použít k načtení testu serveru Redis.
* Ujistěte se, že klient zátěžového testování a azure cache pro redis jsou ve stejné oblasti.
* Použijte redis-cli.exe a sledujte mezipaměť pomocí příkazu INFO.
* Pokud vaše zatížení způsobuje vysokou fragmentaci paměti, měli byste vertikálně navýšit kapacitu na větší velikost mezipaměti.
* Pokyny ke stažení nástrojů Redis naleznete v části [Jak lze spustit příkazy Redis?](#cache-commands)

Následující příkazy poskytují příklad použití redis-benchmark.exe. Pro přesné výsledky spusťte tyto příkazy z virtuálního počítače ve stejné oblasti jako vaše mezipaměti.

* Testování požadavků Pipelined SET pomocí datové části 1k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testování požadavků GET pipelined pomocí datové části 1 kS.
  Poznámka: Spusťte set test je uvedeno výše jako první naplnit mezipaměť

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Důležité podrobnosti o růstu Fondu vláken
Clr ThreadPool má dva typy podprocesů - "Pracovník" a "I/O Dokončení Port" (IOCP) podprocesů.

* Pracovní podprocesy se používají `Task.Run(…)`pro `ThreadPool.QueueUserWorkItem(…)` věci, jako je zpracování , nebo metody. Tato vlákna jsou také používány různými součástmi v CLR, když práce musí probíhat na podprocesu na pozadí.
* Vlákna IOCP se používají, když dojde k asynchronnímu vyvěšení, například při čtení ze sítě.

Fond vláken poskytuje nová pracovní vlákna nebo vstupně-tova dokončení podprocesů na vyžádání (bez omezení), dokud nedosáhne nastavení "Minimum" pro každý typ vlákna. Ve výchozím nastavení je minimální počet podprocesů nastaven na počet procesorů v systému.

Jakmile počet existujících (zaneprázdněn) podprocesů narazí na "minimální" počet vláken, ThreadPool bude omezovat rychlost, jakou vloží nové podprocesy do jednoho vlákna za 500 milisekund. Obvykle pokud váš systém dostane shluk práce, které potřebují vlákno IOCP, bude zpracovávat, že práce rychle. Pokud je však shluk práce více než nakonfigurované nastavení "Minimum", dojde k určitému zpoždění při zpracování některých prací, protože ThreadPool čeká na jednu ze dvou věcí.

1. Existující vlákno se uvolní pro zpracování práce.
2. Žádné existující vlákno se neuvolní po dobu 500 ms, takže je vytvořeno nové vlákno.

V podstatě to znamená, že když je počet zaneprázdněných vláken větší než vlákna min, pravděpodobně platíte zpoždění 500 ms před zpracováním síťového provozu aplikací. Také je důležité si uvědomit, že když existující vlákno zůstane nečinný déle než 15 sekund (na základě toho, co si pamatuji), bude vyčištěn a tento cyklus růstu a smršťování se může opakovat.

Pokud se podíváme na ukázkovou chybovou zprávu z StackExchange.Redis (sestavení 1.0.450 nebo novější), uvidíte, že nyní vytiskne threadpool statistiky (viz podrobnosti IOCP a WORKER níže).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

V předchozím příkladu vidíte, že pro vlákno IOCP existuje šest zaneprázdněných vláken a systém je nakonfigurován tak, aby umožňoval čtyři minimální vlákna. V tomto případě by klient pravděpodobně viděl dvě zpoždění 500 ms, protože 6 > 4.

Všimněte si, že StackExchange.Redis může přístupit časové limity, pokud růst podprocesů IOCP nebo PRACOVNÍ PROCES získá omezen.

### <a name="recommendation"></a>Doporučení

Vzhledem k tomu, tyto informace důrazně doporučujeme, aby zákazníci nastavit minimální hodnotu konfigurace pro podprocesy IOCP a WORKER na něco větší než výchozí hodnotu. Nemůžeme poskytnout univerzální pokyny k tomu, jaká by měla být tato hodnota, protože správná hodnota pro jednu aplikaci bude pravděpodobně příliš vysoká nebo nízká pro jinou aplikaci. Toto nastavení může také ovlivnit výkon jiných částí složitých aplikací, takže každý zákazník musí toto nastavení doladit podle svých specifických potřeb. Dobrým výchozím místem je 200 nebo 300, pak test a vyladit podle potřeby.

Jak toto nastavení nakonfigurovat:

* Doporučujeme změnit toto nastavení programově pomocí [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) metoda v `global.asax.cs`. Příklad:

```cs
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
  > Hodnota určená touto metodou je globální nastavení, které ovlivňuje celou doménu AppDomain. Například pokud máte 4jádrový počítač a chcete nastavit *minWorkerThreads* a *minIoThreads* na 50 na procesor během běhu, použijte **ThreadPool.SetMinThreads(200, 200)**.

* Je také možné zadat minimální podprocesy nastavení pomocí [ *minIoThreads* nebo *minWorkerThreads* nastavení konfigurace](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`pod `<processModel>` konfigurační prvek v `Machine.config`, obvykle se nachází na . **Nastavení počtu minimálních podprocesů tímto způsobem se obecně nedoporučuje, protože se jedná o nastavení celého systému.**

  > [!NOTE]
  > Hodnota zadaná v tomto konfiguračním prvku je nastavení *pro jádro.* Například pokud máte 4-core počítač a chcete, aby vaše *minIoThreads* nastavení 200 za běhu, použijte `<processModel minIoThreads="50"/>`.
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Povolit server GC získat větší propustnost na straně klienta při použití StackExchange.Redis
Povolení server GC můžete optimalizovat klienta a poskytují lepší výkon a propustnost při použití StackExchange.Redis. Další informace o serveru GC a jeho povolení naleznete v následujících článcích:

* [Povolení globálního katalogu serverů](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Základy kolekce paměti](/dotnet/standard/garbage-collection/fundamentals)
* [Uvolňování paměti a výkon](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Aspekty výkonu kolem připojení

Každá cenová úroveň má různá omezení pro připojení klientů, paměť a šířku pásma. Zatímco každá velikost mezipaměti umožňuje *až* určitý počet připojení, každé připojení k Redis má režii s ním spojené. Příkladem takové režie by bylo využití procesoru a paměti v důsledku šifrování TLS/SSL. Maximální limit připojení pro danou velikost mezipaměti předpokládá mírně načtenou mezipaměť. Pokud zatížení z režie připojení *plus* zatížení z operací klienta překročí kapacitu pro systém, může dojít k problémům s kapacitou mezipaměti i v případě, že jste nepřekročili limit připojení pro aktuální velikost mezipaměti.

Další informace o různých omezenípřipojení pro každou úroveň, najdete v [tématu Azure Cache pro redis ceny](https://azure.microsoft.com/pricing/details/cache/). Další informace o připojeních a dalších výchozích konfiguracích naleznete [v tématu Default Redis server configuration](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Jak lze sledovat stav a výkon mezipaměti?
Instance mezipaměti Microsoft Azure pro Redis lze monitorovat na [webu Azure Portal](https://portal.azure.com). Můžete zobrazit metriky, připnout grafy metrik na úvodní obrazovku, přizpůsobit časové období monitorovacích grafů, přidat a odebrat metriky z grafů a nastavit výstrahy, když jsou splněny určité podmínky. Další informace naleznete v [tématu Sledování mezipaměti Azure pro Redis](cache-how-to-monitor.md).

**Nabídka** Prostředku Azure Cache for Redis obsahuje také několik nástrojů pro monitorování a řešení potíží s mezipamětí.

* **Diagnostika a řešení problémů** poskytuje informace o běžných problémech a strategiích jejich řešení.
* **Stav prostředků** sleduje váš prostředek a informuje vás, pokud je spuštěn podle očekávání. Další informace o službě Azure Resource Health najdete v [tématu Přehled stavu prostředků Azure](../resource-health/resource-health-overview.md).
* **Nová žádost o podporu** poskytuje možnosti pro otevření žádosti o podporu pro vaši mezipaměť.

Tyto nástroje umožňují sledovat stav vaší azure cache pro redis instance a pomůže vám spravovat vaše aplikace ukládání do mezipaměti. Další informace naleznete v části Support & troubleshooting settings v části [Jak nakonfigurovat mezipaměť Azure pro Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Proč se mi zobrazují timeouty?
Časové opony se stávají v klientovi, který používáte k rozhovoru s Redis. Když je příkaz odeslán na server Redis, příkaz je zařazen do fronty a server Redis nakonec příkaz zvedne a provede jej. Klient však může časový mat během tohoto procesu a pokud se výjimka je vyvolána na straně volání. Další informace o řešení problémů s časovým účto obdobím naleznete v [tématu řešení potíží na straně klienta](cache-troubleshoot-client.md) a [výjimky časového omezení serveru StackExchange.Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Proč byl můj klient odpojen od mezipaměti?
Následuje některé běžné důvody pro odpojení mezipaměti.

* Příčiny na straně klienta
  * Klientská aplikace byla znovu nasazena.
  * Klientská aplikace provedla operaci škálování.
    * V případě cloudových služeb nebo webových aplikací to může být způsobeno automatickým škálováním.
  * Síťová vrstva na straně klienta se změnila.
  * V klientovi nebo v síťových uzlech mezi klientem a serverem došlo k přechodným chybám.
  * Bylo dosaženo prahových hodnot šířky pásma.
  * Operace vázané na procesor trvalo příliš dlouho.
* Příčiny na straně serveru
  * Ve standardní nabídce mezipaměti služba Azure Cache for Redis iniciovala převzetí služeb při selhání z primárního uzlu do sekundárního uzlu.
  * Azure opravoval instanci, kde byla nasazena mezipaměť.
    * To může být pro aktualizace serveru Redis nebo obecné údržby virtuálních montovny.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Která nabídka Azure Cache je pro mě ta pravá?
> [!IMPORTANT]
> Podle loňského [oznámení](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)byla služba Azure Managed Cache service a služba Azure In-Role Cache **vyřazeny** 30. Naše doporučení je použít [Azure Cache pro Redis](https://azure.microsoft.com/services/cache/). Informace o migraci najdete v tématu [Migrace ze služby spravované mezipaměti do mezipaměti Azure pro Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis je obecně dostupná ve velikostech až 120 GB a má dostupnost s la 99,9 %. Nová [úroveň premium](cache-premium-tier-intro.md) nabízí velikosti až 1,2 TB a podporu pro clustering, virtuální síť a trvalost s 99,9% sla.

Azure Cache for Redis poskytuje zákazníkům možnost používat zabezpečenou a vyhrazenou mezipaměť Azure pro Redis, kterou spravuje Microsoft. S touto nabídkou můžete využít bohatou sadu funkcí a ekosystém poskytovaný společností Redis a spolehlivý hosting a monitorování od společnosti Microsoft.

Na rozdíl od tradičních mezipamětí, které se zabývají pouze páry klíč-hodnota, Redis je populární pro své vysoce výkonné datové typy. Redis také podporuje spuštění atomických operací na těchto typech, jako je připojení k řetězci; zvýšení hodnoty v hodnotě hash; zatlačení na seznam; výpočetní soubor průsečík, sjednocení a rozdíl; nebo získání člena s nejvyšším hodnocením v seřazené sadě. Mezi další funkce patří podpora transakcí, pub / sub, Skriptování Lua, klíče s omezenou dobou života a nastavení konfigurace, aby se Redis choval spíše jako tradiční cache.

Dalším klíčovým aspektem úspěchu redisu je zdravý, živý ekosystém s otevřeným zdrojovým kódem, který je kolem něj postaven. To se odráží v rozmanité sadě klientů Redis, které jsou k dispozici ve více jazycích. Tento ekosystém a široká škála klientů umožňují Azure Cache pro Redis používat téměř všechny úlohy, které byste vytvořili uvnitř Azure.

Další informace o tom, jak začít s Azure Cache for Redis, najdete v tématu [Jak používat Azure Cache pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a Azure Cache pro [Redis dokumentace](index.yml).

### <a name="managed-cache-service"></a>Služba spravované mezipaměti
[Služba Spravované mezipaměti byla vyřazena 30.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Informace o archivované dokumentaci naleznete v [tématu Archivovaná dokumentace ke službě spravované mezipaměti](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Mezipaměť v roli
[Mezipaměť v roli byla vyřazena 30.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Informace o archivované dokumentaci naleznete [v tématu Archivovaná dokumentace mezipaměti v roli](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
