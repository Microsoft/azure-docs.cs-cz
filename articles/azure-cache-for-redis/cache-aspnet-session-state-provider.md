---
title: Zprostředkovatel stavu relací ASP.NET mezipaměti | Dokumentace Microsoftu
description: Zjistěte, jak díky ukládání stavu relací ASP.NET pro Redis pomocí Azure Cache
services: azure-cache-for-redis
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: wesmc
ms.openlocfilehash: c9b843bfd448c05cb2b11165e54d19db0a7ad60e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019387"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Zprostředkovatel stavu relací ASP.NET pro mezipaměť Azure redis Cache
Mezipaměť Azure pro Redis poskytuje zprostředkovatel stavu relací, které vám umožní ukládat vaše relace stavu v paměti s mezipamětí Azure Redis místo databáze systému SQL Server. Použití ukládání do mezipaměti zprostředkovatel stavu relací, nejprve nakonfigurovat mezipaměť a potom konfiguraci aplikace ASP.NET pro použití ukládání do mezipaměti Azure pro balíček NuGet stavu relace. Redis cache.

Je často nepraktické ve skutečných cloudových aplikací, aby ukládání nějakou formu stav uživatelských relací, ale některé přístupy, vliv na výkon a škálovatelnost více než jiné. Pokud máte k uložení stavu, nejlepším řešením je zachovat malé množství stavu a uloží je v souborech cookie. Pokud to není proveditelné, další nejlepším řešením je používání stavu relace ASP.NET u poskytovatele pro distribuované mezipaměti v paměti. Nejhorší řešení z pohledu škálovatelnosti a výkonu je pro použití jiné databáze zajišťuje zprostředkovatel stavu relací. Toto téma obsahuje informace o použití zprostředkovatel stavu relací ASP.NET pro Azure Cache pro Redis. Informace o dalších možnostech stavu relace najdete v tématu [parametry stavu relace ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Uložení stavu relace ASP.NET v mezipaměti
Chcete-li konfigurovat klientskou aplikaci v sadě Visual Studio pomocí ukládání do mezipaměti Azure Redis NuGet stavu relace balíčku, klikněte na tlačítko **Správce balíčků NuGet**, **Konzola správce balíčků** z **nástroje**  nabídky.

V okně `Package Manager Console` spusťte následující příkaz.
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Pokud používáte funkci clusteringu z úrovně premium, je nutné použít [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 nebo vyšší nebo výjimka je vyvolána výjimka. Přechod na 2.0.1 nebo vyšší je zásadní změnu; Další informace najdete v tématu [v2.0.0 nejnovější podrobnosti o změnách](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Během této aktualizace článku je aktuální verze tohoto balíčku 2.2.3.
> 
> 

Balíček Redis NuGet zprostředkovatele stavu relace má závislost na balíčku StackExchange.Redis.StrongName. Pokud balíček StackExchange.Redis.StrongName není k dispozici ve vašem projektu, je nainstalována.

>[!NOTE]
>Kromě StackExchange.Redis.StrongName balíček silným názvem je také verze bez silným názvem StackExchange.Redis. Pokud váš projekt používá verzi StackExchange.Redis bez silným názvem, že musíte provést odinstalaci, v opačném případě můžete získat konflikty pojmenování ve vašem projektu. Další informace týkající se těchto balíčků naleznete v tématu [.NET konfigurace klientů mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Balíček NuGet stáhne a přidá odkazy na požadovaná sestavení a přidá do souboru web.config následující části. Tato část obsahuje požadované konfigurace pro vaši aplikaci ASP.NET pro účely ukládání do mezipaměti Azure redis Cache zprostředkovatel stavu relací.

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
</sessionState>
```

Komentářem část poskytuje příklad atributů a nastavení vzorku pro každý atribut.

Nakonfigurovat atributy s hodnotami z okna vaší mezipaměti na webu Microsoft Azure Portal a podle potřeby nakonfigurujte další hodnoty. Pokyny pro přístup k vlastnosti vaší mezipaměti, naleznete v tématu [konfigurace mezipaměti Azure Redis nastavení](cache-configure.md#configure-azure-cache-for-redis-settings).

* **Hostitel** – zadejte koncový bod mezipaměti.
* **port** – použít port bez SSL nebo port protokolu SSL, v závislosti na nastavení ssl.
* **accessKey** – použít primární nebo sekundární klíč pro mezipaměť.
* **SSL** – hodnota true, pokud chcete pro zabezpečení komunikace klient/mezipaměti pomocí protokolu ssl; jinak hodnota false. Nezapomeňte zadat správný port.
  * Port bez SSL je ve výchozím nastavení pro nové mezipaměti zakázán. Zadejte hodnotu PRAVDA pro toto nastavení pro použití portu SSL. Další informace o povolení portu bez SSL, najdete v článku [přístupové porty](cache-configure.md#access-ports) tématu [konfigurace mezipaměti](cache-configure.md) tématu.
* **throwOnError** – hodnota true, pokud chcete, aby výjimky vyvolána, pokud je selhání, nebo false, pokud chcete, aby selhání Bezobslužná operace. Selhání můžete zkontrolovat tak, že zkontrolujete vlastnost Microsoft.Web.Redis.RedisSessionStateProvider.LastException statické. Výchozí hodnota je true.
* **retryTimeoutInMilliseconds** – operací, které selžou se zopakují během tohoto intervalu zadává v milisekundách. Po 20 MS dojde k prvním opakováním a pak probíhají každou sekundu do vypršení platnosti retryTimeoutInMilliseconds interval opakování. Okamžitě po uplynutí tohoto intervalu je operaci opakovat poslední jednou. Pokud i nadále nezdaří, je volajícímu, v závislosti na nastavení throwOnError zpět vyvolána výjimka. Výchozí hodnota je 0, což znamená, že žádné opakování.
* **ID databáze** – Určuje databázi, kterou chcete použít pro mezipaměť výstupní data. Pokud není zadán, je použita výchozí hodnota 0.
* **applicationName** – klíče jsou uložené v redis jako `{<Application Name>_<Session ID>}_Data`. Toto schéma pojmenování umožňuje více aplikacím sdílet stejný instance Redis. Tento parametr je nepovinný a pokud nezadáte ji použije se výchozí hodnota.
* **connectionTimeoutInMilliseconds** – toto nastavení umožňuje potlačit connectTimeout nastavení klienta StackExchange.Redis. Pokud není zadán, se používá ve výchozím nastavení connectTimeout 5000. Další informace najdete v tématu [konfigurační model StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – toto nastavení umožňuje potlačit syncTimeout nastavení klienta StackExchange.Redis. Pokud není zadán, se používá ve výchozím nastavení syncTimeout 1000. Další informace najdete v tématu [konfigurační model StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** – toto nastavení umožňuje určit vlastní serializace relace obsah odeslaný do Redisu. Zadaný typ musí implementovat `Microsoft.Web.Redis.ISerializer` a musí deklarovat konstruktor public bez parametrů. Ve výchozím nastavení `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` se používá.

Další informace o těchto vlastnostech najdete v tématu původní blogovém příspěvku o na [oznamujeme zprostředkovatel stavu relací ASP.NET pro Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Nezapomeňte okomentovat standardní InProc relace stav poskytovatele oddílu v souboru web.config.

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

Jakmile jsou tyto kroky provést, je aplikace nakonfigurována pro účely ukládání do mezipaměti Azure redis Cache zprostředkovatel stavu relací. Při používání stavu relace ve vaší aplikaci, uloží se do mezipaměti Azure pro Redis instance.

> [!IMPORTANT]
> Data uložená v mezipaměti musí být serializovatelný, na rozdíl od data, která mohou být uloženy ve výchozím stavu relace ASP.NET v paměti zprostředkovatele. Při zprostředkovatel stavu relací pro Redis se používá, se ujistěte, že datové typy, které ukládají stav relací jsou serializovatelné.
> 
> 

## <a name="aspnet-session-state-options"></a>Parametry stavu relace ASP.NET
* Zprostředkovatele stavu relace paměti – tohoto zprostředkovatele ukládat stavy relací v paměti. Výhodou používání tohoto poskytovatele je, že je snadné a rychlé. Ale už nebude možné škálovat svoje aplikace, pokud používáte ve zprostředkovateli paměti, protože není distribuován.
* Zprostředkovatel stavu relací SQL Server – tohoto zprostředkovatele ukládá stav relace v systému Sql Server. Pokud chcete k ukládání stavu relace do odolného úložiště, používání tohoto poskytovatele. Je možné škálovat webové aplikace, ale ve webové aplikaci pomocí Sql serveru pro relaci má dopad na výkon. Můžete použít také s tohoto zprostředkovatele [konfigurace OLTP v paměti](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) jí pomohou zlepšit výkon.
* Distribuované v paměti zprostředkovatel stavu relací například mezipaměti Azure redis Cache zprostředkovatel stavu relací – tohoto zprostředkovatele nabízí to nejlepší z obou světů. Webové aplikace může mít jednoduché, rychlé a škálovatelné zprostředkovatel stavu relací. Vzhledem k tomu, že tento zprostředkovatel je ukládá stav relace v mezipaměti, musí aplikace vzít v potaz všechny charakteristiky související upozorňovat distribuované v mezipaměti, jako je například přechodných síťových chyb. Osvědčené postupy k používání mezipaměti, naleznete v tématu [ukládání do mezipaměti](../best-practices-caching.md) z Microsoft Patterns a postupy [návrh aplikace cloudu Azure a pokyny k implementaci](https://github.com/mspnp/azure-guidance).

Další informace o stavu relace a doporučené postupy najdete v tématu [osvědčené postupy při vývoji webové (sestavování skutečných cloudových aplikací s Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Další postup
Podívejte se [zprostředkovateli výstupní mezipaměti ASP.NET pro Azure Cache pro Redis](cache-aspnet-output-cache-provider.md).

