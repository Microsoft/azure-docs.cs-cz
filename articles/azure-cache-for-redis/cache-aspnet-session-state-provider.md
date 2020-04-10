---
title: Zprostředkovatel stavu relace ASP.NET mezipaměti
description: Zjistěte, jak ukládat ASP.NET stavu relace v paměti pomocí Azure Cache pro Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 8083efe833ec80290713fc14d9cb89acd8263fa2
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010898"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Zprostředkovatel stavu relací ASP.NET pro Azure Cache for Redis

Azure Cache for Redis poskytuje poskytovatele stavu relace, který můžete použít k ukládání stavu relace v paměti s Azure Cache pro Redis namísto databáze SQL Serveru. Chcete-li použít zprostředkovatele stavu mezipaměti, nejprve nakonfigurujte mezipaměť a potom nakonfigurujte ASP.NET aplikaci pro mezipaměť pomocí balíčku NuGet stavu relace Azure Cache for Redis.

Často není praktické v reálné cloudové aplikaci, aby se zabránilo ukládání nějaké formy stavu pro relaci uživatele, ale některé přístupy mají vliv na výkon a škálovatelnost více než ostatní. Pokud máte uložit stav, nejlepším řešením je, aby množství stavu malé a uložit jej do cookies. Pokud to není možné, dalším nejlepším řešením je použít ASP.NET stavu relace s poskytovatelem pro distribuované mezipaměti v paměti. Nejhorší řešení z hlediska výkonu a škálovatelnosti je použít zprostředkovatele stavu relace podporované databází. Toto téma obsahuje pokyny k použití zprostředkovatele stavu relace ASP.NET pro Azure Cache for Redis. Informace o dalších možnostech stavu relace naleznete [v tématu ASP.NET možnosti stavu relace](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Uložení stavu relace ASP.NET v mezipaměti

Chcete-li nakonfigurovat klientskou aplikaci v sadě Visual Studio pomocí balíčku NuGet stavu relace Azure Cache for Redis, klepněte na položku **NuGet Package Manager**, **Console správce balíčků** z nabídky **Nástroje.**

V okně `Package Manager Console` spusťte následující příkaz.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Pokud používáte funkci clusteringu z úrovně premium, musíte použít [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 nebo vyšší nebo je vyvolána výjimka. Přechod na 2.0.1 nebo vyšší je zlomová změna; Další informace naleznete [v tématu podrobnosti o změně změny v 2.0.0](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). V době aktualizace tohoto článku aktuální verze tohoto balíčku je 2.2.3.
> 
> 

Balíček NuGet, který se stavu relace Redis, je závislý na balíčku StackExchange.Redis.StrongName. Pokud StackExchange.Redis.StrongName balíček není k dispozici v projektu, je nainstalován.

>[!NOTE]
>Kromě balíčku stackexchange.redis.strongname se silným názvem stackexchange.redis.strongname balíček, je také StackExchange.Redis bez silné pojmenované verze. Pokud váš projekt používá verzi stackexchange.redis bez silného názvu, musíte ji odinstalovat, jinak se v projektu dostanou konflikty názvů. Další informace o těchto balíčcích naleznete v [tématu Konfigurace klientů mezipaměti .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Balíček NuGet se stáhne a přidá požadované odkazy na sestavení a přidá následující část do souboru web.config. Tato část obsahuje požadovanou konfiguraci pro vaše ASP.NET aplikace k použití Azure Cache pro zprostředkovatele stavu relace Redis.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

Komentář část obsahuje příklad atributů a ukázkové nastavení pro každý atribut.

Nakonfigurujte atributy s hodnotami z okna mezipaměti na portálu Microsoft Azure a podle potřeby nakonfigurujte další hodnoty. Pokyny k přístupu ke vlastnostem mezipaměti najdete [v tématu Konfigurace mezipaměti Azure pro nastavení Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** – zadejte koncový bod mezipaměti.
* **port** – v závislosti na nastavení TLS použijte port bez TLS/SSL nebo port TLS/SSL.
* **accessKey** – použijte primární nebo sekundární klíč pro vaši mezipaměť.
* **ssl** – true, pokud chcete zabezpečit komunikaci mezipaměť/klienta s TLS; jinak nepravdivé. Nezapomeňte zadat správný port.
  * Port netls je ve výchozím nastavení zakázán pro nové mezipaměti. Zadejte hodnotu true pro toto nastavení, chcete-li použít port TLS. Další informace o povolení portu bez tls naleznete v části [Přístupové porty](cache-configure.md#access-ports) v [tématu Konfigurace mezipaměti.](cache-configure.md)
* **throwOnError** – true, pokud chcete, aby byla vyvolána výjimka, pokud došlo k chybě, nebo false, pokud chcete, aby operace selhat tiše. Chybu můžete zkontrolovat kontrolou statické vlastnosti Microsoft.Web.Redis.RedisSessionStateProvider.LastException. Výchozí hodnota je true.
* **retryTimeoutInMilliseconds** – operace, které se nezdaří, jsou opakovány během tohoto intervalu, zadané v milisekundách. První opakování dojde po 20 milisekund ách a pak opakování dojít každou sekundu, dokud interval retryTimeoutInMilliseconds vyprší. Ihned po tomto intervalu je operace zopakována naposledy. Pokud operace stále selže, je výjimka vyvolána zpět volajícímu, v závislosti na nastavení throwOnError. Výchozí hodnota je 0, což znamená žádné opakování.
* **databaseId** – Určuje, která databáze se má použít pro výstupní data mezipaměti. Pokud není zadán, použije se výchozí hodnota 0.
* **applicationName** – Klíče jsou uloženy v redis jako `{<Application Name>_<Session ID>}_Data`. Toto schéma pojmenování umožňuje více aplikacím sdílet stejnou instanci Redis. Tento parametr je volitelný a pokud jej nezadáte, použije se výchozí hodnota.
* **connectionTimeoutInMilliseconds** – Toto nastavení umožňuje přepsat nastavení connectTimeout v klientovi StackExchange.Redis. Pokud není zadán, použije se výchozí nastavení connectTimeout 5000. Další informace naleznete v tématu [StackExchange.Redis model konfigurace](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – Toto nastavení umožňuje přepsat nastavení syncTimeout v klientovi StackExchange.Redis. Pokud není zadán, výchozí syncTimeout nastavení 1000 se používá. Další informace naleznete v tématu [StackExchange.Redis model konfigurace](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** - Toto nastavení umožňuje zadat vlastní serializaci obsahu relace, který je odeslán redis. Zadaný typ `Microsoft.Web.Redis.ISerializer` musí implementovat a musí deklarovat veřejný konstruktor bez parametrů. Ve `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` výchozím nastavení se používá.

Další informace o těchto vlastnostech naleznete v původním oznámení příspěvku blogu na [konferenci ASP.NET zprostředkovatele stavu relace pro redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Nezapomeňte komentovat standardní část poskytovatele stavu relace InProc ve vašem web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Po provedení těchto kroků se vaše aplikace nakonfiguruje tak, aby používala zprostředkovatele stavu relace Azure Cache for Redis. Při použití stavu relace ve vaší aplikaci, je uloženv azure cache pro redis instance.

> [!IMPORTANT]
> Data uložená v mezipaměti musí být serializovatelná, na rozdíl od dat, která mohou být uložena ve výchozím ASP.NET zprostředkovateli stavu relace v paměti. Při použití zprostředkovatele stavu relace pro Redis, ujistěte se, že datové typy, které jsou uloženy ve stavu relace jsou serializovatelné.
> 
> 

## <a name="aspnet-session-state-options"></a>možnosti stavu relace ASP.NET

* Zprostředkovatel stavu relace paměti - Tento zprostředkovatel ukládá stav relace do paměti. Výhodou použití tohoto poskytovatele je, že je jednoduchý a rychlý. Webové aplikace však nelze škálovat, pokud používáte v zprostředkovateli paměti, protože nejsou distribuovány.
* Zprostředkovatel stavu relace serveru SQL Server – tento zprostředkovatel ukládá stav relace na serveru SQL Server. Tento zprostředkovatel použijte, pokud chcete uložit stav relace v trvalém úložišti. Můžete škálovat webovou aplikaci, ale použití Sql Server pro relaci má vliv na výkon webové aplikace. Tohoto zprostředkovatele můžete také použít s [konfigurací OLTP v paměti,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) které pomáhají zlepšit výkon.
* Distribuovaný v paměti stavu relace zprostředkovatele, jako je například Azure Cache pro zprostředkovatele stavu relace Redis – Tento zprostředkovatel poskytuje to nejlepší z obou světů. Webová aplikace může mít jednoduchého, rychlého a škálovatelného poskytovatele stavu relace. Vzhledem k tomu, že tento zprostředkovatel ukládá stav relace do mezipaměti, musí vaše aplikace vzít v úvahu všechny charakteristiky přidružené při mluvení do mezipaměti distribuované v paměti, jako jsou například přechodná selhání sítě. Doporučené postupy při používání mezipaměti najdete v [tématu Pokyny pro ukládání do mezipaměti](../best-practices-caching.md) od společnosti Microsoft Patterns & Practices [Azure Cloud Application Design and Implementation Guidance](https://github.com/mspnp/azure-guidance).

Další informace o stavu relace a dalších doporučených postupech najdete [v tématu Doporučené postupy pro vývoj webu (Vytváření reálných cloudových aplikací s Azure).](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)

## <a name="third-party-session-state-providers"></a>Poskytovatelé stavu relace třetích stran

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache vznítit](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Další kroky

Podívejte se na [zprostředkovatele ASP.NET výstupní mezipaměti pro Azure Cache for Redis](cache-aspnet-output-cache-provider.md).
