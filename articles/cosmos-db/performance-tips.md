---
title: Tipy pro výkon Azure Cosmos DB pro rozhraní .NET
description: Naučte se možnosti konfigurace klienta pro zlepšení výkonu Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: ee2743af2f8499aec04d8b6b733e1ba4c2a82083
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546077"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Tipy pro zvýšení výkonu pro Azure Cosmos DB a .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která se bezproblémově škáluje s garantovanou latencí a propustností. Není třeba provádět hlavní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Škálování nahoru a dolů je stejně snadné jako volání jednoho rozhraní API. Další informace naleznete v tématu jak [zřídit propustnost kontejneru](how-to-provision-container-throughput.md) nebo [jak zřídit propustnost databáze](how-to-provision-database-throughput.md). Ale protože Azure Cosmos DB je přístupný prostřednictvím síťových volání, existují optimalizace na straně klienta můžete provést k dosažení špičkového výkonu při použití [sql .NET SDK](sql-api-sdk-dotnet-standard.md).

Pokud se tedy pokoušíte zlepšit výkon databáze, zvažte tyto možnosti:

## <a name="hosting-recommendations"></a>Hosting doporučení

**Pro úlohy náročné na dotazy použijte windows 64bitové místo linuxového nebo 32bitového zpracování hostitele**

Pro lepší výkon doporučujeme 64bitové zpracování hostitelů v systému Windows. Sql SDK obsahuje nativní ServiceInterop.dll analyzovat a optimalizovat dotazy místně. Služba ServiceInterop.dll je podporována pouze na platformě Windows x64. Pro Linux a další nepodporované platformy, kde ServiceInterop.dll není k dispozici, další síťové volání do brány získat optimalizovaný dotaz. Následující typy aplikací používají ve výchozím nastavení 32bitové zpracování hostitelů. Chcete-li změnit zpracování hostitele na 64bitové zpracování, postupujte takto na základě typu aplikace:

- U spustitelných aplikací můžete změnit zpracování hostitele nastavením [cíle platformy](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) na **x64** v okně **Vlastnosti projektu** na kartě **Sestavení.**

- U testovacích projektů založených na VSTest můžete **Test** > změnit zpracování hostitele výběrem výchozí architektury procesoru**test nastavení** > **testu jako X64** v nabídce **Test** sady Visual Studio.

- U místně nasazených ASP.NET webových aplikací můžete změnit zpracování hostitele výběrem**Options** >  **možnosti Použít 64bitovou verzi služby IIS Express pro webové servery a projekty** v části **Projekty možností nástrojů** > a**webové projekty****řešení** > .

- Pro ASP.NET webových aplikací nasazených v Azure můžete změnit zpracování hostitele výběrem **64bitové** platformy v **nastavení aplikací** na webu Azure Portal.

> [!NOTE] 
> Ve výchozím nastavení jsou nové projekty sady Visual Studio nastaveny na **libovolný procesor**. Doporučujeme nastavit projekt na **x64,** aby se nepřepnul na **x86**. Projekt nastavený na **libovolný procesor** lze snadno přepnout na **x86,** pokud je přidána pouze x86 závislost.<br/>
> ServiceInterop.dll musí být ve složce, ze které je spouštěna dll sady SDK. To by mělo být problém pouze v případě, že ručně kopírovat knihovny DLL nebo vlastní sestavení nebo nasazení systémů.
    
**Zapnutí uvolňování paměti na straně serveru (GC)**

