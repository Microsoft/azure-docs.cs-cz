---
title: 'Azure Cosmos DB SQL API: příklady pružinového data V3'
description: Příklady pro běžné úlohy pomocí rozhraní API pro Azure Cosmos DB SQL, včetně operací CRUD, najdete na GitHubu v ukázkách pružinového data v3.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: aaa9a78342cf64461d85d7542c7ed8a69186906b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275116"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>Azure Cosmos DB SQL API: jarní data Azure Cosmos DB V3 – příklady

> [!div class="op_single_selector"]
> * [Příklady sady .NET v2 SDK](sql-api-dotnet-samples.md)
> * [Příklady sady .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Příklady sady Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Příklady sad data pružiny V3 SDK](sql-api-spring-data-sdk-samples.md)
> * [Příklady v Node.js](sql-api-nodejs-samples.md)
> * [Příklady v Pythonu](sql-api-python-samples.md)
> * [Galerie ukázkového kódu Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

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

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Můžete si [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Za své předplatné sady Visual Studio každý měsíc získáváte kredity, které můžete použít k placení za služby Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Nejnovější ukázkové aplikace, které provádějí operace CRUD a další běžné operace týkající se Azure Cosmos DBch prostředků, jsou součástí úložiště GitHub [Azure-jaře-data-Cosmos-Java-SQL-API-Samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples) . Tento článek obsahuje:

* Odkazuje na úlohy v každém z ukázkových dat na jaře Azure Cosmos DB soubory projektu. 
* Odkazy na související referenční obsah rozhraní API.

**Požadavky**

Ke spuštění této ukázkové aplikace budete potřebovat:

* Sadu Java Development Kit (JDK) 8
* Jarní data Azure Cosmos DB V3

Volitelně můžete použít Maven k získání nejnovějších binárních dat pružiny Azure Cosmos DB v3 pro použití ve vašem projektu. Maven automaticky přidá všechny potřebné závislosti. V opačném případě můžete přímo stáhnout závislosti uvedené v souboru **pom.xml** a přidat je do cesty k sestavení.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Spuštění ukázkových aplikací**

Naklonování ukázkového úložiště:
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

Ukázky můžete spustit buď pomocí integrovaného vývojového prostředí (zatmění, IntelliJ nebo VSCODE), nebo z příkazového řádku pomocí Maven.

V souboru **Application. Properties** musí být nastavené tyto proměnné prostředí.

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

aby měly vzory přístup pro čtení a zápis k vašemu účtu, databázím a kontejnerům.

Vaše IDE může poskytovat možnost spustit vzorový kód pro jarní data. V opačném případě můžete k provedení ukázky použít následující příkaz terminálu:

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>Příklady dokumentu CRUD
V souboru [ukázek](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) se dozvíte, jak provádět následující úlohy. Další informace o dokumentech Azure Cosmos před spuštěním následujících ukázek najdete v článku [práce s databázemi, kontejnery a položkami](account-databases-containers-items.md) konceptu.

| Úloha | API – referenční informace |
| --- | --- |
| [Vytvoření dokumentu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository. Save |
| [Čtení dokumentu podle ID](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [Odstranit všechny dokumenty](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteAll |

## <a name="derived-query-method-examples"></a>Příklady metod odvozených dotazů
V souboru [ukázek](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) se dozvíte, jak provádět následující úlohy. Pokud se chcete dozvědět o Azure Cosmos DB dotazů před spuštěním následujících ukázek, může být užitečné přečíst si [odvozené metody dotazů Baeldung v článku jarní](https://www.baeldung.com/spring-data-derived-queries) .

| [Dotaz na dokumenty](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository. derivedQueryMethod |

## <a name="custom-query-examples"></a>Příklady vlastních dotazů
V souboru [ukázek](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) se dozvíte, jak provádět následující úlohy pomocí gramatiky dotazů SQL. Další informace o odkazech na dotaz SQL v Azure Cosmos DB před spuštěním následujících ukázek najdete v tématu [příklady dotazů SQL pro Azure Cosmos DB](how-to-sql-query.md). 


| Úloha | API – referenční informace |
| --- | --- |
| [Dotazování na všechny dokumenty](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query poznámky |
| [Dotaz na rovnost s využitím ==](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query poznámky |
| [Dotaz na nerovnost s využitím != a NOT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query poznámky |
| [Dotaz s využitím operátorů rozsahu jako >, <, >=, <=](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query poznámky |
| [Dotaz s využitím operátorů rozsahu na řetězce](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query poznámky |
| [Dotaz s ŘAZENÍm podle](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query poznámky |
| [Dotaz s JEDINEČNÝm](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query poznámky |
| [Dotaz s agregačními funkcemi](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query poznámky |
| [Práce s vnořenými dokumenty](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query poznámky |
| [Dotaz s využitím operace Join uvnitř dokumentů](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query poznámky |
| [Dotaz pomocí operátorů String, Math a Array](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query poznámky |