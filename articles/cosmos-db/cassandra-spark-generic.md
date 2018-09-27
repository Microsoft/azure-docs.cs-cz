---
title: Práce s Azure Cosmos DB Cassandra API z aplikace Spark
description: Tento článek je hlavní stránky pro integraci Cosmos DB Cassandra API z aplikace Spark.
services: cosmos-db
author: anagha-microsoft
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: f2f5aebf32cf5860ca8fc32ab741177c6df15c60
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227193"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Připojení ke službě Azure Cosmos DB Cassandra API z aplikace Spark

Tento článek je jedním z řady článků o integraci Azure Cosmos DB Cassandra API z aplikace Spark. V článcích zahrnují připojení, operace Language(DDL) definice dat, základní operace Language(DML) zpracování dat a pokročilou integraci Azure Cosmos DB Cassandra API z aplikace Spark. 

## <a name="prerequisites"></a>Požadavky
* [Zřídíte účet Azure Cosmos DB Cassandra API.](create-cassandra-dotnet.md#create-a-database-account)

* Zřízení podle vašeho výběru prostředí Spark [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight – Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Další].

## <a name="dependencies-for-connectivity"></a>Závislosti pro připojení
* **Konektor Spark pro Cassandra:** konektor Spark se používá pro připojení k rozhraní Cassandra API služby Azure Cosmos DB.  Identifikovat a používat verzi konektoru v [Maven centrální]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) , který je kompatibilní s verzemi Spark a Scala prostředí Spark.

* **Azure Cosmos DB pomocné knihovny pro Apache Cassandra API:** kromě konektor Spark, budete potřebovat další knihovnu s názvem [azure-cosmos-cassandra-spark – pomocné rutiny]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) ze služby Azure Cosmos DB. Tato knihovna obsahuje objekt pro vytváření připojení a třídy zásad vlastní opakování.

  Zásady opakování ve službě Azure Cosmos DB je nakonfigurovaný pro zpracování protokolu HTTP stavový kód 429 ("požadavek velké míry") výjimky. Azure Cosmos DB Cassandra API přeloží tyto výjimky na přetížené chyby na nativní protokolu Cassandra a můžete opakovat s back konfigurace. Vzhledem k tomu Azure Cosmos DB používá model zřízená propustnost, žádost o rychlost limitující dojít k výjimkám zvýšení tarifů příchozí a odchozí transakce. Zásady opakování chrání úlohy spark proti provozní špičky data, která momentálně překročí propustnost přidělené vaší kolekce.

  > [!NOTE] 
  > Zásady opakování může chránit úlohy spark proti pouze přechodné špičky. Pokud jste nenakonfigurovali dostatek jednotek ru potřebné ke spuštění vaší úlohy, neplatí zásady opakování a třídy zásad opakování znovu vyvolá výjimku.

* **Podrobnosti o připojení účtu Azure Cosmos DB:** název účtu, koncový bod účtu a klíč vašeho rozhraní Cassandra API služby Azure.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parametry konfigurace propustnost konektoru Spark

Následující tabulka obsahuje parametry konfigurace specifické pro službu Azure Cosmos DB Cassandra API propustnost poskytnuté konektoru. Podrobný seznam všech parametrů konfigurace najdete v tématu [referenční informace o konfiguraci](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) stránka úložiště GitHub konektoru Spark Cassandra.

