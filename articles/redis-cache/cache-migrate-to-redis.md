---
title: Migrace aplikací Managed Cache Service k Redis – Azure | Dokumentace Microsoftu
description: Zjistěte, jak migrovat službu Managed Cache Service a mezipaměť In-Role aplikace do Azure Redis Cache
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/30/2017
ms.author: wesmc
ms.openlocfilehash: f499925ecea8ca127c90691f7d92e74e8df68cf9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697335"
---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrace ze služby Managed Cache Service na Azure Redis Cache
Migrace vaší aplikace, které používají službu Azure Managed Cache Service k Azure Redis Cache lze provést s minimálními změnami pro vaši aplikaci, v závislosti na službu Managed Cache Service funkcí používaných v rámci ukládání do mezipaměti aplikace. Rozhraní API jsou přesně stejné jsou podobné a velkou část vaší existující kód, který používá službu Managed Cache Service pro přístup k mezipaměti lze opětovně použít s minimálními změnami. Tento článek ukazuje, jak provést související s konfigurací a změny aplikace, které migrují aplikace Managed Cache Service k použití Azure Redis Cache a ukazuje, jak některé funkce Azure Redis Cache lze použít k implementaci funkce Spravovaná mezipaměť služby Cache Service.

>[!NOTE]
>Managed Cache Service a mezipaměť In-Role byly [vyřazeno](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30. listopadu 2016. Pokud máte všechna nasazení v instanci Role, které chcete migrovat na Azure Redis Cache, provedením kroků v tomto článku.

## <a name="migration-steps"></a>Kroky migrace
Následující kroky je potřeba migrovat aplikace Managed Cache Service pro použití Azure Redis Cache.

* Mapování funkce služby Managed Cache Service k Azure Redis Cache
* Vyberte nabídky Cache
* Vytvoření mezipaměti
* Konfigurace klientů mezipaměti
  * Odebrání konfigurace služby Managed Cache
  * Konfigurace klienta mezipaměti pomocí balíčku StackExchange.Redis NuGet
* Migrace kódu služby Managed Cache Service
  * Připojení k mezipaměti horizontálních oddílů pomocí třídy ConnectionMultiplexer
  * Primitivní datové typy přístupu do mezipaměti
  * Práce s objekty .NET v mezipaměti
* Migrace stavu relace ASP.NET a ukládání výstupu do mezipaměti pro Azure Redis Cache 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Mapování funkce služby Managed Cache Service k Azure Redis Cache
Azure Managed Cache Service a Azure Redis Cache jsou podobné, ale některé funkce implementovat různými způsoby. Tato část popisuje některé rozdíly a obsahuje pokyny k implementaci funkcí služby Managed Cache Service v Azure Redis Cache.

| Spravované funkce služby Cache Service | Podpora spravované služby Cache Service | Podpora Azure Redis Cache |
| --- | --- | --- |
| Pojmenované mezipaměti |Výchozí mezipaměti je nakonfigurovaný, a v mezipaměti Standard a Premium nabídky až devět dalších s názvem mezipaměti dá se v případě potřeby. |Mezipaměti Azure redis Cache mají Konfigurovatelný počet databází (výchozí je 16), které lze použít k implementaci podobné funkce jako pojmenovaných mezipamětí. Další informace najdete v tématu [Co jsou databáze Redis?](cache-faq.md#what-are-redis-databases) a [Výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration). |
| Vysoká dostupnost |Poskytuje vysokou dostupnost pro položky v mezipaměti v mezipaměti nabídky Standard a Premium. Pokud jsou ztraceny z důvodu chyby položky, záložní kopie položky v mezipaměti jsou stále dostupné. Zápisy do sekundární mezipaměť probíhají synchronně. |Vysoká dostupnost je k dispozici Standard a Premium nabídky mezipaměti, které mají konfiguraci dva uzly primární/replika (každý horizontální oddíl mezipaměti Premium má primární/replika dvojici). Asynchronně provádí zápisy do repliky. Další informace najdete v tématu [cenách Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/). |
| Oznámení |Umožňuje klientům přijímat asynchronní oznámení, když různé operace s mezipamětí, ke kterým dochází na pojmenovanou mezipaměť. |Klientské aplikace můžou použít Redis pub/sub nebo [oznámení Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) dosáhnout podobné funkce pro oznámení. |
| Místní mezipaměť |Místně ukládá kopie objektů uložených v mezipaměti na straně klienta pro velmi rychlý přístup. |Klientské aplikace, třeba k implementaci této funkce s použitím slovníku nebo podobné datové struktury. |
| Zásady vyřazení |Žádné nebo LRU. Výchozí zásada je LRU. |Azure Redis Cache podporuje následující zásady vyřazení: volatile lru, allkeys lru, náhodných volatile, allkeys náhodné, volatile ttl, noeviction. Výchozí zásada je volatile lru. Další informace najdete v tématu [výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration). |
| Zásady vypršení platnosti |Výchozí zásady vypršení platnosti je absolutní výchozí interval vypršení platnosti je 10 minut Klouzavé a nikdy zásady jsou také k dispozici. |Ve výchozím nastavení se položky v mezipaměti nevyprší, ale dá se vypršení platnosti na základě za zápis pomocí přetížení mezipaměti sady. |
| Oblasti a označování |Oblasti jsou podskupiny pro položky v mezipaměti. Oblasti také podporují poznámky z položek v mezipaměti s další popisné řetězce značky. Oblasti podporují možnost provádět operace vyhledávání na všechny položky označené v této oblasti. Všechny položky v rámci oblasti jsou umístěné v rámci jednoho uzlu clusteru mezipaměti. |Mezipaměti redis cache se skládá z jednoho uzlu (Pokud Redis cluster je povolené) tak koncept oblasti služby Managed Cache Service se nevztahují. Redis podporuje vyhledávání a operacím se zástupnými znaky při načítání klíče, takže popisná klíčová slova můžete vkládán názvy klíčů a používá se k načtení položky později. Příklad implementace označení řešení s využitím Redis, naleznete v tématu [implementaci mezipaměti značení s využitím Redisu](http://stackify.com/implementing-cache-tagging-redis/). |
| Serializace |Managed Cache podporuje NetDataContractSerializer, BinaryFormatter a použití vlastní serializátory. Výchozí hodnota je NetDataContractSerializer. |Zodpovídá za klientské aplikace k serializaci objektů .NET před uvedením do mezipaměti s výběru serializátoru až do vývojář aplikace klienta. Další informace a ukázky kódu najdete v tématu [práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulátor mezipaměti |Managed Cache poskytuje emulátor místní mezipaměti. |Azure Redis Cache nemá emulátoru, ale můžete [místní spuštění sestavení MSOpenTech redis server.exe](cache-faq.md#cache-emulator) a poskytovalo vám emulátoru. |

## <a name="choose-a-cache-offering"></a>Vyberte nabídky Cache
Microsoft Azure Redis Cache je dostupná na následujících úrovních:

* **Basic** – jeden uzel. Více velikostí až do 53 GB.
* **Standard** – dva uzly Primární/Replika. Více velikostí až do 53 GB. 99,9% SLA.
* **Premium** – dva uzly Primární/Replika s až 10 horizontálními oddíly. Více velikostí od 6 GB do 530 GB. Všechny funkce úrovně Standard a navíc podpora [clusteru Redis](cache-how-to-premium-clustering.md), [trvalosti Redis](cache-how-to-premium-persistence.md) a [služby Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9% SLA.

Každá úroveň se liší z hlediska funkcí a cen. Funkce jsou popsané dál v této příručce a další informace o cenách najdete v tématu [podrobnosti o cenách mezipaměti](https://azure.microsoft.com/pricing/details/cache/).

Výchozí bod pro migraci, je vybrat velikost, která odpovídá velikosti předchozí mezipaměť služby Managed Cache Service a potom kapacitu zase vertikálně navýšit nebo snížit kapacitu v závislosti na požadavcích vaší aplikace. Další informace o výběr nejvhodnější nabídky mezipaměti Azure redis Cache najdete v tématu [jakou velikost a kterou nabídku Redis Cache mám použít](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Vytvoření mezipaměti
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurace klientů mezipaměti
Jakmile mezipaměti je vytvořen a nakonfigurován, dalším krokem je odebrat konfiguraci služby Managed Cache Service a přidat konfiguraci Azure Redis Cache a odkazuje na tak, aby klienti mezipaměti mají přístup k mezipaměti.

* Odebrání konfigurace služby Managed Cache
* Konfigurace klienta mezipaměti pomocí balíčku StackExchange.Redis NuGet

### <a name="remove-the-managed-cache-service-configuration"></a>Odebrání konfigurace služby Managed Cache
Předtím, než je možné nakonfigurovat klientské aplikace pro Azure Redis Cache, existující konfigurace služby Managed Cache Service a odkazy na sestavení, musí se odebrat odinstalací balíček NuGet služby Managed Cache.

Pokud chcete odinstalovat balíček NuGet služby Managed Cache, klikněte pravým tlačítkem na klientský projekt v **Průzkumníka řešení** a zvolte **spravovat balíčky NuGet**. Vyberte **nainstalované balíčky** uzlu a typu W**indowsAzure.Caching** do vyhledávacího pole nainstalované balíčky. Vyberte **Windows** **Azure Cache** (nebo **Windows** **ukládání do mezipaměti Azure** v závislosti na verzi balíčku NuGet), klikněte na tlačítko **Odinstalovat**a potom klikněte na tlačítko **Zavřít**.

![Odinstalovat balíček NuGet služby Azure Managed Cache Service](./media/cache-migrate-to-redis/IC757666.jpg)

Odinstalovává se balíček NuGet pro služby spravované mezipaměti odstraní sestavení služby Managed Cache Service a službu Managed Cache Service položky v souboru app.config nebo web.config klientské aplikace. Protože některá vlastní nastavení nemusí být odebrány při odinstalaci balíčku NuGet, otevřete soubor web.config nebo app.config a ujistěte se, že se odeberou následující prvky.

Ujistěte se, že `dataCacheClients` je položka odebrána z `configSections` elementu. Neodstraňujte celý `configSections` element; odebrat jenom `dataCacheClients` položku, pokud je k dispozici.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Ujistěte se, že `dataCacheClients` oddílu se odebere. `dataCacheClients` Části bude vypadat podobně jako v následujícím příkladu.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Po odebrání konfigurace služby Managed Cache Service, můžete nakonfigurovat klienta mezipaměti, jak je popsáno v následující části.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurace klienta mezipaměti pomocí balíčku StackExchange.Redis NuGet
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrace kódu služby Managed Cache Service
Služba Managed Cache Service podobá rozhraní API pro klienta mezipaměti StackExchange.Redis. Tato část obsahuje základní informace o rozdílech.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Připojení k mezipaměti horizontálních oddílů pomocí třídy ConnectionMultiplexer
V Managed Cache Service, byly zpracovány připojení do mezipaměti `DataCacheFactory` a `DataCache` třídy. Ve službě Azure Redis Cache spravuje tato připojení `ConnectionMultiplexer` třídy.

Přidejte následující příkaz do horní části souboru, ve které chcete získat přístup k mezipaměti.

```csharp
using StackExchange.Redis
```

Pokud se tento obor názvů se nevyřeší, ujistěte se, že jste přidali balíček StackExchange.Redis NuGet, jak je popsáno v [rychlý start: použití Azure Redis Cache s aplikací .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Všimněte si, že klient StackExchange.Redis vyžaduje rozhraní .NET Framework 4 nebo vyšší.
> 
> 

Chcete-li připojit k instanci služby Azure Redis Cache, zavolejte statickou `ConnectionMultiplexer.Connect` metoda a předejte jí koncový bod a klíč. Jeden ze způsobů sdílení instance `ConnectionMultiplexer` v aplikaci je pomocí statické vlastnosti, která vrací připojenou instanci, podobně jako v následujícím příkladu.  Tento přístup poskytuje způsob inicializace jedné připojené bezpečné pro vlákna `ConnectionMultiplexer` instance. V tomto příkladu `abortConnect` je nastavena na hodnotu false, což znamená, že volání bude úspěšné i v případě, že nedojde k připojení k mezipaměti. Klíčovou vlastností `ConnectionMultiplexer` je automatické obnovení připojení k mezipaměti po vyřešení problémů se sítí nebo jiných příčin.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Koncový bod mezipaměti, klíče a portů můžete získat **Redis Cache** okně instance mezipaměti. Další informace najdete v tématu [vlastnosti mezipaměti redis cache](cache-configure.md#properties).

Po vytvoření připojení vrátíte odkaz na databázi mezipaměti Redis voláním `ConnectionMultiplexer.GetDatabase` metody. Objekt vrácený metodou `GetDatabase` je prostý průchozí objekt a není nutné jej ukládat.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

Klient StackExchange.Redis používá `RedisKey` a `RedisValue` typy pro přístup a ukládání položek v mezipaměti. Tyto typy mapovány na nejvíce primitivní typy jazyka, včetně řetězců a často nepoužívají se přímo. Redis jsou naprosto základní typ Redis hodnoty řetězce a může obsahovat mnoho typů dat, včetně serializovaných binárních datových proudů, a když typ nelze použít přímo, použijete metody, které obsahují `String` v názvu. Pro největší primitivní datové typy, ukládat a načítat položky z mezipaměti pomocí `StringSet` a `StringGet` metody, pokud jsou kolekce nebo jiných datových typů Redis ukládání do mezipaměti. 

`StringSet` a `StringGet` jsou podobné Managed Cache Service `Put` a `Get` metody s jedním hlavním rozdíl, že se před nastavit a získat objekt .NET do mezipaměti GAC musí serializovat nejprve. 

Při volání metody `StringGet`, pokud objekt existuje, bude vrácen, a pokud tomu tak není, je vrácena hodnota null. V takovém případě můžete načíst hodnotu z požadovaného zdroje dat a ukládání do mezipaměti pro pozdější použití. Tento model se označuje jako model doplňování mezipaměti.

Chcete-li zadat vypršení platnosti položky v mezipaměti, použijte parametr `TimeSpan` metody `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Redis Cache můžete pracovat s objekty .NET i primitivní datové typy, ale před uložením objektu .NET můžete mezipaměti musí být serializován. Serializace je odpovědností vývojáře aplikací a poskytuje flexibilita při výběru serializátoru. Další informace a ukázky kódu najdete v tématu [práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migrace stavu relace ASP.NET a ukládání výstupu do mezipaměti pro Azure Redis Cache
Azure Redis Cache má zprostředkovatele stavu relace ASP.NET a ukládání výstupu stránek. Migrací vaší aplikace, která používá verze služby Managed Cache Service z těchto zprostředkovatelů, nejprve odeberte stávající oddíly ze souboru web.config a potom nakonfigurujte Azure Redis Cache verze zprostředkovatele. Pokyny k používání Azure Redis Cache ASP.NET poskytovatelů najdete v tématu [zprostředkovatel stavu relací ASP.NET pro Azure Redis Cache](cache-aspnet-session-state-provider.md) a [zprostředkovateli výstupní mezipaměti ASP.NET pro Azure Redis Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Další postup
Prozkoumejte [dokumentace ke službě Azure Redis Cache](https://azure.microsoft.com/documentation/services/cache/) pro kurzy, ukázky, videa a další.

