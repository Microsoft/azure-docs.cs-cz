---
title: Kurz – ukázka komplexní asynchronní aplikace Java SQL API pomocí kanálu změn
description: Tento kurz vás provede jednoduchou aplikací Java SQL API, která vloží dokumenty do kontejneru Azure Cosmos DB a současně zachovává materializované zobrazení kontejneru pomocí změny kanálu.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: c74ec73eb06c43110747d87e6fecd12183527759
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872542"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Kurz – ukázka komplexní asynchronní aplikace Java SQL API pomocí kanálu změn

Tento kurz vás provede jednoduchou aplikací Java SQL API, která vloží dokumenty do kontejneru Azure Cosmos DB a současně zachovává materializované zobrazení kontejneru pomocí změny kanálu.

## <a name="prerequisites"></a>Požadavky

* Osobní počítač

* Identifikátor URI a klíč pro váš Azure Cosmos DB účet

* Maven

* Java 8

## <a name="background"></a>Pozadí

Kanál změny Azure Cosmos DB poskytuje rozhraní řízené událostmi, které aktivuje akce v reakci na vložení dokumentu. Má spoustu použití. Například v aplikacích, které jsou pro čtení i zápis těžké, je hlavní použití kanálu změn v reálném čase vytvořit **materializované zobrazení** kontejneru v reálném čase, protože slouží k ingestování dokumentů. Kontejner materializované zobrazení bude obsahovat stejná data, ale rozdělená na oddíly pro efektivní čtení, takže aplikace bude efektivně číst i zapisovat.

Práce se správou událostí kanálu změn je převážně postará o knihovnu, která je integrovaná do sady SDK nástroje Change feed Processor. Tato knihovna je dostatečně výkonná k distribuci událostí kanálu změn mezi několik pracovních procesů, pokud je to žádoucí. Vše, co musíte udělat, je poskytnout zpětné volání knihovny změn kanálu.

Tento jednoduchý příklad znázorňuje knihovnu Change feed Processor s jedním pracovním procesem vytváření a odstraňování dokumentů ze materializované zobrazení.

## <a name="setup"></a>Nastavení

Pokud jste to ještě neudělali, naklonujte úložiště ukázkové aplikace:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> V tomto rychlém startu jste si zvolili, abyste mohli pracovat s Java SDK 4,0 nebo Java SDK 3.7.0. Chcete **-li v typu ```git checkout SDK3.7.0```terminálu použít sadu Java SDK 3.7.0, ** V opačném případě zůstaňte ve ```master``` větvi, kde se použije sada Java SDK 4,0.

Otevřete terminál v adresáři úložiště. Sestavte aplikaci spuštěním

```bash
mvn clean package
```

## <a name="walkthrough"></a>Návod

