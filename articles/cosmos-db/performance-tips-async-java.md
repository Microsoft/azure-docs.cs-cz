---
title: Azure Cosmos DB tipy ke zvýšení výkonu pro Async Javu | Dokumentace Microsoftu
description: Další možnosti konfigurace klienta pro zvýšení výkonu databáze Azure Cosmos DB
keywords: jak vylepšit výkon databáze
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: sngun
ms.openlocfilehash: 233296a825653938da158fc70952c7fe7931498c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261821"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Tipy ke zvýšení výkonu pro službu Azure Cosmos DB a Async Javu

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB je rychlé a flexibilní distribuovanou databázi, která se škáluje bez problémů s garantovanou latencí a propustností. Není nutné měnit hlavní architektury nebo zapsat složitý kód škálování databáze pomocí služby Azure Cosmos DB. Vertikální navýšení a snížení je stejně jednoduché jako jediného volání rozhraní API nebo volání metody sady SDK. Protože Azure Cosmos DB se přistupuje přes síťová volání jsou však můžete provést k dosažení maximální výkon při použití optimalizací na straně klienta [sady Java SDK pro asynchronní SQL](sql-api-sdk-async-java.md).

Takže pokud máte s dotazem "Jak můžu vylepšit výkon Moje databáze?" Zvažte následující možnosti:

## <a name="networking"></a>Sítě
   <a id="same-region"></a>
