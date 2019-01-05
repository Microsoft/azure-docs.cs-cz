---
title: Azure Cosmos DB tipy ke zvýšení výkonu pro .NET
description: Další možnosti konfigurace klienta pro zvýšení výkonu databáze Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: sngun
ms.openlocfilehash: f05935f73f385b076741d2e856af8316a74e9d2f
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042491"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Tipy ke zvýšení výkonu pro službu Azure Cosmos DB a .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB je rychlé a flexibilní distribuovanou databázi, která se škáluje bez problémů s garantovanou latencí a propustností. Není nutné měnit hlavní architektury nebo zapsat složitý kód škálování databáze pomocí služby Azure Cosmos DB. Vertikální navýšení a snížení je stejně jednoduché jako jednoho volání rozhraní API. Další informace najdete v tématu [jak zřídit propustnosti kontejneru](how-to-provision-container-throughput.md) nebo [jak zřídit propustnost databáze](how-to-provision-database-throughput.md). Protože Azure Cosmos DB se přistupuje přes síťová volání jsou však můžete provést k dosažení maximální výkon při použití optimalizací na straně klienta [SQL SDK pro .NET](documentdb-sdk-dotnet.md).

Takže pokud máte s dotazem "Jak můžu vylepšit výkon Moje databáze?" Zvažte následující možnosti:

## <a name="networking"></a>Sítě
<a id="direct-connection"></a>

