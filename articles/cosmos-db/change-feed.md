---
title: Práce se změnami kanálu podpory v Azure Cosmos DB | Microsoft Docs
description: Použijte Azure Cosmos DB změnu informačního kanálu podporu sledování změn v dokumentech a provádět na základě událostí zpracování jako aktivační události a průběžná aktualizace mezipaměti a analýzy systémy.
keywords: Změna kanálu
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: f0a646591811e7c965ad7de5201913a43cae54fc
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715169"
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

1.  [Pomocí Azure Functions](#azure-functions)
2.  [Pomocí Azure Cosmos DB SDK](#rest-apis)
3.  [Pomocí knihovny Azure Cosmos DB změnu kanálu procesoru](#change-feed-processor)

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

<a id="rest-apis"></a>
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

Ano pole kontrolní bod je právě uchovávání pořadové číslo položky pro každý oddíl. Ale pokud nechcete, aby jak nakládat s oddíly, kontrolní body, pořadové číslo položky, počáteční čas, atd. jednodušší možnost je použít knihovna procesoru změn informačního kanálu.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Použití knihovny změnu kanálu procesoru 

[Knihovny Azure Cosmos DB změnu kanálu procesoru](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) můžete snadno distribuovat zpracování událostí napříč více příjemců. Tato knihovna zjednodušuje čtení změny mezi oddílů a paralelně fungujících více vláken.

Hlavní výhodou knihovny změnu kanálu procesoru je, že nemusíte spravovat každý oddíl a token pokračování a vy nemusíte ručně dotazování každou kolekci.

Knihovna změnu kanálu procesoru zjednodušuje čtení změny mezi oddílů a paralelně fungujících více vláken.  Automaticky spravuje čtení změny napříč oddíly používající mechanismus zapůjčení. Jak vidíte na následujícím obrázku, pokud spustíte dvě klientů, které používají knihovnu změnu kanálu procesoru jejich rozdělte práci mezi sebou. Při dalším zvýšit klientů zachovat jako podíl práci mezi sebou.

![Distribuované zpracování změn Azure Cosmos DB kanálu](./media/change-feed/change-feed-output.png)

Levé klient byl spuštěn první a jeho spuštění, monitorování, které se všechny oddíly, pak klient druhý byla spuštěna a potom první umožní přejít některých zapůjčení pro druhý klienta. Jak vidíte, to je dobrý způsob, jak rozložení práce mezi různé počítače a klienty.

Poznámka: Pokud máte dva bez serveru Azure funkcí monitorování stejné kolekce a pomocí stejné zapůjčení, pak dvě funkce může dojít k různých dokumenty v závislosti na způsobu knihovně procesoru rozhodne zpracujte oddíly.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Principy knihovně změnu kanálu procesoru

Existují čtyři hlavní součásti implementace změnu kanálu procesoru: monitorovaných kolekce, kolekce zapůjčení, procesoru hostitele a uživatelé. 

> [!WARNING]
> Za vytvoření kolekce se hradí poplatky, protože rezervujete propustnost pro komunikaci aplikace se službou Azure Cosmos DB. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

**Monitorované kolekci:** monitorovaných kolekce je dat, ze které se generuje změnu informačního kanálu. Všechny operace INSERT a změny monitorovaných kolekce se projeví v informačním kanálu změnu kolekce. 

**Kolekce zapůjčení:** souřadnice kolekce zapůjčení zpracování změn kanálu napříč více pracovníků. Samostatné kolekce slouží k uložení zapůjčení jeden zapůjčení na jeden oddíl. Je výhodné pro uložení této kolekce zapůjčení na jiný účet s oblasti zápisu blíže k se spuštěným systémem změnu kanálu procesoru. Objekt zapůjčení obsahuje následující atributy: 
* Vlastník: Určuje hostitele, který vlastní zapůjčení
* Pokračování: Určuje pozici (token pokračování) v kanálu pro určitý oddíl změn
* Časové razítko: Čas poslední zapůjčení byla aktualizována; časové razítko slouží ke kontrole, jestli zapůjčení je považována za ukončenou 

**Procesor hostitele:** každého hostitele Určuje, kolik oddíly procesu na základě kolik instancí hostitelů mají aktivní zapůjčení. 
1.  Po spuštění hostitele získá zapůjčení vyrovnávat zatížení ve všech hostitelích. Hostitel pravidelně obnovuje zapůjčení, aby zůstala aktivní zapůjčení. 
2.  Kontrolní body hostitele poslední token pokračování jeho zapůjčení pro každou přečíst. K zajištění bezpečnosti souběžnosti, zkontroluje hostitele ETag pro jednotlivé aktualizace zapůjčení. Jsou podporovány také další strategie kontrolního bodu.  
3.  Při vypnutí počítače Hostitel uvolní všechny zapůjčení, ale zachová pokračování informace, takže ho můžete obnovit čtení z uložené kontrolního bodu později. 

V tuto chvíli nemůže být větší než počet oddílů (zapůjčení) počtu hostitelů.

**Příjemci knihovny:** příjemci nebo pracovníci, jsou vláken, které provádějí změnu kanálu zpracování iniciovaná každého hostitele. Každý hostitel procesoru může mít více příjemců. Jednotliví spotřebitelé přečte změnu kanálu z oddílu, který je přiřazen k a upozorní jeho hostitel změny a platnost zapůjčení.

Abyste pochopili, jak tyto čtyři prvky změnu kanálu procesoru spolupracují, podíváme se na příklad na následujícím diagramu. Monitorované kolekci ukládá dokumenty a používá "city" jako klíč oddílu. Vidíte, že blue oddíl obsahuje dokumentů s poli "city" od "A-E" a tak dále. Existují dva hostitele, každý se dvěma spotřebiteli čtení ze čtyř oddílů souběžně. Šipky zobrazují příjemci čtení z na určité místo v změn informačního kanálu. Do prvního oddílu představuje tmavšího blue nepřečtená změny při světla blue představuje již čtení změny při změně kanálu. Hostitele použít k ukládání hodnotu "pokračování" ke sledování aktuální pozici čtení pro každý příjemce kolekci zapůjčení. 

![Pomocí Azure Cosmos DB změnu kanálu procesor hostitele](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Práce s knihovnou změnu kanálu procesoru

Před instalací balíčku NuGet procesoru kanálu změnu, nejprve nainstalujte: 

* Microsoft.Azure.DocumentDB, verze 1.13.1 nebo novější 
* Newtonsoft.Json, verze 9.0.1 nebo novější

Poté nainstalujte [balíček Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) a její zahrnutí jako odkaz.

K implementaci změnu kanálu procesoru knihovny, musíte udělat následující:

1. Implementace **DocumentFeedObserver** objekt, který implementuje **IChangeFeedObserver**.

2. Implementace **DocumentFeedObserverFactory**, který implementuje **IChangeFeedObserverFactory**.

3. V **CreateObserver** metodu **DocumentFeedObserverFacory**, vytváření instancí **ChangeFeedObserver** který jste vytvořili v kroku 1 a obnoví v něm.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. Vytvoření instance **DocumentObserverFactory**.

5. Vytváření instancí **ChangeFeedEventHost**:

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. Zaregistrovat **DocumentFeedObserverFactory** s hostitelem.

Kód pro kroky 4 až 6 je: 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

Je to. Po provedení těchto kroků několik spustí dokumenty, než dorazí do **DocumentFeedObserver ProcessChangesAsync** metoda. Výše uvedený kód v [úložiště GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)

## <a name="next-steps"></a>Další postup

Další informace o databázi Cosmos Azure pomocí Azure Functions najdete v části [Cosmos databázi Azure: bez serveru databáze computing pomocí Azure Functions](serverless-computing-database.md).

Další informace o použití knihovny změnu kanálu procesoru použijte v následujících zdrojích informací:

* [Stránka informací o](sql-api-sdk-dotnet-changefeed.md) 
* [Balíček Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Ukázkový kód zobrazující kroky 1 až 6 výše](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Další ukázky z webu GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Další informace o použití změn kanálu pomocí sady SDK použijte v následujících zdrojích informací:

* [Stránka informace o sadě SDK](sql-api-sdk-dotnet.md)
