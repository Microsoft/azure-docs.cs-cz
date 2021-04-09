---
title: Práce s Azure Cosmos DB rozhraní API Cassandra ze Sparku
description: Tento článek je hlavní stránkou pro Cosmos DB rozhraní API Cassandra integraci z Sparku.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: d25e168e342e22af9dc41d31dd7e18530aaa22b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090507"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Připojení k rozhraní API Cassandra služby Azure Cosmos DB ze Sparku
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Tento článek je jedním z řady článků o Azure Cosmos DB rozhraní API Cassandra integraci z Sparku. Články zahrnují připojení, operace jazyka DDL (Data Definition Language), operace DML (Basic data prohledává jazyk) a rozšířené Azure Cosmos DB rozhraní API Cassandra integraci z Sparku. 

## <a name="prerequisites"></a>Požadavky
* [Zřídí účet Azure Cosmos DB rozhraní API Cassandra.](create-cassandra-dotnet.md#create-a-database-account)

* Zajištění výběru prostředí Spark [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)  |  [Azure HDInsight – Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) | Ostatní].

## <a name="dependencies-for-connectivity"></a>Závislosti pro připojení
* **Konektor Spark pro Cassandra:** Konektor Spark slouží k připojení k Azure Cosmos DB rozhraní API Cassandra.  Identifikujte a používejte verzi konektoru, která se nachází v [Maven Central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) , která je kompatibilní s verzemi Spark a Scala vašeho prostředí Spark.

* **Azure Cosmos DB pomocná knihovna pro rozhraní API Cassandra:** Kromě konektoru Spark potřebujete další knihovnu nazvanou [Azure-Cosmos-Cassandra-Spark-Helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) z Azure Cosmos DB. Tato knihovna obsahuje třídy zásad vlastního vytváření připojení a opakování.

  Zásady opakování v Azure Cosmos DB jsou nakonfigurované tak, aby zpracovávala stavový kód HTTP 429 ("četnost požadavků"). Azure Cosmos DB rozhraní API Cassandra tyto výjimky překládá na chyby přetížení v nativním protokolu Cassandra a můžete je opakovat pomocí back-startů. Vzhledem k tomu, že Azure Cosmos DB používá zřízený model propustnosti, při nárůstu počtu vstupních/výstupních rychlostí dojde k výjimkám omezení rychlosti požadavků. Zásady opakování chrání vaše úlohy Spark proti špičkám dat, které dokončují propustnost přidělené pro váš kontejner.

  > [!NOTE] 
  > Zásady opakování můžou chránit vaše úlohy Sparku jenom v momentech pouze těch špiček. Pokud jste nenakonfigurovali dostatek ru potřebných ke spuštění úlohy, pak se zásady opakování nepoužijí a třída zásad opakování vyvolá výjimku znovu.

* **Podrobnosti připojení Azure Cosmos DB účtu:** Název účtu Azure rozhraní API Cassandra, koncový bod účtu a klíč.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parametry konfigurace propustnosti Spark Connectoru

V následující tabulce jsou uvedeny Azure Cosmos DB parametry konfigurace propustnosti specifické pro rozhraní API Cassandra od konektoru. Podrobný seznam všech parametrů konfigurace najdete na stránce [referenční konfigurační](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) stránka úložiště GitHub konektoru Spark Cassandra.

