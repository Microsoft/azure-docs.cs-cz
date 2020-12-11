---
title: Tipy pro zvýšení výkonu pro sadu Java SDK v4 služby Azure Cosmos DB
description: Seznamte se s možnostmi konfigurace klientů pro zlepšení výkonu Azure Cosmos Database pro Java SDK v4
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 10/13/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: 79f8c868b68cba1cff3e99e88e989fcc4d2a3df2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029018"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Tipy pro zvýšení výkonu pro sadu Java SDK v4 služby Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Sada Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Sada Async Java SDK v2](performance-tips-async-java.md)
> * [Sada Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> Tipy k výkonu v tomto článku jsou k disAzure Cosmos DB pouze v jazyce Java SDK v4. Další informace najdete v části Azure Cosmos DB zpráva k [vydání verze](sql-api-sdk-java-v4.md)Java SDK v4, [úložiště Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)a [Průvodce řešením potíží s](troubleshoot-java-sdk-v4-sql.md) Azure Cosmos DB Java SDK v4. Pokud aktuálně používáte starší verzi než v4, přečtěte si článek průvodce [migrací do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , který vám pomůže s upgradem na V4.
>

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která bez problémů škáluje zaručenou latenci a propustnost. Nemusíte dělat zásadní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Vertikální navýšení a snížení kapacity je stejně snadné jako volání jednoho rozhraní API nebo volání metody sady SDK. Vzhledem k tomu, že k Azure Cosmos DB je k dispozici prostřednictvím síťových volání, existují optimalizace na straně klienta, které vám při použití Azure Cosmos DB Java SDK v4 můžou dosáhnout špičkového výkonu.

Takže pokud si vyžádáte "Jak můžu vylepšit výkon databáze?" Vezměte v úvahu následující možnosti:

## <a name="networking"></a>Sítě

* **Režim připojení: použít přímý režim**
<a id="direct-connection"></a>
    
    Výchozí režim připojení sady Java SDK je přímý. Režim připojení můžete nakonfigurovat v Tvůrci klienta pomocí metod *directMode ()* nebo *gatewayMode ()* , jak je znázorněno níže. Chcete-li nakonfigurovat kterýkoli režim s výchozím nastavením, zavolejte buď metodu bez argumentů. V opačném případě předejte instanci třídy nastavení konfigurace jako argument (*DirectConnectionConfig* pro *directMode ()*,  *GatewayConnectionConfig* pro *gatewayMode ()*.). Další informace o různých možnostech připojení najdete v článku [režimy připojení](sql-sdk-connection-modes.md) .
    
    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Sada Java v4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

    # <a name="sync"></a>[Synchronizovat](#tab/api-sync)

    Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

    --- 

    Metoda *directMode ()* má dodatečné přepsání z následujícího důvodu. Operace roviny ovládacího prvku, jako je databáze a CRUD, používají *vždy* režim brány; Pokud uživatel nakonfiguroval přímý režim pro operace roviny dat, operace řídicí roviny použijí nastavení režimu výchozí brány. To vyhovuje většině uživatelů. Uživatel, který požaduje přímý režim pro operace roviny dat, a také tunability parametrů režimu brány řídicích ploch může použít následující přepsání *directMode ()* :

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Sada Java v4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

    # <a name="sync"></a>[Synchronizovat](#tab/api-sync)

    Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

    --- 

<a name="collocate-clients"></a>
* **Společné umístění klientů ve stejné oblasti Azure pro výkon**<a id="same-region"></a>

    Pokud je to možné, umístěte všechny aplikace, které volají Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos. V případě přibližného porovnání se volání Azure Cosmos DB v rámci stejné oblasti dokončí v 1-2 MS, ale latence mezi západním a východním pobřežím USA je >50 ms. Tato latence se může lišit od požadavku na vyžádání v závislosti na trasách, kterou požadavek prochází z klienta na hranici datacentra Azure. Nejnižší možná latence se dosahuje tím, že se zaručí, že se volající aplikace nachází ve stejné oblasti Azure jako koncový bod zřízené Azure Cosmos DB. Seznam oblastí, které jsou k dispozici, najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Obrázek zásad Azure Cosmos DBho připojení" border="false":::

    Aplikace, která komunikuje s Azure Cosmos DB účtem s více oblastmi, musí nakonfigurovat [upřednostňovaná umístění](tutorial-global-distribution-sql-api.md#preferred-locations) , aby se zajistilo, že se požadavky přecházejí do společně umístěného oblasti.

* **Povolte na VIRTUÁLNÍm počítači Azure akcelerované síťové služby, aby se snížila latence.**

Doporučuje se postupovat podle pokynů pro povolení akcelerovaných síťových prostředí ve [Windows (kliknutím na pokyny)](../virtual-network/create-vm-accelerated-networking-powershell.md) nebo [Linux (kliknutím na pokyny)](../virtual-network/create-vm-accelerated-networking-cli.md) na virtuální počítač Azure, aby se maximalizoval výkon.

Bez urychlení sítě může být vstupně-výstupní operace přenosu mezi VIRTUÁLNÍm počítačem Azure a dalšími prostředky Azure nutně směrována přes hostitele a virtuální přepínač umístěný mezi virtuálním počítačem a jeho síťovou kartou. V případě, že se hostitel a virtuální přepínač vloží do DataPath, se nezvyšuje jenom latence a kolísání komunikačního kanálu, ale taky z virtuálního počítače odvolá cykly CPU. S akcelerovanými síťovými rozhraními se rozhraní virtuálních počítačů přímo s rozhraním NIC bez dodavatelů; všechny podrobnosti o zásadách sítě, které byly zpracovávány hostitelem a virtuálním přepínačem, jsou nyní zpracovávány v hardwaru síťového adaptéru. hostitel a virtuální přepínač se přeskočí. Obecně můžete očekávat nižší latenci a vyšší propustnost a *také spolehlivější* latenci a snížení využití procesoru, když povolíte akcelerované síťové služby.

Omezení: akcelerované síťové služby musí být podporované v operačním systému virtuálních počítačů a dají se povolit jenom v případě, že je virtuální počítač zastavený a navrácený. Virtuální počítač nejde nasadit pomocí Azure Resource Manager.

Další podrobnosti najdete v pokynech pro [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) a [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) .

## <a name="sdk-usage"></a>Využití sady SDK
* **Nainstalovat nejnovější sadu SDK**

    Sady Azure Cosmos DB SDK se neustále zdokonalují, aby poskytovaly nejlepší výkon. Pokud chcete zjistit nejnovější sadu SDK a zkontrolovat vylepšení, podívejte se na stránky [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) .

* **Použití typu Singleton Azure Cosmos DB klienta po dobu života vaší aplikace**

    Každá instance klienta Azure Cosmos DB je bezpečná pro přístup z více vláken a provádí efektivní správu připojení a ukládání adres do mezipaměti. Aby bylo možné efektivně spravovat správu a lepší výkon pomocí Azure Cosmos DB klienta, doporučujeme pro celou dobu života aplikace použít jednu instanci Azure Cosmos DB klienta na doménu AppDomain.

   <a id="max-connection"></a>

* **Použijte nejnižší úroveň konzistence požadovanou pro vaši aplikaci.**

    Když vytvoříte *CosmosClient*, použije se výchozí konzistence, pokud není explicitně nastavená *relace*. Pokud vaše logika aplikace nevyžaduje konzistenci *relace* , nastavte *konzistenci* na hodnotu *ne.* Poznámka: doporučuje se používat minimálně konzistenci *relací* v aplikacích, které používají procesor Azure Cosmos DB Change feed.

* **Použití asynchronního rozhraní API k překročení zajištěné propustnosti**

    Sada Azure Cosmos DB Java SDK V4 má dvě rozhraní API, synchronizaci a asynchronní. Zhruba řečeno, asynchronní rozhraní API implementuje funkce sady SDK, zatímco rozhraní API pro synchronizaci je tenkou obálkou, která umožňuje blokování volání asynchronního rozhraní API. To je na rozdíl od starší Azure Cosmos DB asynchronní sady Java SDK v2, která byla pouze asynchronní, a starší Azure Cosmos DB synchronizaci sady Java SDK v2, která byla pouze pro synchronizaci a měla zcela samostatnou implementaci. 
    
    Volba rozhraní API je určena při inicializaci klienta; *CosmosAsyncClient* podporuje asynchronní rozhraní API, zatímco *CosmosClient* podporuje synchronizaci rozhraní API. 
    
    Asynchronní rozhraní API implementuje neblokování vstupně-výstupní operace a je optimální volbou, pokud je vaším cílem maximální propustnost při vystavování požadavků Azure Cosmos DB. 
    
    Pokud chcete nebo potřebujete rozhraní API, které blokuje odpověď na každý požadavek, nebo pokud je synchronní operace v rámci vaší aplikace, můžete použít synchronizaci přes rozhraní API. Například můžete chtít rozhraní API pro synchronizaci, když ukládáte data do Azure Cosmos DB v aplikaci mikroslužeb, poskytnutá propustnost není kritická.  
    
    Stačí si uvědomit, že synchronizace propustnosti rozhraní API se sníží a zvyšuje čas odezvy žádosti, zatímco asynchronní rozhraní API může navýšit celou šířku pásma vašeho hardwaru. 
    
    Geografické společné umístění vám může při použití rozhraní API pro synchronizaci zajistit vyšší a jednotnější propustnost (viz [společné umístění klienti ve stejné oblasti Azure pro výkon](#collocate-clients)), ale pořád se nepředpokládá, že by se překročila propustnost, která by mohla dosáhnout ASYNCHRONNÍHO rozhraní API.

    Někteří uživatelé mohou také být Neobeznámeni s nástrojem [Project Reactor](https://projectreactor.io/), což je rozhraní reaktivních datových proudů, které se používá k implementaci Azure Cosmos DB asynchronní rozhraní API Java SDK v4. Pokud se to týká, doporučujeme, abyste si přečetli náš [Průvodce vzorem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) pro úvodní reaktor a potom se podíváme na tento [Úvod k Reaktivnímu programování](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) , abyste mohli seznámení sami. Pokud jste již používali Azure Cosmos DB s asynchronním rozhraním a sada SDK, kterou jste použili, byla Azure Cosmos DB Async Java SDK v2, možná budete obeznámeni s RxJavam [ovládacího prvku ActiveX](http://reactivex.io/), ale nemusíte / [RxJava](https://github.com/ReactiveX/RxJava) si být jisti, co se změnilo v reaktoru projektu. V takovém případě se prosím podívejte na náš aktéra [vs. RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) , abyste se seznámili.

    Následující fragmenty kódu ukazují, jak inicializovat klienta Azure Cosmos DB pro operaci rozhraní API asynchronního rozhraní API nebo synchronizace, v uvedeném pořadí:

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Sada Java v4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

    # <a name="sync"></a>[Synchronizovat](#tab/api-sync)

    Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

    --- 

* **Vyladění ConnectionPolicy**

    Ve výchozím nastavení se požadavky přímého režimu Cosmos DB při použití Azure Cosmos DB Java SDK v4 provedou přes protokol TCP. Interně přímý režim používá speciální architekturu k dynamické správě síťových prostředků a k dosažení nejlepšího výkonu.

    V Azure Cosmos DB Java SDK v4 je přímým řešením nejlepší volbou pro zlepšení výkonu databáze s většinou úloh. 

    * ***Přehled přímého režimu** _

        :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Ilustrace architektury přímého režimu" border="false":::

        Architektura na straně klienta pracující v přímém režimu umožňuje předvídatelné využití sítě a multiplexější přístup k replikám Azure Cosmos DB. Výše uvedený diagram ukazuje, jak přímý režim směruje požadavky klienta na repliky v Cosmos DB back-endu. Architektura přímého režimu přiděluje až 10 _ *kanálů** na straně klienta pro repliku databáze. Kanál je připojení TCP předchází vyrovnávací paměť požadavků, což je 30 požadavků hluboko. Kanály patřící do repliky se dynamicky přiřazují podle potřeby **koncového bodu služby** repliky. Když uživatel vydá požadavek v přímém režimu, **TransportClient** směruje požadavek do správného koncového bodu služby na základě klíče oddílu. Vyrovnávací paměti front požadavků se **vyžadují** před koncovým bodem služby.

    * ***Možnosti konfigurace pro přímý režim** _

        Pokud je žádoucí jiné než výchozí chování přímého režimu, vytvořte instanci _DirectConnectionConfig * a upravte její vlastnosti a pak předejte vlastní instanci vlastnosti metodě *directMode ()* v nástroji Azure Cosmos DB Client Builder.

        Tato nastavení konfigurace řídí chování základní architektury přímého režimu popsané výše.

        V prvním kroku použijte následující doporučené konfigurační nastavení. Tyto možnosti *DirectConnectionConfig* jsou pokročilá nastavení konfigurace, která mohou ovlivnit výkon sady SDK neočekávaným způsobem. Doporučujeme, aby se uživatelé nemuseli měnit, pokud se jim nedaří lépe porozumět kompromisům a je nezbytně nutné. Pokud v tomto konkrétním tématu narazíte na problémy, obraťte se prosím na [tým Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) .

        | Možnost konfigurace       | Výchozí   |
        | :------------------:       | :-----:   |
        | idleConnectionTimeout      | "PT0"     |
        | maxConnectionsPerEndpoint  | "130"     |
        | connectTimeout             | "PT5S"    |
        | idleEndpointTimeout        | PT1H    |
        | maxRequestsPerConnection   | 30      |

* **Ladění paralelních dotazů pro dělené kolekce**

    Azure Cosmos DB Java SDK v4 podporuje paralelní dotazy, které umožňují paralelní dotazování dělené kolekce. Další informace najdete v tématu [ukázky kódu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) týkající se práce s Azure Cosmos DB Java SDK v4. Paralelní dotazy jsou navržené tak, aby se zlepšila latence a propustnost dotazů v rámci svého sériového protějšku.

    * ***Vyladění \: setMaxDegreeOfParallelism** _
    
        Paralelní dotazy fungují paralelně dotazování na více oddílů. Data z jednotlivých dělených kolekcí se ale v souvislosti s dotazem načítají sériově. Proto použijte setMaxDegreeOfParallelism k nastavení počtu oddílů, které mají maximální šanci dosáhnout nejvíce výkonného dotazu. za předpokladu, že všechny ostatní systémové podmínky zůstanou stejné. Pokud neznáte počet oddílů, můžete použít setMaxDegreeOfParallelism k nastavení vysokého čísla a systém zvolí minimální (počet oddílů, uživatelem zadaný vstup) jako maximální stupeň paralelismu.

        Je důležité si uvědomit, že paralelní dotazy poskytují nejlepší výhody, pokud jsou data rovnoměrně rozložena napříč všemi oddíly v souvislosti s dotazem. Pokud je dělená kolekce rozdělena takovým způsobem, že všechna nebo většina dat vrácených dotazem je soustředěna v několika oddílech (jeden oddíl v nejhorším případě), výkon dotazu by tyto oddíly měl být kritický.

    _ ***Vyladění \: setMaxBufferedItemCount** _
    
        Parallel query is designed to pre-fetch results while the current batch of results is being processed by the client. The pre-fetching helps in overall latency improvement of a query. setMaxBufferedItemCount limits the number of pre-fetched results. Setting setMaxBufferedItemCount to the expected number of results returned (or a higher number) enables the query to receive maximum benefit from pre-fetching.

        Pre-fetching works the same way irrespective of the MaxDegreeOfParallelism, and there is a single buffer for the data from all partitions.

_ **Horizontálního navýšení kapacity klienta – zatížení**

    If you are testing at high throughput levels, the client application may become the bottleneck due to the machine capping out on CPU or network utilization. If you reach this point, you can continue to push the Azure Cosmos DB account further by scaling out your client applications across multiple servers.

    A good rule of thumb is not to exceed >50% CPU utilization on any given server, to keep latency low.

   <a id="tune-page-size"></a>

* **Vyladění velikosti stránek pro dotazy a kanály pro čtení pro lepší výkon**

    Při hromadném čtení dokumentů pomocí funkce kanálu pro čtení (například *readItems*) nebo při vystavení dotazu SQL (*queryItems*) se výsledky vrátí segmenticky, pokud je sada výsledků příliš velká. Ve výchozím nastavení se výsledky vrátí do bloků 100 položek nebo 1 MB, podle toho, který limit se narazí jako první.

    Předpokládejme, že vaše aplikace vydá dotaz Azure Cosmos DB a předpokládá se, že vaše aplikace vyžaduje úplnou sadu výsledků dotazu, aby bylo možné dokončit její úlohu. Chcete-li snížit počet síťových přenosů potřebných k načtení všech použitelných výsledků, můžete zvětšit velikost stránky úpravou pole Hlavička požadavku [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) . 

    * Pro dotazy s jedním oddílem, nastavením hodnoty v poli [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) na hodnotu-1 (bez omezení velikosti stránky) maximalizujete latenci minimalizací počtu stránek odpovědí na dotazy: Úplná sada výsledků se vrátí na jednu stránku, nebo pokud dotaz trvá déle, než je interval časového limitu, bude celá sada výsledků vrácena v minimálním počtu možných stránek. Tato operace šetří násobky požadavku na dobu odezvy.
    
    * U dotazů mezi oddíly nastavíte pole [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) na hodnotu-1 a odeberete omezení velikosti stránky a omezíte tak rizika na nespravovatelné velikosti stránek sady SDK. V případě mezioddílů doporučujeme zvýšit limit velikosti stránky na určitou velkou, ale konečnou hodnotu, třeba 1000, aby se snížila latence. 
    
    V některých aplikacích nesmíte vyžadovat úplnou sadu výsledků dotazu. V případech, kdy potřebujete zobrazit jenom pár výsledků, například pokud vaše uživatelské rozhraní nebo rozhraní API pro aplikace vrátí jenom 10 výsledků najednou, můžete také zmenšit velikost stránky na 10 a snížit tak propustnost, která se pro čtení a dotazy spotřebují.

    Místo toho, abyste přímo měnili pole záhlaví REST, můžete také nastavit argument upřednostňovaná velikost stránky metody *byPage* . Mějte na paměti, že hodnota [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) nebo preferovaný argument velikosti stránky v *byPage* nastavuje jenom horní limit velikosti stránky, ne absolutní požadavek. z nejrůznějších důvodů se ale může zobrazit Azure Cosmos DB návratové stránky, které jsou menší než upřednostňovaná velikost stránky. 

* **Použít vhodný Plánovač (Vyhněte se krádežím vláken v vstupně-výstupních událostech smyčky)**

    Asynchronní funkce Azure Cosmos DB Java SDK je [založena na](https://netty.io/) neblokujícím vstupu/výstupu. Sada SDK používá pro provádění vstupně-výstupních operací pevný počet vstupně-výstupních operací cyklů událostí v/v (kolik PROCESORových jader má váš počítač). Tok vrácený rozhraním API vygeneruje výsledek na jednom ze sdílených vstupně-výstupních vláken smyčky událostí. Proto je důležité, abyste neblokovali vlákna v počtu sdílených vstupně-výstupních operací smyčky. V případě práce náročné na vstupně-výstupní operaci nebo operací blokování procesoru v/v může dojít k zablokování nebo výraznému snížení propustnosti sady SDK.

    Například následující kód provede práci náročnou na procesor ve vlákně pro vstupně-výstupní operace smyčky události:
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

    Až se vrátí výsledek, pokud chcete, aby se na výsledku prováděla náročná práce s PROCESORem, měli byste se tomuto vláknu vyhnout cyklům v případě vstupně-výstupních operací. Místo toho můžete zadat vlastní Scheduler a poskytnout tak vlastní vlákno pro práci, jak je znázorněno níže (vyžaduje `import reactor.core.scheduler.Schedulers` ).

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

    Na základě typu vaší práce byste měli použít příslušného stávajícího plánovače Reactor pro vaši práci. Přečtěte si tento článek [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html) .

    Další informace o Azure Cosmos DB Java SDK v4 najdete v [adresáři Cosmos DB sady Azure SDK pro Java monorepo na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos).

* **Optimalizace nastavení protokolování v aplikaci**

    Z nejrůznějších důvodů možná budete chtít přidat protokolování do vlákna, které generuje propustnost vysokého požadavku. Pokud je vaším cílem plně nazvýšit výkon zajištěné propustnosti kontejneru pomocí požadavků generovaných tímto vláknem, optimalizace protokolování můžou významně zlepšit výkon.

    * ***Konfigurace asynchronního protokolovacího** nástroje _

        Latence synchronního protokolovacího nástroje nutně zohledňuje celkové latence ve vašem vlákně generující požadavky. Asynchronní protokolovací nástroj, jako je [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) , se doporučuje oddělit režijní náklady od vašich vysoce výkonných aplikačních vláken.

    _ ***Zakázat protokolování síťoviny** _

        Netty library logging is chatty and needs to be turned off (suppressing sign in the configuration may not be enough) to avoid additional CPU costs. If you are not in debugging mode, disable netty's logging altogether. So if you are using log4j to remove the additional CPU costs incurred by ``org.apache.log4j.Category.callAppenders()`` from netty add the following line to your codebase:

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 _ **Omezení prostředků otevřených souborů operačního systému**
 
    Některé systémy Linux (jako Red Hat) mají horní limit počtu otevřených souborů a celkový počet připojení. Chcete-li zobrazit aktuální omezení, spusťte následující:

    ```bash
    ulimit -a
    ```

    Počet otevřených souborů (soubor) musí být dostatečně velký, aby měl dostatek místa pro nakonfigurovanou velikost fondu připojení a jiné otevřené soubory operačním systémem. Dá se upravit tak, aby umožňoval větší velikost fondu připojení.

    Otevřete soubor Limits. conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Přidat nebo upravit následující řádky:

    ```
    * - nofile 100000
    ```

* **Zadat klíč oddílu v zápisech bodů**

    Chcete-li zlepšit výkon zápisů bodů, zadejte klíč oddílu položky v volání rozhraní API bodu zápisu, jak je znázorněno níže:

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

    # <a name="sync"></a>[Synchronizovat](#tab/api-sync)

    Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

    --- 

    místo poskytování pouze instance položky, jak je znázorněno níže:

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

    # <a name="sync"></a>[Synchronizovat](#tab/api-sync)

    Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

    --- 

    Druhá je podporována, ale přidá do aplikace latenci. sada SDK musí položku analyzovat a extrahovat klíč oddílu.

## <a name="indexing-policy"></a>Zásada indexování
 
* **Vyloučení nepoužívaných cest z indexování za účelem zrychlení zápisu**

    Pomocí cest indexování (setIncludedPaths a setExcludedPaths) můžete určit, které cesty dokumentů zahrnout nebo vyloučit z indexování. Azure Cosmos DB Použití cest indexování může nabízet lepší výkon zápisu a nižší indexové úložiště pro scénáře, ve kterých jsou vzory dotazů předem známé, protože náklady na indexování jsou přímo ve vztahu k počtu indexovaných jedinečných cest. Například následující kód ukazuje, jak zahrnout a vyloučit celé oddíly dokumentů (označované také jako podstrom) z indexování pomocí zástupného znaku "*".

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

    Další informace najdete v tématu [Azure Cosmos DB zásady indexování](index-policy.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

* **Měření a optimalizace pro nižší jednotky žádostí za sekundu použití**

    Azure Cosmos DB nabízí bohatou sadu databázových operací, včetně relačních a hierarchických dotazů s UDF, uloženými procedurami a triggery – to vše v dokumentech v rámci kolekce databáze. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, V/V a paměti, které jsou potřeba k dokončení operace. Místo toho, abyste si vymysleli a spravovali hardwarové prostředky, si můžete představit jednotku žádosti (RU) jako jednu míru prostředků potřebných k provádění různých databázových operací a zpracování požadavků aplikace.

    Propustnost se zřizuje na základě počtu [jednotek žádostí](request-units.md) nastavených pro každý kontejner. Spotřeba jednotky požadavku se vyhodnotí jako sazba za sekundu. Aplikace, které překračují sazbu jednotky zřízené žádosti pro svůj kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň kontejneru. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit svou propustnost tím, že zřizujete další jednotky žádostí.

    Složitost dotazu ovlivňuje počet spotřebovaných jednotek požadavků pro určitou operaci. Počet predikátů, povaha predikátů, počet UDF a velikost zdrojové sady dat ovlivňují náklady na operace dotazů.

    Pokud chcete změřit režii jakékoli operace (vytvořit, aktualizovat nebo odstranit), zkontrolujte, že se v hlavičce [x-MS-Request-poplatek](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) měří počet jednotek žádostí spotřebovaných těmito operacemi. Můžete se také podívat na ekvivalentní vlastnost RequestCharge v ResourceResponse \<T> nebo FeedResponse \<T> .

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

    # <a name="sync"></a>[Synchronizovat](#tab/api-sync)

    Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

    --- 

    Poplatek za požadavek vrácený v této hlavičce je zlomek zřízené propustnosti. Pokud jste například zřídili 2000 RU/s, a pokud předchozí dotaz vrátí 1000 1 KB-Documents, náklady na operaci jsou 1000. V takovém případě se server během jedné sekundy připadá pouze na dva takové požadavky, než frekvence omezí následné požadavky. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek požadavků](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Omezení rychlosti zpracování/počet požadavků je moc velký.**

    Když se klient pokusí překročit rezervovanou propustnost účtu, neexistují žádné snížení výkonu na serveru a žádné využití kapacity propustnosti mimo rezervovanou úroveň. Server v tuto chvíli ukončí požadavek pomocí RequestRateTooLarge (kód stavu HTTP 429) a vrátí hlavičku [x-MS-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) , která indikuje, jak dlouho (v milisekundách) musí uživatel počkat, než se znovu pokusí o požadavek.

    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```

    Sady SDK mají implicitně zachytit tuto odpověď, a to v souladu s hlavičkou, která je určená serverem, a zkuste žádost zopakovat. Pokud k účtu nepoužíváte souběžně více klientů, další pokus bude úspěšný.

    Pokud máte více než jednoho klienta, který se konzistentně pracuje konzistentně nad sazbou požadavku, nemusí být výchozí počet opakování nastavený na hodnotu 9 interně klientem. v takovém případě klient vyvolá *CosmosClientException* se stavovým kódem 429 do aplikace. Výchozí počet opakování se dá změnit pomocí setRetryOptions v instanci ConnectionPolicy. Ve výchozím nastavení se *CosmosClientException* se stavovým kódem 429 vrátí po kumulativní čekací době 30 sekund, pokud požadavek nadále funguje nad sazbou požadavku. K tomu dojde i v případě, že aktuální počet opakování je menší než maximální počet opakování, výchozí hodnota je 9 nebo uživatelem definovaná hodnota.

    I když automatizované chování při opakování pomáhá zlepšit odolnost a použitelnost většiny aplikací, může při měření latence docházet k lichá při provádění srovnávacích testů, zejména při měření latence. Latence zjištěná klientem bude špička, pokud experiment narazí na omezení serveru a způsobí, že se klientská sada SDK tiše znovu pokusí. Aby se zabránilo špičkám latence během experimentů s výkonem, změřte poplatky vracené jednotlivými operacemi a zajistěte, aby požadavky byly v provozu pod rezervovanými sazbami požadavků. Další informace najdete v tématu [jednotky žádostí](request-units.md).

* **Návrh pro menší dokumenty pro vyšší propustnost**

    Poplatek za požadavek (náklady na zpracování požadavku) dané operace se přímo koreluje s velikostí dokumentu. Operace s velkým objemem dokumentů se při používání malých dokumentů dotýkají více než operací. V ideálním případě bude vaše aplikace a pracovní postupy mít velikost vaší položky ~ 1 KB nebo podobné pořadí nebo velikost. U velkých položek aplikací citlivých na latenci by se mělo vyhýbat, že vaše aplikace bude zpomalovat dokumenty s více MB.

## <a name="next-steps"></a>Další kroky

Další informace o návrhu aplikace pro škálování a vysoký výkon najdete v tématu [dělení a škálování v Azure Cosmos DB](partitioning-overview.md).
