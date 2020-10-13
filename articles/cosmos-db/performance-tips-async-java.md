---
title: Tipy ke zvýšení výkonu pro Azure Cosmos DB Async Java SDK v2
description: Seznamte se s možnostmi konfigurace klientů pro zlepšení výkonu Azure Cosmos Database pro Async Java SDK v2
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d925c1387a408d38eb7974a01ebf3ce3386b7e58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067606"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Tipy ke zvýšení výkonu pro Azure Cosmos DB Async Java SDK v2

> [!div class="op_single_selector"]
> * [Sada Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Sada Async Java SDK v2](performance-tips-async-java.md)
> * [Sada Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)


> [!IMPORTANT]  
> Nejedná *se o* nejnovější sadu Java SDK pro Azure Cosmos DB. Projekt byste měli upgradovat na [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) a pak si přečtěte příručku Azure Cosmos DB Java SDK v4 – [Průvodce tipy pro výkon](performance-tips-java-sdk-v4-sql.md). Postupujte podle pokynů v tématu [migrace do Azure Cosmos DB příručka Java SDK v4](migrate-java-v4-sdk.md) a příručka [vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) Guide to upgrade. 
> 
> Tipy ke zvýšení výkonu v tomto článku jsou pouze pro Azure Cosmos DB Async Java SDK v2. Další informace najdete v [poznámkách k verzi](sql-api-sdk-async-java.md)Azure Cosmos DB ASYNC Java SDK v2, [úložišti Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)a Azure Cosmos DB asynchronní [příručce k odstraňování potíží](troubleshoot-java-async-sdk.md) se sadou Java SDK v2.
>

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která bez problémů škáluje zaručenou latenci a propustnost. Nemusíte dělat zásadní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Vertikální navýšení a snížení kapacity je stejně snadné jako volání jednoho rozhraní API nebo volání metody sady SDK. Vzhledem k tomu, že k Azure Cosmos DB je k dispozici prostřednictvím síťových volání, existují optimalizace na straně klienta, které vám při použití [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md)umožní dosáhnout špičkového výkonu.

Takže pokud si vyžádáte "Jak můžu vylepšit výkon databáze?" Vezměte v úvahu následující možnosti:

## <a name="networking"></a>Sítě

