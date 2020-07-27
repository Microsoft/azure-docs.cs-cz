---
title: Migrace aplikací Managed Cache Service do Redis – Azure
description: Naučte se migrovat aplikace Managed Cache Service a Mezipaměť hostovaná v instanci role do Azure cache pro Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: yegu
ms.openlocfilehash: 3f5cfccd1f85f68c619192496c62bf80ea8d4785
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170181"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis-deprecated"></a>Migrace z Managed Cache Service do mezipaměti Azure pro Redis (zastaralé)
Migrace aplikací používajících Azure Managed Cache Service do mezipaměti Azure pro Redis se dá udělat s minimálními změnami vaší aplikace, a to v závislosti na funkcích Managed Cache Service používaných vaší aplikací pro ukládání do mezipaměti. I když rozhraní API nejsou přesně stejná, jsou podobná a většina vašeho stávajícího kódu, který používá Managed Cache Service pro přístup do mezipaměti, může být použita s minimálními změnami. V tomto článku se dozvíte, jak provést potřebné změny konfigurace a aplikace pro migraci aplikací Managed Cache Service pro použití Azure cache pro Redis a ukazuje, jak se některé funkce mezipaměti Azure pro Redis dají použít k implementaci funkcí mezipaměti Managed Cache Service.

>[!NOTE]
>Managed Cache Service a Mezipaměť hostovaná v instanci role byly [vyřazení](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30. listopadu 2016. Pokud máte nasazení Mezipaměť hostovaná v instanci role, která chcete migrovat do mezipaměti Azure pro Redis, můžete postupovat podle kroků v tomto článku.

## <a name="migration-steps"></a>Kroky migrace
K migraci Managed Cache Service aplikace pro použití mezipaměti Azure pro Redis se vyžadují následující kroky.

* Mapování funkcí Managed Cache Service do mezipaměti Azure pro Redis
* Výběr nabídky mezipaměti
* Vytvoření mezipaměti
* Konfigurace klientů mezipaměti
  * Odebrat konfiguraci Managed Cache Service
  * Konfigurace klienta mezipaměti pomocí balíčku NuGet StackExchange. Redis
* Migrace kódu Managed Cache Service
  * Připojení k mezipaměti pomocí třídy ConnectionMultiplexer
  * Přístup k primitivním datovým typům v mezipaměti
  * Práce s objekty .NET v mezipaměti
* Migrace stavu relace ASP.NET a ukládání výstupu do mezipaměti do Azure cache pro Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Mapování funkcí Managed Cache Service do mezipaměti Azure pro Redis
Azure Managed Cache Service a Azure cache pro Redis jsou podobné, ale implementují některé z jejich funkcí různými způsoby. Tato část popisuje některé rozdíly a poskytuje pokyny k implementaci funkcí Managed Cache Service v mezipaměti Azure pro Redis.

| Managed Cache Service funkce | Podpora Managed Cache Service | Podpora Azure cache pro Redis |
| --- | --- | --- |
| Pojmenované mezipaměti |V případě potřeby je nakonfigurovaná výchozí mezipaměť a v nabídkách mezipaměti Standard a Premium můžete nakonfigurovat až devět dalších pojmenovaných mezipamětí. |Mezipaměť Azure pro Redis má konfigurovatelný počet databází (výchozí hodnota je 16), které lze použít k implementaci podobných funkcí do pojmenovaných mezipamětí. Další informace najdete v tématu [Co jsou databáze Redis?](cache-faq.md#what-are-redis-databases) a [Výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration). |
| Vysoká dostupnost |Poskytuje vysokou dostupnost pro položky v mezipaměti v nabídkách mezipaměti Standard a Premium. Pokud dojde ke ztrátě položek z důvodu chyby, jsou záložní kopie položek v mezipaměti stále k dispozici. Zápisy do mezipaměti repliky jsou prováděny synchronně. |Vysoká dostupnost je dostupná v nabídkách mezipaměti Standard a Premium, které mají konfiguraci se dvěma primárními a replikami uzlů (každý horizontálních oddílů v mezipaměti Premium má dvojici primárního/repliky). Zápisy do repliky se provádějí asynchronně. Další informace najdete v tématu [ceny služby Azure cache pro Redis](https://azure.microsoft.com/pricing/details/cache/). |
| Oznámení |Umožňuje klientům přijímat asynchronní oznámení v případě, že dojde k různým operacím mezipaměti v pojmenované mezipaměti. |Klientské aplikace mohou používat oznámení Redis k publikování a [podprostorům](cache-configure.md#keyspace-notifications-advanced-settings) a k dosažení podobných funkcí oznámení. |
| Místní mezipaměť |Ukládá kopii objektů uložených v mezipaměti lokálně na klientovi, aby bylo velmi rychlém přístupu. |Klientské aplikace by musely tuto funkci implementovat pomocí slovníku nebo podobné struktury dat. |
| Zásada vyřazení |Žádné nebo LRU. Výchozí zásada je LRU. |Azure cache pro Redis podporuje následující zásady vyřazení: volatile-LRU, AllKeys-LRU, volatile náhodné, AllKeys-Random, volatile-TTL, vyřazení. Výchozí zásada je nestálá – LRU. Další informace najdete v tématu [výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration). |
| Zásady vypršení platnosti |Výchozí zásady vypršení platnosti jsou absolutní a výchozí interval vypršení platnosti je 10 minut. K dispozici jsou také pozvolna a nikdy žádné zásady. |Ve výchozím nastavení se položky v mezipaměti nevypršení platnosti, ale vypršení platnosti je možné nakonfigurovat na základě zápisu pomocí přetížení sady mezipaměti. |
| Oblasti a označování |Oblasti jsou podskupiny pro položky v mezipaměti. Oblasti také podporují poznámky k položkám uloženým v mezipaměti s dalšími popisnými řetězci s názvem Tags. Oblasti podporují možnost provádět operace hledání u všech označených položek v této oblasti. Všechny položky v rámci oblasti jsou umístěny v jednom uzlu clusteru mezipaměti. |Mezipaměť Azure pro Redis se skládá z jednoho uzlu (Pokud není povolený cluster Redis), takže koncept Managed Cache Service oblasti neplatí. Redis podporuje vyhledávání a zástupné operace při načítání klíčů, takže popisné značky mohou být vloženy do názvů klíčů a použity pro pozdější načtení položek. Příklad implementace označení řešení pomocí Redis naleznete v tématu [implementace označování mezipaměti pomocí Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Serializace |Spravovaná mezipaměť podporuje NetDataContractSerializer, BinaryFormatter a použití vlastních serializátorů. Výchozí hodnota je NetDataContractSerializer. |Je zodpovědností klientské aplikace o serializaci objektů .NET před jejich vložením do mezipaměti s možností volby serializátoru až po vývojáře klientské aplikace. Další informace a ukázkový kód naleznete v tématu [práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulátor mezipaměti |Spravovaná mezipaměť poskytuje emulátor místní mezipaměti. |Mezipaměť Azure pro Redis neobsahuje emulátor, ale můžete [Spustit MSOpenTech build redis-server.exe místně](cache-faq.md#cache-emulator) a poskytnout tak prostředí emulátoru. |

## <a name="choose-a-cache-offering"></a>Výběr nabídky mezipaměti
Microsoft Azure cache pro Redis je k dispozici na následujících úrovních:

* **Basic** – jeden uzel. Více velikostí až do 53 GB.
* **Standard** – dva uzly Primární/Replika. Více velikostí až do 53 GB. 99,9% SLA.
* **Premium** – dva uzly Primární/Replika s až 10 horizontálními oddíly. Více velikostí z 6 GB na 1,2 TB. Všechny funkce úrovně Standard a navíc podpora [clusteru Redis](cache-how-to-premium-clustering.md), [trvalosti Redis](cache-how-to-premium-persistence.md) a [služby Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9% SLA.

Každá úroveň se liší z hlediska funkcí a cen. Tyto funkce jsou uvedené dále v této příručce a další informace o cenách najdete v tématu [Podrobnosti o cenách mezipaměti](https://azure.microsoft.com/pricing/details/cache/).

Výchozím bodem migrace je výběr velikosti, která odpovídá velikosti předchozí mezipaměti Managed Cache Service, a pak horizontální navýšení nebo snížení kapacity v závislosti na požadavcích vaší aplikace. Další informace o výběru správné mezipaměti Azure pro nabídku Redis najdete v tématu [co mám použít v mezipaměti Azure pro nabídku a velikost služby Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Vytvoření mezipaměti
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurace klientů mezipaměti
Po vytvoření a konfiguraci mezipaměti je dalším krokem odebrání konfigurace Managed Cache Service a Přidání konfigurace mezipaměti Azure pro Redis a odkazů tak, aby klienti mezipaměti mohli přistupovat k mezipaměti.

* Odebrat konfiguraci Managed Cache Service
* Konfigurace klienta mezipaměti pomocí balíčku NuGet StackExchange. Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Odebrat konfiguraci Managed Cache Service
Aby bylo možné konfigurovat klientské aplikace pro službu Azure cache pro Redis, je nutné odebrat existující konfiguraci Managed Cache Service a odkazy na sestavení odinstalováním balíčku NuGet Managed Cache Service.

Pokud chcete odinstalovat Managed Cache Service balíček NuGet, klikněte pravým tlačítkem na projekt klienta v **Průzkumník řešení** a vyberte **Spravovat balíčky NuGet**. Vyberte uzel **nainstalované balíčky** a do pole Hledat nainstalované balíčky zadejte W**indowsAzure. Caching** . Vyberte **Windows** **mezipaměť Windows Azure** (nebo **mezipaměť Windows** **Azure** v závislosti na verzi balíčku NuGet), klikněte na **odinstalovat**a pak klikněte na **Zavřít**.

![Odinstalace balíčku NuGet pro Azure Managed Cache Service](./media/cache-migrate-to-redis/IC757666.jpg)

Odinstalace balíčku Managed Cache Service NuGet odebere Managed Cache Service sestavení a Managed Cache Service položky v app.config nebo web.config klientské aplikaci. Vzhledem k tomu, že některá přizpůsobená nastavení nemusí být odebrána při odinstalaci balíčku NuGet, otevřete web.config nebo app.config a zajistěte, aby byly odebrány následující prvky.

Ujistěte se, že `dataCacheClients` je položka odebrána z `configSections` prvku. Neodstraňujte celý `configSections` element. `dataCacheClients` Pokud je přítomen, stačí položku odebrat.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Ujistěte se, že `dataCacheClients` je oddíl odebraný. `dataCacheClients`Oddíl bude podobný následujícímu příkladu.

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

Po odebrání konfigurace Managed Cache Service můžete nakonfigurovat klienta mezipaměti, jak je popsáno v následující části.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurace klienta mezipaměti pomocí balíčku NuGet StackExchange. Redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrace kódu Managed Cache Service
Rozhraní API pro StackExchange. Azure cache pro klienta Redis je podobné Managed Cache Service. V této části najdete Přehled rozdílů.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Připojení k mezipaměti pomocí třídy ConnectionMultiplexer
V Managed Cache Service byly připojení k mezipaměti zpracována `DataCacheFactory` `DataCache` třídami a. V mezipaměti Azure pro Redis jsou tato připojení spravovaná `ConnectionMultiplexer` třídou.

Do horní části libovolného souboru, ze kterého chcete získat přístup k mezipaměti, přidejte následující příkaz using.

```csharp
using StackExchange.Redis
```

Pokud tento obor názvů nepůjde vyřešit, ujistěte se, že jste přidali balíček NuGet StackExchange. Redis, jak je popsáno v tématu [rychlý Start: použití mezipaměti Azure pro Redis s aplikací .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Všimněte si, že klient StackExchange. Redis vyžaduje .NET Framework 4 nebo vyšší.
> 
> 

Pokud se chcete připojit k mezipaměti Azure pro instanci Redis, zavolejte statickou `ConnectionMultiplexer.Connect` metodu a předejte do něj koncový bod a klíč. Jeden ze způsobů sdílení instance `ConnectionMultiplexer` v aplikaci je pomocí statické vlastnosti, která vrací připojenou instanci, podobně jako v následujícím příkladu.  Tento přístup poskytuje způsob inicializace jedné připojené instance způsobem bezpečným pro přístup z více vláken `ConnectionMultiplexer` . V tomto příkladu `abortConnect` je nastavena hodnota false, což znamená, že volání bude úspěšné i v případě, že není navázáno připojení k mezipaměti. Klíčovou vlastností `ConnectionMultiplexer` je automatické obnovení připojení k mezipaměti po vyřešení problémů se sítí nebo jiných příčin.

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

Koncový bod mezipaměti, klíče a porty lze získat z okna **Azure cache pro Redis** pro vaši instanci mezipaměti. Další informace najdete v tématu [vlastnosti Azure cache pro Redis](cache-configure.md#properties).

Po navázání spojení vraťte odkaz na databázi Azure cache pro Redis voláním `ConnectionMultiplexer.GetDatabase` metody. Objekt vrácený metodou `GetDatabase` je prostý průchozí objekt a není nutné jej ukládat.

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

Klient StackExchange. Redis používá `RedisKey` `RedisValue` typy a pro přístup k položkám v mezipaměti a jejich ukládání. Tyto typy jsou mapovány na většinu primitivních typů jazyků, včetně řetězce a často nejsou použity přímo. Řetězce Redis jsou nejzákladnější typ Redis hodnoty a mohou obsahovat mnoho typů dat, včetně serializovaných binárních proudů a i když nemůžete typ použít přímo, budete používat metody, které `String` v názvu obsahují. Pro většinu primitivních datových typů ukládáte a načítáte položky z mezipaměti pomocí `StringSet` metod a `StringGet` , pokud do mezipaměti neukládáte kolekce nebo jiné Redis datové typy. 

`StringSet`a `StringGet` jsou podobné Managed Cache Service `Put` a `Get` metodám, s jedním velkým rozdílem, že před nastavením a získáním objektu .NET do mezipaměti je nutné nejprve serializovat. 

Při volání `StringGet` , pokud objekt existuje, je vráceno a pokud není, je vrácena hodnota null. V takovém případě můžete načíst hodnotu z požadovaného zdroje dat a uložit ji do mezipaměti pro pozdější použití. Tento model je známý jako model doplňování mezipaměti.

Chcete-li zadat vypršení platnosti položky v mezipaměti, použijte parametr `TimeSpan` metody `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure cache pro Redis může pracovat s objekty .NET i primitivními datovými typy, ale před tím, než se objekt .NET může ukládat do mezipaměti, musí být serializován. Tato serializace je zodpovědností vývojáře aplikace a dává vývojářům flexibilitu v možnosti volby serializátoru. Další informace a ukázkový kód naleznete v tématu [práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrace stavu relace ASP.NET a ukládání výstupu do mezipaměti do Azure cache pro Redis
Mezipaměť Azure pro Redis má zprostředkovatele pro ukládání stavu relace ASP.NET a ukládání výstupu stránky do mezipaměti. Pokud chcete migrovat aplikaci, která používá Managed Cache Service verze těchto zprostředkovatelů, odeberte nejdřív stávající oddíly z web.config a pak nakonfigurujte Azure cache pro Redis verze zprostředkovatelů. Pokyny k používání mezipaměti Azure pro poskytovatele ASP.NET pro Redis najdete v tématu zprostředkovatel [stavu relace ASP.NET pro Azure cache pro](cache-aspnet-session-state-provider.md) [poskytovatele výstupní mezipaměti pro Redis a ASP.NET pro Azure cache pro Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Další kroky
Prozkoumejte [dokumentaci k Azure cache for Redis](https://azure.microsoft.com/documentation/services/cache/) pro kurzy, ukázky, videa a další materiály.

