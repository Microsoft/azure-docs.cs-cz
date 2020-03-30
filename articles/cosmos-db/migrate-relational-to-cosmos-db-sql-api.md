---
title: Migrace relačních dat 1:1 do několika relačních dat do rozhraní SQL API Azure Cosmos DB
description: Zjistěte, jak zpracovat komplexní migraci dat pro relace 1:1 do sql api
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896633"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrace 1:1-n. relačních dat do účtu SQL API Azure Cosmos DB

Chcete-li migrovat z relační databáze do rozhraní Azure Cosmos DB SQL API, může být nutné provést změny datového modelu pro optimalizaci.

Jednou z běžných transformací je denormalizace dat vložením souvisejících podpoložek do jednoho dokumentu JSON. Tady se podíváme na několik možností, které pro topomocí Azure Data Factory nebo Azure Databricks. Obecné pokyny k modelování dat pro Cosmos DB najdete v přehledech [modelování dat v Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Ukázkový scénář

Předpokládejme, že máme následující dvě tabulky v naší databázi SQL, Objednávky a OrderDetails.


![Podrobnosti objednávky](./media/migrate-relational-to-cosmos-sql-api/orders.png)

Chceme tento vztah 1:10.000 do jednoho dokumentu JSON během migrace. Chcete-li to provést, můžeme vytvořit T-SQL dotaz pomocí "FOR JSON", jak je uvedeno níže:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Výsledky tohoto dotazu by vypadat takto: 

![Podrobnosti objednávky](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


V ideálním případě chcete použít jednu aktivitu kopírování Azure Data Factory (ADF) k dotazování dat SQL jako zdroj a zápis výstupu přímo do jímky Azure Cosmos DB jako správné objekty JSON. V současné době není možné provést potřebnou transformaci JSON v jedné aktivitě kopírování. Pokud se pokusíme zkopírovat výsledky výše uvedeného dotazu do kontejneru rozhraní SQL API Azure Cosmos DB, zobrazí se pole OrderDetails jako vlastnost řetězce našeho dokumentu namísto očekávaného pole JSON.

Můžeme obejít toto aktuální omezení jedním z následujících způsobů:

* **Azure Data Factory se dvěma aktivitami kopírování:** 
  1. Získejte data ve formátu JSON z SQL do textového souboru ve zprostředkujícím umístění úložiště objektů blob a 
  2. Načtěte data z textového souboru JSON do kontejneru v Azure Cosmos DB.

* **Pomocí Azure Databricks číst z SQL a zapisovat do Azure Cosmos DB** – tady naem naem budeme prezentovat dvě možnosti.


Podívejme se na tyto přístupy podrobněji:

## <a name="azure-data-factory"></a>Azure Data Factory

Přestože nelze vložit OrderDetails jako JSON pole v dokumentu cílové Cosmos DB, můžeme tento problém vyřešit pomocí dvou samostatných aktivit copy.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Aktivita kopírování #1: SqlJsonToBlobText

Pro zdrojová data používáme dotaz SQL k získání sady výsledků jako jednoho sloupce s jedním objektem JSON (představujícím Order) na řádek pomocí funkcí SQL Server OPENJSON a FOR JSON PATH:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![Kopie adf](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


Pro jímky sqljsonToblobText kopírovat aktivity, zvolíme "Oddělovač textu" a přejděte na konkrétní složku v úložišti objektů@concatblob Azure s dynamicky generovaný jedinečný název souboru (například ' (pipeline(). RunId,'.json').
Vzhledem k tomu, že náš textový soubor není ve skutečnosti "oddělený" a nechceme, aby byl analyzován do samostatných sloupců pomocí čárek a chceme zachovat dvojité uvozovky ("), nastavíme "Oddělovač sloupců" na kartu ("\t") - nebo jiný znak, který se nevyskytuje v datech - a "Znak citace" na "Žádný znak uvozovky".

![Kopie adf](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>Aktivita kopírování #2: BlobJsonToCosmos

Dále upravíme náš kanál ADF přidáním druhé aktivity kopírování, která vypadá v úložišti objektů Blob Azure pro textový soubor, který byl vytvořen první aktivitou. Zpracovává jej jako "JSON" zdroj vložit do dřezu Cosmos DB jako jeden dokument na řádek JSON nalezené v textovém souboru.

![Kopie adf](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

Volitelně také přidáme aktivitu "Odstranit" do kanálu tak, aby odstranila všechny předchozí soubory zbývající ve složce /Orders/ před každým spuštěním. Náš kanál ADF nyní vypadá nějak takto:

![Kopie adf](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

Poté, co spustíme kanál výše, uvidíme soubor vytvořený v našem zprostředkovatelském umístění úložiště objektů blob Azure obsahující jeden objekt JSON na řádek:

![Kopie adf](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

Vidíme také objednávky dokumenty s správně vložené OrderDetails vložené do naší kolekce Cosmos DB:

![Kopie adf](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

Spark v Azure [Databricks](https://azure.microsoft.com/services/databricks/) můžeme taky použít ke zkopírování dat z našeho zdroje databáze SQL do cíle Azure Cosmos DB bez nutnosti vytváření zprostředkujících textových/JSON souborů ve službě Azure Blob Storage. 

> [!NOTE]
> Pro přehlednost a jednoduchost, fragmenty kódu níže obsahují fiktivní hesla databáze explicitně vložkou, ale vždy byste měli používat tajné kódy Azure Databricks.
>

Nejprve vytvoříme a připojíme požadované [sql konektor](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) a knihovny [konektorů Azure Cosmos DB](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) do našeho clusteru Azure Databricks. Restartujte cluster a ujistěte se, že jsou načteny knihovny.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

Dále uvádíme dva ukázky, pro Scala a Python. 

### <a name="scala"></a>Scala
Zde získáme výsledky dotazu SQL s výstupem "FOR JSON" do datového rámce:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

Dále se připojujeme k naší databázi Cosmos DB a kolekci:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Nakonec definujeme naše schéma a použít from_json použít DataFrame před uložením do kolekce CosmosDB.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

Jako alternativní přístup může být nutné provést transformace JSON v Spark (pokud zdrojová databáze nepodporuje "FOR JSON" nebo podobnou operaci), nebo můžete chtít použít paralelní operace pro velmi velké datové sady. Zde uvádíme vzorek PySpark. Začněte konfigurací připojení zdrojové a cílové databáze v první buňce:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Potom budeme dotaz zdrojové databáze (v tomto případě SQL Server) pro záznamy podrobností pořadí a pořadí, uvedení výsledků do datových rámců Spark. Vytvoříme také seznam obsahující všechna ID objednávky a fond vláken pro paralelní operace:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Potom vytvořte funkci pro zápis objednávky do kolekce rozhraní SQL API. Tato funkce vyfiltruje všechny podrobnosti objednávky pro dané ID objednávky, převede je na pole JSON a vloží pole do dokumentu JSON, který zapíšeme do cílové kolekce rozhraní SQL API pro toto pořadí:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Nakonec budeme volat výše pomocí funkce mapy ve fondu vláken, aby se provedla paralelně, předávání v seznamu ID objednávky jsme vytvořili dříve:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
V obou přístupů, na konci bychom měli získat správně uloženy vložené OrderDetails v rámci každé objednávky dokumentu v kolekci Cosmos DB:

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>Další kroky
* Informace o [modelování dat v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* [Zjistěte, jak modelovat a rozdělovat data v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)
