---
title: Migrace relačních dat 1:1 do Azure Cosmos DB SQL API
description: Naučte se zvládnout složitou migraci dat pro relace 1:1 do SQL API.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 53a3317f38cc22ffa3745f5f0e58cc01a54b825c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096744"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrace relačních dat 1:1 do Azure Cosmos DB účtu rozhraní SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Aby bylo možné migrovat z relační databáze na Azure Cosmos DB SQL API, může být nutné provést změny v datovém modelu pro účely optimalizace.

Jednou z běžných transformací je denormalizace dat vložením souvisejících podpoložky do jednoho dokumentu JSON. Tady se podíváme na několik možností použití Azure Data Factory nebo Azure Databricks. Obecné pokyny k modelování dat pro Cosmos DB najdete [v Azure Cosmos DB modelování dat](modeling-data.md).  

## <a name="example-scenario"></a>Ukázkový scénář

Předpokládejme, že máme v naší databázi SQL, objednávkách a OrderDetails následující dvě tabulky.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Snímek obrazovky zobrazující tabulky Orders a OrderDetails v databázi SQL" border="false" :::

Chceme během migrace kombinovat tento vztah 1:1 do jednoho dokumentu JSON. K tomu můžeme vytvořit dotaz T-SQL pomocí "pro JSON", jak je uvedeno níže:

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

Výsledky tohoto dotazu by vypadaly takto: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Podrobnosti objednávky" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

V ideálním případě chcete použít aktivitu kopírování s jednou Azure Data Factory (ADF) k dotazování dat SQL jako zdroje a zapsat výstup přímo do Azure Cosmos DB jímky jako správné objekty JSON. V současné době není možné provést nezbytnou transformaci JSON v jedné aktivitě kopírování. Pokud se pokusíte zkopírovat výsledky výše uvedeného dotazu do kontejneru rozhraní SQL API Azure Cosmos DB, zobrazí se pole OrderDetails jako řetězcová vlastnost našeho dokumentu namísto očekávaného pole JSON.

Toto současné omezení můžeme obejít jedním z následujících způsobů:

* **Použít Azure Data Factory se dvěma aktivitami kopírování**: 
  1. Získat data ve formátu JSON z SQL do textového souboru v umístění zprostředkujícího úložiště objektů BLOB a 
  2. Načtěte data z textového souboru JSON do kontejneru v Azure Cosmos DB.

* **Pomocí Azure Databricks můžete číst z SQL a zapisovat do Azure Cosmos DB** – tady se zobrazí dvě možnosti.


Pojďme se podívat na tyto přístupy podrobněji:

## <a name="azure-data-factory"></a>Azure Data Factory

I když do cílového Cosmos DB dokumentu nemůžeme vložit OrderDetails jako pole JSON, můžeme tento problém obejít pomocí dvou samostatných aktivit kopírování.

### <a name="copy-activity-1-sqljsontoblobtext"></a>#1 aktivity kopírování: SqlJsonToBlobText

Pro zdrojová data použijeme dotaz SQL k získání sady výsledků jako jednoho sloupce s jedním objektem JSON (reprezentujícím pořadí) na řádek pomocí SQL Server OPENJSON a možnosti cesty JSON:

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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="Kopie ADF":::


