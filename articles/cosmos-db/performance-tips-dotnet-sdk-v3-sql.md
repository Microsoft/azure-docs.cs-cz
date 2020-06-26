---
title: Tipy pro výkon Azure Cosmos DB pro .NET SDK V3
description: Seznamte se s možnostmi konfigurace klienta, abyste vylepšili Azure Cosmos DB výkon sady .NET V3 SDK.
author: j82w
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: jawilley
ms.openlocfilehash: ce0bc73fd21210e7cd5cd48c8134abd5f014b026
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392416"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Tipy pro zvýšení výkonu pro Azure Cosmos DB a .NET

> [!div class="op_single_selector"]
> * [.NET SDK V3](performance-tips-dotnet-sdk-v3-sql.md)
> * [Sada .NET SDK v2](performance-tips.md)
> * [Sada Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Sada Async Java SDK v2](performance-tips-async-java.md)
> * [Sada Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která bez problémů škáluje zaručenou latenci a propustnost. Nemusíte dělat zásadní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Vertikální navýšení a snížení kapacity je stejně snadné jako při provádění jediného volání rozhraní API. Další informace najdete v tématu [jak zřídit propustnost kontejneru](how-to-provision-container-throughput.md) nebo [jak zřídit propustnost databáze](how-to-provision-database-throughput.md). Vzhledem k tomu, že k Azure Cosmos DB je k dispozici prostřednictvím síťových volání, existují optimalizace na straně klienta, které vám při použití [sady SQL .NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3)umožní dosáhnout špičkového výkonu.

Pokud se tedy snažíte zvýšit výkon databáze, zvažte tyto možnosti:

## <a name="hosting-recommendations"></a>Doporučení hostování

**Pro úlohy náročné na dotazy použijte Windows 64, nikoli Linux nebo Windows 32-bit Processing Host.**

Pro zlepšení výkonu doporučujeme zpracování bitového hostitelského systému Windows 64. Sada SQL SDK obsahuje nativní ServiceInterop.dll k analýze a optimalizaci dotazů v místním prostředí. ServiceInterop.dll se podporuje jenom na platformě Windows x64. Pro Linux a jiné nepodporované platformy, kde ServiceInterop.dll není k dispozici, je bráně k získání optimalizovaného dotazu učiněno další síťové volání. Následující typy aplikací používají ve výchozím nastavení 32 hostitelského zpracování. Chcete-li změnit zpracování hostitele na 64, postupujte podle těchto kroků na základě typu vaší aplikace:

- U spustitelných aplikací lze změnit zpracování hostitele nastavením možnosti [cíl platformy](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) na hodnotu **x64** v okně **Vlastnosti projektu** na kartě **sestavení** .

- U testovacích projektů založených na VSTest můžete změnit zpracování hostitele tak, **Test**že  >  v nabídce Test sady Visual Studio vyberete**možnost nastavení test testu**  >  **výchozí architektura procesoru jako x64** . **Test**

- Pro lokálně nasazené webové aplikace v ASP.NET můžete změnit zpracování hostitele tak, že vyberete možnost **použít 64 verze IIS Express pro weby a projekty** v nabídce **nástroje**  >  **Možnosti**  >  **projekty a řešení**  >  **webové projekty**.

- Pro webové aplikace ASP.NET nasazené v Azure můžete změnit zpracování hostitele tak, že v **nastavení aplikace** v Azure Portal vyberete platformu **64** .

> [!NOTE] 
> Ve výchozím nastavení jsou nové projekty sady Visual Studio nastaveny na **Libovolný procesor**. Doporučujeme, abyste projekt nastavili na **x64** , takže se nepřepne na **x86**. Projekt nastavený na **Libovolný procesor** může snadno přepnout na **x86** , pokud se přidá závislost jenom pro procesory x86.<br/>
> ServiceInterop.dll musí být ve složce, ze které je spuštěna knihovna DLL sady SDK. To by mělo být obavy jenom v případě, že knihovny DLL ručně kopírujete nebo máte vlastní systémy sestavení/nasazení.
    
**Zapnout uvolňování paměti na straně serveru (GC)**

Omezení frekvence uvolňování paměti může v některých případech pomáhat. V rozhraní .NET nastavte [gcServer](https://docs.microsoft.com/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) na `true` .

**Horizontální navýšení kapacity úlohy klienta**

Pokud testujete na úrovních vysoké propustnosti (více než 50 000 RU/s), může se klientská aplikace stát kritickým bodem, protože počítač capping na využití procesoru nebo sítě. Pokud se dostanou k tomuto bodu, můžete dál nasdílet Azure Cosmos DB účet tím, že navedete horizontální navýšení kapacity klientských aplikací napříč více servery.

> [!NOTE] 
> Vysoké využití procesoru může způsobit zvýšení latence a vypršení časového limitu požadavku.

## <a name="networking"></a>Sítě
<a id="direct-connection"></a>

**Zásady připojení: použít přímý režim připojení**

Způsob připojení klienta k Azure Cosmos DB má důležité dopady na výkon, zejména u pozorované latence na straně klienta. K dispozici jsou dvě nastavení konfigurace klíče pro konfiguraci zásad připojení klienta: *režim* připojení a *protokol*připojení.  K dispozici jsou dva režimy:

   * Přímý režim (výchozí)

     Přímý režim podporuje připojení prostřednictvím protokolu TCP a je výchozím režimem připojení, pokud používáte [sadu Microsoft. Azure. Cosmos/. NET V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3). To nabízí lepší výkon a vyžaduje méně směrování sítě než režim brány.

   * Režim brány
      
     Pokud vaše aplikace běží v podnikové síti s přísnými omezeními brány firewall, je nejlepší volbou režim brány, protože používá standardní port HTTPS a jeden koncový bod. Kompromisy týkající se výkonu však jsou v tom, že režim brány zahrnuje dodatečné směrování sítě pokaždé, když se data čtou nebo se zapisují do Azure Cosmos DB. Přímý režim proto nabízí lepší výkon, protože je k dispozici méně síťových segmentů. Režim připojení brány doporučujeme také v případě, že spouštíte aplikace v prostředích, které mají omezený počet připojení soketu.

     Při použití sady SDK v Azure Functions, zejména v [plánu spotřeby](../azure-functions/functions-scale.md#consumption-plan), si pamatujte na aktuální [omezení připojení](../azure-functions/manage-connections.md). V takovém případě může být režim brány lepší, pokud také pracujete s jinými klienty na bázi protokolu HTTP v rámci vaší aplikace Azure Functions.


V režimu brány Azure Cosmos DB pomocí rozhraní Azure Cosmos DB API pro MongoDB používat port 443 a porty 10250, 10255 a 10256. Port 10250 se mapuje na výchozí instanci MongoDB bez geografické replikace. Porty 10255 a 10256 jsou mapovány na instanci MongoDB, která má geografickou replikaci.
     
Pokud používáte protokol TCP v přímém režimu kromě portů brány, je nutné zajistit, aby byl rozsah portů mezi 10000 a 20000 otevřený, protože Azure Cosmos DB používá dynamické porty TCP (při použití přímého režimu na [soukromých koncových bodech](./how-to-configure-private-endpoints.md)musí být otevřen plný rozsah portů TCP-od 0 do 65535-). Ve výchozím nastavení jsou porty otevřené pro standardní konfiguraci virtuálních počítačů Azure. Pokud se tyto porty neotevřou a pokusíte se použít protokol TCP, obdržíte nedostupnou chybu služby 503. Tato tabulka zobrazuje režimy připojení dostupné pro různá rozhraní API a porty služeb používané pro každé rozhraní API:

|Režim připojení  |Podporovaný protokol  |Podporované sady SDK  |Port API/Service  |
|---------|---------|---------|---------|
|brána  |   HTTPS    |  Všechny sady SDK    |   SQL (443), MongoDB (10250, 10255, 10256), Table (443), Cassandra (10350), Graph (443)    |
|Direct    |     TCP    |  .NET SDK    | Při použití koncových bodů veřejné/služby: porty v rozsahu 10000 až 20000<br>Při použití privátních koncových bodů: porty v rozsahu 0 až 65535 |

Azure Cosmos DB nabízí jednoduchý a otevřený programovací model RESTful přes protokol HTTPS. Navíc nabízí efektivní protokol TCP, který se také RESTful ve svém komunikačním modelu a je dostupný prostřednictvím klientské sady SDK pro .NET. Protokol TCP používá pro počáteční ověřování a šifrování provozu protokol TLS. Pro nejlepší výkon použijte protokol TCP, pokud je to možné.

Pro sadu SDK V3 nakonfigurujete režim připojení při vytváření `CosmosClient` instance v `CosmosClientOptions` . Pamatujte, že výchozí hodnota je přímý režim.

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Protože je protokol TCP podporován pouze v přímém režimu, pokud používáte režim brány, protokol HTTPS se vždy používá ke komunikaci s bránou.

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Zásady připojení Azure Cosmos DB" border="false":::

**Vyčerpání dočasných portů**

Pokud se na instancích zobrazí velký objem připojení nebo vysoké využití portů, ověřte nejprve, zda jsou klientské instance typu singleton. Jinými slovy, instance klientů by měly být pro celou dobu života aplikace jedinečné.

Při spuštění v protokolu TCP se klient optimalizuje kvůli latenci pomocí dlouhotrvajících připojení na rozdíl od protokolu HTTPS, který ukončí připojení po 2 minutách nečinnosti.

Ve scénářích, kde máte zhuštěný přístup a pokud si všimnete vyššího počtu připojení v porovnání s přístupem k režimu brány, můžete:

* Nakonfigurujte vlastnost [CosmosClientOptions. PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) na `PrivatePortPool` (platí pro rozhraní Framework Version>= 4.6.1 a .net Core verze >= 2,0): Tato vlastnost umožňuje, aby sada SDK používala malý fond dočasných portů pro různé Azure Cosmos DB cílové koncové body.
* Nakonfigurujte vlastnost [CosmosClientOptions. IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) musí být větší než nebo rovna 10 minutám. Doporučené hodnoty jsou mezi 20 minutami a 24 hodinami.

<a id="same-region"></a>

**Pro výkon společné umístění klienty ve stejné oblasti Azure**

Pokud je to možné, umístěte všechny aplikace, které volají Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos DB. Toto je přibližné porovnání: volání Azure Cosmos DB v rámci stejné oblasti se dokončila v rozmezí od 1 do 2 MS, ale latence mezi západním a východním pobřežím USA je větší než 50 ms. Tato latence se může lišit od požadavku na vyžádání v závislosti na trasách, kterou požadavek prochází z klienta na hranici datacentra Azure. Nejnižší možnou latenci získáte tak, že zajistíte, aby se volající aplikace nacházela ve stejné oblasti Azure jako koncový bod zřízené Azure Cosmos DB. Seznam oblastí, které jsou k dispozici, najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Zásady připojení Azure Cosmos DB" border="false":::

   <a id="increase-threads"></a>

**Zvýšení počtu vláken/úloh**

Vzhledem k tomu, že volání Azure Cosmos DB jsou provedena přes síť, možná budete muset změnit stupeň souběžnosti požadavků, aby klientská aplikace strávila minimální dobu čekání mezi požadavky. Pokud například používáte [úlohu .NET Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx), vytvořte si pořadí stovky úloh, které čtou nebo zapisují do Azure Cosmos DB.

**Povolit akcelerované síťové služby**
 
 Pro snížení latence a kolísání procesoru doporučujeme povolit akcelerované síťové služby na virtuálních počítačích klienta. Přečtěte si článek [Vytvoření virtuálního počítače s Windows s akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-powershell.md) službami nebo [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-cli.md)službami.

## <a name="sdk-usage"></a>Využití sady SDK
**Nainstalovat nejnovější sadu SDK**

Sady Azure Cosmos DB SDK se neustále zdokonalují, aby poskytovaly nejlepší výkon. Pokud chcete zjistit nejnovější sadu SDK a zkontrolovat vylepšení, podívejte se na stránky [Azure Cosmos DB SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) .

**Použití rozhraní API pro streamování**

[Sada .NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) obsahuje rozhraní API pro Stream, která mohou přijímat a vracet data bez serializace. 

Aplikace střední vrstvy, které nevyužívají odpovědi přímo ze sady SDK, ale jejich přenos na jiné aplikační vrstvy můžou těžit z rozhraní API streamu. Příklady manipulace s datovým proudem najdete v ukázkách [správy položek](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) .

**Použití typu Singleton Azure Cosmos DB klienta po dobu života vaší aplikace**

Každá `CosmosClient` instance je bezpečná pro přístup z více vláken a při provozu v přímém režimu provádí efektivní správu připojení a ukládání adres do mezipaměti. K zajištění efektivní správy připojení a lepšího výkonu klienta sady SDK doporučujeme, abyste `AppDomain` pro celou dobu života aplikace použili jednu instanci.

Při práci na Azure Functions by instance měly také postupovat podle stávajících [pokynů](../azure-functions/manage-connections.md#static-clients) a udržovat jednu instanci.

<a id="max-connection"></a>

**Zakažte odpověď obsahu při operacích zápisu.**

Pro úlohy, které mají některá vytvořit datovou část, nastavte možnost žádosti EnableContentResponseOnWrite na hodnotu false (NEPRAVDA). Služba již nebude vracet vytvořený nebo aktualizovaný prostředek k sadě SDK. Normálně má aplikace vytvořen objekt, takže nepotřebuje službu, aby ji vrátil. Hodnoty hlaviček jsou stále přístupné, jako poplatek za požadavek. To může zvýšit výkon, protože sada SDK již nebude muset přidělovat paměť nebo serializovat tělo odpovědi. Tím se také snižuje využití šířky pásma sítě, aby bylo možné lépe zvýšit výkon.  

```csharp
ItemRequestOption requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**Povolit hromadnou optimalizaci pro zajištění propustnosti místo latence** Pro scénáře, které vyžadují velké množství propustnosti, můžete povolit hromadnou zátěž a latence není tak důležitá. Další informace o tom, jak tuto funkci povolit a jaké scénáře by se měly používat, najdete v [úvodu k hromadnému](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk) sestavování.

**Při použití režimu brány zvýšit System.Net MaxConnections na hostitele**

Při použití režimu brány se požadavky na Azure Cosmos DB zavedou přes HTTPS/REST. Vztahují se na výchozí limit počtu připojení na název hostitele nebo IP adresu. Možná budete muset nastavit `MaxConnections` na vyšší hodnotu (100 až 1 000), aby Klientská knihovna mohla použít více souběžných připojení k Azure Cosmos DB. V sadě .NET SDK 1.8.0 a novější je výchozí hodnota pro [Třída ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50. Chcete-li změnit hodnotu, můžete nastavit [Documents. Client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) na vyšší hodnotu.

**Ladění paralelních dotazů pro dělené kolekce**

Sada SQL .NET SDK podporuje paralelní dotazy, které umožňují paralelní dotazování kontejneru rozděleného na oddíly. Další informace najdete v tématu [ukázky kódu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) týkající se práce se sadami SDK. Paralelní dotazy jsou navržené tak, aby poskytovaly lepší latenci a propustnost dotazů než jejich sériové protějšky. Paralelní dotazy poskytují dva parametry, které můžete ladit podle svých požadavků: 
- `MaxConcurrency`Určuje maximální počet oddílů, které lze dotazovat paralelně. 
- `MaxBufferedItemCount`Určuje počet předběžně načtených výsledků.

***Stupeň optimalizace souběžnosti***

Paralelní dotaz funguje paralelně dotazování na více oddílů. Data z jednotlivého oddílu se ale v souvislosti s dotazem načítají sériově. Nastavení `MaxConcurrency` v [sadě SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) na počet oddílů má největší šanci na dosažení nejvíce výkonného dotazu, za předpokladu, že všechny ostatní systémové podmínky zůstávají stejné. Pokud neznáte počet oddílů, můžete nastavit úroveň paralelismu na vysoké číslo. V systému se jako stupeň paralelismu zvolí minimální počet oddílů, uživatelem zadaný vstup.

Paralelní dotazy poskytují nejvíc výhod, pokud jsou data rovnoměrně rozložena napříč všemi oddíly s ohledem na dotaz. Pokud je dělená kolekce rozdělená tak, aby všechna nebo většinu dat vrácených dotazem byla soustředěna v několika oddílech (jeden z nich je v nejhorším případě), tyto oddíly budou mít kritický vliv na výkon dotazu.

***Vyladění MaxBufferedItemCount***
    
Paralelní dotaz je navržený tak, aby byly výsledky předem načteny, zatímco aktuální dávka výsledků je zpracovávána klientem. Toto předběžné načítání pomáhá zlepšit celkovou latenci dotazu. `MaxBufferedItemCount`Parametr omezuje počet předběžně načtených výsledků. Nastavte `MaxBufferedItemCount` na očekávaný počet vrácených výsledků (nebo vyšší číslo), aby dotaz mohl získat maximální přínos před načtením.

Předběžné načítání funguje stejným způsobem bez ohledu na stupeň paralelismu a existuje jedna vyrovnávací paměť pro data ze všech oddílů.  

**Implementace omezení rychlosti v intervalech RetryAfter**

Během testování výkonu byste měli zvýšit zatížení, dokud se neomezí malá míra požadavků. Pokud jsou požadavky omezené, klientská aplikace by se měla v případě intervalu opakování zadaného serverem vypnout na omezovači. Respektování omezení rychlosti zajišťuje minimální dobu čekání mezi opakovanými pokusy. 

Další informace najdete v tématu [RetryAfter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?view=azure-dotnet#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
Existuje mechanismus pro protokolování dalších diagnostických informací a řešení potíží s latencí, jak je znázorněno v následující ukázce. Můžete protokolovat diagnostický řetězec pro požadavky, které mají vyšší latenci čtení. Zachycený diagnostický řetězec vám pomůže pochopit, kolikrát jste v dané žádosti obdrželi chyby 429.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**Zvýšení počtu vláken/úloh**

Podívejte [se na téma zvýšení počtu vláken/úloh](#increase-threads) v části sítě v tomto článku.

## <a name="indexing-policy"></a>Zásady indexování
 
**Vyloučit nepoužívané cesty z indexování pro rychlejší zápis**

Zásada indexování Azure Cosmos DB také umožňuje určit, které cesty dokumentů zahrnout nebo vyloučit z indexování pomocí cest indexování (IndexingPolicy. IncludedPaths a IndexingPolicy. ExcludedPaths). Indexování pouze těch cest, které potřebujete, může vylepšit výkon zápisu, snížit náklady na zápis a snížit kapacitu úložiště pro scénáře, ve kterých jsou vzorce dotazů známy předem. Důvodem je to, že se náklady na indexování nevztahují přímo na počet indexovaných jedinečných cest. Tento kód například ukazuje, jak vyloučit celý oddíl dokumentů (podstrom) z indexování pomocí zástupného znaku "*":

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

Další informace najdete v tématu [Azure Cosmos DB zásady indexování](index-policy.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

**Měření a optimalizace pro nižší jednotky žádostí za sekundu použití**

Azure Cosmos DB nabízí bohatou sadu databázových operací. Tyto operace zahrnují relační a hierarchické dotazy s UDF, uloženými procedurami a triggery, a to vše v dokumentech v rámci kolekce databáze. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, vstupně-výstupních operacích a paměti potřebných k dokončení operace. Místo toho, abyste si vymysleli a spravovali hardwarové prostředky, si můžete představit jednotku žádosti (RU) jako jednu míru prostředků potřebných k provádění různých databázových operací a zpracování požadavků aplikace.

Propustnost se zřizuje na základě počtu [jednotek žádostí](request-units.md) nastavených pro každý kontejner. Spotřeba jednotky požadavku se vyhodnotí jako sazba za sekundu. Aplikace, které překračují sazbu jednotky zřízené žádosti pro svůj kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň kontejneru. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit svou propustnost tím, že zřizujete další jednotky žádostí.

Složitost dotazu ovlivňuje počet spotřebovaných jednotek požadavků pro určitou operaci. Počet predikátů, povaha predikátů, počet UDF a velikost zdrojové datové sady ovlivňují náklady na operace dotazů.

Pokud chcete změřit režii jakékoli operace (vytvořit, aktualizovat nebo odstranit), zkontrolujte záhlaví [x-MS-Request-poplatek](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (nebo ekvivalentní `RequestCharge` vlastnost v `ResourceResponse\<T>` `FeedResponse\<T>` sadě SDK sady .NET) a změřte tak počet jednotek žádostí spotřebovaných operacemi:

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Poplatek za požadavek vrácený v této hlavičce je zlomek zřízené propustnosti (tj. 2 000 ru za sekundu). Pokud například předchozí dotaz vrátí dokumenty 1 000 1 – KB, cena za operaci je 1 000. Takže během jedné sekundy Server respektuje jenom dva takové požadavky, než tarif omezí pozdější požadavky. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek požadavků](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Omezení rychlosti zpracování/počet požadavků je moc velký.**

Když se klient pokusí překročit rezervovanou propustnost účtu, neexistují žádné snížení výkonu na serveru a žádné využití kapacity propustnosti mimo rezervovanou úroveň. Server bude žádost bez jakýchkoli požadavků RequestRateTooLarge (kód stavu HTTP 429). Vrátí záhlaví [x-MS-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) , které indikuje množství času (v milisekundách), po který uživatel musí čekat, než bude požadavek znovu proveden.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Sady SDK mají implicitně zachytit tuto odpověď, a to v souladu s hlavičkou, která je určená serverem, a zkuste žádost zopakovat. Pokud k účtu nepoužíváte souběžně více klientů, další pokus bude úspěšný.

Pokud máte více než jednoho klienta, který se konzistentně pracuje konzistentně nad sazbou požadavku, nemusí stačit výchozí počet opakování nastavený na 9 interně klientem. V takovém případě klient vyvolá CosmosException se stavovým kódem 429 do aplikace. 

Výchozí počet opakování můžete změnit nastavením `RetryOptions` v `CosmosClientOptions` instanci. Ve výchozím nastavení se CosmosException se stavovým kódem 429 vrátí po kumulativní čekací době 30 sekund, pokud požadavek nadále funguje nad sazbou požadavku. Tato chyba se vrátí i v případě, že aktuální počet opakování je menší než maximální počet opakování, zda je aktuální hodnota výchozí hodnotou 9 nebo uživatelem definovanou hodnotou.

Automatizované chování při opakování pomáhá zlepšit odolnost a použitelnost většiny aplikací. To ale nemusí být nejlepší chování při provádění srovnávacích testů výkonu, zejména při měření latence. Latence zjištěná klientem bude špička, pokud experiment narazí na omezení serveru a způsobí, že se klientská sada SDK tiše znovu pokusí. Aby se zabránilo špičkám latence během experimentů s výkonem, změřte poplatky vracené jednotlivými operacemi a zajistěte, aby požadavky byly v provozu pod rezervovanými sazbami požadavků. Další informace najdete v tématu [jednotky žádostí](request-units.md).

**Pro vyšší propustnost, návrh pro menší dokumenty**

Poplatek za požadavek (tj. náklady na zpracování žádosti) dané operace koreluje přímo s velikostí dokumentu. Operace s velkým objemem dokumentů se dotýkají více než operací na malých dokumentech.

## <a name="next-steps"></a>Další kroky
Ukázkovou aplikaci, která se používá k vyhodnocení Azure Cosmos DB pro scénáře s vysokým výkonem na několika klientských počítačích, najdete v tématu [testování výkonu a škálování pomocí Azure Cosmos DB](performance-testing.md).

Další informace o návrhu aplikace pro škálování a vysoký výkon najdete v tématu [dělení a škálování v Azure Cosmos DB](partition-data.md).