* **Režim připojení: použít přímý režim**
    
  Způsob připojení klienta k Azure Cosmos DB má důležité dopady na výkon, zejména z hlediska latence na straně klienta. *ConnectionMode* je klíčovým nastavením konfigurace, které je k dispozici pro konfiguraci klienta *ConnectionPolicy*. Pro Azure Cosmos DB Async Java SDK v2 jsou k dispozici dvě dostupné ConnectionModes:  
      
  * [Brána (výchozí)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  Režim brány je podporovaný na všech platformách SDK a ve výchozím nastavení je nakonfigurovaná možnost. Pokud vaše aplikace běží v podnikové síti s přísnými omezeními brány firewall, je nejlepší volbou režim brány, protože používá standardní port HTTPS a jeden koncový bod.   Kompromis mezi výkonem je ale v tom, že režim brány zahrnuje další směrování sítě při každém čtení nebo zápisu dat do Azure Cosmos DB. Díky tomu přímý režim nabízí lepší výkon kvůli menšímu počtu směrování sítě.
  
  *ConnectionMode* se konfiguruje během vytváření instance *DocumentClient* s parametrem *ConnectionPolicy* .

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **Společné umístění klientů ve stejné oblasti Azure pro výkon**

  Pokud je to možné, umístěte všechny aplikace, které volají Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos. V případě přibližného porovnání se volání Azure Cosmos DB v rámci stejné oblasti dokončí v 1-2 MS, ale latence mezi západním a východním pobřežím USA je >50 ms. Tato latence se může lišit od požadavku na vyžádání v závislosti na trasách, kterou požadavek prochází z klienta na hranici datacentra Azure. Nejnižší možná latence se dosahuje tím, že se zaručí, že se volající aplikace nachází ve stejné oblasti Azure jako koncový bod zřízené Azure Cosmos DB. Seznam oblastí, které jsou k dispozici, najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Obrázek zásad Azure Cosmos DBho připojení" border="false":::

## <a name="sdk-usage"></a>Využití sady SDK

* **Nainstalovat nejnovější sadu SDK**

  Sady Azure Cosmos DB SDK se neustále zdokonalují, aby poskytovaly nejlepší výkon. Pokud chcete zjistit nejnovější sadu SDK a zkontrolovat vylepšení, přečtěte si stránky [poznámky k verzi](sql-api-sdk-async-java.md) Azure Cosmos DB ASYNC Java SDK v2.

* **Použití typu Singleton Azure Cosmos DB klienta po dobu života vaší aplikace**

  Každá instance AsyncDocumentClient je bezpečná pro přístup z více vláken a provádí efektivní správu připojení a ukládání adres do mezipaměti. Aby bylo možné efektivně spravovat správu připojení a lepší výkon díky AsyncDocumentClient, doporučujeme pro celou doménu aplikace používat jednu instanci AsyncDocumentClient.

* **Vyladění ConnectionPolicy**

  Ve výchozím nastavení se požadavky na přímý režim Cosmos DB při použití Azure Cosmos DB asynchronního Java SDK v2 provedou přes protokol TCP. Interně sada SDK používá speciální architekturu přímého režimu pro dynamickou správu síťových prostředků a dosažení nejlepšího výkonu.

  V Azure Cosmos DB Async Java SDK v2 je přímým režimem nejlepší volbou pro zlepšení výkonu databáze s většinou úloh. 

  * ***Přehled přímého režimu***

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Obrázek zásad Azure Cosmos DBho připojení" border="false":::
  
  Architektura na straně klienta pracující v přímém režimu umožňuje předvídatelné využití sítě a multiplexější přístup k replikám Azure Cosmos DB. Výše uvedený diagram ukazuje, jak přímý režim směruje požadavky klienta na repliky v Cosmos DB back-endu. Architektura přímého režimu přiděluje až 10 **kanálů** na straně klienta pro repliku databáze. Kanál je připojení TCP předchází vyrovnávací paměť požadavků, což je 30 požadavků hluboko. Kanály patřící do repliky se dynamicky přiřazují podle potřeby **koncového bodu služby**repliky. Když uživatel vydá požadavek v přímém režimu, **TransportClient** směruje požadavek do správného koncového bodu služby na základě klíče oddílu. Vyrovnávací paměti front požadavků se **vyžadují** před koncovým bodem služby.

  * ***Možnosti konfigurace ConnectionPolicy pro přímý režim***

    V prvním kroku použijte následující doporučené konfigurační nastavení. Pokud v tomto konkrétním tématu narazíte na problémy, obraťte se prosím na [tým Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) .

    Pokud používáte Azure Cosmos DB jako referenční databázi (to znamená, že databáze se používá v mnoha operacích čtení a několika operací zápisu), může být přijatelné nastavit *idleEndpointTimeout* na hodnotu 0 (tj. bez časového limitu).


    | Možnost konfigurace       | Výchozí    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | connectionTimeout          | "PT1M"     |
    | idleChannelTimeout         | "PT0S"     |
    | idleEndpointTimeout        | "PT1M10S"  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receiveHangDetectionTime   | "PT1M5S"   |
    | requestExpiryInterval      | "PT5S"     |
    | requestTimeout             | "PT1M"     |
    | requestTimerResolution     | "PT 0.5 S"   |
    | sendHangDetectionTime      | "PT10S"    |
    | shutdownTimeout            | "PT15S"    |

* ***Tipy pro programování pro přímý režim***

  Přečtěte si článek [řešení potíží s nástrojem](troubleshoot-java-async-sdk.md) Azure Cosmos DB ASYNC Java SDK v2 jako standardní hodnotu pro řešení problémů sady SDK.
  
  Některé důležité tipy pro programování při použití přímého režimu:
  
  * **Použití multithreading ve vaší aplikaci pro efektivní přenos dat TCP** – po vytvoření žádosti by se vaše aplikace měla přihlásit k odběru dat v jiném vlákně. Nedělá se to tak, že vynutí neúmyslnou operaci "poloduplexní" a následné požadavky se zablokují čekáním na odpověď předchozí žádosti.
  
  * **Provádění úloh náročných na výpočetní výkon na vyhrazeném vlákně** – z podobných důvodů na předchozí Tip jsou operace, jako je složité zpracování dat, nejlépe umístěny v samostatném vlákně. Požadavek, který přebírá data z jiného úložiště dat (například pokud vlákno využívá Azure Cosmos DB a datové úložiště Spark současně) se může zvýšit latence a doporučuje se vytvořit další vlákno, které čeká na odpověď z jiného úložiště dat.
  
    * Podkladová vstupně-výstupní operace v Azure Cosmos DB Async Java SDK v2 je spravovaná pomocí síťoviny. Další informace najdete v těchto [tipech pro zamezení vzorům kódování, které blokují vstupně-výstupní vlákna](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).
  
  * **Modelování dat** – Azure Cosmos DB smlouva SLA předpokládá, že velikost dokumentu bude menší než 1 KB. Optimalizace datového modelu a programování pro upřednostnění menší velikosti dokumentů obvykle vede k nižší latenci. Pokud budete potřebovat úložiště a načítání dokumentů větších než 1 KB, doporučuje se pro dokumenty, které odkazují na data v Azure Blob Storage.

* **Ladění paralelních dotazů pro dělené kolekce**

  Azure Cosmos DB Async Java SDK v2 podporuje paralelní dotazy, které umožňují paralelní dotazování rozdělené kolekce. Další informace najdete v tématu [ukázky kódu](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) týkající se práce se sadami SDK. Paralelní dotazy jsou navržené tak, aby se zlepšila latence a propustnost dotazů v rámci svého sériového protějšku.

  * ***Vyladění setMaxDegreeOfParallelism\:***
    
    Paralelní dotazy fungují paralelně dotazování na více oddílů. Data z jednotlivých dělených kolekcí se ale v souvislosti s dotazem načítají sériově. Proto použijte setMaxDegreeOfParallelism k nastavení počtu oddílů, které mají maximální šanci dosáhnout nejvíce výkonného dotazu. za předpokladu, že všechny ostatní systémové podmínky zůstanou stejné. Pokud neznáte počet oddílů, můžete použít setMaxDegreeOfParallelism k nastavení vysokého čísla a systém zvolí minimální (počet oddílů, uživatelem zadaný vstup) jako maximální stupeň paralelismu.

    Je důležité si uvědomit, že paralelní dotazy poskytují nejlepší výhody, pokud jsou data rovnoměrně rozložena napříč všemi oddíly v souvislosti s dotazem. Pokud je dělená kolekce rozdělena takovým způsobem, že všechna nebo většina dat vrácených dotazem je soustředěna v několika oddílech (jeden oddíl v nejhorším případě), výkon dotazu by tyto oddíly měl být kritický.

  * ***Vyladění setMaxBufferedItemCount\:***
    
    Paralelní dotaz je navržený tak, aby byly výsledky předem načteny, zatímco aktuální dávka výsledků je zpracovávána klientem. Předběžné načítání pomáhá při celkové latenci v rámci dotazu. setMaxBufferedItemCount omezuje počet předběžně načtených výsledků. Nastavení setMaxBufferedItemCount na očekávaný počet vrácených výsledků (nebo vyšší číslo) umožňuje, aby dotaz získal maximální přínos před načtením.

    Předběžné načítání funguje stejným způsobem bez ohledu na Z MaxDegreeOfParallelism a existuje jedna vyrovnávací paměť pro data ze všech oddílů.

* **Implementace omezení rychlosti v intervalech getRetryAfterInMilliseconds**

  Během testování výkonu byste měli zvýšit zatížení až do omezení malých sazeb požadavků. V případě omezení by se klientská aplikace měla omezení rychlosti pro interval opakování zadaný serverem. Respektování omezení rychlosti zajistí, že strávíte minimální dobu čekání mezi opakovanými pokusy.

* **Horizontální navýšení kapacity klienta – zatížení**

  Pokud testujete na úrovních vysoké propustnosti (>50 000 RU/s), může se klientská aplikace stát kritickým bodem, protože počítač capping na využití procesoru nebo sítě. Pokud se dostanou k tomuto bodu, můžete dál nasdílet Azure Cosmos DB účet tím, že navedete horizontální navýšení kapacity klientských aplikací napříč více servery.

* **Použití adresování na základě názvu**

  Použijte adresování na základě názvu, kde odkazy mají formát `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` namísto SelfLinks ( \_ samoobslužný), který má formát, `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` aby nedocházelo k načítání ResourceID všech prostředků použitých k vytvoření odkazu. I když se tyto prostředky znovu vytvoří (možná se stejným názvem), nemusí do mezipaměti pomáhat.

* **Vyladění velikosti stránek pro dotazy a kanály pro čtení pro lepší výkon**

  Při hromadném čtení dokumentů pomocí funkce kanálu pro čtení (například readDocuments) nebo při vystavování dotazu SQL se výsledky vrátí segmenticky, pokud je sada výsledků příliš velká. Ve výchozím nastavení se výsledky vrátí do bloků 100 položek nebo 1 MB, podle toho, který limit se narazí jako první.

  Chcete-li snížit počet síťových přenosů potřebných k načtení všech použitelných výsledků, můžete zvětšit velikost stránky v hlavičce požadavku [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) až na 1000. V případech, kdy potřebujete zobrazit jenom pár výsledků, například pokud vaše uživatelské rozhraní nebo rozhraní API pro aplikace vrátí jenom 10 výsledků, můžete také zmenšit velikost stránky na 10 a snížit tak propustnost, která se pro čtení a dotazy spotřebují.

  Velikost stránky můžete nastavit také pomocí metody setMaxItemCount.

* **Použít vhodný Plánovač (Vyhněte se krádežím vláken v vstupně-výstupních událostech smyčky)**

  Azure Cosmos DB Async Java SDK v2 používá pro neblokovou vstupně-výstupní operaci [síťovinu](https://netty.io/) . Sada SDK používá pro provádění vstupně-výstupních operací pevný počet vstupně-výstupních operací cyklů událostí v/v (kolik PROCESORových jader má váš počítač). Pozorovatelé vrácené rozhraním API vygeneruje výsledek na jednom ze sdílených vláken v rámci smyčky vstupně-výstupních operací. Proto je důležité, abyste neblokovali vlákna v počtu sdílených vstupně-výstupních operací smyčky. V případě práce náročné na vstupně-výstupní operaci nebo operací blokování procesoru v/v může dojít k zablokování nebo výraznému snížení propustnosti sady SDK.

  Například následující kód provede práci náročnou na procesor ve vlákně pro vstupně-výstupní operace smyčky události:

  **Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)**

  ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
  ```

  Až se vrátí výsledek, pokud chcete, aby se na výsledku prováděla náročná práce s PROCESORem, měli byste se tomuto vláknu vyhnout cyklům v případě vstupně-výstupních operací. Místo toho můžete zadat vlastní Plánovač, který poskytne vlastní vlákno pro práci.

  **Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)**

  ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
  ```

  Na základě typu vaší práce byste měli použít odpovídající existující Plánovač RxJava pro vaši práci. Přečtěte si tento článek [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html) .

  Další informace najdete na [stránce GitHubu](https://github.com/Azure/azure-cosmosdb-java) , kde Azure Cosmos DB ASYNC Java SDK v2.

* **Zakázat protokolování síťoviny**

    Protokolování do knihovny síťoviny je konverzace a je nutné je vypnout (potlačení konfigurace nemusí být dostatečné), aby se předešlo dalším nákladům na procesor. Pokud nejste v režimu ladění, zakažte protokolování síťoviny úplně. Takže pokud používáte log4j k odebrání dalších nákladů na procesor vynaložených ``org.apache.log4j.Category.callAppenders()`` z síťoviny, přidejte do základu kódu následující řádek:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Omezení prostředků otevřených souborů operačního systému**
 
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

## <a name="indexing-policy"></a>Zásady indexování
 
* **Vyloučení nepoužívaných cest z indexování za účelem zrychlení zápisu**

    Pomocí cest indexování (setIncludedPaths a setExcludedPaths) můžete určit, které cesty dokumentů zahrnout nebo vyloučit z indexování. Azure Cosmos DB Použití cest indexování může nabízet lepší výkon zápisu a nižší indexové úložiště pro scénáře, ve kterých jsou vzory dotazů předem známé, protože náklady na indexování jsou přímo ve vztahu k počtu indexovaných jedinečných cest. Například následující kód ukazuje, jak vyloučit celý oddíl dokumentů (označovaný také jako podstrom) z indexování pomocí zástupného znaku "*".

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Další informace najdete v tématu [Azure Cosmos DB zásady indexování](indexing-policies.md).

## <a name="throughput"></a><a id="measure-rus"></a>Propustnost

* **Měření a optimalizace pro nižší jednotky žádostí za sekundu použití**

    Azure Cosmos DB nabízí bohatou sadu databázových operací, včetně relačních a hierarchických dotazů s UDF, uloženými procedurami a triggery – to vše v dokumentech v rámci kolekce databáze. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, V/V a paměti, které jsou potřeba k dokončení operace. Místo toho, abyste si vymysleli a spravovali hardwarové prostředky, si můžete představit jednotku žádosti (RU) jako jednu míru prostředků potřebných k provádění různých databázových operací a zpracování požadavků aplikace.

    Propustnost se zřizuje na základě počtu [jednotek žádostí](request-units.md) nastavených pro každý kontejner. Spotřeba jednotky požadavku se vyhodnotí jako sazba za sekundu. Aplikace, které překračují sazbu jednotky zřízené žádosti pro svůj kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň kontejneru. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit svou propustnost tím, že zřizujete další jednotky žádostí.

    Složitost dotazu ovlivňuje počet spotřebovaných jednotek požadavků pro určitou operaci. Počet predikátů, povaha predikátů, počet UDF a velikost zdrojové sady dat ovlivňují náklady na operace dotazů.

    Pokud chcete změřit režii jakékoli operace (vytvořit, aktualizovat nebo odstranit), zkontrolujte, že se v hlavičce [x-MS-Request-poplatek](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) měří počet jednotek žádostí spotřebovaných těmito operacemi. Můžete se také podívat na ekvivalentní vlastnost RequestCharge v ResourceResponse \<T> nebo FeedResponse \<T> .

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Async Java SDK v2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Poplatek za požadavek vrácený v této hlavičce je zlomek zřízené propustnosti. Pokud jste například zřídili 2000 RU/s, a pokud předchozí dotaz vrátí 1000 1 KB-Documents, náklady na operaci jsou 1000. V takovém případě se server během jedné sekundy připadá pouze na dva takové požadavky, než frekvence omezí následné požadavky. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek požadavků](https://www.documentdb.com/capacityplanner).

* **Omezení rychlosti zpracování/počet požadavků je moc velký.**

    Když se klient pokusí překročit rezervovanou propustnost účtu, neexistují žádné snížení výkonu na serveru a žádné využití kapacity propustnosti mimo rezervovanou úroveň. Server v tuto chvíli ukončí požadavek pomocí RequestRateTooLarge (kód stavu HTTP 429) a vrátí hlavičku [x-MS-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) , která indikuje, jak dlouho (v milisekundách) musí uživatel počkat, než se znovu pokusí o požadavek.

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    Sady SDK mají implicitně zachytit tuto odpověď, a to v souladu s hlavičkou, která je určená serverem, a zkuste žádost zopakovat. Pokud k účtu nepoužíváte souběžně více klientů, další pokus bude úspěšný.

    Pokud máte více než jednoho klienta, který se konzistentně pracuje konzistentně nad sazbou požadavku, nemusí být výchozí počet opakování nastavený na hodnotu 9 interně klientem. v takovém případě klient vyvolá DocumentClientException se stavovým kódem 429 do aplikace. Výchozí počet opakování se dá změnit pomocí setRetryOptions v instanci ConnectionPolicy. Ve výchozím nastavení se DocumentClientException se stavovým kódem 429 vrátí po kumulativní čekací době 30 sekund, pokud požadavek nadále funguje nad sazbou požadavku. K tomu dojde i v případě, že aktuální počet opakování je menší než maximální počet opakování, výchozí hodnota je 9 nebo uživatelem definovaná hodnota.

    I když automatizované chování při opakování pomáhá zlepšit odolnost a použitelnost většiny aplikací, může při měření latence docházet k lichá při provádění srovnávacích testů, zejména při měření latence. Latence zjištěná klientem bude špička, pokud experiment narazí na omezení serveru a způsobí, že se klientská sada SDK tiše znovu pokusí. Aby se zabránilo špičkám latence během experimentů s výkonem, změřte poplatky vracené jednotlivými operacemi a zajistěte, aby požadavky byly v provozu pod rezervovanými sazbami požadavků. Další informace najdete v tématu [jednotky žádostí](request-units.md).

* **Návrh pro menší dokumenty pro vyšší propustnost**

    Poplatek za požadavek (náklady na zpracování požadavku) dané operace se přímo koreluje s velikostí dokumentu. Operace s velkým objemem dokumentů se při používání malých dokumentů dotýkají více než operací.

## <a name="next-steps"></a>Další kroky

Další informace o návrhu aplikace pro škálování a vysoký výkon najdete v tématu [dělení a škálování v Azure Cosmos DB](partition-data.md).
