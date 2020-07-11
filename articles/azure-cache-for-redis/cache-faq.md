---
title: Nejčastější dotazy ke službě Azure Cache for Redis
description: Přečtěte si odpovědi na běžné otázky, vzory a osvědčené postupy pro službu Azure cache pro Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 9a6ee4f5b18c6747796f33bc433d1d40982205a3
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185003"
---
# <a name="azure-cache-for-redis-faq"></a>Nejčastější dotazy ke službě Azure Cache for Redis
Seznamte se s odpověďmi na běžné otázky, vzory a osvědčené postupy pro službu Azure cache pro Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Co když tady není odpověď na moji otázku?
Pokud tady uvedený dotaz není, dejte nám prosím jistotu a my vám pomůžeme najít odpověď.

* V komentářích na konci těchto nejčastějších dotazů můžete poslat otázku a začít s týmem Azure cache a dalšími členy komunity o tomto článku.
* Pokud chcete oslovit širší cílovou skupinu, můžete odeslat otázku na stránce s [dotazem k Microsoft Q&pro Azure cache](https://docs.microsoft.com/answers/topics/azure-cache-redis.html) a spojit se s týmem Azure cache a dalšími členy komunity.
* Pokud chcete vytvořit žádost o funkci, můžete odeslat své žádosti a nápady do [Azure cache pro Redis uživatelského hlasu](https://feedback.azure.com/forums/169382-cache).
* Můžete také poslat e-mail na [externí zpětnou vazbu v mezipaměti Azure](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Základy Azure cache pro Redis
Nejčastější dotazy v této části se týkají některých základních informací o službě Azure cache pro Redis.

* [Co je Azure Cache for Redis?](#what-is-azure-cache-for-redis)
* [Jak můžu začít pracovat s Azure cache pro Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

Následující nejčastější dotazy se týkají základních konceptů a dotazů týkajících se služby Azure cache pro Redis a jsou zodpovězeny v jednom z dalších částí nejčastějších dotazů.

* [Jakou mezipaměť Azure pro nabídku a velikost Redis mám použít?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Jakou mezipaměť Azure pro klienty Redis můžu použít?](#what-azure-cache-for-redis-clients-can-i-use)
* [Existuje místní emulátor pro Azure cache pro Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Návody monitorovat stav a výkon složky Moje mezipaměť?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Nejčastější dotazy k plánování
* [Jakou mezipaměť Azure pro nabídku a velikost Redis mám použít?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure cache pro výkon Redis](#azure-cache-for-redis-performance)
* [V jaké oblasti mám najít mezipaměť?](#in-what-region-should-i-locate-my-cache)
* [Kde se nacházejí moje data z mezipaměti?](#where-do-my-cached-data-reside)
* [Jak se fakturuje Azure cache pro Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Můžu použít Azure cache pro Redis s Azure Governmentm cloudem, cloudem Azure Čína nebo Microsoft Azure (Německo)?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Nejčastější dotazy týkající se vývoje
* [Co dělají konfigurační možnosti StackExchange. Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Jakou mezipaměť Azure pro klienty Redis můžu použít?](#what-azure-cache-for-redis-clients-can-i-use)
* [Existuje místní emulátor pro Azure cache pro Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak můžu spustit příkazy Redis?](#how-can-i-run-redis-commands)
* [Proč mezipaměť Azure pro Redis nemá odkaz knihovny tříd MSDN jako některé z ostatních služeb Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Můžu použít Azure cache pro Redis jako mezipaměť relace PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Co jsou databáze Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Nejčastější dotazy týkající se zabezpečení
* [Kdy je vhodné povolit port bez TLS/SSL pro připojení k Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Provozní Nejčastější dotazy
* [Jaké jsou osvědčené postupy pro produkci?](#what-are-some-production-best-practices)
* [Jaké jsou některé důležité informace při použití běžných příkazů Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak mohu srovnávací testy a testovat výkon své mezipaměti?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Důležité podrobnosti o růstu fondu vláken](#important-details-about-threadpool-growth)
* [Povolit GC serveru pro získání větší propustnosti klienta při používání StackExchange. Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Požadavky na výkon kolem připojení](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Nejčastější dotazy týkající se monitorování a odstraňování potíží
Nejčastější dotazy v této části se týkají běžných otázek monitorování a řešení potíží. Další informace o monitorování a řešení potíží s mezipamětí Azure cache pro instance Redis najdete v tématu [monitorování mezipaměti Azure pro Redis](cache-how-to-monitor.md) a v různých průvodcích odstraňováním potíží.

* [Návody monitorovat stav a výkon složky Moje mezipaměť?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Proč se zobrazuje časový limit?](#why-am-i-seeing-timeouts)
* [Proč byl můj klient odpojený od mezipaměti?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Předchozí mezipaměť – Nejčastější dotazy
* [Která nabídka Azure cache je pro mě ta pravá?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Co je Azure Cache for Redis?
Mezipaměť Azure pro Redis je založená na oblíbeném open source softwaru [Redis](https://redis.io/). Poskytuje přístup k zabezpečené, vyhrazené mezipaměti Azure pro Redis, která je spravovaná Microsoftem a přístupná z libovolné aplikace v Azure. Podrobnější přehled najdete na stránce produktu [Azure cache for Redis](https://azure.microsoft.com/services/cache/) na Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Jak můžu začít pracovat s Azure cache pro Redis?
Existuje několik způsobů, jak můžete začít s Azure cache pro Redis.

* Můžete se podívat na jeden z našich kurzů dostupných pro [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)a [Python](cache-python-get-started.md).
* Můžete sledovat, [jak vytvářet vysoce výkonné aplikace pomocí Microsoft Azure cache pro Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Pokud chcete zjistit, jak používat Redis, můžete se podívat na dokumentaci klienta pro klienty, kteří odpovídají vývojovému jazyku vašeho projektu. Existuje mnoho Redis klientů, které lze použít s Azure cache pro Redis. Seznam Redis klientů najdete v tématu [https://redis.io/clients](https://redis.io/clients) .

Pokud ještě nemáte účet Azure, můžete:

* [Otevřít bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Získáte kredity, které můžete použít k vyzkoušení placených služeb Azure. I po vyčerpání kreditů si můžete účet ponechat a používat bezplatné funkce a služby Azure.
* [Aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Jakou mezipaměť Azure pro nabídku a velikost Redis mám použít?
Každá mezipaměť Azure pro nabídku Redis poskytuje různé úrovně **velikosti**, **šířky pásma**, **vysoké dostupnosti**a možností **smlouvy SLA** .

Níže jsou uvedeny požadavky pro výběr nabídky mezipaměti.

* **Paměť**: úrovně Basic a Standard nabízejí 250 MB – 53 GB. Úroveň Premium nabízí až 1,2 TB (jako cluster) nebo 120 GB (neclusterovaný). Další informace najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Výkon sítě**: Pokud máte úlohu, která vyžaduje vysokou propustnost, úroveň Premium nabízí větší šířku pásma v porovnání se standardem nebo základním. Kromě toho v každé vrstvě mají větší velikost mezipaměti větší šířku pásma kvůli základnímu virtuálnímu počítači, který hostuje mezipaměť. Další informace najdete v [následující tabulce](#cache-performance).
* **Propustnost**: úroveň Premium nabízí maximální dostupnou propustnost. Pokud server mezipaměti nebo klient dosáhne limitů šířky pásma, může docházet k vypršení časového limitu na straně klienta. Další informace najdete v následující tabulce.
* **Vysoká dostupnost/SLA**: Azure cache pro Redis zaručuje, že mezipaměť Standard/Premium je k dispozici minimálně 99,9% času. Další informace o naší smlouvě SLA najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Smlouva SLA pokrývá jenom připojení ke koncovým bodům mezipaměti. Smlouva SLA nepokrývá ochranu před ztrátou dat. Pro zvýšení odolnosti proti ztrátě dat doporučujeme používat funkci trvalosti dat Redis na úrovni Premium.
* **Trvalost dat Redis**: úroveň Premium vám umožňuje uchovávat data z mezipaměti v účtu Azure Storage. V mezipaměti Basic a Standard jsou všechna data uložena pouze v paměti. Základní problémy s infrastrukturou můžou způsobit ztrátu dat. Pro zvýšení odolnosti proti ztrátě dat doporučujeme používat funkci trvalosti dat Redis na úrovni Premium. Azure cache for Redis nabízí možnosti RDB a AOF (Preview) v Redis persistenci. Další informace najdete v tématu [Konfigurace trvalosti pro službu Azure cache Premium pro Redis](cache-how-to-premium-persistence.md).
* **Cluster Redis**: Pokud chcete vytvořit mezipaměti větší než 120 GB nebo horizontálních oddílů data napříč několika uzly Redis, můžete použít clustering Redis, který je k dispozici na úrovni Premium. Každý uzel se skládá z dvojice mezipaměti primárního/repliky pro zajištění vysoké dostupnosti. Další informace najdete v tématu [Konfigurace clusteringu pro službu Azure cache v úrovni Premium pro Redis](cache-how-to-premium-clustering.md).
* **Rozšířené zabezpečení a izolace sítě**: nasazení služby Azure Virtual Network (VNET) poskytuje rozšířené zabezpečení a izolaci pro službu Azure cache pro Redis a podsítě, zásady řízení přístupu a další funkce, které umožňují další omezení přístupu. Další informace najdete v tématu [jak nakonfigurovat Virtual Network podporu pro Azure cache Premium pro Redis](cache-how-to-premium-vnet.md).
* **Konfigurace Redis**: v úrovních Standard a Premium můžete nakonfigurovat Redis pro oznámení na místě.
* **Maximální počet připojení klientů**: úroveň Premium nabízí maximální počet klientů, kteří se mohou připojit k Redis s větším počtem připojení pro mezipaměti větší velikosti. Clustering nezvyšuje počet připojení dostupných pro clusterovou mezipaměť. Další informace najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Vyhrazená jádro pro Redis Server**: na úrovni Premium mají všechny velikosti mezipaměti vyhrazenou jádro pro Redis. V úrovních Basic a Standard má velikost C1 a vyšší velikost pro server Redis.
* **Redis je jeden podproces** , takže s více než dvěma jádry neposkytuje další výhody nad dvěma jádry, ale větší velikosti virtuálních počítačů obvykle mají větší šířku pásma než menší velikost. Pokud server mezipaměti nebo klient dosáhne omezení šířky pásma, obdržíte časový limit na straně klienta.
* **Vylepšení výkonu**: mezipamětí v úrovni Premium se nasazují na hardware, který má rychlejší procesory, což zajišťuje vyšší výkon v porovnání s úrovní Basic nebo Standard. Mezipaměti úrovně Premium mají vyšší propustnost a nižší latenci.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure cache pro výkon Redis
V následující tabulce jsou uvedeny maximální hodnoty šířky pásma zjištěné při testování různých velikostí mezipamětí Standard a Premium pomocí `redis-benchmark.exe` z virtuálního počítače s IaaS pro koncový bod Redis v mezipaměti Azure. V případě propustnosti TLS se Redis-test používá s stunnelu pro připojení ke koncovému bodu Azure cache pro Redis.

>[!NOTE] 
>Tyto hodnoty nejsou zaručené a pro tato čísla nejsou k dispozici žádná smlouva SLA, ale měla by být typická. Pro určení správné velikosti mezipaměti pro aplikaci byste měli načíst vlastní aplikaci.
>Tato čísla se můžou při pravidelném nastavování novějších výsledků změnit.
>

Z této tabulky můžeme vykreslit následující závěry:

* Propustnost pro mezipaměti, které mají stejnou velikost, je vyšší než úroveň Premium ve srovnání s úrovní Standard. Například s pamětí 6 GB je propustnost P1 180 000 požadavků za sekundu (RPS) v porovnání s 100 000 RPS pro C3.
* Díky clusteringu Redis se propustnost při zvýšení počtu horizontálních oddílů (uzlů) v clusteru lineárně zvyšuje. Pokud třeba vytvoříte cluster P4 o 10 horizontálních oddílů, pak je dostupná propustnost 400 000 × 10 = 4 000 000 RPS.
* Propustnost pro větší velikosti klíčů je vyšší než úroveň Premium ve srovnání s úrovní Standard.

| Cenová úroveň | Velikost | Procesorová jádra | Dostupná šířka pásma | velikost hodnoty 1 – KB | velikost hodnoty 1 – KB |
| --- | --- | --- | --- | --- | --- |
| **Standardní velikosti mezipaměti** | | |**Megabitů za sekundu (MB/s)/megabajtů za sekundu (MB/s)** |**Počet požadavků za sekundu (RPS) bez SSL** |**SSL požadavků za sekundu (RPS)** |
| C0 | 250 MB | Shared | 100/12,5  |  15 000 |   7 500 |
| C1 |   1 GB | 1      | 500/62,5  |  38 000 |  20 720 |
| C2 | 2,5 GB | 2      | 500/62,5  |  41 000 |  37 000 |
| C3 |   6 GB | 4      | 1000/125  | 100 000 |  90,000 |
| C4 |  13 GB | 2      | 500/62,5  |  60 000 |  55 000 |
| C5 |  26 GB | 4      | 1 000/125 | 102 000 |  93 000 |
| C6 |  53 GB | 8      | 2 000/250 | 126 000 | 120 000 |
| **Velikosti mezipaměti úrovně Premium** | |**PROCESORová jádra na horizontálních oddílů** | **Megabitů za sekundu (MB/s)/megabajtů za sekundu (MB/s)** |**Počet požadavků za sekundu (RPS) jiný než SSL na horizontálních oddílů** |**Počet požadavků SSL za sekundu (RPS), za horizontálních oddílů** |
| P1 |   6 GB |  2 | 1 500/187,5 | 180 000 | 172 000 |
| P2 |  13 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P3 |  26 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P4 |  53 GB |  8 | 6 000/750   | 400 000 | 373 000 |
| P5 | 120 GB | 20 | 6 000/750   | 400 000 | 373 000 |

Pokyny k nastavení stunnelu nebo stažení nástrojů Redis, jako jsou `redis-benchmark.exe` , najdete v části [Jak můžu spustit příkazy Redis?](#cache-commands) .

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>V jaké oblasti mám najít mezipaměť?
Pro nejlepší výkon a nejnižší latenci vyhledejte mezipaměť Azure pro Redis ve stejné oblasti, jako je klientská aplikace pro mezipaměť.

### <a name="where-do-my-cached-data-reside"></a>Kde se nacházejí moje data z mezipaměti?
Azure cache pro Redis ukládá data vaší aplikace do paměti RAM virtuálního počítače nebo virtuálních počítačů v závislosti na vrstvě, která hostuje vaši mezipaměť. Vaše data se budou nacházet výhradně v oblasti Azure, kterou jste vybrali ve výchozím nastavení. Existují dva případy, kdy vaše data mohou opustit oblast:
  1. Když v mezipaměti povolíte trvalost, mezipaměť Azure pro Redis bude zálohovat vaše data na účet Azure Storage, který vlastníte. Pokud se účet úložiště, který zadáte, nachází v jiné oblasti, ukončí se kopie vašich dat.
  1. Pokud nastavíte geografickou replikaci a sekundární mezipaměť je v jiné oblasti, což by mohlo být v normálním případě, vaše data budou replikována do této oblasti.

Aby bylo možné používat tyto funkce, budete muset explicitně nakonfigurovat Azure cache pro Redis. Máte také úplnou kontrolu nad oblastí, ve které se nachází účet úložiště nebo sekundární mezipaměť.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Jak se fakturuje Azure cache pro Redis?
Ceny za Azure cache for Redis [najdete tady](https://azure.microsoft.com/pricing/details/cache/). Na stránce s cenami se zobrazují ceny jako hodinové sazby. Mezipaměti se účtují po minutách od doby, kdy se mezipaměť vytvoří, až do doby, kdy se mezipaměť odstraní. Neexistuje možnost pro zastavení nebo pozastavení fakturace v mezipaměti.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Můžu použít Azure cache pro Redis s Azure Governmentm cloudem, cloudem Azure Čína nebo Microsoft Azure (Německo)?
Ano, mezipaměť Azure pro Redis je dostupná v cloudu Azure Government, Azure Čína 21Vianet a Microsoft Azure (Německo). Adresy URL pro přístup a správu Azure cache pro Redis se v porovnání s veřejným cloudem Azure liší.

| Cloud   | Přípona DNS pro Redis            |
|---------|---------------------------------|
| Veřejný  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Německo | *. redis.cache.cloudapi.de       |
| Čína   | *. redis.cache.chinacloudapi.cn  |

Další informace o tom, co je třeba zvážit při použití mezipaměti Azure pro Redis s jinými cloudy, najdete na následujících odkazech.

- [Databáze Azure Government – mezipaměť Azure pro Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Cloud Azure Čína 21Vianet – Azure cache pro Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

Informace o používání služby Azure cache pro Redis s prostředím PowerShell v Azure Government cloudu, Azure Čína 21Vianet Cloud a Microsoft Azure (Německo) najdete v tématu [jak se připojit k dalším cloudům – Azure cache pro Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Co dělají konfigurační možnosti StackExchange. Redis?
StackExchange. Redis má mnoho možností. Tato část pojednává o některých běžných nastaveních. Podrobnější informace o možnostech StackExchange. Redis najdete v tématu [Konfigurace stackexchange. Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Popis | Doporučení |
| --- | --- | --- |
| AbortOnConnectFail |Když se nastaví na true, připojení se po selhání sítě znovu nepřipojí. |Nastavte na hodnotu false a nechte StackExchange. Redis automaticky znovu připojit. |
| ConnectRetry |Počet opakovaných pokusů o připojení při počátečním připojení. |Pokyny najdete v následujících pokynech. |
| ConnectTimeout |Vypršel časový limit v MS pro operace Connect. |Pokyny najdete v následujících pokynech. |

Obvykle jsou výchozí hodnoty klienta dostatečné. Možnosti můžete vyladit podle svých úloh.

* **Opakování**
  * V případě ConnectRetry a ConnectTimeout je obecné pokyny pro rychlé selhání a zkuste to znovu. Tyto doprovodné materiály jsou založené na vašich úlohách a o tom, kolik času v průměru trvá klientovi, aby vydával příkaz Redis a dostal odpověď.
  * Nechejte StackExchange. Redis automaticky znovu připojit místo kontroly stavu připojení a opětovném připojení. **Vyhněte se použití vlastnosti ConnectionMultiplexer. nepřipojená**.
  * Snowballing – někdy můžete narazit na problém, ve kterém zkoušíte, a pokusy Snowball a nikdy se neobnoví. Pokud dojde k Snowballing, měli byste zvážit použití exponenciálního algoritmu opakování omezení rychlosti, jak je popsáno v [obecných pokynech](../best-practices-retry-general.md) , které publikovala skupina Microsoft patterns & Practices.
  
* **Hodnoty časového limitu**
  * Vezměte v úvahu svůj pracovní postup a nastavte hodnoty odpovídajícím způsobem. Pokud ukládáte velké hodnoty, nastavte časový limit na vyšší hodnotu.
  * Nastavte `AbortOnConnectFail` na false a umožněte vám stackexchange. Redis se znovu připojit.
  * Pro aplikaci použijte jednu instanci ConnectionMultiplexer. Pomocí LazyConnection můžete vytvořit jednu instanci, která je vrácena vlastností připojení, jak je znázorněno v [části připojení k mezipaměti pomocí třídy ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * `ConnectionMultiplexer.ClientName`Pro účely diagnostiky nastavte vlastnost na jedinečný název instance aplikace.
  * `ConnectionMultiplexer`Pro vlastní úlohy použijte více instancí.
      * Pokud máte v aplikaci proměnlivé zatížení, můžete postupovat podle tohoto modelu. Například:
      * Můžete mít jeden multiplexor pro zvládnutí velkých klíčů.
      * Můžete mít jeden multiplexor pro zvládnutí malých klíčů.
      * Pro každý ConnectionMultiplexer, který používáte, můžete nastavit různé hodnoty pro vypršení časového limitu připojení a logiku opakování.
      * Nastavte `ClientName` u každého multiplexního multiplexoru vlastnost, která bude pomáhat s diagnostikou.
      * Tento návod může vést k efektivnější latenci na `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Jakou mezipaměť Azure pro klienty Redis můžu použít?
Jednou z skvělých věcí o Redis je to, že mnoho klientů podporuje mnoho různých vývojových jazyků. Aktuální seznam klientů najdete v tématu [Redis klienti](https://redis.io/clients). Výukové kurzy, které pokrývají několik různých jazyků a klientů, najdete v článku [Jak používat Azure cache pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a na stejné úrovni jako v obsahu.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Existuje místní emulátor pro Azure cache pro Redis?
Pro službu Azure cache pro Redis není k dispozici žádný místní emulátor, ale můžete spustit MSOpenTech verzi redis-server.exe z [nástrojů příkazového řádku Redis](https://github.com/MSOpenTech/redis/releases/) v místním počítači a připojit se k němu a získat podobné prostředí emulátoru místní mezipaměti, jak je znázorněno v následujícím příkladu:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Volitelně můžete nakonfigurovat soubor [Redis. conf](https://redis.io/topics/config) tak, aby lépe odpovídal [výchozím nastavením mezipaměti](cache-configure.md#default-redis-server-configuration) pro online Azure cache pro Redis, pokud je to potřeba.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Jak můžu spustit příkazy Redis?
Můžete použít kterýkoli z příkazů uvedených v [Redis příkazech](https://redis.io/commands#) s výjimkou příkazů uvedených v [příkazech Redis, které nejsou podporovány v mezipaměti Azure pro Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). K dispozici máte několik možností, jak spustit příkazy Redis.

* Pokud máte mezipaměť úrovně Standard nebo Premium, můžete spustit příkazy Redis pomocí [konzoly Redis](cache-configure.md#redis-console). Konzola Redis poskytuje zabezpečený způsob, jak spouštět příkazy Redis v Azure Portal.
* Můžete také použít nástroje příkazového řádku Redis. Pokud je chcete použít, proveďte následující kroky:
* Stáhněte si [nástroje příkazového řádku Redis](https://github.com/MSOpenTech/redis/releases/).
* Připojte se k mezipaměti pomocí `redis-cli.exe` . Předejte koncový bod mezipaměti pomocí přepínače-h a klíče pomocí-a, jak je znázorněno v následujícím příkladu:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Nástroje příkazového řádku Redis nefungují s portem TLS, ale pomocí `stunnel` pokynů v článku [Jak používat nástroj příkazového řádku Redis s Azure cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) můžete použít nástroj, například k bezpečnému připojení nástrojů k portu TLS.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Proč mezipaměť Azure pro Redis nemá odkaz knihovny tříd MSDN jako některé z ostatních služeb Azure?
Microsoft Azure cache pro Redis je založená na oblíbené open source mezipaměti Azure pro Redis. Dá se k němu přistupovat širokou škálou [Redis klientů](https://redis.io/clients) pro řadu programovacích jazyků. Každý klient má své vlastní rozhraní API, které umožňuje volání instance Azure cache for Redis pomocí [příkazů Redis](https://redis.io/commands).

Vzhledem k tomu, že každý klient je jiný, není na webu MSDN žádný odkaz na centralizované třídy a každý klient udržuje vlastní referenční dokumentaci. Kromě referenční dokumentace je k dispozici několik kurzů, které ukazují, jak začít s Azure cache pro Redis pomocí různých jazyků a klientů mezipaměti. Pokud chcete získat přístup k těmto kurzům, přečtěte si článek [Jak používat Azure cache pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a na stejné úrovni jako v obsahu.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Můžu použít Azure cache pro Redis jako mezipaměť relace PHP?
Ano, pokud chcete použít mezipaměť Azure pro Redis jako mezipaměť relace PHP, zadejte připojovací řetězec do mezipaměti Azure pro instanci Redis v `session.save_path` .

> [!IMPORTANT]
> Pokud používáte mezipaměť Azure pro Redis jako mezipaměť relace PHP, je nutné kódovat klíč zabezpečení, který se používá pro připojení k mezipaměti, jak je znázorněno v následujícím příkladu:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Pokud klíč není kódovaný v adrese URL, může se zobrazit výjimka se zprávou jako:`Failed to parse session.save_path`
>
>

Další informace o použití mezipaměti Azure pro Redis jako mezipaměti relace PHP s klientem PhpRedis najdete v tématu [obslužná rutina relace php](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Co jsou databáze Redis?

Databáze Redis jsou jenom logicky oddělené data v rámci stejné instance Redis. Paměť mezipaměti je sdílena mezi všemi databázemi a skutečnou spotřebou paměti dané databáze závisí na klíčích a hodnotách uložených v této databázi. Například mezipaměť C6 má 53 GB paměti a P5 má 120 GB. Můžete zvolit, aby se všechny 53 GB/120 GB nastavily do jedné databáze, nebo je můžete rozdělit mezi více databází. 

> [!NOTE]
> Pokud používáte mezipaměť Azure Premium pro Redis s povoleným clusteringem, je k dispozici pouze databáze 0. Toto omezení je vnitřní omezení Redis a není specifické pro Azure cache pro Redis. Další informace najdete v tématu musím dělat [změny v klientské aplikaci, aby používaly clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Kdy je vhodné povolit port bez TLS/SSL pro připojení k Redis?
Redis Server neprovádí nativně podporu TLS, ale mezipaměť Azure pro Redis. Pokud se připojujete ke službě Azure cache pro Redis a váš klient podporuje protokol TLS, jako je StackExchange. Redis, pak byste měli použít protokol TLS.

>[!NOTE]
>Port bez protokolu TLS je ve výchozím nastavení pro novou mezipaměť Azure pro instance Redis zakázaný. Pokud váš klient nepodporuje protokol TLS, musíte povolit port bez TLS podle pokynů v části [přístupové porty](cache-configure.md#access-ports) v tématu [Konfigurace mezipaměti v mezipaměti Azure pro Redis](cache-configure.md) .
>
>

Nástroje Redis, jako `redis-cli` je například nefungují s portem TLS, ale můžete použít nástroj, jako je například `stunnel` k bezpečnému připojení nástrojů k portu TLS podle pokynů v tomto příspěvku na Blogový příspěvek [pro vydání služby ASP.NET pro Redis verze Preview](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/) .

Pokyny ke stažení nástrojů Redis naleznete v části How to [Run Redis Commands?](#cache-commands)

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
* Pokud pracujete v systému Windows, povolte VRSS na klientském počítači. [Podrobnosti najdete tady](https://technet.microsoft.com/library/dn383582.aspx).
* Instance Redis úrovně Premium mají lepší latenci a propustnost sítě, protože jsou spuštěné na lepším hardwaru pro procesor i síť.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jaké jsou některé důležité informace při použití běžných příkazů Redis?

* Vyhněte se použití určitých příkazů Redis, jejichž dokončení trvá dlouhou dobu, pokud plně nerozumíte dopadu těchto příkazů. Nespouštějte například příkaz [Keys](https://redis.io/commands/keys) v produkčním prostředí. V závislosti na počtu klíčů může vrácení zpět trvat dlouhou dobu. Redis je jeden podproces serveru a zpracovává příkazy po jednom. Pokud máte další příkazy vydané po KLÍČích, nebudou zpracovány, dokud Redis nezpracuje příkaz KEYS. [Web Redis.IO](https://redis.io/commands/) obsahuje podrobnosti o časové složitosti každé podporované operace. Pro zobrazení složitosti jednotlivých operací klikněte na jednotlivé příkazy.
* Velikosti klíčů – mám použít malé hodnoty a hodnoty nebo velký klíč/hodnoty? Závisí na scénáři. Pokud váš scénář vyžaduje větší klíče, můžete upravit ConnectionTimeout a pak opakovat hodnoty a upravit logiku opakování. V perspektivě serveru Redis menší hodnoty poskytují lepší výkon.
* Tato doporučení neznamenají, že v Redis nemůžete ukládat větší hodnoty; je nutné vzít v úvahu následující skutečnosti. Latence budou vyšší. Pokud máte jednu sadu dat, která je větší a menší, můžete použít několik instancí ConnectionMultiplexer, z nichž každá je nakonfigurovaná s jinou sadou časových limitů a opakováním, jak je popsáno v předchozím oddílu věnovaném [možnostem konfigurace stackexchange. Redis](#cache-configuration) .

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak mohu srovnávací testy a testovat výkon své mezipaměti?
* [Povolte diagnostiku mezipaměti](cache-how-to-monitor.md#enable-cache-diagnostics), abyste mohli [monitorovat](cache-how-to-monitor.md) stav svojí mezipaměti. Metriky můžete zobrazit v Azure Portal a můžete je také [Stáhnout a revidovat](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) pomocí nástrojů podle vašeho výběru.
* Pro zátěžový test serveru Redis můžete použít redis-benchmark.exe.
* Ujistěte se, že klient zátěžového testování a mezipaměť Azure pro Redis jsou ve stejné oblasti.
* Použijte redis-cli.exe a monitorujte mezipaměť pomocí příkazu INFO.
* Pokud zatížení způsobuje velkou fragmentaci paměti, měli byste škálovat až na větší velikost mezipaměti.
* Pokyny ke stažení nástrojů Redis naleznete v části How to [Run Redis Commands?](#cache-commands)

Následující příkazy poskytují příklad použití redis-benchmark.exe. Pro přesné výsledky spusťte tyto příkazy z virtuálního počítače ve stejné oblasti, ve které je vaše mezipaměť.

* Testování požadavků SET nastavených v kanálu pomocí datové části 1 tisíc

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testování požadavků GET v kanálu pomocí datové části 1 tisíc
  Poznámka: Pokud chcete naplnit mezipaměť, spusťte nejdříve nastavený test, který je uvedený výše.

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Důležité podrobnosti o růstu fondu vláken
Podprocesy CLR mají dva typy vláken – "pracovní proces" a "vstupně-výstupní" port pro dokončení (IOCP).

* Pracovní vlákna se používají pro věci, jako je zpracování `Task.Run(…)` nebo `ThreadPool.QueueUserWorkItem(…)` metody. Tato vlákna jsou používána také v různých komponentách CLR v případě, že je potřeba pracovat ve vlákně na pozadí.
* Vlákna IOCP se používají, když dojde k asynchronní vstupně-výstupní situaci, například při čtení ze sítě.

Fond vláken poskytuje nová pracovní vlákna nebo vstupně-výstupní vlákna na vyžádání (bez omezení), dokud nedosáhne minimálního nastavení pro každý typ vlákna. Ve výchozím nastavení je minimální počet vláken nastavený na počet procesorů v systému.

Jakmile počet stávajících (zaneprázdněných) vláken dosáhne minimálního počtu vláken, vystavení bude omezovat rychlost, s jakou vloží nová vlákna do jednoho vlákna na 500 milisekund. V případě, že váš systém získá nárůst práce, která vyžaduje vlákno IOCP, bude proces práce zpracovávat rychle. Pokud je však nárůst práce více než nakonfigurované "minimální" nastavení, dojde ke zpoždění při zpracovávání některé práce, protože podprocesní operace čekají na jednu ze dvou věcí, které mají být provedeny.

1. Stávající vlákno bude pro zpracování práce zadarmo.
2. Žádné existující vlákno se neuvolní po 500 ms, takže se vytvoří nové vlákno.

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

### <a name="recommendation"></a>Doporučení

S těmito informacemi doporučujeme, aby zákazníci nastavili minimální hodnotu konfigurace pro IOCP a pracovní vlákna na něco většího než výchozí hodnota. Nemůžeme nám dát k dispozici všechny pokyny, jak by tato hodnota měla být, protože správná hodnota pro jednu aplikaci bude pravděpodobně pro jinou aplikaci příliš vysoká nebo nízká. Toto nastavení může mít vliv i na výkon jiných částí složitých aplikací, takže každý zákazník musí toto nastavení vyladit podle svých konkrétních potřeb. Dobrým počátečním místem je 200 nebo 300 a pak podle potřeby proveďte testování a vylepšení.

Jak nakonfigurovat toto nastavení:

* Toto nastavení doporučujeme změnit programově pomocí metody [fondu vláken. SetMinThreads – (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) v `global.asax.cs` . Například:

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
    > Hodnota zadaná touto metodou je globální nastavení, které ovlivňuje celou doménu AppDomain. Pokud máte například počítač se 4 jádry a chcete nastavit *MinWorkerThreads* a *MINIOTHREADS* 50 na procesor za běhu za běhu, použijte auto. **SetMinThreads – (200, 200)**.

* Je také možné zadat minimální nastavení vláken pomocí [nastavení konfigurace *MinIoThreads* nebo *MinWorkerThreads* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) v rámci `<processModel>` konfiguračního prvku v `Machine.config` , obvykle se nachází v `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **Nastavení počtu minimálních vláken tímto způsobem se obecně nedoporučuje, protože se jedná o nastavení v rámci systému.**

  > [!NOTE]
  > Hodnota zadaná v tomto konfiguračním elementu je nastavení *pro jádro* . Pokud máte například počítač se 4 jádry a chcete, aby nastavení *minIoThreads* bylo za běhu 200, použijte `<processModel minIoThreads="50"/>` .
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Povolit GC serveru pro získání větší propustnosti klienta při používání StackExchange. Redis
Povolením GC serveru můžete optimalizovat klienta a zajistit lepší výkon a propustnost při používání StackExchange. Redis. Další informace o nástroji GC serveru a jeho povolení najdete v následujících článcích:

* [Povolení serveru GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Základy kolekce paměti](/dotnet/standard/garbage-collection/fundamentals)
* [Uvolňování paměti a výkon](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Požadavky na výkon kolem připojení

Každá cenová úroveň má odlišná omezení pro připojení klientů, paměť a šířku pásma. I když každá velikost mezipaměti umožňuje *až* určitý počet připojení, každé připojení k Redis má spojenou režii. Příkladem takové režie by bylo využití CPU a paměti v důsledku šifrování TLS/SSL. Maximální limit připojení pro danou velikost mezipaměti předpokládá lehce načtenou mezipaměť. Pokud načtení z režie připojení *plus* zatížení z operací klienta překročí kapacitu systému, může mezipaměť zaznamenat problémy s kapacitou i v případě, že jste nepřekročili limit připojení pro aktuální velikost mezipaměti.

Další informace o různých omezeních připojení pro jednotlivé úrovně najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/). Další informace o připojeních a dalších výchozích konfiguracích najdete v tématu [výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Návody monitorovat stav a výkon složky Moje mezipaměť?
Mezipaměť Microsoft Azure pro instance Redis lze monitorovat v [Azure Portal](https://portal.azure.com). Můžete zobrazit metriky, připnout grafy metriky na úvodní panel, přizpůsobit datum a časový rozsah grafů monitorování, přidat a odebrat metriky z grafů a nastavit upozornění, když budou splněny určité podmínky. Další informace najdete v tématu [monitorování mezipaměti Azure pro Redis](cache-how-to-monitor.md).

**Nabídka prostředků** Azure cache for Redis obsahuje taky několik nástrojů pro monitorování a odstraňování potíží s mezipamětí.

* **Diagnostika a řešení problémů** poskytuje informace o běžných problémech a strategiích jejich řešení.
* **Stav prostředku** sleduje váš prostředek a oznamuje, zda je spuštěný podle očekávání. Další informace o službě Azure Resource Health najdete v článku [Přehled Azure Resource Health](../resource-health/resource-health-overview.md).
* **Nová žádost o podporu** poskytuje možnosti pro otevření žádosti o podporu pro vaši mezipaměť.

Tyto nástroje vám umožní monitorovat stav mezipaměti Azure pro instance Redis a pomáhat při správě aplikací pro ukládání do mezipaměti. Další informace najdete v části "Podpora nastavení řešení potíží s & v tématu [Konfigurace mezipaměti Azure pro Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Proč se zobrazuje časový limit?
V klientovi dojde k vypršení časového limitu, který používáte ke komunikaci s Redis. Když se do serveru Redis pošle příkaz, příkaz se zařadí do fronty a server Redis ho nakonec vybere a spustí. Klient však může během tohoto procesu vyprší časový limit a v případě, že dojde k výjimce, je vyvolána na volající straně. Další informace o řešení potíží s časovým limitem najdete v tématu [řešení potíží na straně klienta](cache-troubleshoot-client.md) a [výjimky stackexchange. Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Proč byl můj klient odpojený od mezipaměti?
Níže jsou uvedeny některé běžné důvody pro odpojení mezipaměti.

* Příčiny na straně klienta
  * Klientská aplikace se znovu nasadila.
  * Klientská aplikace provedla operaci škálování.
    * V případě Cloud Services nebo Web Apps to může být způsobeno automatickým škálováním.
  * Síťová vrstva na straně klienta se změnila.
  * V klientovi nebo v síťových uzlech mezi klientem a serverem došlo k přechodným chybám.
  * Bylo dosaženo limitu prahové hodnoty šířky pásma.
  * Dokončení operací vázaných na procesor trvalo příliš dlouho.
* Příčiny na straně serveru
  * V nabídce standardní mezipaměti služba Azure cache pro Redis zahájila převzetí služeb při selhání z primárního uzlu do uzlu repliky.
  * Azure použil opravu instance, ve které byla mezipaměť nasazená.
    * Může to být pro aktualizace serveru Redis nebo obecnou údržbu virtuálních počítačů.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Která nabídka Azure cache je pro mě ta pravá?
> [!IMPORTANT]
> Od 30. listopadu 2016 se na základě [oznámení](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)za minulé roky **vyřadí** služba azure Managed Cache Service a Azure mezipaměť hostovaná v instanci role. Naším doporučením je použití [Azure cache pro Redis](https://azure.microsoft.com/services/cache/). Informace o migraci najdete v tématu [migrace z Managed Cache Service do Azure cache pro Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Mezipaměť Azure pro Redis je všeobecně dostupná ve velikosti až 120 GB a má smlouvu SLA o dostupnosti 99,9%. Nová [úroveň Premium](cache-premium-tier-intro.md) nabízí velikost až 1,2 TB a podporu CLUSTERINGU, virtuální sítě a trvalosti se smlouvou SLA s 99,9%.

Azure cache pro Redis poskytuje zákazníkům možnost používat pro Redis zabezpečenou vyhrazenou mezipaměť Azure pro spravovanou Microsoftem. Pomocí této nabídky získáte bohatou sadu funkcí a ekosystém poskytovaný Redis a spolehlivé hostování a monitorování od Microsoftu.

Na rozdíl od tradičních mezipamětí, které se týkají pouze párů klíč-hodnota, je Redis oblíbený pro své vysoce výkonné datové typy. Redis také podporuje spouštění atomických operací na těchto typech, jako je připojení k řetězci; zvýšení hodnoty v hodnotě hash; doručování do seznamu; Výpočet průniku sady výpočtů, sjednocení a rozdílu; nebo získání člena s nejvyšším hodnocením v seřazené sadě. Mezi další funkce patří podpora transakcí, publikování v reálném čase, lua skriptování, klíčů s omezeným časovým obdobím a nastavení konfigurace, aby se Redis chovala podobně jako tradiční mezipaměť.

Dalším aspektem klíčových Redis úspěchu je dobrý a vysoce živý ekosystém open source. To se projeví v různorodé sadě klientů Redis dostupných napříč různými jazyky. Tento ekosystém a široké spektrum klientů umožňují použití mezipaměti Azure pro Redis skoro všemi úlohami, které byste sestavili v Azure.

Další informace o tom, jak začít s Azure cache pro Redis, najdete v tématu [Jak používat Azure cache pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a [Azure cache pro Redis dokumentaci](index.yml).

### <a name="managed-cache-service"></a>Služba Managed Cache Service
[Služba Managed Cache Service byla vyřazena z 30. listopadu 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Chcete-li zobrazit archivovanou dokumentaci, přečtěte si téma [Archivovaná dokumentace Managed Cache Service](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Mezipaměť hostovaná v instanci role
[Mezipaměť hostovaná v instanci role bylo vyřazení 30. listopadu 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Chcete-li zobrazit archivovanou dokumentaci, přečtěte si téma [Archivovaná dokumentace mezipaměť hostovaná v instanci role](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
