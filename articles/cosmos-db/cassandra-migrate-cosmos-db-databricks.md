---
title: Migrace dat z Apache Cassandra do Azure Cosmos DB rozhraní API Cassandra pomocí datacihly (Spark)
description: Naučte se migrovat data z databáze Apache Cassandra do Azure Cosmos DB rozhraní API Cassandra pomocí Azure Databricks a Sparku.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caedefbf3887205b68bcd5de5e7cd5f1f7d7f53c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801005"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Migrace dat z Cassandra do účtu Azure Cosmos DB rozhraní API Cassandra pomocí Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Rozhraní API Cassandra v Azure Cosmos DB se stal skvělou volbou pro podniková zatížení, která běží na Apache Cassandra, z několika důvodů:

* **Žádná režie správy a monitorování:** Eliminuje režijní náklady na správu a monitorování nastavení napříč operačním systémem, JVM a YAML soubory a jejich interakcemi.

* **Významné úspory nákladů:** Můžete ušetřit náklady pomocí Azure Cosmos DB, což zahrnuje náklady na virtuální počítače, šířku pásma a všechny příslušné licence. Nemusíte spravovat datová centra, servery, úložiště SSD, sítě a náklady na elektřinu.

* **Možnost použít existující kód a nástroje:** Azure Cosmos DB poskytuje kompatibilitu na úrovni protokolu se stávajícími sadami SDK a nástroji Cassandra. Tato kompatibilita zajišťuje, že můžete použít stávající základ kódu s Azure Cosmos DB rozhraní API Cassandra s triviálními změnami.

Existuje mnoho způsobů, jak migrovat databázové úlohy z jedné platformy do jiné. [Azure Databricks](https://azure.microsoft.com/services/databricks/) je nabídka typu platforma jako služba (PaaS) pro [Apache Spark](https://spark.apache.org/) , která nabízí způsob, jak provádět offline migrace ve velkém měřítku. Tento článek popisuje kroky potřebné k migraci dat z nativních Cassandra klíčů a tabulek Apache do Azure Cosmos DB rozhraní API Cassandra pomocí Azure Databricks.

## <a name="prerequisites"></a>Požadavky

* [Zřídí účet Azure Cosmos DB rozhraní API Cassandra](create-cassandra-dotnet.md#create-a-database-account).

* [Projděte si základy připojení k Azure Cosmos DB rozhraní API Cassandra](cassandra-spark-generic.md).

* Zkontrolujte [podporované funkce v Azure Cosmos DB rozhraní API Cassandra](cassandra-support.md) , abyste zajistili kompatibilitu.

* Ujistěte se, že jste už vytvořili prázdné prostory a tabulky v cílovém Azure Cosmos DB rozhraní API Cassandram účtu.

* [Pro ověření použijte cqlsh nebo hostované prostředí](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Zřízení clusteru Azure Databricks

Podle pokynů můžete [zřídit cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Doporučujeme vybrat modul runtime datacihly verze 7,5, který podporuje Spark 3,0.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Snímek obrazovky s informacemi o tom, jak najít verzi modulu runtime datacihly":::

## <a name="add-dependencies"></a>Přidat závislosti

Abyste se mohli připojit k nativním i Azure Cosmos DB koncovým bodům Cassandra, musíte do clusteru přidat knihovnu Apache Spark konektoru Cassandra. V clusteru vyberte **knihovny**  >  **nainstalovat nový**  >  **Maven** a pak přidejte `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` do souřadnic Maven.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Snímek obrazovky, který ukazuje hledání balíčků Maven v datacihlách.":::

Po dokončení instalace vyberte **nainstalovat** a pak cluster restartujte.

> [!NOTE]
> Po instalaci knihovny konektoru Cassandra se ujistěte, že restartujete cluster datacihly.

## <a name="create-scala-notebook-for-migration"></a>Vytvoření poznámkového bloku Scala pro migraci

V datacihlách vytvořte Scala Poznámkový blok. Své zdrojové a cílové Cassandra konfigurace nahraďte odpovídajícími přihlašovacími údaji a zdrojovými a cílovými oblastmi a tabulkami. Pak spusťte následující kód:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> `spark.cassandra.output.batch.size.rows`Hodnoty a `spark.cassandra.output.concurrent.writes` a počet pracovních procesů v clusteru Spark jsou důležité konfigurace, které je třeba ladit, aby nedocházelo [k omezení rychlosti](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/). Omezení rychlosti se projeví, když žádosti o Azure Cosmos DB překračují zřízenou propustnost nebo [jednotky žádostí](./request-units.md) (ru). Tato nastavení může být nutné upravit v závislosti na počtu prováděcích modulů v clusteru Spark a potenciálně velikosti (a proto náklady na RU) každého záznamu zapsaného do cílových tabulek.

## <a name="troubleshoot"></a>Řešení potíží

### <a name="rate-limiting-429-error"></a>Omezení rychlosti (Chyba 429)

Může se zobrazit chybový kód 429 nebo "frekvence požadavků je velká", a to i v případě, že jste nastavení omezili na minimální hodnoty. Omezení četnosti může způsobit následující scénáře:

* **Propustnost přidělená tabulce je menší než 6 000 [jednotek žádostí](./request-units.md)**. V případě minimálního nastavení dokáže Spark zapisovat rychlostí přibližně 6 000 jednotek žádosti. Pokud jste zřídili tabulku v prostoru klíčů se sdílenou propustností, je možné, že tato tabulka má méně než 6 000 ru dostupných za běhu.

    Zajistěte, aby v tabulce, na kterou migrujete, bylo při spuštění migrace k dispozici minimálně 6 000 ru. V případě potřeby přidělte této tabulce vyhrazené jednotky žádosti.

* **Nadměrné zkosení dat s velkým objemem dat**. Pokud máte velké množství dat, která se mají migrovat do dané tabulky, ale mají v datech značnou hodnotu zkosení dat (to znamená, že se zapisuje velký počet záznamů pro stejnou hodnotu klíče oddílu), může i nadále docházet k omezení rychlosti i v případě, že máte v tabulce zřízených několik [jednotek žádostí](./request-units.md) . Jednotky žádostí jsou rozdělené rovnoměrně mezi fyzické oddíly a vysoké zešikmení dat může způsobit kritické body požadavků na jeden oddíl.

    V tomto scénáři zmenšete nastavení minimální propustnosti ve Sparku a vynuťte, aby migrace běžela pomalu. Tento scénář může být běžnější, pokud migrujete tabulky odkazů nebo ovládacích prvků, kde je přístup méně častý a může být vysoký i zkosený. Pokud se ale v jakémkoli jiném typu tabulky nachází výrazné zkosení, můžete si projít datový model a vyhnout se tak problémům s oddíly pro vaše zatížení během operací ustálených stavů.

## <a name="next-steps"></a>Další kroky

* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Doporučené postupy pro klíč oddílu](partitioning-overview.md#choose-partitionkey)
* [Odhad RU/s pomocí plánovače kapacity Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
* [Elastické škálování v Azure Cosmos DB rozhraní API Cassandra](manage-scale-cassandra.md)
