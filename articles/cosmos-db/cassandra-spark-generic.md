---
title: Práce s rozhraním API Azure Cosmos DB Cassandra ze Sparku
description: Tento článek je hlavní stránka pro integraci rozhraní API Cosmos DB Cassandra ze Sparku.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: cb34ea44c069f067d13a6480531a94a1a515f380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70241245"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Připojení k rozhraní API Cassandra služby Azure Cosmos DB ze Sparku

Tento článek je jedním z řady článků o integraci rozhraní API Azure Cosmos DB Cassandra ze Sparku. Články pokrývají připojení, operace jazyka definice dat (DDL), základní operace jazyka pro manipulaci s daty (DML) a pokročilou integraci rozhraní API Azure Cosmos DB Cassandra ze Služby Spark. 

## <a name="prerequisites"></a>Požadavky
* [Zřídit účet rozhraní API Azure Cosmos DB Cassandra.](create-cassandra-dotnet.md#create-a-database-account)

* Zřídit si vybrat prostředí Spark [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Ostatní].

## <a name="dependencies-for-connectivity"></a>Závislosti pro připojení
* **Spark konektor pro Cassandra:** Spark konektor se používá k připojení k rozhraní API Azure Cosmos DB Cassandra.  Identifikujte a používejte verzi konektoru umístěného v [Maven central,]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) která je kompatibilní s verzemi Spark a Scala vašeho prostředí Spark.

* **Pomocná knihovna Azure Cosmos DB pro rozhraní CASSANDRA API:** Kromě konektoru Spark potřebujete další knihovnu nazvanou [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) z Azure Cosmos DB. Tato knihovna obsahuje vlastní objekt pro vytváření připojení a opakování tříd zásad.

  Zásady opakování v Azure Cosmos DB je nakonfigurované pro zpracování výjimky stavový kód HTTP 429("Request Rate Large"). Rozhraní API Azure Cosmos DB Cassandra převádí tyto výjimky na přetížené chyby v nativním protokolu Cassandra a můžete to opakovat s back-off. Vzhledem k tomu, že Azure Cosmos DB používá zřízené propustnosti modelu, požadavek omezení rychlosti výjimky dojít při zvýšení rychlosti příchozího přenosu dat a odchozího přenosu dat. Zásady opakování chrání vaše jiskry úlohy proti špičky dat, které dočasně překročí propustnost přidělenou pro váš kontejner.

  > [!NOTE] 
  > Zásady opakování můžete chránit vaše jiskra úlohy proti momentální hroty pouze. Pokud jste nenakonfigurovali dostatek ru potřebné ke spuštění úlohy, pak zásady opakování není použitelná a opakování třídy zásad znovu vyvolá výjimku.

* **Podrobnosti o připojení účtu Azure Cosmos DB:** Název účtu rozhraní API Azure Cassandra, koncový bod účtu a klíč.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parametry konfigurace propustnost konektoru Spark

V následující tabulce jsou uvedeny parametry konfigurace propustnost Azure Cosmos DB CASSANDRA specifické propustnost poskytované konektorem. Podrobný seznam všech konfiguračních parametrů najdete v [tématu referenční](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) stránka konfigurace úložiště GitHub konektoru Spark Cassandra.

