---
title: Mezipaměť poskytovatel výstupní mezipaměti ASP.NET
description: Zjistěte, jak pomocí Azure Cache pro Redis výstup stránky ASP.NET do mezipaměti
services: azure-cache-for-redis
documentationcenter: na
author: yegu-ms
manager: cfowler
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: yegu
ms.openlocfilehash: 8346570b86a343dd966e69edf457bab8d6e2f4bc
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106440"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Poskytovatel výstupní mezipaměti ASP.NET pro mezipaměť Azure redis Cache
Poskytovatel výstupní mezipaměti redis cache je mechanismus úložiště mimo proces pro výstupní data v mezipaměti. Tato data jsou speciálně pro úplné odpovědi protokolu HTTP (stránce ukládání výstupu do mezipaměti). Zprostředkovatel zpřístupní nový výstupní mezipaměť zprostředkovatele rozšíření bod, která byla zavedena v rozhraní ASP.NET 4.

Poskytovatel výstupní mezipaměti Redis, nejprve nakonfigurovat mezipaměť a potom konfiguraci aplikace ASP.NET pomocí balíčku Redis NuGet poskytovatel výstupní mezipaměti. Toto téma obsahuje pokyny ke konfiguraci vaší aplikace pro použití zprostředkovatele výstupní mezipaměti Redis. Další informace o vytváření a konfiguraci Azure pro instanci Redis Cache najdete v tématu [vytvoření mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Store výstup stránky technologie ASP.NET v mezipaměti
Chcete-li konfigurovat klientskou aplikaci v sadě Visual Studio pomocí ukládání do mezipaměti Azure Redis NuGet stavu relace balíčku, klikněte na tlačítko **Správce balíčků NuGet**, **Konzola správce balíčků** z **nástroje**  nabídky.

V okně `Package Manager Console` spusťte následující příkaz.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Balíček Redis NuGet poskytovatel výstupní mezipaměti má závislost na balíčku StackExchange.Redis.StrongName. Pokud balíček StackExchange.Redis.StrongName není k dispozici ve vašem projektu, je nainstalována. Další informace o balíčku Redis NuGet zprostředkovatele mezipaměti výstupu, najdete v článku [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) stránce NuGet.

>[!NOTE]
>Kromě StackExchange.Redis.StrongName balíček silným názvem je také verze bez silným názvem StackExchange.Redis. Pokud váš projekt používá verzi StackExchange.Redis bez silným názvem, že musíte provést odinstalaci, v opačném případě můžete získat konflikty pojmenování ve vašem projektu. Další informace týkající se těchto balíčků naleznete v tématu [.NET konfigurace klientů mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Balíček NuGet stáhne a přidá odkazy na požadovaná sestavení a přidá do souboru web.config následující části. Tato část obsahuje požadované konfigurace pro aplikace ASP.NET pomocí poskytovatel výstupní mezipaměti Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <!-- For more details check https://github.com/Azure/aspnet-redis-providers/wiki -->
      <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
      <!-- 'databaseId' and 'applicationName' can be used with both options. -->
      <!--
      <add name="MyRedisOutputCache" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
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
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Komentářem část poskytuje příklad atributů a nastavení vzorku pro každý atribut.

Nakonfigurovat atributy s hodnotami z okna vaší mezipaměti na webu Microsoft Azure Portal a podle potřeby nakonfigurujte další hodnoty. Pokyny pro přístup k vlastnosti vaší mezipaměti, naleznete v tématu [konfigurace mezipaměti Azure Redis nastavení](cache-configure.md#configure-azure-cache-for-redis-settings).

* **Hostitel** – zadejte koncový bod mezipaměti.
* **port** – použít port bez SSL nebo port protokolu SSL, v závislosti na nastavení ssl.
* **accessKey** – použít primární nebo sekundární klíč pro mezipaměť.
* **SSL** – hodnota true, pokud chcete pro zabezpečení komunikace klient/mezipaměti pomocí protokolu ssl; jinak hodnota false. Nezapomeňte zadat správný port.
  * Port bez SSL je ve výchozím nastavení pro nové mezipaměti zakázán. Zadejte hodnotu PRAVDA pro toto nastavení pro použití portu SSL. Další informace o povolení portu bez SSL, najdete v článku [přístupové porty](cache-configure.md#access-ports) tématu [konfigurace mezipaměti](cache-configure.md) tématu.
* **ID databáze** – zadanou databázi, kterou má použít pro mezipaměť výstupní data. Pokud není zadán, je použita výchozí hodnota 0.
* **applicationName** – klíče jsou uložené v redis jako `<AppName>_<SessionId>_Data`. Toto schéma pojmenování umožňuje více aplikacím sdílet stejný klíč. Tento parametr je nepovinný a pokud nezadáte ji použije se výchozí hodnota.
* **connectionTimeoutInMilliseconds** – toto nastavení umožňuje potlačit connectTimeout nastavení klienta StackExchange.Redis. Pokud není zadán, se používá ve výchozím nastavení connectTimeout 5000. Další informace najdete v tématu [konfigurační model StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – toto nastavení umožňuje potlačit syncTimeout nastavení klienta StackExchange.Redis. Pokud není zadán, se používá ve výchozím nastavení syncTimeout 1000. Další informace najdete v tématu [konfigurační model StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).

Přidejte direktivu OutputCache na každou stránku, pro kterou chcete výstupu do mezipaměti.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

V předchozím příkladu stránky v mezipaměti data zůstanou v mezipaměti po dobu 60 sekund, a jinou verzi na stránce se uloží do mezipaměti pro každou kombinaci parametrů. Další informace o direktivě OutputCache najdete v tématu [ @OutputCache ](https://go.microsoft.com/fwlink/?linkid=320837).

Jakmile jsou tyto kroky provést, vaše aplikace nakonfigurována pro použití zprostředkovatele výstupní mezipaměti Redis.

## <a name="next-steps"></a>Další postup
Podívejte se [zprostředkovatel stavu relací ASP.NET pro Azure Cache pro Redis](cache-aspnet-session-state-provider.md).