1. **Zásady připojení: Použití režimu přímé připojení**

    Jak se klient připojí ke službě Azure Cosmos DB má důležité vliv na výkon, hlavně z hlediska pozorované latence na straně klienta. K dispozici dva klíče konfigurační nastavení pro konfiguraci klienta zásady připojení – připojení *režimu* a [připojení *protokol*](#connection-protocol).  Jsou k dispozici dva režimy:

   * Režim brány (výchozí)
      
     Režim brány se podporuje na všech platformách sady SDK a je nakonfigurované výchozí nastavení. Pokud je aplikace spuštěná v rámci podnikové sítě s omezeními striktní brány firewall, režim brány je nejlepší volbou, protože používá standardní port HTTPS a jeden koncový bod. Tento režim brány zahrnuje směrování další síti pokaždé, když se data číst nebo zapisovat do služby Azure Cosmos DB je ale úkor výkonu. Z tohoto důvodu přímý režim nabízí lepší výkon z důvodu menší počet segmentů směrování. Režim připojení brány také doporučujeme při spuštění aplikace v prostředích s omezený počet připojení soketu, například při použití Azure Functions nebo pokud jste v plánu consumption. 

   * Přímý režim

     Přímý režim podporuje připojení přes protokoly TCP a HTTPS. V současné době se podporuje přímý v .NET Standard 2.0. Když používáte přímý režim, nejsou k dispozici dvě možnosti protokolu:

    * TCP
    * HTTPS

    Když používáte režim brány, Cosmos DB používá port 443 a porty 10250, 10255 a 10256 při používání rozhraní API služby Azure Cosmos DB pro MongoDB. Mapy 10250 port pro výchozí instanci databáze MongoDB bez geografickou replikaci a mapování portů 10255/10256 k instanci MongoDB pomocí funkce geografické replikace. Při použití protokolu TCP v přímém režimu, kromě portů brány, je potřeba zajistit port v rozsahu 10000 až 20000 je otevřený, protože Azure Cosmos DB používá dynamické porty TCP. Pokud tyto porty nejsou otevřené a při pokusu o použití protokolu TCP, zobrazí se chyba 503 Služba není dostupná. Následující tabulka uvádí dostupné režimy připojení různých rozhraní API a porty uživatele služby pro každé rozhraní API:

    |Režim připojení  |Podporovaný protokol  |Podporovaných sad SDK  |Služba API/port  |
    |---------|---------|---------|---------|
    |brána  |   HTTPS    |  Všechny sady SDK    |   SQL(443), Mongo (10250, 10255, 10256), Table(443), Cassandra(443), Graph(443)    |
    |Přímé    |    HTTPS     |  .NET a Java SDK    |    SQL(443)   |
    |Přímé    |     TCP    |  .NET SDK    | Porty v rozsahu 20 10 000-000 |

    Azure Cosmos DB nabízí jednoduchý a otevřené rozhraní RESTful programovací model přes protokol HTTPS. Kromě toho nabízí efektivní protokolu TCP, který je také RESTful svůj model komunikace a je dostupný prostřednictvím klienta .NET SDK. Přímé TCP i protokol HTTPS používat protokol SSL pro počáteční ověřování a šifrování přenosu. Pro zajištění nejlepšího výkonu použijte protokol TCP, pokud je to možné.

    Během procesu vytváření instance DocumentClient, který se má parametr ConnectionPolicy nastaven režim připojení. Pokud se používá přímý režim, můžete v rámci má parametr ConnectionPolicy nastavit protokol.

    ```csharp
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    Protože TCP je podporovaný jenom v režimu přímého, pokud se používá režim brány, protokol HTTPS se vždy používá ke komunikaci s bránou a hodnoty protokolu v ConnectionPolicy se ignoruje.

    ![Obrázek připojení zásad služby Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Volání OpenAsync, aby se zabránilo latenci při spuštění na první požadavek**

    Ve výchozím nastavení první požadavek má vyšší latence, protože má k načtení tabulky směrování adres. Aby se zabránilo tato čekací doba spuštění na první žádost, měli byste zavolat OpenAsync() jednou během inicializace následujícím způsobem.

        await client.OpenAsync();
   <a id="same-region"></a>
3. **Společné umístění klienty ve stejné oblasti Azure pro výkon**

    Pokud je to možné, umístěte všechny aplikace, volání služby Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos DB. Pro porovnávání přibližné volání do služby Azure Cosmos DB v rámci stejné oblasti dokončena do 1 až 2 ms, ale latence mezi západním a východním pobřežím USA je > 50 ms. Latence může žádost z požadavku pravděpodobně lišit v závislosti na trasu v požadavku během z klienta na hranici datového centra Azure. Nejnižší možnou latenci se dosahuje tím, že zajišťuje, že je volající aplikace umístěné ve stejné oblasti Azure jako koncový bod zřízené služby Azure Cosmos DB. Seznam dostupných oblastí naleznete v tématu [oblastí Azure](https://azure.microsoft.com/regions/#services).

    ![Obrázek připojení zásad služby Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Zvyšte počet vláken nebo úloh**

    Od volání do služby Azure Cosmos DB v síti, budete muset lišit stupně paralelního zpracování žádostí o tak, aby klientská aplikace stráví velmi málo dobu čekání mezi požadavky. Pokud například používáte. NET společnosti [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx), vytvořte v pořadí 100s úlohy čtení a zápis do služby Azure Cosmos DB.

## <a name="sdk-usage"></a>Použití sady SDK
1. **Nainstalujte nejnovější sadu SDK**

    Se sadami SDK služby Azure Cosmos DB se neustále vylepšují pro zajištění nejlepšího výkonu. Najdete v článku [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) stránky pro určení nejnovější sady SDK a zkontrolujte vylepšení.
2. **Použití klienta služby Azure Cosmos DB jednotlivý prvek po dobu životnosti vaší aplikace**

    Každá instance DocumentClient je bezpečná pro vlákno a provádí připojení efektivní správu a ukládání adres do mezipaměti při fungování v přímém režimu. Povolit správu efektivní připojení a lepší výkon pomocí DocumentClient, doporučujeme použít jednu instanci DocumentClient na doménu aplikace po dobu životnosti aplikace.

   <a id="max-connection"></a>
3. **Zvýšit System.Net MaxConnections na hostitele, když používáte režim brány**

    Azure Cosmos DB požadavků probíhají přes protokol HTTPS nebo rozhraní REST, když používáte režim brány a jsou vystaveny výchozí limit pro připojení za název hostitele nebo IP adresu. Budete muset nastavit MaxConnections na vyšší hodnotu (100-1000) tak, aby Klientská knihovna může využívat více současných připojení ke službě Azure Cosmos DB. V sadě .NET SDK 1.8.0 a nad výchozí hodnotu pro [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) je 50 a ke změně hodnoty, můžete nastavit [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)na vyšší hodnotu.   
4. **Ladění používejte u dělených kolekcí paralelní dotazy**

     SQL SDK pro .NET verze 1.9.0 a novější paralelní dotazy podpory, které vám umožní dotazovat do dělené kolekce paralelně. Další informace najdete v tématu [ukázky kódu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) týkající se práce spolu se sadami SDK. Paralelní dotazy jsou navržené ke zlepšení latence dotazu a propustnost nad jejich protějšky sériového portu. Paralelní dotazy poskytují dva parametry, které můžou uživatelé naladit k jejich požadavky (a) z MaxDegreeOfParallelism odpovídal: maximální počet oddílů pak může být dotazována v paralelních úloh a (b) MaxBufferedItemCount ovládací prvek: řídit počet předem počet získaných výsledků.

    (a) ***ladění z MaxDegreeOfParallelism\:***  paralelní dotaz funguje dotazováním více oddílů souběžně. Data z jednotlivých oddílů shromažďování je však načíst sériově s ohledem na dotaz. Ano nastavení z MaxDegreeOfParallelism na počtu oddílů má maximální příležitost dosáhnout většina výkonných dotazů, zadaná všech ostatních situacích, systém zůstávají stejné. Pokud si nejste jisti počet oddílů, můžete nastavit z MaxDegreeOfParallelism na velký počet a systém zvolí jako z MaxDegreeOfParallelism minimální (počet oddílů, vstup uživatele, které jsou k dispozici).

    Je důležité si uvědomit, že paralelní dotazy vytvořit nejlepší výhody, pokud je data rovnoměrně rozdělené mezi všechny oddíly s ohledem na dotaz. Pokud dělené kolekce je rozdělit na oddíly tak, že se soustřeďuje všechny nebo většina dat vrácených dotazem v několika oddílech (jeden oddíl v nejhorším případě) a pak výkon dotazu by bottlenecked podle těchto oddílů.

    (b) ***ladění MaxBufferedItemCount\:***  paralelní dotaz byl navržen k předběžného načítání výsledků během zpracování dávky aktuální výsledky klientem. Předběžné načítání pomáhá zvyšování celkové latence dotazu. MaxBufferedItemCount je parametr lze omezit, předem počet získaných výsledků. Nastavení MaxBufferedItemCount očekávaný počet výsledků vrácených (nebo vyššího čísla) umožňuje dotazu pro příjem maximální výhody z předběžného načítání.

    Předběžné načítání funguje stejným způsobem bez ohledu na to, z MaxDegreeOfParallelism a jednu vyrovnávací paměti pro data ze všech oddílů.  
5. **Zapnout uvolňování paměti na straně serveru**

    V některých případech může pomoct snížení frekvence uvolňování paměti. V rozhraní .NET, nastavte [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) na hodnotu true.
6. **Implementace omezení rychlosti intervalech RetryAfter**

    Během testování výkonnosti, měli byste zvýšit zatížení dokud malý počet žádostí o získání omezené. Když omezený, klientská aplikace má omezení rychlosti na omezení pro interval opakování zadaný server. Respektování omezení rychlosti zajistí, že strávíte co nejkratším čase čekat mezi opakovanými pokusy. Podpora zásad opakování je součástí verze 1.8.0 a nad SQL [.NET](sql-api-sdk-dotnet.md) a [Java](sql-api-sdk-java.md), verze 1.9.0 a novější z [Node.js](sql-api-sdk-node.md) a [Python](sql-api-sdk-python.md), a všechny podporované verze [.NET Core](sql-api-sdk-dotnet-core.md) sady SDK. Další informace najdete [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    S verzí 1.19 a novější sady .NET SDK je mechanismus pro protokolování dalších diagnostických informací a řešit problémy s latencí, jak je znázorněno v následujícím příkladu. Můžete protokolovat diagnostické řetězec pro požadavky, které mají vyšší latence čtení. Zachycený diagnostických řetězec vám pomůže pochopit, kolikrát jste zaznamenali 429s pro daný požadavek.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Horizontální navýšení kapacity vašich úloh klienta**

    Pokud testujete při vysoké propustnosti (> 50 000 RU/s), aplikace klienta se může stát kritickým bodem kvůli počítač omezení navýšení kapacity na využití procesoru nebo sítě. Pokud překročíte tento bod, můžete nadále push další účet Azure Cosmos DB díky horizontálnímu navýšení kapacity klientských aplikací napříč několika servery.
8. **Nižší latence čtení dokumentu mezipaměti identifikátorů URI**

    Dokument mezipaměti identifikátorů URI, kdykoli je to možné, pro zajištění nejlepšího výkonu pro čtení. Je nutné definovat logiku pro ukládání do mezipaměti ID prostředku, když vytvoříte prostředek. Vyhledávání podle ID prostředku jsou rychlejší než vyhledávání podle názvu, takže tyto hodnoty ukládání do mezipaměti zvyšuje výkon. 

   <a id="tune-page-size"></a>
1. **Vyladěním stránek pro informační kanály pro zajištění lepšího výkonu dotazů/čtení**

    Při provádění hromadné čtení dokumentů pomocí funkce (například ReadDocumentFeedAsync) informační kanál čtení nebo při vydání příkazu jazyka SQL, výsledky se vrátí segmentovaným způsobem, pokud sada výsledků je příliš velký. Ve výchozím nastavení výsledky jsou vráceny v blocích 100 položek nebo 1 MB, podle omezení dosáhnete první.

    Abyste snížili počet sítě zaokrouhlit zkracuje dobu odezvy potřebný k načtení všech platných výsledky, můžete zvýšit velikost stránky pomocí [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hlavičku požadavku na až 1000. V případech, kdy potřebujete zobrazit jenom pár výsledky například pokud vaše uživatelské rozhraní nebo aplikací rozhraní API vrátí jenom 10 výsledky čas, může také snížit velikost stránky na 10 ke snížení propustnosti využité pro dotazy a čtení.

    Můžete také nastavit velikost stránky pomocí dostupné sady SDK Azure Cosmos DB.  Příklad:

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Zvyšte počet vláken nebo úloh**

    Zobrazit [zvýšit počet vláken nebo úloh](#increase-threads) v části sítě.

11. **Použijte hostitele 64bitové zpracování**

    Sada SQL SDK funguje v 32-bit hostitelský proces, při použití SQL SDK pro .NET verze 1.11.4 a vyšší. Pokud používáte dotazy napříč oddílu, 64bitový hostitelský zpracování doporučuje za účelem vylepšení výkonu. Následující typy aplikací mají 32-bit hostitelský proces jako výchozí, tak, aby bylo možné změnit na 64-bit, postupujte podle těchto kroků podle typu aplikace:

    - Pro spustitelný soubor aplikace, to lze provést zrušením **preferovat 32bitovou verzi** možnost **vlastnosti projektu** okno na **sestavení** kartu.

    - Pro VSTest založené projekty testů, to můžete udělat tak, že vyberete **testování**->**nastavení testu**->**výchozí architektura procesoru jako X64**, z **Visual Studio Test** nabídky.

    - Pro místně nasazené webové aplikace ASP.NET, to můžete udělat tak, že zkontrolujete **použít 64bitovou verzi služby IIS Express pro weby a projekty**v části **nástroje**->**možnosti**  -> **Projekty a řešení**->**webových projektů**.

    - Pro webové aplikace ASP.NET nasazené v Azure, to lze provést výběrem **platformy jako 64-bit** v **nastavení aplikace** na portálu Azure portal.

## <a name="indexing-policy"></a>Zásada indexování
 
1. **Vyloučit cesty nevyužité indexování pro rychlejší zápisy**

    Zásady indexování cosmos DB můžete také zadat které dokumentu cesty pro zahrnutí nebo vyloučení z indexování s využitím indexování cesty (IndexingPolicy.IncludedPaths a IndexingPolicy.ExcludedPaths). Použití indexování cesty může nabídnout zápisu lepší výkon a dolní index úložiště pro scénáře, ve kterých vzory dotazů znám předem, jako jsou indexování náklady přímo korelační počtu jedinečné cesty indexované.  Například následující kód ukazuje, jak vyloučit celý oddíl dokumenty (označovaný také jako podstrom) z indexování pomocí "*" zástupný znak.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Další informace najdete v tématu [zásadám indexování služby Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

1. **Měření a ladění pro nižší požadavek jednotek za sekundu využití**

    Azure Cosmos DB nabízí bohatou sadu databázových operací včetně relačních a hierarchických dotazů s uživatelem definovanými funkcemi, uložených procedur a aktivačních událostí. všechno funguje s dokumenty v databázové kolekci. Náklady spojené s každou z těchto operací se liší v závislosti na využití procesoru, vstup/výstup a paměti potřebných k dokončení operace. Místo přemýšlet o tom a správy hardwarových prostředků můžete jako jedno opatření pro prostředky požadované k provádění různých databázových operací a plnění požadavků aplikace představit jednotky žádosti (RU).

    Zřízení propustnosti na základě počtu [jednotky žádostí](request-units.md) nastavit pro každý kontejner. Spotřeba jednotek žádosti se vyhodnotí jako sazba za sekundu. Aplikace, které překračují jednotka frekvence zřízené požadavků na své kontejnery jsou omezené, dokud rychlost klesne pod úroveň zřízené pro kontejner. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit propustnost zřizování další jednotky žádostí. 

    Složitost dotazu má vliv na tom, kolik jednotek žádostí se spotřebovávají pro operaci. Počet predikátů, povaze predikáty, počet funkcí UDF a velikost zdrojové datové sady všech ovlivnit náklady na operace dotazů.

    K měření nároky na jakékoli operace (vytvoření, aktualizace nebo odstranění), zkontrolujte [x-ms žádost poplatek](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) záhlaví (nebo ekvivalentní RequestCharge vlastnost ResourceResponse<T> nebo FeedResponse<T> v. NET SDK) k měření počtu jednotek žádosti spotřebovaná těchto operací.

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

    Vrácená v hlavičce tohoto platí požadavek se zlomek zřízená propustnost (například 2000 ru / s). Například pokud předchozí dotaz vrací 1000 1KB – dokumenty, náklady na operace je 1000. V rámci jedné sekundy, v důsledku toho serveru respektuje pouze dva takové požadavky před tady následných žádostí. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek žádosti](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Zpracování frekvence omezení/požadavků míra příliš velká**

    Když se klient pokusí překročí vyhrazené propustnosti pro účet, neexistuje žádné snížení výkonu na serveru a používat sady kapacitou propustnosti mimo úroveň rezervované. Server preventivně ukončení požadavku s RequestRateTooLarge (kód stavu HTTP 429), který se vrátit [x-ms opakování po ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) záhlaví určující dobu v milisekundách, které musí uživatel čekat před opakováním požadavek.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Sady SDK všechny implicitně zachytit tuto odpověď, respektují zadaný server hlavičkou retry-after a opakujte žádost. Pokud váš účet je současně přistupuje více klientů, bude při dalším pokusu úspěšné.

    Pokud máte více než jednoho klienta kumulativně provozní konzistentně výše je frekvence požadavků, nemusí stačit výchozí počet opakování aktuálně nastavená na 9 interně klientem; v tomto případě klient vyvolá DocumentClientException se stavovým kódem 429 do aplikace. Výchozí počet opakování můžete změnit tak, že nastavíte RetryOptions ConnectionPolicy instance. Ve výchozím nastavení je vrácena DocumentClientException se stavovým kódem 429 po uplynutí určité doby kumulativní Počkejte 30 sekund, pokud požadavek dál pracovat nad frekvence požadavků. K tomu dojde i v případě aktuální počet opakování je menší než počet opakování, jde o výchozí hodnotu 9 nebo uživatelem definovanou hodnotu.

    Při automatické opakování chování pomáhá zlepšit odolnost proti chybám a použitelnost pro většinu aplikací, zřejmě na odds při provádění srovnávací testy výkonu, zejména v případě měření latence.  Klienta pozorované latence se dosahuje, pokud experiment narazí na server omezení a způsobí, že klientská sada SDK tiše opakování. Aby se zabránilo latence dosahující během výkonu experimenty, měřit poplatek vrácený každou operaci a ujistěte se, jsou požadavky funguje pod sazbu žádost o rezervovanou. Další informace najdete v tématu [jednotky žádostí](request-units.md).
3. **Návrh pro zajištění vyšší propustnost menších dokumentů**

    Zátěž žádostí (tj. zpracování požadavku náklady) dané operace jsou korelována přímo velikost dokumentu. Operace na velkých dokumentů nákladů více než operace pro malé dokumenty.

## <a name="next-steps"></a>Další postup
Ukázková aplikace používá k vyhodnocení služby Azure Cosmos DB pro vysoce výkonné scénáře na několika klientských počítačích, najdete v části [testování pomocí služby Azure Cosmos DB výkonu a škálování](performance-testing.md).

Další informace o návrhu vaší aplikace pro škálování a vysoký výkon, viz také [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).
