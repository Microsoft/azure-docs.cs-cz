---
title: Podpora práce s Změna kanálu ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Pomocí podpory informačního kanálu změn služby Azure Cosmos DB sledovat změny v dokumentech a provádět zpracování jako aktivační události na základě událostí a průběžná mezipaměti a analytické systémy.
keywords: kanálu změn
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: e53f1e62b9265d2eec2f49537cc05c865e1436f3
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902958"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Práce s změnu podpora kanálu ve službě Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/introduction.md) je rychlé a flexibilní globálně replikovat databázi, skvěle se hodí pro IoT, hraní her, maloobchod a provozní protokolování aplikací. Běžný vzor návrhu v těchto aplikacích je použít změny v datech aktivovala další akce. Tyto další akce může být jedno z následujících akcí: 

* Při vložení nebo upravit dokumentu, která aktivuje upozornění nebo volání rozhraní API.
* Stream zpracování pro IoT nebo provádění analýz.
* Přesun dat další synchronizaci s mezipaměti, vyhledávací web nebo datového skladu nebo archivaci dat do studeného úložiště.

**Podpora kanálu změn** ve službě Azure Cosmos DB vám umožní sestavovat efektivní a škálovatelné řešení pro každou z těchto vzorců, jak je znázorněno na následujícím obrázku:

