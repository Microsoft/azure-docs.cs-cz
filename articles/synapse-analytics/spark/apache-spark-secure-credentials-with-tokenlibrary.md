---
title: Zabezpečení přihlašovacích údajů pro přístup k propojeným službám v Apache Spark pro Azure synapse Analytics
description: Tento článek poskytuje základní informace o tom, jak bezpečně integrovat Apache Spark pro Azure synapse Analytics s dalšími službami pomocí propojených služeb a knihovny tokenů.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: d542e6ef784d763e406aad28231431cbc382fbfd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450917"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>Zabezpečení přihlašovacích údajů prostřednictvím propojených služeb pomocí TokenLibrary
Přístup k datům z externích zdrojů je běžným vzorem. Pokud externí zdroj dat nepovoluje anonymní přístup, je nutné zabezpečit připojení pomocí přihlašovacích údajů, tajného klíče nebo připojovacího řetězce.  

Služba Azure synapse Analytics poskytuje propojené služby, které usnadňují proces integrace tím, že ukládá podrobnosti o připojení do propojené služby nebo Azure Key Vault. Po vytvoření propojené služby může Apache Spark odkazovat na propojenou službu a použít tak informace o připojení ve vašem kódu. 

Další informace najdete v tématu [propojené služby](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
> [!NOTE]
> Přístup k souborům z Azure Data Lake Storage v pracovním prostoru používá pro ověřování průchozí služby AAD, takže nebudete muset používat TokenLibrary. 


## <a name="prerequisite"></a>Požadavek
* Propojená služba – musíte vytvořit propojenou službu s externím zdrojem dat a odkazovat na propojenou službu z knihovny tokenů. Přečtěte si další informace o [propojených službách](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Připojení k ADLS Gen2 mimo pracovní prostor synapse

Synapse poskytuje prostředí integrovaných propojených služeb pro Azure Data Lake Storage Gen2.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>Použití knihovny tokenů

Pokud se chcete připojit k jiným propojeným službám, můžete vytvořit přímé volání TokenLibrary.

### <a name="getconnectionstring"></a>GetConnectionString
 Chcete-li načíst připojovací řetězec, použijte funkci <b>GetConnectionString</b> a předejte <b>název propojené služby</b>.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Chcete-li analyzovat konkrétní hodnoty z dvojice <i>klíč = hodnota</i> v připojovacím řetězci, například 

<i>DefaultEndpointsProtocol = https; Účet Account = \<AccountName> ; AccountKey =\<AccountKey></i>

použijte funkci <b>getConnectionStringAsMap</b> a předejte klíč k vrácení hodnoty.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>Další kroky

- [Zapsat do vyhrazeného fondu SQL](./synapse-spark-sql-pool-import-export.md)