1. **Společné umístění klienty ve stejné oblasti Azure pro výkon**

    Pokud je to možné, umístěte všechny aplikace, volání služby Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos DB. Pro porovnávání přibližné volání do služby Azure Cosmos DB v rámci stejné oblasti dokončena do 1 až 2 ms, ale latence mezi západním a východním pobřežím USA je > 50 ms. Latence může žádost z požadavku pravděpodobně lišit v závislosti na trasu v požadavku během z klienta na hranici datového centra Azure. Nejnižší možnou latenci se dosahuje tím, že zajišťuje, že je volající aplikace umístěné ve stejné oblasti Azure jako koncový bod zřízené služby Azure Cosmos DB. Seznam dostupných oblastí naleznete v tématu [oblastí Azure](https://azure.microsoft.com/regions/#services).

    ![Obrázek připojení zásad služby Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Použití sady SDK
1. **Nainstalujte nejnovější sadu SDK**

    Se sadami SDK služby Azure Cosmos DB se neustále vylepšují pro zajištění nejlepšího výkonu. Najdete v článku [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) stránky pro určení nejnovější sady SDK a zkontrolujte vylepšení.
2. **Použití klienta služby Azure Cosmos DB jednotlivý prvek po dobu životnosti vaší aplikace**

    Každá instance AsyncDocumentClient je bezpečná pro vlákno a provádí připojení efektivní správu a ukládání adres do mezipaměti. Povolit správu efektivní připojení a lepší výkon, když AsyncDocumentClient, doporučujeme použít jednu instanci AsyncDocumentClient na doménu aplikace po dobu životnosti aplikace.

   <a id="max-connection"></a>

3. **Ladění ConnectionPolicy**

    Azure Cosmos DB požadavků probíhají přes protokol HTTPS nebo rozhraní REST, při použití sady Java SDK asynchronní a jsou vystaveny výchozí velikost fondu připojení maximální (1000). Tato výchozí hodnota by měla být ideální pro většinu případů použití. Ale v případě, že máte velké kolekce s mnoha oddílů, můžete nastavit velikost fondu maximální počet připojení k většímu počtu (například 1500) pomocí setMaxPoolSize.

4. **Ladění používejte u dělených kolekcí paralelní dotazy**

    Azure Cosmos DB SQL asynchronní sady Java SDK podporuje paralelní dotazy, které vám umožní dotazovat do dělené kolekce paralelně (viz [práce spolu se sadami SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) a související [ukázky kódu](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) Další informace). Paralelní dotazy jsou navržené ke zlepšení latence dotazu a propustnost nad jejich protějšky sériového portu.

    (a) ***ladění setMaxDegreeOfParallelism\:***  paralelní dotazy pracovní dotazováním více oddílů souběžně. Data z jednotlivých dělené kolekce je však načíst sériově s ohledem na dotaz. Ano setMaxDegreeOfParallelism použijte k nastavení počtu oddílů, který má maximální příležitost dosáhnout většina výkonné, zadaný dotaz všech ostatních situacích, systém zůstávají stejné. Pokud si nejste jisti počet oddílů, setMaxDegreeOfParallelism můžete použít k nastavení vysoké číslo a systém zvolí jako maximální volnost paralelismu minimální (počet oddílů, vstup uživatele, které jsou k dispozici). 

    Je důležité si uvědomit, že paralelní dotazy vytvořit nejlepší výhody, pokud je data rovnoměrně rozdělené mezi všechny oddíly s ohledem na dotaz. Pokud dělené kolekce je rozdělit na oddíly tak, že se soustřeďuje všechny nebo většina dat vrácených dotazem v několika oddílech (jeden oddíl v nejhorším případě) a pak výkon dotazu by bottlenecked podle těchto oddílů.

    (b) ***ladění setMaxBufferedItemCount\:***  paralelní dotaz byl navržen k předběžného načítání výsledků během zpracování dávky aktuální výsledky klientem. Předběžné načítání pomáhá zvyšování celkové latence dotazu. setMaxBufferedItemCount omezuje počet předem počet získaných výsledků. Nastavení setMaxBufferedItemCount očekávaný počet výsledků vrácených (nebo vyššího čísla) umožňuje dotazu pro příjem maximální výhody z předběžného načítání.

    Předběžné načítání funguje stejným způsobem bez ohledu na to, z MaxDegreeOfParallelism a jednu vyrovnávací paměti pro data ze všech oddílů.  

5. **Implementace omezení rychlosti intervalech getRetryAfterInMilliseconds**

    Během testování výkonnosti, měli byste zvýšit zatížení dokud malý počet žádostí o získání omezené. Když omezený, klientská aplikace má omezení rychlosti pro interval opakování zadaný server. Respektování omezení rychlosti zajistí, že strávíte co nejkratším čase čekat mezi opakovanými pokusy. 
6. **Horizontální navýšení kapacity vašich úloh klienta**

    Pokud testujete při vysoké propustnosti (> 50 000 RU/s), aplikace klienta se může stát kritickým bodem kvůli počítač omezení navýšení kapacity na využití procesoru nebo sítě. Pokud překročíte tento bod, můžete nadále push další účet Azure Cosmos DB díky horizontálnímu navýšení kapacity klientských aplikací napříč několika servery.

7. **Použití adresování podle názvu**

    Použijte název založený adresování, kde odkazy mají formát `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, namísto SelfLinks (\_vlastní), které mají formát `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` aby se zabránilo načítání ResourceId všechny prostředky používané k vytvoření propojení. Také jak tyto prostředky načíst znovu vytvořit (možná se stejným názvem), ukládání do mezipaměti je nepomůže.

   <a id="tune-page-size"></a>
8. **Vyladěním stránek pro informační kanály pro zajištění lepšího výkonu dotazů/čtení**

    Při provádění hromadné čtení dokumentů pomocí funkce (například readDocuments) informační kanál čtení nebo při zadání dotazu SQL, výsledky se vrátí segmentovaným způsobem, pokud sada výsledků je příliš velký. Ve výchozím nastavení výsledky jsou vráceny v blocích 100 položek nebo 1 MB, podle omezení dosáhnete první.

    Abyste snížili počet sítě zaokrouhlit zkracuje dobu odezvy potřebný k načtení všech platných výsledky, můžete zvýšit velikost stránky pomocí [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hlavičku požadavku na až 1000. V případech, kdy potřebujete zobrazit jenom pár výsledky například pokud vaše uživatelské rozhraní nebo aplikací rozhraní API vrátí jenom 10 výsledky čas, může také snížit velikost stránky na 10 ke snížení propustnosti využité pro dotazy a čtení.

    Můžete také nastavit velikost stránky pomocí metody setMaxItemCount.
    
9. **Použít příslušné Plánovač (nepoužívejte zcizování Netty vláken vstupně-výstupních operací smyčky událostí)**

    Pomocí sady Java SDK asynchronní [netty](https://netty.io/) pro neblokující vstupně-výstupních operací. Sada SDK používá pevný počet vstupně-výstupních operací netty události smyčky vláken (počet jader procesoru, které má váš počítač) pro provádění v/v operace. Pozorovat vrácená rozhraním API vysílá výsledek na jednom ze sdílených vstupně-výstupních operací události smyčky netty vlákna. Proto je důležité nedochází k blokování sdílené vstupně-výstupních operací události smyčky netty vlákna. Provádějící práce náročné na procesor nebo blokující operace ve vlákně netty smyčky událostí vstupně-výstupní operace může způsobit zablokování nebo významné snížení propustnosti SDK.

    Například následující kód provede náročné práce procesoru na smyčkou událostí netty vláken vstupně-výstupní operace:

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

    Po obdržení výsledku, pokud ji chcete intenzivní nároky na procesor pracovat na výsledek byste se měli vyhnout způsobem atd. událost smyčky vstupně-výstupních operací netty vlákna. Místo toho můžete zadat vlastní plánovač k poskytování vlastních vláken pro spouštění práce.

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

    Na základě typu práce byste měli použít odpovídající stávající RxJava plánování pro svou práci. Tady najdete [ ``Schedulers`` ](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Další informace, podívejte se prosím na [stránku Githubu](https://github.com/Azure/azure-cosmosdb-java) pro asynchronní Java SDK.

10. **Volitelný zákaz protokolování pro netty** Netty knihovny protokolování je přetížení a musí být vypnuté (potlačení přihlášení konfigurace nemusí stačit) aby se zabránilo další náklady na využití procesoru. Pokud nejste v režimu ladění, netty zakázat protokolování úplně se vynechá. Pokud používáte log4j k odebrání dodatečné procesoru poplatky vzniklé v souvislosti ``org.apache.log4j.Category.callAppenders()`` z netty přidejte následující řádek do vašeho základu kódu:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Operační systém otevřené soubory limitu prostředků** systémů Linux některé (jako je Red Hat) nemají horní omezení počtu otevřít soubory a proto celkový počet připojení. Spusťte následující příkaz a zobrazí aktuální omezení:

    ```bash
    ulimit -a
    ```

    Počet otevřených souborů (nofile) musí být dostatečně velký, aby obsahovat dostatek místa pro velikost fondu nakonfigurované připojení a jiných otevřených souborů operačního systému. Může být upraveno umožňující větší velikost fondu připojení.

    Otevřete soubor limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Přidat/změnit následující řádky:

    ```
    * - nofile 100000
    ```

12. **Použít nativní implementace protokolu SSL pro netty** Netty můžete použít OpenSSL přímo pro zásobník implementace SSL můžete dosáhnout lepšího výkonu. Chybí tato konfigurace netty se vrátit zpět k implementaci protokolu SSL výchozí jazyka Java.

    na Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    a přidejte následující závislost závislostí vašeho projektu maven:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Pro jiné platformy (Red Hat, Windows, Mac, atd.) najdete v těchto pokynech https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Zásada indexování
 
1. **Vyloučit cesty nevyužité indexování pro rychlejší zápisy**

    Zásady indexování služby Azure Cosmos DB umožňuje určit, které dokumentu cesty pro zahrnutí nebo vyloučení z indexování s využitím indexování cesty (setIncludedPaths a setExcludedPaths). Použití indexování cesty může nabídnout zápisu lepší výkon a dolní index úložiště pro scénáře, ve kterých vzory dotazů znám předem, jako jsou indexování náklady přímo korelační počtu jedinečné cesty indexované.  Například následující kód ukazuje, jak vyloučit celý oddíl dokumenty (označovaný také jako podstrom) z indexování pomocí "*" zástupný znak.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Další informace najdete v tématu [zásadám indexování služby Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

1. **Měření a ladění pro nižší požadavek jednotek za sekundu využití**

    Azure Cosmos DB nabízí bohatou sadu databázových operací včetně relačních a hierarchických dotazů s uživatelem definovanými funkcemi, uložených procedur a aktivačních událostí. všechno funguje s dokumenty v databázové kolekci. Náklady spojené s každou z těchto operací se liší v závislosti na využití procesoru, vstup/výstup a paměti potřebných k dokončení operace. Místo přemýšlet o tom a správy hardwarových prostředků můžete jako jedno opatření pro prostředky požadované k provádění různých databázových operací a plnění požadavků aplikace představit jednotky žádosti (RU).

    Zřízení propustnosti na základě počtu [jednotky žádostí](request-units.md) nastavit pro každý kontejner. Spotřeba jednotek žádosti se vyhodnotí jako sazba za sekundu. Aplikace, které překračují jednotka frekvence zřízené požadavků na své kontejnery jsou omezené, dokud rychlost klesne pod úroveň zřízené pro kontejner. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit propustnost zřizování další jednotky žádostí. 

    Složitost dotazu má vliv na tom, kolik jednotek žádosti se spotřebovávají pro operaci. Počet predikátů, povaze predikáty, počet funkcí UDF a velikost zdrojové datové sady všech ovlivnit náklady na operace dotazů.

    K měření nároky na jakékoli operace (vytvoření, aktualizace nebo odstranění), zkontrolujte [x-ms žádost poplatek](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hlavičky k měření počtu jednotek žádosti spotřebovaná tyto operace. Můžete se také podívat na vlastnost RequestCharge ekvivalentní ResourceResponse<T> nebo FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```             

    Vrácená v hlavičce tohoto platí požadavek se zlomek zřízené propustnosti. Například pokud máte 2000 zřízené RU/s, a pokud předchozí dotaz vrací 1000 1KB – dokumenty, náklady na operace je 1000. V rámci jedné sekundy, v důsledku toho serveru respektuje pouze dva takové požadavky před tady následných žádostí. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek žádosti](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Zpracování frekvence omezení/požadavků míra příliš velká**

    Když se klient pokusí překročí vyhrazené propustnosti pro účet, neexistuje žádné snížení výkonu na serveru a používat sady kapacitou propustnosti mimo úroveň rezervované. Server preventivně ukončení požadavku s RequestRateTooLarge (kód stavu HTTP 429), který se vrátit [x-ms opakování po ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) záhlaví určující dobu v milisekundách, které musí uživatel čekat před opakováním požadavek.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Sady SDK všechny implicitně zachytit tuto odpověď, respektují zadaný server hlavičkou retry-after a opakujte žádost. Pokud váš účet je současně přistupuje více klientů, bude při dalším pokusu úspěšné.

    Pokud máte více než jednoho klienta kumulativně provozní konzistentně výše je frekvence požadavků, nemusí stačit výchozí počet opakování aktuálně nastavená na 9 interně klientem; v tomto případě klient vyvolá DocumentClientException se stavovým kódem 429 do aplikace. Výchozí počet opakování můžete změnit pomocí setRetryOptions ConnectionPolicy instance. Ve výchozím nastavení je vrácena DocumentClientException se stavovým kódem 429 po uplynutí určité doby kumulativní Počkejte 30 sekund, pokud požadavek dál pracovat nad frekvence požadavků. K tomu dojde i v případě aktuální počet opakování je menší než počet opakování, jde o výchozí hodnotu 9 nebo uživatelem definovanou hodnotu.

    Při automatické opakování chování pomáhá zlepšit odolnost proti chybám a použitelnost pro většinu aplikací, zřejmě na odds při provádění srovnávací testy výkonu, zejména v případě měření latence.  Klienta pozorované latence se dosahuje, pokud experiment narazí na server omezení a způsobí, že klientská sada SDK tiše opakování. Aby se zabránilo latence dosahující během výkonu experimenty, měřit poplatek vrácený každou operaci a ujistěte se, jsou požadavky funguje pod sazbu žádost o rezervovanou. Další informace najdete v tématu [jednotky žádostí](request-units.md).
3. **Návrh pro zajištění vyšší propustnost menších dokumentů**

    Zátěž žádostí (náklady na zpracování žádosti) dané operace jsou korelována přímo velikost dokumentu. Operace na velkých dokumentů nákladů více než operace pro malé dokumenty.

## <a name="next-steps"></a>Další postup
Další informace o návrhu vaší aplikace pro škálování a vysoký výkon, najdete v článku [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).
