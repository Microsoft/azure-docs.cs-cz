---
title: Zprostředkovatel stavu relace ASP.NET mezipaměti | Microsoft Docs
description: Informace o ukládání stavu relace ASP.NET pomocí Azure Redis Cache
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: wesmc
ms.openlocfilehash: bb0c53433af8a679811f00bfff2efee94d211a24
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31518752"
---
# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Zprostředkovatel stavu relací ASP.NET pro Azure Redis Cache
Azure Redis Cache poskytuje poskytovatele stavu relace, který můžete použít k uložení vaší relace stavu v paměti s Redis Cache místo databázi systému SQL Server. Pokud chcete používat ukládání do mezipaměti poskytovatele stavu relace, nejdřív nakonfigurovat mezipaměť a pak nakonfigurujte aplikace ASP.NET pro mezipaměť pomocí balíčku Redis Cache relace stavu NuGet.

Je často není praktické v reálných cloudové aplikace předejdete ukládání určitou formu stavu pro uživatelské relace, ale některé přístupy vliv na výkon a škálovatelnost více než jiné. Pokud máte k uložení stavu, je nejlepším řešením zachovat malé množství stavu a ukládat v souborech cookie. Pokud se nejedná o to vhodné, další nejlepším řešením je používání stavu relace ASP.NET s poskytovatelem pro distribuované, v paměti mezipaměti. Nejhorší řešení z hlediska škálovatelnosti a výkonu, je použít databázi založenou na poskytovatele stavu relace. Toto téma obsahuje informace o použití poskytovatele stavu relace ASP.NET pro Azure Redis Cache. Informace o dalších možnostech stavu relace najdete v tématu [možnosti stavu relace ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Uložení stavu relace ASP.NET v mezipaměti
Chcete-li konfigurovat klientskou aplikaci v sadě Visual Studio pomocí balíčku Redis Cache relace stavu NuGet, klikněte na tlačítko **Správce balíčků NuGet**, **Konzola správce balíčků** z **nástroje** nabídky.

V okně `Package Manager Console` spusťte následující příkaz.
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Pokud používáte funkci clustering z úrovně premium, musíte použít [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 nebo vyšší nebo výjimku, je vyvolána. Přesun na 2.0.1 nebo novější je narušující změně; Další informace najdete v tématu [v2.0.0 nejnovější podrobností o změně](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Během této aktualizace článku je aktuální verzi tohoto balíčku 2.2.3.
> 
> 

Balíček Redis NuGet poskytovatele stavu relace má závislost na StackExchange.Redis.StrongName balíčku. Pokud balíček StackExchange.Redis.StrongName se nenachází ve vašem projektu, je nainstalovaná.

>[!NOTE]
>Kromě StackExchange.Redis.StrongName balíčku silným názvem vzrůstá také jiný silným názvem verze StackExchange.Redis. Pokud váš projekt používá StackExchange.Redis verze jiný silným názvem, že je nutné odinstalovat, v opačném případě můžete získat konflikty pojmenování ve vašem projektu. Další informace o těchto balíčcích najdete v tématu [.NET konfigurace klientů mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Balíček NuGet se stáhne a přidá požadované odkazy na sestavení a přidá do souboru web.config v následující části. Tato část obsahuje požadovanou konfiguraci pro aplikace ASP.NET pomocí poskytovatele stavu relace mezipaměti Redis.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
    <!--
    <add name="MySessionStateStore"
           host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "0" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
    />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
</sessionState>
```

V komentáři části poskytuje příklad atributů a ukázkové nastavení pro každý atribut.

Nakonfigurovat atributy s hodnotami v okně vaší mezipaměti na portálu Microsoft Azure a podle potřeby nakonfigurujte další hodnoty. Pokyny pro přístup k vlastnosti mezipaměti najdete v části [konfigurace nastavení mezipaměti Redis](cache-configure.md#configure-redis-cache-settings).

* **hostitele** – zadejte váš koncový bod mezipaměti.
* **port** – použít port bez SSL nebo vaše port SSL, v závislosti na nastavení ssl.
* **accessKey** – použít primární nebo sekundární klíč pro mezipaměť.
* **SSL** – hodnota true, pokud chcete zabezpečit komunikaci mezipaměti nebo klienta pomocí protokolu ssl; jinak hodnota false. Nezapomeňte zadat správný port.
  * Port bez SSL je ve výchozím nastavení pro nové mezipaměti zakázán. Zadejte hodnotu PRAVDA pro toto nastavení pro použití portu SSL. Další informace o povolení portu bez SSL najdete v tématu [přístupové porty](cache-configure.md#access-ports) tématu [konfigurace mezipaměti](cache-configure.md) tématu.
* **throwOnError** – hodnota true, pokud chcete, aby výjimku, která je vyvolána, pokud existuje je selhání, nebo hodnotu NEPRAVDA, pokud chcete, aby selhání Bezobslužná operace. Selhání můžete zkontrolovat zaškrtnutím statickou vlastnost Microsoft.Web.Redis.RedisSessionStateProvider.LastException. Výchozí hodnota je true.
* **retryTimeoutInMilliseconds** – operací, které selžou jsou opakovat během tohoto intervalu, zadaný v milisekundách. Po 20 milisekundách dojde k první opakování, a pak realizován v každou sekundu do vypršení platnosti retryTimeoutInMilliseconds interval opakování. Okamžitě po této době je operaci opakovat poslední jednou. Pokud se přesto nezdaří, je volajícího, v závislosti na nastavení throwOnError zpět vyvolána výjimka. Výchozí hodnota je 0 znamená bez opakování.
* **hodnotu databaseId** – Určuje databázi, kterou chcete použít pro výstupní data do mezipaměti. Pokud není zadáno, je použita výchozí hodnota 0.
* **applicationName** – klíče jsou uložené v redis jako `{<Application Name>_<Session ID>}_Data`. Toto schéma pojmenování umožňuje více aplikacím sdílet stejnou instanci Redis. Tento parametr je volitelný a pokud nezadáte, je použita výchozí hodnota.
* **connectionTimeoutInMilliseconds** – toto nastavení umožňuje potlačit connectTimeout nastavení klienta StackExchange.Redis. Pokud není zadaný, použije se výchozí nastavení connectTimeout 5000. Další informace najdete v tématu [konfigurační model StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – toto nastavení umožňuje potlačit syncTimeout nastavení klienta StackExchange.Redis. Pokud není zadaný, použije se výchozí nastavení syncTimeout 1000. Další informace najdete v tématu [konfigurační model StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Další informace o těchto vlastnostech najdete v tématu v původní blogovém příspěvku na [uvedení ASP.NET poskytovatele stavu relace pro Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Nezapomeňte si komentář standardní InProc relace stavu zprostředkovatele oddíl v souboru web.config.

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

Jakmile jsou tyto kroky provést, vaše aplikace nakonfigurována pro použití poskytovatele stavu relace mezipaměti Redis. Použijete-li stav relace v aplikaci, je uložený v instanci služby Azure Redis Cache.

> [!IMPORTANT]
> Data uložená v mezipaměti musí být serializovatelný, na rozdíl od data, která mohou být uloženy ve výchozím stavu relace ASP.NET v paměti zprostředkovatele. Při poskytovatele stavu relace pro Redis se používá, se ujistěte, že jsou Serializovatelné typy dat, které jsou uložené ve stavu relace.
> 
> 

## <a name="aspnet-session-state-options"></a>Možnosti stavu relace ASP.NET
* V zprostředkovatel stavu relace paměti – tohoto zprostředkovatele ukládá stav relace v paměti. Výhodou používání tohoto zprostředkovatele je, že se že jedná o jednoduchým a rychlým. Pokud používáte ve zprostředkovateli paměti vzhledem k tomu, že není distribuovaná, ale nemůže škálování vašich webových aplikací.
* Zprostředkovatel stavu relace serveru SQL – tohoto zprostředkovatele ukládá stav relace v systému Sql Server. Tohoto zprostředkovatele použijte, pokud chcete uložit do trvalého úložiště stavu relace. Je možné škálovat vaší webové aplikace, ale ve webové aplikaci pomocí Sql serveru pro relaci má dopad na výkon. Můžete také použít tohoto zprostředkovatele s [OLTP v paměti konfigurace](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) účelem zlepšení výkonu.
* Distribuované v paměti poskytovatele stavu relace například Redis Cache poskytovatele stavu relace – tohoto zprostředkovatele nabízí to nejlepší z obou světů. Webové aplikace může mít jednoduché, rychlé a škálovatelné poskytovatele stavu relace. Protože tento zprostředkovatel ukládá stav relace do mezipaměti, aplikace musí vzít v potaz všechny vlastnosti, které jsou spojené při posuzování do distribuované v paměti mezipaměti, jako je například selhání přechodný síťový. Osvědčené postupy týkající se použití mezipaměti najdete v části [ukládání do mezipaměti pokyny](../best-practices-caching.md) z Microsoft Patterns & postupy [Azure cloudové aplikace návrhu a implementace pokyny](https://github.com/mspnp/azure-guidance).

Další informace o stavu relace a ostatní osvědčené postupy najdete v tématu [webové vývoj osvědčených postupů (vytváření reálných cloudových aplikací s Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Další postup
Podívejte se [poskytovatel výstupní mezipaměti technologie ASP.NET pro Azure Redis Cache](cache-aspnet-output-cache-provider.md).