Snížení četnosti uvolňování paměti může pomoci v některých případech. V rozhraní .NET nastavte `true` [server GCServer](https://msdn.microsoft.com/library/ms229357.aspx) na .

**Horizontální navýšení kapacity úloh klienta**

Pokud testujete na vysokých úrovních propustnost (více než 50 000 RU/s), může se klientská aplikace stát kritickým bodem z důvodu omezení procesoru nebo využití sítě počítače. Pokud dosáhnete tohoto bodu, můžete pokračovat v dalším nabízení účtu Azure Cosmos DB horizontálním navýšením kapacity klientských aplikací na více serverech.

> [!NOTE] 
> Vysoké využití procesoru může způsobit zvýšenou latenci a výjimky časového limitu požadavku.

## <a name="networking"></a>Sítě
<a id="direct-connection"></a>

**Zásady připojení: Použití režimu přímého připojení**

Jak se klient připojí k Azure Cosmos DB má důležité důsledky pro výkon, zejména pro pozorované latence na straně klienta. Pro konfiguraci zásad připojení klienta jsou k dispozici dvě nastavení konfigurace klíčů: *režim* připojení a *protokol*připojení .  Dva dostupné režimy jsou:

   * Režim brány
      
     Režim brány je podporován na všech platformách sady SDK a je nakonfigurovaným výchozím nastavením sady [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md). Pokud vaše aplikace běží v rámci podnikové sítě s přísnými omezeními brány, režim brány je nejlepší volbou, protože používá standardní port HTTPS a jeden koncový bod. Kompromis výkonu je však, že režim brány zahrnuje další síťový směrování pokaždé, když se data číst nebo zapisovat do Azure Cosmos DB. Přímý režim tedy nabízí lepší výkon, protože je méně síťových směrování. Doporučujeme také režim připojení brány při spuštění aplikací v prostředích, které mají omezený počet připojení soketu.

     Při použití sady SDK ve funkcích Azure, zejména v [plánu spotřeba](../azure-functions/functions-scale.md#consumption-plan), uvědomte si aktuální [limity pro připojení](../azure-functions/manage-connections.md). V takovém případě může být lepší režim brány, pokud také pracujete s jinými klienty založenými na protokolu HTTP v rámci aplikace Azure Functions.

   * Přímý režim

     Přímý režim podporuje připojení prostřednictvím protokolu TCP a je výchozím režimem připojení, pokud používáte [sadu Microsoft.Azure.Cosmos/.NET V3 SDK](sql-api-sdk-dotnet-standard.md).

V režimu brány Azure Cosmos DB používá port 443 a porty 10250, 10255 a 10256, když používáte rozhraní API Azure Cosmos DB pro MongoDB. Port 10250 se mapuje na výchozí instanci MongoDB bez geografické replikace. Porty 10255 a 10256 mapovat na instanci MongoDB, která má geografickou replikaci.
     
Při použití protokolu TCP v přímém režimu, kromě portů brány, je třeba zajistit rozsah portů mezi 10000 a 20000 je otevřená, protože Azure Cosmos DB používá dynamické porty TCP. Pokud tyto porty nejsou otevřené a pokusíte se použít protokol TCP, zobrazí se chyba nedostupné služby 503. Tato tabulka zobrazuje režimy připojení, které jsou k dispozici pro různá rozhraní API a porty služeb používané pro každé rozhraní API:

|Režim připojení  |Podporovaný protokol  |Podporované sady SDK  |Port ROZHRANÍ API/SlužbY  |
|---------|---------|---------|---------|
|brána  |   HTTPS    |  Všechny sady SDK    |   SQL (443), MongoDB (10250, 10255, 10256), Tabulka (443), Cassandra (10350), Graf (443)    |
|Direct    |     TCP    |  .NET SDK    | Přístavy v rozsahu 10000 až 20000 |

Azure Cosmos DB nabízí jednoduchý, otevřený programovací model RESTful přes protokol HTTPS. Kromě toho nabízí efektivní protokol TCP, který je také RESTful ve svém komunikačním modelu a je k dispozici prostřednictvím sady .NET client SDK. Protokol TCP používá protokol TLS pro počáteční ověřování a šifrování přenosů. Pro dosažení nejlepšího výkonu použijte protokol TCP, pokud je to možné.

Pro sdk V3 nakonfigurujete `CosmosClient` režim `CosmosClientOptions`připojení při vytváření instance v aplikaci . Nezapomeňte, že přímý režim je výchozí.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Pro sadu Microsoft.Azure.DocumentDB SDK nakonfigurujete `DocumentClient` režim připojení `ConnectionPolicy` během konstrukce instance pomocí parametru. Pokud používáte přímý režim, můžete `Protocol` také `ConnectionPolicy` nastavit pomocí parametru.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Vzhledem k tomu, že protokol TCP je podporován pouze v přímém režimu, `Protocol` pokud `ConnectionPolicy` používáte režim brány, protokol HTTPS se vždy používá ke komunikaci s bránou a hodnota v je ignorována.

![Zásady připojení Azure Cosmos DB](./media/performance-tips/connection-policy.png)

**Volání OpenAsync, aby se zabránilo latenci spuštění na první žádost**

Ve výchozím nastavení má první požadavek vyšší latenci, protože potřebuje načíst směrovací tabulku adres. Při použití [sady SDK V2](sql-api-sdk-dotnet.md), volání `OpenAsync()` jednou během inicializace, aby se zabránilo této latence při spuštění na první požadavek:

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`vygeneruje požadavky na získání směrovací tabulky adres pro všechny kontejnery v účtu. Pro účty, které mají mnoho kontejnerů, ale `OpenAsync` jejichž aplikace přistupuje k podmnožině z nich, by generovat zbytečné množství provozu, což by zpomalilo inicializaci. Takže `OpenAsync` použití nemusí být užitečné v tomto scénáři, protože zpomaluje spuštění aplikace.

   <a id="same-region"></a>
**Z důvodu výkonu můžete společně umístit klienty ve stejné oblasti Azure.**

Pokud je to možné, umístěte všechny aplikace, které volají Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos DB. Tady je přibližné porovnání: volání do Azure Cosmos DB ve stejné oblasti dokončena do 1 ms na 2 ms, ale latence mezi západním a východním pobřeží usa je více než 50 ms. Tato latence se může lišit od požadavku k požadavku, v závislosti na trase přijaté požadavek při jeho předává z klienta na hranici datového centra Azure. Nejnižší možnou latenci můžete získat tím, že zajistíte, že volající aplikace se nachází ve stejné oblasti Azure jako zřízený koncový bod Azure Cosmos DB. Seznam dostupných oblastí najdete v [tématu Oblasti Azure](https://azure.microsoft.com/regions/#services).

![Zásady](./media/performance-tips/same-region.png) připojení Azure Cosmos DB<a id="increase-threads"></a>

**Zvýšení počtu podprocesů/úkolů**

Vzhledem k tomu, že volání Azure Cosmos DB jsou prováděny v síti, budete muset změnit stupeň paralelismu vašich požadavků tak, aby klientská aplikace stráví minimální dobu čekání mezi požadavky. Například pokud používáte [.NET Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx), vytvořte v pořadí stovek úloh, které čtou z nebo zapisují do Azure Cosmos DB.

**Povolení zrychlených sítí**
 
 Chcete-li snížit latenci a kolísání procesoru, doporučujeme povolit zrychlené vytváření sítí na klientských virtuálních počítačích. Viz [Vytvoření virtuálního počítače s Windows s akcelerací sítí](../virtual-network/create-vm-accelerated-networking-powershell.md) nebo Vytvoření [virtuálního počítače s Linuxem se zrychlenou sítí](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Využití sady SDK
**Instalace nejnovější sady SDK**

Sady Azure Cosmos DB SDK se neustále vylepšují, aby poskytovaly nejlepší výkon. Podívejte se na stránky [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) k určení nejnovější sady SDK a vylepšení kontroly.

**Použití datových api**

[Sada .NET SDK V3](sql-api-sdk-dotnet-standard.md) obsahuje rozhraní API datového proudu, která mohou přijímat a vracet data bez serializace. 

Aplikace střední vrstvy, které nespotřebovávají odpovědi přímo z sady SDK, ale předávají je jiným aplikačním vrstvám, mohou využívat data API. Podívejte se na ukázky [správy položek](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) příklady zpracování datového proudu.

**Používejte klienta Azure Cosmos DB po dobu životnosti vaší aplikace**

Každý `DocumentClient` `CosmosClient` a instance je bezpečný pro přístup z více vláken a provádí efektivní správu připojení a ukládání adres do mezipaměti při práci v přímém režimu. Chcete-li povolit efektivní správu připojení a lepší výkon klienta `AppDomain` sady SDK, doporučujeme použít jednu instanci za životnost aplikace.

   <a id="max-connection"></a>

**Zvýšení System.Net MaxConnections na hostitele při použití režimu brány**

Požadavky Azure Cosmos DB se uskutečňují přes PROTOKOL HTTPS/REST při použití režimu brány. Podléhají výchozímu limitu připojení na název hostitele nebo IP adresu. Možná budete muset `MaxConnections` nastavit vyšší hodnotu (100 až 1 000), aby klientská knihovna mohla používat více souběžných připojení k Azure Cosmos DB. V sdk 1.8.0 a novějším rozhraní .NET Je výchozí hodnota pro [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50. Chcete-li změnit hodnotu, můžete nastavit [documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) na vyšší hodnotu.

**Vyladit paralelní dotazy pro rozdělené kolekce**

SQL .NET SDK 1.9.0 a novější podporují paralelní dotazy, které umožňují dotaz na rozdělenou kolekci paralelně. Další informace naleznete v [tématu ukázky kódu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) související s prací s sadami SDK. Paralelní dotazy jsou navrženy tak, aby poskytovaly lepší latenci dotazu a propustnost než jejich sériový protějšek. Paralelní dotazy poskytují dva parametry, které můžete vyladit tak, aby vyhovovaly vašim požadavkům: 
- `MaxDegreeOfParallelism`řídí maximální počet oddílů, které mohou být dotazovány paralelně. 
- `MaxBufferedItemCount`určuje počet výsledků před načtením.

***Stupeň ladění paralelismu***

Paralelní dotaz funguje tak, že dotazuje více oddílů paralelně. Ale data z jednotlivého oddílu je načten sériově s ohledem na dotaz. Nastavení `MaxDegreeOfParallelism` v [sdk V2](sql-api-sdk-dotnet.md) nebo `MaxConcurrency` [SDK V3](sql-api-sdk-dotnet-standard.md) na počet oddílů má nejlepší šanci na dosažení nejvýkonnější dotaz, za předpokladu, že všechny ostatní systémové podmínky zůstávají stejné. Pokud neznáte počet oddílů, můžete nastavit stupeň paralelismu na vysoké číslo. Systém zvolí minimální (počet oddílů, uživatel za předpokladu, vstup) jako stupeň paralelismu.

Všimněte si, že paralelní dotazy produkují největší užitek, pokud jsou data rovnoměrně rozložena mezi všechny oddíly s ohledem na dotaz. Pokud je rozdělená kolekce rozdělena tak, aby všechna nebo většina dat vrácených dotazem byla soustředěna v několika oddílech (jeden oddíl je nejhorší případ), budou tyto oddíly kritickým bodem výkonu dotazu.

***Optimalizace maxbuffereditemcount***
    
Paralelní dotaz je navržen tak, aby předem načíst výsledky při aktuální dávka výsledků je zpracovávána klientem. Toto předběžné načtení pomáhá zlepšit celkovou latenci dotazu. Parametr `MaxBufferedItemCount` omezuje počet výsledků před načtením. Nastavte `MaxBufferedItemCount` očekávaný počet vrácených výsledků (nebo vyšší číslo), aby dotaz mohl získat maximální užitek z předběžného načítání.

Předběžné načítání funguje stejným způsobem bez ohledu na stupeň paralelismu a je k dispozici jedna vyrovnávací paměť pro data ze všech oddílů.  

**Implementovat backoff v intervalech RetryAfter**

Během testování výkonu byste měli zvýšit zatížení, dokud nebude omezena malá rychlost požadavků. Pokud jsou požadavky omezeny, klientská aplikace by měla zálohovat na omezení pro interval opakování zadané serverem. Respektování backoff zajišťuje strávíte minimální množství času čekání mezi opakování. 

Podpora zásad opakování je součástí těchto sad SDK:
- Verze 1.8.0 a novější [sady .NET SDK pro SQL](sql-api-sdk-dotnet.md) a java [sdk pro SQL](sql-api-sdk-java.md)
- Verze 1.9.0 a novější [sady Node.js SDK pro SQL](sql-api-sdk-node.md) a sady Python [SDK pro SQL](sql-api-sdk-python.md)
- Všechny podporované verze sad [SDK jádra .NET](sql-api-sdk-dotnet-core.md) 

Další informace naleznete v tématu [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
Ve verzi 1.19 a novější sady .NET SDK existuje mechanismus pro protokolování dalších diagnostických informací a odstraňování problémů s latencí, jak je znázorněno v následující ukázce. Diagnostický řetězec můžete protokolovat pro požadavky, které mají vyšší latenci čtení. Zachycený diagnostický řetězec vám pomůže pochopit, kolikrát jste obdrželi 429 chyb pro daný požadavek.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Ukládat do mezipaměti identifikátory URI dokumentu pro nižší latenci čtení**

Pokud je to možné, uvězte do mezipaměti identifikátory URI dokumentů, kdykoli je to možné, pro dosažení nejlepšího výkonu čtení. Při vytváření prostředku je třeba definovat logiku pro uložení ID prostředku do mezipaměti. Vyhledávání založená na ID prostředků jsou rychlejší než vyhledávání založená na názvech, takže ukládání těchto hodnot do mezipaměti zvyšuje výkon.

   <a id="tune-page-size"></a>
**Vyladění velikosti stránky pro dotazy/zdroje pro čtení pro lepší výkon**

Při hromadnéčtení dokumentů pomocí funkce informačního kanálu pro `ReadDocumentFeedAsync`čtení (například) nebo při vydání dotazu SQL, výsledky jsou vráceny segmentovaným způsobem, pokud je sada výsledků příliš velká. Ve výchozím nastavení jsou výsledky vráceny v blocích 100 položek nebo 1 MB, podle toho, jaký limit je přístupů jako první.

Chcete-li snížit počet síťových zpátečních cest potřebných k načtení všech platných výsledků, můžete zvětšit velikost stránky pomocí [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) a požádat o až 1 000 záhlaví. Pokud potřebujete zobrazit pouze několik výsledků, například pokud vaše uživatelské rozhraní nebo rozhraní API aplikace vrátí pouze 10 výsledků najednou, můžete také snížit velikost stránky na 10 snížit propustnost spotřebované pro čtení a dotazy.

> [!NOTE] 
> Vlastnost `maxItemCount` by neměla být používána pouze pro stránkování. Jeho hlavním účelem je zlepšit výkon dotazů snížením maximálního počtu položek vrácených na jedné stránce.  

Můžete také nastavit velikost stránky pomocí dostupných sad Azure Cosmos DB SDK. [Vlastnost MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) `FeedOptions` v aplikaci umožňuje nastavit maximální počet položek, které mají být vráceny v operaci výčtu. Pokud `maxItemCount` je nastavena na -1, sada SDK automaticky najde optimální hodnotu v závislosti na velikosti dokumentu. Například:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Při spuštění dotazu jsou výsledná data odeslána v rámci paketu TCP. Pokud zadáte příliš nízkou `maxItemCount`hodnotu pro , počet cest potřebných k odeslání dat v rámci paketu TCP je vysoký, což má vliv na výkon. Pokud si tedy nejste jisti, jakou hodnotu nastavit pro `maxItemCount` vlastnost, je nejlepší ji nastavit na hodnotu -1 a nechat sadu SDK zvolit výchozí hodnotu.

**Zvýšení počtu podprocesů/úkolů**

Viz [Zvýšení počtu vláken/úkolů](#increase-threads) v části Sítě v tomto článku.

## <a name="indexing-policy"></a>Zásady indexování
 
**Vyloučit nepoužívané cesty z indexování pro rychlejší zápisy**

Zásady indexování Azure Cosmos DB také umožňuje určit, které cesty dokumentu zahrnout do nebo vyloučit z indexování pomocí indexování cesty (IndexingPolicy.IncludedPaths a IndexingPolicy.ExcludedPaths). Indexování cesty můžete zlepšit výkon zápisu a snížit úložiště indexu pro scénáře, ve kterých jsou předem známy vzorky dotazu. Důvodem je, že náklady na indexování korelují přímo s počtem jedinečných cest indexovaných. Tento kód například ukazuje, jak vyloučit celou část dokumentů (podstrom) z indexování pomocí zástupného znaku *:

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

Další informace naleznete v tématu [zásadindexování Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

**Měření a ladění pro nižší jednotky požadavků za sekundu**

Azure Cosmos DB nabízí bohatou sadu databázových operací. Tyto operace zahrnují relační a hierarchické dotazy s UD, uložené procedury a aktivační události, všechny operace na dokumenty v rámci kolekce databáze. Náklady spojené s každou z těchto operací se liší v závislosti na procesoru, vod a paměti potřebné k dokončení operace. Místo přemýšlení o a správě hardwarových prostředků si můžete jednotku požadavku (RU) považovat za jediné opatření pro prostředky potřebné k provádění různých databázových operací a servisu žádosti o aplikaci.

Propustnost je zřízena na základě počtu [jednotek požadavků](request-units.md) nastavených pro každý kontejner. Požadavek Jednotka spotřeba je vyhodnocena jako sazba za sekundu. Aplikace, které překračují zřízenou jednotkovou sazbu požadavku pro jejich kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň pro kontejner. Pokud vaše aplikace vyžaduje vyšší úroveň propustnost, můžete zvýšit propustnost zřízením další chynaté jednotky.

Složitost dotazu ovlivňuje, kolik jednotek požadavků je spotřebováno pro operaci. Počet predikátů, povaha predikátů, počet UD a velikost zdrojové datové sady ovlivňují náklady na operace dotazu.

Chcete-li měřit režii jakékoli operace (vytvoření, aktualizace nebo odstranění), zkontrolujte hlavičku `ResourceResponse\<T>` [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (nebo ekvivalentní `RequestCharge` vlastnost v nebo `FeedResponse\<T>` v sdk .NET) a změřte počet jednotek požadavků spotřebovaných operacemi:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Poplatek za požadavek vrácený v této hlavičce je zlomkem zřízené propustnosti (to znamená 2 000 ru za sekundu). Například pokud předchozí dotaz vrátí 1 000 dokumentů 1 KB, náklady na operaci je 1 000. Takže během jedné sekundy server respektuje pouze dva takové požadavky před omezením rychlosti novějších požadavků. Další informace naleznete v tématu [Request Units](request-units.md) a calculator [Request Unit](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Zpracovat rychlost omezení /rychlost požadavku příliš velký**

Když se klient pokusí překročit vyhrazenou propustnost pro účet, neexistuje žádné snížení výkonu na serveru a žádné použití kapacity propustnosti nad vyhrazenou úroveň. Server preventivně ukončí požadavek pomocí RequestRateTooLarge (stavový kód HTTP 429). Vrátí hlavičku [x-ms-retry-after-ms,](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) která označuje dobu v milisekundách, po kterou musí uživatel před opětovným pokusem o požadavek počkat.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Všechny sady SDK implicitně zachytit tuto odpověď, respektovat serverem zadané opakování po záhlaví a opakujte požadavek. Pokud váš účet není přístupná současně více klientů, další opakování bude úspěšné.

Pokud máte více než jednoho klienta kumulativně pracujícíka konzistentně nad sazbu požadavku, výchozí počet opakování aktuálně nastavený na 9 interně klientem nemusí stačit. V tomto případě klient vyvolá DocumentClientException se stavovým kódem 429 do aplikace. 

Výchozí počet opakování můžete změnit nastavením `RetryOptions` `ConnectionPolicy` instance. Ve výchozím nastavení DocumentClientException se stavovým kódem 429 je vrácena po kumulativní čekací doba 30 sekund, pokud požadavek nadále pracovat nad sazbu požadavku. Tato chyba vrátí i v případě, že aktuální počet opakování je menší než maximální počet opakování, zda aktuální hodnota je výchozí 9 nebo uživatelem definovaná hodnota.

Automatické chování opakování pomáhá zlepšit odolnost proti chybám a použitelnost pro většinu aplikací. Ale nemusí to být nejlepší chování, když děláte měřítka výkonu, zejména když měříte latenci. Latence s pozorovaná klientem se zvýší, pokud experiment narazí na serverovou škrticí klapku a způsobí, že sada SDK klienta tiše provede opakování. Chcete-li se vyhnout špičkám latence během experimentů s výkonem, změřte poplatek vrácený každou operací a ujistěte se, že požadavky pracují pod rezervovanou sazbou požadavku. Další informace naleznete v tématu [Request Units](request-units.md).

**Pro vyšší propustnost, návrh pro menší dokumenty**

Poplatek požadavku (tj. náklady na zpracování požadavku) dané operace koreluje přímo s velikostí dokumentu. Operace s velkými dokumenty stojí více než operace s malými dokumenty.

## <a name="next-steps"></a>Další kroky
Ukázková aplikace, která se používá k vyhodnocení Azure Cosmos DB pro scénáře s vysokým výkonem na několika klientských počítačích, najdete [v tématu Testování výkonu a škálování pomocí Azure Cosmos DB](performance-testing.md).

Další informace o návrhu aplikace pro škálování a vysoký výkon najdete [v tématu dělení a škálování v Azure Cosmos DB](partition-data.md).
