---
title: Zprostředkovatel výstupní mezipaměti ASP.NET pro Azure cache pro Redis
description: Přečtěte si, jak ukládat výstup stránky ASP.NET do mezipaměti pomocí Azure cache pro Redis. Zprostředkovatel výstupní mezipaměti Redis je nezpracovávající mechanismus úložiště pro data výstupní mezipaměti.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 6d711b07a10e04dcdf31259f3e53c9687af28e28
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544626"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Zprostředkovatel výstupní mezipaměti ASP.NET pro Azure cache pro Redis

Zprostředkovatel výstupní mezipaměti Redis je nezpracovávající mechanismus úložiště pro data výstupní mezipaměti. Tato data jsou určena konkrétně pro úplné odpovědi HTTP (ukládání výstupu stránky do mezipaměti). Zprostředkovatel se připojí k novému bodu rozšiřitelnosti zprostředkovatele výstupní mezipaměti, který byl představen v ASP.NET 4. V případě ASP.NET Core aplikací číst [ukládání odpovědí do mezipaměti v ASP.NET Core](/aspnet/core/performance/caching/response). 

Pokud chcete použít poskytovatele výstupní mezipaměti Redis, nejdřív nakonfigurujte mezipaměť a pak nakonfigurujte aplikaci ASP.NET pomocí balíčku NuGet poskytovatele výstupní mezipaměti Redis. Toto téma poskytuje pokyny ke konfiguraci aplikace pro použití poskytovatele výstupní mezipaměti Redis. Další informace o vytváření a konfiguraci mezipaměti Azure pro instanci Redis najdete v tématu [vytvoření mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Ukládat výstup stránky ASP.NET do mezipaměti

Chcete-li konfigurovat klientskou aplikaci v aplikaci Visual Studio pomocí balíčku NuGet pro stav relace Azure cache for Redis, klikněte na **Správce balíčků NuGet** , **Konzola správce balíčků** v nabídce **nástroje** .

V okně `Package Manager Console` spusťte následující příkaz.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Balíček NuGet zprostředkovatele výstupní mezipaměti Redis má závislost na balíčku StackExchange. Redis. StrongName. Pokud se v projektu nenachází balíček StackExchange. Redis. StrongName, je nainstalován. Další informace o balíčku NuGet poskytovatele výstupní mezipaměti Redis najdete na stránce NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) .

