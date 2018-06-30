---
title: Práce se změnami kanálu podpory v Azure Cosmos DB | Microsoft Docs
description: Použijte Azure Cosmos DB změnu informačního kanálu podporu sledování změn v dokumentech a provádět na základě událostí zpracování jako aktivační události a průběžná aktualizace mezipaměti a analýzy systémy.
keywords: změnit informačního kanálu
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 6b0aaa075b8b2881e269d79a67e75528d0d9a86a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129854"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Práce se změnami kanálu podpory v Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/introduction.md) je rychlého a flexibilní globálně replikované databáze, dobře hodí pro IoT, hry, maloobchodní a provozní protokolování aplikace. Běžné vzoru návrhu v těchto aplikacích je použití změny dat na ji další akce. Tyto další akce může být jedno z následujících akcí: 

* Když je dokument vložit nebo upravit, která aktivuje oznámení nebo volání rozhraní API.
* Datový proud zpracování pro IoT nebo provádění analýzy.
* Přesun dat další synchronizaci s mezipaměti, vyhledávací web nebo datového skladu nebo archivací dat na studené úložiště.

**Změnu kanálu podporu** v Azure Cosmos DB umožňuje vytvářet efektivní a škálovatelné řešení pro každou z těchto vzory, jak je znázorněno na následujícím obrázku:

