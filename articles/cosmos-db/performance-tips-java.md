---
title: Azure Cosmos DB tipy pro zvýšení výkonu pro Javu | Microsoft Docs
description: Další možnosti konfigurace klienta ke zlepšení výkonu databáze Azure Cosmos DB
keywords: tom, jak zvýšit výkon databáze
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: sngun
ms.openlocfilehash: bf0bd715e47cc7145a76d5f8b5f2589f24b343a8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100551"
---
> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Tipy pro zvýšení výkonu pro Azure Cosmos DB a Java
Azure Cosmos DB je rychlé a flexibilní distribuovanou databázi, která bezproblémově škáluje s zaručenou latence a propustnosti. Není nutné provádět změny hlavní architektura nebo zapsat složitý kód se škálovat databázi Azure Cosmos DB. Škálování nahoru a dolů je stejně snadná jako provedení jednoho volání rozhraní API nebo [volání metody SDK](set-throughput.md#set-throughput-java). Protože Azure Cosmos DB přistupuje prostřednictvím volání sítě jsou ale optimalizace na straně klienta můžete provést dosáhnout špičkový výkon při použití [SQL Java SDK](documentdb-sdk-java.md).

Takže pokud vás nemůže ověřit "jak vylepšit výkon Moje databáze?" Zvažte následující možnosti:

## <a name="networking"></a>Sítě
<a id="direct-connection"></a>

1. **Režim připojení: použití DirectHttps**

    Jak se klient připojí k databázi Cosmos Azure má důležité dopady na výkon, hlavně z hlediska zjištěnou latence klienta. Jeden klíč konfigurace nastavení pro konfiguraci klienta k dispozici [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) – [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode).  Dvě dostupné ConnectionModes jsou:

   1. [Brána (výchozí)](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_mode)
   2. [DirectHttps](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_mode)

    Režim brány je podporovaná na všech platformách SDK a je nakonfigurované výchozí.  Pokud vaše aplikace běží v rámci podnikové sítě s omezeními striktní brány firewall, brána je nejlepší volbou, protože používá standardní port HTTPS a jeden koncový bod. Kompromis výkonu, je ale, že brány režimu zahrnuje směrování další síti pokaždé, když je číst nebo zapisovat do databáze Cosmos Azure data. Z toho důvodu režimu DirectHttps nabízí lepší výkon z důvodu méně síťové směrování. 

    Sady Java SDK používá jako přenosový protokol HTTPS. Protokol HTTPS používá protokol SSL pro počáteční ověřování a šifrování komunikace. Při použití sady Java SDK, musí být otevřený jenom protokol HTTPS port 443. 

    Při vytváření instance DocumentClient s parametrem ConnectionPolicy je nakonfigurován ConnectionMode. 

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

    ![Obrázek připojení zásad Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Společně umístit klienty ve stejné oblasti Azure výkonu**

    Pokud je to možné, umístěte všechny aplikace, volání Azure Cosmos DB ve stejné oblasti jako databázi Azure Cosmos DB. Přibližná porovnání najdete dokončí volání do databáze Cosmos Azure v rámci stejné oblasti v rámci ms 1 – 2, ale je latence mezi – západ a východním pobřeží USA > 50 ms. Tato čekací doba můžete z požadavku na žádost pravděpodobně lišit v závislosti na směrování žádosti jako předává z klienta na hranici datového centra Azure. Nejnižší možnou latenci je dosaženo tím zajistíte, že je volající aplikace se nachází v rámci stejné oblasti Azure jako zřízené koncový bod Azure Cosmos DB. Seznam dostupných oblastí najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

    ![Obrázek připojení zásad Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Použití sady SDK
1. **Nainstalujte nejnovější sadu SDK**

    Sady SDK Azure Cosmos DB neustále probíhá lepší zajistit optimální výkon. Najdete v článku [Azure Cosmos DB SDK](documentdb-sdk-java.md) stránky, abyste zjistili nejnovější SDK a zkontrolujte vylepšení.
2. **Pomocí klienta Azure Cosmos DB singleton po dobu jeho existence vaší aplikace.**

    Každý [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) instance je bezpečné pro přístup z více vláken a provádí správu efektivní připojení a ukládání adres do mezipaměti v režimu přímého. Povolit správu efektivní připojení a lepší výkon pomocí DocumentClient, doporučujeme použít jednu instanci DocumentClient jednotlivé domény aplikace po dobu jeho existence aplikace.

   <a id="max-connection"></a>
3. **Při použití režimu brány zvýšit MaxPoolSize na hostitele**

    Azure Cosmos DB požadavky jsou při použití režimu brány proveden prostřednictvím protokolu HTTPS nebo REST a jsou vystaveny výchozí limit připojení pro hostitele nebo IP adresa. Musíte nastavit MaxPoolSize na vyšší hodnotu (200-1000) tak, aby klientské knihovny může využívat víc souběžných připojení k databázi Azure Cosmos. V jazyce Java SDK, výchozí hodnota pro [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.getmaxpoolsize) je 100. Použití [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize) ke změně hodnoty.

4. **Ladění paralelní dotazy pro dělené kolekce**

    Azure Cosmos DB SQL Java SDK verze 1.9.0 a vyšší paralelní dotazy podpory, které vám umožní dotazovat dělenou kolekci paralelně (viz [práce sady SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) a související [ukázky kódu](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) pro Další informace o). Paralelní dotazy jsou navrženy pro zlepšit latenci dotazů a propustnosti na jejich sériové protějšku.

    (a) ***ladění setMaxDegreeOfParallelism\:***  paralelní dotazy pracovní dotazováním více oddílů souběžně. Data z kolekci jednotlivých oddílů je však načtených sériově s ohledem na dotaz. Ano, použít [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism) nastavit počet oddílů, který má maximální riziko dosažení nejvíce původce dotazu zadat další podmínky systému zůstávají stejné. Pokud neznáte počet oddílů, setMaxDegreeOfParallelism můžete nastavit vysoké číslo a systém zvolí minimální (počet oddílů, uživatel zadal vstup) jako maximální stupně paralelního zpracování. 

    Je důležité si uvědomit, že paralelní dotazy vytvořit nejlepší výhody, pokud data je rovnoměrně rozdělené mezi všechny oddíly s ohledem na dotaz. Pokud dělené kolekce je rozdělena na oddíly tak, že se soustřeďuje všechny nebo většinu dat vrácených dotazem do několika oddílů (jeden oddíl v nejhorším případě), a potom výkonu dotazu by omezené podle těchto oddílů.

    (b) ***ladění setMaxBufferedItemCount\:***  paralelního dotazu slouží k předem načíst výsledky během aktuální dávku výsledky zpracování klientem. Předběžné načítání pomáhá v celkového zlepšení latence dotazu. setMaxBufferedItemCount omezuje počet předem načtených výsledků. Nastavením [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount) očekávaný počet výsledků vrácených (nebo vyšším číslem), to umožňuje dotazu z předem načítání maximální výhody nezískají.

    Předběžné načítání funguje stejným způsobem bez ohledu MaxDegreeOfParallelism a je do jedné vyrovnávací paměti pro data ze všech oddílů.  

5. **Implementace omezení rychlosti v intervalech getRetryAfterInMilliseconds**

    Během testování výkonu měli zvýšit zatížení, dokud malý počet žádostí o získání omezeny. Pokud omezené, klientská aplikace měli omezení rychlosti na omezení pro interval opakování zadaný server. Bere ohledy omezení rychlosti zajišťuje strávený minimální množství času čekat mezi opakovanými pokusy. Podpora zásad opakování je součástí verze 1.8.0 a výše z [sady Java SDK](documentdb-sdk-java.md). Další informace najdete v tématu [Exceeding vyhrazené omezení propustnosti](request-units.md#RequestRateTooLarge) a [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds).
6. **Horizontální navýšení kapacity vaše úlohy klienta**

    Pokud testujete na úrovních vysoké propustnosti (> 50 000 RU/s), klientská aplikace se může stát úzkým místem kvůli počítač omezení se na využití procesoru nebo sítě. Pokud dostanete tento bod, můžete dál tak, aby nabízel další účet Azure Cosmos DB podle škálování klientských aplikací na více serverech.

7. **Použijte název, na základě adresování**

    Použít na základě názvu adresy, kde mají odkazy formát `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, místo SelfLinks (\_vlastní), které mají formát `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` předejdete načítání ResourceIds všechny prostředky použitý k vytvoření odkazu. Navíc ukládání do mezipaměti tyto nemusí pomoci jako tyto prostředky získat znovu vytvoří (i se stejným názvem).

   <a id="tune-page-size"></a>
8. **Optimalizovat velikost stránky pro dotazy/číst informační kanály pro lepší výkon**

    Při provádění hromadně číst dokumentů pomocí čtení kanálu funkce (například [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c) nebo při vydání příkazu jazyka SQL, budou vráceny výsledky segmentovaným způsobem Pokud sadu výsledků dotazu je příliš velký. Ve výchozím nastavení budou vráceny výsledky v bloky 100 položky nebo 1 MB, z těchto omezení dosáhl první.

    Chcete-li snížit počet sítě zaokrouhlit služebních cest potřebnou k načtení všech platných výsledky, můžete zvýšit velikost stránky pomocí [x-ms-max--počet položek](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hlavička požadavku na až 1000. V případech, kdy je třeba zobrazit jen pár výsledky například pokud vaše uživatelské rozhraní nebo aplikací rozhraní API vrátí jenom 10 výsledků na dobu, může také snížit velikost stránky na 10 ke snížení propustnosti využité pro čtení a dotazy.

    Můžete také nastavit velikost stránky pomocí [setPageSize metoda](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer).

## <a name="indexing-policy"></a>Zásada indexování
 
1. **Vyloučit z indexování pro rychlejší zápisy nepoužívané cesty**

    Zásady indexování Azure Cosmos DB umožňuje určit které cesty dokumentu zahrnout nebo vyloučit z indexování s využitím indexování cesty ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths) a [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths)). Použití indexování cesty můžete jako indexování náklady jsou přímo korelační počet jedinečných cesty indexované nabízet zápisu lepší výkon a dolní index úložiště pro scénáře, ve kterých jsou předem, známé typy dotazů.  Například následující kód ukazuje, jak vyloučit celý oddíl dokumentů (také známa jako podstrom) indexování pomocí "*" zástupný znak.

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

    K měření režijní náklady na všechny operace (vytvoření, aktualizace nebo odstranění), zkontrolujte [x-ms požadavku poplatků](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) záhlaví (nebo ekvivalentní vlastnost RequestCharge v [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response) nebo [FeedResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response) k měření počtu jednotek žádosti uplatníte tyto operace.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Žádost o poplatků, vrátí se v tuto hlavičku je zlomek zřízené propustnosti. Například pokud máte 2000 zřízený RU/s, a pokud předchozí dotaz vrátí 1000 1KB – dokumenty, náklady na operaci je 1000. V rámci jedné sekundy jako takový serveru ctí jenom dva takových požadavků, než míra omezení následných žádostí. Další informace najdete v tématu [požadované jednotky](request-units.md) a [kalkulačky jednotek žádosti](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
1. **Rychlost omezení nebo požadavků popisovač míra příliš velký**

    Když se klient pokusí delší než vyhrazenou propustností pro účet, není bez snížení výkonu na serveru a bez využití kapacity propustnosti mimo úroveň vyhrazené. Server bude ho preventivně ukončit požadavek s RequestRateTooLarge (kód stavu HTTP 429) a vrátit se [x-ms opakování za ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) hlavičky, která určuje množství času v milisekundách, která uživatel musí počkat před provedením nového pokusu požadavek.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Sady SDK všechny implicitně catch této odpovědi, respektují záhlaví zadaný server opakovat po a opakujte žádost. Pokud váš účet je současně přistupuje více klientů, další pokus bude úspěšné.

    Pokud máte více než jednoho klienta kumulativně operační konzistentně vyšší rychlost požadavků, nemusí stačit výchozí počet opakování aktuálně nastavený na hodnotu 9 interně klientem; v tomto případě klient vyvolá [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) stavem code 429 k aplikaci. Výchozí počet opakování můžete změnit pomocí [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions) na [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) instance. Ve výchozím nastavení je DocumentClientException se stavovým kódem 429 vrátila po uplynutí určité doby kumulativní Počkejte 30 sekund, pokud požadavek i nadále fungovat vyšší rychlost požadavků. K tomu dojde i v případě aktuální počet opakování je menší než počet maximálního počtu opakování, je jej výchozí hodnotu 9 nebo hodnotu definovanou uživatelem.

    Při automatické opakování chování pomáhá zlepšovat odolnost proti chybám a použitelnost pro většinu aplikací, zřejmě ve odds při provádění srovnávacích testů výkonu, zejména v případě, že měření latence.  Latence zjištěnými klienta bude špiček Pokud experimentu dotkne omezení serveru a způsobí, že klient SDK bezobslužně opakovat. Abyste se vyhnuli latence špičky během výkonu experimenty, měřit poplatků vrácený jednotlivých operací a ujistěte se, že jsou pod rychlost vyhrazené požadavků operační požadavky. Další informace najdete v tématu [požadované jednotky](request-units.md).
3. **Návrh pro menší dokumenty pro vyšší propustnost**

    Požadavek zdarma (náklady na zpracování požadavků) dané operace je korelační přímo na velikost dokumentu. Operace u velkých dokumentů nákladů více než operací pro malé dokumenty.

## <a name="next-steps"></a>Další postup
Další informace o návrhu aplikace pro škálování a vysoký výkon, najdete v části [dělení a škálování v Azure Cosmos DB](partition-data.md).
