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
ms.openlocfilehash: 2600565493a334c7227e5c0d67a5808f30751108
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261063"
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

* Microsoft.Azure.DocumentDB, verze 1.13.1 nebo novější 
* Newtonsoft.Json, verze 9.0.1 nebo novější

Poté nainstalujte [balíček Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) a její zahrnutí jako odkaz.

K implementaci knihovna informačního kanálu procesoru změn, je nutné provést následující:

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

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Jaké jsou různé způsoby, můžete si přečíst změnu kanálu? a kdy používat jednotlivé metody?

Existují tři možnosti, vám umožní číst informační kanál změn:

* **[Pomocí Azure Cosmos DB SQL rozhraní API .NET SDK](#sql-sdk)**
   
   Pomocí této metody získáte nízkou úroveň ovládacího prvku na změnu informačního kanálu. Můžete spravovat kontrolního bodu, má přístup konkrétní oddíl klíče atd. Pokud máte více čtenářů, můžete použít [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) čtení rozdělovat do různých vláknech nebo různých klientů. .

* **[Pomocí Azure Cosmos DB změnu kanálu procesoru knihovny](#change-feed-processor)**

   Pokud chcete využít spoustu složitosti změnu kanálu k můžete změnit informačního kanálu procesoru knihovny. Tato knihovna skryje spoustu složitost, ale stále nabízí úplnou kontrolu na změnit informačního kanálu. Tato knihovna používá [vzor pozorovatel](https://en.wikipedia.org/wiki/Observer_pattern), volá funkci pro zpracování sady SDK. 

   Pokud máte vysoké propustnosti změnu kanálu, můžete vytvořit instanci více klientů ke čtení změnu informačního kanálu. Vzhledem k tomu, že používáte "Změna knihovny informačního kanálu procesor", ho bude automaticky rozdělit zatížení mezi různých klientů. Nemusíte provádět žádné kroky. Zpracovává všechny složitost sady SDK. Ale pokud budete chtít mít vlastní nástroj pro vyrovnávání zatížení, potom můžete implementovat IParitionLoadBalancingStrategy pro vlastní oddíl strategie. Implementujte IPartitionProcessor – vlastní zpracování změny na oddíl. Sada SDK a může zpracovat rozsahu oddílu ale pokud chcete zpracovat klíč oddílu konkrétní pak budete muset použít sady SDK pro rozhraní API pro SQL.

* **[Pomocí Azure Functions](#azure-functions)** 
   
   Poslední možnost Funkce Azure je nejjednodušší možnost. Doporučujeme používat tuto možnost. Když vytvoříte aktivační procedury Azure Cosmos DB v aplikaci Azure Functions, vyberte kolekci Azure Cosmos DB pro připojení k a funkce se aktivuje vždy, když ke změně do kolekce. sledování [obrazovky přetypování](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) používání Azure fungovat a změňte informačního kanálu

   Aktivační události lze vytvořit na portálu Azure Functions na portálu Azure Cosmos DB nebo prostřednictvím kódu programu. Visual Studio a VS Code má podpory pro zápis funkce Azure. Můžete napsat a ladění kódu na pracovní ploše a pak nasadit funkci s jedním kliknutím. Další informace najdete v tématu [Cosmos databázi Azure: bez serveru databáze computing pomocí Azure Functions](serverless-computing-database.md) článku.

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Co je řazení dokumentů v kanálu změn?

Změna kanálu dokumenty se dodává v pořadí podle jejich čas změny. Toto pořadí řazení záruku, že se jenom na jeden oddíl.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>Pro účet služby oblast co se stane změnou kanálu když selže převzetí oblasti zápisu? Změnu kanálu také převzetí služeb při selhání? Změna kanálu stále objeví souvislý nebo příčina selhání změní informačního kanálu resetovat?

Ano, změna kanálu bude fungovat na všech operaci ruční převzetí služeb při selhání a je souvislý.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Jak dlouho změnu kanálu zachovat změněných dat. Pokud vlastnost TTL (Time to Live) pro dokument lze nastavit na hodnotu -1?

Změna kanálu se uchová navždy. Pokud data nejsou odstraněna, zůstane v kanálu změnu.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Jak můžete nakonfigurovat Azure functions číst z konkrétní oblasti, jak změnit kanálu je k dispozici ve všech oblastech čtení ve výchozím nastavení?

Aktuálně není možné konfigurovat Azure Functions číst z konkrétní oblasti. V úložišti Azure Functions k nastavení upřednostňovaných oblasti všechny aktivační události a vazeb Azure Cosmos DB není problém Githubu.

Azure Functions využívá výchozí zásady připojení. Režim připojení můžete nakonfigurovat v Azure Functions a ve výchozím nastavení, z oblasti zápisu přečte proto je vhodné společné umísťování Azure Functions na stejné oblasti.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Co je výchozí velikost dávky v Azure Functions?

100 dokumenty v každé vyvolání Azure Functions. Toto číslo je však konfigurovat v souboru function.json. Tady je kompletní [seznam možností konfigurace](../azure-functions/functions-run-local.md). Pokud vyvíjíte místně, aktualizovat nastavení aplikace v rámci [local.settings.json](../azure-functions/functions-run-local.md) souboru.

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Sledování kolekce a čtení změn kanálu, ale zobrazuje, že se nezobrazují se všechna vloženého dokumentu, chybí některé dokumenty. Co se děje tady?

Přesvědčte se, že neexistuje žádná funkce, které čtení stejné kolekce ke stejné kolekci zapůjčení. Se stalo s mi a později realizována, že chybí dokumenty se zpracovávají Moje jiné Azure funkce, která rovněž používá stejný zapůjčení.

Proto pokud vytváříte několik Azure Functions číst stejné změnit informačního kanálu, pak se musí použít jiný zapůjčení kolekce nebo použít nastavení "leasePrefix" sdílet stejné kolekci. Při použití knihovna informačního kanálu procesoru změn můžete spustit více instancí funkce a SDK bude dělit dokumenty mezi různými instancemi automaticky za vás.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Dokument se aktualizují každou sekundu a nezobrazují se všechny změny v Azure Functions naslouchá. Chcete-li změnit informačního kanálu.

Změna hlasování Azure funkce informačního kanálu pro každých 5 sekund, budou ztraceny všechny změny provedené mezi 5 sekund. Azure Cosmos DB ukládá jenom jednu verzi pro každých 5 sekund, zobrazí se 5. Změna na dokumentu. Ale pokud chcete přejít níže 5 sekund a chcete dotazování změnu kanálu za sekundu, můžete nakonfigurovat čas dotazování "feedPollTime", najdete v části [Azure Cosmos DB vazby](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). Je definována v milisekundách výchozí hodnota je 5000. Pod 1 sekunda je možné, ale není vhodné, jak se spustí, zápis víc Procesorových.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>Po vložení dokumentu v kolekci Mongo rozhraní API, ale při doručení dokumentu v kanálu změnu, zobrazuje hodnotu jiné id. Co je problém tady?

Kolekce je kolekce Mongo rozhraní API. Pamatujte si, že změna kanálu je pro čtení pomocí klienta SQL a serializuje položky do formátu JSON. Z důvodu JSON formátování, MongoDB, bude mít klienti ve formátu neshody mezi dokumenty formátu BSON a JSON změnu informačního kanálu. Vidíte je reprezentace BSON dokument ve formátu JSON. Pokud používáte binárních atributů v účtech Mongo, jsou převést na JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Existuje způsob, jak řídit změnu kanálu jen pro aktualizace a není vloží?

Není v současné době ale tato funkce je na plán. V současné době můžete přidat softwarové značky na dokumentu pro aktualizace.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Existuje způsob, jak získat odstranění v kanálu změny?

Změna kanálu není aktuálně protokolu odstraní. Změna kanálu průběžně zlepšují a tato funkce je na plán. V současné době můžete přidat softwarové značky na dokumentu pro odstranění. Přidání atributu na dokumentu názvem "odstraněné" a nastavte na hodnotu "true" a nastavte hodnotu TTL na dokumentu tak, aby mohl být odstraněn automaticky.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Může číst změnu kanálu pro historické dokumentů (například dokumenty, které byly přidány zpět 5 let)?

Ano, pokud se neodstraní dokumentu si můžete přečíst změnu informačního kanálu, pokud je to počátek vaší kolekce.

### <a name="can-i-read-change-feed-using-javascript"></a>Může číst informační kanál změn pomocí jazyka JavaScript

Ano, Node.js SDK počáteční podpora pro změnu kanálu nedávno přidali. Lze použít, jak je znázorněno v následujícím příkladu prosím aktualizace documentdb modul na aktuální verzi před spuštěním kódu:

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