![Pomocí Azure Cosmos DB změnu kanálu power analýzu v reálném čase a událostmi řízené výpočetní scénáře](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Změna kanálu podpory se poskytuje pro všechny datové modely a kontejnerů v Azure Cosmos DB. Ale informačního kanálu změn je pro čtení pomocí klienta SQL a serializuje položky do formátu JSON. Z důvodu JSON formátování, MongoDB, bude mít klienti ve formátu neshody mezi dokumenty formátu BSON a JSON změnu informačního kanálu.

V následujícím videu Azure manažer programu DB Cosmos Andrew Liu ukazuje, jak změnit Azure Cosmos DB kanálu funguje.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>Jak změnit kanálu pracovní?

Změníte informačního kanálu podpory v Azure Cosmos DB funguje naslouchá na kolekci Azure Cosmos DB žádné změny. Potom vypíše seřazený seznam dokumenty, které byly změněny v pořadí, ve kterém byly upraveny. Změny jsou nastavené jako trvalé, může být zpracována asynchronně a postupně a výstupu mohou být distribuovány na jeden nebo více příjemce pro paralelní zpracování. 

Jak je popsáno dále v tomto článku si můžete přečíst změnu kanálu třemi různými způsoby:

*   [Pomocí Azure Functions](#azure-functions)
*   [Pomocí Azure Cosmos DB SDK](#sql-sdk)
*   [Pomocí Azure Cosmos DB změnu kanálu procesoru knihovny](#change-feed-processor)

Změna informačního kanálu je k dispozici pro každý oddíl klíče rozsahem v rámci kolekce dokumentů a proto mohou být distribuovány na jeden nebo více příjemce pro paralelní zpracování jak je znázorněno na následujícím obrázku.

![Distribuované zpracování změn Azure Cosmos DB kanálu](./media/change-feed/changefeedvisual.png)

Další podrobnosti:
* Informační kanál změn je povoleno ve výchozím nastavení pro všechny účty.
* Můžete použít vaše [zřízené propustnosti](request-units.md) ve vaší oblasti zápisu ani žádné [číst oblast](distribute-data-globally.md) číst z změnu kanálu, stejně jako všechny ostatní operace Azure Cosmos DB.
* Informační kanál změnu zahrnuje vložení a operace aktualizace provedené na dokumenty v rámci kolekce. Můžete zaznamenat odstranění nastavením příznak "soft odstranění" v rámci dokumentů místo odstranění. Alternativně můžete nastavit konečný platnosti pro dokumentů prostřednictvím [TTL schopností](time-to-live.md), například 24 hodin a použití hodnota této vlastnosti k zachycení odstranění. S tímto řešením je nutné zpracovat změny v časovém intervalu kratší než doba TTL vypršení platnosti.
* Každé změně do dokumentu se zobrazí právě jednou v změnu kanálu a klienti spravovat své logiku vytváření kontrolních bodů. Knihovna informačního kanálu procesoru změn poskytuje automatické vytváření kontrolních bodů a "alespoň jednou" sémantiku.
* Protokol změn je součástí pouze poslední změny u daného dokumentu. Přechodných změn nemusí být k dispozici.
* Změna informačního kanálu je řazen u úpravy v rámci každé hodnotu klíče oddílu. Neexistuje žádné zaručenou objednávka napříč hodnoty klíč oddílu.
* Změny mohou být synchronizovány z jakékoli bodu v čase, tedy neexistuje žádné období uchovávání pevné dat, pro které jsou k dispozici změny.
* Změny jsou k dispozici v bloky rozsahy klíčů oddílů. Tato možnost umožňuje změny z rozsáhlých kolekcí, které mají být zpracovány současně více příjemci nebo servery.
* Aplikace můžete požadovat více informačních kanálů změnu současně na stejné kolekci.
* ChangeFeedOptions.StartTime slouží k poskytování počáteční počáteční bod, třeba, aby najít odpovídající dané času hodin token pro pokračování. ContinuationToken, pokud zadaný, wins přes hodnoty StartTime a StartFromBeginning. Přesnost ChangeFeedOptions.StartTime je ~ 5 sekund. 

## <a name="use-cases-and-scenarios"></a>Případy použití a scénáře

Informační kanál změnu umožňuje efektivní zpracování rozsáhlých datových sad k velkému počtu zápisy a nabízí alternativu k dotazování celá sada dat k identifikaci, co se změnilo. 

Například pomocí změnu kanálu, můžete provádět následující úlohy efektivně:

* Aktualizace mezipaměti, index vyhledávání nebo datového skladu s daty uloženými v databázi Azure Cosmos.
* Aplikační úrovni využití dat vrstvení a archivaci, tedy ukládání "horkých dat." v Azure Cosmos DB a po určité době odstraněny "pomaleji přístupná data" k [Azure Blob Storage](../storage/common/storage-introduction.md) nebo [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Provést nulové době migrací na jiný účet Azure Cosmos DB jiné schéma rozdělení oddílů.
* Implementace [lambda kanály v Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) s Azure Cosmos DB. Azure Cosmos DB poskytuje řešení škálovatelná databáze, které může zpracovat přijímání a dotazů a implementovat lambda architektury s nízkou celkové náklady na vlastnictví. 
* Přijímat a ukládání dat události ze zařízení, senzorů, infrastruktury a aplikace a zpracování těchto událostí v reálném čase s [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md), nebo [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

Následující obrázek ukazuje, jak změnit lambda kanály, které obě ingestování a pomocí dotazu pomocí Azure Cosmos DB informačního kanálu podpory: 

![Azure na základě Cosmos DB lambda kanálu pro přijímání a dotazů](./media/change-feed/lambda.png)

Také v rámci vaší [bez serveru](http://azure.com/serverless) webových a mobilních aplikací, můžete sledovat události, jako jsou například změny do vašeho zákazníka profilu, předvolby nebo umístění pro spuštění určitých akcí, jako je odesílání nabízených oznámení do jejich zařízení pomocí [Azure Functions](#azure-functions). Pokud používáte databázi Cosmos Azure k vytvoření hry, můžete, například použití změnit informačního kanálu v reálném čase tabulky podle skóre z dokončené hry implementovat.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Pomocí Azure Functions 

Pokud používáte Azure Functions, nejjednodušší způsob, jak se připojit k Azure Cosmos DB změnu kanálu je přidat do aplikace Azure Functions aktivační procedury Azure Cosmos DB. Když vytvoříte aktivační procedury Azure Cosmos DB v aplikaci Azure Functions, vyberte kolekci Azure Cosmos DB pro připojení k a funkce se aktivuje vždy, když ke změně do kolekce. 

Aktivační události lze vytvořit na portálu Azure Functions na portálu Azure Cosmos DB nebo prostřednictvím kódu programu. Další informace najdete v tématu [Cosmos databázi Azure: bez serveru databáze computing pomocí Azure Functions](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Použití sady SDK

[SQL SDK](sql-api-sdk-dotnet.md) pro Azure Cosmos DB vám dává všechny ke čtení a správa změn, kanálu. Ale s skvělé power obsahuje příliš velké množství odpovědnosti. Pokud chcete spravovat kontrolní body, řeší dokumentu pořadová čísla a mít podrobnou kontrolu nad klíče oddílů, pak pomocí sady SDK může mít správný přístup.

Tato část vás provede jak používat sadu SDK SQL pro práci s změnu informačního kanálu.

1. Nejdříve si přečtěte následující prostředky z appconfig. Pokyny k načítání koncový bod a autorizační klíč jsou k dispozici v [aktualizovat připojovací řetězec](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Vytvoření klienta následujícím způsobem:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Získáte oddílu rozsahy klíčů:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Volání ExecuteNextAsync pro každý rozsah klíče oddílu:

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

> [!NOTE]
> Místo `ChangeFeedOptions.PartitionKeyRangeId`, můžete použít `ChangeFeedOptions.PartitionKey` zadat jeden oddíl klíč pro který má být získána změnu informačního kanálu. Například, `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Pokud máte více čtenářů, můžete použít **ChangeFeedOptions** čtení rozdělovat do různých vláknech nebo různých klientů.

A je to, tyto několika řádků kódu můžete spouštět čtení změnu informačního kanálu. Kód dokončení použít v tomto článku můžete získat [úložiště GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

V kódu v kroku 4 výše **ResponseContinuation** v poslední řádek obsahuje poslední logické pořadové číslo (položky LSN) dokumentu, který budete používat při příštím číst nové dokumenty po této pořadové číslo. Pomocí **StartTime** z **ChangeFeedOption** rozšířit vaše net získat dokumenty. Ano, pokud vaše **ResponseContinuation** má hodnotu null, ale vaše **StartTime** přejde zpět v čase a zobrazí se všechny dokumenty, které od změnila **StartTime**. Ale pokud vaše **ResponseContinuation** má hodnotu, pak systém získáte všechny dokumenty od tohoto pořadové číslo položky.

Ano pole kontrolní bod je právě uchovávání pořadové číslo položky pro každý oddíl. Ale pokud nechcete, aby jak nakládat s oddíly, kontrolní body, pořadové číslo položky, počáteční čas, atd. jednodušší možnost je použít změnu kanálu procesoru knihovny.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Pomocí změnu kanálu procesoru knihovny 

[Azure Cosmos DB změnu kanálu procesoru knihovny](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) můžete snadno distribuovat zpracování událostí napříč více příjemců. Tato knihovna zjednodušuje čtení změny mezi oddílů a paralelně fungujících více vláken.

Hlavní výhodou knihovna informačního kanálu procesoru změn je, že nemusíte spravovat každý oddíl a token pokračování a vy nemusíte ručně dotazování každou kolekci.

Knihovna informačního kanálu procesoru změn zjednodušuje čtení změny mezi oddílů a paralelně fungujících více vláken.  Automaticky spravuje čtení změny napříč oddíly používající mechanismus zapůjčení. Jak vidíte na následujícím obrázku, pokud spustíte dvě klienty, kteří používají změnu kanálu procesoru knihovny se rozdělují práci mezi sebou. Při dalším zvýšit klientů zachovat jako podíl práci mezi sebou.

![Distribuované zpracování změn Azure Cosmos DB kanálu](./media/change-feed/change-feed-output.png)

Levé klient byl spuštěn první a jeho spuštění, monitorování, které se všechny oddíly, pak klient druhý byla spuštěna a potom první umožní přejít některých zapůjčení pro druhý klienta. Jak vidíte, to je dobrý způsob, jak rozložení práce mezi různé počítače a klienty.

Poznámka: Pokud máte dva bez serveru Azure funkcí monitorování stejné kolekce a pomocí stejné zapůjčení, pak dvě funkce může dojít k různých dokumenty v závislosti na způsobu knihovně procesoru rozhodne zpracujte oddíly.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Principy změn kanálu procesoru knihovny

Existují čtyři hlavní součásti implementace knihovna informačního kanálu procesoru změn: monitorovaných kolekce, kolekce zapůjčení, procesoru hostitele a uživatelé. 

> [!WARNING]
> Za vytvoření kolekce se hradí poplatky, protože rezervujete propustnost pro komunikaci aplikace se službou Azure Cosmos DB. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

**Monitorované kolekci:** monitorovaných kolekce je dat, ze které se generuje změnu informačního kanálu. Všechny operace INSERT a změny monitorovaných kolekce se projeví v informačním kanálu změnu kolekce. 

**Kolekce zapůjčení:** souřadnice kolekce zapůjčení zpracování změn kanálu napříč více pracovníků. Samostatné kolekce slouží k uložení zapůjčení jeden zapůjčení na jeden oddíl. Je výhodné pro uložení této kolekce zapůjčení na jiný účet s oblasti zápisu blíže na kterém je spuštěný změnu kanálu procesoru. Objekt zapůjčení obsahuje následující atributy: 
* Vlastník: Určuje hostitele, který vlastní zapůjčení
* Pokračování: Určuje pozici (token pokračování) v kanálu pro určitý oddíl změn
* Časové razítko: Čas poslední zapůjčení byla aktualizována; časové razítko slouží ke kontrole, jestli zapůjčení je považována za ukončenou 

**Procesor hostitele:** každého hostitele Určuje, kolik oddíly procesu na základě kolik instancí hostitelů mají aktivní zapůjčení. 
1.  Po spuštění hostitele získá zapůjčení vyrovnávat zatížení ve všech hostitelích. Hostitel pravidelně obnovuje zapůjčení, aby zůstala aktivní zapůjčení. 
2.  Kontrolní body hostitele poslední token pokračování jeho zapůjčení pro každou přečíst. K zajištění bezpečnosti souběžnosti, zkontroluje hostitele ETag pro jednotlivé aktualizace zapůjčení. Jsou podporovány také další strategie kontrolního bodu.  
3.  Při vypnutí počítače Hostitel uvolní všechny zapůjčení, ale zachová pokračování informace, takže ho můžete obnovit čtení z uložené kontrolního bodu později. 

V tuto chvíli nemůže být větší než počet oddílů (zapůjčení) počtu hostitelů.

**Příjemci knihovny:** příjemci nebo pracovníci, jsou vláken, které provádějí změnu kanálu zpracování iniciovaná každého hostitele. Každý hostitel procesoru může mít více příjemců. Jednotliví spotřebitelé přečte změnu kanálu z oddílu, který je přiřazen k a upozorní jeho hostitel změny a platnost zapůjčení.

Abyste pochopili, jak tyto čtyři prvky změnu kanálu procesoru pracovní společně, podíváme se na příklad na následujícím diagramu. Monitorované kolekci ukládá dokumenty a používá "city" jako klíč oddílu. Vidíte, že blue oddíl obsahuje dokumentů s poli "city" od "A-E" a tak dále. Existují dva hostitele, každý se dvěma spotřebiteli čtení ze čtyř oddílů souběžně. Šipky zobrazují příjemci čtení z na určité místo v změn informačního kanálu. Do prvního oddílu představuje tmavšího blue nepřečtená změny při světla blue představuje již čtení změny při změně kanálu. Hostitele použít k ukládání hodnotu "pokračování" ke sledování aktuální pozici čtení pro každý příjemce kolekci zapůjčení. 

![Pomocí Azure Cosmos DB změnu kanálu procesor hostitele](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Práce se změnami kanálu procesoru knihovny

Před instalací změnu kanálu balíček NuGet procesoru, nejprve nainstalujte: 

* Microsoft.Azure.DocumentDB, nejnovější verzi.
* Newtonsoft.Json, nejnovější verze

Poté nainstalujte [balíček Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) a její zahrnutí jako odkaz.

K implementaci knihovna informačního kanálu procesoru změn, je nutné provést následující:

1. Implementace **DocumentFeedObserver** objekt, který implementuje **IChangeFeedObserver**.
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. Implementace **DocumentFeedObserverFactory**, který implementuje **IChangeFeedObserverFactory**.
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. Definování *CancellationTokenSource* a *ChangeFeedProcessorBuilder*

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. sestavení **ChangeFeedProcessorBuilder** po definování relevantní objekty 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
            ```

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>Může číst informační kanál změn pomocí Java?

Knihovna Java číst informační kanál změn je k dispozici v [úložiště Github](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). Aktuálně knihovna Java je však několik verzí za knihovny .NET. Obě knihovny bude brzy synchronizované.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Můžete použít _etag, _lsn nebo _ts pro účetnictví, který se zobrazí v reakci?

Formát _etag je interní a nesmí na ní závisí (analyzovat ji) vzhledem k tomu, že můžete kdykoli změnit.
_ts je časové razítko vytvoření nebo úpravy. _Ts můžete použít pro chronologickém porovnání.
je _lsn je id dávky, která je přidána pouze pro informační kanál změnu, představuje id transakce z úložiště... Mnoho dokumenty může mít stejný _lsn.
Jeden krok si uvědomit, značka ETag na FeedResponse se liší od _etag, které se zobrazuje v dokumentu. _etag je interní identifikátor a slouží k concurrency, obsahuje informace o verzi dokumentu a značka ETag se používá pro pořadí úloh informačního kanálu.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Čtení změnu kanálu přidat všechny dodatečné náklady?

Budou se vám účtovat pro RU spotřebované znamená přesun dat do/z Azure Cosmos DB kolekce vždy využívat RU. Uživatelé budou účtovat poplatek za RU spotřebovávají kolekci zapůjčení.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Víc funkcí Azure může číst informační kanál změnu jednu kolekci?

Ano. Víc funkcí Azure může číst informační kanál změnu stejné kolekci. Azure Functions je však nutné mít samostatné leaseCollectionPrefix definované.

### <a name="should-the-lease-collection-be-partitioned"></a>Oddíly kolekci zapůjčení?

Ne, lze opravit zapůjčení kolekce. Není zapotřebí kolekce oddílů zapůjčení a je aktuálně podporované.

### <a name="can-i-read-change-feed-from-spark"></a>Může číst změnu kanálu z Spark?

Ano, je to možné. Najdete v tématu [Azure Cosmos DB Spark konektor](spark-connector.md). Tady je [obrazovky přetypování](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) znázorňující, jak můžete změnit kanálu jako strukturovaný stream zpracovat.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Pokud zpracování změn kanál pomocí Azure Functions vyslovení dávky 10 dokumenty a zobrazí chybová zpráva na 7. dokumentu. V takovém případě poslední tři dokumenty nebudou zpracovány, jak můžete spustit zpracování z dokumentu se nezdařilo (tj 7. dokument) v mé další kanálu?

Ke zpracování chyby, je doporučené vzor zabalit kódu s blok try-catch. Catch – chyba a vložit tento dokument ve frontě (nedoručených zpráv) a pak definovat logiku a pracují s dokumenty, které vytváří chyba. Pomocí této metody Pokud máte dokument 200 batch a pouze jeden dokument se nepovedlo, není nutné throw rychle celý batch.

V případě, že chyby by neměl rewind kontrolní bod zpět na začátku jinak se bude můžete často zobrazuje tyto dokumenty z změnu kanálu. Pamatujte si, že se změny informačního kanálu udržuje poslední snímek konečné snap dokumentů, protože jste to vy, může dojít ke ztrátě předchozí snímek na dokumentu. změnu kanálu zachová jenom jednu poslední verzi dokumentu a v rozmezí můžete jiné procesy pocházet a změnu dokumentu.

Jak zachovat opravě kódu, bude brzy najít žádné dokumenty na frontu nedoručených zpráv.
Azure Functions je automaticky volána informačního kanálu systémem změny a kontrolní bod atd se spravuje interně pomocí funkce Azure. Pokud chcete vrátit zpět kontrolní bod a řídit všechny aspekty, měli byste zvážit, že pomocí změnu kanálu procesoru SDK.


## <a name="next-steps"></a>Další postup

Další informace o databázi Cosmos Azure pomocí Azure Functions najdete v části [Cosmos databázi Azure: bez serveru databáze computing pomocí Azure Functions](serverless-computing-database.md).

Další informace o použití změn kanálu procesoru knihovny použijte následující prostředky:

* [Stránka informací o](sql-api-sdk-dotnet-changefeed.md) 
* [Balíček Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Ukázkový kód zobrazující kroky 1 až 6 výše](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Další ukázky z webu GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Další informace o použití změn kanálu pomocí sady SDK použijte v následujících zdrojích informací:

* [Stránka informace o sadě SDK](sql-api-sdk-dotnet.md)
