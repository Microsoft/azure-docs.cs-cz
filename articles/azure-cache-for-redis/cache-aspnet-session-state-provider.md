---
title: Zprostředkovatel stavu relací ASP.NET mezipaměti
description: Naučte se ukládat stav relace ASP.NET v paměti pomocí Azure cache pro Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 05/01/2017
ms.openlocfilehash: ce77f5074d707da5cfb251a103653b96e4644b5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92544524"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Zprostředkovatel stavu relací ASP.NET pro Azure Cache for Redis

Azure cache pro Redis poskytuje zprostředkovatele stavu relace, který můžete použít k uložení stavu relace v paměti s Azure cache pro Redis namísto databáze SQL Server. Pokud chcete použít poskytovatele stavu relace do mezipaměti, nejdřív nakonfigurujte mezipaměť a pak nakonfigurujte aplikaci ASP.NET pro mezipaměť pomocí balíčku NuGet pro stav relace Azure cache for Redis. V případě aplikací ASP.NET Core, přečtěte si téma [Správa relace a stavu v ASP.NET Core](/aspnet/core/fundamentals/app-state).

V reálné cloudové aplikaci není často praktické, aby se zabránilo ukládání určitého stavu pro relaci uživatele, ale některé přístupy mají dopad na výkon a škálovatelnost více než jiných. Pokud je třeba uložit stav, nejlepším řešením je zachovat malý objem a uložit ho do souborů cookie. Pokud to není proveditelné, příští nejlepší řešení je použití stavu relace ASP.NET se zprostředkovatelem pro distribuovanou mezipaměť v paměti. Nejhorším řešením z hlediska výkonu a škálovatelnosti je použití zprostředkovatele stavu relace zálohovaného databáze. Toto téma poskytuje pokyny k používání zprostředkovatele stavu relace ASP.NET pro Azure cache pro Redis. Informace o dalších možnostech stavu relace najdete v tématu [Možnosti stavu relace ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Uložení stavu relace ASP.NET v mezipaměti

Chcete-li konfigurovat klientskou aplikaci v aplikaci Visual Studio pomocí balíčku NuGet pro stav relace Azure cache for Redis, klikněte na **Správce balíčků NuGet**, **Konzola správce balíčků** v nabídce **nástroje** .

V okně `Package Manager Console` spusťte následující příkaz.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Pokud používáte funkci clusteringu z úrovně Premium, musíte použít [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 nebo vyšší nebo je vyvolána výjimka. Přesunutí na 2.0.1 nebo vyšší je zásadní změna. Další informace najdete v části [v tématu 2.0.0 – Podrobnosti o přerušující změny](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). V době aktualizace tohoto článku je aktuální verze tohoto balíčku 2.2.3.
> 
> 

Balíček NuGet zprostředkovatele stavu relace Redis má závislost na balíčku StackExchange. Redis. StrongName. Pokud se v projektu nenachází balíček StackExchange. Redis. StrongName, je nainstalován.

>[!NOTE]
>Kromě balíčku StackExchange. Redis. StrongName se silným názvem existuje také StackExchange. Redis verze, která není silně pojmenována. Pokud váš projekt používá verzi StackExchange. Redis, která není silně pojmenována, musíte ji odinstalovat, jinak se v projektu zobrazí konflikty názvů. Další informace o těchto balíčcích najdete v tématu [Konfigurace klientů mezipaměti rozhraní .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Balíček NuGet stáhne a přidá požadované odkazy na sestavení a přidá následující oddíl do souboru web.config. Tato část obsahuje požadovanou konfiguraci pro vaši aplikaci ASP.NET, aby používala službu Azure cache pro poskytovatele stavu relace Redis.

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

Oddíl s komentářem poskytuje příklad atributů a ukázkových nastavení pro každý atribut.

Nakonfigurujte atributy s hodnotami z okna cache v portál Microsoft Azure a podle potřeby nakonfigurujte další hodnoty. Pokyny k přístupu k vlastnostem mezipaměti najdete v tématu [Konfigurace nastavení služby Azure cache pro Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **hostitel** – Zadejte koncový bod mezipaměti.
* **port** – v závislosti na nastavení TLS použijte port bez TLS/SSL nebo port TLS/SSL.
* **AccessKey** – pro mezipaměť použijte primární nebo sekundární klíč.
* **SSL** – hodnota true, pokud chcete zabezpečenou komunikaci s protokolem TLS s mezipamětí a klientem v opačném případě false. Ujistěte se, že jste zadali správný port.
  * Port bez protokolu TLS je ve výchozím nastavení pro nové mezipaměti zakázán. Pro toto nastavení zadejte hodnotu true, pokud chcete použít port TLS. Další informace o povolení portu bez TLS najdete v části [přístupové porty](cache-configure.md#access-ports) v tématu [Konfigurace mezipaměti](cache-configure.md) .
* **parametr throwOnError** – hodnota true, pokud chcete vyvolat výjimku, pokud dojde k chybě, nebo false, pokud chcete, aby se operace v tichém režimu nezdařila. Selhání můžete zkontrolovat tak, že zkontrolujete statickou vlastnost Microsoft. Web. Redis. RedisSessionStateProvider. LastException. Výchozí hodnota je true.
* **retryTimeoutInMilliseconds** – operace, u kterých dojde k selhání, se v průběhu tohoto intervalu zopakují, a to v milisekundách. K prvnímu opakování dojde po 20 milisekundách a pak opakování proběhne každou sekundu, dokud nevyprší interval retryTimeoutInMilliseconds. Ihned po tomto intervalu se operace opakuje po jednom konečném čase. Pokud se operace stále nedaří, výjimka se vrátí volajícímu v závislosti na nastavení parametr throwOnError. Výchozí hodnota je 0, což znamená žádné opakování.
* **DatabaseID** – určuje, která databáze se má použít pro výstupní data mezipaměti. Pokud není zadaný, použije se výchozí hodnota 0.
* **ApplicationName** – klíče jsou uloženy v Redis jako `{<Application Name>_<Session ID>}_Data` . Toto schéma pojmenování umožňuje více aplikacím sdílet stejnou instanci Redis. Tento parametr je nepovinný a pokud nezadáte, použije se výchozí hodnota.
* **connectionTimeoutInMilliseconds** – toto nastavení umožňuje přepsat nastavení connectTimeout v klientovi stackexchange. Redis. Pokud není zadaný, použije se výchozí nastavení connectTimeout 5000. Další informace najdete v tématu [konfigurační model stackexchange. Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – toto nastavení umožňuje přepsat nastavení syncTimeout v klientovi stackexchange. Redis. Pokud není zadaný, použije se výchozí nastavení syncTimeout 1000. Další informace najdete v tématu [konfigurační model stackexchange. Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** – toto nastavení umožňuje zadat vlastní serializaci obsahu relace, který je odeslán do Redis. Zadaný typ musí implementovat `Microsoft.Web.Redis.ISerializer` a musí deklarovat veřejný konstruktor bez parametrů. Ve výchozím nastavení  `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` se používá.

Další informace o těchto vlastnostech najdete v původním příspěvku na blogu v oznámení [o poskytovateli stavu relace ASP.NET pro Redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).

Nezapomeňte ve svém web.config komentovat oddíl standardního zprostředkovatele stavu relace InProc.

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

Po provedení těchto kroků je vaše aplikace nakonfigurovaná tak, aby používala službu Azure cache pro poskytovatele stavu relace Redis. Když ve své aplikaci použijete stav relace, je uložený v mezipaměti Azure pro instanci Redis.

> [!IMPORTANT]
> Data uložená v mezipaměti musí být serializovatelný, na rozdíl od dat, která mohou být uložena ve výchozím poskytovateli stavu relace v paměti ASP.NET. Pokud se používá zprostředkovatel stavu relací pro Redis, ujistěte se, že datové typy, které jsou uložené ve stavu relace, jsou serializovatelné.
> 
> 

## <a name="aspnet-session-state-options"></a>Možnosti stavu relace ASP.NET

* Ve zprostředkovateli stavu relace paměti – tento poskytovatel ukládá stav relace do paměti. Výhodou použití tohoto poskytovatele je jednoduché a rychlé. Nemůžete však škálovat Web Apps, pokud používáte v poskytovateli paměti, protože není distribuován.
* Zprostředkovatel stavu relace systému SQL Server – tento poskytovatel ukládá stav relace do systému SQL Server. Tento zprostředkovatel použijte, pokud chcete uložit stav relace do trvalého úložiště. Můžete škálovat webovou aplikaci, ale použití SQL serveru pro relaci má dopad na výkon vaší webové aplikace. Tohoto poskytovatele můžete také použít spolu s [konfigurací OLTP v paměti](/archive/blogs/sqlserverstorageengine/asp-net-session-state-with-sql-server-in-memory-oltp) , což pomáhá zlepšit výkon.
* Distribuované ve zprostředkovateli stavu relace paměti, jako je Azure cache pro poskytovatele stavu relace Redis – tento poskytovatel vám dává nejlepší z obou světů. Vaše webová aplikace může mít jednoduchého, rychlého a škálovatelného poskytovatele stavu relace. Vzhledem k tomu, že tento poskytovatel ukládá stav relace do mezipaměti, musí aplikace brát v úvahu všechny vlastnosti přidružené při komunikaci s distribuovanou mezipamětí, jako je například přechodná chyba sítě. Osvědčené postupy při používání mezipaměti najdete v tématu [pokyny k ukládání do mezipaměti](/azure/architecture/best-practices/caching) z Microsoft patterns & postupy pro [navrhování cloudových aplikací Azure a pokyny k implementaci](https://github.com/mspnp/azure-guidance).

Další informace o stavu relace a dalších osvědčených postupech najdete v tématu [osvědčené postupy pro Web Development (vytváření Real-World cloudových aplikací s Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="third-party-session-state-providers"></a>Zprostředkovatelé stavu relací třetích stran

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Další kroky

Podívejte se na [zprostředkovatele výstupní mezipaměti ASP.NET pro Azure cache pro Redis](cache-aspnet-output-cache-provider.md).