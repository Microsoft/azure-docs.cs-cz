---
title: Azure Cosmos DB tipy pro zvýšení výkonu pro jazyk Java asynchronní | Microsoft Docs
description: Další možnosti konfigurace klienta ke zlepšení výkonu databáze Azure Cosmos DB
keywords: tom, jak zvýšit výkon databáze
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: dfe8f426-3c98-4edc-8094-092d41f2795e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: sngun
ms.openlocfilehash: 95f6e3d6d9db5a88b5b974daf6e36573b60878a5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Tipy pro zvýšení výkonu pro Azure Cosmos DB a asynchronních Java
Azure Cosmos DB je rychlé a flexibilní distribuovanou databázi, která bezproblémově škáluje s zaručenou latence a propustnosti. Není nutné provádět změny hlavní architektura nebo zapsat složitý kód se škálovat databázi Azure Cosmos DB. Škálování nahoru a dolů je stejně snadná jako jednoho volání rozhraní API nebo volání metody SDK. Protože Azure Cosmos DB přistupuje prostřednictvím volání sítě jsou ale optimalizace na straně klienta můžete provést dosáhnout špičkový výkon při použití [SQL asynchronní Java SDK](sql-api-sdk-async-java.md).

Takže pokud vás nemůže ověřit "jak vylepšit výkon Moje databáze?" Zvažte následující možnosti:

## <a name="networking"></a>Sítě
   <a id="same-region"></a>
