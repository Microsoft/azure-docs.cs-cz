---
title: Mezipaměť poskytovatel výstupní mezipaměti ASP.NET
description: Naučte se mezipaměť výstupu stránky ASP.NET pomocí Azure Redis Cache
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: wesmc
ms.openlocfilehash: 81c95949971d54833ca7a15ec5148116c94767f7
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27909818"
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Poskytovatel výstupní mezipaměti ASP.NET pro Azure Redis Cache
Poskytovatel výstupní mezipaměti Redis je mechanismus mimo proces úložiště pro data do výstupní mezipaměti. Tato data jsou speciálně pro úplné odpovědi protokolu HTTP (stránka ukládání výstupu do mezipaměti). Zprostředkovatel připojuje do nový výstupní mezipaměti poskytovatele rozšíření bod byla zavedena v technologii ASP.NET 4.

Pokud chcete používat poskytovatel výstupní mezipaměti Redis, nejdřív nakonfigurovat mezipaměť a pak nakonfigurujte vaši aplikaci ASP.NET pomocí balíčku Redis NuGet poskytovatel výstupní mezipaměti. Toto téma obsahuje pokyny týkající se konfigurace aplikace k používání poskytovatel výstupní mezipaměti Redis. Další informace o vytváření a konfiguraci instanci služby Azure Redis Cache najdete v tématu [vytvoření mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Ukládání výstupu stránek ASP.NET do mezipaměti
Chcete-li konfigurovat klientskou aplikaci v sadě Visual Studio pomocí balíčku Redis Cache relace stavu NuGet, klikněte na tlačítko **Správce balíčků NuGet**, **Konzola správce balíčků** z **nástroje** nabídky.

V okně `Package Manager Console` spusťte následující příkaz.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Balíček Redis NuGet poskytovatel výstupní mezipaměti má závislost na StackExchange.Redis.StrongName balíčku. Pokud balíček StackExchange.Redis.StrongName se nenachází ve vašem projektu, je nainstalovaná. Další informace o balíčku pro Redis NuGet poskytovatel výstupní mezipaměti najdete v tématu [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet stránky.

>[!NOTE]
>Kromě StackExchange.Redis.StrongName balíčku silným názvem vzrůstá také jiný silným názvem verze StackExchange.Redis. Pokud váš projekt používá StackExchange.Redis verze jiný silným názvem, že je nutné odinstalovat, v opačném případě můžete získat konflikty pojmenování ve vašem projektu. Další informace o těchto balíčcích najdete v tématu [.NET konfigurace klientů mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Balíček NuGet se stáhne a přidá požadované odkazy na sestavení a přidá do souboru web.config v následující části. Tato část obsahuje požadovanou konfiguraci pro aplikace ASP.NET pomocí poskytovatel výstupní mezipaměti Redis.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!--
      <add name="MyRedisOutputCache"
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

V komentáři části poskytuje příklad atributů a ukázkové nastavení pro každý atribut.

Nakonfigurovat atributy s hodnotami v okně vaší mezipaměti na portálu Microsoft Azure a podle potřeby nakonfigurujte další hodnoty. Pokyny pro přístup k vlastnosti mezipaměti najdete v části [konfigurace nastavení mezipaměti Redis](cache-configure.md#configure-redis-cache-settings).

* **hostitele** – zadejte váš koncový bod mezipaměti.
* **port** – použít port bez SSL nebo vaše port SSL, v závislosti na nastavení ssl.
* **accessKey** – použít primární nebo sekundární klíč pro mezipaměť.
* **SSL** – hodnota true, pokud chcete zabezpečit komunikaci mezipaměti nebo klienta pomocí protokolu ssl; jinak hodnota false. Nezapomeňte zadat správný port.
  * Port bez SSL je ve výchozím nastavení pro nové mezipaměti zakázán. Zadejte hodnotu PRAVDA pro toto nastavení pro použití portu SSL. Další informace o povolení portu bez SSL najdete v tématu [přístupové porty](cache-configure.md#access-ports) tématu [konfigurace mezipaměti](cache-configure.md) tématu.
* **hodnotu databaseId** – zadanou databázi, kterou chcete použít pro mezipaměť výstupní data. Pokud není zadáno, je použita výchozí hodnota 0.
* **applicationName** – klíče jsou uložené v redis jako `<AppName>_<SessionId>_Data`. Toto schéma pojmenování umožňuje více aplikacím sdílet stejný klíč. Tento parametr je volitelný a pokud nezadáte, je použita výchozí hodnota.
* **connectionTimeoutInMilliseconds** – toto nastavení umožňuje potlačit connectTimeout nastavení klienta StackExchange.Redis. Pokud není zadaný, použije se výchozí nastavení connectTimeout 5000. Další informace najdete v tématu [konfigurační model StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – toto nastavení umožňuje potlačit syncTimeout nastavení klienta StackExchange.Redis. Pokud není zadaný, použije se výchozí nastavení syncTimeout 1000. Další informace najdete v tématu [konfigurační model StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Přidáte direktivu OutputCache na každé stránce, pro kterou chcete výstupu do mezipaměti.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

V předchozím příkladu data uložená v mezipaměti stránky zůstává v mezipaměti po dobu 60 sekund, a jinou verzi stránky se uloží do mezipaměti pro každou kombinaci parametrů. Další informace o direktivu OutputCache najdete v tématu [ @OutputCache ](http://go.microsoft.com/fwlink/?linkid=320837).

Jakmile jsou tyto kroky provést, vaše aplikace nakonfigurována pro použití poskytovatel výstupní mezipaměti Redis.

## <a name="next-steps"></a>Další postup
Podívejte se [poskytovatele stavu relace ASP.NET pro Azure Redis Cache](cache-aspnet-session-state-provider.md).

