---
title: Tipy pro výkon Azure Cosmos DB pro asynchronní Jazyk Java
description: Naučte se možnosti konfigurace klienta pro zlepšení výkonu databáze Azure Cosmos
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 89df941eb6ebaad6e078c278f1ed883db5528c7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152549"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Tipy pro zvýšení výkonu pro Azure Cosmos DB a asynchronní Javu

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která se bezproblémově škáluje s garantovanou latencí a propustností. Není třeba provádět hlavní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Škálování nahoru a dolů je stejně snadné jako volání jednoho rozhraní API nebo volání metody SDK. Protože však azure cosmos DB je přístupná prostřednictvím síťových volání existují optimalizace na straně klienta, které můžete provést k dosažení špičkového výkonu při použití [sady SQL Async Java SDK](sql-api-sdk-async-java.md).

Pokud se tedy ptáte: "Jak mohu zlepšit výkon databáze?" zvážit následující možnosti:

## <a name="networking"></a>Síťové služby

* **Režim připojení: Použití přímého režimu**
<a id="direct-connection"></a>
    
    Jak se klient připojí k Azure Cosmos DB má důležité důsledky pro výkon, zejména z hlediska latence na straně klienta. *ConnectionMode* je nastavení konfigurace klíče, které je k dispozici pro konfiguraci zásad *připojení klienta*. Pro asynchronní Java SDK dva dostupné connectionmodes jsou:  
      
    * [Brána (výchozí)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    Režim brány je podporován na všech platformách sady SDK a ve výchozím nastavení se jedná o nakonfigurovanou možnost. Pokud vaše aplikace běží v rámci podnikové sítě s přísnými omezeními brány firewall, režim brány je nejlepší volbou, protože používá standardní port HTTPS a jeden koncový bod. Kompromis výkonu je však, že režim brány zahrnuje další síťový směrování pokaždé, když se data číst nebo zapisovat do Azure Cosmos DB. Z tohoto důvodu přímý režim nabízí lepší výkon díky menšímu počtu síťových směrování.

    *Režim connectionmode* je konfigurován během výstavby instance *DocumentClient* s parametrem *ConnectionPolicy.*
    
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

* **Collocate klienty ve stejné oblasti Azure pro výkon**<a id="same-region"></a>

    Pokud je to možné, umístěte všechny aplikace volající Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos. Pro přibližné porovnání volání Azure Cosmos DB ve stejné oblasti dokončena do 1–2 ms, ale latence mezi západní a východní pobřeží USA je >50 ms. Tato latence se může pravděpodobně lišit od požadavku k požadavku v závislosti na trase provedené požadavkem při jeho předávání z klienta na hranici datového centra Azure. Nejnižší možné latence je dosaženo zajištěním volající aplikace se nachází ve stejné oblasti Azure jako zřízené Azure Cosmos DB koncového bodu. Seznam dostupných oblastí najdete v tématu [Oblasti Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustrace zásad připojení Azure Cosmos DB](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>Využití sady SDK
* **Instalace nejnovější sady SDK**

    Sady Azure Cosmos DB SDK se neustále vylepšují, aby poskytovaly nejlepší výkon. Podívejte se na stránky [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) k určení nejnovější sady SDK a vylepšení kontroly.

* **Používejte klienta Azure Cosmos DB po dobu životnosti vaší aplikace**

    Každá instance AsyncDocumentClient je bezpečná pro přístup z více vláken a provádí efektivní správu připojení a ukládání adres do mezipaměti. Chcete-li povolit efektivní správu připojení a lepší výkon asyncDocumentClient, doporučujeme použít jednu instanci AsyncDocumentClient per AppDomain po dobu životnosti aplikace.

   <a id="max-connection"></a>

* **Ladění zásad připojení**

    Ve výchozím nastavení jsou požadavky Cosmos DB v přímém režimu prováděny přes TCP při použití sady Async Java SDK. Interně sada SDK používá speciální architekturu přímého režimu k dynamické správě síťových prostředků a dosažení nejlepšího výkonu.

    V asynchronní java sdk je přímý režim nejlepší volbou pro zlepšení výkonu databáze s většinou úloh. 

    * ***Přehled přímého režimu***

        ![Ilustrace architektury přímého režimu](./media/performance-tips-async-java/rntbdtransportclient.png)

        Architektura na straně klienta používaná v přímém režimu umožňuje předvídatelné využití sítě a multiplexní přístup k replikám Db Azure Cosmos. Výše uvedený diagram ukazuje, jak přímý režim směruje požadavky klientů na repliky v back-endu Cosmos DB. Architektura přímého režimu přiděluje až 10 **kanálů** na straně klienta na repliku DB. Kanál je připojení TCP předchází vyrovnávací paměti požadavku, což je 30 požadavků hluboko. Kanály patřící do repliky jsou dynamicky přiděleny podle potřeby **koncovým bodem služby repliky**. Když uživatel vydá požadavek v přímém režimu, **TransportClient** směruje požadavek na koncový bod správné služby na základě klíče oddílu. **Fronta požadavků** uvadí požadavky před koncovým bodem služby.

    * ***Možnosti konfigurace connectionpolicy pro přímý režim***

        Jako první krok použijte následující doporučená nastavení konfigurace níže. Pokud narazíte na problémy s tímto konkrétním tématem, obraťte se na [tým Azure Cosmos DB.](mailto:CosmosDBPerformanceSupport@service.microsoft.com)

        Pokud používáte Azure Cosmos DB jako referenční databázi (to znamená, že databáze se používá pro mnoho operací čtení bodu a několik operací zápisu), může být přijatelné nastavit *idleEndpointTimeout* na 0 (to znamená, že žádný časový limit).


        | Možnost konfigurace       | Výchozí    |
        | :------------------:       | :-----:    |
        | vyrovnávací paměťPageSize             | 8192       |
        | Connectiontimeout          | "PT1M"     |
        | idleChannelTimeout         | "PT0S"     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maximální kapacita vyrovnávací paměti          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | "PT5S"     |
        | requestTimeout             | "PT1M"     |
        | requestTimerResolution     | "PT0.5S"   |
        | sendHangDetectionTime      | "PT10S"    |
        | vypnutíčasového času            | "PT15S"    |

    * ***Tipy pro programování pro přímý režim***

        Projděte si článek řešení potíží s sadou Java [SDK](troubleshoot-java-async-sdk.md) Azure Cosmos DB Async java jako výchozí pro řešení problémů s asynchronní sadou Java SDK.

        Některé důležité tipy pro programování při použití přímého režimu:

        + **Použijte multithreading ve vaší aplikaci pro efektivní přenos dat TCP** - Po podání požadavku, aplikace by se měla přihlásit k odběru dat v jiném vlákně. Pokud tak neučiníte vynutí neúmyslné "half-duplex" operace a následné požadavky jsou blokovány čekání na odpověď předchozí požadavek.

        + **Proveďte úlohy náročné na výpočetní výkon ve vyhrazeném vlákně** – z podobných důvodů jako předchozí tip jsou operace, jako je komplexní zpracování dat, nejlépe umístěny v samostatném vlákně. Požadavek, který získává data z jiného úložiště dat (například pokud vlákno využívá úložiště dat Azure Cosmos DB a Spark současně) může dojít ke zvýšení latence a doporučuje se vytvořit další vlákno, které čeká na odpověď od ostatních ukládání dat.

            + Základní síťové vi videa v asynchronní java sdk je spravovánnetty, viz tyto [tipy pro zamezení kódování vzory, které blokují Netty IO vlákna](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).

        + **Modelování dat** – Azure Cosmos DB SLA předpokládá velikost dokumentu být menší než 1 KB. Optimalizace datového modelu a programování ve prospěch menší velikosti dokumentu obecně povede ke snížení latence. Pokud budete potřebovat úložiště a načítání dokumentů větších než 1 KB, doporučený přístup je pro dokumenty pro propojení s daty ve službě Azure Blob Storage.


* **Optimalizace paralelních dotazů pro rozdělené kolekce**

    Azure Cosmos DB SQL Async Java SDK podporuje paralelní dotazy, které umožňují dotaz na rozdělenou kolekci paralelně. Další informace naleznete v [tématu ukázky kódu](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) související s prací s sadami SDK. Paralelní dotazy jsou navrženy tak, aby zlepšit latenci dotazu a propustnost přes jejich sériový protějšek.

    * ***TuningsetMaxDegreeOfParallelism\:***
    
        Paralelní dotazy fungují dotazováním více oddílů paralelně. Data z jednotlivých oddílové kolekce je však načtensériově s ohledem na dotaz. Takže použijte setMaxDegreeOfParallelism nastavit počet oddílů, který má maximální šanci na dosažení nejvýkonnější dotaz, za předpokladu, že všechny ostatní systémové podmínky zůstávají stejné. Pokud neznáte počet oddílů, můžete použít setMaxDegreeOfParallelism nastavit vysoké číslo a systém zvolí minimální (počet oddílů, uživatel za předpokladu, vstup) jako maximální stupeň paralelismu.

        Je důležité si uvědomit, že paralelní dotazy vytvořit nejlepší výhody, pokud data je rovnoměrně distribuovány mezi všechny oddíly s ohledem na dotaz. Pokud je rozdělená kolekce rozdělena tak, že všechna nebo většina dat vrácených dotazem je soustředěna v několika oddílech (jeden oddíl v nejhorším případě), pak výkon dotazu by byl kritický těmito oddíly.

    * ***TuningsetMaxBufferedItemCount\:***
    
        Paralelní dotaz je navržen tak, aby předem načíst výsledky při aktuální dávka výsledků je zpracovávána klientem. Předběžné načítání pomáhá při celkovém zlepšení latence dotazu. setMaxBufferedItemCount omezuje počet předem načtených výsledků. Nastavení setMaxBufferedItemCount na očekávaný počet vrácených výsledků (nebo vyšší číslo) umožňuje dotazu získat maximální užitek z předběžného načítání.

        Předběžné načítání funguje stejným způsobem bez ohledu na MaxDegreeOfParallelism a je jedna vyrovnávací paměť pro data ze všech oddílů.

* **Implementovat backoff v intervalech getRetryAfterInMilliseconds**

    Během testování výkonu byste měli zvýšit zatížení, dokud se neomezí malá rychlost požadavků. Pokud omezena, klientská aplikace by měla backoff pro interval opakování zadané serverem. Respektování backoff zajišťuje, že strávíte minimální množství času čekání mezi opakování.

* **Horizontální navýšení kapacity úloh y klienta**

    Pokud testujete na vysokých úrovních propustnost (>50 000 RU/s), může se klientská aplikace stát kritickým bodem z důvodu omezení počítače na využití procesoru nebo sítě. Pokud dosáhnete tohoto bodu, můžete pokračovat v dalším nabízení účtu Azure Cosmos DB horizontálním navýšením kapacity klientských aplikací na více serverech.

* **Použít adresování založené na názvu**

    Použijte adresování založené na názvu, `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`kde odkazy mají\_formát , namísto SelfLinks (self), které mají formát, `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` aby se zabránilo načítání ResourceIds všech prostředků použitých k vytvoření propojení. Také jako tyto prostředky získat znovu (případně se stejným názvem), ukládání do mezipaměti je nemusí pomoci.

   <a id="tune-page-size"></a>

* **Vyladění velikosti stránky pro dotazy/zdroje pro čtení pro lepší výkon**

    Při hromadném čtení dokumentů pomocí funkce informačního kanálu pro čtení (například readDocuments) nebo při vydávání dotazu SQL jsou výsledky vráceny segmentovaným způsobem, pokud je sada výsledků příliš velká. Ve výchozím nastavení jsou výsledky vráceny v blocích 100 položek nebo 1 MB, podle toho, jaký limit je přístupů jako první.

    Chcete-li snížit počet síťových zpátečních cest potřebných k načtení všech platných výsledků, můžete zvětšit velikost stránky pomocí hlavičky požadavku [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) až na 1000. V případech, kdy potřebujete zobrazit pouze několik výsledků, například pokud vaše uživatelské rozhraní nebo rozhraní API aplikace vrátí pouze 10 výsledků za čas, můžete také snížit velikost stránky na 10 snížit propustnost spotřebované pro čtení a dotazy.

    Můžete také nastavit velikost stránky pomocí metody setMaxItemCount.

* **Použít vhodný plánovač (Vyhněte se krádeži vláken IO Netty smyčky událostí)**

    Sada Async Java SDK používá [netty](https://netty.io/) pro neblokující vi. Sada SDK používá pevný počet vláken smyčky netty Vo (tolik jader procesoru, které má váš počítač) pro provádění operací vi. Observable vrácené rozhraní API vydává výsledek na jednom ze sdílených vláken smyčky událostí Vi. Proto je důležité neblokovat sdílené iO událost smyčky netty podprocesů. Provádění práce náročné na procesor nebo blokování operace v podprocesu vi událost netty může způsobit zablokování nebo výrazně snížit propustnost sady SDK.

    Například následující kód provede práci náročnou na procesor na vlákno vi netty smyčky událostí:

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

    Po přijetí výsledku, pokud chcete provést náročnou práci na náročném procesoru na výsledku, měli byste se tomu vyhnout na vlákno vi netty smyčky událostí. Místo toho můžete poskytnout vlastní Plánovač poskytnout vlastní vlákno pro spuštění práce.

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

    Na základě typu práce byste měli pro svou práci použít příslušný stávající plánovač RxJava. Čtěte [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)zde .

    Další informace naleznete na [stránce GitHub](https://github.com/Azure/azure-cosmosdb-java) pro asynchronní Java SDK.

* **Zakázat protokolování netty**

    Netty knihovna protokolování je upovídaný a je třeba vypnout (potlačení znaménko v konfiguraci nemusí být dost), aby se zabránilo další náklady na procesor. Pokud nejste v režimu ladění, zakažte netty protokolování úplně. Takže pokud používáte log4j odebrat další náklady ``org.apache.log4j.Category.callAppenders()`` na procesor vzniklé z netty přidat následující řádek na základ kódu:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Limit prostředků pro otevření souborů operačního systému**
 
    Některé linuxové systémy (například Red Hat) mají horní limit počtu otevřených souborů a tak celkový počet připojení. Chcete-li zobrazit aktuální limity, spusťte následující:

    ```bash
    ulimit -a
    ```

    Počet otevřených souborů (nofile) musí být dostatečně velký, aby měl dostatek místa pro konfiguraci velikosti fondu připojení a dalších otevřených souborů v os. Může být upraven tak, aby umožňoval větší velikost fondu připojení.

    Otevřete soubor limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Přidejte nebo upravte následující řádky:

    ```
    * - nofile 100000
    ```

* **Použití nativní implementace SSL pro netty**

    Netty můžete použít OpenSSL přímo pro implementaci SSL zásobníku k dosažení lepšího výkonu. V případě, že tato konfigurace netty se vrátí k výchozí implementaci SSL Java.

    na Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    a přidejte následující závislost do závislostí projektu maven:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Pro ostatní platformy (Red Hat, Windows, Mac atd.) viz tyto pokynyhttps://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Zásady indexování
 
* **Vyloučit nepoužívané cesty z indexování pro rychlejší zápisy**

    Zásady indexování Azure Cosmos DB umožňuje určit, které cesty dokumentu zahrnout nebo vyloučit z indexování pomocí indexování cesty (setIncludedPaths a setExcludedPaths). Použití cesty indexování může nabídnout lepší výkon zápisu a nižší index úložiště pro scénáře, ve kterých jsou předem známy vzorky dotazu, protože náklady na indexování jsou přímo korelují s počtem jedinečných cest indexovaných. Například následující kód ukazuje, jak vyloučit celou část dokumentů (označovanou také jako podstrom) z indexování pomocí zástupného znaku "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Další informace naleznete v tématu [zásadindexování Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

* **Měření a ladění pro použití s nižšími požadavky/sekunda**

    Azure Cosmos DB nabízí bohatou sadu databázových operací, včetně relačních a hierarchických dotazů s UD, uloženými procedurami a aktivačními událostmi – všechny pracují s dokumenty v rámci kolekce databází. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, V/V a paměti, které jsou potřeba k dokončení operace. Místo přemýšlení o a správě hardwarových prostředků si můžete jednotku požadavku (RU) považovat za jediné opatření pro prostředky potřebné k provádění různých databázových operací a servisu požadavku aplikace.

    Propustnost je zřízena na základě počtu [jednotek požadavků](request-units.md) nastavených pro každý kontejner. Spotřeba jednotky požadavku je vyhodnocována jako sazba za sekundu. Aplikace, které překračují zřízenou jednotkovou sazbu požadavku pro jejich kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň pro kontejner. Pokud vaše aplikace vyžaduje vyšší úroveň propustnost, můžete zvýšit propustnost zřízením další chynaté jednotky.

    Složitost dotazu má vliv na to, kolik jednotek požadavků je spotřebováno pro operaci. Počet predikátů, povaha predikátů, počet UD f a velikost zdrojové sady dat ovlivňují náklady na operace dotazu.

    Chcete-li měřit režii jakékoli operace (vytvořit, aktualizovat nebo odstranit), zkontrolujte hlavičku [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) a změřte počet jednotek požadavků spotřebovaných těmito operacemi. Můžete se také podívat na ekvivalentní\<RequestCharge vlastnost\<v ResourceResponse T> nebo FeedResponse T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Poplatek za požadavek vrácený v této hlavičce je zlomkem zřízené propustnosti. Například pokud máte 2000 RU/s zřízena a pokud předchozí dotaz vrátí 1000 1KB-dokumenty, náklady na operaci je 1000. Jako takové během jedné sekundy server respektuje pouze dva takové požadavky před omezením rychlosti následné požadavky. Další informace naleznete v tématu [Request units](request-units.md) and the request [unit calculator](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Zpracovat rychlost omezení /rychlost požadavku příliš velký**

    Pokud se klient pokusí překročit vyhrazenou propustnost pro účet, neexistuje žádné snížení výkonu na serveru a žádné použití kapacity propustnosti nad vyhrazenou úroveň. Server preventivně ukončí požadavek pomocí RequestRateTooLarge (stavový kód HTTP 429) a vrátí hlavičku [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) označující dobu v milisekundách, po kterou musí uživatel před opětovným pokusem o požadavek počkat.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Všechny sady SDK implicitně zachytit tuto odpověď, respektovat serverem zadané opakování po záhlaví a opakujte požadavek. Pokud váš účet není přístupná současně více klientů, další opakování bude úspěšné.

    Pokud máte více než jednoho klienta, který kumulativně pracuje konzistentně nad sazbu požadavku, výchozí počet opakování aktuálně nastavený klientem na 9 interně nemusí stačit. v tomto případě klient vyvolá DocumentClientException se stavovým kódem 429 do aplikace. Výchozí počet opakování lze změnit pomocí setRetryOptions na connectionpolicy instance. Ve výchozím nastavení DocumentClientException se stavovým kódem 429 je vrácena po kumulativní čekací doba 30 sekund, pokud požadavek nadále pracovat nad sazbu požadavku. K tomu dochází i v případě, že aktuální počet opakování je menší než maximální počet opakování, ať už je to výchozí hodnota 9 nebo uživatelem definovaná hodnota.

    Zatímco automatické chování opakování pomáhá zlepšit odolnost proti chybám a použitelnost pro většinu aplikací, může přijít v rozporu při provádění výkonnostních měřítek, zejména při měření latence. Latence s pozorovaná klientem se zvýší, pokud experiment narazí na serverovou škrticí klapku a způsobí, že sada SDK klienta tiše provede opakování. Chcete-li se vyhnout špičkám latence během experimentů s výkonem, změřte poplatek vrácený každou operací a ujistěte se, že požadavky pracují pod rezervovanou sazbou požadavku. Další informace naleznete v tématu [Request units](request-units.md).

* **Návrh pro menší dokumenty pro vyšší propustnost**

    Poplatek za požadavek (náklady na zpracování požadavku) dané operace přímo souvisí s velikostí dokumentu. Operace s velkými dokumenty stojí více než operace pro malé dokumenty.

## <a name="next-steps"></a>Další kroky

Další informace o návrhu aplikace pro škálování a vysoký výkon najdete [v tématu dělení a škálování v Azure Cosmos DB](partition-data.md).