1. **Společně umístit klienty ve stejné oblasti Azure výkonu**

    Pokud je to možné, umístěte všechny aplikace, volání Azure Cosmos DB ve stejné oblasti jako databázi Azure Cosmos DB. Přibližná porovnání najdete dokončí volání do databáze Cosmos Azure v rámci stejné oblasti v rámci ms 1 – 2, ale je latence mezi – západ a východním pobřeží USA > 50 ms. Tato čekací doba můžete z požadavku na žádost pravděpodobně lišit v závislosti na směrování žádosti jako předává z klienta na hranici datového centra Azure. Nejnižší možnou latenci je dosaženo tím zajistíte, že je volající aplikace se nachází v rámci stejné oblasti Azure jako zřízené koncový bod Azure Cosmos DB. Seznam dostupných oblastí najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

    ![Obrázek připojení zásad Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Použití sady SDK
1. **Nainstalujte nejnovější sadu SDK**

    Sady SDK Azure Cosmos DB neustále probíhá lepší zajistit optimální výkon. Najdete v článku [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) stránky, abyste zjistili nejnovější SDK a zkontrolujte vylepšení.
2. **Pomocí klienta Azure Cosmos DB singleton po dobu jeho existence vaší aplikace.**

    Každá instance AsyncDocumentClient je bezpečné pro přístup z více vláken a provádí správu efektivní připojení a ukládání adres do mezipaměti. Povolit správu efektivní připojení a lepší výkon pomocí AsyncDocumentClient, doporučujeme použít jednu instanci AsyncDocumentClient jednotlivé domény aplikace po dobu jeho existence aplikace.

   <a id="max-connection"></a>

3. **Ladění ConnectionPolicy**

    Azure Cosmos DB požadavky jsou při použití sady Java SDK asynchronní proveden prostřednictvím protokolu HTTPS nebo REST a jsou vystaveny výchozí velikost fondu maximální počet připojení (1000). Tato výchozí hodnota by měla být ideální pro většinu případy použití. Ale v případě, že máte velké kolekce s mnoha oddílů, můžete nastavit velikost fondu maximální počet připojení k větší počet (indikované, 1500) pomocí setMaxPoolSize.

4. **Ladění paralelní dotazy pro dělené kolekce**

    Azure Cosmos DB SQL asynchronní Java SDK podporuje paralelní dotazy, které vám umožní dotazovat dělenou kolekci paralelně (viz [práce sady SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) a související [ukázky kódu](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) Další informace). Paralelní dotazy jsou navrženy pro zlepšit latenci dotazů a propustnosti na jejich sériové protějšku.

    (a) ***ladění setMaxDegreeOfParallelism\:***  paralelní dotazy pracovní dotazováním více oddílů souběžně. Data z kolekci jednotlivých oddílů je však načtených sériově s ohledem na dotaz. Ano použití setMaxDegreeOfParallelism nastavit počet oddílů, který má maximální riziko dosažení většina původce dotazu, poskytuje další podmínky systému zůstávají stejné. Pokud neznáte počet oddílů, setMaxDegreeOfParallelism můžete nastavit vysoké číslo a systém zvolí minimální (počet oddílů, uživatel zadal vstup) jako maximální stupně paralelního zpracování. 

    Je důležité si uvědomit, že paralelní dotazy vytvořit nejlepší výhody, pokud data je rovnoměrně rozdělené mezi všechny oddíly s ohledem na dotaz. Pokud dělené kolekce je rozdělena na oddíly tak, že se soustřeďuje všechny nebo většinu dat vrácených dotazem do několika oddílů (jeden oddíl v nejhorším případě), a potom výkonu dotazu by omezené podle těchto oddílů.

    (b) ***ladění setMaxBufferedItemCount\:***  paralelního dotazu slouží k předem načíst výsledky během aktuální dávku výsledky zpracování klientem. Předběžné načítání pomáhá v celkového zlepšení latence dotazu. setMaxBufferedItemCount omezuje počet předem načtených výsledků. Nastavení setMaxBufferedItemCount očekávaný počet výsledků vrácených (nebo vyšším číslem) umožňuje dotazu z předem načítání maximální výhody nezískají.

    Předběžné načítání funguje stejným způsobem bez ohledu MaxDegreeOfParallelism a je do jedné vyrovnávací paměti pro data ze všech oddílů.  

5. **Implementace omezení rychlosti v intervalech getRetryAfterInMilliseconds**

    Během testování výkonu měli zvýšit zatížení, dokud malý počet žádostí o získání omezeny. Pokud omezené, klientská aplikace měli omezení rychlosti pro interval opakování zadaný server. Bere ohledy omezení rychlosti zajišťuje strávený minimální množství času čekat mezi opakovanými pokusy. Další informace najdete v tématu [Exceeding vyhrazené omezení propustnosti](request-units.md#RequestRateTooLarge) a DocumentClientException.getRetryAfterInMilliseconds.
6. **Horizontální navýšení kapacity vaše úlohy klienta**

    Pokud testujete na úrovních vysoké propustnosti (> 50 000 RU/s), klientská aplikace se může stát úzkým místem kvůli počítač omezení se na využití procesoru nebo sítě. Pokud dostanete tento bod, můžete dál tak, aby nabízel další účet Azure Cosmos DB podle škálování klientských aplikací na více serverech.

7. **Použijte název, na základě adresování**

    Použít na základě názvu adresy, kde mají odkazy formát `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, místo SelfLinks (\_vlastní), které mají formát `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` předejdete načítání ResourceIds všechny prostředky použitý k vytvoření odkazu. Navíc ukládání do mezipaměti je nemusí pomoci jako tyto prostředky získat znovu vytvoří (i se stejným názvem).

   <a id="tune-page-size"></a>
8. **Optimalizovat velikost stránky pro dotazy/číst informační kanály pro lepší výkon**

    Při provádění hromadného čtení dokumentů pomocí funkce (například readDocuments) informační kanál čtení nebo při vydání příkazu jazyka SQL, budou vráceny výsledky segmentovaným způsobem, pokud sadu výsledků dotazu je příliš velký. Ve výchozím nastavení budou vráceny výsledky v bloky 100 položky nebo 1 MB, z těchto omezení dosáhl první.

    Chcete-li snížit počet sítě zaokrouhlit služebních cest potřebnou k načtení všech platných výsledky, můžete zvýšit velikost stránky pomocí [x-ms-max--počet položek](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hlavička požadavku na až 1000. V případech, kdy je třeba zobrazit jen pár výsledky například pokud vaše uživatelské rozhraní nebo aplikací rozhraní API vrátí jenom 10 výsledků na dobu, může také snížit velikost stránky na 10 ke snížení propustnosti využité pro čtení a dotazy.

    Mohou také nastavit pomocí metody setMaxItemCount velikost stránky.
    
9. **Použít příslušné Plánovač (nepoužívejte krádež eventloop – vstupně-výstupní operace Netty vláken)**

    Asynchronní Java SDK používá [netty](https://netty.io/) pro neblokující vstupně-výstupní operace. Sada SDK používá pevný počet vstupně-výstupní operace netty eventloop – vláken (libovolný počet jader procesoru, které má váš počítač) pro provádění operací vstupně-výstupní operace. Lze zobrazit vrácený API vysílá výsledek na jednom ze sdílených vláken netty eventloop – vstupně-výstupní operace. Proto je důležité sdílené vláken netty eventloop – vstupně-výstupní operace nejsou blokovány. Procesor intenzivně pracujete nebo blokování operace ve vlákně netty eventloop – vstupně-výstupní operace může způsobit zablokování nebo výrazně snížit propustnost SDK.

    Například následující kód provede náročné pracovní procesoru ve vlákně netty eventloop – vstupně-výstupní operace:

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

    Po přijetí výsledek, pokud ji chcete intenzivní nároky na procesor fungovat na výsledek byste se měli vyhnout provádění netty vlákna tak dále eventloop – vstupně-výstupní operace. Místo toho můžete zadat vlastní plánovače poskytnout vlastní vlákno pro spuštění práci.

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

    Na základě typu práce byste měli používat příslušné existující RxJava Plánovač pro práci. Přečtěte si zde [ ``Schedulers`` ](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Další informace, podívejte se prosím na [Github stránce](https://github.com/Azure/azure-cosmosdb-java) pro asynchronní Java SDK.

10. **Zakázání protokolování na netty** Netty knihovny protokolování je chatty a musí být vypnuté (potlačení protokolu v konfiguraci nemusí stačit) aby se zabránilo další náklady procesoru. Pokud nejste v režimu ladění, zakažte netty veškeré protokolování. Pokud používáte log4j odebrat náklady na další procesoru způsobené ``org.apache.log4j.Category.callAppenders()`` z netty přidejte následující řádek na vaše základu kódu:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Otevřete operační systém souborů limitu prostředků** některá Linux systémy (např. Redhat) mají horní limit počtu otevřené soubory a tak celkový počet připojení. Spusťte následující příkaz a zobrazí aktuální omezení:

    ```bash
    ulimit -a
    ```

    Počet otevřených souborů (nofile) musí být dostatečně velký pro nemá dostatek místa pro vaše velikost fondu nakonfigurované připojení a dalších otevřených souborů podle operačního systému. Může být upraven umožňující větší velikost fondu připojení.

    Otevřete soubor limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Přidat nebo upravte následující řádky:

    ```
    * - nofile 100000
    ```

12. **Použít nativní implementaci protokolu SSL pro netty** Netty můžete použít OpenSSL přímo pro implementace zásobníku SSL můžete dosáhnout lepšího výkonu. Bez této konfiguraci netty se vrátit zpět k implementaci SSL Java na výchozí.

    na Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    a přidejte následující závislost pro váš projekt maven závislosti:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Pro jiné platformy (Red Hat, Windows, Mac, atd.) postupujte podle těchto pokynů https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Zásada indexování
 
1. **Vyloučit z indexování pro rychlejší zápisy nepoužívané cesty**

    Zásady indexování Azure Cosmos DB umožňuje určit, které cesty dokumentu zahrnout nebo vyloučit z indexování s využitím indexování cesty (setIncludedPaths a setExcludedPaths). Použití indexování cesty můžete jako indexování náklady jsou přímo korelační počet jedinečných cesty indexované nabízet zápisu lepší výkon a dolní index úložiště pro scénáře, ve kterých jsou předem, známé typy dotazů.  Například následující kód ukazuje, jak vyloučit celý oddíl dokumentů (také známa jako podstrom) indexování pomocí "*" zástupný znak.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Další informace najdete v tématu [Azure DB Cosmos indexování zásady](indexing-policies.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

1. **Měření a optimalizovat pro nižší požadavek jednotek za sekundu využití**

    Azure Cosmos DB nabízí širokou škálu databázové operace, včetně dotazů na relační a hierarchických UDF, uložené procedury a triggery – všechny provozní s dokumenty v databázi kolekce. Náklady spojené s každou z těchto operací se liší podle využití procesoru, vstupně-výstupní operace a paměť požadovanou pro dokončení operace. Namísto přemýšlení o a správu hardwarové prostředky si můžete představit jednotka žádosti (RU) jako jednu míru pro prostředky potřebné k provádět různé operace databáze a služeb na žádost aplikace.

    Propustnost je zřízený na základě počtu [požadované jednotky](request-units.md) nastavit pro každý kontejner. Spotřeba jednotek žádosti budou vyhodnocené jako za sekundu. Aplikace, které překračují rychlost jednotky zřízené požadavků pro jejich kontejner jsou omezené, dokud rychlost klesne pod úroveň zřízené v kontejneru. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit vaše propustnost zřizování jednotek další žádosti. 

    Složitost dotazu má dopad na tom, kolik jednotek žádosti se spotřebovávají pro operace. Počet predikáty, povaha predikáty, počet UDF a velikost datové sady zdroje, které jsou všechny ovlivnit náklady na operace dotazů.

    K měření režijní náklady na všechny operace (vytvoření, aktualizace nebo odstranění), zkontrolujte [x-ms požadavku poplatků](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hlavičky k měření počtu jednotek žádosti uplatníte tyto operace. Můžete také prohlédnout vlastnost RequestCharge ekvivalentní ResourceResponse<T> nebo FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```             

    Žádost o poplatků, vrátí se v tuto hlavičku je zlomek zřízené propustnosti. Například pokud máte 2000 zřízený RU/s, a pokud předchozí dotaz vrátí 1000 1KB – dokumenty, náklady na operaci je 1000. Jako takový jedné sekundy ctí serveru pouze dva takových požadavků před omezení následných žádostí. Další informace najdete v tématu [požadované jednotky](request-units.md) a [kalkulačky jednotek žádosti](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Rychlost omezení nebo požadavků popisovač míra příliš velký**

    Když se klient pokusí delší než vyhrazenou propustností pro účet, není bez snížení výkonu na serveru a bez využití kapacity propustnosti mimo úroveň vyhrazené. Server bude ho preventivně ukončit požadavek s RequestRateTooLarge (kód stavu HTTP 429) a vrátit se [x-ms opakování za ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hlavičky, která určuje množství času v milisekundách, která uživatel musí počkat před provedením nového pokusu požadavek.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Sady SDK všechny implicitně catch této odpovědi, respektují záhlaví zadaný server opakovat po a opakujte žádost. Pokud váš účet je současně přistupuje více klientů, další pokus bude úspěšné.

    Pokud máte více než jednoho klienta kumulativně operační konzistentně vyšší rychlost požadavků, nemusí stačit výchozí počet opakování aktuálně nastavený na hodnotu 9 interně klientem; v tomto případě klient vyvolá DocumentClientException se stavovým kódem 429 k aplikaci. Při použití setRetryOptions v instanci ConnectionPolicy můžete změnit výchozí počet opakování. Ve výchozím nastavení je DocumentClientException se stavovým kódem 429 vrátila po uplynutí určité doby kumulativní Počkejte 30 sekund, pokud požadavek i nadále fungovat vyšší rychlost požadavků. K tomu dojde i v případě aktuální počet opakování je menší než počet maximálního počtu opakování, je jej výchozí hodnotu 9 nebo hodnotu definovanou uživatelem.

    Při automatické opakování chování pomáhá zlepšovat odolnost proti chybám a použitelnost pro většinu aplikací, zřejmě ve odds při provádění srovnávacích testů výkonu, zejména v případě, že měření latence.  Latence zjištěnými klienta bude špiček Pokud experimentu dotkne omezení serveru a způsobí, že klient SDK bezobslužně opakovat. Abyste se vyhnuli latence špičky během výkonu experimenty, měřit poplatků vrácený jednotlivých operací a ujistěte se, že jsou pod rychlost vyhrazené požadavků operační požadavky. Další informace najdete v tématu [požadované jednotky](request-units.md).
3. **Návrh pro menší dokumenty pro vyšší propustnost**

    Požadavek zdarma (náklady na zpracování požadavků) dané operace je korelační přímo na velikost dokumentu. Operace u velkých dokumentů nákladů více než operací pro malé dokumenty.

## <a name="next-steps"></a>Další postup
Další informace o návrhu aplikace pro škálování a vysoký výkon, najdete v části [dělení a škálování v Azure Cosmos DB](partition-data.md).
