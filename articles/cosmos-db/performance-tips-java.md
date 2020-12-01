---
title: Tipy ke zvýšení výkonu pro Azure Cosmos DB synchronizaci Java SDK v2
description: Seznamte se s možnostmi konfigurace klientů pro zlepšení výkonu služby Azure Cosmos Database pro synchronizaci sady Java SDK v2
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 3eced4e727954906d8ff67e7afe2279100f8f1cc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349057"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Tipy ke zvýšení výkonu pro Azure Cosmos DB synchronizaci Java SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Sada Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Sada Async Java SDK v2](performance-tips-async-java.md)
> * [Sada Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> Nejedná *se o* nejnovější sadu Java SDK pro Azure Cosmos DB. Projekt byste měli upgradovat na [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) a pak si přečtěte příručku Azure Cosmos DB Java SDK v4 – [Průvodce tipy pro výkon](performance-tips-java-sdk-v4-sql.md). Postupujte podle pokynů v tématu [migrace do Azure Cosmos DB příručka Java SDK v4](migrate-java-v4-sdk.md) a příručka [vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) Guide to upgrade. 
> 
> Tyto tipy k výkonu jsou pro Azure Cosmos DB synchronizaci pouze s Java SDK v2. Další informace najdete v [poznámkách k vydání verze](sql-api-sdk-java.md) Java SDK v2 a Azure Cosmos DB v [úložišti Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) .
>

Azure Cosmos DB je rychlá a flexibilní distribuovaná databáze, která bez problémů škáluje zaručenou latenci a propustnost. Nemusíte dělat zásadní změny architektury nebo psát složitý kód pro škálování databáze pomocí Azure Cosmos DB. Vertikální navýšení a snížení kapacity je stejně snadné jako při provádění jediného volání rozhraní API. Další informace najdete v tématu [jak zřídit propustnost kontejneru](how-to-provision-container-throughput.md) nebo [jak zřídit propustnost databáze](how-to-provision-database-throughput.md). Vzhledem k tomu, že k Azure Cosmos DB je k dispozici prostřednictvím síťových volání, jsou k dispozici optimalizace na straně klienta, které vám při použití [Azure Cosmos DB synchronizaci sady Java SDK v2](./sql-api-sdk-java.md)umožní dosáhnout špičkového výkonu.

Takže pokud si vyžádáte "Jak můžu vylepšit výkon databáze?" Vezměte v úvahu následující možnosti:

## <a name="networking"></a>Sítě
<a id="direct-connection"></a>

1. **Režim připojení: použití DirectHttps**

    Způsob, jakým se klient připojuje ke službě Azure Cosmos DB, má významný vliv na výkon, a to zejména z hlediska pozorované latence na straně klienta. Pro konfiguraci [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) klienta – [ConnectionMode](/java/api/com.microsoft.azure.documentdb.connectionmode)je k dispozici jedno nastavení konfigurace klíče.  K dispozici jsou tyto dvě ConnectionModes:

   1. [Brána (výchozí)](/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](/java/api/com.microsoft.azure.documentdb.connectionmode)

      Režim brány je podporován na všech platformách sady SDK a je nastaven jako výchozí.  Pokud vaše aplikace běží v podnikové síti s přísnými omezeními brány firewall, je nejlepší volbou brána, protože používá standardní port HTTPS a jeden koncový bod. Kompromis mezi výkonem je ale v tom, že režim brány zahrnuje další směrování sítě při každém čtení nebo zápisu dat do Azure Cosmos DB. Z tohoto důvodu nabízí režim DirectHttps lepší výkon kvůli menšímu počtu směrování sítě. 

      Azure Cosmos DB Synchronization Java SDK v2 používá jako transportní protokol HTTPS. Protokol HTTPS používá pro počáteční ověřování a šifrování provozu protokol TLS. Při použití Azure Cosmos DB synchronizaci Java SDK v2 musí být otevřený jenom port HTTPS 443. 

      ConnectionMode se konfiguruje během vytváření instance DocumentClient s parametrem ConnectionPolicy. 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>Synchronizace sady Java SDK v2 (Maven com. Microsoft. Azure:: Azure-DocumentDB)

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

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="Diagram znázorňuje zásady připojení Azure Cosmos D B." border="false":::

   <a id="same-region"></a>
2. **Společné umístění klientů ve stejné oblasti Azure pro výkon**

    Pokud je to možné, umístěte všechny aplikace, které volají Azure Cosmos DB ve stejné oblasti jako databáze Azure Cosmos. V případě přibližného porovnání se volání Azure Cosmos DB v rámci stejné oblasti dokončí v 1-2 MS, ale latence mezi západním a východním pobřežím USA je >50 ms. Tato latence se může lišit od požadavku na vyžádání v závislosti na trasách, kterou požadavek prochází z klienta na hranici datacentra Azure. Nejnižší možná latence se dosahuje tím, že se zaručí, že se volající aplikace nachází ve stejné oblasti Azure jako koncový bod zřízené Azure Cosmos DB. Seznam oblastí, které jsou k dispozici, najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/#services).

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Diagram zobrazuje žádosti a odpovědi ve dvou oblastech, kde se počítače připojují k účtu Cosmos D B prostřednictvím služeb střední vrstvy." border="false":::
   
## <a name="sdk-usage"></a>Využití sady SDK
1. **Nainstalovat nejnovější sadu SDK**

    Sady Azure Cosmos DB SDK se neustále zdokonalují, aby poskytovaly nejlepší výkon. Pokud chcete zjistit nejnovější sadu SDK a zkontrolovat vylepšení, podívejte se na stránky [Azure Cosmos DB SDK](./sql-api-sdk-java.md) .
2. **Použití typu Singleton Azure Cosmos DB klienta po dobu života vaší aplikace**

    Každá instance [DocumentClient](/java/api/com.microsoft.azure.documentdb.documentclient) je bezpečná pro přístup z více vláken a při provozu v přímém režimu provádí efektivní správu připojení a ukládání adres do mezipaměti. Aby bylo možné efektivně spravovat správu připojení a lepší výkon díky DocumentClient, doporučujeme pro celou doménu aplikace používat jednu instanci DocumentClient.

   <a id="max-connection"></a>
3. **Při použití režimu brány zvýšit MaxPoolSize na hostitele**

    Azure Cosmos DB požadavky se při použití režimu brány převezmou přes HTTPS/REST a řídí se výchozí limit počtu připojení na název hostitele nebo IP adresa. Je možné, že budete muset nastavit MaxPoolSize na vyšší hodnotu (200-1000), aby Klientská knihovna mohla využívat více souběžných připojení k Azure Cosmos DB. V Azure Cosmos DB synchronizace Java SDK v2 je výchozí hodnota pro [ConnectionPolicy. getMaxPoolSize](/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) 100. Hodnotu můžete změnit pomocí [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) .

4. **Ladění paralelních dotazů pro dělené kolekce**

    Azure Cosmos DB Sync Java SDK verze 1.9.0 a vyšší podporují paralelní dotazy, které umožňují paralelní dotazování rozdělené kolekce. Další informace najdete v tématu [ukázky kódu](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) týkající se práce se sadami SDK. Paralelní dotazy jsou navržené tak, aby se zlepšila latence a propustnost dotazů v rámci svého sériového protějšku.

    (a) **_ladění setMaxDegreeOfParallelism \:_* _ paralelní dotazy fungují s dotazem na více oddílů paralelně. Data z jednotlivých dělených kolekcí se ale v souvislosti s dotazem načítají sériově. Proto použijte [setMaxDegreeOfParallelism](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) k nastavení počtu oddílů, které mají maximální šanci dosáhnout nejvíce výkonného dotazu. za předpokladu, že všechny ostatní systémové podmínky zůstanou stejné. Pokud neznáte počet oddílů, můžete použít setMaxDegreeOfParallelism k nastavení vysokého čísla a systém zvolí minimální (počet oddílů, uživatelem zadaný vstup) jako maximální stupeň paralelismu. 

    Je důležité si uvědomit, že paralelní dotazy poskytují nejlepší výhody, pokud jsou data rovnoměrně rozložena napříč všemi oddíly v souvislosti s dotazem. Pokud je dělená kolekce rozdělena takovým způsobem, že všechna nebo většina dat vrácených dotazem je soustředěna v několika oddílech (jeden oddíl v nejhorším případě), výkon dotazu by tyto oddíly měl být kritický.

    (b) _*_optimalizace \:_*_ paralelního dotazu setMaxBufferedItemCount je navržena tak, aby se výsledky předem načetly, zatímco je aktuální dávka výsledků zpracovávána klientem. Předběžné načítání pomáhá při celkové latenci v rámci dotazu. setMaxBufferedItemCount omezuje počet předběžně načtených výsledků. Nastavením [setMaxBufferedItemCount](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) na očekávaný počet vrácených výsledků (nebo vyšší číslo) Tím umožníte, aby dotaz získal maximální přínos před načtením.

    Předběžné načítání funguje stejným způsobem bez ohledu na Z MaxDegreeOfParallelism a existuje jedna vyrovnávací paměť pro data ze všech oddílů.  

5. _ *Implementovat omezení rychlosti v intervalech getRetryAfterInMilliseconds**

    Během testování výkonu byste měli zvýšit zatížení až do omezení malých sazeb požadavků. V případě omezení by se klientská aplikace měla omezení rychlosti na omezení pro interval opakování zadaný serverem. Respektování omezení rychlosti zajistí, že strávíte minimální dobu čekání mezi opakovanými pokusy. Podpora zásad opakování je obsažená ve verzi 1.8.0 a vyšší než [Azure Cosmos DB Sync Java SDK](./sql-api-sdk-java.md). Další informace najdete v tématu [getRetryAfterInMilliseconds](/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Horizontální navýšení kapacity klienta – zatížení**

    Pokud testujete na úrovních vysoké propustnosti (>50 000 RU/s), může se klientská aplikace stát kritickým bodem, protože počítač capping na využití procesoru nebo sítě. Pokud se dostanou k tomuto bodu, můžete dál nasdílet Azure Cosmos DB účet tím, že navedete horizontální navýšení kapacity klientských aplikací napříč více servery.

7. **Použití adresování na základě názvu**

    Použijte adresování na základě názvu, kde odkazy mají formát `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` namísto SelfLinks ( \_ samoobslužný), který má formát, `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` aby nedocházelo k načítání ResourceID všech prostředků použitých k vytvoření odkazu. I když se tyto prostředky znovu vytvoří (možná se stejným názvem), nemusí ukládání do mezipaměti pomáhat.

   <a id="tune-page-size"></a>
8. **Vyladění velikosti stránek pro dotazy a kanály pro čtení pro lepší výkon**

    Při hromadném čtení dokumentů pomocí funkce kanálu pro čtení (například [readDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) nebo při vystavování dotazu SQL se výsledky vrátí segmenticky, pokud je sada výsledků příliš velká. Ve výchozím nastavení se výsledky vrátí do bloků 100 položek nebo 1 MB, podle toho, který limit se narazí jako první.

    Chcete-li snížit počet síťových přenosů potřebných k načtení všech použitelných výsledků, můžete zvětšit velikost stránky v hlavičce požadavku [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) až na 1000. V případech, kdy potřebujete zobrazit jenom pár výsledků, například pokud vaše uživatelské rozhraní nebo rozhraní API pro aplikace vrátí jenom 10 výsledků, můžete také zmenšit velikost stránky na 10 a snížit tak propustnost, která se pro čtení a dotazy spotřebují.

    Velikost stránky můžete nastavit také pomocí [metody setPageSize](/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Zásady indexování
 
1. **Vyloučení nepoužívaných cest z indexování za účelem zrychlení zápisu**

    Pomocí cest indexování ([setIncludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) a [setExcludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)) můžete určit, které cesty dokumentů zahrnout nebo vyloučit z indexování. Azure Cosmos DB Použití cest indexování může nabízet lepší výkon zápisu a nižší indexové úložiště pro scénáře, ve kterých jsou vzory dotazů předem známé, protože náklady na indexování jsou přímo ve vztahu k počtu indexovaných jedinečných cest.  Například následující kód ukazuje, jak vyloučit celý oddíl (podstrom) dokumentů z indexování pomocí zástupného znaku "*".


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>Synchronizace sady Java SDK v2 (Maven com. Microsoft. Azure:: Azure-DocumentDB)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Další informace najdete v tématu [Azure Cosmos DB zásady indexování](./index-policy.md).

## <a name="throughput"></a>Propustnost
<a id="measure-rus"></a>

1. **Měření a optimalizace pro nižší jednotky žádostí za sekundu použití**

    Azure Cosmos DB nabízí bohatou sadu databázových operací, včetně relačních a hierarchických dotazů s UDF, uloženými procedurami a triggery – to vše v dokumentech v rámci kolekce databáze. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, V/V a paměti, které jsou potřeba k dokončení operace. Místo toho, abyste si vymysleli a spravovali hardwarové prostředky, si můžete představit jednotku žádosti (RU) jako jednu míru prostředků potřebných k provádění různých databázových operací a zpracování požadavků aplikace.

    Propustnost se zřizuje na základě počtu [jednotek žádostí](request-units.md) nastavených pro každý kontejner. Spotřeba jednotky požadavku se vyhodnotí jako sazba za sekundu. Aplikace, které překračují sazbu jednotky zřízené žádosti pro svůj kontejner, jsou omezené, dokud sazba neklesne pod zřízenou úroveň kontejneru. Pokud vaše aplikace vyžaduje vyšší úroveň propustnosti, můžete zvýšit svou propustnost tím, že zřizujete další jednotky žádostí. 

    Složitost dotazu ovlivňuje počet spotřebovaných jednotek požadavků pro určitou operaci. Počet predikátů, povaha predikátů, počet UDF a velikost zdrojové sady dat ovlivňují náklady na operace dotazů.

    Pro měření režie jakékoli operace (vytvoření, aktualizace nebo odstranění) Zkontrolujte záhlaví [x-MS-Request-poplatek](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (nebo ekvivalentní vlastnost RequestCharge v [ResourceResponse \<T> ](/java/api/com.microsoft.azure.documentdb.resourceresponse) nebo [FeedResponse \<T> ](/java/api/com.microsoft.azure.documentdb.feedresponse) pro měření počtu jednotek žádostí, které tyto operace spotřebují.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Synchronizace sady Java SDK v2 (Maven com. Microsoft. Azure:: Azure-DocumentDB)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Poplatek za požadavek vrácený v této hlavičce je zlomek zřízené propustnosti. Pokud jste například zřídili 2000 RU/s, a pokud předchozí dotaz vrátí 1000 1 KB-Documents, náklady na operaci jsou 1000. V takovém případě se server během jedné sekundy připadá pouze na dva takové požadavky, než frekvence omezí následné požadavky. Další informace najdete v tématu [jednotky žádostí](request-units.md) a [Kalkulačka jednotek požadavků](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Omezení rychlosti zpracování/počet požadavků je moc velký.**

    Když se klient pokusí překročit rezervovanou propustnost účtu, neexistují žádné snížení výkonu na serveru a žádné využití kapacity propustnosti mimo rezervovanou úroveň. Server v tuto chvíli ukončí požadavek pomocí RequestRateTooLarge (kód stavu HTTP 429) a vrátí hlavičku [x-MS-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) , která indikuje, jak dlouho (v milisekundách) musí uživatel počkat, než se znovu pokusí o požadavek.
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    Sady SDK mají implicitně zachytit tuto odpověď, a to v souladu s hlavičkou, která je určená serverem, a zkuste žádost zopakovat. Pokud k účtu nepoužíváte souběžně více klientů, další pokus bude úspěšný.

    Pokud máte více než jednoho klienta, který se konzistentně pracuje konzistentně nad sazbou požadavku, nemusí být výchozí počet opakování nastavený na hodnotu 9 interně klientem. v takovém případě klient vyvolá [DocumentClientException](/java/api/com.microsoft.azure.documentdb.documentclientexception) se stavovým kódem 429 do aplikace. Výchozí počet opakování se dá změnit pomocí [setRetryOptions](/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) v instanci [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) . Ve výchozím nastavení se DocumentClientException se stavovým kódem 429 vrátí po kumulativní čekací době 30 sekund, pokud požadavek nadále funguje nad sazbou požadavku. K tomu dojde i v případě, že aktuální počet opakování je menší než maximální počet opakování, výchozí hodnota je 9 nebo uživatelem definovaná hodnota.

    I když automatizované chování při opakování pomáhá zlepšit odolnost a použitelnost většiny aplikací, může při měření latence docházet k lichá při provádění srovnávacích testů, zejména při měření latence.  Latence zjištěná klientem bude špička, pokud experiment narazí na omezení serveru a způsobí, že se klientská sada SDK tiše znovu pokusí. Aby se zabránilo špičkám latence během experimentů s výkonem, změřte poplatky vracené jednotlivými operacemi a zajistěte, aby požadavky byly v provozu pod rezervovanými sazbami požadavků. Další informace najdete v tématu [jednotky žádostí](request-units.md).
3. **Návrh pro menší dokumenty pro vyšší propustnost**

    Poplatek za požadavek (náklady na zpracování požadavku) dané operace se přímo koreluje s velikostí dokumentu. Operace s velkým objemem dokumentů se při používání malých dokumentů dotýkají více než operací.

## <a name="next-steps"></a>Další kroky
Další informace o návrhu aplikace pro škálování a vysoký výkon najdete v tématu [dělení a škálování v Azure Cosmos DB](partitioning-overview.md).