1. Při první kontrole byste měli mít účet Azure Cosmos DB. V prohlížeči otevřete **portál Azure** , přejděte na účet Azure Cosmos DB a v levém podokně přejděte na **Průzkumník dat**.

    ![Účet Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Spusťte aplikaci v terminálu pomocí následujícího příkazu:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Po zobrazení zobrazit stiskněte klávesu ENTER

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    pak se vraťte na portál Azure Portal Průzkumník dat v prohlížeči. Uvidíte, že se do databáze **GroceryStoreDatabase** přidalo tři prázdné kontejnery: 

    * **InventoryContainer** – záznam inventáře pro náš příklad PRODEJNOSTI, rozdělený na položku ```id``` , která je identifikátorem UUID.
    * **InventoryContainer-pktype** – materializované zobrazení záznamu inventáře optimalizovaného pro dotazy nad položkou```type```
    * **InventoryContainer – zapůjčení** – kontejner zapůjčení je vždycky potřebný pro kanál změn; zapůjčení sleduje pokrok aplikace při čtení kanálu změn.


    ![Prázdné kontejnery](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. V terminálu by se teď měla zobrazit výzva.

    ```bash
    Press enter to start creating the materialized view...
    ```

    Stiskněte klávesu ENTER. Nyní následující blok kódu provede a inicializuje procesor změn kanálu v jiném vlákně: 

   # <a name="java-sdk-40"></a>[Java SDK 4,0](#tab/v4sdk)

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

   # <a name="java-sdk-370"></a>[Java SDK 3.7.0](#tab/v3sdk)

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```
   ---

    ```"SampleHost_1"```je název pracovníka procesoru změny kanálu. ```changeFeedProcessorInstance.start()```je to, co se ve skutečnosti spustí procesor Change feed.

    V prohlížeči se vraťte na Průzkumník dat webu Azure Portal. V kontejneru **InventoryContainer-Leases** klikněte na **položky** , abyste viděli jeho obsah. Uvidíte, že v procesoru pro změnu kanálu se naplní kontejner zapůjčení, tj. procesor přiřadil ```SampleHost_1``` pracovnímu procesu zapůjčení na některých oddílech **InventoryContainer**.

    ![Zapůjčení](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. V terminálu znovu stiskněte klávesu ENTER. Tím se spustí 10 dokumentů, které se budou vkládat do **InventoryContainer**. Každé vložení dokumentu se zobrazí v kanálu změn jako JSON; Následující kód zpětného volání zpracovává tyto události zrcadlením dokumentů JSON do materializované zobrazení:

   # <a name="java-sdk-40"></a>[Java SDK 4,0](#tab/v4sdk)

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

   # <a name="java-sdk-370"></a>[Java SDK 3.7.0](#tab/v3sdk)

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```
   ---

1. Povolí spuštění kódu 5 – 10sec. Pak se vraťte na portál Azure Portal Průzkumník dat a přejděte k **položkám > InventoryContainer**. Měli byste vidět, že se položky vkládají do kontejneru inventáře; Poznamenejte si klíč oddílu```id```().

    ![Kontejner informačního kanálu](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Nyní v Průzkumník dat přejděte na **položku > položky InventoryContainer-pktype**. Toto je materializované zobrazení – položky v tomto kontejneru zrcadlení **InventoryContainer** , protože byly vloženy programově pomocí změny kanálu. Poznamenejte si klíč oddílu```type```(). Toto materializované zobrazení je optimalizováno pro filtrování dotazů ```type```, které by bylo pro **InventoryContainer** neefektivní, protože je rozdělené na ```id```oddíly.

    ![Materializované zobrazení](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. K odstranění dokumentu z obou **InventoryContainer** a **InventoryContainer-pktype** se používá pouze jedno ```upsertItem()``` volání. Nejdřív se podíváme na portál Azure Portal Průzkumník dat. Odstraníme dokument, pro který ```/type == "plums"```; je encircled červeně pod

    ![Materializované zobrazení](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Stiskněte znovu Enter pro volání funkce ```deleteDocument()``` v ukázkovém kódu. Tato funkce, která je zobrazena níže, upsertuje novou verzi dokumentu s ```/ttl == 5```hodnotou, která nastaví hodnotu TTL (Time to Live) do 5sec. 
    
   # <a name="java-sdk-40"></a>[Java SDK 4,0](#tab/v4sdk)

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```
   # <a name="java-sdk-370"></a>[Java SDK 3.7.0](#tab/v3sdk)

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```
   ---

    Kanál ```feedPollDelay``` změn je nastaven na 100 MS; Proto kanál změn reaguje na tuto aktualizaci skoro okamžitě a volání ```updateInventoryTypeMaterializedView()``` uvedená výše. Toto poslední volání funkce bude Upsert nový dokument s hodnotou TTL 5sec do **InventoryContainer-pktype**.

    To platí, že po asi 5 sekundách vyprší platnost dokumentu a odstraní se z obou kontejnerů.

    Tento postup je nezbytný, protože Změna kanálu vydává události jenom při vkládání nebo aktualizaci položek, nikoli při odstraňování položek.

1. Stisknutím klávesy ENTER ukončíte program a vyčistíte jeho prostředky.