![Použití změn databáze Azure Cosmos DB informační kanál k založený na událostech výpočetní scénáře a analýzy v reálném čase power](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Podpora kanálu změn se poskytuje pro všechny datové modely a kontejnerů ve službě Azure Cosmos DB. Kanál změn, ale je pro čtení pomocí nástroje SQL client a položky serializuje do formátu JSON. Z důvodu formátování JSON MongoDB klientů bude docházet k neshodě mezi dokumenty ve formátu BSON a JSON ve formátu kanálu změn.

V následujícím videu Azure Cosmos DB programový manažer Andrew Liu ukazuje, jak kanálu změn databáze Azure Cosmos DB funguje.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>Jak změnit informační kanál k práci?

Podpora ve službě Azure Cosmos DB funguje kanálu změn prostřednictvím naslouchání na kolekci Azure Cosmos DB k nějakým změnám. Potom vypíše seřazený seznam dokumentů, které byly změněny v pořadí, ve kterém byly změněny. Změny jsou trvalé, může být zpracována asynchronně a postupně a výstupu mohou být distribuovány na jeden nebo více příjemců pro paralelní zpracování. 

Kanál třemi různými způsoby, změn si můžete přečíst, jak je popsáno dále v tomto článku:

*   [Pomocí služby Azure Functions](#azure-functions)
*   [Pomocí služby Azure Cosmos DB SDK](#sql-sdk)
*   [Použití změn databáze Azure Cosmos DB kanálu knihovny procesoru](#change-feed-processor)

Kanál změn je k dispozici pro každým rozsahem klíče oddílu v rámci kolekce dokumentů a proto mohou být distribuovány na jeden nebo více příjemců pro paralelní zpracování jak je znázorněno na následujícím obrázku.

![Distribuované zpracování kanálu změn služby Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Další podrobnosti:
* Kanál změn je povoleno standardně pro všechny účty.
* Můžete použít vaše [zřízená propustnost](request-units.md) v oblasti pro zápis nebo jakékoli [čtení oblasti](distribute-data-globally.md) čtení z kanálu změn, stejně jako všechny ostatní operace služby Azure Cosmos DB.
* Kanál změn zahrnuje operace INSERT a update operace provedené na dokumenty v kolekci. Můžete zaznamenat odstraní nastavením příznaku "obnovitelného odstranění" v rámci dokumentů místo odstraní. Alternativně můžete nastavit dobu konečná platnosti pro dokumentů prostřednictvím [interval TTL, ZÍSKÁ možnost](time-to-live.md), například 24 hodin a použijte hodnotu této vlastnosti k zachycení odstraní. Pomocí tohoto řešení je nutné zpracovat změny v časovém intervalu kratší než interval TTL, ZÍSKÁ dobu vypršení platnosti.
* Jednotlivé změny do dokumentu se zobrazí pouze jednou v kanálu změn a klienti spravovat své logiky vytváření kontrolních bodů. Knihovnou change feed processor automatické vytváření kontrolních bodů a "alespoň jednou" poskytuje sémantiku.
* Protokol změn je součástí pouze poslední změny pro daný dokument. Přechodných změn nemusí být k dispozici.
* Kanál změn je řazen cyklicky úpravy v rámci každé hodnotu klíče oddílu. Není zaručeno pořadí napříč hodnotami klíče oddílu.
* Změny můžete synchronizované z libovolný bod za běhu, to znamená, neexistuje žádné pevné data uchovávají, pro které změny jsou k dispozici.
* Změny jsou dostupné v blocích rozsahů klíče oddílů. Tato funkce umožňuje, aby změny z rozsáhlých kolekcí pro paralelní zpracování více příjemců/servery.
* Aplikace můžou požádat o více kanálů změnit současně ve stejné kolekci.
* ChangeFeedOptions.StartTime slouží k poskytování počátečního bodu, například, najít odpovídající danému čas token pro pokračování. Token ContinuationToken, je-li zadána, wins přes hodnoty StartTime a StartFromBeginning. Přesnost ChangeFeedOptions.StartTime je přibližně 5 sekund. 

## <a name="use-cases-and-scenarios"></a>Scénáře a případy použití

Kanál změn umožňuje efektivní zpracování velkých datových sad s velký počet zápisů a nabízí alternativu k dotazování celou datovou sadu k identifikaci, co se změnilo. 

Například se změnou datového kanálu, můžete provádět následující úlohy efektivně:

* Aktualizujte mezipaměť, index vyhledávání nebo datový sklad s daty uloženými ve službě Azure Cosmos DB.
* Implementace dat na úrovni aplikace vrstvení a archivace, to znamená, ukládání "horkých dat." ve službě Azure Cosmos DB a stáří "studených dat" na [Azure Blob Storage](../storage/common/storage-introduction.md) nebo [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Provádět nula dolů čas migrace na jiný účet služby Azure Cosmos DB pomocí jiné schéma rozdělení oddílů.
* Implementace [lambda kanály v Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) pomocí služby Azure Cosmos DB. Azure Cosmos DB poskytuje řešení škálovatelná databáze, které lze zpracovat příjem dat a dotazu a implementace architektury lambda s nízkou celkových nákladů na vlastnictví. 
* Zobrazí data události ze zařízení, senzorů, infrastruktury a aplikací a ukládat zpracování těchto událostí v reálném čase pomocí [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md), nebo [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

Následující obrázek ukazuje, jak změnit lambda kanály, které přijmout a dotaz pomocí služby Azure Cosmos DB můžete použít informační kanály podpory: 

![Azure Cosmos DB na základě lambda kanál pro příjem dat a dotazu](./media/change-feed/lambda.png)

Také v rámci vaší [bez serveru](http://azure.com/serverless) webových a mobilních aplikací, můžete sledovat události, jako jsou například změny vašeho zákazníka profilu, předvolby nebo umístění pro aktivaci určité akce, jako je odesílání nabízených oznámení do zařízení pomocí [Azure Functions](#azure-functions). Pokud používáte službu Azure Cosmos DB k vytvoření hry, je možné, například použití změnit informační kanál k implementaci v reálném čase žebříčky podle skóre, které se od dokončených hry.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Pomocí služby Azure Functions 

Pokud používáte Azure Functions, nejjednodušší způsob, jak připojit ke kanálu změn Azure Cosmos DB je přidání aktivační událost Azure Cosmos DB k aplikaci Azure Functions. Když vytvoříte aktivační událost Azure Cosmos DB v aplikaci Azure Functions, vyberete kolekci Azure Cosmos DB se připojit k a funkce se aktivuje vždy, když do kolekce dojde ke změně. 

Aktivační události lze vytvořit na portálu Azure Functions na portálu služby Azure Cosmos DB nebo prostřednictvím kódu programu. Další informace najdete v tématu [služby Azure Cosmos DB: výpočetní prostředí bez serveru databázi s využitím Azure Functions](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Použití sady SDK

[SQL SDK](sql-api-sdk-dotnet.md) pro službu Azure Cosmos DB poskytuje veškerou sílu ke čtení a správa změn informačního kanálu. Ale s větší možnosti je příliš velké množství odpovědnosti. Pokud chcete spravovat kontrolní body, řešil dokumentu pořadová čísla a mít podrobnou kontrolu nad klíči oddílů, může být potom pomocí sady SDK služby správný přístup.

Tato část vás provede pomocí sady SQL SDK pracovat a změní informačního kanálu.

1. Začněte tím, že v následujících zdrojích, z appconfig. Pokyny k načítání koncového bodu a autorizačního klíče jsou k dispozici v [aktualizovat připojovací řetězec](create-sql-api-dotnet.md#update-your-connection-string).

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

3. Získáte oddíl rozsahy klíčů:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Pro každý rozsah klíče oddílu volejte ExecuteNextAsync:

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
> Místo `ChangeFeedOptions.PartitionKeyRangeId`, můžete použít `ChangeFeedOptions.PartitionKey` k určení jednoho klíče oddílu pro který má být získána změnu informačního kanálu. Například, `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Pokud máte více čtenářům, můžete použít **ChangeFeedOptions** distribuci další zátěže do různých vláken nebo různých klientů.

A to je vše, tyto několik řádků kódu můžete začít čtení kanálu změn. Získáte kompletní kód použitý v tomto článku si [úložiště GitHub se vzorovými](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

V kódu v kroku 4 výše **ResponseContinuation** v poslední řádek obsahuje poslední logické pořadové číslo (položky LSN) dokumentu, který budete používat při dalším přečíst nové dokumenty po tomto Pořadové číslo. S použitím **StartTime** z **ChangeFeedOption** lze rozšířit vaše net získat dokumenty. Ano, pokud vaše **ResponseContinuation** má hodnotu null, ale vaše **StartTime** přejde zpět v čase, pak se zobrazí všechny dokumenty, které se změnily od **StartTime**. Ale pokud vaše **ResponseContinuation** má hodnotu, pak systém vám pomůže se všechny dokumenty od této hodnoty LSN.

Vaše pole kontrolního bodu tak, je pouze zachování pořadové číslo položky pro každý oddíl. Ale pokud nechcete, aby se oddíly, kontrolní body, hodnota LSN, počáteční čas, atd. možnost jednodušší, je použití kanálu knihovny procesoru změn.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Změny pomocí kanálu knihovny procesoru 

[Kanálu změn databáze Azure Cosmos DB knihovny procesoru](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) vám umožňují usnadňuje distribuci zpracování událostí mezi několik příjemců. Tato knihovna usnadňuje čtení změny napříč oddíly a fungujících paralelně několik vláken.

Hlavní výhodou knihovnou change feed processor je, že není nutné spravovat každý oddíl a token pro pokračování a vy nemusíte ručně dotazovat každou kolekci.

Knihovnou change feed processor usnadňuje čtení změn napříč oddíly a fungujících paralelně několik vláken.  Automaticky spravuje čtení změn napříč oddíly, použití mechanismu zapůjčení. Jak vidíte na následujícím obrázku, pokud spustíte dvě klienty, kteří používají kanálu knihovny procesoru změn, rozdělení práce mezi servery. V průběhu zvýšit klienty, zachovat jako podíl práce mezi servery.

![Distribuované zpracování kanálu změn služby Azure Cosmos DB](./media/change-feed/change-feed-output.png)

Byl spuštěn první levé klienta a jeho spuštění, sledování, které všechny oddíly, pak druhého klienta byla spuštěna a potom první opustila některých zapůjčení pro druhého klienta. Jak je vidět, to je dobrý způsob, jak k rozložení práce mezi různé počítače a klienty.

Poznámka: Pokud máte dva bez serveru Azure funkcí monitorování stejné kolekce a pomocí stejného zapůjčení, pak tyto dvě funkce může získat různé dokumenty v závislosti na způsobu knihovny procesoru rozhodne zpracujte oddíly.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Principy změn kanálu knihovny procesoru

Existují čtyři hlavní součásti implementace knihovnou change feed processor: monitorované kolekci, kolekci zapůjčení, procesoru hostitele a spotřebitele. 

> [!WARNING]
> Za vytvoření kolekce se hradí poplatky, protože rezervujete propustnost pro komunikaci aplikace se službou Azure Cosmos DB. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

**Monitorované kolekci:** monitorované kolekci se data, ze kterého je generován kanál změn. Všechny operace vložení a změny v monitorované kolekci se projeví v kanálu změn kolekce. 

**Kolekci zapůjčení:** souřadnice kolekci zapůjčení, zpracování kanálu změn napříč několika pracovních procesů. Samostatné kolekce slouží k uložení zapůjčení s jeden zapůjčení na oddíl. Je výhodné pro uložení této kolekci zapůjčení na jiný účet s oblastí zápisu blíže na kterém je spuštěný kanálu procesoru změn. Objekt zapůjčení obsahuje následující atributy: 
* Vlastník: Určuje hostitele, který vlastní zapůjčení
* Pokračování: Určuje umístění (token pro pokračování) v kanálu pro konkrétní oddíl změn
* Časové razítko: Čas poslední byla aktualizována zapůjčení; časové razítko slouží ke kontrole, jestli zapůjčení je považována za ukončenou 

**Hostitel procesoru:** každého hostitele Určuje, kolik oddíly k procesu v závislosti na tom, kolik instancí hostitelů máte aktivní zapůjčení. 
1.  Při spuštění hostitele, získá zapůjčení vyrovnávat zatížení na všech hostitelích. Hostitel pravidelně obnoví zapůjčení, tak zůstanou aktivní zapůjčení. 
2.  Kontrolní body hostitele poslední token pro pokračování k zapůjčení pro každé čtení. K zajištění bezpečnosti souběžnosti, zkontroluje hostitele ETag pro každou aktualizaci zapůjčení. Podporují se také další strategie zaměřené na kontrolní bod.  
3.  Při vypnutí počítače Hostitel uvolní všechny zapůjčení ale uchovává informace o pokračování, abyste ho mohli obnovit čtení z uložené kontrolního bodu později. 

Počet hostitelů v tuto chvíli nemůže být větší než počet oddílů (zapůjčení).

**Příjemci:** spotřebitelům nebo zaměstnanců, jsou vlákna, které provádějí zpracování kanálu změn inicializuje v každém hostiteli. Každý procesor hostitel může mít několik příjemců. Každý příjemce čte změnu datového kanálu z oddílu, který je přiřazen k a upozorní jeho hostitel změny a vypršení platnosti zapůjčení.

Abyste pochopili, jak tyto čtyři prvky z kanálu změn práce procesoru společně, Pojďme se podívat na příklad na následujícím diagramu. Monitorované kolekci ukládá dokumenty a používá "city" jako klíč oddílu. Vidíme, že modrý oddíl obsahuje dokumenty s polem "city" z "A-E –" a tak dále. Existují dva hostitele, každou s dvěma čtením čtyři oddíly paralelní konzumenty. Šipky zobrazují příjemci čtení z určité místo v kanálu změn. Do prvního oddílu představuje tmavší modrá nepřečtené změny, zatímco světle modrá představuje už další změny v kanálu změn. Hostitele použít kolekci zapůjčení pro uložení hodnoty "pokračování" ke sledování na aktuální pozici čtení pro každého příjemce. 

![Eventprocessorhost změn databáze Azure Cosmos DB pomocí kanálu.](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Práce s kanálu knihovny procesoru změn

Před instalací změnu informačního kanálu balíčku NuGet procesoru, nejprve nainstalujte: 

* Microsoft.Azure.DocumentDB, nejnovější verze.
* Newtonsoft.Json, nejnovější verze

Nainstalujte [balíček Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) a zahrnout ho jako odkaz.

K implementaci knihovnou change feed processor, musíte udělat následující:

1. Implementace **DocumentFeedObserver** objektu, který implementuje **IChangeFeedObserver**.
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

2. Implementace **DocumentFeedObserverFactory**, která implementuje **IChangeFeedObserverFactory**.
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

5. sestavení **ChangeFeedProcessorBuilder** po definování souvisejících objektů 

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

Je to. Po provedení těchto kroků několik dokumentů se začnou zobrazovat do **DocumentFeedObserver.ProcessChangesAsync** metody.

Nad kód je pro účely ilustrace zobrazíte jiný druh objekty a jejich interakce. Je nutné definovat správné proměnné a inicializujte u správné hodnoty. Získáte kompletní kód použitý v tomto článku si [úložiště GitHub se vzorovými](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> Nikdy byste měli mít hlavní klíč ve vašem kódu nebo v konfiguračním souboru, jak je znázorněno výše kódu. Podrobnosti najdete na [použití služby Key Vault k načtení klíčů](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## <a name="faq"></a>Nejčastější dotazy

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Jaké jsou různé způsoby, jak si můžete přečíst Change Feed? a kdy používat jednotlivé metody?

Existují tři možnosti, můžete ke čtení kanálu změn:

* **[Pomocí služby Azure Cosmos DB SQL SDK pro .NET API](#sql-sdk)**
   
   Tímto způsobem získáte nízké úrovni ovládacího prvku na kanál změn. Můžete spravovat kontrolní bod, můžete přistupovat konkrétní oddíl klíčů atd. Pokud máte více čtenářům, můžete použít [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) distribuci další zátěže do různých vláken nebo různých klientů. .

* **[Použití změn databáze Azure Cosmos DB kanálu knihovny procesoru](#change-feed-processor)**

   Pokud chcete, můžete použít knihovnou change feed processor využívající spoustu složitost kanálu změn. Tuto knihovnu skryje hodně složité, ale stále získáte tak úplnou kontrolu v kanálu změn. Tato knihovna používá stejné postupy [vzor pozorovatel](https://en.wikipedia.org/wiki/Observer_pattern), volá funkci pro zpracování sady SDK. 

   Pokud máte kanálu změn vysokou propustnost, můžete vytvořit instanci více klientů ke čtení kanálu změn. Vzhledem k tomu, že používáte "Změna knihovny feed processor", rozdělí automaticky zatížení mezi různé klienty. Není nutné nic dělat. Všem složitosti zařizuje služba SDK. Ale pokud chcete mít vlastní nástroje pro vyrovnávání zatížení, pak můžete implementovat IParitionLoadBalancingStrategy pro vlastní oddíl strategie. Implementujte IPartitionProcessor – pro vlastní zpracování změn na oddíl. Sada SDK a může zpracovat rozsah oddílu ale pokud chcete zpracovat klíč oddílu konkrétní pak budete muset používat sadu SDK pro rozhraní SQL API.

* **[Pomocí služby Azure Functions](#azure-functions)** 
   
   Funkce Azure Functions jako poslední možnost je nejjednodušší možnost. Doporučujeme použít tuto možnost. Když vytvoříte aktivační událost Azure Cosmos DB v aplikaci Azure Functions, vyberete kolekci Azure Cosmos DB se připojit k a funkce se aktivuje vždy, když do kolekce dojde ke změně. sledování [obrazovky přetypování](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) funkce pomocí Azure a kanálu změn

   Aktivační události lze vytvořit na portálu Azure Functions na portálu služby Azure Cosmos DB nebo prostřednictvím kódu programu. Visual Studio a VS Code má skvělou podporu pro zápis funkce Azure functions. Můžete napsat a ladění kódu na vašem počítači a pak nasadit funkci s jedním kliknutím. Další informace najdete v tématu [služby Azure Cosmos DB: výpočetní prostředí bez serveru databázi s využitím Azure Functions](serverless-computing-database.md) článku.

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Co je v pořadí řazení dokumentů v kanálu změn?

Obsahuje dokumenty kanálu změn v pořadí podle jejich čas změny. Toto pořadí řazení je zaručeno pouze na oddíl.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>Pro účet více oblastí co se stane kanál, když selže selhání oblasti zápisu změn? Změnu informační kanál také převzetí služeb při selhání? Stále kanálu změn objevuje souvislý nebo by příčině převzetí služeb při selhání změnit informační kanál resetovat?

Ano, budou fungovat kanálu změn napříč operace ruční převzetí služeb při selhání a bude souvislé.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Jak dlouho kanálu změn zachovat změněná data, je-li nastavit vlastnost hodnota TTL (Time to Live) pro dokument na hodnotu -1?

Kanál změn se uloží navždy. Když se neodstraní data, zůstane v kanálu změn.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Jak lze nakonfigurovat Azure functions ke čtení z konkrétní oblasti, jako kanál změn je k dispozici ve všech oblastech čtení ve výchozím nastavení?

Aktuálně není možné nakonfigurovat Azure Functions ke čtení z konkrétní oblasti. V úložišti Azure Functions k nastavení preferované oblasti všechny aktivační události a vazby Azure Cosmos DB je problém na Githubu.

Služba Azure Functions využívá výchozí zásady připojení. Režim připojení můžete nakonfigurovat ve službě Azure Functions a ve výchozím nastavení, načte z oblasti pro zápis, takže je vhodné umístit společně Azure Functions ve stejné oblasti.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Jaká je výchozí velikost dávek ve službě Azure Functions?

100 dokumentů v každé volání služby Azure Functions. Toto číslo je však možné konfigurovat v souboru function.json. Tady je úplný [seznam možností konfigurace](../azure-functions/functions-run-local.md). Pokud vyvíjíte místně, aktualizovat nastavení aplikace v rámci [local.settings.json](../azure-functions/functions-run-local.md) souboru.

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Sleduji kolekce a čtení jeho změn informačního kanálu, ale vidím, že nemám k dispozici vloženého dokumentu, chybí některé dokumenty. K čemu tady?

Ujistěte se prosím, že neexistuje žádná jiná funkce čtení stejné kolekce ke stejné kolekci zapůjčení. Došlo k sobě a později realizované, že chybí dokumenty jsou zpracovány Moje další Azure functions, což je také pomocí stejné zapůjčení.

Proto pokud vytvoříte více funkcí Azure ke čtení stejné kanálu změn pak musíte použít kolekci různých zapůjčení nebo pomocí konfigurace "leasePrefix" můžete sdílet stejné kolekce. Při použití knihovnou change feed processor můžete spustit více instancí vaší funkce a sady SDK se bude dělit dokumenty mezi různými instancemi automaticky za vás.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Dokument se aktualizuje každou sekundu a nemám k dispozici všechny změny ve službě Azure Functions naslouchání kanálu změn.

Azure Functions hlasování změnit informační kanál pro každých 5 sekund, tak, aby byly ztraceny všechny změny provedené od 5 sekund. Azure Cosmos DB ukládá pouze jednu verzi pro každých 5 sekund, 5. Změna se zobrazí v dokumentu. Nicméně, pokud chcete přejít níže 5 sekund a chcete dotazovat každou sekundu kanálu změn, můžete nakonfigurovat čas dotazování "feedPollTime", naleznete v tématu [vazby Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). Je definován v milisekundách, s výchozí 5000. Nižší než 1 sekundu je možné ale není žádoucí, jak začne vypalování další procesor.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>Vložený dokument v kolekci rozhraní Mongodb API, ale když obdržím dokumentu v kanálu změn, zobrazuje hodnotu jiné id. Co je špatně tady?

Vaše kolekce je kolekce rozhraní Mongodb API. Nezapomeňte, že kanál změn je pro čtení pomocí nástroje SQL client a serializuje položky do formátu JSON. Z důvodu formátování JSON MongoDB klientů bude docházet k neshodě mezi dokumenty ve formátu BSON a JSON ve formátu kanálu změn. Vidíte je reprezentace dokument formátu BSON ve formátu JSON. Pokud používáte binární atributy v účtech Mongo, jsou převedeny na formát JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Existuje způsob, jak řídit kanálu pouze pro aktualizace změn a ne vloží?

Není dnes ale tato funkce je v plánu. V současné době můžete přidat softwarové značky pro dokument pro aktualizace.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Existuje způsob, jak získat odstraní v kanálu změn?

Kanál změn není aktuálně protokolu odstraní. Kanál změn je průběžně zdokonaluje a tato funkce je v plánu. V současné době můžete přidat softwarové značky pro dokument k odstranění. Přidáte atribut v dokumentu nazývá "odstraněné" a nastavte na hodnotu "true" a nastavit hodnotu TTL v dokumentu tak, aby se dají automaticky odstranit.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Může číst kanálu pro historické dokumentů (například dokumenty, které byly přidány zpět 5 let) změn?

Ano, pokud se neodstraní dokumentu si můžete přečíst tato změna kanálu až na hodnotu počátek vaší kolekce.

### <a name="can-i-read-change-feed-using-javascript"></a>Může číst kanálu změn pomocí jazyka JavaScript

Ano, základem je podpora pro kanál změn sady Node.js SDK nedávno přidaly. Můžete použít, jak je znázorněno v následujícím příkladu prosím aktualizace modulu documentdb na aktuální verzi před spuštěním kódu:

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

### <a name="can-i-read-change-feed-using-java"></a>Může číst kanálu změn pomocí Javy

Knihovna Java ke čtení kanálu změn je k dispozici v [úložiště Github](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). Aktuálně Java knihovna je však několik verzí za knihovny .NET. Obě tyto knihovny bude brzy synchronizované.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Můžete použít _etag, _lsn nebo _ts pro interní účetnictví, které můžu získat v reakci?

Formát _etag je interní a by neměl jsou na ní závislé (ne analyzuje) vzhledem k tomu, že můžete kdykoli změnit.
_ts je vytvoření nebo úprava časové razítko. Můžete použít _ts chronologickém porovnání.
je _lsn je id dávky, která se přidá pouze pro kanál změn, představuje id transakce z úložiště... Stejné _lsn může mít mnoho dokumenty.
Ještě jedna věc si uvědomit, značku ETag na FeedResponse se liší od _etag, který se zobrazí v dokumentu. _etag je interní identifikátor a používá se souběžností, obsahuje informace o verzi dokumentu a značka ETag pro pořadí úloh informačního kanálu.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Nepřidává žádné další poplatky čtení kanálu změn?

Bude se vám účtovat pro RU spotřebovaných to znamená přesun dat do a z kolekce Azure Cosmos DB vždy spotřebovávat RU. Uživatelé budou účtovat RU spotřebovaných kolekci zapůjčení.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Může číst několik Azure Functions kanálu změn jednu kolekci?

Ano. Víc funkcí Azure můžete čtení kanálu změn stejné kolekce. Azure Functions je však nutné mít samostatné leaseCollectionPrefix definované.

### <a name="should-the-lease-collection-be-partitioned"></a>By měl být dělené kolekci zapůjčení?

Ne, lze napravit kolekci zapůjčení. Není potřeba zapůjčení dělené kolekce a aktuálně nepodporuje.

### <a name="can-i-read-change-feed-from-spark"></a>Může číst změny ze Spark?

Ano, je to možné. Podrobnosti najdete na [konektor služby Azure Cosmos DB Spark](spark-connector.md). Tady je [obrazovky přetypování](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) znázorňující, jak můžete zpracovávat jako strukturovaný stream kanálu změn.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Pokud zpracování kanálu s využitím Azure Functions změn Řekněme, že batch 10 dokumentů a átovat dokument 7 se zobrazí chyba. V takovém případě poslední tři dokumenty nejsou zpracovány, jak můžete začít zpracování z neúspěšných dokumentu (např.) dokument 7) ve své další informační kanál?

Doporučený model chybu zpracovat, je zabalit svůj kód pomocí bloku try-catch. Zachytávat chyby a daný dokument umístit do fronty (nedoručené zprávy) a pak definovat logiku a pracují s dokumenty, u kterých došlo k chybě. Pomocí této metody Pokud máte služby batch dokument 200 a pouze jeden dokument se nepovedlo, není nutné zbavovat celý batch.

V případě chyby by neměl rewind bodu vrácení zpět na začátek jinak je se můžete stále se zobrazuje tyto dokumenty z kanálu změn. Nezapomeňte, že změna informačního kanálu zachová poslední konečné snap snímek dokumenty, protože jste to vy, může dojít ke ztrátě předchozího snímku v dokumentu. kanál změn udržuje pouze jednu poslední verzi dokumentu a mezi mohou jiné procesy pocházet a změna dokumentu.

Jak zachovat opravu kódu, bude brzy najít žádné dokumenty fronty nedoručených zpráv.
Služba Azure Functions je automaticky volána informačního kanálu systémem změny a kontrolní bod atd je interně spravuje funkce Azure functions. Pokud chcete vrátit zpět kontrolní bod a řízení všech aspektů, měli byste zvážit použití řešení change kanálu procesoru SDK.


## <a name="next-steps"></a>Další postup

Další informace o používání služby Azure Cosmos DB s využitím Azure Functions najdete v části [služby Azure Cosmos DB: výpočetní prostředí bez serveru databázi s využitím Azure Functions](serverless-computing-database.md).

Další informace o použití kanálu knihovny procesoru změn použijte následující prostředky:

* [Informace o stránce](sql-api-sdk-dotnet-changefeed.md) 
* [Balíček Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Vzorový kód ukazuje kroky 1 až 6 výše](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Další ukázky na Githubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Další informace o použití kanálu pomocí sady SDK změn použijte následující prostředky:

* [Stránka informace o sadě SDK](sql-api-sdk-dotnet.md)
