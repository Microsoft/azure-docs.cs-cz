---
title: Migrace aplikací služby Spravované mezipaměti do Redis – Azure
description: Zjistěte, jak migrovat aplikace služby spravované mezipaměti a mezipaměť do mezipaměti Azure Pro redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 9596b8cb771f114cb09c5d6c6ae33b4fc4a8cada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122686"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrace ze služby Managed Cache Service na službu Azure Cache for Redis
Migrace aplikací, které používají službu Azure Managed Cache Service do Azure Cache for Redis, lze provést s minimálními změnami vaší aplikace v závislosti na funkcích služby Spravované mezipaměti používaných vaší aplikací pro ukládání do mezipaměti. Zatímco api nejsou přesně stejné jsou podobné a velká část existujícího kódu, který používá službu spravované mezipaměti pro přístup ke mezipaměti lze znovu použít s minimálními změnami. Tento článek ukazuje, jak provést potřebné změny konfigurace a aplikací pro migraci aplikací služby spravované mezipaměti pro použití azure cache pro Redis a ukazuje, jak lze některé funkce Azure Cache for Redis použít k implementaci funkcí a mezipaměť služby spravované mezipaměti.

>[!NOTE]
>Služba Spravované mezipaměti a Mezipaměť rolí byly [vyřazeny](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30. Pokud máte nasazení v mezipaměti role, které chcete migrovat do mezipaměti Azure pro Redis, můžete postupovat podle kroků v tomto článku.

## <a name="migration-steps"></a>Kroky migrace
Následující kroky jsou nutné k migraci aplikace služby spravované mezipaměti k použití azure cache pro Redis.

* Mapování funkcí služby spravované mezipaměti do mezipaměti Azure pro Redis
* Zvolte nabídku mezipaměti
* Vytvoření mezipaměti
* Konfigurace klientů mezipaměti
  * Odebrání konfigurace služby Spravované mezipaměti
  * Konfigurace klienta mezipaměti pomocí balíčku StackExchange.Redis NuGet
* Migrace kódu služby spravované mezipaměti
  * Připojení ke mezipaměti pomocí třídy ConnectionMultiplexer
  * Přístup k primitivním datovým typům v mezipaměti
  * Práce s objekty .NET v mezipaměti
* Migrace ASP.NET stavu relace a ukládání výstupu do mezipaměti Azure pro Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Mapování funkcí služby spravované mezipaměti do mezipaměti Azure pro Redis
Služba Azure Managed Cache Service a Azure Cache for Redis jsou podobné, ale implementují některé jejich funkce různými způsoby. Tato část popisuje některé rozdíly a poskytuje pokyny k implementaci funkcí služby spravované mezipaměti v Azure Cache for Redis.

| Funkce služby Spravované mezipaměti | Podpora služby Spravované mezipaměti | Podpora Azure Cache for Redis |
| --- | --- | --- |
| Pojmenované mezipaměti |Je nakonfigurována výchozí mezipaměť a v nabídce mezipaměti Standard a Premium lze v případě potřeby nakonfigurovat až devět dalších pojmenovaných mezipamětí. |Azure Cache for Redis má konfigurovatelný počet databází (výchozí 16), které lze použít k implementaci podobné funkce pojmenované mezipaměti. Další informace najdete v tématu [Co jsou databáze Redis?](cache-faq.md#what-are-redis-databases) a [Výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration). |
| Vysoká dostupnost |Poskytuje vysokou dostupnost pro položky v mezipaměti v nabídce mezipaměti Standard a Premium. Pokud dojde ke ztrátě položek v důsledku selhání, záložní kopie položek v mezipaměti jsou stále k dispozici. Zápisy do sekundární mezipaměti jsou prováděny synchronně. |Vysoká dostupnost je k dispozici v nabídce mezipaměti Standard a Premium, které mají konfiguraci primární nebo repliky dvou uzlů (každý úlomek v mezipaměti Premium má dvojici primárních a replik). Zápisy do repliky jsou provedeny asynchronně. Další informace najdete v tématu [Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/). |
| Oznámení |Umožňuje klientům přijímat asynchronní oznámení, když dojde k různým operacím mezipaměti v pojmenované mezipaměti. |Klientské aplikace mohou používat redis pub/sub nebo [Keyspace oznámení](cache-configure.md#keyspace-notifications-advanced-settings) k dosažení podobné funkce jako oznámení. |
| Místní mezipaměť |Ukládá kopii objektů v mezipaměti místně na straně klienta pro extra rychlý přístup. |Klientské aplikace by musely implementovat tuto funkci pomocí slovníku nebo podobné datové struktury. |
| Politika vystěhování |Žádné nebo LRU. Výchozí zásadou je LRU. |Azure Cache for Redis podporuje následující zásady vyřazovacího příkazu: volatile-lru, allkeys-lru, volatile-random, allkeys-random, volatile-ttl, noeviction. Výchozí zásada je volatile-lru. Další informace naleznete [v tématu Default Redis server configuration](cache-configure.md#default-redis-server-configuration). |
| Zásady vypršení platnosti |Výchozí zásada vypršení platnosti je Absolutní a výchozí interval vypršení platnosti je 10 minut. K dispozici jsou také posuvné a nikdy. |Ve výchozím nastavení položky v mezipaměti nevyprší, ale vypršení platnosti lze nakonfigurovat na základě pro zápis pomocí přetížení sady mezipaměti. |
| Oblasti a označování |Oblasti jsou podskupiny pro položky uložené v mezipaměti. Oblasti také podporují poznámku položek uložených v mezipaměti s dalšími popisnými řetězci nazývanými značky. Oblasti podporují možnost provádět operace vyhledávání u všech označených položek v dané oblasti. Všechny položky v rámci oblasti jsou umístěny v rámci jednoho uzlu clusteru mezipaměti. |Mezipaměť Azure pro redis se skládá z jednoho uzlu (pokud není povolen cluster Redis), takže koncept oblastí služby spravované mezipaměti se nevztahuje. Redis podporuje vyhledávání a operace se zástupnými kódy při načítání klíčů, takže popisné značky mohou být vloženy do názvů klíčů a použity k načtení položek později. Příklad implementace řešení označování pomocí redisu najdete v [tématu Implementace označování mezipaměti pomocí redisu](https://stackify.com/implementing-cache-tagging-redis/). |
| Serializace |Spravovaná mezipaměť podporuje NetDataContractSerializer, BinaryFormatter a použití vlastních serializátorů. Výchozí hodnota je NetDataContractSerializer. |Je odpovědností klientské aplikace serializovat objekty .NET před jejich umístěním do mezipaměti s volbou serializátoru až vývojáře klientské aplikace. Další informace a ukázkový kód naleznete [v tématu Práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulátor mezipaměti |Spravovaná mezipaměť poskytuje emulátor místní mezipaměti. |Azure Cache for Redis nemá emulátor, ale můžete [spustit sestavení MSOpenTech redis-server.exe místně](cache-faq.md#cache-emulator) poskytnout prostředí emulátoru. |

## <a name="choose-a-cache-offering"></a>Zvolte nabídku mezipaměti
Mezipaměť Microsoft Azure pro redis je k dispozici v následujících úrovních:

* **Basic** – jeden uzel. Více velikostí až do 53 GB.
* **Standard** – dva uzly Primární/Replika. Více velikostí až do 53 GB. 99,9% SLA.
* **Premium** – dva uzly Primární/Replika s až 10 horizontálními oddíly. Více velikostí od 6 GB do 1,2 TB. Všechny funkce úrovně Standard a navíc podpora [clusteru Redis](cache-how-to-premium-clustering.md), [trvalosti Redis](cache-how-to-premium-persistence.md) a [služby Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9% SLA.

Každá úroveň se liší z hlediska funkcí a cen. Funkce jsou uvedeny dále v této příručce a další informace o cenách naleznete v [tématu Podrobnosti o cenách mezipaměti](https://azure.microsoft.com/pricing/details/cache/).

Výchozím bodem pro migraci je vybrat velikost, která odpovídá velikosti předchozí mezipaměti spravované mezipaměti, a potom vertikálně navýšit nebo snížit kapacitu v závislosti na požadavcích vaší aplikace. Další informace o výběru správné nabídky Azure Cache for Redis najdete v [tématu Co azure cache pro Redis nabízí a velikost bych měl použít](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Vytvoření mezipaměti
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurace klientů mezipaměti
Po vytvoření a konfiguraci mezipaměti je dalším krokem odebrání konfigurace služby Spravované mezipaměti a přidání konfigurace a odkazů azure cache pro redis, aby klienti mezipaměti měli přístup ke mezipaměti.

* Odebrání konfigurace služby Spravované mezipaměti
* Konfigurace klienta mezipaměti pomocí balíčku StackExchange.Redis NuGet

### <a name="remove-the-managed-cache-service-configuration"></a>Odebrání konfigurace služby Spravované mezipaměti
Předtím, než lze klientské aplikace nakonfigurovat pro Azure Cache for Redis, musí být existující konfigurace služby spravované mezipaměti a odkazy na sestavení odebrány odinstalováním balíčku NuGet služby spravované mezipaměti.

Chcete-li odinstalovat balíček NuGet služby spravované mezipaměti, klepněte pravým tlačítkem myši na projekt klienta v **Průzkumníku řešení** a zvolte **Spravovat balíčky NuGet**. Vyberte uzel **Nainstalované balíčky** a do pole Hledat nainstalované balíčky zadejte W**indowsAzure.Caching.** Vyberte **Mezipaměť Windows** **Azure** (nebo ukládání do **mezipaměti** **Windows** Azure v závislosti na verzi balíčku NuGet), klikněte na **Odinstalovat**a potom klikněte na **Zavřít**.

![Odinstalace balíčku NuGet služby Azure Managed Cache](./media/cache-migrate-to-redis/IC757666.jpg)

Odinstalováním balíčku NuGet služby spravované mezipaměti odeberete sestavení služby Spravované mezipaměti a položky služby Spravované mezipaměti v souboru app.config nebo web.config klientské aplikace. Vzhledem k tomu, že některá přizpůsobená nastavení nemusí být při odinstalaci balíčku NuGet odebrána, otevřete web.config nebo app.config a ujistěte se, že jsou odebrány následující prvky.

Ujistěte `dataCacheClients` se, že `configSections` položka je odebrána z prvku. Neodstraňujte celý `configSections` prvek; stačí odstranit `dataCacheClients` položku, pokud je k dispozici.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Ujistěte `dataCacheClients` se, že je oddíl odebrán. Sekce `dataCacheClients` bude podobná následujícímu příkladu.

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

Po odebrání konfigurace služby Spravované mezipaměti můžete nakonfigurovat klienta mezipaměti, jak je popsáno v následující části.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurace klienta mezipaměti pomocí balíčku StackExchange.Redis NuGet
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrace kódu služby spravované mezipaměti
Rozhraní API pro klienta StackExchange.Azure Cache for Redis je podobné službě spravované mezipaměti. Tato část obsahuje přehled rozdílů.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Připojení ke mezipaměti pomocí třídy ConnectionMultiplexer
Ve službě Spravované mezipaměti byla připojení `DataCacheFactory` ke `DataCache` mezipaměti zpracována třídami a. V Azure Cache pro Redis jsou `ConnectionMultiplexer` tato připojení spravovaná třídou.

Přidejte následující příkaz using do horní části libovolného souboru, ze kterého chcete získat přístup ke mezipaměti.

```csharp
using StackExchange.Redis
```

Pokud se tento obor názvů nevyřeší, ujistěte se, že jste přidali balíček StackExchange.Redis NuGet, jak je popsáno v [úvodním panelu: Použijte Azure Cache pro Redis s aplikací .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Všimněte si, že klient StackExchange.Redis vyžaduje rozhraní .NET Framework 4 nebo vyšší.
> 
> 

Chcete-li se připojit k instanci `ConnectionMultiplexer.Connect` Azure Cache for Redis, zavolejte statickou metodu a předejte koncový bod a klíč. Jeden ze způsobů sdílení instance `ConnectionMultiplexer` v aplikaci je pomocí statické vlastnosti, která vrací připojenou instanci, podobně jako v následujícím příkladu.  Tento přístup poskytuje bezpečný způsob, jak inicializovat jednu připojenou `ConnectionMultiplexer` instanci. V tomto `abortConnect` příkladu je nastavena na false, což znamená, že volání bude úspěšné i v případě, že připojení ke mezipaměti není vytvořena. Klíčovou vlastností `ConnectionMultiplexer` je automatické obnovení připojení k mezipaměti po vyřešení problémů se sítí nebo jiných příčin.

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

Koncový bod mezipaměti, klíče a porty lze získat z okna **Azure Cache for Redis** pro instanci mezipaměti. Další informace naleznete v [tématu Azure Cache for Redis vlastnosti](cache-configure.md#properties).

Po navázání připojení vraťte odkaz na databázi Azure Cache `ConnectionMultiplexer.GetDatabase` for Redis voláním metody. Objekt vrácený metodou `GetDatabase` je prostý průchozí objekt a není nutné jej ukládat.

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

Klient StackExchange.Redis používá `RedisKey` `RedisValue` typy a pro přístup a ukládání položek v mezipaměti. Tyto typy mapovat na nejprimitivnější typy jazyků, včetně řetězce a často nejsou používány přímo. Redis Řetězce jsou nejzákladnější druh Redis hodnotu a může obsahovat mnoho typů dat, včetně serializovaných binární datové proudy `String` a zatímco nemusí používat typ přímo, budete používat metody, které obsahují v názvu. U nejprimitivnějších datových typů ukládáte `StringSet` a `StringGet` načítáte položky z mezipaměti pomocí metod a, pokud neukládáte kolekce nebo jiné datové typy Redis do mezipaměti. 

`StringSet`a `StringGet` jsou podobné službě `Put` spravované `Get` mezipaměti a metodám, přičemž jeden hlavní rozdíl spočá, že před nastavením a získáním objektu .NET do mezipaměti je nutné jej nejprve serializovat. 

Při `StringGet`volání , pokud objekt existuje, je vrácena a pokud tomu tak není, null je vrácena. V takovém případě můžete hodnotu načíst z požadovaného zdroje dat a uložit ji do mezipaměti pro následné použití. Tento vzor se označuje jako vzor cache-aside.

Chcete-li zadat vypršení platnosti položky v mezipaměti, použijte parametr `TimeSpan` metody `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Cache for Redis může pracovat s objekty .NET i primitivními datovými typy, ale před uložením objektu .NET do mezipaměti musí být serializován. Tato serializace je odpovědností vývojáře aplikace a poskytuje vývojářflexibilitu při výběru serializátoru. Další informace a ukázkový kód naleznete [v tématu Práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrace ASP.NET stavu relace a ukládání výstupu do mezipaměti Azure pro Redis
Azure Cache for Redis má zprostředkovatele pro ukládání do mezipaměti ASP.NET stavu relace i pro ukládání výstupu stránky do mezipaměti. Chcete-li migrovat aplikaci, která používá verze služby Spravované mezipaměti těchto zprostředkovatelů, nejprve odeberte existující oddíly z webu. Pokyny k použití zprostředkovatelů ASP.NET Azure Cache for Redis najdete [v tématu ASP.NET zprostředkovatele stavu relací pro Azure Cache for Redis](cache-aspnet-session-state-provider.md) a [ASP.NET zprostředkovatele výstupní mezipaměti pro Azure Cache for Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Další kroky
Prozkoumejte [dokumentaci k Azure Cache for Redis,](https://azure.microsoft.com/documentation/services/cache/) kde nazímůžete s kurzy, ukázkami, videi a dalšími informacemi.