V případě jímky aktivity kopírování SqlJsonToBlobText vybíráme "text s oddělovači" a Navedeme ho na konkrétní složku v Azure Blob Storage pomocí dynamicky generovaného jedinečného názvu souboru (například @concat (kanálu (). RunId, '. JSON ').
Vzhledem k tomu, že náš textový soubor není ve skutečnosti "oddělen" a nechcete, aby se analyzoval na samostatné sloupce pomocí čárek a chcete zachovat dvojité uvozovky ("), nastavíme" Oddělovač sloupců "na kartu (" \t ") – nebo jiný znak, který se nevyskytuje v datech-a" znak citace "na" bez znaku uvozovek ".

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Snímek obrazovky, který zvýrazňuje nastavení oddělovače sloupců a znaků uvozovek.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>#2 aktivity kopírování: BlobJsonToCosmos

V dalším kroku provedeme úpravu kanálu ADF přidáním druhé aktivity kopírování, která vypadá v Azure Blob Storage pro textový soubor, který byl vytvořen první aktivitou. Zpracuje ho jako zdroj "JSON", který se má vložit do Cosmos DB jímka jako jeden dokument na řádek JSON, který se nachází v textovém souboru.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Snímek obrazovky, který zvýrazní zdrojový soubor JSON a pole cesta k souboru.":::

Volitelně taky do kanálu přidáme aktivitu "odstranit", aby se před každým spuštěním odstranily všechny předchozí soubory ve složce/Orders/. Náš kanál ADF teď vypadá nějak takto:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Snímek obrazovky, který zvýrazní aktivitu odstranění.":::

Po aktivaci kanálu výše se zobrazí soubor vytvořený v naší zprostředkující službě Azure Blob Storage umístění, které obsahuje jeden objekt JSON pro každý řádek:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Snímek obrazovky zobrazující vytvořený soubor, který obsahuje objekty JSON.":::

V naší kolekci Cosmos DB se také v objednávkách objednávek zobrazují správně vložené OrderDetails:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Snímek obrazovky zobrazující podrobnosti objednávky jako součást dokumentu Cosmos DB":::


## <a name="azure-databricks"></a>Azure Databricks

Spark v [Azure Databricks](https://azure.microsoft.com/services/databricks/) můžeme také použít ke kopírování dat z našeho SQL Databaseho zdroje do Azure Cosmos DBho cíle bez vytvoření zprostředkujícího textu nebo souborů JSON v BLOB Storage Azure. 

> [!NOTE]
> Pro přehlednost a jednoduchost níže uvedené fragmenty kódu zahrnují fiktivní databázová hesla explicitně vložená, ale měli byste je vždycky používat Azure Databricks tajných klíčů.
>

Nejprve vytvoříme a připojíte požadované knihovny [SQL Connector](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) a [Azure Cosmos DB konektoru](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) do našeho clusteru Azure Databricks. Restartujte cluster, abyste se ujistili, že jsou knihovny načteny.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Snímek obrazovky, který ukazuje, kde vytvořit a připojit požadované konektory SQL a knihovny Azure Cosmos DB konektoru do našeho clusteru Azure Databricks.":::

Dále pro Scala a Python máme dvě ukázky. 

### <a name="scala"></a>Scala
Tady získáte výsledky dotazu SQL s výstupem "FOR JSON" do datového rámce:

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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Snímek obrazovky, který zobrazuje výstup dotazu SQL v dataframe.":::

V dalším kroku se připojíme k naší Cosmos DB databázi a kolekci:

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

Nakonec definujeme naše schéma a použijeme from_json k aplikování datového rámce před jeho uložením do kolekce CosmosDB.

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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Snímek obrazovky, který zvýrazní správné pole pro uložení do kolekce Cosmos DB.":::


### <a name="python"></a>Python

V rámci alternativního přístupu možná budete muset v Sparku spustit transformace JSON (Pokud zdrojová databáze nepodporuje "FOR JSON" nebo podobnou operaci), nebo můžete chtít použít paralelní operace pro velmi velkou datovou sadu. Tady máme ukázku PySpark. Začněte konfigurací připojení zdrojové a cílové databáze v první buňce:

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

Pak povedeme dotazování zdrojové databáze (v tomto případě SQL Server) pro záznamy podrobností objednávky i objednávky, a to vložením výsledků do Spark dataframes. Vytvoříme také seznam obsahující všechna ID objednávek a fond vláken pro paralelní operace:

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

Pak vytvořte funkci pro zápis objednávek do cílové kolekce rozhraní SQL API. Tato funkce vyfiltruje podrobnosti o všech objednávkách pro dané ID objednávky, převede je na pole JSON a vloží pole do dokumentu JSON, který zapíšeme do cílové kolekce rozhraní SQL API pro tuto objednávku:

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

Nakonec budeme volat výše uvedené pomocí funkce mapy ve fondu vláken, aby se prováděly paralelně a prošly seznamem ID objednávek, které jsme vytvořili dříve:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
V obou případech by měl být na konci správně uložen vložený OrderDetails v rámci každého dokumentu objednávky v kolekci Cosmos DB:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Další kroky
* Další informace o [modelování dat v Azure Cosmos DB](./modeling-data.md)
* Naučte [se modelovat data a rozdělit je na Azure Cosmos DB](./how-to-model-partition-example.md)