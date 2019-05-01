---
title: Poskytovatel výstupní mezipaměti ASP.NET pro mezipaměť Azure redis Cache
description: Zjistěte, jak pomocí Azure Cache pro Redis výstup stránky ASP.NET do mezipaměti
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943877"
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
>Kromě StackExchange.Redis.StrongName balíček silným názvem je také verze bez silným názvem StackExchange.Redis. Pokud váš projekt používá verzi StackExchange.Redis bez silným názvem je nutné odinstalovat. v opačném případě bude docházet ke konfliktům s názvy ve vašem projektu. Další informace týkající se těchto balíčků naleznete v tématu [.NET konfigurace klientů mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Balíček NuGet stáhne a přidá odkazy na požadovaná sestavení a přidá do souboru web.config následující části. Tato část obsahuje požadované konfigurace pro aplikace ASP.NET pomocí poskytovatel výstupní mezipaměti Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Nakonfigurovat atributy s hodnotami z okna vaší mezipaměti na webu Microsoft Azure Portal a podle potřeby nakonfigurujte další hodnoty. Pokyny pro přístup k vlastnosti vaší mezipaměti, naleznete v tématu [konfigurace mezipaměti Azure Redis nastavení](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atribut | Type | Výchozí | Popis |
| --------- | ---- | ------- | ----------- |
| *host* | string | "localhost" | Redis serveru IP adresu nebo název hostitele |
| *Port* | kladné celé číslo | 6379 (bez SSL)<br/>6380 (SSL) | Port serveru redis |
| *accessKey* | string | "" | Heslo serveru redis, pokud je povoleno ověřování Redis. Hodnota je prázdný řetězec, ve výchozím nastavení, což znamená, že zprostředkovatel stavu relací nebudeme používat jakékoli heslo při připojování k serveru Redis. **Pokud je Redis server v veřejně přístupné síti, jako je Azure Redis Cache, je nutné povolit Redis autorizace pro zlepšení zabezpečení a poskytovat zabezpečené heslo.** |
| *ssl* | Boolean | **False** | Určuje, zda se připojit k serveru Redis prostřednictvím protokolu SSL. Tato hodnota je **false** ve výchozím nastavení protože Redis nepodporuje SSL úprav. **Pokud používáte Azure Redis Cache mimo pole, která podporuje protokol SSL je potřeba nastavte tuto hodnotu na true pro zlepšení zabezpečení.**<br/><br/>Port bez SSL je ve výchozím nastavení pro nové mezipaměti zakázán. Zadejte **true** pro toto nastavení pro použití portu SSL. Další informace o povolení portu bez SSL, najdete v článku [přístupové porty](cache-configure.md#access-ports) tématu [konfigurace mezipaměti](cache-configure.md) tématu. |
| *databaseIdNumber* | kladné celé číslo | 0 | *Tento atribut se dá nastavit jenom prostřednictvím souboru web.config nebo AppSettings.*<br/><br/>Zadejte databázi, kterou Redis k použití. |
| *connectionTimeoutInMilliseconds* | kladné celé číslo | Poskytuje StackExchange.Redis | Slouží k nastavení *ConnectTimeout* při vytváření StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | kladné celé číslo | Poskytuje StackExchange.Redis | Slouží k nastavení *SyncTimeout* při vytváření StackExchange.Redis.ConnectionMultiplexer. |
| *připojovací řetězec* (StackExchange.Redis platný připojovací řetězec) | string | *není k dispozici* | Buď parametr odkazem na AppSettings souboru web.config, nebo jinak platný připojovací řetězec StackExchange.Redis. Tento atribut můžete zadat hodnoty pro *hostitele*, *port*, *accessKey*, *ssl*a další atributy StackExchange.Redis. Pro blíže podívat *connectionString*, naleznete v tématu [nastavení connectionString](#setting-connectionstring) v [atribut poznámky](#attribute-notes) oddílu. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *není k dispozici* | *Tyto atributy lze zadat pouze prostřednictvím souboru web.config nebo AppSettings.*<br/><br/>Tyto atributy můžete zadat připojovací řetězec. *settingsClassName* by měly být třídy kvalifikovaný název sestavení, který obsahuje metodu určenou atributem *settingsMethodName*.<br/><br/>Metodu určenou atributem *settingsMethodName* by měly být veřejné, statické a void (ne nepřebírá žádné parametry), s návratovým typem **řetězec**. Tato metoda vrátí skutečným připojovacím řetězcem. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *není k dispozici* | *Tyto atributy lze zadat pouze prostřednictvím souboru web.config nebo AppSettings.*<br/><br/>Pomocí těchto atributů pro ladění vaší aplikace tím, že poskytuje protokoly ze stavu relací a výstupní mezipaměti spolu s protokoly z StackExchange.Redis. *loggingClassName* by měly být třídy kvalifikovaný název sestavení, který obsahuje metodu určenou atributem *loggingMethodName*.<br/><br/>Metodu určenou atributem *loggingMethodName* by měly být veřejné, statické a void (ne nepřebírá žádné parametry), s návratovým typem **System.IO.TextWriter**. |
| *applicationName* | string | Název modulu aktuálního procesu nebo "/" | *Pouze sessionstateprovider s*<br/>*Tento atribut se dá nastavit jenom prostřednictvím souboru web.config nebo AppSettings.*<br/><br/>Předpona názvu aplikace v mezipaměti Redis. Zákazník může používat stejný mezipaměť redis cache pro různé účely. – Pomáhat zajistit, že klíče relace nejsou v konfliktu, můžete mít předponu s názvem aplikace. |
| *throwOnError* | Boolean | true (pravda) | *Pouze sessionstateprovider s*<br/>*Tento atribut se dá nastavit jenom prostřednictvím souboru web.config nebo AppSettings.*<br/><br/>Určuje, zda vyvolání výjimky, když dojde k chybě.<br/><br/>Další informace o tom *throwOnError*, naleznete v tématu [poznámky na *throwOnError* ](#notes-on-throwonerror) v [atribut poznámky](#attribute-notes) oddílu. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | kladné celé číslo | 5000 | *Pouze sessionstateprovider s*<br/>*Tento atribut se dá nastavit jenom prostřednictvím souboru web.config nebo AppSettings.*<br/><br/>Jak dlouho se má opakovat, pokud operace selže. Pokud je tato hodnota menší než *operationTimeoutInMilliseconds*, zprostředkovatel nebude akci opakovat.<br/><br/>Další informace o tom *retryTimeoutInMilliseconds*, naleznete v tématu [poznámky na *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) v [atribut poznámky](#attribute-notes) oddílu. |
| *redisSerializerType* | string | *není k dispozici* | Určuje název sestavení kvalifikovaný typ třídy, která implementuje Microsoft.Web.Redis. ISerializer a který obsahuje vlastní logiku k serializaci a deserializaci hodnoty. Další informace najdete v tématu [o *redisSerializerType* ](#about-redisserializertype) v [atribut poznámky](#attribute-notes) oddílu. |
|

## <a name="attribute-notes"></a>Poznámky atributu

### <a name="setting-connectionstring"></a>Nastavení *připojovací řetězec*

Hodnota *connectionString* slouží jako klíč k načtení skutečným připojovacím řetězcem z položky AppSettings, pokud takový řetězec existuje v AppSettings. Pokud není nalezen uvnitř AppSettings, hodnota *připojovací řetězec* se použije jako klíč pro načtení samotný připojovací řetězec ze souboru web.config **ConnectionString** části, pokud existuje v této části. Pokud připojovací řetězec není v AppSettings souboru web.config existuje **ConnectionString** části, hodnota literálu *connectionString* se použije jako připojovací řetězec při vytváření StackExchange.Redis.ConnectionMultiplexer.

Následující příklady znázorňují způsob *connectionString* se používá.

#### <a name="example-1"></a>Příklad 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

V `web.config`, použijte výše klíč jako hodnotu parametru namísto skutečné hodnoty.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Příklad 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

V `web.config`, použijte výše klíč jako hodnotu parametru namísto skutečné hodnoty.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Příklad 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Poznámky na *throwOnError*

Pokud dojde k chybě během operace relace, v současné době zprostředkovatel stavu relací vyvolá výjimku. Ukončí aplikaci.

Toto chování byl změněn způsobem, který podporuje několik předpokladů existující uživatelů poskytovatele stavu relace ASP.NET a zároveň zajistit možnost tak, aby fungoval na výjimky, v případě potřeby. Výchozí chování stále vyvolá výjimku, pokud dojde k chybě, konzistentní s jinými poskytovateli stavu relace ASP.NET; stávající kód by měl fungovat stejně jako před.

Pokud nastavíte *throwOnError* k **false**, pak namísto vyvolání výjimky, když dojde k chybě, dojde k selhání bezobslužně. Chcete-li zobrazit, pokud došlo k chybě a pokud ano, zjistit, co byla výjimka, zkontrolujte statickou vlastnost *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Poznámky na *retryTimeoutInMilliseconds*

To poskytuje některé Logika opakování pro zjednodušení tento případ, kdy opakovat některé operace relace při selhání z důvodu věci, jako je síť poruchu, umožní vám k řízení časový limit opakování nebo zcela vyjádření nesouhlasu, opakování i.

Pokud nastavíte *retryTimeoutInMilliseconds* na číslo, například 2000, pak pokud se nezdaří operace relace, bude pokusy opakovat 2000 milisekund před považuje za chybu. Proto pokud chcete, aby zprostředkovatel stavu relací použít tuto logiku opakování, stačí pouze nakonfigurujte časový limit. První opakování dojde po 20 MS, což je ve většině případů dostatečná, když se stane poruchu sítě. Po tomto bude pokusy opakovat každou sekundu, dokud vyprší časový limit. Hned po vypršení časového limitu bude pokusy opakovat ještě jednou abyste měli jistotu, že ji nebude snížili mimo časový limit (maximální) sekundu.

Pokud si myslíte, budete potřebovat opakování (například když spustíte Redis server na stejném počítači jako aplikace) nebo pokud chcete zpracovávat logiku opakování, sami, nastavte *retryTimeoutInMilliseconds* na hodnotu 0.

### <a name="about-redisserializertype"></a>O *redisSerializerType*

Ve výchozím nastavení, serializace pro uložení hodnot v Redis se provádí v binárním formátu poskytovaném rutinami **BinaryFormatter** třídy. Použití *redisSerializerType* k zadání názvu sestavení kvalifikovaný typ třídy, která implementuje **Microsoft.Web.Redis.ISerializer** a má vlastní logiku k serializaci a deserializaci hodnoty. Tady je příklad třídy serializátor Json pomocí JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Za předpokladu, že tato třída je definována v sestavení s názvem **MyCompanyDll**, můžete nastavit parametr *redisSerializerType* jeho použití:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Cache – direktiva Output

Přidejte direktivu OutputCache na každou stránku, pro kterou chcete výstupu do mezipaměti.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

V předchozím příkladu stránky v mezipaměti data zůstanou v mezipaměti po dobu 60 sekund, a jinou verzi na stránce se uloží do mezipaměti pro každou kombinaci parametrů. Další informace o direktivě OutputCache najdete v tématu [ @OutputCache ](https://go.microsoft.com/fwlink/?linkid=320837).

Jakmile jsou tyto kroky provést, vaše aplikace nakonfigurována pro použití zprostředkovatele výstupní mezipaměti Redis.

## <a name="next-steps"></a>Další postup

Podívejte se [zprostředkovatel stavu relací ASP.NET pro Azure Cache pro Redis](cache-aspnet-session-state-provider.md).
