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
ms.openlocfilehash: 489f241453436190213b99cb4e7be0688a8b6237
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387094"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Rychlý Start: Vytvoření aplikace Java pro správu Azure Cosmos DB dat rozhraní SQL API


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu vytvoříte a spravujete Azure Cosmos DB účet rozhraní SQL API z Azure Portal a pomocí aplikace Java naklonované z GitHubu. Nejdřív vytvoříte Azure Cosmos DB účet rozhraní SQL API pomocí Azure Portal a pak vytvoříte aplikaci Java pomocí SQL Java SDK a potom do svého účtu Cosmos DB přidáte prostředky pomocí aplikace Java. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) můžete použít také s identifikátorem URI `https://localhost:8081` a klíčovým `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Najeďte proměnnou prostředí `JAVA_HOME` na složku, ve které je nainstalovaná JDK.
- [Binární archiv Maven](https://maven.apache.org/download.cgi) V Ubuntu spusťte `apt-get install maven` a nainstalujte Maven.
- [Git](https://www.git-scm.com/downloads) V Ubuntu spusťte `sudo apt-get install git` pro instalaci Gitu.

## <a name="introductory-notes"></a>Úvodní poznámky

*Struktura Cosmos DB účtu* Bez ohledu na rozhraní API nebo programovací jazyk, Cosmos DB *účet* obsahuje nula nebo více *databází*, *databáze* (DB) obsahuje nula nebo více *kontejnerů*a *kontejner* obsahuje nula nebo více položek, jak je znázorněno na následujícím obrázku:

![Entity účtu Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

Další informace o databázích, kontejnerech a položkách si můžete přečíst [tady.](databases-containers-items.md) Na úrovni kontejneru je definováno několik důležitých vlastností, mezi kterými je *zřízena propustnost* a *klíč oddílu*. 

Zřízená propustnost se měří v jednotkách žádosti (*ru*), které mají peněžní cenu, a jsou zásadním rozhodujícím faktorem za provozní náklady na účet. Zřízenou propustnost lze vybrat v členitosti podle kontejneru nebo členitosti na databázi, ale obvykle je upřednostňována specifikace propustnosti na úrovni kontejneru. Další informace o zřizování propustnosti si můžete přečíst [tady.](set-throughput.md)

Když jsou položky vloženy do kontejneru Cosmos DB, databáze se poroste vodorovně přidáním dalšího úložiště a výpočetních prostředků pro zpracování požadavků. Kapacita úložiště a výpočetní kapacity se přidávají do diskrétních jednotek označovaných jako *oddíly*a Vy musíte zvolit jedno pole v dokumentu, které bude mít klíč oddílu, který mapuje jednotlivé dokumenty na oddíl. Způsob správy oddílů je, že každý oddíl má přiřazený zhruba stejný řez mimo rozsah hodnot klíče oddílu; Proto doporučujeme zvolit klíč oddílu, který je relativně náhodný nebo rovnoměrně distribuovaný. V opačném případě některé oddíly uvidí podstatně více požadavků (*aktivní oddíl*), zatímco ostatní oddíly jsou v podstatě méně požadavků (*studený oddíl*) a že se to vyhne. [Tady](partitioning-overview.md)se můžete dozvědět víc o vytváření oddílů.

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

Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Spuštění aplikace](#run-the-app). 

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Správa databázových prostředků pomocí synchronního (synchronizačního) rozhraní API

* Inicializace klienta `CosmosClient`. `CosmosClient` poskytuje logickou reprezentaci na straně klienta pro službu Azure Cosmos Database. Tento klient slouží ke konfiguraci a provádění požadavků na službu.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* vytváření `CosmosDatabase`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* vytváření `CosmosContainer`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Vytvoření položky pomocí metody `createItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Čtení bodů se provádí pomocí metody `readItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* Dotazy SQL přes JSON se provádějí pomocí metody `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Správa databázových prostředků pomocí asynchronního (asynchronního) rozhraní API

* Asynchronní volání rozhraní API vrátí okamžitě bez čekání na odpověď serveru. Na základě tohoto příkladu následující fragmenty kódu ukazují správné vzory návrhu pro splnění všech předchozích úloh správy pomocí asynchronního rozhraní API.

* Inicializace klienta `CosmosAsyncClient`. `CosmosAsyncClient` poskytuje logickou reprezentaci na straně klienta pro službu Azure Cosmos Database. Tento klient se používá ke konfiguraci a spouštění asynchronních požadavků na službu.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* vytváření `CosmosAsyncDatabase`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* vytváření `CosmosAsyncContainer`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Stejně jako u rozhraní API pro synchronizaci je vytvoření položky provedeno pomocí metody `createItem`. Tento příklad ukazuje, jak efektivně vydávat množství asynchronních `createItem` požadavků pomocí přihlášení k odběru reaktivního proudu, který vydává žádosti a tiskne oznámení. Vzhledem k tomu, že tento jednoduchý příklad běží na dokončování a končí, `CountDownLatch` instance slouží k zajištění, že program během vytváření položky nekončí. **Správný postup asynchronního programování není blokován při asynchronním volání – v reálných žádostech o použití jsou generovány z hlavní () smyčky, která se spouští po neomezenou dobu, což eliminuje nutnost západky na asynchronní volání.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Stejně jako u rozhraní API pro synchronizaci je čtení bodů prováděno pomocí metody `readItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Stejně jako u rozhraní API pro synchronizaci jsou dotazy SQL přes JSON prováděny pomocí metody `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>Spuštění aplikace

Teď se vraťte zpět na web Azure Portal, kde najdete informace o připojovacím řetězci, a spusťte aplikaci s použitím informací o vašem koncovém bodu. Tím aplikaci umožníte komunikovat s hostovanou databází.

1. V okně terminálu Git přejděte pomocí příkazu `cd` do složky se vzorovým kódem.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. V okně terminálu Git pomocí následujícího příkazu nainstalujte požadované balíčky Java.

    ```bash
    mvn package
    ```

3. V okně terminálu Git pomocí následujícího příkazu spusťte aplikaci Java (nahraďte SYNCASYNCMODE hodnotou `sync` nebo `async` v závislosti na tom, který vzorový kód chcete spustit, nahraďte YOUR_COSMOS_DB_HOSTNAME hodnotou identifikátoru URI v uvozovkách z portálu a nahraďte YOUR_COSMOS_DB_MASTER_KEY primárním klíčem z portálu.)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

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

V tomto rychlém startu jste se naučili, jak vytvořit Azure Cosmos DB účet rozhraní SQL API, vytvořit databázi dokumentů a kontejner pomocí Průzkumník dat a spustit aplikaci Java, která provede stejné věci programově. Teď můžete do svého účtu Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
