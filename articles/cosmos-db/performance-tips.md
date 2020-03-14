---
title: Tipy pro výkon Azure Cosmos DB pro .NET
description: Seznamte se s možnostmi konfigurace klientů pro zlepšení výkonu databáze Azure Cosmos
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: dc9d10a6539c7fc3a7c5c8b3db290cc951c24883
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252464"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Tipy ke zvýšení výkonu pro Azure Cosmos DB a .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která bez problémů škáluje zaručenou latenci a propustnost. Nemusíte dělat zásadní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Vertikální navýšení a snížení kapacity je stejně snadné jako při provádění jediného volání rozhraní API. Další informace najdete v tématu [jak zřídit propustnost kontejneru](how-to-provision-container-throughput.md) nebo [jak zřídit propustnost databáze](how-to-provision-database-throughput.md). Vzhledem k tomu, že k Azure Cosmos DB je k dispozici prostřednictvím síťových volání, existují optimalizace na straně klienta, které vám při použití [sady SQL .NET SDK](sql-api-sdk-dotnet-standard.md)umožní dosáhnout špičkového výkonu.

Takže pokud si vyžádáte "Jak můžu vylepšit výkon databáze?" Vezměte v úvahu následující možnosti:

## <a name="hosting-recommendations"></a>Doporučení hostování

1.  **Pro úlohy náročné na výpočetní výkon použijte Windows 64, nikoli hostitele se systémem Linux nebo Windows 32.**

    Pro zvýšení výkonu se doporučuje zpracování bitového hostitelského systému Windows 64. Sada SQL SDK obsahuje nativní ServiceInterop. dll k analýze a optimalizaci dotazů v místním prostředí a je podporována pouze na platformě Windows x64. Pro Linux a jiné nepodporované platformy, kde ServiceInterop. dll není k dispozici, provede další síťové volání brány k získání optimalizovaného dotazu. Následující typy aplikací mají 32 hostitelský hostitelský proces jako výchozí, takže aby bylo možné změnit to na 64-bit, postupujte podle těchto kroků v závislosti na typu vaší aplikace:

    - U spustitelných aplikací to lze provést nastavením možnosti [cíl platformy](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) na hodnotu **x64** v okně **Vlastnosti projektu** na kartě **sestavení** .

    - Pro projekty testů založené na VSTest lze to provést tak, že v nabídce **test sady Visual Studio** vyberete možnost **test**->**nastavení testu**->**výchozí architekturu procesoru jako x64**.

    - Pro lokálně nasazené webové aplikace v ASP.NET se to dá udělat tak, že zkontrolujete **použití 64 verze IIS Express pro weby a projekty**v části **nástroje**->**možnosti**->**projekty a řešení**->**webové projekty**.

    - Pro webové aplikace ASP.NET nasazené v Azure to můžete udělat tak, že v **nastavení aplikace** na Azure Portal vyberete možnost **platforma jako 64-bit** .

    > [!NOTE] 
    > Visual Studio je výchozím nastavením nových projektů na libovolný procesor. Doporučuje se nastavit projekt na x64, aby se zabránilo přepínání na x86. Každý projekt CPU se může snadno přepnout na x86, pokud se přidá jakákoli závislost, která je jenom x86.<br/>
    > ServiceInterop. dll musí být ve stejné složce, ze které je spuštěna knihovna DLL sady SDK. To by mělo být nutné provést pouze pro uživatele, kteří ručně kopírování knihovny DLL, nebo mají vlastní systémy sestavení/nasazení.
    
2. **Zapnout uvolňování paměti na straně serveru (GC)**

    Omezení frekvence uvolňování paměti může v některých případech pomáhat. V rozhraní .NET nastavte [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) na true.

3. **Horizontální navýšení kapacity klienta – zatížení**

    Pokud testujete na úrovních vysoké propustnosti (> 50 000 RU/s), může se klientská aplikace stát kritickým bodem, protože počítač vycapping na využití procesoru nebo sítě. Pokud se dostanou k tomuto bodu, můžete dál nasdílet Azure Cosmos DB účet tím, že navedete horizontální navýšení kapacity klientských aplikací napříč více servery.

    > [!NOTE] 
    > Vysoké využití procesoru může způsobit zvýšení latence a vypršení časového limitu požadavku.

## <a name="networking"></a>Sítě
<a id="direct-connection"></a>

