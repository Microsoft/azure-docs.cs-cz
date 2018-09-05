---
title: Azure Cosmos DB tipy ke zvýšení výkonu pro Javu | Dokumentace Microsoftu
description: Další možnosti konfigurace klienta pro zvýšení výkonu databáze Azure Cosmos DB
keywords: jak vylepšit výkon databáze
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: sngun
ms.openlocfilehash: 0a2bd840c4e93755988cf1638a6c0bdcb6b6207d
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696376"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Tipy ke zvýšení výkonu pro Azure Cosmos DB a Javou

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB je rychlé a flexibilní distribuovanou databázi, která se škáluje bez problémů s garantovanou latencí a propustností. Není nutné měnit hlavní architektury nebo zapsat složitý kód škálování databáze pomocí služby Azure Cosmos DB. Vertikální navýšení a snížení je stejně jednoduché jako jednoho volání rozhraní API nebo [volání metody SDK](set-throughput.md#set-throughput-java). Protože Azure Cosmos DB se přistupuje přes síťová volání jsou však můžete provést k dosažení maximální výkon při použití optimalizací na straně klienta [SQL Java SDK](documentdb-sdk-java.md).

Takže pokud máte s dotazem "Jak můžu vylepšit výkon Moje databáze?" Zvažte následující možnosti:

## <a name="networking"></a>Sítě
<a id="direct-connection"></a>

1. **Režim připojení: použití DirectHttps**

    Jak se klient připojí ke službě Azure Cosmos DB má důležité vliv na výkon, hlavně z hlediska pozorované latence na straně klienta. Existuje jeden klíč konfigurační nastavení pro konfiguraci klienta k dispozici [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) – [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode).  Dvě dostupné ConnectionModes jsou:

   1. [Brána (výchozí)](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_mode)
   2. [DirectHttps](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_mode)

    Režim brány se podporuje na všech platformách sady SDK a je nakonfigurované výchozí nastavení.  Pokud je aplikace spuštěná v rámci podnikové sítě s omezeními striktní brány firewall, brána je nejlepší volbou, protože používá standardní port HTTPS a jeden koncový bod. Tento režim brány zahrnuje směrování další síti pokaždé, když se data číst nebo zapisovat do služby Azure Cosmos DB je ale úkor výkonu. Z tohoto důvodu režimu DirectHttps nabízí lepší výkon z důvodu menší počet segmentů směrování. 

    Sada Java SDK používá jako přenosový protokol HTTPS. Protokol HTTPS používá protokol SSL pro počáteční ověřování a šifrování přenosu. Při použití sady Java SDK, musí být otevřené pouze na port HTTPS 443. 

    Během procesu vytváření instance DocumentClient, který se má parametr ConnectionPolicy je nakonfigurovaný ConnectionMode. 

    ```Java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
    }
        
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

    ![Obrázek připojení zásad služby Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Společné umístění klienty ve stejné oblasti Azure pro výkon**

    Pokud je to možné, umístěte všechny aplikace, volání služby Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos DB. Pro porovnávání přibližné volání do služby Azure Cosmos DB v rámci stejné oblasti dokončena do 1 až 2 ms, ale latence mezi západním a východním pobřežím USA je > 50 ms. Latence může žádost z požadavku pravděpodobně lišit v závislosti na trasu v požadavku během z klienta na hranici datového centra Azure. Nejnižší možnou latenci se dosahuje tím, že zajišťuje, že je volající aplikace umístěné ve stejné oblasti Azure jako koncový bod zřízené služby Azure Cosmos DB. Seznam dostupných oblastí naleznete v tématu [oblastí Azure](https://azure.microsoft.com/regions/#services).

    ![Obrázek připojení zásad služby Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Použití sady SDK
1. **Nainstalujte nejnovější sadu SDK**

    Se sadami SDK služby Azure Cosmos DB se neustále vylepšují pro zajištění nejlepšího výkonu. Najdete v článku [Azure Cosmos DB SDK](documentdb-sdk-java.md) stránky pro určení nejnovější sady SDK a zkontrolujte vylepšení.
2. **Použití klienta služby Azure Cosmos DB jednotlivý prvek po dobu životnosti vaší aplikace**

    Každý [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) instance je bezpečná pro vlákno a provádí připojení efektivní správu a ukládání adres do mezipaměti při fungování v přímém režimu. Povolit správu efektivní připojení a lepší výkon pomocí DocumentClient, doporučujeme použít jednu instanci DocumentClient na doménu aplikace po dobu životnosti aplikace.

   <a id="max-connection"></a>
3. **Zvýšit MaxPoolSize na hostitele, když používáte režim brány**

    Azure Cosmos DB požadavků probíhají přes protokol HTTPS nebo rozhraní REST, když používáte režim brány a jsou vystaveny výchozí limit pro připojení za název hostitele nebo IP adresu. Budete muset nastavit MaxPoolSize na vyšší hodnotu (200-1000) tak, aby Klientská knihovna může využívat více současných připojení ke službě Azure Cosmos DB. V sadě Java SDK, výchozí hodnota pro [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.getmaxpoolsize) je 100. Použití [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize) ke změně hodnoty.

4. **Ladění používejte u dělených kolekcí paralelní dotazy**

    Azure Cosmos DB SQL Java SDK verze 1.9.0 a vyšší podporu paralelních dotazů, které vám umožní dotazovat do dělené kolekce paralelně (viz [práce spolu se sadami SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) a související [ukázky kódu](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) pro Další informace o). Paralelní dotazy jsou navržené ke zlepšení latence dotazu a propustnost nad jejich protějšky sériového portu.

    (a) ***ladění setMaxDegreeOfParallelism\:***  paralelní dotazy pracovní dotazováním více oddílů souběžně. Data z jednotlivých dělené kolekce je však načíst sériově s ohledem na dotaz. Ano, použít [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism) chcete nastavit počet oddílů, který má maximální příležitost dosáhnout největší výkonných dotazů, k dispozici všechny ostatní podmínky systém zůstávají stejné. Pokud si nejste jisti počet oddílů, setMaxDegreeOfParallelism můžete použít k nastavení vysoké číslo a systém zvolí jako maximální volnost paralelismu minimální (počet oddílů, vstup uživatele, které jsou k dispozici). 

    Je důležité si uvědomit, že paralelní dotazy vytvořit nejlepší výhody, pokud je data rovnoměrně rozdělené mezi všechny oddíly s ohledem na dotaz. Pokud dělené kolekce je rozdělit na oddíly tak, že se soustřeďuje všechny nebo většina dat vrácených dotazem v několika oddílech (jeden oddíl v nejhorším případě) a pak výkon dotazu by bottlenecked podle těchto oddílů.

    (b) ***ladění setMaxBufferedItemCount\:***  paralelní dotaz byl navržen k předběžného načítání výsledků během zpracování dávky aktuální výsledky klientem. Předběžné načítání pomáhá zvyšování celkové latence dotazu. setMaxBufferedItemCount omezuje počet předem počet získaných výsledků. Nastavením [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount) očekávaný počet výsledků vrácených (nebo vyššího čísla), díky tomu dotazu pro příjem maximální výhody z předběžného načítání.

    Předběžné načítání funguje stejným způsobem bez ohledu na to, z MaxDegreeOfParallelism a jednu vyrovnávací paměti pro data ze všech oddílů.  

5. **Implementace omezení rychlosti intervalech getRetryAfterInMilliseconds**

    Během testování výkonnosti, měli byste zvýšit zatížení dokud malý počet žádostí o získání omezené. Když omezený, klientská aplikace má omezení rychlosti na omezení pro interval opakování zadaný server. Respektování omezení rychlosti zajistí, že strávíte co nejkratším čase čekat mezi opakovanými pokusy. Podpora zásad opakování je součástí verze 1.8.0 a nad z [sady Java SDK](documentdb-sdk-java.md). Další informace najdete v tématu [vyhrazené překročení omezení propustnosti](request-units.md#RequestRateTooLarge) a [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds).
6. **Horizontální navýšení kapacity vašich úloh klienta**

    Pokud testujete při vysoké propustnosti (> 50 000 RU/s), aplikace klienta se může stát kritickým bodem kvůli počítač omezení navýšení kapacity na využití procesoru nebo sítě. Pokud překročíte tento bod, můžete nadále push další účet Azure Cosmos DB díky horizontálnímu navýšení kapacity klientských aplikací napříč několika servery.

7. **Použití adresování podle názvu**

    Použijte název založený adresování, kde odkazy mají formát `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, namísto SelfLinks (\_vlastní), které mají formát `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` aby se zabránilo načítání ResourceId všechny prostředky používané k vytvoření propojení. Také jak tyto prostředky načíst znovu vytvořit (možná se stejným názvem), ukládání do mezipaměti tyto nepomůže.

   <a id="tune-page-size"></a>
8. **Vyladěním stránek pro informační kanály pro zajištění lepšího výkonu dotazů/čtení**

    Funkce při provádění hromadné čtení dokumentů s použitím čtení kanálu (třeba [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c) nebo při vydání příkazu jazyka SQL, výsledky se vrátí segmentovaným způsobem, pokud sada výsledků je příliš velký. Ve výchozím nastavení výsledky jsou vráceny v blocích 100 položek nebo 1 MB, podle omezení dosáhnete první.

    Abyste snížili počet sítě zaokrouhlit zkracuje dobu odezvy potřebný k načtení všech platných výsledky, můžete zvýšit velikost stránky pomocí [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hlavičku požadavku na až 1000. V případech, kdy potřebujete zobrazit jenom pár výsledky například pokud vaše uživatelské rozhraní nebo aplikací rozhraní API vrátí jenom 10 výsledky čas, může také snížit velikost stránky na 10 ke snížení propustnosti využité pro dotazy a čtení.

    Můžete také nastavit velikost stránky pomocí [setPageSize metoda](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer).

## <a name="indexing-policy"></a>Zásada indexování
 
1. **Vyloučit cesty nevyužité indexování pro rychlejší zápisy**

    Zásady indexování služby Azure Cosmos DB umožňuje určit, které dokumentu cesty pro zahrnutí nebo vyloučení z indexování s využitím indexování cesty ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths) a [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths)). Použití indexování cesty může nabídnout zápisu lepší výkon a dolní index úložiště pro scénáře, ve kterých vzory dotazů znám předem, jako jsou indexování náklady přímo korelační počtu jedinečné cesty indexované.  Například následující kód ukazuje, jak vyloučit celý oddíl dokumenty (označovaný také jako podstrom) z indexování pomocí "*" zástupný znak.

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

    K měření nároky na jakékoli operace (vytvoření, aktualizace nebo odstranění), zkontrolujte [x-ms žádost poplatek](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) záhlaví (nebo ekvivalentní vlastnost RequestCharge v [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response) nebo [FeedResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response) měření počtu jednotek žádosti spotřebovaná těchto operací.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Vrácená v hlavičce tohoto platí požadavek se zlomek zřízené propustnosti. Například pokud máte 2000 zřízené RU/s, a pokud předchozí dotaz vrací 1000 1KB – dokumenty, náklady na operace je 1000. V rámci jedné sekundy, v důsledku toho serveru respektuje pouze dva takové požadavky před tady následných žádostí. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek žádosti](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
1. **Zpracování frekvence omezení/požadavků míra příliš velká**

    Když se klient pokusí překročí vyhrazené propustnosti pro účet, neexistuje žádné snížení výkonu na serveru a používat sady kapacitou propustnosti mimo úroveň rezervované. Server preventivně ukončení požadavku s RequestRateTooLarge (kód stavu HTTP 429), který se vrátit [x-ms opakování po ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) záhlaví určující dobu v milisekundách, které musí uživatel čekat před opakováním požadavek.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Sady SDK všechny implicitně zachytit tuto odpověď, respektují zadaný server hlavičkou retry-after a opakujte žádost. Pokud váš účet je současně přistupuje více klientů, bude při dalším pokusu úspěšné.

    Pokud máte více než jednoho klienta kumulativně provozní konzistentně výše je frekvence požadavků, nemusí stačit výchozí počet opakování aktuálně nastavená na 9 interně klientem; v tomto případě klient vyvolá [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) se stavem kódu 429 do aplikace. Výchozí počet opakování můžete změnit pomocí [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions) na [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) instance. Ve výchozím nastavení je vrácena DocumentClientException se stavovým kódem 429 po uplynutí určité doby kumulativní Počkejte 30 sekund, pokud požadavek dál pracovat nad frekvence požadavků. K tomu dojde i v případě aktuální počet opakování je menší než počet opakování, jde o výchozí hodnotu 9 nebo uživatelem definovanou hodnotu.

    Při automatické opakování chování pomáhá zlepšit odolnost proti chybám a použitelnost pro většinu aplikací, zřejmě na odds při provádění srovnávací testy výkonu, zejména v případě měření latence.  Klienta pozorované latence se dosahuje, pokud experiment narazí na server omezení a způsobí, že klientská sada SDK tiše opakování. Aby se zabránilo latence dosahující během výkonu experimenty, měřit poplatek vrácený každou operaci a ujistěte se, jsou požadavky funguje pod sazbu žádost o rezervovanou. Další informace najdete v tématu [jednotky žádostí](request-units.md).
3. **Návrh pro zajištění vyšší propustnost menších dokumentů**

    Zátěž žádostí (náklady na zpracování žádosti) dané operace jsou korelována přímo velikost dokumentu. Operace na velkých dokumentů nákladů více než operace pro malé dokumenty.

## <a name="next-steps"></a>Další postup
Další informace o návrhu vaší aplikace pro škálování a vysoký výkon, najdete v článku [dělení a škálování ve službě Azure Cosmos DB](partition-data.md).
