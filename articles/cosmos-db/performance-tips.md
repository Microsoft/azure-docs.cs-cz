---
title: Tipy pro Azure Cosmos DB výkonu pro .NET SDK v2
description: Seznamte se s možnostmi konfigurace klienta pro zlepšení Azure Cosmos DB výkonu sady .NET v2 SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: e3d6771f841d3a1d403c1c825da3b504b6896d9e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277223"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Tipy pro zvýšení výkonu pro službu Azure Cosmos DB a sadu .NET SDK v2

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Sada Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Sada Async Java SDK v2](performance-tips-async-java.md)
> * [Sada Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která bez problémů škáluje zaručenou latenci a propustnost. Nemusíte dělat zásadní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Vertikální navýšení a snížení kapacity je stejně snadné jako při provádění jediného volání rozhraní API. Další informace najdete v tématu [jak zřídit propustnost kontejneru](how-to-provision-container-throughput.md) nebo [jak zřídit propustnost databáze](how-to-provision-database-throughput.md). Vzhledem k tomu, že k Azure Cosmos DB je k dispozici prostřednictvím síťových volání, existují optimalizace na straně klienta, které vám při použití [sady SQL .NET SDK](sql-api-sdk-dotnet-standard.md)umožní dosáhnout špičkového výkonu.

Pokud se tedy snažíte zvýšit výkon databáze, zvažte tyto možnosti:

## <a name="upgrade-to-the-net-v3-sdk"></a>Upgrade na sadu .NET V3 SDK

[Sada .NET V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) je vydaná. Pokud používáte sadu .NET V3 SDK, přečtěte si [Průvodce výkonem .NET V3](performance-tips-dotnet-sdk-v3-sql.md) , kde najdete následující informace:

- Výchozí hodnota je přímý režim TCP.
- Podpora Stream API
- Podpora vlastního serializátoru pro povolení System.Text.JSpři použití
- Integrovaná dávka a Hromadná podpora

## <a name="hosting-recommendations"></a>Doporučení hostování

**Pro úlohy náročné na dotazy použijte Windows 64, nikoli Linux nebo Windows 32-bit Processing Host.**

Pro zlepšení výkonu doporučujeme zpracování bitového hostitelského systému Windows 64. Sada SQL SDK obsahuje nativní ServiceInterop.dll k analýze a optimalizaci dotazů v místním prostředí. ServiceInterop.dll se podporuje jenom na platformě Windows x64. Pro Linux a jiné nepodporované platformy, kde ServiceInterop.dll není k dispozici, je bráně k získání optimalizovaného dotazu učiněno další síťové volání. Následující typy aplikací používají ve výchozím nastavení 32 hostitelského zpracování. Chcete-li změnit zpracování hostitele na 64, postupujte podle těchto kroků na základě typu vaší aplikace:

- U spustitelných aplikací lze změnit zpracování hostitele nastavením možnosti [cíl platformy](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019&preserve-view=true) na hodnotu **x64**  v okně **Vlastnosti projektu** na kartě **sestavení** .

- U testovacích projektů založených na VSTest můžete změnit zpracování hostitele tak, **Test**že  >  v nabídce Test sady Visual Studio vyberete**možnost nastavení test testu**  >  **výchozí architektura procesoru jako x64** . **Test**

- Pro lokálně nasazené webové aplikace v ASP.NET můžete změnit zpracování hostitele tak, že vyberete možnost **použít 64 verze IIS Express pro weby a projekty** v nabídce **nástroje**  >  **Možnosti**  >  **projekty a řešení**  >  **webové projekty**.

- Pro webové aplikace ASP.NET nasazené v Azure můžete změnit zpracování hostitele tak, že v **nastavení aplikace** v Azure Portal vyberete platformu **64** .

> [!NOTE] 
> Ve výchozím nastavení jsou nové projekty sady Visual Studio nastaveny na **Libovolný procesor**. Doporučujeme, abyste projekt nastavili na **x64** , takže se nepřepne na **x86**. Projekt nastavený na **Libovolný procesor** může snadno přepnout na **x86** , pokud se přidá závislost jenom pro procesory x86.<br/>
> ServiceInterop.dll musí být ve složce, ze které je spuštěna knihovna DLL sady SDK. To by mělo být obavy jenom v případě, že knihovny DLL ručně kopírujete nebo máte vlastní systémy sestavení/nasazení.
    
**Zapnout uvolňování paměti na straně serveru (GC)**

Omezení frekvence uvolňování paměti může v některých případech pomáhat. V rozhraní .NET nastavte [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) na `true` .

**Horizontální navýšení kapacity úlohy klienta**

Pokud testujete na úrovních vysoké propustnosti (více než 50 000 RU/s), může se klientská aplikace stát kritickým bodem, protože počítač capping na využití procesoru nebo sítě. Pokud se dostanou k tomuto bodu, můžete dál nasdílet Azure Cosmos DB účet tím, že navedete horizontální navýšení kapacity klientských aplikací napříč více servery.

> [!NOTE] 
> Vysoké využití procesoru může způsobit zvýšení latence a vypršení časového limitu požadavku.

## <a name="networking"></a><a id="networking"></a> Sítě

**Zásady připojení: použít přímý režim připojení**

Výchozím režimem připojení sady .NET v2 SDK je brána. Režim připojení můžete nakonfigurovat během vytváření `DocumentClient` instance pomocí `ConnectionPolicy` parametru. Použijete-li přímý režim, je nutné také nastavit pomocí `Protocol` `ConnectionPolicy` parametru. Další informace o různých možnostech připojení najdete v článku [režimy připojení](sql-sdk-connection-modes.md) .

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**Vyčerpání dočasných portů**

Pokud se na instancích zobrazí velký objem připojení nebo vysoké využití portů, ověřte nejprve, zda jsou klientské instance typu singleton. Jinými slovy, instance klientů by měly být pro celou dobu života aplikace jedinečné.

Při spuštění v protokolu TCP se klient optimalizuje kvůli latenci pomocí dlouhotrvajících připojení na rozdíl od protokolu HTTPS, který ukončí připojení po 2 minutách nečinnosti.

Ve scénářích, kde máte zhuštěný přístup a pokud si všimnete vyššího počtu připojení v porovnání s přístupem k režimu brány, můžete:

* Nakonfigurujte vlastnost [ConnectionPolicy. PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) na `PrivatePortPool` (platí pro rozhraní Framework Version>= 4.6.1 a .net Core verze >= 2,0): Tato vlastnost umožňuje, aby sada SDK používala malý fond dočasných portů pro různé Azure Cosmos DB cílové koncové body.
* Nakonfigurujte vlastnost [ConnectionPolicy. IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) musí být větší než nebo rovna 10 minutám. Doporučené hodnoty jsou mezi 20 minutami a 24 hodinami.

**Zavolejte OpenAsync, aby se zamezilo latenci při spuštění prvního požadavku.**

Ve výchozím nastavení má první požadavek větší latenci, protože potřebuje načíst směrovací tabulku adres. Při použití [sady SDK v2](sql-api-sdk-dotnet.md)zavolejte při `OpenAsync()` inicializaci jedenkrát, aby se předešlo této latenci při spuštění prvního požadavku. Volání vypadá takto: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` vygeneruje žádosti o získání směrovací tabulky adres pro všechny kontejnery v účtu. Pro účty, které mají mnoho kontejnerů, ale jejichž aplikace přistupuje k podmnožině, `OpenAsync` by vygenerovala zbytečný objem provozu, což by způsobilo pomalé inicializaci. Použití `OpenAsync` nemusí být v tomto scénáři užitečné, protože zpomaluje spuštění aplikace.

**Pro výkon společné umístění klienty ve stejné oblasti Azure**

Pokud je to možné, umístěte všechny aplikace, které volají Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos DB. Toto je přibližné porovnání: volání Azure Cosmos DB v rámci stejné oblasti se dokončila v rozmezí od 1 do 2 MS, ale latence mezi západním a východním pobřežím USA je větší než 50 ms. Tato latence se může lišit od požadavku na vyžádání v závislosti na trasách, kterou požadavek prochází z klienta na hranici datacentra Azure. Nejnižší možnou latenci získáte tak, že zajistíte, aby se volající aplikace nacházela ve stejné oblasti Azure jako koncový bod zřízené Azure Cosmos DB. Seznam oblastí, které jsou k dispozici, najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Zásady připojení Azure Cosmos DB" border="false":::

**Zvýšení počtu vláken/úloh**
<a id="increase-threads"></a>

Vzhledem k tomu, že volání Azure Cosmos DB jsou provedena přes síť, možná budete muset změnit stupeň paralelismu požadavků, aby klientská aplikace strávila minimální dobu čekání mezi požadavky. Pokud například používáte [úlohu .NET Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx), vytvořte si pořadí stovky úloh, které čtou nebo zapisují do Azure Cosmos DB.

**Povolit akcelerované síťové služby**
 
Pro snížení latence a kolísání procesoru doporučujeme povolit akcelerované síťové služby na virtuálních počítačích klienta. Přečtěte si článek [Vytvoření virtuálního počítače s Windows s akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-powershell.md) službami nebo [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-cli.md)službami.

## <a name="sdk-usage"></a>Využití sady SDK

**Nainstalovat nejnovější sadu SDK**

Sady Azure Cosmos DB SDK se neustále zdokonalují, aby poskytovaly nejlepší výkon. Pokud chcete zjistit nejnovější sadu SDK a zkontrolovat vylepšení, podívejte se na stránky [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) .

**Použití typu Singleton Azure Cosmos DB klienta po dobu života vaší aplikace**

Každá `DocumentClient` instance je bezpečná pro přístup z více vláken a při provozu v přímém režimu provádí efektivní správu připojení a ukládání adres do mezipaměti. K zajištění efektivní správy připojení a lepšího výkonu klienta sady SDK doporučujeme, abyste `AppDomain` pro celou dobu života aplikace použili jednu instanci.

**Při použití režimu brány zvýšit System.Net MaxConnections na hostitele**

Při použití režimu brány se požadavky na Azure Cosmos DB zavedou přes HTTPS/REST. Vztahují se na výchozí limit počtu připojení na název hostitele nebo IP adresu. Možná budete muset nastavit `MaxConnections` na vyšší hodnotu (100 až 1 000), aby Klientská knihovna mohla použít více souběžných připojení k Azure Cosmos DB. V sadě .NET SDK 1.8.0 a novější je výchozí hodnota pro [Třída ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50. Chcete-li změnit hodnotu, můžete nastavit [Documents. Client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) na vyšší hodnotu.

**Ladění paralelních dotazů pro dělené kolekce**

Sada SQL .NET SDK 1.9.0 a novější podporuje paralelní dotazy, které umožňují paralelní dotazování rozdělené kolekce. Další informace najdete v tématu [ukázky kódu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) týkající se práce se sadami SDK. Paralelní dotazy jsou navržené tak, aby poskytovaly lepší latenci a propustnost dotazů než jejich sériové protějšky. Paralelní dotazy poskytují dva parametry, které můžete ladit podle svých požadavků: 
- `MaxDegreeOfParallelism` Určuje maximální počet oddílů, které lze dotazovat paralelně. 
- `MaxBufferedItemCount` Určuje počet předběžně načtených výsledků.

***Stupeň optimalizace paralelismu***

Paralelní dotaz funguje paralelně dotazování na více oddílů. Data z jednotlivého oddílu se ale v souvislosti s dotazem načítají sériově. Nastavení `MaxDegreeOfParallelism` v [sadě SDK v2](sql-api-sdk-dotnet.md) na počet oddílů má největší šanci na dosažení nejvíce výkonného dotazu, za předpokladu, že všechny ostatní systémové podmínky zůstávají stejné. Pokud neznáte počet oddílů, můžete nastavit úroveň paralelismu na vysoké číslo. V systému se jako stupeň paralelismu zvolí minimální počet oddílů, uživatelem zadaný vstup.

Paralelní dotazy poskytují nejvíc výhod, pokud jsou data rovnoměrně rozložena napříč všemi oddíly s ohledem na dotaz. Pokud je dělená kolekce rozdělená tak, aby všechna nebo většinu dat vrácených dotazem byla soustředěna v několika oddílech (jeden z nich je v nejhorším případě), tyto oddíly budou mít kritický vliv na výkon dotazu.

***Vyladění MaxBufferedItemCount***
    
Paralelní dotaz je navržený tak, aby byly výsledky předem načteny, zatímco aktuální dávka výsledků je zpracovávána klientem. Toto předběžné načítání pomáhá zlepšit celkovou latenci dotazu. `MaxBufferedItemCount`Parametr omezuje počet předběžně načtených výsledků. Nastavte `MaxBufferedItemCount` na očekávaný počet vrácených výsledků (nebo vyšší číslo), aby dotaz mohl získat maximální přínos před načtením.

Předběžné načítání funguje stejným způsobem bez ohledu na stupeň paralelismu a existuje jedna vyrovnávací paměť pro data ze všech oddílů.  

**Implementace omezení rychlosti v intervalech RetryAfter**

Během testování výkonu byste měli zvýšit zatížení, dokud se neomezí malá míra požadavků. Pokud jsou požadavky omezené, klientská aplikace by se měla v případě intervalu opakování zadaného serverem vypnout na omezovači. Respektování omezení rychlosti zajišťuje minimální dobu čekání mezi opakovanými pokusy. 

Podpora zásad opakování je součástí těchto sad SDK:
- Verze 1.8.0 a novější sady [.NET SDK pro SQL](sql-api-sdk-dotnet.md) a [Java SDK pro SQL](sql-api-sdk-java.md)
- Verze 1.9.0 a novější sady [Node.js SDK pro SQL](sql-api-sdk-node.md) a [Python SDK pro SQL](sql-api-sdk-python.md)
- Všechny podporované verze sady [.NET Core](sql-api-sdk-dotnet-core.md) SDK 

Další informace najdete v tématu [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
Ve verzi 1,19 a novější sadě .NET SDK je k dispozici mechanismus pro protokolování dalších diagnostických informací a potíží s latencí při řešení problémů, jak je znázorněno v následující ukázce. Můžete protokolovat diagnostický řetězec pro požadavky, které mají vyšší latenci čtení. Zachycený diagnostický řetězec vám pomůže pochopit, kolikrát jste v dané žádosti obdrželi chyby 429.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Mezipaměť identifikátorů URI dokumentů pro nižší latenci čtení**

Ukládání identifikátorů URI dokumentů mezipaměti, kdykoli je to možné, pro nejlepší výkon čtení. Při vytváření prostředku musíte definovat logiku pro ukládání ID prostředku do mezipaměti. Vyhledávání založená na ID prostředků jsou rychlejší než vyhledávání na základě názvů, takže ukládání těchto hodnot do mezipaměti vylepšuje výkon.

**Vyladění velikosti stránek pro dotazy a kanály pro čtení pro lepší výkon**

Když provádíte hromadnou čtení dokumentů pomocí funkce informačního kanálu pro čtení (například `ReadDocumentFeedAsync` ) nebo když vydáte dotaz SQL, výsledky se vrátí segmenticky, pokud je sada výsledků příliš velká. Ve výchozím nastavení se výsledky vrátí do bloků 100 položek nebo 1 MB, podle toho, který limit se narazí jako první.

Chcete-li snížit počet síťových přenosů potřebných k načtení všech použitelných výsledků, můžete zvětšit velikost stránky pomocí [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) pro vyžádání až 1 000 hlaviček. Pokud potřebujete zobrazit jenom pár výsledků, například pokud vaše uživatelské rozhraní nebo rozhraní API pro aplikace vrátí jenom 10 výsledků najednou, můžete také zmenšit velikost stránky na 10 a snížit tak propustnost, která se pro čtení a dotazy spotřebují.

> [!NOTE] 
> `maxItemCount`Vlastnost by se neměla používat jenom pro stránkování. Jeho hlavním použitím je zvýšit výkon dotazů omezením maximálního počtu položek vrácených na jednu stránku.  

Velikost stránky můžete nastavit také pomocí dostupných Azure Cosmos DB sad SDK. Vlastnost [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet&preserve-view=true) v `FeedOptions` umožňuje nastavit maximální počet položek, které mají být vráceny v rámci operace výčtu. Když `maxItemCount` je nastavená hodnota-1, sada SDK automaticky vyhledá optimální hodnotu v závislosti na velikosti dokumentu. Příklad:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Při spuštění dotazu jsou výsledná data odeslána v rámci paketu TCP. Pokud zadáte příliš nízkou hodnotu pro `maxItemCount` , je počet cest potřebných k odeslání dat v rámci paketu TCP vysoký, což má vliv na výkon. Takže pokud si nejste jistí, jakou hodnotu má `maxItemCount` vlastnost nastavit, je nejlepší ji nastavit na-1 a nechat sadu SDK, aby zvolila výchozí hodnotu.

**Zvýšení počtu vláken/úloh**

Podívejte [se na téma zvýšení počtu vláken/úloh](#increase-threads) v části sítě v tomto článku.

## <a name="indexing-policy"></a>Zásada indexování
 
**Vyloučení nepoužívaných cest z indexování za účelem zrychlení zápisu**

Zásada indexování Azure Cosmos DB také umožňuje určit, které cesty dokumentů zahrnout nebo vyloučit z indexování pomocí cest indexování (IndexingPolicy. IncludedPaths a IndexingPolicy. ExcludedPaths). Indexování cest může zlepšit výkon zápisu a omezit úložiště indexu pro scénáře, ve kterých jsou vzory dotazů předem známé. Důvodem je to, že se náklady na indexování nevztahují přímo na počet indexovaných jedinečných cest. Tento kód například ukazuje, jak vyloučit celý oddíl dokumentů (podstrom) z indexování pomocí zástupného znaku "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

Další informace najdete v tématu [Azure Cosmos DB zásady indexování](index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a> Zvyšují

**Měření a optimalizace pro nižší jednotky žádostí za sekundu použití**

Azure Cosmos DB nabízí bohatou sadu databázových operací. Tyto operace zahrnují relační a hierarchické dotazy s UDF, uloženými procedurami a triggery, a to vše v dokumentech v rámci kolekce databáze. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, vstupně-výstupních operacích a paměti potřebných k dokončení operace. Místo toho, abyste si vymysleli a spravovali hardwarové prostředky, si můžete představit jednotku žádosti (RU) jako jednu míru prostředků potřebných k provádění různých databázových operací a zpracování požadavků aplikace.

Propustnost se zřizuje na základě počtu [jednotek žádostí](request-units.md) nastavených pro každý kontejner. Spotřeba jednotky požadavku se vyhodnotí jako sazba za sekundu. Aplikace, které překračují sazbu jednotky zřízené žádosti pro svůj kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň kontejneru. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit svou propustnost tím, že zřizujete další jednotky žádostí.

Složitost dotazu ovlivňuje počet spotřebovaných jednotek požadavků pro určitou operaci. Počet predikátů, povaha predikátů, počet UDF a velikost zdrojové datové sady ovlivňují náklady na operace dotazů.

Pokud chcete změřit režii jakékoli operace (vytvořit, aktualizovat nebo odstranit), zkontrolujte záhlaví [x-MS-Request-poplatek](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (nebo ekvivalentní `RequestCharge` vlastnost v `ResourceResponse\<T>` `FeedResponse\<T>` sadě SDK sady .NET) a změřte tak počet jednotek žádostí spotřebovaných operacemi:

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

Poplatek za požadavek vrácený v této hlavičce je zlomek zřízené propustnosti (tj. 2 000 ru za sekundu). Pokud například předchozí dotaz vrátí dokumenty 1 000 1 – KB, cena za operaci je 1 000. Takže během jedné sekundy Server respektuje jenom dva takové požadavky, než tarif omezí pozdější požadavky. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek požadavků](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Omezení rychlosti zpracování/počet požadavků je moc velký.**

Když se klient pokusí překročit rezervovanou propustnost účtu, neexistují žádné snížení výkonu na serveru a žádné využití kapacity propustnosti mimo rezervovanou úroveň. Server bude žádost bez jakýchkoli požadavků RequestRateTooLarge (kód stavu HTTP 429). Vrátí záhlaví [x-MS-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) , které indikuje množství času (v milisekundách), po který uživatel musí čekat, než bude požadavek znovu proveden.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

Sady SDK mají implicitně zachytit tuto odpověď, a to v souladu s hlavičkou, která je určená serverem, a zkuste žádost zopakovat. Pokud k účtu nepoužíváte souběžně více klientů, další pokus bude úspěšný.

Pokud máte více než jednoho klienta, který se konzistentně pracuje konzistentně nad sazbou požadavku, nemusí stačit výchozí počet opakování nastavený na 9 interně klientem. V takovém případě klient vyvolá DocumentClientException se stavovým kódem 429 do aplikace. 

Výchozí počet opakování můžete změnit nastavením `RetryOptions` v `ConnectionPolicy` instanci. Ve výchozím nastavení se DocumentClientException se stavovým kódem 429 vrátí po kumulativní čekací době 30 sekund, pokud požadavek nadále funguje nad sazbou požadavku. Tato chyba se vrátí i v případě, že aktuální počet opakování je menší než maximální počet opakování, zda je aktuální hodnota výchozí hodnotou 9 nebo uživatelem definovanou hodnotou.

Automatizované chování při opakování pomáhá zlepšit odolnost a použitelnost většiny aplikací. To ale nemusí být nejlepší chování při provádění srovnávacích testů výkonu, zejména při měření latence. Latence zjištěná klientem bude špička, pokud experiment narazí na omezení serveru a způsobí, že se klientská sada SDK tiše znovu pokusí. Aby se zabránilo špičkám latence během experimentů s výkonem, změřte poplatky vracené jednotlivými operacemi a zajistěte, aby požadavky byly v provozu pod rezervovanými sazbami požadavků. Další informace najdete v tématu [jednotky žádostí](request-units.md).

**Pro vyšší propustnost, návrh pro menší dokumenty**

Poplatek za požadavek (tj. náklady na zpracování žádosti) dané operace koreluje přímo s velikostí dokumentu. Operace s velkým objemem dokumentů se dotýkají více než operací na malých dokumentech.

## <a name="next-steps"></a>Další kroky

Ukázkovou aplikaci, která se používá k vyhodnocení Azure Cosmos DB pro scénáře s vysokým výkonem na několika klientských počítačích, najdete v tématu [testování výkonu a škálování pomocí Azure Cosmos DB](performance-testing.md).

Další informace o návrhu aplikace pro škálování a vysoký výkon najdete v tématu [dělení a škálování v Azure Cosmos DB](partitioning-overview.md).