| **Název vlastnosti** | **Výchozí hodnota** | **Popis** |
|---------|---------|---------|
| Spark.cassandra.Output.batch.Size.Rows |  1 |Počet řádků v jedné dávce. Tento parametr nastavte na hodnotu 1. Tento parametr se používá k dosažení vyšší propustnost pro náročné úlohy. |
| Spark.cassandra.Connection.connections_per_executor_max  | Žádný | Maximální počet připojení za uzel a prováděcí modul. 10 * je ekvivalentní k 10 připojení podle počtu uzlů v clusteru Cassandra uzel n n. Ano Pokud požadujete 5 připojení za uzel a prováděcí modul 5 uzlů clusteru Cassandra, pak byste měli nastavit tuto konfiguraci 25. Upravte tuto hodnotu na základě stupně paralelního zpracování nebo počtu prováděcích procesů nakonfigurovaných pro sparkových úloh.   |
| Spark.cassandra.Output.Concurrent.Writes  |  100 | Definuje počet paralelních zápisů, které mohou nastat za prováděcího modulu. Protože "batch.size.rows" je nastavena na hodnotu 1, ujistěte se, že chcete vertikálně navýšit kapacitu této hodnoty odpovídajícím způsobem. Upravte tuto hodnotu podle stupeň paralelismu nebo propustnost, které chcete dosáhnout u svých úloh. |
| Spark.cassandra.Concurrent.reads |  512 | Definuje počet paralelních čtení, které mohou nastat za prováděcího modulu. Upravit tuto hodnotu podle stupeň paralelismu nebo propustnost, které chcete dosáhnout vašich úloh  |
| Spark.cassandra.Output.throughput_mb_per_sec  | Žádný | Definuje celkový zápisu propustnost za prováděcího modulu. Tento parametr lze použít jako horní omezení pro propustnost úlohy spark a založit na zřízenou propustnost vaší kolekce Cosmos DB.   |
| Spark.cassandra.Input.reads_per_sec| Žádný   | Definuje celková propustnost čtení za prováděcího modulu. Tento parametr lze použít jako horní omezení pro propustnost úlohy spark a založit na zřízenou propustnost vaší kolekce Cosmos DB.  |
| Spark.cassandra.Output.batch.Grouping.Buffer.Size |  1000  | Definuje počet dávek na spark jeden úkol, který můžou být uložené v paměti před odesláním do rozhraní Cassandra API |
| Spark.cassandra.Connection.keep_alive_ms | 60000 | Definuje dobu, po kterou nepoužívané připojení jsou k dispozici. | 

Nastavte propustnost a stupeň paralelismu z těchto parametrů na základě vytížení, které očekáváte, že pro vaše úlohy spark a propustnost, kterou jste zřídili pro váš účet služby Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Připojení ke službě Azure Cosmos DB Cassandra API z aplikace Spark

### <a name="cqlsh"></a>cqlsh
Následující příkazy podrobně popisují, jak se připojit k Azure cosmos DB Cassandra API z cqlsh.  To je užitečné pro ověření při spuštění ukázky ve Sparku.<br>
**Ze systému Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.windows-ppe.net 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
Následující článek popisuje Azure Databricks pro cluster pro zřizování a konfiguraci clusteru pro připojení k rozhraní Cassandra API služby Azure Cosmos DB a několika ukázkové poznámkové bloky, které zahrnují operace DDL, operace DML a další.<BR>
[Práce s Azure Cosmos DB Cassandra API z Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Azure HDInsight Spark
Na článek dole zahrnuje službu HDinsight Spark, zřizování, konfigurace clusteru pro připojení k rozhraní Cassandra API služby Azure Cosmos DB a několika ukázkové poznámkové bloky, které zahrnují operace DDL, operace DML a další.<BR>
[Práce s Azure Cosmos DB Cassandra API z Azure HDInsight Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Obecně Spark prostředí
Kdy výše uvedené části byly specifické pro služby PaaS založené na Sparku v Azure, tato část zahrnuje všechny obecné prostředí Spark.  Konektor závislosti, dovoz a konfigurace relace Spark je podrobně popsaný níže. V části "Další kroky" obsahuje ukázky kódu pro operace DDL, operace DML a další.  

#### <a name="connector-dependencies"></a>Konektor závislosti:

1. Přidat souřadnice maven zobrazíte [Cassandra konektor pro Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Souřadnice maven pro přidání [pomocné knihovny služby Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) pro Apache Cassandra API

#### <a name="imports"></a>Importy:

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

## <a name="next-steps"></a>Další postup

Následující články ukazují Spark integrace s Azure Cosmos DB Cassandra API. 
 
* [Operace DDL](cassandra-spark-ddl-ops.md)
* [Vytvořit/vložit operací](cassandra-spark-create-ops.md)
* [operace čtení](cassandra-spark-read-ops.md)
* [Operace Upsert](cassandra-spark-upsert-ops.md)
* [Operace odstranění](cassandra-spark-delete-ops.md)
* [Agregační operace](cassandra-spark-aggregation-ops.md)
* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)