| **Název vlastnosti** | **Výchozí hodnota** | **Popis** |
|---------|---------|---------|
| spark.cassandra.output.batch. Size. Rows |  1 |Počet řádků na jednu dávku Nastavte tento parametr na hodnotu 1. Tento parametr slouží k dosažení vyšší propustnosti pro náročné úlohy. |
| spark.cassandra.connection.connections_per_executor_max  | Žádné | Maximální počet připojení na uzel na jeden prováděcí modul. 10 * n je ekvivalentem 10 připojení na uzel v clusteru Cassandra n-Node. Pokud tedy budete potřebovat 5 připojení na uzel na jeden prováděcí modul pro cluster Cassandra s pěti uzly, měli byste nastavit tuto konfiguraci na 25. Změňte tuto hodnotu na základě stupně paralelismu nebo počtu prováděcích modulů, pro které jsou úlohy Spark nakonfigurované.   |
| Spark. Cassandra. Output. souběžné. zápisy  |  100 | Definuje počet paralelních zápisů, které mohou být provedeny na vykonavateli. Vzhledem k tomu, že jste nastavili "Batch. Size. Rows" na hodnotu 1, nezapomeňte odpovídajícím způsobem škálovat tuto hodnotu. Tuto hodnotu upravte na základě úrovně paralelismu nebo propustnosti, kterou chcete pro své zatížení dosáhnout. |
| Spark. Cassandra. souběžné. čtení |  512 | Definuje počet paralelních čtení, ke kterým může dojít na vykonavatel. Změňte tuto hodnotu na základě úrovně paralelismu nebo propustnosti, kterou chcete pro své zatížení dosáhnout.  |
| spark.cassandra.output.throughput_mb_per_sec  | Žádné | Definuje celkovou propustnost zápisu na vykonavatele. Tento parametr se dá použít jako horní limit pro propustnost úloh Sparku a založit ho na zřízené propustnosti vašeho kontejneru Cosmos.   |
| spark.cassandra.input.reads_per_sec| Žádné   | Definuje celkovou propustnost čtení na vykonavatele. Tento parametr se dá použít jako horní limit pro propustnost úloh Sparku a založit ho na zřízené propustnosti vašeho kontejneru Cosmos.  |
| spark.cassandra.output.batch. Grouping. Buffer. Size |  1000  | Definuje počet dávek na jednu úlohu Spark, které mohou být před odesláním do rozhraní API Cassandra uloženy v paměti. |
| spark.cassandra.connection.keep_alive_ms | 60000 | Definuje časovou prodlevu, po kterou nejsou k dispozici žádná nepoužívaná připojení. | 

Nastavte propustnost a stupeň paralelismu těchto parametrů na základě zatížení, které očekáváte pro úlohy Spark, a propustnosti, kterou jste zřídili pro váš účet Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Připojení k rozhraní API Cassandra služby Azure Cosmos DB ze Sparku

### <a name="cqlsh"></a>cqlsh
Následující příkazy podrobně popisují, jak se připojit k Azure CosmosDB rozhraní API Cassandra z cqlsh.  To je užitečné při ověřování při spuštění ukázek ve Sparku.<br>
**Ze systému Linux/UNIX/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure Databricks
Níže uvedený článek popisuje Azure Databricks zřízení clusteru, konfiguraci clusteru pro připojení k Azure Cosmos DB rozhraní API Cassandra a několik ukázkových poznámkových bloků, které zahrnují operace DDL, operace DML a další.<BR>
[Práce s Azure Cosmos DB rozhraní API Cassandra z Azure datacihly](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. HDInsight-Spark Azure
Níže uvedený článek popisuje HDinsight-Spark služby, zřizování, konfiguraci clusteru pro připojení k Azure Cosmos DB rozhraní API Cassandra a několik ukázkových poznámkových bloků, které zahrnují operace DDL, operace DML a další.<BR>
[Práce s Azure Cosmos DB rozhraní API Cassandra z Azure HDInsight – Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. sparkové prostředí obecně
I když byly výše uvedené části specifické pro služby PaaS založené na Azure Spark, Tato část se zabývá všemi obecnými prostředími Spark.  Závislosti konektorů, importů a konfigurace relací Spark jsou podrobně popsané níže. Oddíl "další kroky" obsahuje ukázky kódu pro operace DDL, operace DML a další.  

#### <a name="connector-dependencies"></a>Závislosti konektoru:

1. Přidáním souřadnic Maven získáte [konektor Cassandra pro Spark](cassandra-spark-generic.md#dependencies-for-connectivity) .
2. Přidání souřadnic Maven pro [pomocnou knihovnu Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) pro rozhraní API Cassandra

#### <a name="imports"></a>Objem

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Konfigurace relace Spark:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Další kroky

Následující články ukazují integraci Sparku s Azure Cosmos DB rozhraní API Cassandra. 
 
* [Operace DDL](cassandra-spark-ddl-ops.md)
* [Operace vytvoření/vložení](cassandra-spark-create-ops.md)
* [Operace čtení](cassandra-spark-read-ops.md)
* [Operace Upsert](cassandra-spark-upsert-ops.md)
* [Operace Delete](cassandra-spark-delete-ops.md)
* [Operace agregace](cassandra-spark-aggregation-ops.md)
* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)