| **Název vlastnosti** | **Výchozí hodnota** | **Popis** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Počet řádků na jednu dávku. Nastavte tento parametr na 1. Tento parametr se používá k dosažení vyšší propustnost pro velké úlohy. |
| spark.cassandra.connection.connections_per_executor_max  | Žádný | Maximální počet připojení na uzel na vykonavatele. 10*n je ekvivalentní 10 připojení na uzel v clusteru Cluster u n-uzel Cassandra. Pokud tedy požadujete 5 připojení na uzel na vykonavatele pro cluster Cassandra s 5 uzlem, měli byste tuto konfiguraci nastavit na 25. Upravte tuto hodnotu na základě stupně paralelismu nebo počtu vykonavatelů, pro které jsou nakonfigurovány vaše jiskrové úlohy.   |
| spark.cassandra.output.concurrent.writes  |  100 | Definuje počet paralelních zápisů, ke kterým může dojít na vykonavatele. Vzhledem k tomu, že nastavíte "batch.size.rows" na 1, ujistěte se, že vertikálně navýšit tuto hodnotu odpovídajícím způsobem. Upravte tuto hodnotu na základě stupně paralelismu nebo propustnost, kterou chcete dosáhnout pro vaše úlohy. |
| spark.cassandra.concurrent.reads |  512 | Definuje počet paralelních čtení, ke kterým může dojít na vykonavatele. Upravte tuto hodnotu na základě stupně paralelismu nebo propustnost, které chcete dosáhnout pro vaše úlohy  |
| spark.cassandra.output.throughput_mb_per_sec  | Žádný | Definuje celkovou propustnost zápisu na vykonavatele. Tento parametr lze použít jako horní limit pro propustnost jiskřicí úlohy a založit jej na zřízené propustnosti vašeho kontejneru Cosmos.   |
| spark.cassandra.input.reads_per_sec| Žádný   | Definuje celkovou propustnost čtení na vykonavatele. Tento parametr lze použít jako horní limit pro propustnost jiskřicí úlohy a založit jej na zřízené propustnosti vašeho kontejneru Cosmos.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Definuje počet dávek na jednu úlohu jiskry, které mohou být uloženy v paměti před odesláním do rozhraní CASSANDRA API |
| spark.cassandra.connection.keep_alive_ms | 60000 | Definuje dobu, do které jsou k dispozici nevyužitá připojení. | 

Upravte propustnost a stupeň paralelismu těchto parametrů na základě zatížení, které očekáváte pro úlohy jiskry, a propustnost, kterou jste zříditi pro svůj účet Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Připojení k rozhraní API Azure Cosmos DB Cassandra ze Sparku

### <a name="cqlsh"></a>cqlsh
Následující příkazy podrobně popisují, jak se připojit k rozhraní API Azure CosmosDB Cassandra z cqlsh.  To je užitečné pro ověření při spuštění ukázky v Spark.<br>
**Z Linuxu/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Datové cihly Azure
Článek v tomto článku popisuje zřizování clusteru Azure Databricks, konfiguraci clusteru pro připojení k rozhraní API Azure Cosmos DB Cassandra a několik ukázkových poznámkových bloků, které pokrývají operace DDL, operace DML a další.<BR>
[Práce s rozhraním Azure Cosmos DB Cassandra API z datových cihel Azure](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight-Spark
Následující článek popisuje službu HDinsight-Spark, zřizování, konfiguraci clusteru pro připojení k rozhraní API Azure Cosmos DB Cassandra a několik ukázkových poznámkových bloků, které pokrývají operace DDL, operace DML a další.<BR>
[Pracujte s rozhraním AZURE Cosmos DB Cassandra api z Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. Jiskra prostředí obecně
Zatímco výše uvedené části byly specifické pro služby PaaS založené na Azure Spark, tato část pokrývá jakékoli obecné prostředí Spark.  Závislosti konektoru, importy a konfigurace relace Spark jsou podrobně popsány níže. Část "Další kroky" popisuje ukázky kódu pro operace DDL, operace DML a další.  

#### <a name="connector-dependencies"></a>Závislosti konektoru:

1. Přidejte maven souřadnice získat [konektor Cassandra pro Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Přidání souřadnic maven pro [pomocnou knihovnu Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) pro rozhraní CASSANDRA API

#### <a name="imports"></a>Dovoz:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Konfigurace relace jiskry:

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

Následující články ukazují integraci Spark s rozhraním API Azure Cosmos DB Cassandra. 
 
* [Operace DDL](cassandra-spark-ddl-ops.md)
* [Vytvořit nebo vložit operace](cassandra-spark-create-ops.md)
* [Operace čtení](cassandra-spark-read-ops.md)
* [Upsert operace](cassandra-spark-upsert-ops.md)
* [Odstranit operace](cassandra-spark-delete-ops.md)
* [Agregační operace](cassandra-spark-aggregation-ops.md)
* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)
