---
title: Vytvoření komplexního Azure Cosmos DB ukázkové aplikace sady Java SDK v4 pomocí kanálu změn
description: Tato příručka vás provede jednoduchou aplikací Java SQL API, která vloží dokumenty do kontejneru Azure Cosmos DB a přitom zachová materializované zobrazení kontejneru pomocí změny kanálu.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 765fd3afc7fe688d3e6b0e3394e7dc8c39af69b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096848"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Postup vytvoření aplikace Java, která používá Azure Cosmos DB SQL API a změna procesoru kanálu
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tato příručka vás provede jednoduchou aplikací v jazyce Java, která používá rozhraní Azure Cosmos DB SQL API k vkládání dokumentů do kontejneru Azure Cosmos DB a při zachování materializované zobrazení kontejneru pomocí Change feed a Change feed Processor. Aplikace Java komunikuje s rozhraním Azure Cosmos DB SQL API pomocí Azure Cosmos DB Java SDK v4.

> [!IMPORTANT]  
> Tento kurz je určen pouze pro Azure Cosmos DB Java SDK v4. Další informace najdete v Azure Cosmos DB zpráva k [vydání verze](sql-api-sdk-java-v4.md)Java SDK v4, [úložiště pro Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB [tipy pro výkon](performance-tips-java-sdk-v4-sql.md)Java SDK v4 a Azure Cosmos DB [Průvodce řešením potíží](troubleshoot-java-sdk-v4-sql.md) se sadou Java SDK v4. Pokud aktuálně používáte starší verzi než v4, přečtěte si článek průvodce [migrací do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , který vám pomůže s upgradem na V4.
>

## <a name="prerequisites"></a>Předpoklady

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

Otevřete terminál v adresáři úložiště. Sestavte aplikaci spuštěním

```bash
mvn clean package
```

## <a name="walkthrough"></a>Názorný postup

1. Při první kontrole byste měli mít účet Azure Cosmos DB. Otevřete **Azure Portal** v prohlížeči, přejděte na účet Azure Cosmos DB a v levém podokně přejděte na **Průzkumník dat**.

   :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_empty.JPG" alt-text="Účet Azure Cosmos DB":::

1. Spusťte aplikaci v terminálu pomocí následujícího příkazu:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Po zobrazení zobrazit stiskněte klávesu ENTER

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    pak se vraťte do Průzkumník dat Azure Portal v prohlížeči. Uvidíte, že se do databáze **GroceryStoreDatabase** přidalo tři prázdné kontejnery: 

    * **InventoryContainer** – záznam inventáře pro náš příklad PRODEJNOSTI, rozdělený na položku, ```id``` která je identifikátorem UUID.
    * **InventoryContainer-pktype** – materializované zobrazení záznamu inventáře optimalizovaného pro dotazy nad položkou ```type```
    * **InventoryContainer – zapůjčení** – kontejner zapůjčení je vždycky potřebný pro kanál změn; zapůjčení sleduje pokrok aplikace při čtení kanálu změn.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG" alt-text="Prázdné kontejnery":::

1. V terminálu by se teď měla zobrazit výzva.

    ```bash
    Press enter to start creating the materialized view...
    ```

    Stiskněte klávesu ENTER. Nyní následující blok kódu provede a inicializuje procesor změn kanálu v jiném vlákně: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=InitializeCFP)]

    ```"SampleHost_1"``` je název pracovníka procesoru změny kanálu. ```changeFeedProcessorInstance.start()``` je to, co se ve skutečnosti spustí procesor Change feed.

    Vraťte se do Průzkumník dat Azure Portal v prohlížeči. V kontejneru **InventoryContainer-Leases** klikněte na **položky** , abyste viděli jeho obsah. Uvidíte, že v procesoru pro změnu kanálu se naplní kontejner zapůjčení, tj. procesor přiřadil ```SampleHost_1``` pracovnímu procesu zapůjčení na některých oddílech **InventoryContainer**.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_leases.JPG" alt-text="Zapůjčení":::

1. V terminálu znovu stiskněte klávesu ENTER. Tím se spustí 10 dokumentů, které se budou vkládat do **InventoryContainer**. Každé vložení dokumentu se zobrazí v kanálu změn jako JSON; Následující kód zpětného volání zpracovává tyto události zrcadlením dokumentů JSON do materializované zobrazení:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=CFPCallback)]

1. Povolí spuštění kódu 5 – 10sec. Pak se vraťte do Azure Portal Průzkumník dat a přejděte na **položky InventoryContainer >**. Měli byste vidět, že se položky vkládají do kontejneru inventáře; Poznamenejte si klíč oddílu ( ```id``` ).

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_items.JPG" alt-text="Kontejner informačního kanálu":::

1. Nyní v Průzkumník dat přejděte na **položku > položky InventoryContainer-pktype**. Toto je materializované zobrazení – položky v tomto kontejneru zrcadlení **InventoryContainer** , protože byly vloženy programově pomocí změny kanálu. Poznamenejte si klíč oddílu ( ```type``` ). Toto materializované zobrazení je optimalizováno pro filtrování dotazů ```type``` , které by bylo pro **InventoryContainer** neefektivní, protože je rozdělené na oddíly ```id``` .

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG" alt-text="Snímek obrazovky ukazuje stránku Průzkumník dat pro účet Azure Cosmos D B s vybranými položkami.":::

1. K odstranění dokumentu z obou **InventoryContainer** a **InventoryContainer-pktype** se používá pouze jedno ```upsertItem()``` volání. Nejprve se podíváme na Azure Portal Průzkumník dat. Odstraníme dokument, pro který ```/type == "plums"``` se encircled červeně.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG" alt-text="Snímek obrazovky ukazuje stránku Průzkumník dat pro účet Azure Cosmos D B s vybranou konkrétní položkou D.":::

    Stiskněte znovu Enter pro volání funkce ```deleteDocument()``` v ukázkovém kódu. Tato funkce, která je zobrazena níže, upsertuje novou verzi dokumentu s hodnotou ```/ttl == 5``` , která nastaví hodnotu TTL (Time to Live) do 5sec. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=DeleteWithTTL)]

    Kanál změn ```feedPollDelay``` je nastavený na 100 ms. proto kanál změn reaguje na tuto aktualizaci skoro okamžitě a na ```updateInventoryTypeMaterializedView()``` výše uvedené volání. Toto poslední volání funkce bude Upsert nový dokument s hodnotou TTL 5sec do **InventoryContainer-pktype**.

    To platí, že po asi 5 sekundách vyprší platnost dokumentu a odstraní se z obou kontejnerů.

    Tento postup je nezbytný, protože Změna kanálu vydává události jenom při vkládání nebo aktualizaci položek, nikoli při odstraňování položek.

1. Stisknutím klávesy ENTER ukončíte program a vyčistíte jeho prostředky.