1. **Zásady připojení: použít přímý režim připojení**

    Způsob, jakým se klient připojuje ke službě Azure Cosmos DB, má významný vliv na výkon, a to zejména z hlediska pozorované latence na straně klienta. K dispozici jsou dvě nastavení konfigurace klíče pro konfiguraci zásad připojení klienta – *režim* připojení a *protokol*připojení.  K dispozici jsou dva režimy:

   * Režim brány
      
     Režim brány je podporovaný na všech platformách SDK a je nakonfigurovaná výchozí [sada pro Microsoft. Azure. DocumentDB SDK](sql-api-sdk-dotnet.md). Pokud vaše aplikace běží v podnikové síti s přísnými omezeními brány firewall, je nejlepší volbou režim brány, protože používá standardní port HTTPS a jeden koncový bod. Kompromis mezi výkonem je ale v tom, že režim brány zahrnuje další směrování sítě při každém čtení nebo zápisu dat do Azure Cosmos DB. Díky tomu přímý režim nabízí lepší výkon kvůli menšímu počtu směrování sítě. Režim připojení brány se doporučuje taky při spouštění aplikací v prostředích s omezeným počtem připojení soketů. 

     Při použití sady SDK v Azure Functions, zejména v [plánu spotřeby](../azure-functions/functions-scale.md#consumption-plan), si zavědomete současných [omezení v připojeních](../azure-functions/manage-connections.md). V takovém případě se může režim brány doporučit, pokud také pracujete s dalšími klienty založenými na protokolu HTTP v rámci vaší aplikace Azure Functions.

   * Přímý režim

     Přímý režim podporuje připojení prostřednictvím protokolu TCP a je výchozím režimem připojení, pokud používáte [Microsoft. Azure. Cosmos/.NET V3 SDK](sql-api-sdk-dotnet-standard.md).

     Při použití režimu brány Cosmos DB používá rozhraní API Azure Cosmos DB pro MongoDB port 443 a porty 10250, 10255 a 10256. Port 10250 se mapuje na výchozí instanci MongoDB bez geografické replikace a 10255/10256 portů na instanci MongoDB s funkcí geografické replikace. Při použití TCP v přímém režimu kromě portů brány je potřeba zajistit, aby byl rozsah portů mezi 10000 a 20000 otevřený, protože Azure Cosmos DB používá dynamické porty TCP. Pokud nejsou tyto porty otevřené a pokusíte se použít protokol TCP, obdržíte nedostupnou chybu služby 503. Následující tabulka uvádí režimy připojení, které jsou dostupné pro různá rozhraní API a porty služby pro každé rozhraní API:

     |Režim připojení  |Podporovaný protokol  |Podporované sady SDK  |Port API/Service  |
     |---------|---------|---------|---------|
     |Brána  |   HTTPS    |  Všechny sady SDK    |   SQL (443), Mongo (10250, 10255, 10256), Table (443), Cassandra (10350), Graph (443)    |
     |Přímé    |     TCP    |  .NET SDK    | Porty v rozsahu 10000 až 20000 |

     Azure Cosmos DB nabízí jednoduchý a otevřený programovací model RESTful přes protokol HTTPS. Navíc nabízí efektivní protokol TCP, který se také RESTful ve svém komunikačním modelu a je dostupný prostřednictvím klientské sady SDK pro .NET. Protokol TCP používá protokol SSL pro počáteční ověřování a šifrování provozu. Pro nejlepší výkon použijte protokol TCP, pokud je to možné.

     V případě sady SDK v3 je režim připojení nakonfigurován při vytváření instance CosmosClient jako součást CosmosClientOptions, pamatujte, že výchozím režimem je přímý režim.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
     new CosmosClientOptions
     {
        ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
     });
     ```

     V sadě Microsoft. Azure. DocumentDB SDK je režim připojení nakonfigurovaný během vytváření instance DocumentClient s parametrem ConnectionPolicy. Je-li použit přímý režim, lze protokol nastavit také v rámci parametru ConnectionPolicy.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct, //ConnectionMode.Gateway is the default
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Protože je protokol TCP podporován pouze v přímém režimu, je-li použit režim brány, protokol HTTPS se vždy používá ke komunikaci s bránou a hodnota protokolu v ConnectionPolicy je ignorována.

     ![Obrázek zásad Azure Cosmos DBho připojení](./media/performance-tips/connection-policy.png)

2. **Zavolejte OpenAsync, aby se zamezilo latenci při spuštění prvního požadavku.**

    Ve výchozím nastavení má první požadavek větší latenci, protože musí načíst směrovací tabulku adres. Pokud používáte [sadu SDK v2](sql-api-sdk-dotnet.md), abyste se vyhnuli této latenci při spuštění prvního požadavku, měli byste zavolat openAsync () jednou během inicializace následujícím způsobem.

        await client.OpenAsync();

    > [!NOTE] 
    > Metoda OpenAsync vytvoří žádosti o získání směrovací tabulky adres pro všechny kontejnery v účtu. Pro účty, které mají mnoho kontejnerů, ale jejich aplikace přistupuje k podmnožině, by vygenerovala zbytečný objem provozu, který způsobí, že se inicializace zpomalí. Použití metody OpenAsync nemusí být v tomto scénáři užitečné, protože zpomaluje spuštění aplikace.

   <a id="same-region"></a>
3. **Společné umístění klientů ve stejné oblasti Azure pro výkon**

    Pokud je to možné, umístěte všechny aplikace, které volají Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos. V případě přibližného porovnání se volání Azure Cosmos DB v rámci stejné oblasti dokončí v 1-2 MS, ale latence mezi západním a východním pobřežím USA je > 50 ms. Tato latence se může lišit od požadavku na vyžádání v závislosti na trasách, kterou požadavek prochází z klienta na hranici datacentra Azure. Nejnižší možná latence se dosahuje tím, že se zaručí, že se volající aplikace nachází ve stejné oblasti Azure jako koncový bod zřízené Azure Cosmos DB. Seznam oblastí, které jsou k dispozici, najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

    ![obrázek zásady připojení Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>

4. **Zvýšení počtu vláken/úloh**

    Vzhledem k tomu, že se volání Azure Cosmos DB provádí přes síť, možná budete muset změnit stupeň paralelismu požadavků, aby klientská aplikace při zpracování požadavků čekala velmi málo času. Například pokud používáte. SÍŤ [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx), která se vytváří v řádu 100 úloh pro čtení nebo zápis do Azure Cosmos DB.

5. **Povolit akcelerované síťové služby**

   Aby se snížila latence a kolísání procesoru, doporučujeme, aby klientské virtuální počítače měly aktivované urychlení sítě. Pokud chcete povolit urychlené síťové služby, přečtěte si téma [Vytvoření virtuálního počítače s Windows s akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-powershell.md) službami nebo [Vytvoření virtuálního počítače se systémem Linux s využitím akcelerovaných síťových](../virtual-network/create-vm-accelerated-networking-cli.md) článků.


## <a name="sdk-usage"></a>Využití sady SDK
1. **Nainstalovat nejnovější sadu SDK**

    Sady Azure Cosmos DB SDK se neustále zdokonalují, aby poskytovaly nejlepší výkon. Pokud chcete zjistit nejnovější sadu SDK a zkontrolovat vylepšení, podívejte se na stránky [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) .

2. **Použití rozhraní API pro streamování**

    [Sada .NET SDK V3](sql-api-sdk-dotnet-standard.md) obsahuje rozhraní API pro Stream, která mohou přijímat a vracet data bez serializace. 

    Aplikace střední vrstvy, které nespotřebovávají odpovědi přímo z SDK, ale předají jim přenos na jiné aplikační vrstvy, můžou těžit z rozhraní API streamu. Příklady zpracování streamu najdete v ukázkách [správy položek](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) .

3. **Použití typu Singleton Azure Cosmos DB klienta po dobu života vaší aplikace**

    Každá instance DocumentClient a CosmosClient je bezpečná pro přístup z více vláken a při práci v přímém režimu provádí efektivní správu připojení a ukládání adres do mezipaměti. Aby bylo možné efektivně spravovat správu a lepší výkon pomocí klienta SDK, doporučuje se použít pro každou doménu AppDomain jednu instanci pro celou dobu života aplikace.

   <a id="max-connection"></a>

4. **Při použití režimu brány zvýšit System.Net MaxConnections na hostitele**

    Azure Cosmos DB požadavky se při použití režimu brány převezmou přes HTTPS/REST a řídí se výchozí limit počtu připojení na název hostitele nebo IP adresa. Je možné, že bude nutné nastavit hodnoty MaxConnections na vyšší hodnotu (100-1000), aby Klientská knihovna mohla využívat více souběžných připojení k Azure Cosmos DB. V sadě .NET SDK 1.8.0 a vyšších je výchozí hodnota pro [Třída ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50 a změnit hodnotu, můžete nastavit vyšší hodnotu v [Documents. Client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) .

5. **Ladění paralelních dotazů pro dělené kolekce**

     SQL .NET SDK verze 1.9.0 a vyšší podporují paralelní dotazy, které umožňují paralelní dotazování rozdělené kolekce. Další informace najdete v tématu [ukázky kódu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) týkající se práce se sadami SDK. Paralelní dotazy jsou navržené tak, aby se zlepšila latence a propustnost dotazů v rámci svého sériového protějšku. Paralelní dotazy poskytují dva parametry, které mohou uživatelé ladit tak, aby vyhovovaly vlastním požadavkům, (a) Z MaxDegreeOfParallelism: pro kontrolu maximálního počtu oddílů, které lze následně dotazovat paralelně a (b) MaxBufferedItemCount: pro řízení počtu předem načtené výsledky.

    (a) ***stupeň vyladění paralelismu\:*** paralelní dotaz funguje tak, že dotazuje více oddílů paralelně. Data z jednotlivého oddílu se ale v souvislosti s dotazem načítají do sériového tvaru. Nastavení `MaxDegreeOfParallelism` v [sadě SDK v2](sql-api-sdk-dotnet.md) nebo `MaxConcurrency` v [sadě SDK V3](sql-api-sdk-dotnet-standard.md) na počet oddílů má maximální šanci na dosažení nejvíce výkonného dotazu, pokud všechny ostatní systémové podmínky zůstanou stejné. Pokud neznáte počet oddílů, můžete nastavit míru paralelismu na vysoké číslo a systém zvolí minimální (počet oddílů, vstup zadaný uživatelem) jako stupeň paralelismu.

    Je důležité si uvědomit, že paralelní dotazy poskytují nejlepší výhody, pokud jsou data rovnoměrně rozložena napříč všemi oddíly v souvislosti s dotazem. Pokud je dělená kolekce rozdělena takovým způsobem, že všechna nebo většina dat vrácených dotazem je soustředěna v několika oddílech (jeden oddíl v nejhorším případě), výkon dotazu by tyto oddíly měl být kritický.

    (b) ***vyladění MaxBufferedItemCount\:*** paralelní dotaz je navržený tak, aby se výsledky předem načetly, zatímco aktuální dávka výsledků zpracovává klient. Předběžné načítání pomáhá při celkové latenci v rámci dotazu. MaxBufferedItemCount je parametr pro omezení počtu předběžně načtených výsledků. Nastavení MaxBufferedItemCount na očekávaný počet vrácených výsledků (nebo vyšší číslo) umožňuje, aby dotaz získal maximální přínos před načtením.

    Předběžné načítání funguje stejným způsobem bez ohledu na stupeň paralelismu a existuje jedna vyrovnávací paměť pro data ze všech oddílů.  

6. **Implementace omezení rychlosti v intervalech RetryAfter**

    Během testování výkonu byste měli zvýšit zatížení až do omezení malých sazeb požadavků. V případě omezení by se klientská aplikace měla omezení rychlosti na omezení pro interval opakování zadaný serverem. Respektování omezení rychlosti zajistí, že strávíte minimální dobu čekání mezi opakovanými pokusy. Podpora zásad opakování je obsažená ve verzi 1.8.0 a novějších rozhraních SQL [.NET](sql-api-sdk-dotnet.md) a [Java](sql-api-sdk-java.md)verze 1.9.0 a novějších z [Node. js](sql-api-sdk-node.md) a [Pythonu](sql-api-sdk-python.md)a všechny podporované verze sady [.NET Core](sql-api-sdk-dotnet-core.md) SDK. Další informace najdete v [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    S verzí 1,19 a novější sadou .NET SDK je k dispozici mechanismus pro protokolování dalších diagnostických informací a řešení potíží s latencí, jak je znázorněno v následující ukázce. Můžete protokolovat diagnostický řetězec pro požadavky, které mají vyšší latenci čtení. Zachycený diagnostický řetězec vám pomůže pochopit počet pokusů o 429si pro daný požadavek.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```

