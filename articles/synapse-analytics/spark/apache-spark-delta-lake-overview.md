---
title: Přehled způsobu použití systému Linux Foundation Delta Lake v Apache Spark pro Azure synapse Analytics
description: Naučte se používat ke službě Azure synapse Analytics v Apache Spark k vytváření a používání tabulek s vlastnostmi kyseliny rozdílové Lake.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 1e0dfd597e7f445eeba6cef332d8ea12b27dc3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676302"
---
# <a name="linux-foundation-delta-lake-overview"></a>Přehled rozdílových Lake pro Linux Foundation

Tento článek jsme [tady](https://docs.delta.io/latest/quick-start.html)přizpůsobili pro větší přehlednost z původního protějšku. Tento článek vám pomůže rychle prozkoumat hlavní funkce [rozdílových Lake](https://delta.io). Tento článek obsahuje fragmenty kódu, které ukazují, jak číst a zapisovat do rozdílových tabulek Lake Lake z interaktivních, dávkových a streamových dotazů. Fragmenty kódu jsou také k dispozici v sadě poznámkových bloků [PySpark zde](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb), [Scala zde](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb)a [C#](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb)

Přejdeme na to, co se zabývá:

* Vytvoření tabulky
* Čtení dat
* Aktualizovat data tabulky
* Přepsat data tabulky
* Podmíněná aktualizace bez přepsání
* Čtení starších verzí dat s využitím času na cestách
* Zápis proudu dat do tabulky
* Načte datový proud změn z tabulky.
* Podpora SQL

## <a name="configuration"></a>Konfigurace

Ujistěte se, že pro vaše prostředí upravíte níže uvedenou níže.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Výsledky:

'/delta/delta-table-335323'

## <a name="create-a-table"></a>Vytvoření tabulky

Chcete-li vytvořit rozdílovou tabulku Lake Lake, napište datový rámec jako datový rámec ve formátu Delta. Můžete změnit formát z Parquet, CSV, JSON a tak dále na rozdíl.

Následující kód ukazuje, jak vytvořit novou rozdílovou tabulku se systémem pomocí schématu odvozeného z vašeho datového rámce.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Výsledky:

| ID|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Čtení dat

Data si přečtete v tabulce rozdílového Lake, a to zadáním cesty k souborům a rozdílovým formátům.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Výsledky:

| ID|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

Pořadí výsledků se liší od výše uvedeného, protože před výstupem výsledků nebylo explicitně zadáno žádné pořadí.

## <a name="update-table-data"></a>Aktualizovat data tabulky

Rozdílový Lake podporuje několik operací pro úpravu tabulek pomocí standardních rozhraní API dataframe, jedná se o jedno ze velkých vylepšení, která přidá rozdílový formát. Následující příklad spustí úlohu Batch, která přepíše data v tabulce.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Výsledky:

| ID|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

Tady vidíte, že se všechny pět záznamů aktualizovaly tak, aby obsahovaly nové hodnoty.

## <a name="save-as-catalog-tables"></a>Uložit jako katalogové tabulky

Rozdílový Lake může zapisovat do spravovaných nebo externích tabulek katalogu.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Výsledky:

|database|         tableName|velmi dočasné|
|--------|------------------|-----------|
| default|externaldeltatable|      false (nepravda)|
| default| manageddeltatable|      false (nepravda)|

Pomocí tohoto kódu jste v katalogu vytvořili novou tabulku z existujícího datového rámce, který se označuje jako spravovaná tabulka. Pak jste v katalogu definovali novou externí tabulku, která používá existující umístění, označované jako externí tabulka. Ve výstupu uvidíte obě tabulky bez ohledu na to, jak byly vytvořeny, a jsou uvedeny v katalogu.

Nyní se můžete podívat na rozšířené vlastnosti obou těchto tabulek.

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Výsledky:

|col_name                    |data_type                                                                                                    |comment|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |bigint                                                                                                       |null   |
|                            |                                                                                                             |       |
|Podrobné informace o tabulce  |                                                                                                             |       |
|databáze                    |default                                                                                                      |       |
|Tabulka                       |manageddeltatable                                                                                            |       |
|Vlastník                       |Trusted-Service-User                                                                                         |       |
|Čas vytvoření                |So Apr 25 00:35:34 UTC 2020                                                                                 |       |
|Poslední přístup                 |Čtvrtek LED 01 00:00:00 UTC 1970                                                                                 |       |
|Created By                  |Spark 2.4.4.2.6.99.201 – 11401300                                                                              |       |
|Typ                        |STAROSTI                                                                                                      |       |
|Poskytovatel                    |Delta                                                                                                        |       |
|Vlastnosti tabulky            |[transient_lastDdlTime = 1587774934]                                                                           |       |
|Statistika                  |2407 bajtů                                                                                                   |       |
|Umístění                    |abfss://data @ <data lake> . DFS.Core.Windows.NET/synapse/Workspaces/ <workspace name> /Warehouse/manageddeltatable|       |
|Knihovna Serde               |org. Apache. Hadoop. podregistr. serde2. opožděné. LazySimpleSerDe                                                           |       |
|InputFormat                 |org. Apache. Hadoop. mapred. SequenceFileInputFormat                                                             |       |
|OutputFormat                |org. Apache. Hadoop. podregistr. QL. IO. HiveSequenceFileOutputFormat                                                    |       |
|Vlastnosti úložiště          |[Serialization. Format = 1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Výsledky:

|col_name                    |data_type                                                             |comment|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |bigint                                                                |null   |
|                            |                                                                      |       |
|Podrobné informace o tabulce  |                                                                      |       |
|databáze                    |default                                                               |       |
|Tabulka                       |externaldeltatable                                                    |       |
|Vlastník                       |Trusted-Service-User                                                  |       |
|Čas vytvoření                |So Apr 25 00:35:38 UTC 2020                                          |       |
|Poslední přístup                 |Čtvrtek LED 01 00:00:00 UTC 1970                                          |       |
|Created By                  |Spark 2.4.4.2.6.99.201 – 11401300                                       |       |
|Typ                        |ZAHRANIČNÍCH                                                              |       |
|Poskytovatel                    |ROZDÍL                                                                 |       |
|Vlastnosti tabulky            |[transient_lastDdlTime = 1587774938]                                    |       |
|Umístění                    |abfss://data @ <data lake> . DFS.Core.Windows.NET/Delta/Delta-Table-587152|       |
|Knihovna Serde               |org. Apache. Hadoop. podregistr. serde2. opožděné. LazySimpleSerDe                    |       |
|InputFormat                 |org. Apache. Hadoop. mapred. SequenceFileInputFormat                      |       |
|OutputFormat                |org. Apache. Hadoop. podregistr. QL. IO. HiveSequenceFileOutputFormat             |       |
|Vlastnosti úložiště          |[Serialization. Format = 1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Podmíněná aktualizace bez přepsání

Delta Lake poskytuje programová rozhraní API pro podmíněné aktualizace, odstraňování a sloučení (Tato data se v tabulkách běžně označují jako Upsert).

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Výsledky:

| ID|
|---|
|106|
|108|
|  5|
|  7|
|  9|

Tady jste přidali 100 pro každé sudé ID.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Výsledky:

| ID|
|---|
|  5|
|  7|
|  9|

Všimněte si, že všechny sudé řádky byly odstraněny.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Výsledky:

| ID|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

Tady máte kombinaci stávajících dat. Existujícím datům byla přiřazena hodnota-1 v cestě kódu Update (WhenMatched). Nová data, která byla vytvořena v horní části fragmentu kódu a byla přidána prostřednictvím cesty vložení kódu (WhenNotMatched), byla také přidána.

### <a name="history"></a>Historie

Funkce Delta Lake má možnost povolit prohlížení historie tabulky. To znamená změny provedené v podkladové tabulce rozdílů. Následující buňka ukazuje, jak jednoduché je zkontrolovat historii.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Výsledky:

|verze|          časové razítko|userId|userName|operation|                                                operationParameters| úloha|poznámkový blok|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  null|    null|    SLOUČENÍ|                       [predikát-> (oldData.`ID` = newData. `ID` )]|null|    null|     null|          3|          null|        false (nepravda)|
|      3|2020-04-25 00:36:08|  null|    null|   DELETE|[predikát-> ["(( `ID` % cast (2 jako bigint)) = přetypování (0 jako bigint))]|null|    null|     null|          2|          null|        false (nepravda)|
|      2|2020-04-25 00:35:51|  null|    null|   UPDATE| [predikát-> ((ID # 744L% cast (2 jako bigint)) = přetypování (0 jako bigint))]|null|    null|     null|          1|          null|        false (nepravda)|
|      1|2020-04-25 00:35:05|  null|    null|    PSAL|                             [Mode-> přepsání, partitionBy-> []]|null|    null|     null|          0|          null|        false (nepravda)|
|      0|2020-04-25 00:34:34|  null|    null|    PSAL|                         [Mode-> ErrorIfExists, partitionBy-> []]|null|    null|     null|       null|          null|         true|

Tady vidíte všechny úpravy provedené výše uvedenými fragmenty kódu.

## <a name="read-older-versions-of-data-using-time-travel"></a>Čtení starších verzí dat s využitím času na cestách

Je možné zadat dotaz na předchozí snímky z rozdílové tabulky Lake pomocí funkce s názvem Doba cesty. Chcete-li získat přístup k datům, která jste přepsali, můžete zadat dotaz na snímek tabulky před tím, než jste přepsali první sadu dat pomocí možnosti versionAsOf.

Po spuštění níže uvedené buňky by se měla zobrazit první sada dat před tím, než jste ji přepsali. Doba cesty je velice efektivní funkce, která využívá sílu protokolu rozdílové transakce Lake Lake pro přístup k datům, která už nejsou v tabulce. Odebráním možnosti verze 0 (nebo zadáním verze 1) se vám umožní znovu zobrazit novější data. Další informace najdete v tématu [dotazování staršího snímku tabulky](https://docs.delta.io/latest/delta-batch.html#deltatimetravel).

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Výsledky:

| ID|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

Tady vidíte, že jste se vrátili k nejstarší verzi dat.

## <a name="write-a-stream-of-data-to-a-table"></a>Zápis proudu dat do tabulky

Pomocí strukturovaného streamování Spark můžete také zapisovat do tabulky Delta Lake. Protokol rozdílového protokolu Lake transakcí garantuje právě jedno zpracování, i když existují jiné datové proudy nebo dávkové dotazy spuštěné v tabulce současně. Ve výchozím nastavení se streamy spouštějí v režimu připojení, který do tabulky přidává nové záznamy.

Další informace o integraci rozdílových Lake se strukturovaným streamem najdete v tématu [čtení a zápisy pomocí streamování tabulek](https://docs.delta.io/latest/delta-streaming.html).

V následujících buňkách:

* Buňka 30 – zobrazení nově připojených dat
* Kontrola historie v buňce 31
* Buňka 32 zastavení strukturované úlohy streamování
* Buňka 33 Kontrola historie < – všimnete si, že se připojení zastavila

Nejprve nastavíte jednoduchou úlohu streamování s datovým proudem Spark pro vygenerování sekvence a napíšete úlohu do tabulky Delta.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Načte datový proud změn z tabulky.

Zatímco Stream se zapisuje do tabulky rozdílových Lake, můžete si ho z této tabulky přečíst také jako zdroj streamování. Můžete například spustit jiný dotaz streamování, který vytiskne všechny změny provedené v rozdílových Lake tabulkách.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Výsledky:

| ID|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Výsledky:

|verze|          časové razítko|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|AKTUALIZACE STREAMOVÁNÍ|[outputMode-> Append, queryId-> d26b4f8a-7e5a-44f2-A5FB-23a7bd02aef7, epochId-> 0]|          4|
|      4|2020-04-25 00:36:27|           SLOUČENÍ|                                         [predikát-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predikát-> ["(( `id` % cast (2 jako bigint)) = přetypování (0 jako bigint))]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predikát-> ((ID # 744L% cast (2 jako bigint)) = přetypování (0 jako bigint))]|          1|
|      1|2020-04-25 00:35:05|           PSAL|                                               [Mode-> přepsání, partitionBy-> []]|          0|
|      0|2020-04-25 00:34:34|           PSAL|                                           [Mode-> ErrorIfExists, partitionBy-> []]|       null|

Tady přetahujete některé z méně zajímavých sloupců, které zjednodušují zobrazování zobrazení historie.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Výsledky:

|verze|          časové razítko|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|AKTUALIZACE STREAMOVÁNÍ|[outputMode-> Append, queryId-> d26b4f8a-7e5a-44f2-A5FB-23a7bd02aef7, epochId-> 0]|          4|
|      4|2020-04-25 00:36:27|           SLOUČENÍ|                                         [predikát-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predikát-> ["(( `id` % cast (2 jako bigint)) = přetypování (0 jako bigint))]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predikát-> ((ID # 744L% cast (2 jako bigint)) = přetypování (0 jako bigint))]|          1|
|      1|2020-04-25 00:35:05|           PSAL|                                               [Mode-> přepsání, partitionBy-> []]|          0|
|      0|2020-04-25 00:34:34|           PSAL|                                           [Mode-> ErrorIfExists, partitionBy-> []]|       null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Převést Parquet na rozdíl

Můžete provést místní převod z formátu Parquet na rozdíl.

Zde se otestujete, zda je stávající tabulka v rozdílovém formátu.
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Výsledky:

Ne

Nyní převedete data na formát Delta a ověříte, že pracovala.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Výsledky:

Ano

## <a name="sql-support"></a>Podpora SQL

Rozdíl podporuje příkazy tabulkového nástroje prostřednictvím SQL. SQL můžete použít k těmto akcím:

* Získat historii rozdílových objektu
* Vakuová a rozdílová
* Převést soubor Parquet na rozdíl

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Výsledky:

|verze|          časové razítko|userId|userName|       operation| operationParameters| úloha|poznámkový blok|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  null|    null|AKTUALIZACE STREAMOVÁNÍ|[outputMode-> přístupový bod...|null|    null|     null|          4|          null|         true|
|      4|2020-04-25 00:36:27|  null|    null|           SLOUČENÍ|[predikát-> (OL...|null|    null|     null|          3|          null|        false (nepravda)|
|      3|2020-04-25 00:36:08|  null|    null|          DELETE|[predikát-> ["(...|null|    null|     null|          2|          null|        false (nepravda)|
|      2|2020-04-25 00:35:51|  null|    null|          UPDATE|[predikát-> (i...|null|    null|     null|          1|          null|        false (nepravda)|
|      1|2020-04-25 00:35:05|  null|    null|           PSAL|[Mode-> Overwrit...|null|    null|     null|          0|          null|        false (nepravda)|
|      0|2020-04-25 00:34:34|  null|    null|           PSAL|[Mode-> ErrorIfE...|null|    null|     null|       null|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Výsledky:

|                program|
|--------------------|
|abfss://data@arca...|

Nyní se chystáte ověřit, že tabulka není rozdílová Formátová tabulka, pak ji převést na rozdílový formát pomocí Spark SQL a potvrdit, že byla správně převedena.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Výsledky:

Ano

Úplnou dokumentaci najdete na stránce s [dokumentací rozdílových Lake](https://docs.delta.io/latest/delta-intro.html) .

Další informace najdete v tématu [rozdílový projekt Lake](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Další kroky

* [Dokumentace k rozhraní .NET pro Apache Spark](/dotnet/spark)
* [Azure Synapse Analytics](../index.yml)