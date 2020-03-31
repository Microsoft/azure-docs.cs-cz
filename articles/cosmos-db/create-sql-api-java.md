---
title: Úvodní příručka – vytvoření databáze dokumentů pomocí Azure Cosmos DB pomocí Javy
description: Tento rychlý start představuje ukázku kódu Jazyka Java, kterou můžete použít k připojení a dotazování na rozhraní SQL API Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 489f241453436190213b99cb4e7be0688a8b6237
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240174"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Úvodní příručka: Vytvoření aplikace Java pro správu dat sql api Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu vytvoříte a spravujete účet Azure Cosmos DB SQL API z webu Azure Portal a pomocí aplikace Java naklonované z GitHubu. Nejprve vytvoříte účet Azure Cosmos DB SQL API pomocí portálu Azure, pak vytvoříte aplikaci Java pomocí sql java sdk a pak přidáte prostředky do účtu Cosmos DB pomocí aplikace Java. Azure Cosmos DB je vícemodelová databázová služba, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, hodnot klíče a grafů s možností globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. Můžete také použít [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) `https://localhost:8081` s identifikátorem URI a klíč `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Java Vývojová sada (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Najeďte `JAVA_HOME` proměnnou prostředí do složky, do které je nainstalována sada JDK.
- [Maven binární archiv](https://maven.apache.org/download.cgi). Na Ubuntu, `apt-get install maven` spusťte instalaci Maven.
- [Git](https://www.git-scm.com/downloads). Na Ubuntu, `sudo apt-get install git` spusťte instalaci Git.

## <a name="introductory-notes"></a>Úvodní poznámky

*Struktura účtu Cosmos DB.* Bez ohledu na rozhraní API nebo programovací jazyk obsahuje *účet* Cosmos DB nulovou nebo více *databází*, *databáze* (DB) obsahuje nulu nebo více *kontejnerů*a *kontejner* obsahuje nulu nebo více položek, jak je znázorněno na obrázku níže:

![Entity účtu Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

Můžete si přečíst více o databázích, kontejnerech a položkách [zde.](databases-containers-items.md) Několik důležitých vlastností jsou definovány na úrovni kontejneru, mezi nimi *zřízená propustnost* a *klíč oddílu*. 

Zřízená propustnost se měří v jednotkách požadavků (*RU*), které mají peněžní cenu a jsou podstatným určujícím faktorem provozních nákladů účtu. Zřízená propustnost může být vybrána na rozlišovací schopnost na kontejner nebo rozlišovací schopnost podle databáze, ale specifikace propustnosti na úrovni kontejneru je obvykle upřednostňována. Můžete si přečíst další informace o zřizování propustností [zde.](set-throughput.md)

Jako položky jsou vloženy do kontejneru Cosmos DB, databáze zvětšuje vodorovně přidáním další úložiště a výpočetní pro zpracování požadavků. Úložiště a výpočetní kapacita jsou přidány v diskrétní jednotky známé jako *oddíly*a musíte zvolit jedno pole v dokumentech, které mají být klíč oddílu, který mapuje každý dokument do oddílu. Způsob, jakým jsou oddíly spravovány, je, že každému oddílu je přiřazen a zhruba stejný řez mimo rozsah hodnot klíče oddílu; Proto se doporučuje zvolit klíč oddílu, který je relativně náhodný nebo rovnoměrně rozložený. V opačném případě budou některé oddíly vidět podstatně více požadavků *(hot partition),* zatímco jiné oddíly vidět podstatně méně požadavků *(studený oddíl*), a to je třeba se vyhnout. Můžete se dozvědět více o dělení [zde](partitioning-overview.md).

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moci vytvořit databázi dokumentů, je potřeba pomocí služby Azure Cosmos DB vytvořit účet rozhraní SQL API.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Přidání kontejneru

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

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Správa databázových prostředků pomocí synchronního rozhraní API (synchronizace)

* Inicializace klienta `CosmosClient`. Poskytuje `CosmosClient` logické znázornění na straně klienta pro databázovou službu Azure Cosmos. Tento klient slouží ke konfiguraci a provádění požadavků na službu.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase`Vytvoření.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer`Vytvoření.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Vytvoření položky `createItem` pomocí metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Čtení bodů se `readItem` provádí metodou.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* Dotazy SQL přes JSON jsou `queryItems` prováděny pomocí metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Správa databázových prostředků pomocí asynchronního (asynchronního) rozhraní API

* Asynchronní volání rozhraní API se okamžitě vrátí bez čekání na odpověď ze serveru. S ohledem na to následující fragmenty kódu zobrazit správné návrhové vzory pro provádění všech předchozích úloh správy pomocí asynchronní rozhraní API.

* Inicializace klienta `CosmosAsyncClient`. Poskytuje `CosmosAsyncClient` logické znázornění na straně klienta pro databázovou službu Azure Cosmos. Tento klient slouží ke konfiguraci a spouštění asynchronních požadavků proti službě.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase`Vytvoření.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer`Vytvoření.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Stejně jako u rozhraní API synchronizace, vytváření položek se provádí pomocí `createItem` metody. Tento příklad ukazuje, jak efektivně `createItem` vydávat mnoho asynchronních požadavků přihlášením k odběru reaktivního datového proudu, který vydává požadavky a vytiskne oznámení. Vzhledem k tomu, že tento `CountDownLatch` jednoduchý příklad běží na dokončení a ukončí, instance se používají k zajištění programu nekončí během vytváření položky. **Správné asynchronní programování praxe není blokovat na asynchronní volání - v realistických případech použití požadavky jsou generovány z main() smyčky, která se spustí neomezeně dlouho, což eliminuje potřebu západka na asynchronní volání.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Stejně jako u rozhraní API `readItem` synchronizace se čtení bodů provádí pomocí metody.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Stejně jako u rozhraní API synchronizace, sql `queryItems` dotazy přes JSON jsou prováděny pomocí metody.

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

3. V okně terminálu git použijte následující příkaz ke spuštění aplikace Java `sync` `async` (nahraďte SYNCASYNCMODE nebo v závislosti na tom, který ukázkový kód chcete spustit, nahraďte YOUR_COSMOS_DB_HOSTNAME kótovanou hodnotou URI z portálu a nahraďte YOUR_COSMOS_DB_MASTER_KEY citovaným primárním klíčem z portálu)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    V okně terminálu se zobrazí oznámení o vytvoření databáze FamilyDB. 
    
4. Aplikace vytvoří databázi s názvem`AzureSampleFamilyDB`
5. Aplikace vytvoří kontejner s názvem`FamilyContainer`
6. Aplikace bude provádět čtení bodů pomocí ID objektu a hodnoty klíče oddílu (což je lastName v naší ukázce). 
7. Aplikace bude dotazovat položky k načtení všech rodin s příjmením v ('Andersen', 'Wakefield', 'Johnson')

7. Aplikace neodstraňuje vytvořené prostředky. Přepněte zpět na portál a [vyčistěte prostředky](#clean-up-resources)  ze svého účtu, aby se vám za ně neúčtovaly poplatky.

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet SQL API Azure Cosmos DB, vytvořit databázi dokumentů a kontejner pomocí Průzkumníka dat a spustit aplikaci Java, abyste to udělali programově. Teď můžete importovat další data do účtu Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