7. **Mezipaměť identifikátorů URI dokumentů pro nižší latenci čtení**

    Ukládání identifikátorů URI dokumentů mezipaměti, kdykoli je to možné, pro nejlepší výkon čtení. Je nutné definovat logiku pro ukládání ID prostředku do mezipaměti při vytváření prostředku. Vyhledávání na základě ID prostředku jsou rychlejší než vyhledávání na základě názvu, takže ukládání těchto hodnot do mezipaměti zvyšuje výkon. 

   <a id="tune-page-size"></a>
8. **Vyladění velikosti stránek pro dotazy a kanály pro čtení pro lepší výkon**

   Při hromadném čtení dokumentů pomocí funkce kanálu čtení (například ReadDocumentFeedAsync) nebo při vystavování dotazu SQL se výsledky vrátí segmenticky, pokud je sada výsledků příliš velká. Ve výchozím nastavení se výsledky vrátí do bloků 100 položek nebo 1 MB, podle toho, který limit se narazí jako první.

   Chcete-li snížit počet síťových přenosů potřebných k načtení všech použitelných výsledků, můžete zvětšit velikost stránky pomocí záhlaví požadavku [x-MS-Max-Item-Count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) až na 1000. V případech, kdy potřebujete zobrazit jenom pár výsledků, například pokud vaše uživatelské rozhraní nebo rozhraní API pro aplikace vrátí jenom 10 výsledků, můžete také zmenšit velikost stránky na 10 a snížit tak propustnost, která se pro čtení a dotazy spotřebují.

   > [!NOTE] 
   > Vlastnost maxItemCount by se neměla používat jenom pro účely stránkování. Je to hlavní využití pro zlepšení výkonu dotazů snížením maximálního počtu položek vrácených na jednu stránku.  

   Velikost stránky můžete nastavit také pomocí dostupných Azure Cosmos DB sad SDK. Vlastnost [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) v FeedOptions umožňuje nastavit maximální počet položek, které mají být vráceny v rámci operace výčtu. Pokud je `maxItemCount` nastaveno na hodnotu-1, sada SDK automaticky najde optimální hodnotu v závislosti na velikosti dokumentu. Příklad:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Při spuštění dotazu jsou výsledná data odeslána v rámci paketu TCP. Pokud zadáte pro `maxItemCount`příliš nízkou hodnotu, počet cest potřebných k odeslání dat v rámci paketu TCP je vysoký, což ovlivňuje výkon. Takže pokud si nejste jistí, jaká hodnota má být nastavena pro vlastnost `maxItemCount`, je nejlepší ji nastavit na hodnotu-1 a nechat sadu SDK, aby zvolila výchozí hodnotu. 

