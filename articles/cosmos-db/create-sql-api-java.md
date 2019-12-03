---
title: Rychlý Start – použití jazyka Java k vytvoření databáze dokumentů pomocí Azure Cosmos DB
description: V tomto rychlém startu se zobrazí ukázka kódu Java, kterou můžete použít k připojení a dotazování Azure Cosmos DB rozhraní SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: a4a8990b3da534acb39ff87c9f7665fb3b08ef06
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708166"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Rychlý Start: Vytvoření aplikace Java pro správu Azure Cosmos DB dat rozhraní SQL API


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu se dozvíte, jak pomocí aplikace v jazyce Java vytvořit a spravovat databázi dokumentů z Azure Cosmos DB účtu rozhraní SQL API. Nejdřív vytvoříte Azure Cosmos DB účet rozhraní SQL API pomocí Azure Portal, vytvoříte aplikaci Java pomocí SQL Java SDK a potom do svého účtu Cosmos DB přidáte prostředky pomocí aplikace Java. Pokyny v tomto rychlém startu platí pro všechny operační systémy, které podporují Javu. Po dokončení tohoto rychlého startu budete obeznámeni s vytvářením a úpravou databází Cosmos DB, kontejnerů buď v uživatelském rozhraní, nebo programově, podle toho, co je vaše preference.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Dále musí být splněny všechny tyto podmínky: 

* [Java Development Kit (JDK) verze 8](https://aka.ms/azure-jdks)
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv [Maven](https://maven.apache.org/).
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moci vytvořit databázi dokumentů, je potřeba pomocí služby Azure Cosmos DB vytvořit účet rozhraní SQL API.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Přidat kontejner

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Naklonujeme aplikaci SQL API z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Spuštění aplikace](#run-the-app). 

* Inicializace klienta `CosmosClient`. `CosmosClient` poskytuje logickou reprezentaci na straně klienta pro službu Azure Cosmos Database. Tento klient slouží ke konfiguraci a provádění požadavků na službu.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* Vytváření CosmosDatabase

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Vytváření CosmosContainer

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Vytvoření položky pomocí metody `createItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Čtení bodů se provádí pomocí metody `getItem` a `read`

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* Dotazy SQL přes JSON se provádějí pomocí metody `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>Spusťte aplikaci

Teď se vraťte zpět na web Azure Portal, kde najdete informace o připojovacím řetězci, a spusťte aplikaci s použitím informací o vašem koncovém bodu. Tím aplikaci umožníte komunikovat s hostovanou databází.


1. V okně terminálu Git přejděte pomocí příkazu `cd` do složky se vzorovým kódem.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. V okně terminálu Git pomocí následujícího příkazu nainstalujte požadované balíčky Java.

    ```bash
    mvn package
    ```

3. V okně terminálu Git pomocí následujícího příkazu spusťte aplikaci v Javě (YOUR_COSMOS_DB_HOSTNAME nahraďte hodnotou identifikátoru URI z portálu v uvozovkách a YOUR_COSMOS_DB_MASTER_KEY nahraďte primárním klíčem z portálu v uvozovkách).

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    V okně terminálu se zobrazí oznámení o vytvoření databáze FamilyDB. 
    
4. Aplikace vytvoří databázi s názvem `AzureSampleFamilyDB`
5. Aplikace vytvoří kontejner s názvem `FamilyContainer`
6. Aplikace provede čtení bodů pomocí ID objektů a hodnoty klíče oddílu (což je lastName v naší ukázce). 
7. Aplikace se bude dotazovat na položky a načíst všechny rodiny s posledním názvem v (' Andersen ', ' Wakefieldů ', ' Johnsonem ').

7. Aplikace neodstraňuje vytvořené prostředky. Přepněte zpět na portál a [vyčistěte prostředky](#clean-up-resources)  ze svého účtu, aby se vám za ně neúčtovaly poplatky.

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos, databáze dokumentů a kontejneru pomocí Průzkumník dat a spuštění aplikace ke stejnému účelu prostřednictvím kódu programu. Teď můžete do svého kontejneru Azure Cosmos importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
