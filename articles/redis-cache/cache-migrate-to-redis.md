---
title: Migrace aplikací spravovaných Cache Service k Redis - Azure | Microsoft Docs
description: Zjistěte, jak migrovat aplikací spravované služby mezipaměti a mezipaměť hostovaná v instanci Role na Azure Redis Cache
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
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639482"
---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrovat na Azure Redis Cache z Managed Cache Service
Migrace vaší aplikace, které používají Azure spravované mezipaměti Service k Azure Redis Cache lze provést s minimálními změnami k vaší aplikaci, v závislosti na spravované služby mezipaměti funkce, které používá aplikaci ukládání do mezipaměti. Když rozhraní API jsou přesně stejný jsou podobné a většinu váš stávající kód, který používá služba spravovaných mezipaměti pro přístup k mezipaměti lze opětovně použít s minimálními změnami. Tento článek ukazuje, jak chcete-li nezbytné konfigurace a změny aplikace k migraci aplikace spravované služby mezipaměti použití Azure Redis Cache a ukazuje, jak některé funkce Azure Redis Cache lze použít k implementaci funkce Spravované služby mezipaměti mezipaměti.

>[!NOTE]
>Spravované služby mezipaměti a mezipaměť hostovaná v instanci Role byly [vyřazeno](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30. listopadu 2016. Pokud máte všechna nasazení mezipaměť hostovaná v instanci Role, které chcete provést migraci na Azure Redis Cache, můžete podle kroků v tomto článku.

## <a name="migration-steps"></a>Kroky migrace
Následující kroky jsou potřeba k migraci aplikace spravované služby mezipaměti použití Azure Redis Cache.

* Mapování funkce spravované služby mezipaměti na Azure Redis Cache
* Vyberte nabídku mezipaměti
* Vytvoření mezipaměti
* Konfigurace klientů mezipaměti
  * Odeberte konfiguraci Managed Cache Service
  * Konfigurace klienta mezipaměti pomocí balíčku StackExchange.Redis NuGet
* Migrace kódu spravované služby mezipaměti
  * Připojení k mezipaměti pomocí ConnectionMultiplexer – třída
  * Primitivní datové typy přístupu v mezipaměti
  * Práce s objekty .NET v mezipaměti
* Migrace stavu relace ASP.NET a ukládání výstupu do mezipaměti pro Azure Redis Cache 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Mapování funkce spravované služby mezipaměti na Azure Redis Cache
Azure spravované Cache Service a Azure Redis Cache jsou podobné, ale některé z jejich funkcí implementovat různými způsoby. Tato část popisuje některé rozdíly a poskytuje pokyny k implementaci funkcí služby mezipaměti spravované ve službě Azure Redis Cache.

| Spravované funkce Cache Service | Podpora spravovaného Cache Service | Podpora Azure Redis Cache |
| --- | --- | --- |
| Pojmenované mezipaměti |Výchozí mezipaměti je nakonfigurován, a v mezipaměti Standard a Premium lze nakonfigurovat nabídky až 9 další s názvem mezipaměti v případě potřeby. |Mezipamětí Azure Redis mít konfigurovat počet databází (výchozí 16), které lze použít k implementaci podobné funkce jako s názvem mezipamětí. Další informace najdete v tématu [Co jsou databáze Redis?](cache-faq.md#what-are-redis-databases) a [Výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration). |
| Vysoká dostupnost |Poskytuje vysokou dostupnost pro položky v mezipaměti v mezipaměti nabídky Standard a Premium. Pokud jsou položky ztraceno v důsledku selhání, jsou stále k dispozici záložní kopie položek v mezipaměti. Zápis do mezipaměti v sekundární jsou vytvářeny synchronně. |Zajištění vysoké dostupnosti je k dispozici v Standard a Premium nabídky mezipaměti, které mají konfiguraci dva uzly primární/replika (pár primární/replika má každý horizontálního oddílu v mezipaměti Premium). Zápis do repliky jsou vytvářeny asynchronně. Další informace najdete v tématu [cenách Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/). |
| Oznámení |Umožňuje klientům přijímat asynchronní upozornění, když celou řadu operace mezipaměti, ke kterým došlo u pojmenované mezipaměti. |Klientské aplikace můžete použít protokol pub nebo sub Redis nebo [oznámení Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) k dosažení podobné funkce pro oznámení. |
| Místní mezipaměť |Ukládá kopie v mezipaměti objektů místně na klientovi velmi rychlý přístup. |Klientské aplikace by bylo nutné implementovat tuto funkci pomocí slovníku nebo podobné datové struktury. |
| Zásady vyřazení |Žádná nebo hodnoty nejdelšího Nepoužití. Výchozí zásada je hodnoty nejdelšího Nepoužití. |Azure Redis Cache podporuje tyto zásady vyřazení: volatile lru, allkeys lru, náhodné volatile, allkeys náhodné, volatile ttl, noeviction. Výchozí zásada je volatile hodnoty nejdelšího nepoužití. Další informace najdete v tématu [konfigurace serveru výchozí Redis](cache-configure.md#default-redis-server-configuration). |
| Zásady vypršení platnosti |Výchozí zásady vypršení platnosti je absolutní a je výchozí interval vypršení platnosti je 10 minut. Klouzavé a nikdy zásady jsou také k dispozici. |Ve výchozím nastavení nevyprší položky v mezipaměti, ale dá se vypršení platnosti na základě zápisu za použití mezipaměti sady přetížení. |
| Oblasti a označování |Oblasti jsou podskupiny pro položky v mezipaměti. Oblasti také podporují poznámky položek v mezipaměti s další popisné řetězce názvem značky. Oblasti podpory schopnost provádět operace hledání na všech položek s příznakem v této oblasti. Všechny položky v rámci oblasti se nacházejí v rámci jednoho uzlu clusteru mezipaměti. |Mezipaměť Redis systému se skládá z jednoho uzlu (Pokud je povolená clusteru Redis), nelze použít koncept oblastech spravovaných Cache Service. Při načítání klíče popisné značky umožní vkládán názvy klíčů a používá se k načtení položky později redis podporuje vyhledávání a operace zástupný znak. Příklad implementace označování řešení pomocí Redis, naleznete v části [implementace mezipaměť s Redisem označování](http://stackify.com/implementing-cache-tagging-redis/). |
| Serializace |Managed Cache podporuje NetDataContractSerializer, BinaryFormatter a použití vlastních serializátorů. Výchozí hodnota je NetDataContractSerializer. |Je zodpovědností klientská aplikace k serializaci objektů .NET před uvedením do mezipaměti s výběru serializátoru až vývojář aplikace klienta. Další informace a ukázkový kód, najdete v části [práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulátor mezipaměti |Managed Cache poskytuje emulátoru místní mezipaměti. |Azure Redis Cache nemá emulátoru, ale můžete [místní spuštění MSOpenTech sestavení redis server.exe](cache-faq.md#cache-emulator) zajistit emulátoru prostředí. |

## <a name="choose-a-cache-offering"></a>Vyberte nabídku mezipaměti
Microsoft Azure Redis Cache je dostupná na následujících úrovních:

* **Basic** – jeden uzel. Více velikostí až do 53 GB.
* **Standard** – dva uzly Primární/Replika. Více velikostí až do 53 GB. 99,9% SLA.
* **Premium** – dva uzly Primární/Replika s až 10 horizontálními oddíly. Více velikostí od 6 GB do 530 GB. Všechny funkce úrovně Standard a navíc podpora [clusteru Redis](cache-how-to-premium-clustering.md), [trvalosti Redis](cache-how-to-premium-persistence.md) a [služby Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9% SLA.

Každá úroveň se liší z hlediska funkcí a cen. Funkce jsou popsané dál v této příručce a další informace o cenách najdete v tématu [podrobnosti o cenách mezipaměti](https://azure.microsoft.com/pricing/details/cache/).

Výchozí bod pro migraci je vyberte velikost, která odpovídá velikosti předchozí mezipaměti spravovanou službu mezipaměti a pak škálovat nahoru nebo dolů v závislosti na požadavcích vaší aplikace. Další informace o výběru správné nabídky Azure Redis Cache najdete v tématu [jaké mezipaměť Redis nabídky a velikosti použít](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Vytvoření mezipaměti
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurace klientů mezipaměti
Jakmile mezipaměti je vytvořen a nakonfigurován, dalším krokem je odebrat konfiguraci spravované služby mezipaměti a přidat konfigurace Azure Redis Cache a odkazuje tak, aby klienti mezipaměti přístup do mezipaměti.

* Odeberte konfiguraci Managed Cache Service
* Konfigurace klienta mezipaměti pomocí balíčku StackExchange.Redis NuGet

### <a name="remove-the-managed-cache-service-configuration"></a>Odeberte konfiguraci Managed Cache Service
Předtím, než je možné nakonfigurovat klientské aplikace pro Azure Redis Cache, existující konfigurace spravované služby mezipaměti a odkazy na sestavení musí být odebrány odinstalací balíčku NuGet pro spravované mezipaměti Service.

Pro odinstalaci balíčku spravované mezipaměti Service NuGet, klikněte pravým tlačítkem na projekt klienta v **Průzkumníku řešení** a zvolte **spravovat balíčky NuGet**. Vyberte **nainstalované balíky** uzel a typ W**indowsAzure.Caching** do vyhledávacího pole nainstalované balíčky. Vyberte **Windows** **Azure Cache** (nebo **Windows** **ukládání do mezipaměti Azure** v závislosti na verzi balíčku NuGet), klikněte na tlačítko **Odinstalace**a potom klikněte na **Zavřít**.

![Odinstalace balíčku NuGet Azure Managed Cache Service](./media/cache-migrate-to-redis/IC757666.jpg)

Odinstalace balíčku NuGet pro spravované mezipaměti Service odebere sestavení spravované služby mezipaměti a položky spravované služby mezipaměti v souboru app.config nebo web.config klientské aplikace. Protože některé vlastní nastavení nelze odebrat, při odinstalaci balíčku NuGet, otevřete soubor web.config nebo app.config a ujistěte se, že tyto prvky jsou odebrány.

Ujistěte se, že `dataCacheClients` položka je odebrána z `configSections` elementu. Neodebírejte celý `configSections` element; odebrat jenom `dataCacheClients` položku, pokud je k dispozici.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Ujistěte se, že `dataCacheClients` oddíl je odebrat. `dataCacheClients` Části bude vypadat podobně jako v následujícím příkladu.

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

Odebraný konfiguraci spravované služby mezipaměti můžete nakonfigurovat klienta mezipaměti, jak je popsáno v následující části.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurace klienta mezipaměti pomocí balíčku StackExchange.Redis NuGet
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrace kódu spravované služby mezipaměti
Rozhraní API pro klienta mezipaměti StackExchange.Redis se podobá mezipaměti služby spravovat. Tato část obsahuje přehled rozdíly.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Připojení k mezipaměti pomocí ConnectionMultiplexer – třída
Ve spravované službě mezipaměti byly připojení k mezipaměti zpracována `DataCacheFactory` a `DataCache` třídy. Ve službě Azure Redis Cache spravuje tato připojení `ConnectionMultiplexer` třídy.

Přidejte následující pomocí příkazu na začátek souboru, ve kterém chcete přístup do mezipaměti.

```csharp
using StackExchange.Redis
```

Pokud se tento obor názvů se nevyřeší, ujistěte se, že jste přidali balíčku StackExchange.Redis NuGet jak je popsáno v [rychlý start: použití Azure Redis Cache s aplikací .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Všimněte si, že klient StackExchange.Redis vyžaduje rozhraní .NET Framework 4 nebo vyšší.
> 
> 

Pokud chcete připojit k instanci služby Azure Redis Cache, zavolejte statickou `ConnectionMultiplexer.Connect` metoda a předejte jí koncový bod a klíč. Jeden ze způsobů sdílení instance `ConnectionMultiplexer` v aplikaci je pomocí statické vlastnosti, která vrací připojenou instanci, podobně jako v následujícím příkladu.  Tento přístup poskytuje způsob inicializace jedné připojené vláken `ConnectionMultiplexer` instance. V tomto příkladu `abortConnect` nastaven na hodnotu false, to znamená, že volání bude úspěšné i v případě, že nedojde k vytvoření připojení k mezipaměti. Klíčovou vlastností `ConnectionMultiplexer` je automatické obnovení připojení k mezipaměti po vyřešení problémů se sítí nebo jiných příčin.

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

Koncový bod mezipaměti, klíče a portů můžete získat **Redis Cache** okně instance mezipaměti. Další informace najdete v tématu [Redis Cache vlastnosti](cache-configure.md#properties).

Po vytvoření připojení vrátíte odkaz na databázi mezipaměti Redis zavoláním `ConnectionMultiplexer.GetDatabase` metoda. Objekt vrácený metodou `GetDatabase` je prostý průchozí objekt a není nutné jej ukládat.

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

Klient StackExchange.Redis používá `RedisKey` a `RedisValue` typy pro přístup k informacím a ukládání položek do mezipaměti. Tyto typy namapovat na nejvíce primitivní typy jazyka, včetně řetězec a často nepoužívají se přímo. Redis řetězce jsou nejzákladnější druh hodnotu Redis a může obsahovat mnoho typů dat, včetně serializovaných binární datové proudy, a při typ nemusí používat přímo, budete používat metody, které obsahují `String` v názvu. Pro většinu primitivní datové typy, ukládat a načítat položky z mezipaměti pomocí `StringSet` a `StringGet` metody, pokud jsou kolekce nebo jiné datové typy Redis ukládání do mezipaměti. 

`StringSet` a `StringGet` jsou podobná mezipaměti služba spravovaných `Put` a `Get` metody s jedním hlavní rozdíl, že před nastavování a získávání objekt .NET do mezipaměti musí serializovat nejdřív. 

Při volání metody `StringGet`, pokud objekt existuje, bude vrácen, a pokud ne, vrátí se hodnota null. V takovém případě můžete načíst hodnotu z požadovaného zdroje dat a uložit ho do mezipaměti pro pozdější použití. Tento vzor se označuje jako doplňováním mezipaměti.

Chcete-li zadat vypršení platnosti položky v mezipaměti, použijte parametr `TimeSpan` metody `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Redis Cache může pracovat s objekty .NET, jakož i primitivní datové typy, ale před objekt .NET můžete uložit do mezipaměti, musí být serializovány. Serializace má na starosti vývojář aplikace a poskytuje flexibilitu vývojáře při výběru serializátoru. Další informace a ukázkový kód, najdete v části [práce s objekty .NET v mezipaměti](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migrace stavu relace ASP.NET a ukládání výstupu do mezipaměti pro Azure Redis Cache
Azure Redis Cache má zprostředkovatele pro stavu relace ASP.NET a stránky ukládání výstupu do mezipaměti. K migraci aplikace, který používá služba spravovaných mezipaměti verze těchto poskytovatelů, nejprve odeberte stávající části ze souboru web.config a pak nakonfigurujte Azure Redis Cache verze zprostředkovatele. Pokyny k používání Azure Redis Cache ASP.NET zprostředkovatele najdete v tématu [poskytovatele stavu relace ASP.NET pro Azure Redis Cache](cache-aspnet-session-state-provider.md) a [poskytovatel výstupní mezipaměti technologie ASP.NET pro Azure Redis Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Další postup
Prozkoumejte [dokumentace k Azure Redis Cache](https://azure.microsoft.com/documentation/services/cache/) pro kurzy, ukázky, videa a další.

