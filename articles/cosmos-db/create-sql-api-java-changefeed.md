---
title: Kurz – ukázka aplikace asynchronní ho asynchronního java api api s kanálem změn
description: Tento kurz vás provede jednoduchou aplikací java rozhraní SQL API, která vkládá dokumenty do kontejneru Azure Cosmos DB při zachování materializovaného zobrazení kontejneru pomocí kanálu změn.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586698"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Kurz – ukázka aplikace asynchronní ho asynchronního java api api s kanálem změn

Tento výukový průvodce vás provede jednoduchou aplikací java sql api, která vkládá dokumenty do kontejneru Azure Cosmos DB při zachování materializovaného zobrazení kontejneru pomocí kanálu změn.

## <a name="prerequisites"></a>Požadavky

* Osobní počítač

* Identifikátor URI a klíč pro váš účet Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Pozadí

Kanál změn Azure Cosmos DB poskytuje rozhraní řízené událostmi, které aktivuje akce v reakci na vložení dokumentu. To má mnoho využití. Například v aplikacích, které jsou čtení a zápis těžký, hlavní použití kanálu změn je vytvořit v reálném čase **materializované zobrazení** kontejneru, jak je ingestování dokumentů. Kontejner materializovaného zobrazení bude obsahovat stejná data, ale rozdělena na oddíly pro efektivní čtení, takže aplikace čtení i zápis efektivní.

O práci na správě událostí kanálu změn se do značné míry postará knihovna kanálu změn integrovaná do sady SDK. Tato knihovna je dostatečně výkonný distribuovat události kanálu změn mezi více pracovníků, pokud je to žádoucí. Jediné, co musíte udělat, je poskytnout knihovně kanálu změn zpětné volání.

Tento jednoduchý příklad znázorní knihovnu zpracovatele kanálu změn s jedním pracovníkem, který vytváří a maže dokumenty z materializovaného zobrazení.

## <a name="setup"></a>Nastavení

Pokud jste tak ještě neučinili, naklonovat příklad aplikace repo:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> Máte možnost pracovat přes tento rychlý start s Java SDK 4.0 nebo Java SDK 3.7.0. **Pokud byste chtěli použít Java SDK 3.7.0, ```git checkout SDK3.7.0```v typu terminálu **. V opačném případě ```master``` zůstaňte na větvi, která je výchozí java sdchartou 4.0.

Otevřete terminál v adresáři úložiště. Vytvoření aplikace spuštěním

```bash
mvn clean package
```

## <a name="walkthrough"></a>Názorný postup

1. Jako první kontrolu byste měli mít účet Azure Cosmos DB. Otevřete **portál Azure Ve** svém prohlížeči, přejděte na svůj účet Azure Cosmos DB a v levém podokně přejděte na **Průzkumníka dat**.

    ![Účet Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Spusťte aplikaci v terminálu pomocí následujícího příkazu:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Stiskněte klávesu ENTER, když vidíte

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    pak se vraťte do Průzkumníka dat portálu Azure Portal ve vašem prohlížeči. Zobrazí databáze **GroceryStoreDatabase** byla přidána se třemi prázdnými kontejnery: 

    * **InventoryContainer** - skladový záznam pro náš příklad obchodu ```id``` s potravinami, rozdělený na položku, která je UUID.
    * **InventoryContainer-pktype** - Materializované zobrazení záznamu zásob, optimalizované pro dotazy na položku```type```
    * **InventoryContainer-leases** – kontejner zapůjčení je vždy potřeba pro kanál změn; pronajímá sledovat průběh aplikace při čtení kanálu změn.


    ![Prázdné nádoby](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. V terminálu byste nyní měli vidět výzvu

    ```bash
    Press enter to start creating the materialized view...
    ```

    Stiskněte klávesu ENTER. Nyní se spustí následující blok kódu a inicializuje procesor kanálu změn v jiném vlákně: 


    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
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

    ```"SampleHost_1"```je název pracovníka procesoru kanálu změn. ```changeFeedProcessorInstance.start()```je to, co skutečně spustí change feed procesor.

    Vraťte se do Průzkumníka dat portálu Azure Portal ve svém prohlížeči. V kontejneru **InventoryContainer-leases** klikněte na **položky** a zomenšete jeho obsah. Uvidíte, že změnit feed procesor naplnil kontejner zapůjčení, tj. ```SampleHost_1``` **InventoryContainer**

    ![Zapůjčení](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Stiskněte tlačítko enter znovu v terminálu. Tím se spustí 10 dokumentů, které mají být vloženy do **InventoryContainer**. Každé vložení dokumentu se zobrazí v kanálu změn jako JSON; následující kód zpětného volání zpracovává tyto události zrcadlením dokumentů JSON do materializovaného zobrazení:

    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
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

1. Nechte kód spustit 5-10sec. Pak se vraťte do Průzkumníka dat portálu Azure Portal a přejděte na **InventoryContainer > položky**. Měli byste vidět, že položky jsou vkládány do kontejneru zásob; poznamenejte```id```si klíč oddílu ( ).

    ![Nádoba na krmivo](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Teď v Průzkumníku dat přejděte na **InventoryContainer-pktype > položky**. Toto je materializované zobrazení - položky v tomto kontejneru zrcadlí **InventoryContainer,** protože byly vloženy programově pomocí kanálu změn. Poznamenejte```type```si klíč oddílu ( ). Takže toto zhmotněné zobrazení je ```type```optimalizovánpro filtrování dotazů přes , což ```id```by bylo neefektivní na **InventoryContainer,** protože je rozdělenna na .

    ![Materializované zobrazení](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Odstraníme dokument z **InventoryContainer** a **InventoryContainer-pktype** pomocí jediného ```upsertItem()``` volání. Nejdřív se podívejte na Průzkumníka dat portálu Azure Portal. Odstraníme dokument, pro ```/type == "plums"```který ; je obklopen červeně pod

    ![Materializované zobrazení](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Dalším klepnutím na klávesu Enter zavoláte funkci ```deleteDocument()``` v ukázkovém kódu. Tato funkce, zobrazená níže, upserts novou ```/ttl == 5```verzi dokumentu s , který nastaví dokument Time-To-Live (TTL) na 5sec. 
    
    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
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

    Kanál změn ```feedPollDelay``` je nastaven na 100 ms; proto Change Feed reaguje na tuto aktualizaci ```updateInventoryTypeMaterializedView()``` téměř okamžitě a volání jsou uvedena výše. Toto poslední volání funkce bude upsert nový dokument s TTL 5sec do **InventoryContainer-pktype**.

    Efekt je, že po asi 5 sekund, dokument vyprší a budou odstraněny z obou kontejnerů.

    Tento postup je nezbytný, protože kanál změn vydává pouze události při vkládání nebo aktualizaci položky, nikoli při odstraňování položek.

1. Dalším stisknutím klávesy zadejte program a vyčistěte jeho zdroje.
