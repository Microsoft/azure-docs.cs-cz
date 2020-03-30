---
title: Tipy pro výkon Azure Cosmos DB pro Jazyk Java
description: Naučte se možnosti konfigurace klienta pro zlepšení výkonu databáze Azure Cosmos
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 3b7d221c2afc952f40da035c6e2c282b3b932aa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69616760"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Tipy pro zvýšení výkonu pro Azure Cosmos DB a Javu

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která se bezproblémově škáluje s garantovanou latencí a propustností. Není třeba provádět hlavní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Škálování nahoru a dolů je stejně snadné jako volání jednoho rozhraní API. Další informace naleznete v tématu jak [zřídit propustnost kontejneru](how-to-provision-container-throughput.md) nebo [jak zřídit propustnost databáze](how-to-provision-database-throughput.md). Protože však azure cosmos DB je přístupná prostřednictvím síťových volání existují optimalizace na straně klienta, které můžete provést k dosažení špičkového výkonu při použití [sady SQL Java SDK](documentdb-sdk-java.md).

Pokud se tedy ptáte: "Jak mohu zlepšit výkon databáze?" zvážit následující možnosti:

## <a name="networking"></a>Síťové služby
<a id="direct-connection"></a>

1. **Režim připojení: Použití directhttps**

    Způsob, jakým se klient připojuje ke službě Azure Cosmos DB, má významný vliv na výkon, a to zejména z hlediska pozorované latence na straně klienta. Pro konfiguraci zásad [připojení klienta –](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)je k dispozici jedno nastavení konfigurace klíče .  Dva dostupné ConnectionModes jsou:

   1. [Brána (výchozí)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      Režim brány je podporován na všech platformách sady SDK a je nakonfigurovaným výchozím nastavením.  Pokud vaše aplikace běží v rámci podnikové sítě s přísnými omezeními brány firewall, gateway je nejlepší volbou, protože používá standardní port HTTPS a jeden koncový bod. Kompromis výkonu je však, že režim brány zahrnuje další síťový směrování pokaždé, když se data číst nebo zapisovat do Azure Cosmos DB. Z tohoto důvodu režim DirectHttps nabízí lepší výkon z důvodu menšího počtu síťových směrování. 

      Sada Java SDK používá protokol HTTPS jako přenosový protokol. Protokol HTTPS používá protokol SSL pro počáteční ověřování a šifrování přenosů. Při použití sady Java SDK musí být otevřený pouze port HTTPS 443. 

      Režim connectionmode je konfigurován během výstavby instance DocumentClient s parametrem ConnectionPolicy. 

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

      ![Ilustrace zásad připojení Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Collocate klienty ve stejné oblasti Azure pro výkon**

    Pokud je to možné, umístěte všechny aplikace volající Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos. Pro přibližné porovnání volání Azure Cosmos DB ve stejné oblasti dokončena do 1–2 ms, ale latence mezi západní a východní pobřeží USA je >50 ms. Tato latence se může pravděpodobně lišit od požadavku k požadavku v závislosti na trase provedené požadavkem při jeho předávání z klienta na hranici datového centra Azure. Nejnižší možné latence je dosaženo zajištěním volající aplikace se nachází ve stejné oblasti Azure jako zřízené Azure Cosmos DB koncového bodu. Seznam dostupných oblastí najdete v tématu [Oblasti Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustrace zásad připojení Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Využití sady SDK
1. **Instalace nejnovější sady SDK**

    Sady Azure Cosmos DB SDK se neustále vylepšují, aby poskytovaly nejlepší výkon. Podívejte se na stránky [Azure Cosmos DB SDK](documentdb-sdk-java.md) k určení nejnovější sady SDK a vylepšení kontroly.
2. **Používejte klienta Azure Cosmos DB po dobu životnosti vaší aplikace**

    Každá instance [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) je bezpečná pro přístup z více vláken a provádí efektivní správu připojení a ukládání adres do mezipaměti při práci v přímém režimu. Chcete-li povolit efektivní správu připojení a lepší výkon podle DocumentClient, doporučuje me použít jednu instanci DocumentClient per AppDomain po dobu životnosti aplikace.

   <a id="max-connection"></a>
3. **Zvýšení velikosti maxpoolu na hostitele při použití režimu brány**

    Požadavky Azure Cosmos DB se provádějí přes PROTOKOL HTTPS/REST při použití režimu brány a podléhají výchozímu limitu připojení na název hostitele nebo IP adresu. Možná budete muset nastavit MaxPoolSize na vyšší hodnotu (200-1000), takže klientská knihovna může využívat více souběžných připojení k Azure Cosmos DB. V sadě Java SDK výchozí hodnota [pro ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) je 100. Chcete-li změnit hodnotu, použijte [setMaxPoolSize.]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize)

4. **Optimalizace paralelních dotazů pro rozdělené kolekce**

    Azure Cosmos DB SQL Java SDK verze 1.9.0 a vyšší podporují paralelní dotazy, které umožňují paralelní dotaz na rozdělenou kolekci. Další informace naleznete v [tématu ukázky kódu](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) související s prací s sadami SDK. Paralelní dotazy jsou navrženy tak, aby zlepšit latenci dotazu a propustnost přes jejich sériový protějšek.

    (a) ***Optimalizace\: setMaxDegreeOfParallelism Paralelní*** dotazy práce dotazováním více oddílů paralelně. Data z jednotlivých oddílové kolekce je však načtensériově s ohledem na dotaz. Takže použijte [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) nastavit počet oddílů, který má maximální šanci na dosažení nejvýkonnější dotaz, za předpokladu, že všechny ostatní systémové podmínky zůstávají stejné. Pokud neznáte počet oddílů, můžete použít setMaxDegreeOfParallelism nastavit vysoké číslo a systém zvolí minimální (počet oddílů, uživatel za předpokladu, vstup) jako maximální stupeň paralelismu. 

    Je důležité si uvědomit, že paralelní dotazy vytvořit nejlepší výhody, pokud data je rovnoměrně distribuovány mezi všechny oddíly s ohledem na dotaz. Pokud je rozdělená kolekce rozdělena tak, že všechna nebo většina dat vrácených dotazem je soustředěna v několika oddílech (jeden oddíl v nejhorším případě), pak výkon dotazu by byl kritický těmito oddíly.

    (b) ***Optimalizace setMaxBufferedItemCount\: *** Paralelní dotaz je určen k pre-fetch výsledky při aktuální dávka výsledků je zpracovávána klientem. Předběžné načítání pomáhá při celkovém zlepšení latence dotazu. setMaxBufferedItemCount omezuje počet předem načtených výsledků. Nastavením [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) na očekávaný počet vrácených výsledků (nebo vyšší číslo), to umožňuje dotazu získat maximální užitek z předběžného načítání.

    Předběžné načítání funguje stejným způsobem bez ohledu na MaxDegreeOfParallelism a je jedna vyrovnávací paměť pro data ze všech oddílů.  

5. **Implementovat backoff v intervalech getRetryAfterInMilliseconds**

    Během testování výkonu byste měli zvýšit zatížení, dokud se neomezí malá rychlost požadavků. Pokud omezena, klientská aplikace by měla backoff na omezení pro interval opakování zadané serverem. Respektování backoff zajišťuje, že strávíte minimální množství času čekání mezi opakování. Podpora zásad opakování je součástí verze 1.8.0 a vyšší sady [Java SDK](documentdb-sdk-java.md). Další informace naleznete v tématu [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Horizontální navýšení kapacity úloh y klienta**

    Pokud testujete na vysokých úrovních propustnost (>50 000 RU/s), může se klientská aplikace stát kritickým bodem z důvodu omezení počítače na využití procesoru nebo sítě. Pokud dosáhnete tohoto bodu, můžete pokračovat v dalším nabízení účtu Azure Cosmos DB horizontálním navýšením kapacity klientských aplikací na více serverech.

7. **Použít adresování založené na názvu**

    Použijte adresování založené na názvu, `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`kde odkazy mají\_formát , namísto SelfLinks (self), které mají formát, `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` aby se zabránilo načítání ResourceIds všech prostředků použitých k vytvoření propojení. Také jako tyto prostředky získat znovu (případně se stejným názvem), ukládání do mezipaměti tyto nemusí pomoci.

   <a id="tune-page-size"></a>
8. **Vyladění velikosti stránky pro dotazy/zdroje pro čtení pro lepší výkon**

    Při hromadném čtení dokumentů pomocí funkce informačního kanálu pro čtení (například [readDocuments)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)nebo při vydávání dotazu SQL jsou výsledky vráceny segmentovaným způsobem, pokud je sada výsledků příliš velká. Ve výchozím nastavení jsou výsledky vráceny v blocích 100 položek nebo 1 MB, podle toho, jaký limit je přístupů jako první.

    Chcete-li snížit počet síťových zpátečních cest potřebných k načtení všech platných výsledků, můžete zvětšit velikost stránky pomocí hlavičky požadavku [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) až na 1000. V případech, kdy potřebujete zobrazit pouze několik výsledků, například pokud vaše uživatelské rozhraní nebo rozhraní API aplikace vrátí pouze 10 výsledků za čas, můžete také snížit velikost stránky na 10 snížit propustnost spotřebované pro čtení a dotazy.

    Můžete také nastavit velikost stránky pomocí [metody setPageSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Zásady indexování
 
1. **Vyloučit nepoužívané cesty z indexování pro rychlejší zápisy**

    Zásady indexování Azure Cosmos DB umožňuje určit, které cesty dokumentu zahrnout nebo vyloučit z indexování pomocí indexování cesty[(setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) a [setExcludedPaths).](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths) Použití cesty indexování může nabídnout lepší výkon zápisu a nižší index úložiště pro scénáře, ve kterých jsou předem známy vzorky dotazu, protože náklady na indexování jsou přímo korelují s počtem jedinečných cest indexovaných.  Následující kód například ukazuje, jak vyloučit celou část dokumentů (také od podstrom) z indexování pomocí zástupný znak "*".

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

1. **Měření a ladění pro použití s nižšími požadavky/sekunda**

    Azure Cosmos DB nabízí bohatou sadu databázových operací, včetně relačních a hierarchických dotazů s UD, uloženými procedurami a aktivačními událostmi – všechny pracují s dokumenty v rámci kolekce databází. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, V/V a paměti, které jsou potřeba k dokončení operace. Místo přemýšlení o a správě hardwarových prostředků si můžete jednotku požadavku (RU) považovat za jediné opatření pro prostředky potřebné k provádění různých databázových operací a servisu požadavku aplikace.

    Propustnost je zřízena na základě počtu [jednotek požadavků](request-units.md) nastavených pro každý kontejner. Spotřeba jednotky požadavku je vyhodnocována jako sazba za sekundu. Aplikace, které překračují zřízenou jednotkovou sazbu požadavku pro jejich kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň pro kontejner. Pokud vaše aplikace vyžaduje vyšší úroveň propustnost, můžete zvýšit propustnost zřízením další chynaté jednotky. 

    Složitost dotazu má vliv na to, kolik jednotek požadavků je spotřebováno pro operaci. Počet predikátů, povaha predikátů, počet UD f a velikost zdrojové sady dat ovlivňují náklady na operace dotazu.

    Chcete-li měřit režii jakékoli operace (vytvoření, aktualizace nebo odstranění), zkontrolujte hlavičku [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (nebo ekvivalentní vlastnost RequestCharge v [resourceresponse\<t>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) nebo [FeedResponse\<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) k měření počtu jednotek požadavků spotřebovaných těmito operacemi.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Poplatek za požadavek vrácený v této hlavičce je zlomkem zřízené propustnosti. Například pokud máte 2000 RU/s zřízena a pokud předchozí dotaz vrátí 1000 1KB-dokumenty, náklady na operaci je 1000. Jako takové během jedné sekundy server respektuje pouze dva takové požadavky před omezením rychlosti následné požadavky. Další informace naleznete v tématu [Request units](request-units.md) and the request [unit calculator](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Zpracovat rychlost omezení /rychlost požadavku příliš velký**

    Pokud se klient pokusí překročit vyhrazenou propustnost pro účet, neexistuje žádné snížení výkonu na serveru a žádné použití kapacity propustnosti nad vyhrazenou úroveň. Server preventivně ukončí požadavek pomocí RequestRateTooLarge (stavový kód HTTP 429) a vrátí hlavičku [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) označující dobu v milisekundách, po kterou musí uživatel před opětovným pokusem o požadavek počkat.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Všechny sady SDK implicitně zachytit tuto odpověď, respektovat serverem zadané opakování po záhlaví a opakujte požadavek. Pokud váš účet není přístupná současně více klientů, další opakování bude úspěšné.

    Pokud máte více než jednoho klienta, který kumulativně pracuje konzistentně nad sazbu požadavku, výchozí počet opakování aktuálně nastavený klientem na 9 interně nemusí stačit. v tomto případě klient vyvolá [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) se stavovým kódem 429 do aplikace. Výchozí počet opakování lze změnit pomocí [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) na [connectionpolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) instance. Ve výchozím nastavení DocumentClientException se stavovým kódem 429 je vrácena po kumulativní čekací doba 30 sekund, pokud požadavek nadále pracovat nad sazbu požadavku. K tomu dochází i v případě, že aktuální počet opakování je menší než maximální počet opakování, ať už je to výchozí hodnota 9 nebo uživatelem definovaná hodnota.

    Zatímco automatické chování opakování pomáhá zlepšit odolnost proti chybám a použitelnost pro většinu aplikací, může přijít v rozporu při provádění výkonnostních měřítek, zejména při měření latence.  Latence s pozorovaná klientem se zvýší, pokud experiment narazí na serverovou škrticí klapku a způsobí, že sada SDK klienta tiše provede opakování. Chcete-li se vyhnout špičkám latence během experimentů s výkonem, změřte poplatek vrácený každou operací a ujistěte se, že požadavky pracují pod rezervovanou sazbou požadavku. Další informace naleznete v tématu [Request units](request-units.md).
3. **Návrh pro menší dokumenty pro vyšší propustnost**

    Poplatek za požadavek (náklady na zpracování požadavku) dané operace přímo souvisí s velikostí dokumentu. Operace s velkými dokumenty stojí více než operace pro malé dokumenty.

## <a name="next-steps"></a>Další kroky
Další informace o návrhu aplikace pro škálování a vysoký výkon najdete [v tématu dělení a škálování v Azure Cosmos DB](partition-data.md).
