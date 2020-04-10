---
title: ASP.NET zprostředkovatele výstupní mezipaměti pro Azure Cache for Redis
description: Přečtěte si, jak uložit do mezipaměti ASP.NET výstup stránky pomocí Azure Cache for Redis. Zprostředkovatel mezipaměti výstupu Redis je mimoprocesový mechanismus úložiště pro výstupní data mezipaměti.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: f1d8189068278b46e3ec3ea66875d79bb91e5e16
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010201"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET zprostředkovatele výstupní mezipaměti pro Azure Cache for Redis

Zprostředkovatel mezipaměti výstupu Redis je mimoprocesový mechanismus úložiště pro výstupní data mezipaměti. Tato data jsou speciálně pro úplné odpovědi HTTP (ukládání do mezipaměti výstupu stránky). Zprostředkovatel se zapojí do nového bodu rozšiřitelnosti poskytovatele výstupní mezipaměti, který byl zaveden v ASP.NET 4.

Chcete-li použít zprostředkovatele výstupní mezipaměti Redis, nejprve nakonfigurujte mezipaměť a potom nakonfigurujte ASP.NET aplikaci pomocí balíčku NuGet zprostředkovatele výstupní mezipaměti Redis. Toto téma obsahuje pokyny ke konfiguraci aplikace pro použití zprostředkovatele výstupní mezipaměti Redis. Další informace o vytvoření a konfiguraci instance Azure Cache for Redis najdete [v tématu Vytvoření mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Uložení výstupu stránky ASP.NET do mezipaměti

Chcete-li nakonfigurovat klientskou aplikaci v sadě Visual Studio pomocí balíčku NuGet stavu relace Azure Cache for Redis, klepněte na položku **NuGet Package Manager**, **Console správce balíčků** z nabídky **Nástroje.**

V okně `Package Manager Console` spusťte následující příkaz.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Balíček NuGet zprostředkovatele výstupní mezipaměti Redis má závislost na balíčku StackExchange.Redis.StrongName. Pokud StackExchange.Redis.StrongName balíček není k dispozici v projektu, je nainstalován. Další informace o balíčku NuGet zprostředkovatele výstupní mezipaměti Redis naleznete na stránce [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet.

>[!NOTE]
>Kromě balíčku stackexchange.redis.strongname se silným názvem stackexchange.redis.strongname balíček, je také StackExchange.Redis bez silné pojmenované verze. Pokud váš projekt používá verzi stackexchange.redis bez silného názvem, je nutné ji odinstalovat. v opačném případě dojde ke konfliktům názvů v projektu. Další informace o těchto balíčcích naleznete v [tématu Konfigurace klientů mezipaměti .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Balíček NuGet se stáhne a přidá požadované odkazy na sestavení a přidá následující část do souboru web.config. Tato část obsahuje požadovanou konfiguraci pro ASP.NET aplikace pro použití zprostředkovatele výstupní mezipaměti Redis.

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

Nakonfigurujte atributy s hodnotami z okna mezipaměti na portálu Microsoft Azure a podle potřeby nakonfigurujte další hodnoty. Pokyny k přístupu ke vlastnostem mezipaměti najdete [v tématu Konfigurace mezipaměti Azure pro nastavení Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atribut | Typ | Výchozí | Popis |
| --------- | ---- | ------- | ----------- |
| *Hostitele* | řetězec | "localhost" | Ip adresa serveru Redis nebo název hostitele |
| *Port* | kladné celé číslo | 6379 (bez TLS/SSL)<br/>6380 (TLS/SSL) | Port serveru Redis |
| *Accesskey* | řetězec | "" | Redis heslo serveru, pokud je povolena autorizace Redis. Hodnota je ve výchozím nastavení prázdný řetězec, což znamená, že poskytovatel stavu relace nepoužije žádné heslo při připojování k serveru Redis. **Pokud je váš server Redis ve veřejně přístupné síti, jako je Azure Redis Cache, nezapomeňte povolit autorizaci Redis ke zlepšení zabezpečení a zadat zabezpečené heslo.** |
| *Ssl* | Boolean | **False** | Zda se připojit k serveru Redis přes TLS. Tato hodnota je **false** ve výchozím nastavení, protože Redis nepodporuje TLS po vybalení z krabice. **Pokud používáte Azure Redis Cache, který podporuje SSL po vybalení z krabice, nezapomeňte nastavit na true ke zlepšení zabezpečení.**<br/><br/>Port netls je ve výchozím nastavení zakázán pro nové mezipaměti. Zadejte **hodnotu true** pro toto nastavení, chcete-li použít port TLS. Další informace o povolení portu bez tls naleznete v části [Přístupové porty](cache-configure.md#access-ports) v [tématu Konfigurace mezipaměti.](cache-configure.md) |
| *databaseIdNumber* | kladné celé číslo | 0 | *Tento atribut lze zadat pouze prostřednictvím web.config nebo AppSettings.*<br/><br/>Určete, kterou databázi Redis chcete použít. |
| *connectionTimeoutInMilliseconds* | kladné celé číslo | Poskytuje StackExchange.Redis | Slouží k nastavení *ConnectTimeout* při vytváření StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | kladné celé číslo | Poskytuje StackExchange.Redis | Slouží k nastavení *SyncTimeout* při vytváření StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (Platný připojovací řetězec StackExchange.Redis) | řetězec | *N/a* | Buď odkaz na parametr AppSettings nebo web.config, nebo platný připojovací řetězec StackExchange.Redis. Tento atribut může poskytnout hodnoty pro *atributy hostitele*, *port*, *accessKey*, *ssl*a další atributy StackExchange.Redis. Bližší pohled na *connectionString*naleznete v [tématu Nastavení řetězce připojení](#setting-connectionstring) v části [Poznámky atributu.](#attribute-notes) |
| *settingsNázev_třídy*<br/>*settingsMethodName* | řetězec<br/>řetězec | *N/a* | *Tyto atributy lze zadat pouze prostřednictvím web.config nebo AppSettings.*<br/><br/>Tyto atributy slouží k poskytnutí připojovacího řetězce. *settingsClassName* by měl být název třídy s kvalifikací sestavení, který obsahuje metodu určenou *parametrem settingsMethodName*.<br/><br/>Metoda určená *podle settingsMethodName* by měla být veřejná, statická a neplatná (nepřevzít žádné parametry) s návratovým typem **řetězce**. Tato metoda vrátí skutečný připojovací řetězec. |
| *loggingClassName*<br/>*název_metody logging* | řetězec<br/>řetězec | *N/a* | *Tyto atributy lze zadat pouze prostřednictvím web.config nebo AppSettings.*<br/><br/>Pomocí těchto atributů ladit aplikace tím, že protokoly z relace stavu nebo výstupu mezipaměti spolu s protokoly z StackExchange.Redis. *loggingClassName* by měl být název třídy s kvalifikací sestavení, který obsahuje metodu určenou *názvem loggingMethodName*.<br/><br/>Metoda určená *loggingMethodName* by měla být veřejná, statická a neplatná (nepřevzít žádné parametry) s návratovým typem **System.IO.TextWriter**. |
| *název aplikace* | řetězec | Název modulu aktuálního procesu nebo "/" | *Pouze sessionStateProvider*<br/>*Tento atribut lze zadat pouze prostřednictvím web.config nebo AppSettings.*<br/><br/>Předpona názvu aplikace, která se má použít v mezipaměti Redis. Zákazník může používat stejnou mezipaměť Redis pro různé účely. Chcete-li se ujistit, že klíče relace nejsou kolize, může být předponou s názvem aplikace. |
| *Throwonerror* | Boolean | true | *Pouze sessionStateProvider*<br/>*Tento atribut lze zadat pouze prostřednictvím web.config nebo AppSettings.*<br/><br/>Určuje, zda má být vyvolána výjimka, když dojde k chybě.<br/><br/>Další informace o *throwOnError*naleznete v [tématu Poznámky *k throwOnError* ](#notes-on-throwonerror) v části [Poznámky atributu.](#attribute-notes) |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | kladné celé číslo | 5000 | *Pouze sessionStateProvider*<br/>*Tento atribut lze zadat pouze prostřednictvím web.config nebo AppSettings.*<br/><br/>Jak dlouho opakovat, když se operace nezdaří. Pokud je tato hodnota menší než *operaceTimeoutInMilliseconds*, zprostředkovatel nebude opakovat.<br/><br/>Další informace o *retryTimeoutInMilliseconds*najdete v [článku Poznámky na *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) v části [Poznámky atributu.](#attribute-notes) |
| *redisSerializerType* | řetězec | *N/a* | Určuje název typu s kvalifikací sestavení třídy, která implementuje soubor Microsoft.Web.Redis. ISerializer a který obsahuje vlastní logiku serializovat a rekonstruovat hodnoty. Další informace naleznete v [tématu O *redisSerializerType* ](#about-redisserializertype) v části [Poznámky atributu.](#attribute-notes) |

## <a name="attribute-notes"></a>Poznámky atributů

### <a name="setting-connectionstring"></a>Nastavení *řetězce připojení*

Hodnota *connectionString* se používá jako klíč k načtení skutečného připojovacího řetězce z AppSettings, pokud takový řetězec existuje v AppSettings. Pokud není nalezen uvnitř AppSettings, hodnota *connectionString* bude použita jako klíč pro načtení skutečného připojovacího řetězce z oddílu web.config **ConnectionString,** pokud tato část existuje. Pokud připojovací řetězec neexistuje v AppSettings nebo web.config **ConnectionString** části, literál hodnota *connectionString* bude použita jako připojovací řetězec při vytváření StackExchange.Redis.ConnectionMultiplexer.

Následující příklady ilustrují, jak se používá *connectionString.*

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

### <a name="notes-on-throwonerror"></a>Poznámky *k throwOnError*

V současné době dojde k chybě během operace relace, zprostředkovatel stavu relace vyvolá výjimku. Tím se aplikace vypne.

Toto chování bylo upraveno způsobem, který podporuje očekávání stávajících uživatelů ASP.NET stavu relace a zároveň poskytuje možnost jednat na výjimky, pokud je to žádoucí. Výchozí chování stále vyvolá výjimku, když dojde k chybě, konzistentní s jinými poskytovateli stavu ASP.NET relace; existující kód by měl fungovat stejně jako dříve.

Pokud nastavíte *throwOnError* na **false**, pak místo vyvolání výjimky při výskytu chyby se nezdaří tiše. Chcete-li zjistit, zda došlo k chybě, a pokud ano, zjistěte, jaká byla výjimka, zkontrolujte statickou vlastnost *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Poznámky k *retryTimeoutInMilliseconds*

To poskytuje některé logiky opakování zjednodušit případ, kdy některé operace relace by měl opakovat na selhání z důvodu chyby sítě, a zároveň umožňuje řídit časový režim opakování nebo odhlásit opakování úplně.

Pokud nastavíte *retryTimeoutInMilliseconds* na číslo, například 2000, pak při selhání operace relace, bude opakovat pro 2000 milisekund před zpracováním jako chyba. Takže mít zprostředkovatele stavu relace použít tuto logiku opakování, stačí nakonfigurovat časový čas. První pokus se uskuteční po 20 milisekundách, což je ve většině případů dostačující, když dojde k chybě sítě. Poté se bude opakovat každou sekundu, dokud neskončí časový rámec. Hned po odletu se bude opakovat ještě jednou, aby se ujistil, že neodřízne časový rámec (maximálně) o jednu sekundu.

Pokud si nemyslíte, že potřebujete opakování (například při spuštění serveru Redis ve stejném počítači jako vaše aplikace) nebo pokud chcete zpracovat logiku opakování sami, nastavte *retryTimeoutInMilliseconds* na 0.

### <a name="about-redisserializertype"></a>O *společnosti redisSerializerType*

Ve výchozím nastavení serializace pro uložení hodnot na Redis se provádí v binárním formátu poskytované **BinaryFormatter** třídy. Pomocí *redisSerializerType* určete typ typu s kvalifikací sestavení třídy, která implementuje **Microsoft.Web.Redis.ISerializer** a má vlastní logiku serializovat a rekonstruovat hodnoty. Zde je například třída serializátoru Json používající JSON.NET:

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

Za předpokladu, že tato třída je definována v sestavení s názvem **MyCompanyDll**, můžete nastavit parametr *redisSerializerType* ji použít:

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

## <a name="output-cache-directive"></a>Výstupní směrnice mezipaměti

Přidejte na každou stránku direktivu OutputCache, pro kterou chcete výstup uložit do mezipaměti.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

V předchozím příkladu zůstanou data stránky uložená v mezipaměti v mezipaměti po dobu 60 sekund a pro každou kombinaci parametrů je uložena do mezipaměti jiná verze stránky. Další informace o směrnici OutputCache [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)naleznete v tématu .

Po provedení těchto kroků je aplikace nakonfigurována tak, aby používala zprostředkovatele výstupní mezipaměti Redis.

## <a name="third-party-output-cache-providers"></a>Poskytovatelé výstupní mezipaměti jiných výrobců

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Zapalte](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Další kroky

Podívejte se na [zprostředkovatele stavu ASP.NET relace pro Azure Cache for Redis](cache-aspnet-session-state-provider.md).