>[!NOTE]
>Kromě balíčku StackExchange. Redis. StrongName se silným názvem existuje také StackExchange. Redis verze, která není silně pojmenována. Pokud váš projekt používá verzi StackExchange. Redis, která není silně pojmenována, musíte ji odinstalovat; v opačném případě budete mít v projektu konflikty pojmenování. Další informace o těchto balíčcích najdete v tématu [Konfigurace klientů mezipaměti rozhraní .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Balíček NuGet stáhne a přidá požadované odkazy na sestavení a přidá následující oddíl do souboru web.config. Tato část obsahuje požadovanou konfiguraci pro vaši aplikaci ASP.NET, aby používala poskytovatele výstupní mezipaměti Redis.

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

Nakonfigurujte atributy s hodnotami z okna cache v portál Microsoft Azure a podle potřeby nakonfigurujte další hodnoty. Pokyny k přístupu k vlastnostem mezipaměti najdete v tématu [Konfigurace nastavení služby Azure cache pro Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atribut | Typ | Výchozí | Popis |
| --------- | ---- | ------- | ----------- |
| *provoz* | řetězec | místního | IP adresa nebo název hostitele serveru Redis |
| *přístavní* | kladné celé číslo | 6379 (bez TLS/SSL)<br/>6380 (TLS/SSL) | Port serveru Redis |
| *accessKey* | řetězec | "" | Redis heslo serveru, když je povolená autorizace Redis. Ve výchozím nastavení je hodnota prázdný řetězec, což znamená, že zprostředkovatel stavu relace nebude při připojování k serveru Redis používat žádné heslo. **Pokud je server Redis ve veřejně přístupné síti, jako je Azure Redis Cache, Nezapomeňte povolit autorizaci Redis pro zlepšení zabezpečení a zadat zabezpečené heslo.** |
| *zabezpečení* | boolean | **chybné** | Zda se chcete připojit k serveru Redis prostřednictvím protokolu TLS. Ve výchozím nastavení je tato hodnota **false** , protože Redis nepodporuje protokol TLS mimo box. **Pokud používáte Azure Redis Cache, který podporuje protokol SSL, nezapomeňte tuto vlastnost nastavit na hodnotu true, aby se zlepšila zabezpečení.**<br/><br/>Port bez protokolu TLS je ve výchozím nastavení pro nové mezipaměti zakázán. Pro toto nastavení zadejte **hodnotu true** , pokud chcete použít port bez protokolu TLS. Další informace o povolení portu bez TLS najdete v části [přístupové porty](cache-configure.md#access-ports) v tématu [Konfigurace mezipaměti](cache-configure.md) . |
| *databaseIdNumber* | kladné celé číslo | 0 | *Tento atribut lze zadat pouze pomocí web.config nebo AppSettings.*<br/><br/>Určete, která databáze Redis se má použít. |
| *connectionTimeoutInMilliseconds* | kladné celé číslo | Poskytuje StackExchange. Redis | Slouží k nastavení *ConnectTimeout* při vytváření stackexchange. Redis. ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | kladné celé číslo | Poskytuje StackExchange. Redis | Slouží k nastavení *SyncTimeout* při vytváření stackexchange. Redis. ConnectionMultiplexer. |
| *ConnectionString* (platný připojovací řetězec stackexchange. Redis) | řetězec | *není k dispozici* | Buď odkaz na parametr AppSettings nebo web.config, nebo jinak platný připojovací řetězec StackExchange. Redis. Tento atribut může poskytovat hodnoty pro *hostitel* , *port* , *AccessKey* , *SSL* a další atributy stackexchange. Redis. Bližší pohled na *ConnectionString* najdete v tématu [Nastavení ConnectionString](#setting-connectionstring) v oddílu [poznámky k atributu](#attribute-notes) . |
| *settingsClassName*<br/>*settingsMethodName* | řetězec<br/>řetězec | *není k dispozici* | *Tyto atributy lze zadat pouze pomocí web.config nebo AppSettings.*<br/><br/>Tyto atributy použijte k poskytnutí připojovacího řetězce. *settingsClassName* by měl být kvalifikovaný název třídy sestavení, který obsahuje metodu určenou parametrem *settingsMethodName* .<br/><br/>Metoda určená parametrem *settingsMethodName* by měla být public, static a void (nesmí přebírat žádné parametry) s návratovým typem **řetězce** . Tato metoda vrátí skutečný připojovací řetězec. |
| *loggingClassName*<br/>*loggingMethodName* | řetězec<br/>řetězec | *není k dispozici* | *Tyto atributy lze zadat pouze pomocí web.config nebo AppSettings.*<br/><br/>Tyto atributy použijte k ladění aplikace poskytováním protokolů z stavu relace/výstupní mezipaměti spolu s protokoly z StackExchange. Redis. *loggingClassName* by měl být kvalifikovaný název třídy sestavení, který obsahuje metodu určenou parametrem *loggingMethodName* .<br/><br/>Metoda určená parametrem *loggingMethodName* by měla být public, static a void (nesmí přebírat žádné parametry) s návratovým typem **System. IO. TextWriter** . |
| *applicationName* | řetězec | Název modulu aktuálního procesu nebo "/" | *Jenom SessionStateProvider*<br/>*Tento atribut lze zadat pouze pomocí web.config nebo AppSettings.*<br/><br/>Předpona názvu aplikace, která se má použít v Redis Cache Zákazník může používat stejnou mezipaměť Redis pro různé účely. Aby se zajistilo, že klíče relace nekolidují, může být předpona s názvem aplikace. |
| *Parametr throwOnError* | boolean | true | *Jenom SessionStateProvider*<br/>*Tento atribut lze zadat pouze pomocí web.config nebo AppSettings.*<br/><br/>Určuje, zda má být při výskytu chyby vyvolána výjimka.<br/><br/>Další informace o *parametr throwOnError* naleznete v tématu [poznámky k *parametr throwOnError*](#notes-on-throwonerror) v oddílu [poznámky k atributu](#attribute-notes) . |>*Microsoft. Web. Redis. RedisSessionStateProvider. LastException* . |
| *retryTimeoutInMilliseconds* | kladné celé číslo | 5000 | *Jenom SessionStateProvider*<br/>*Tento atribut lze zadat pouze pomocí web.config nebo AppSettings.*<br/><br/>Doba, po kterou se bude opakovat pokus o neúspěch operace. Pokud je tato hodnota menší než *operationTimeoutInMilliseconds* , poskytovatel se nezopakuje.<br/><br/>Další informace o *retryTimeoutInMilliseconds* naleznete v tématu [poznámky k *retryTimeoutInMilliseconds*](#notes-on-retrytimeoutinmilliseconds) v oddílu [poznámky k atributu](#attribute-notes) . |
| *redisSerializerType* | řetězec | *není k dispozici* | Určuje kvalifikovaný název typu sestavení třídy, která implementuje Microsoft. Web. Redis. ISerializer a obsahuje vlastní logiku k serializaci a deserializaci hodnot. Další informace naleznete v části [o *redisSerializerType*](#about-redisserializertype) v oddílu [poznámky k atributu](#attribute-notes) . |

## <a name="attribute-notes"></a>Poznámky k atributu

### <a name="setting-connectionstring"></a>Nastavení *připojovacího řetězce*

Hodnota *ConnectionString* se používá jako klíč k načtení skutečného připojovacího řetězce z appSettings, pokud takový řetězec existuje v appSettings. Pokud se v elementu AppSettings nenajde, hodnota *ConnectionString* se použije jako klíč k načtení skutečného připojovacího řetězce z oddílu web.config **ConnectionString** , pokud tato část existuje. Pokud připojovací řetězec v oddílu AppSettings nebo web.config **ConnectionString** neexistuje, použije se při vytváření stackexchange. Redis. ConnectionMultiplexer hodnota literálu *ConnectionString* jako připojovací řetězec.

Následující příklady znázorňují, jak se používá *připojovací řetězec* .

#### <a name="example-1"></a>Příklad 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

V `web.config` použijte místo skutečné hodnoty klíč jako hodnotu parametru.

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

V `web.config` použijte místo skutečné hodnoty klíč jako hodnotu parametru.

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

### <a name="notes-on-throwonerror"></a>Poznámky k *parametr throwOnError*

Pokud v současné době dojde k chybě během operace relace, zprostředkovatel stavu relace vyvolá výjimku. Tím se aplikace ukončí.

Toto chování bylo upraveno způsobem, který podporuje očekávání stávajících uživatelů poskytovatele stavu relace ASP.NET a zároveň poskytuje schopnost jednat o výjimky, pokud je to požadováno. Výchozí chování stále vyvolá výjimku, pokud dojde k chybě, která je konzistentní s jinými zprostředkovateli stavu relací ASP.NET. existující kód by měl fungovat stejně jako předtím.

Pokud nastavíte *parametr throwOnError* na **hodnotu false** , pak namísto vyvolání výjimky, pokud dojde k chybě, dojde k selhání v tichém režimu. Chcete-li zjistit, zda došlo k chybě, a pokud ano, zjistíte, co byla výjimka, zkontrolujte statickou vlastnost *Microsoft. Web. Redis. RedisSessionStateProvider. LastException* .

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Poznámky k *retryTimeoutInMilliseconds*

Díky tomu je k dispozici několik logických postupů, které zjednodušují případ, kdy by se operace relace měla opakovat při selhání, a to kvůli tomu, jako je například Network porucha, a zároveň vám umožní řídit časový limit opakování nebo se zcela znovu odhlásit.

Pokud *retryTimeoutInMilliseconds* nastavíte na číslo, například 2000, pak když operace relace selže, bude po dobu 2000 milisekund znovu opakována, než se zpracuje jako chyba. Takže pokud chcete, aby zprostředkovatel stavu relací mohl použít tuto logiku opakování, stačí nakonfigurovat časový limit. K prvnímu opakování dojde po 20 milisekundách, což ve většině případů postačuje, když dojde k síťovému poruchau. Pak se to opakuje každou sekundu, dokud nevyprší časový limit. Hned po vypršení časového limitu se to opakuje ještě jednou, aby se zajistilo, že nezíská časový limit (nejvýše) jednu sekundu.

Pokud si nejste vědomi, že budete chtít opakovat pokus (například pokud používáte server Redis ve stejném počítači jako aplikaci) nebo pokud chcete zpracovat logiku opakování sami, nastavte *retryTimeoutInMilliseconds* na 0.

### <a name="about-redisserializertype"></a>O *redisSerializerType*

Ve výchozím nastavení je serializace pro ukládání hodnot na Redis provedeno v binárním formátu poskytovaném třídou **BinaryFormatter** . Použijte *redisSerializerType* k určení kvalifikovaného názvu typu sestavení třídy, která implementuje **Microsoft. Web. Redis. ISerializer** a má vlastní logiku k serializaci a deserializaci hodnot. Například tady je třída serializátoru JSON s použitím JSON.NET:

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
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Za předpokladu, že je tato třída definována v sestavení s názvem **MyCompanyDll** , můžete nastavit parametr *redisSerializerType* na použití:

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

## <a name="output-cache-directive"></a>Direktiva výstupní mezipaměti

Přidejte direktivu OutputCache na každou stránku, pro kterou chcete výstup ukládat do mezipaměti.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

V předchozím příkladu data stránky uložená v mezipaměti zůstanou v mezipaměti po dobu 60 sekund a pro každou kombinaci parametrů se ukládá do mezipaměti jiná verze této stránky. Další informace o direktivě OutputCache naleznete v tématu [@OutputCache](/previous-versions/dotnet/netframework-4.0/hdxfb6cy(v=vs.100)) .

Po provedení těchto kroků je vaše aplikace nakonfigurovaná tak, aby používala poskytovatele výstupní mezipaměti Redis.

## <a name="third-party-output-cache-providers"></a>Poskytovatelé výstupní mezipaměti třetích stran

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Další kroky

Podívejte se na [zprostředkovatele stavu relace ASP.NET pro Azure cache pro Redis](cache-aspnet-session-state-provider.md).