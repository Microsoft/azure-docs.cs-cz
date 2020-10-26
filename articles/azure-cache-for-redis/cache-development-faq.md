---
title: Nejčastější dotazy týkající se vývoje Azure cache pro Redis
description: Přečtěte si odpovědi na běžné dotazy, které vám pomůžou s vývojem pro Azure cache pro Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: be2e4a002d1daf4da7d042f1fd7d5bf0e9a01377
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544507"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Nejčastější dotazy týkající se vývoje Azure cache pro Redis

Tento článek obsahuje odpovědi na běžné dotazy týkající se vývoje pro službu Azure cache pro Redis.

## <a name="common-questions-and-answers"></a>Časté otázky a odpovědi
Tato část obsahuje následující Nejčastější dotazy:

* [Jak můžu začít pracovat s Azure cache pro Redis?](#how-can-i-get-started-with-azure-cache-for-redis)
* [Co dělají konfigurační možnosti StackExchange. Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Jakou mezipaměť Azure pro klienty Redis můžu použít?](#what-azure-cache-for-redis-clients-can-i-use)
* [Existuje místní emulátor pro Azure cache pro Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak můžu spustit příkazy Redis?](#how-can-i-run-redis-commands)
* [Proč mezipaměť Azure pro Redis nemá odkaz knihovny tříd MSDN?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Můžu použít Azure cache pro Redis jako mezipaměť relace PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Co jsou databáze Redis?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Jak můžu začít pracovat s Azure cache pro Redis?
Existuje několik způsobů, jak můžete začít s Azure cache pro Redis.

* Můžete se podívat na jeden z našich kurzů dostupných pro [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)a [Python](cache-python-get-started.md).
* Můžete sledovat, [jak vytvářet High-Performance aplikace pomocí Microsoft Azure mezipaměti pro Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Pokud chcete zjistit, jak používat Redis, můžete se podívat na dokumentaci klienta pro klienty, kteří odpovídají vývojovému jazyku vašeho projektu. Existuje mnoho Redis klientů, které lze použít s Azure cache pro Redis. Seznam Redis klientů najdete v tématu [https://redis.io/clients](https://redis.io/clients) .

Pokud ještě nemáte účet Azure, můžete:

* [Otevřít bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Získáte kredity, které můžete použít k vyzkoušení placených služeb Azure. I po vyčerpání kreditů si můžete účet ponechat a používat bezplatné funkce a služby Azure.
* [Aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure.

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
  * Nechejte StackExchange. Redis automaticky znovu připojit místo kontroly stavu připojení a opětovném připojení. **Vyhněte se použití vlastnosti ConnectionMultiplexer. nepřipojená** .
  * Snowballing – někdy můžete narazit na problém, ve kterém zkoušíte, a pokusy Snowball a nikdy se neobnoví. Pokud dojde k Snowballing, měli byste zvážit použití exponenciálního algoritmu opakování omezení rychlosti, jak je popsáno v [obecných pokynech](/azure/architecture/best-practices/transient-faults) , které publikovala skupina Microsoft patterns & Practices.
  
* **Hodnoty časového limitu**
  * Vezměte v úvahu svůj pracovní postup a nastavte hodnoty odpovídajícím způsobem. Pokud ukládáte velké hodnoty, nastavte časový limit na vyšší hodnotu.
  * Nastavte `AbortOnConnectFail` na false a umožněte vám stackexchange. Redis se znovu připojit.
  * Pro aplikaci použijte jednu instanci ConnectionMultiplexer. Pomocí LazyConnection můžete vytvořit jednu instanci, která je vrácena vlastností připojení, jak je znázorněno v [části připojení k mezipaměti pomocí třídy ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * `ConnectionMultiplexer.ClientName`Pro účely diagnostiky nastavte vlastnost na jedinečný název instance aplikace.
  * `ConnectionMultiplexer`Pro vlastní úlohy použijte více instancí.
      * Pokud máte v aplikaci proměnlivé zatížení, můžete postupovat podle tohoto modelu. Příklad:
      * Můžete mít jeden multiplexor pro zvládnutí velkých klíčů.
      * Můžete mít jeden multiplexor pro zvládnutí malých klíčů.
      * Pro každý ConnectionMultiplexer, který používáte, můžete nastavit různé hodnoty pro vypršení časového limitu připojení a logiku opakování.
      * Nastavte `ClientName` u každého multiplexního multiplexoru vlastnost, která bude pomáhat s diagnostikou.
      * Tento návod může vést k efektivnější latenci na `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Jakou mezipaměť Azure pro klienty Redis můžu použít?
Jednou z skvělých věcí o Redis je to, že mnoho klientů podporuje mnoho různých vývojových jazyků. Aktuální seznam klientů najdete v tématu [Redis klienti](https://redis.io/clients). Výukové kurzy, které pokrývají několik různých jazyků a klientů, najdete v článku [Jak používat Azure cache pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a na stejné úrovni jako v obsahu.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

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

### <a name="how-can-i-run-redis-commands"></a>Jak můžu spustit příkazy Redis?
Můžete použít kterýkoli z příkazů uvedených v [Redis příkazech](https://redis.io/commands#) s výjimkou příkazů uvedených v [příkazech Redis, které nejsou podporovány v mezipaměti Azure pro Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). K dispozici máte několik možností, jak spustit příkazy Redis.

* Pokud máte mezipaměť úrovně Standard nebo Premium, můžete spustit příkazy Redis pomocí [konzoly Redis](cache-configure.md#redis-console). Konzola Redis poskytuje zabezpečený způsob, jak spouštět příkazy Redis v Azure Portal.
* Můžete také použít nástroje příkazového řádku Redis. Pokud je chcete použít, proveďte následující kroky:
* Stáhněte si [nástroje příkazového řádku Redis](https://github.com/MSOpenTech/redis/releases/).
* Připojte se k mezipaměti pomocí `redis-cli.exe` . Předejte koncový bod mezipaměti pomocí přepínače-h a klíče pomocí-a, jak je znázorněno v následujícím příkladu:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Nástroje příkazového řádku Redis nefungují s portem TLS, ale pomocí `stunnel` pokynů v článku [Jak používat nástroj příkazového řádku Redis s Azure cache for Redis](./cache-how-to-redis-cli-tool.md) můžete použít nástroj, například k bezpečnému připojení nástrojů k portu TLS.
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Proč mezipaměť Azure pro Redis nemá odkaz knihovny tříd MSDN?
Microsoft Azure cache pro Redis vychází z oblíbeného Open Source úložiště dat Redis. Dá se k němu přistupovat širokou škálou [Redis klientů](https://redis.io/clients) pro řadu programovacích jazyků. Každý klient má své vlastní rozhraní API, které umožňuje volání instance Azure cache for Redis pomocí [příkazů Redis](https://redis.io/commands).

Vzhledem k tomu, že každý klient je jiný, není na webu MSDN žádný odkaz na centralizované třídy a každý klient udržuje vlastní referenční dokumentaci. Kromě referenční dokumentace je k dispozici několik kurzů, které ukazují, jak začít s Azure cache pro Redis pomocí různých jazyků a klientů mezipaměti. Pokud chcete získat přístup k těmto kurzům, přečtěte si článek [Jak používat Azure cache pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a na stejné úrovni jako v obsahu.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Můžu použít Azure cache pro Redis jako mezipaměť relace PHP?
Ano, pokud chcete použít mezipaměť Azure pro Redis jako mezipaměť relace PHP, zadejte připojovací řetězec do mezipaměti Azure pro instanci Redis v `session.save_path` .

> [!IMPORTANT]
> Pokud používáte mezipaměť Azure pro Redis jako mezipaměť relace PHP, je nutné kódovat klíč zabezpečení, který se používá pro připojení k mezipaměti, jak je znázorněno v následujícím příkladu:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Pokud klíč není kódovaný v adrese URL, může se zobrazit výjimka se zprávou jako: `Failed to parse session.save_path`
>

Další informace o použití mezipaměti Azure pro Redis jako mezipaměti relace PHP s klientem PhpRedis najdete v tématu [obslužná rutina relace php](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Co jsou databáze Redis?

Databáze Redis jsou jenom logicky oddělené data v rámci stejné instance Redis. Paměť mezipaměti je sdílena mezi všemi databázemi a skutečnou spotřebou paměti dané databáze závisí na klíčích a hodnotách uložených v této databázi. Například mezipaměť C6 má 53 GB paměti a P5 má 120 GB. Můžete zvolit, aby se všechny 53 GB/120 GB nastavily do jedné databáze, nebo je můžete rozdělit mezi více databází. 

> [!NOTE]
> Pokud používáte mezipaměť Azure Premium pro Redis s povoleným clusteringem, je k dispozici pouze databáze 0. Toto omezení je vnitřní omezení Redis a není specifické pro Azure cache pro Redis. Další informace najdete v tématu musím dělat [změny v klientské aplikaci, aby používaly clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

## <a name="next-steps"></a>Další kroky

Přečtěte si o dalších [nejčastějších dotazech k mezipaměti Azure pro Redis](cache-faq.md).