9. **Zvýšení počtu vláken/úloh**

    Viz [zvýšení počtu vláken/úloh](#increase-threads) v části sítě.

## <a name="indexing-policy"></a>Zásada indexování
 
1. **Vyloučení nepoužívaných cest z indexování za účelem zrychlení zápisu**

    Zásada indexování Cosmos DB také umožňuje určit, které cesty dokumentů zahrnout nebo vyloučit z indexování pomocí cest indexování (IndexingPolicy. IncludedPaths a IndexingPolicy. ExcludedPaths). Použití cest indexování může nabízet lepší výkon zápisu a nižší indexové úložiště pro scénáře, ve kterých jsou vzory dotazů předem známé, protože náklady na indexování jsou přímo ve vztahu k počtu indexovaných jedinečných cest.  Například následující kód ukazuje, jak vyloučit celý oddíl dokumentů (podstrom) z indexování pomocí zástupného znaku "*".

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Další informace najdete v tématu [Azure Cosmos DB zásady indexování](index-policy.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

1. **Měření a optimalizace pro nižší jednotky žádostí za sekundu použití**

    Azure Cosmos DB nabízí bohatou sadu databázových operací, včetně relačních a hierarchických dotazů s UDF, uloženými procedurami a triggery – to vše v dokumentech v rámci kolekce databáze. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, V/V a paměti, které jsou potřeba k dokončení operace. Místo toho, abyste si vymysleli a spravovali hardwarové prostředky, si můžete představit jednotku žádosti (RU) jako jednu míru prostředků potřebných k provádění různých databázových operací a zpracování požadavků aplikace.

    Propustnost se zřizuje na základě počtu [jednotek žádostí](request-units.md) nastavených pro každý kontejner. Spotřeba jednotky požadavku se vyhodnotí jako sazba za sekundu. Aplikace, které překračují sazbu jednotky zřízené žádosti pro svůj kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň kontejneru. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit svou propustnost tím, že zřizujete další jednotky žádostí. 

    Složitost dotazu ovlivňuje počet spotřebovaných jednotek požadavků pro určitou operaci. Počet predikátů, povaha predikátů, počet UDF a velikost zdrojové sady dat ovlivňují náklady na operace dotazů.

    Pokud chcete změřit režii jakékoli operace (vytvořit, aktualizovat nebo odstranit), zkontrolujte záhlaví [x-MS-Request-poplatek](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (nebo ekvivalentní vlastnost RequestCharge v ResourceResponse\<t > nebo FeedResponse\<t > v sadě .NET SDK) a změřte tak počet jednotek žádostí spotřebovaných těmito operacemi.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    Poplatek za požadavek vrácený v této hlavičce je zlomek zřízené propustnosti (tj. 2000 ru za sekundu). Například pokud předchozí dotaz vrátí 1000 1 KB-Documents, náklady na operaci jsou 1000. V takovém případě se server během jedné sekundy připadá pouze na dva takové požadavky, než frekvence omezí následné požadavky. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek požadavků](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Omezení rychlosti zpracování/počet požadavků je moc velký.**

    Když se klient pokusí překročit rezervovanou propustnost účtu, neexistují žádné snížení výkonu na serveru a žádné využití kapacity propustnosti mimo rezervovanou úroveň. Server v tuto chvíli ukončí požadavek pomocí RequestRateTooLarge (kód stavu HTTP 429) a vrátí hlavičku [x-MS-Retry-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) , která indikuje, jak dlouho (v milisekundách) musí uživatel počkat, než se znovu pokusí o požadavek.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Sady SDK mají implicitně zachytit tuto odpověď, a to v souladu s hlavičkou, která je určená serverem, a zkuste žádost zopakovat. Pokud k účtu nepoužíváte souběžně více klientů, další pokus bude úspěšný.

    Pokud máte více než jednoho klienta, který se konzistentně pracuje konzistentně nad sazbou požadavku, nemusí být výchozí počet opakování nastavený na hodnotu 9 interně klientem. v takovém případě klient vyvolá DocumentClientException se stavovým kódem 429 do aplikace. Výchozí počet opakování lze změnit nastavením RetryOptions na instanci ConnectionPolicy. Ve výchozím nastavení se DocumentClientException se stavovým kódem 429 vrátí po kumulativní čekací době 30 sekund, pokud požadavek nadále funguje nad sazbou požadavku. K tomu dojde i v případě, že aktuální počet opakování je menší než maximální počet opakování, výchozí hodnota je 9 nebo uživatelem definovaná hodnota.

    I když automatizované chování při opakování pomáhá zlepšit odolnost a použitelnost většiny aplikací, může při měření latence docházet k lichá při provádění srovnávacích testů, zejména při měření latence.  Latence zjištěná klientem bude špička, pokud experiment narazí na omezení serveru a způsobí, že se klientská sada SDK tiše znovu pokusí. Aby se zabránilo špičkám latence během experimentů s výkonem, změřte poplatky vracené jednotlivými operacemi a zajistěte, aby požadavky byly v provozu pod rezervovanými sazbami požadavků. Další informace najdete v tématu [jednotky žádostí](request-units.md).
3. **Návrh pro menší dokumenty pro vyšší propustnost**

    Poplatek za požadavek (tj. náklady na zpracování požadavků) dané operace se přímo koreluje s velikostí dokumentu. Operace s velkým objemem dokumentů se při používání malých dokumentů dotýkají více než operací.

## <a name="next-steps"></a>Další kroky
Ukázkovou aplikaci, která se používá k vyhodnocení Azure Cosmos DB pro scénáře s vysokým výkonem na několika klientských počítačích, najdete v tématu [testování výkonu a škálování pomocí Azure Cosmos DB](performance-testing.md).

Další informace o návrhu aplikace pro škálování a vysoký výkon najdete v tématu [dělení a škálování v Azure Cosmos DB](partition-data.md).
