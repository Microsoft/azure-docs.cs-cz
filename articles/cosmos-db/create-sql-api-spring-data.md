---
title: Rychlý Start – použití jarních dat Azure Cosmos DB V3 k vytvoření databáze dokumentů pomocí Azure Cosmos DB
description: V tomto rychlém startu se zobrazí jarní ukázka kódu Azure Cosmos DB v3, kterou můžete použít pro připojení k rozhraní SQL API Azure Cosmos DB a dotazování na ně.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: acd5914ca9f465c69df4c017162ef92f795b235a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278372"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Rychlý Start: Vytvoření aplikace pružinové dat Azure Cosmos DB v3 pro správu Azure Cosmos DB dat rozhraní SQL API


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> * [Sada Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu vytvoříte a spravujete Azure Cosmos DB účet rozhraní SQL API z Azure Portal a pomocí aplikace na jaře data Azure Cosmos DB V3 naklonované z GitHubu. Nejdřív vytvoříte Azure Cosmos DB účet rozhraní SQL API pomocí Azure Portal a pak vytvoříte aplikaci pružinového spouštění pomocí konektoru jarní data Azure Cosmos DB v3 a pak do svého účtu Cosmos DB přidáte prostředky pomocí aplikace pružiny pro spouštění. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

> [!IMPORTANT]  
> Tyto poznámky k verzi jsou pro Azure Cosmos DB verze 3 jarních dat. [Poznámky k verzi 2 najdete tady](sql-api-sdk-java-spring-v2.md). 
>
> Jarní data Azure Cosmos DB podporují pouze rozhraní SQL API.
>
> V těchto článcích najdete informace o jarních datech v jiných Azure Cosmos DB rozhraních API:
> * [Jarní data pro Apache Cassandra s Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Jarní MongoDBy dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Jarní Gremliny dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) můžete použít také s identifikátorem URI `https://localhost:8081` a klíčem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Najeďte `JAVA_HOME` proměnnou prostředí na složku, ve které je nainstalovaná JDK.
- [Binární archiv Maven](https://maven.apache.org/download.cgi) Na Ubuntu spusťte `apt-get install maven` instalaci Maven.
- [Git](https://www.git-scm.com/downloads). Na Ubuntu spusťte `sudo apt-get install git` instalaci Gitu.

## <a name="introductory-notes"></a>Úvodní poznámky

*Struktura Cosmos DB účtu* Bez ohledu na rozhraní API nebo programovací jazyk, Cosmos DB *účet* obsahuje nula nebo více *databází*, *databáze* (DB) obsahuje nula nebo více *kontejnerů*a *kontejner* obsahuje nula nebo více položek, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entity účtu Azure Cosmos" border="false":::

Další informace o databázích, kontejnerech a položkách si můžete přečíst [tady.](account-databases-containers-items.md) Na úrovni kontejneru je definováno několik důležitých vlastností, mezi kterými je *zřízena propustnost* a *klíč oddílu*. 

Zřízená propustnost se měří v jednotkách žádosti (*ru*), které mají peněžní cenu, a jsou zásadním rozhodujícím faktorem za provozní náklady na účet. Zřízenou propustnost lze vybrat v členitosti podle kontejneru nebo členitosti na databázi, ale obvykle je upřednostňována specifikace propustnosti na úrovni kontejneru. Další informace o zřizování propustnosti si můžete přečíst [tady.](set-throughput.md)

Když jsou položky vloženy do kontejneru Cosmos DB, databáze se poroste vodorovně přidáním dalšího úložiště a výpočetních prostředků pro zpracování požadavků. Kapacita úložiště a výpočetní kapacity se přidávají do diskrétních jednotek označovaných jako *oddíly*a Vy musíte zvolit jedno pole v dokumentu, které bude mít klíč oddílu, který mapuje jednotlivé dokumenty na oddíl. Způsob správy oddílů je, že každý oddíl má přiřazený zhruba stejný řez mimo rozsah hodnot klíče oddílu; Proto doporučujeme zvolit klíč oddílu, který je relativně náhodný nebo rovnoměrně distribuovaný. V opačném případě některé oddíly uvidí podstatně více požadavků (*aktivní oddíl*), zatímco ostatní oddíly jsou v podstatě méně požadavků (*studený oddíl*) a že se to vyhne. [Tady](partitioning-overview.md)se můžete dozvědět víc o vytváření oddílů.

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
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Spuštění aplikace](#run-the-app). 

### <a name="application-configuration-file"></a>Konfigurační soubor aplikace

V tomto příkladu jsme předvedli, jak se stlačují spouštěcích a jarních dat zvyšují uživatelské prostředí – proces vytvoření klienta Cosmos a připojení k prostředkům Cosmos je teď konfigurační místo kódu. Při spouštěcím startu aplikace při spuštění zpracovává všechny tyto často používané příklady pomocí nastavení v **aplikaci. vlastnosti**:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Po vytvoření účtu Azure Cosmos DB, databáze a kontejneru se v konfiguračním souboru, stačí vyplnit prázdná data a data pružiny nebo pružiny automaticky provede následujícími kroky: (1) vytvoření základní instance sady Java SDK `CosmosClient` s identifikátorem URI a klíčem a (2) Připojte se k databázi a kontejneru. Všechno je nastavené – **žádný další kód správy prostředků!**

### <a name="java-source"></a>Zdroj Java

Hodnota dat pružiny – přidání taky pochází z jednoduchého, čistého a standardizovaného rozhraní nezávislého na platformě pro provozování v úložišti dat. Sestavování na výše uvedeném příkladu na jaře data na webu najdete v dokumentu CRUD a ukázky dotazů pro práci s Azure Cosmos DB dokumenty pomocí Azure Cosmos DB pružinových dat.

* Vytváření a aktualizace položek pomocí `save` metody.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Point-čtení pomocí odvozené metody dotazu definované v úložišti. `findByIdAndLastName`Provádí čtení bodů pro `UserRepository` . Pole, která jsou uvedena v názvu metody, způsobují pružinová data pro spuštění bodu-čtení definovaného `id` `lastName` poli a:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Položka se odstraní pomocí `deleteAll` :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Odvozený dotaz založený na názvu metody úložiště Pružinová data implementují `UserRepository` `findByFirstName` metodu jako dotaz SQL sady Java SDK v `firstName` poli (Tento dotaz nebylo možné implementovat jako bod-Read):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Spuštění aplikace

Teď se vraťte zpět na web Azure Portal, kde najdete informace o připojovacím řetězci, a spusťte aplikaci s použitím informací o vašem koncovém bodu. Tím aplikaci umožníte komunikovat s hostovanou databází.

1. V okně terminálu Git přejděte pomocí příkazu `cd` do složky se vzorovým kódem.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. V okně terminálu Git pomocí následujícího příkazu nainstalujte požadovaná Azure Cosmos DB balíčky pro jarní data.

    ```bash
    mvn clean package
    ```

3. V okně terminálu Git pomocí následujícího příkazu spusťte Azure Cosmos DB aplikaci pro jarní data:

    ```bash
    mvn spring-boot:run
    ```
    
4. Aplikace načte **aplikaci. Properties** a připojí prostředky v účtu Azure Cosmos DB.
5. Aplikace provede výše popsané operace CRUD.
6. Aplikace provede odvozený dotaz.
7. Aplikace neodstraní vaše prostředky. Pokud se chcete vyhnout poplatkům, přepněte zpátky na portál a [vyčistěte prostředky](#clean-up-resources) z vašeho účtu.

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit Azure Cosmos DB účet rozhraní SQL API, vytvořit databázi dokumentů a kontejner pomocí Průzkumník dat a spustit datovou aplikaci s pružinou, která provede stejné věci programově. Teď můžete do svého účtu Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)
