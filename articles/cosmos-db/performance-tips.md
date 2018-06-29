---
title: Azure Cosmos DB tipy pro zvýšení výkonu pro rozhraní .NET | Microsoft Docs
description: Další možnosti konfigurace klienta ke zlepšení výkonu databáze Azure Cosmos DB
keywords: tom, jak zvýšit výkon databáze
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: sngun
ms.openlocfilehash: 9418525e60f255787f39a42657ee0dbdbd46957d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096929"
---
> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Tipy pro zvýšení výkonu pro Azure Cosmos DB a rozhraní .NET

Azure Cosmos DB je rychlé a flexibilní distribuovanou databázi, která bezproblémově škáluje s zaručenou latence a propustnosti. Není nutné provádět změny hlavní architektura nebo zapsat složitý kód se škálovat databázi Azure Cosmos DB. Škálování nahoru a dolů je stejně snadná jako provedení jednoho volání rozhraní API nebo [volání metody SDK](set-throughput.md#set-throughput-sdk). Protože Azure Cosmos DB přistupuje prostřednictvím volání sítě jsou ale optimalizace na straně klienta můžete provést dosáhnout špičkový výkon při použití [SQL .NET SDK](documentdb-sdk-dotnet.md).

Takže pokud vás nemůže ověřit "jak vylepšit výkon Moje databáze?" Zvažte následující možnosti:

## <a name="networking"></a>Sítě
<a id="direct-connection"></a>

1. **Zásady připojení: použití režimu přímé připojení**

    Jak se klient připojí k databázi Cosmos Azure má důležité dopady na výkon, hlavně z hlediska zjištěnou latence klienta. Nejsou k dispozici pro konfiguraci klienta zásady připojení – připojení dvě nastavení konfigurace klíče *režimu* a [připojení *protokol*](#connection-protocol).  Jsou k dispozici dva režimy:

   * Režim brány (výchozí)
      
     Režim brány je podporovaná na všech platformách SDK a je nakonfigurované výchozí. Pokud vaše aplikace běží v rámci podnikové sítě s omezeními striktní brány firewall, režimu brány je nejlepší volbou, protože používá standardní port HTTPS a jeden koncový bod. Kompromis výkonu, je ale, že brány režimu zahrnuje směrování další síti pokaždé, když je číst nebo zapisovat do databáze Cosmos Azure data. Z toho důvodu přímý režim nabízí lepší výkon z důvodu méně síťové směrování.

   * Přímý režim

     Přímý režim podporuje připojení prostřednictvím protokolů TCP a HTTPS. V současné době direct je podporována v rozhraní .NET 2.0 standardní pro platformu Windows jenom. Při použití režimu přímého, existují dvě možnosti protokolu k dispozici:

    * TCP
    * HTTPS

    Při použití režimu brány, Azure Cosmos DB používá port 443 a MongoDB API používá 10250, 10255 a 10256 porty. Mapy 10250 portu do výchozí instance Mongodb bez geografická replikace a porty 10255/10256 mapovat do instance Mongodb s geografická replikace funkce. Při použití protokolu TCP v režimu přímého kromě portů brány, je potřeba zajistit port rozsahu 10000 až 20000 je otevřený, protože Azure Cosmos DB používá dynamické porty TCP. Pokud nejsou tyto porty otevřít a pokus o použití protokolu TCP, obdržíte chybu 503 Služba není k dispozici. Následující tabulka uvádí připojení režimy, které jsou k dispozici pro jiné rozhraní API a porty uživatele služby pro každé rozhraní API:

    |Režim připojení  |Podporovaných protokolů  |Podporované sady SDK  |Port rozhraní API/služby  |
    |---------|---------|---------|---------|
    |brána  |   HTTPS    |  Všechny sady SDK    |   SQL(443) Mongo (10250, 10255, 10256), Table(443), Cassandra(443), Graph(443)    |
    |Přímé    |    HTTPS     |  Rozhraní .net a Java SDK    |    SQL(443)   |
    |Přímé    |     TCP    |  .NET SDK    | Porty v rozsahu 20 10 000 000 |

    Azure Cosmos DB nabízí jednoduché a otevřete RESTful programovací model přes protokol HTTPS. Kromě toho nabízí efektivní protokolu TCP, který je také dosáhl standardu RESTful v jeho komunikace modelu a je k dispozici prostřednictvím klient .NET SDK. Přímé TCP a HTTPS používat protokol SSL pro počáteční ověřování a šifrování přenosů. Pro nejlepší výkon použijte protokol TCP, pokud je to možné.

    Režim připojení je nakonfigurovat při vytváření instance DocumentClient s parametrem ConnectionPolicy. Pokud se používá přímý režim, můžete v rámci parametr ConnectionPolicy nastavit protokol.

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

    Protože TCP je podporována pouze v režimu přímého, pokud se používá v režimu brány, vždy používá protokol HTTPS ke komunikaci s bránou a hodnota protokolu v ConnectionPolicy je ignorována.

    ![Obrázek připojení zásad Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Volání OpenAsync, aby se zabránilo zpoždění při spuštění na první požadavek**

    Ve výchozím nastavení první požadavek má vyšší latence, protože má načíst tabulky směrování adres. Abyste se vyhnuli tato čekací doba spuštění na první žádost, měli byste zavolat OpenAsync() jednou během inicializace následujícím způsobem.

        await client.OpenAsync();
   <a id="same-region"></a>
3. **Společně umístit klienty ve stejné oblasti Azure výkonu**

    Pokud je to možné, umístěte všechny aplikace, volání Azure Cosmos DB ve stejné oblasti jako databázi Azure Cosmos DB. Přibližná porovnání najdete dokončí volání do databáze Cosmos Azure v rámci stejné oblasti v rámci ms 1 – 2, ale je latence mezi – západ a východním pobřeží USA > 50 ms. Tato čekací doba můžete z požadavku na žádost pravděpodobně lišit v závislosti na směrování žádosti jako předává z klienta na hranici datového centra Azure. Nejnižší možnou latenci je dosaženo tím zajistíte, že je volající aplikace se nachází v rámci stejné oblasti Azure jako zřízené koncový bod Azure Cosmos DB. Seznam dostupných oblastí najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

    ![Obrázek připojení zásad Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Zvýšit počet vláken/úlohy**

    Vzhledem k tomu, že k databázi Azure Cosmos volání přes síť, musíte ke změně stupně paralelního zpracování žádostí o tak, aby klientská aplikace stráví velmi malé dobu čekání mezi požadavky. Pokud například používáte. NET na [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx), vytvořte v pořadí 100s úloh čtení nebo zápisu do databáze Azure Cosmos.

## <a name="sdk-usage"></a>Použití sady SDK
1. **Nainstalujte nejnovější sadu SDK**

    Sady SDK Azure Cosmos DB neustále probíhá lepší zajistit optimální výkon. Najdete v článku [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) stránky, abyste zjistili nejnovější SDK a zkontrolujte vylepšení.
2. **Pomocí klienta Azure Cosmos DB singleton po dobu jeho existence vaší aplikace.**

    Každá instance DocumentClient je bezpečné pro přístup z více vláken a provádí správu efektivní připojení a ukládání adres do mezipaměti v režimu přímého. Povolit správu efektivní připojení a lepší výkon pomocí DocumentClient, doporučujeme použít jednu instanci DocumentClient jednotlivé domény aplikace po dobu jeho existence aplikace.

   <a id="max-connection"></a>
3. **Při použití režimu brány zvýšit System.Net MaxConnections na hostitele**

    Azure Cosmos DB požadavky jsou při použití režimu brány proveden prostřednictvím protokolu HTTPS nebo REST a jsou vystaveny výchozí limit připojení pro hostitele nebo IP adresa. Musíte nastavit MaxConnections na vyšší hodnotu (100-1000) tak, aby klientské knihovny může využívat víc souběžných připojení k databázi Azure Cosmos. V sadě SDK .NET 1.8.0 a vyšší, výchozí hodnota pro [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) je 50 a chcete-li změnit hodnotu, můžete nastavit [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)na vyšší hodnotu.   
4. **Ladění paralelní dotazy pro dělené kolekce**

     SQL .NET SDK verze 1.9.0 a vyšší paralelní dotazy podpory, které vám umožní dotazovat dělenou kolekci paralelně (najdete v části [práce sady SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) a související [ukázky kódu jsou](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) Další informace). Paralelní dotazy jsou navrženy pro zlepšit latenci dotazů a propustnosti na jejich sériové protějšku. Paralelní dotazy poskytují dva parametry, které uživatelé mohli vyladit vlastní-přizpůsobit jejich požadavků (a) MaxDegreeOfParallelism: řídit maximální počet oddílů pak může být dotazována v paralelní a (b) MaxBufferedItemCount: k řízení počet předem načtených výsledky.

    (a) ***ladění MaxDegreeOfParallelism\:***  paralelní dotaz funguje dotazováním více oddílů souběžně. Data z shromažďování jednotlivých oddílů je však načtených sériově s ohledem na dotaz. Ano nastavení MaxDegreeOfParallelism na počet oddílů má maximální riziko dosažení většina původce dotazu, zadaná jinými podmínkami systému zůstávají stejné. Pokud si nejste jisti počet oddílů, můžete nastavit MaxDegreeOfParallelism na vysoké číslo, a systém zvolí minimální (počet oddílů, uživatel zadal vstup) jako MaxDegreeOfParallelism.

    Je důležité si uvědomit, že paralelní dotazy vytvořit nejlepší výhody, pokud data je rovnoměrně rozdělené mezi všechny oddíly s ohledem na dotaz. Pokud dělené kolekce je rozdělena na oddíly tak, že se soustřeďuje všechny nebo většinu dat vrácených dotazem do několika oddílů (jeden oddíl v nejhorším případě), a potom výkonu dotazu by omezené podle těchto oddílů.

    (b) ***ladění MaxBufferedItemCount\:***  paralelního dotazu slouží k předem načíst výsledky během aktuální dávku výsledky zpracování klientem. Předběžné načítání pomáhá v celkového zlepšení latence dotazu. MaxBufferedItemCount je parametr omezit počet předem načtených výsledků. Nastavení MaxBufferedItemCount očekávaný počet výsledků vrácených (nebo vyšším číslem) umožňuje dotazu z předem načítání maximální výhody nezískají.

    Předběžné načítání funguje stejným způsobem bez ohledu MaxDegreeOfParallelism a je do jedné vyrovnávací paměti pro data ze všech oddílů.  
5. **Zapnout na straně serveru globálního katalogu**

    V některých případech mohou pomoci snížit frekvenci uvolňování paměti. V rozhraní .NET, nastavte [gcserver –](https://msdn.microsoft.com/library/ms229357.aspx) na hodnotu true.
6. **Implementace omezení rychlosti v intervalech RetryAfter**

    Během testování výkonu měli zvýšit zatížení, dokud malý počet žádostí o získání omezeny. Pokud omezené, klientská aplikace měli omezení rychlosti na omezení pro interval opakování zadaný server. Bere ohledy omezení rychlosti zajišťuje strávený minimální množství času čekat mezi opakovanými pokusy. Podpora zásad opakování je součástí verze 1.8.0 a výše SQL [rozhraní .NET](sql-api-sdk-dotnet.md) a [Java](sql-api-sdk-java.md), verze 1.9.0 a vyšší z [Node.js](sql-api-sdk-node.md) a [Python](sql-api-sdk-python.md), a všechny podporované verze [.NET Core](sql-api-sdk-dotnet-core.md) sady SDK. Další informace najdete v tématu [Exceeding vyhrazené omezení propustnosti](request-units.md#RequestRateTooLarge) a [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    S verzí 1.19 a novější sady .NET SDK je mechanismus protokolu jiné diagnostické informace a řešení potíží s problémy s latencí jak znázorňuje následující ukázka. Přihlaste se na diagnostiky řetězec pro požadavky, které mají vyšší latence pro čtení. Zaznamenané diagnostiky řetězec vám pomůže porozumět počet, kolikrát zjištěnými 429s pro daný požadavek.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Horizontální navýšení kapacity vaše úlohy klienta**

    Pokud testujete na úrovních vysoké propustnosti (> 50 000 RU/s), klientská aplikace se může stát úzkým místem kvůli počítač omezení se na využití procesoru nebo sítě. Pokud dostanete tento bod, můžete dál tak, aby nabízel další účet Azure Cosmos DB podle škálování klientských aplikací na více serverech.
8. **Dokument mezipaměti identifikátorů URI pro nižší latenci pro čtení**

    Dokument mezipaměti identifikátory URI, kdykoli je to možné nejlepšího výkonu dosáhnete pro čtení.
   <a id="tune-page-size"></a>
9. **Optimalizovat velikost stránky pro dotazy/číst informační kanály pro lepší výkon**

    Při provádění hromadně číst dokumenty pomocí funkce (například ReadDocumentFeedAsync) informační kanál čtení, nebo při vydání příkazu jazyka SQL, budou vráceny výsledky segmentovaným způsobem, pokud sadu výsledků dotazu je příliš velký. Ve výchozím nastavení budou vráceny výsledky v bloky 100 položky nebo 1 MB, z těchto omezení dosáhl první.

    Chcete-li snížit počet sítě zaokrouhlit služebních cest potřebnou k načtení všech platných výsledky, můžete zvýšit velikost stránky pomocí [x-ms-max--počet položek](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hlavička požadavku na až 1000. V případech, kdy je třeba zobrazit jen pár výsledky například pokud vaše uživatelské rozhraní nebo aplikací rozhraní API vrátí jenom 10 výsledků na dobu, může také snížit velikost stránky na 10 ke snížení propustnosti využité pro čtení a dotazy.

    Mohou také nastavit velikost stránky pomocí sady k dispozici DB SDK Azure Cosmos.  Příklad:

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Zvýšit počet vláken/úlohy**

    V tématu [zvýšit počet vláken/úlohy](#increase-threads) v části sítě.

11. **Použijte hostitele 64-bit zpracování**

    Sadu SDK SQL pracuje v 32bitové hostitelský proces, při použití SQL .NET SDK verze 1.11.4 a vyšší. Ale pokud používáte křížové oddílu dotazy, se doporučuje zpracování hostitelem 64-bit pro zlepšení výkonu. Následující typy aplikací mít 32-bit hostitelský proces jako výchozí, tak, aby bylo možné změnit, na 64-bit, postupujte podle těchto kroků na základě typu aplikace:

    - Pro spustitelný soubor aplikace, to můžete provést zrušením zaškrtnutí políčka **raději 32bitové** možnost **vlastnosti projektu** okno na **sestavení** kartě.

    - Pro testovací projekty založené na VSTest, to lze provést výběrem **testování**->**Test nastavení**->**výchozí architekturu procesoru jako X64**, z **testu sady Visual Studio** možnost nabídky.

    - Pro místně nasazené webové aplikace ASP.NET, stačí kontrolou **použít 64bitová verze služby IIS Express pro weby a projekty**v části **nástroje**->**možnosti**  -> **Projekty a řešení**->**webových projektů**.

    - Pro ASP.NET – webové aplikace nasazené na platformě Azure, to lze provést výběrem **platforma jako 64-bit** v **nastavení aplikace** na portálu Azure.

## <a name="indexing-policy"></a>Zásada indexování
 
1. **Vyloučit z indexování pro rychlejší zápisy nepoužívané cesty**

    Zásady indexování cosmos DB společnosti můžete také zadat které cesty dokumentu zahrnout nebo vyloučit z indexování s využitím indexování cesty (IndexingPolicy.IncludedPaths a IndexingPolicy.ExcludedPaths). Použití indexování cesty můžete jako indexování náklady jsou přímo korelační počet jedinečných cesty indexované nabízet zápisu lepší výkon a dolní index úložiště pro scénáře, ve kterých jsou předem, známé typy dotazů.  Například následující kód ukazuje, jak vyloučit celý oddíl dokumentů (také známa jako podstrom) indexování pomocí "*" zástupný znak.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Další informace najdete v tématu [Azure DB Cosmos indexování zásady](indexing-policies.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

1. **Měření a optimalizovat pro nižší požadavek jednotek za sekundu využití**

    Azure Cosmos DB nabízí širokou škálu databázové operace, včetně dotazů na relační a hierarchických UDF, uložené procedury a triggery – všechny provozní s dokumenty v databázi kolekce. Náklady spojené s každou z těchto operací se liší podle využití procesoru, vstupně-výstupní operace a paměť požadovanou pro dokončení operace. Namísto přemýšlení o a správu hardwarové prostředky si můžete představit jednotka žádosti (RU) jako jednu míru pro prostředky potřebné k provádět různé operace databáze a služeb na žádost aplikace.

    Propustnost je zřízený na základě počtu [požadované jednotky](request-units.md) nastavit pro každý kontejner. Spotřeba jednotek žádosti budou vyhodnocené jako za sekundu. Aplikace, které překračují rychlost jednotky zřízené požadavků pro jejich kontejner jsou omezené, dokud rychlost klesne pod úroveň zřízené v kontejneru. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit vaše propustnost zřizování jednotek další žádosti. 

    Složitost dotazu má dopad na tom, kolik jednotek žádosti se spotřebovávají pro operace. Počet predikáty, povaha predikáty, počet UDF a velikost datové sady zdroje, které jsou všechny ovlivnit náklady na operace dotazů.

    K měření režijní náklady na všechny operace (vytvoření, aktualizace nebo odstranění), zkontrolujte [x-ms požadavku poplatků](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) záhlaví (nebo ekvivalentní vlastnost RequestCharge v ResourceResponse<T> nebo FeedResponse<T> v. NET SDK) k měření počtu jednotek žádosti uplatníte tyto operace.

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

    Žádost o poplatků, vrátí se v tuto hlavičku je zlomek zřízené propustnosti (tj, 2000 RUs / druhý). Například pokud předchozí dotaz vrátí 1000 1KB – dokumenty, náklady na operaci je 1 000. V rámci jedné sekundy jako takový serveru ctí jenom dva takových požadavků, než míra omezení následných žádostí. Další informace najdete v tématu [požadované jednotky](request-units.md) a [kalkulačky jednotek žádosti](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Rychlost omezení nebo požadavků popisovač míra příliš velký**

    Když se klient pokusí delší než vyhrazenou propustností pro účet, není bez snížení výkonu na serveru a bez využití kapacity propustnosti mimo úroveň vyhrazené. Server bude ho preventivně ukončit požadavek s RequestRateTooLarge (kód stavu HTTP 429) a vrátit se [x-ms opakování za ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) hlavičky, která určuje množství času v milisekundách, která uživatel musí počkat před provedením nového pokusu požadavek.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Sady SDK všechny implicitně catch této odpovědi, respektují záhlaví zadaný server opakovat po a opakujte žádost. Pokud váš účet je současně přistupuje více klientů, další pokus bude úspěšné.

    Pokud máte více než jednoho klienta kumulativně operační konzistentně vyšší rychlost požadavků, nemusí stačit výchozí počet opakování aktuálně nastavený na hodnotu 9 interně klientem; v tomto případě klient vyvolá DocumentClientException se stavovým kódem 429 k aplikaci. Výchozí počet opakování můžete změnit nastavením RetryOptions ConnectionPolicy instance. Ve výchozím nastavení je DocumentClientException se stavovým kódem 429 vrátila po uplynutí určité doby kumulativní Počkejte 30 sekund, pokud požadavek i nadále fungovat vyšší rychlost požadavků. K tomu dojde i v případě aktuální počet opakování je menší než počet maximálního počtu opakování, je jej výchozí hodnotu 9 nebo hodnotu definovanou uživatelem.

    Při automatické opakování chování pomáhá zlepšovat odolnost proti chybám a použitelnost pro většinu aplikací, zřejmě ve odds při provádění srovnávacích testů výkonu, zejména v případě, že měření latence.  Latence zjištěnými klienta bude špiček Pokud experimentu dotkne omezení serveru a způsobí, že klient SDK bezobslužně opakovat. Abyste se vyhnuli latence špičky během výkonu experimenty, měřit poplatků vrácený jednotlivých operací a ujistěte se, že jsou pod rychlost vyhrazené požadavků operační požadavky. Další informace najdete v tématu [požadované jednotky](request-units.md).
3. **Návrh pro menší dokumenty pro vyšší propustnost**

    Požadavek zdarma (tj. zpracování žádosti náklady) dané operace je korelační přímo na velikost dokumentu. Operace u velkých dokumentů nákladů více než operací pro malé dokumenty.

## <a name="next-steps"></a>Další postup
Ukázkovou aplikaci, používá k vyhodnocení Azure Cosmos DB pro vysoce výkonné scénáře na několik klientských počítačů, najdete v části [výkonu a možností škálování testování pomocí Azure Cosmos DB](performance-testing.md).

Další informace o návrhu aplikace pro škálování a vysoký výkon, viz také [dělení a škálování v Azure Cosmos DB](partition-data.md).
