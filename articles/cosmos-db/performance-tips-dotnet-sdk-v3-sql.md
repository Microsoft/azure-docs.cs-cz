---
title: Tipy pro výkon Azure Cosmos DB pro .NET SDK V3
description: Seznamte se s možnostmi konfigurace klienta, které vám pomůžou vylepšit Azure Cosmos DB výkonu sady .NET V3 SDK
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: jawilley
ms.custom: devx-track-dotnet, contperfq2
ms.openlocfilehash: ab9fc4f08b96fc10a20125c30af2d6b8050c7606
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341735"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Tipy pro zvýšení výkonu pro Azure Cosmos DB a .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Sada Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Sada Async Java SDK v2](performance-tips-async-java.md)
> * [Sada Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která plynule škáluje zaručenou latenci a úrovně propustnosti. Nemusíte dělat zásadní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Vertikální navýšení a snížení kapacity je stejně snadné jako při provádění jediného volání rozhraní API. Další informace najdete v tématu [zřízení propustnosti kontejneru](how-to-provision-container-throughput.md) nebo [zřízení propustnosti databáze](how-to-provision-database-throughput.md). 

Vzhledem k tomu, že k Azure Cosmos DB dochází prostřednictvím síťových volání, můžete provádět optimalizace na straně klienta, aby při použití [sady SQL .NET SDK](sql-api-sdk-dotnet-standard.md)dosáhly špičkový výkon.

Pokud se snažíte zvýšit výkon databáze, vezměte v úvahu možnosti uvedené v následujících oddílech.

## <a name="hosting-recommendations"></a>Doporučení hostování

**Pro úlohy náročné na dotazy použijte Windows 64, nikoli Linux nebo Windows 32-bit Processing Host.**

Pro zlepšení výkonu doporučujeme zpracování bitového hostitelského systému Windows 64. Sada SQL SDK obsahuje nativní ServiceInterop.dll k analýze a optimalizaci dotazů v místním prostředí. ServiceInterop.dll se podporuje jenom na platformě Windows x64. 

Pro Linux a jiné nepodporované platformy, kde ServiceInterop.dll není k dispozici, je bráně k získání optimalizovaného dotazu učiněno další síťové volání. 

Čtyři uvedené typy aplikací používají ve výchozím nastavení 32 hostitelského zpracování. Chcete-li změnit zpracování hostitele na 64 zpracování pro typ aplikace, postupujte následovně:

- **Pro spustitelné aplikace** : v okně **Vlastnosti projektu** v podokně **sestavení** nastavte [cíl platformy](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) na hodnotu **x64**.

- **Pro projekty testů založené na VSTest** : v nabídce **test** sady Visual Studio vyberte nastavení **test**  >  **testu** a pak nastavte **výchozí architekturu procesoru** na hodnotu **x64**.

- **Pro lokálně nasazené webové aplikace v ASP.NET** : vyberte **nástroje**  >  **Možnosti**  >  **projekty a řešení**  >  **webové projekty** a potom vyberte **použít 64 verze IIS Express pro weby a projekty**.

- **Pro webové aplikace ASP.NET nasazené v Azure** : v **nastavení aplikace** vyberte v Azure Portal části nastavení aplikace **64** platformu.

> [!NOTE] 
> Ve výchozím nastavení jsou nové projekty sady Visual Studio nastaveny na **Libovolný procesor**. Doporučujeme, abyste projekt nastavili na **x64** , takže se nepřepne na **x86**. Projekt, který je nastavený na **Libovolný procesor** , se může snadno přepnout na **x86** , pokud se přidá závislost jenom pro procesory x86.<br/>
> Soubor ServiceInterop.dll musí být ve složce, ze které je spuštěna knihovna DLL sady SDK. To by mělo být obavy pouze v případě, že knihovny DLL ručně kopírujete nebo máte vlastní systémy sestavení nebo nasazení.
    
**Zapnout shromažďování paměti na straně serveru**

Omezení frekvence uvolňování paměti může v některých případech pomáhat. V rozhraní .NET nastavte [gcServer](/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) na `true` .

**Horizontální navýšení kapacity úlohy klienta**

Pokud testujete na úrovních vysoké propustnosti nebo v tarifech, které jsou větší než 50 000 jednotek žádostí za sekundu (RU/s), může se klientská aplikace stát kritickým bodem úlohy. Důvodem je to, že počítač může zakončit na využití procesoru nebo sítě. Pokud se dostanou k tomuto bodu, můžete dál nasdílet Azure Cosmos DB účet tím, že navedete horizontální navýšení kapacity klientských aplikací napříč více servery.

> [!NOTE] 
> Vysoké využití procesoru může způsobit zvýšení latence a vypršení časového limitu požadavku.

## <a name="networking"></a>Sítě
<a id="direct-connection"></a>

**Zásady připojení: použít přímý režim připojení**

Výchozí režim připojení sady .NET V3 SDK je přímý. Režim připojení nakonfigurujete při vytváření `CosmosClient` instance v nástroji `CosmosClientOptions` .  Další informace o různých možnostech připojení najdete v článku [režimy připojení](sql-sdk-connection-modes.md) .

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

**Vyčerpání dočasných portů**

Pokud se na instancích zobrazí velký objem připojení nebo vysoké využití portů, ověřte nejprve, zda jsou klientské instance typu singleton. Jinými slovy, instance klientů by měly být pro celou dobu života aplikace jedinečné.

Když je spuštěný protokol TCP, klient se optimalizuje kvůli latenci pomocí dlouhotrvajících připojení. To je v kontrastu s protokolem HTTPS, který ukončí připojení po dvou minutách nečinnosti.

V případech, kdy máte zhuštěný přístup, a pokud si všimnete vyššího počtu připojení v porovnání s přístupem k režimu brány, můžete:

* Nakonfigurujte vlastnost [CosmosClientOptions. PortReuseMode](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) na `PrivatePortPool` (platí pro rozhraní Framework verze 4.6.1 a novější a .net Core verze 2,0 a novější). Tato vlastnost umožňuje sadě SDK používat pro různé Azure Cosmos DB cílové koncové body malý fond dočasných portů.
* Nakonfigurujte vlastnost [CosmosClientOptions. IdleConnectionTimeout](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) tak, aby byla větší nebo rovna 10 minutám. Doporučené hodnoty jsou 20 minut až 24 hodin.

<a id="same-region"></a>

**Pro výkon společné umístění klienti ve stejné oblasti Azure**

Pokud je to možné, umístěte všechny aplikace, které volají Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos DB. Toto je přibližné porovnání: volání Azure Cosmos DB v rámci stejné oblasti se dokončí do 1 milisekundy (MS) až 2 MS, ale latence mezi západním a východním pobřežím USA je větší než 50 ms. Tato latence se může lišit od požadavku na vyžádání v závislosti na trasách, kterou požadavek prochází z klienta na hranici datacentra Azure. 

Nejnižší možnou latenci získáte tak, že zajistíte, aby se volající aplikace nacházela ve stejné oblasti Azure jako koncový bod zřízené Azure Cosmos DB. Seznam oblastí, které jsou k dispozici, najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Společné umístění klienty ve stejné oblasti." border="false":::

   <a id="increase-threads"></a>

**Zvýšení počtu vláken/úloh**

Vzhledem k tomu, že volání Azure Cosmos DB jsou provedena přes síť, možná budete muset změnit stupeň souběžnosti požadavků, aby klientská aplikace strávila minimální dobu čekání mezi požadavky. Pokud například používáte [úlohu .NET Parallel Library](/dotnet/standard/parallel-programming/task-parallel-library-tpl), vytvořte si pořadí stovky úloh, které čtou nebo zapisují do Azure Cosmos DB.

**Povolit akcelerované síťové služby**
 
Pro snížení latence a kolísání procesoru doporučujeme povolit akcelerované síťové služby na virtuálních počítačích klientů. Další informace najdete v tématu [Vytvoření virtuálního počítače s Windows s akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-powershell.md) službami nebo [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-cli.md)službami.

## <a name="sdk-usage"></a>Využití sady SDK

**Nainstalovat nejnovější sadu SDK**

Sady Azure Cosmos DB SDK se neustále zdokonalují, aby poskytovaly nejlepší výkon. Chcete-li určit nejnovější sadu SDK a zkontrolovat vylepšení, přečtěte si téma [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md).

**Použití rozhraní API pro streamování**

[Sada .NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) obsahuje rozhraní API pro Stream, která mohou přijímat a vracet data bez serializace. 

Aplikace střední vrstvy, které nevyužívají odpovědi přímo ze sady SDK, ale jejich přenos na jiné aplikační vrstvy můžou těžit z rozhraní API streamu. Příklady zpracování datových proudů najdete v ukázkách [správy položek](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) .

**Použití typu Singleton Azure Cosmos DB klienta po dobu života vaší aplikace**

Každá `CosmosClient` instance je bezpečná pro přístup z více vláken a provádí efektivní správu připojení a ukládání adres do mezipaměti, když funguje v přímém režimu. K zajištění efektivní správy připojení a lepšího výkonu klienta sady SDK doporučujeme, abyste `AppDomain` pro celou dobu života aplikace použili jednu instanci.

Když pracujete na Azure Functions, instance by měly také postupovat podle stávajících [pokynů](../azure-functions/manage-connections.md#static-clients) a udržovat jednu instanci.

<a id="max-connection"></a>

**Zakažte odpověď obsahu při operacích zápisu.**

Pro úlohy s velkým množstvím vytvoření datových částí nastavte `EnableContentResponseOnWrite` možnost požadavek na `false` . Služba již nebude vracet vytvořený nebo aktualizovaný prostředek k sadě SDK. Vzhledem k tomu, že aplikace má objekt, který je právě vytvořen, obvykle nepotřebuje službu k jejímu vrácení. Hodnoty hlaviček jsou pořád dostupné, třeba poplatky za požadavek. Vypnutí odpovědi na obsah může zvýšit výkon, protože sada SDK již nemusí přidělovat paměť nebo serializovat tělo odpovědi. Také snižuje využití šířky pásma sítě, aby bylo možné lépe zvýšit výkon.  

```csharp
ItemRequestOptions requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**Povolit hromadnou optimalizaci pro zajištění propustnosti místo latence**

Pro scénáře, které vyžadují velké množství propustnosti, můžete povolit *hromadnou* zátěž a latence není tak důležitá. Další informace o tom, jak povolit hromadnou funkci a zjistit, jaké scénáře by se měly použít, najdete v tématu [Úvod do hromadné podpory](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk).

**Zvýšit System.Net MaxConnections na hostitele při použití režimu brány**

Při použití režimu brány se požadavky na Azure Cosmos DB zavedou přes HTTPS/REST. Vztahují se na výchozí limit počtu připojení na název hostitele nebo IP adresu. Možná budete muset nastavit `MaxConnections` na vyšší hodnotu (od 100 do 1 000), aby Klientská knihovna mohla používat víc souběžných připojení k Azure Cosmos DB. V sadě .NET SDK 1.8.0 a novější je výchozí hodnota pro [Třída ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 50. Chcete-li změnit hodnotu, můžete nastavit [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) na vyšší hodnotu.

**Ladění paralelních dotazů pro dělené kolekce**

Sada SQL .NET SDK podporuje paralelní dotazy, které umožňují paralelní dotazování kontejneru rozděleného na oddíly. Další informace najdete v tématu [ukázky kódu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) týkající se práce se sadami SDK. Paralelní dotazy jsou navržené tak, aby poskytovaly lepší latenci a propustnost dotazů než jejich sériové protějšky. 

Paralelní dotazy poskytují dva parametry, které můžete ladit podle svých požadavků: 

- **MaxConcurrency** : Určuje maximální počet oddílů, které lze dotazovat paralelně.

   Paralelní dotaz funguje paralelně dotazování na více oddílů. Data z jednotlivého oddílu se ale v souvislosti s dotazem načítají sériově. Nastavení `MaxConcurrency` v [sadě SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) na počet oddílů má největší šanci na dosažení nejvíce výkonného dotazu, za předpokladu, že všechny ostatní systémové podmínky zůstávají stejné. Pokud neznáte počet oddílů, můžete nastavit úroveň paralelismu na vysoké číslo. V systému se jako stupeň paralelismu zvolí minimální počet oddílů, uživatelem zadaný vstup.

    Paralelní dotazy poskytují nejvíc výhod, pokud jsou data rovnoměrně rozložena napříč všemi oddíly s ohledem na dotaz. Pokud je dělená kolekce rozdělená tak, aby všechna nebo většinu dat vrácených dotazem byla soustředěna v několika oddílech (jeden z nich je v nejhorším případě), tyto oddíly budou mít kritický vliv na výkon dotazu.
   
- **MaxBufferedItemCount** : řídí počet předběžně načtených výsledků.

   Paralelní dotaz je navržený tak, aby byly výsledky předem načteny, zatímco aktuální dávka výsledků je zpracovávána klientem. Toto předběžné načítání pomáhá zlepšit celkovou latenci dotazu. `MaxBufferedItemCount`Parametr omezuje počet předběžně načtených výsledků. Nastavte `MaxBufferedItemCount` na očekávaný počet vrácených výsledků (nebo vyšší číslo), aby dotaz mohl získat maximální přínos před načtením.

   Předběžné načítání funguje stejným způsobem bez ohledu na stupeň paralelismu a existuje jedna vyrovnávací paměť pro data ze všech oddílů.  

**Implementace omezení rychlosti v intervalech RetryAfter**

Během testování výkonu byste měli zvýšit zatížení, dokud se neomezí malá míra požadavků. Pokud jsou požadavky omezené, klientská aplikace by měla pro interval opakování zadaného serveru obnovit omezení. Respektování omezení rychlosti vám pomůže zajistit, že budete věnovat minimální dobu čekání mezi opakovanými pokusy. 

Další informace najdete v tématu [RetryAfter](/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
Existuje mechanismus pro protokolování dalších diagnostických informací a problémů s latencí při odstraňování potíží, jak je znázorněno v následující ukázce. Můžete protokolovat řetězec diagnostiky pro požadavky, které mají vyšší latenci čtení. Zachycený řetězec diagnostiky vám pomůže pochopit, kolikrát se vám pro daný požadavek zobrazila chyba *429* .

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**Zvýšení počtu vláken/úloh**

Podívejte [se na téma zvýšení počtu vláken/úloh](#increase-threads) v části sítě v tomto článku.

## <a name="indexing-policy"></a>Zásada indexování
 
**Vyloučení nepoužívaných cest z indexování za účelem zrychlení zápisu**

Zásada indexování Azure Cosmos DB také umožňuje určit, které cesty dokumentů zahrnout nebo vyloučit z indexování pomocí cest indexování (IndexingPolicy. IncludedPaths a IndexingPolicy. ExcludedPaths). 

Indexování pouze těch cest, které potřebujete, může vylepšit výkon zápisu, snížit náklady na zápis a snížit kapacitu úložiště pro scénáře, ve kterých jsou vzorce dotazů známy předem. Důvodem je to, že se náklady na indexování nevztahují přímo na počet indexovaných jedinečných cest. Například následující kód ukazuje, jak vyloučit celý oddíl dokumentů (podstrom) z indexování pomocí zástupného znaku "*":

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

Další informace najdete v tématu [Azure Cosmos DB zásady indexování](index-policy.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

**Měření a optimalizace pro nižší využití RU/s**

Azure Cosmos DB nabízí bohatou sadu databázových operací. Tyto operace zahrnují relační a hierarchické dotazy se soubory UDF (Universal Disk Format), uloženými procedurami a triggery, a to vše v dokumentech v rámci kolekce databáze. 

Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, vstupně-výstupních operacích a paměti, které jsou potřeba k dokončení operace. Místo toho, abyste si vymysleli a spravovali hardwarové prostředky, si můžete představit jako jedno opatření pro prostředky, které jsou nutné k provádění různých databázových operací a zpracování požadavků aplikace.

Propustnost se zřizuje na základě počtu [jednotek žádostí](request-units.md) nastavených pro každý kontejner. Spotřeba jednotky požadavku se vyhodnocuje jako sazba jednotek za sekundu. Aplikace, které překračují sazbu jednotky zřízené žádosti pro svůj kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň kontejneru. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit svou propustnost tím, že zřizujete další jednotky žádostí.

Složitost dotazu ovlivňuje počet spotřebovaných jednotek požadavků pro určitou operaci. Počet predikátů, povaha predikátů, počet souborů UDF a velikost zdrojové datové sady ovlivňují náklady na operace dotazů.

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

Poplatek za požadavek vrácený v této hlavičce je zlomek zřízené propustnosti (to znamená 2 000 RU/s). Pokud například předchozí dotaz vrátí dokumenty 1 000 1 – KB, cena za operaci je 1 000. Takže během jedné sekundy Server respektuje jenom dva takové požadavky, než se později požadavky omezí. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek požadavků](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Omezení rychlosti zpracování/počet požadavků je moc velký.**

Když se klient pokusí překročit rezervovanou propustnost účtu, neexistují žádné snížení výkonu na serveru a žádné využití kapacity propustnosti mimo rezervovanou úroveň. Server ukončí požadavek pomocí RequestRateTooLarge (kód stavu HTTP 429). Vrací hlavičku [x-MS-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) , která indikuje, jak dlouho (v milisekundách) musí uživatel čekat, než se požadavek znovu pokusí.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

Sady SDK mají implicitně zachytit tuto odpověď, a to v souladu s hlavičkou, která je určená serverem, a zkuste žádost zopakovat. Pokud k účtu nepoužíváte souběžně více klientů, další pokus bude úspěšný.

Pokud máte více než jednoho klienta, který je v současné době neustále stejný než frekvence požadavků, nemusí stačit výchozí počet opakování nastavený na 9 interně klientem. V takovém případě klient vyvolá CosmosException se stavovým kódem 429 do aplikace. 

Výchozí počet opakování můžete změnit nastavením `RetryOptions` v `CosmosClientOptions` instanci. Ve výchozím nastavení se CosmosException se stavovým kódem 429 vrátí po kumulativní čekací době 30 sekund, pokud požadavek nadále funguje nad sazbou požadavku. Tato chyba je vrácena i v případě, že je aktuální počet opakování menší než maximální počet opakování, zda je aktuální hodnota výchozí hodnotou 9 nebo uživatelem definovanou hodnotou.

Automatizované chování při opakování pomáhá zlepšit odolnost a použitelnost většiny aplikací. To ale nemusí být nejlepší chování při provádění srovnávacích testů výkonu, zejména při měření latence. Latence zjištěná klientem bude špička, pokud experiment narazí na omezení serveru a způsobí, že se klientská sada SDK tiše znovu pokusí. Aby se zabránilo špičkám latence během experimentů s výkonem, změřte poplatek, který je vrácený jednotlivými operacemi, a zajistěte, aby požadavky byly v provozu pod rezervovanými sazbami požadavků. 

Další informace najdete v tématu [jednotky žádostí](request-units.md).

**Pro vyšší propustnost, návrh pro menší dokumenty**

Poplatek za požadavek (tj. náklady na zpracování žádosti) zadané operace koreluje přímo s velikostí dokumentu. Operace s velkým objemem dokumentů se dotýkají více než operací na malých dokumentech.

## <a name="next-steps"></a>Další kroky
Ukázkovou aplikaci, která se používá k vyhodnocení Azure Cosmos DB pro scénáře s vysokým výkonem na několika klientských počítačích, najdete v tématu [testování výkonu a škálování pomocí Azure Cosmos DB](performance-testing.md).

Další informace o návrhu aplikace pro škálování a vysoký výkon najdete v tématu [dělení a škálování v Azure Cosmos DB](partitioning-overview.md).