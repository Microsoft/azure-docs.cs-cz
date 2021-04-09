---
title: Přihlašovací údaje zabezpečeného přístupu k propojeným službám v Apache Spark pro Azure synapse Analytics
description: Tento článek poskytuje základní informace o tom, jak bezpečně integrovat Apache Spark pro Azure synapse Analytics s dalšími službami pomocí propojených služeb a knihovny tokenů.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693573"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>Zabezpečení přihlašovacích údajů pomocí propojených služeb pomocí TokenLibrary

Přístup k datům z externích zdrojů je běžným vzorem. Pokud externí zdroj dat nepovoluje anonymní přístup, je nutné zabezpečit připojení pomocí přihlašovacích údajů, tajného klíče nebo připojovacího řetězce.  

Synapse ve výchozím nastavení používá pro ověřování mezi prostředky průchozí průchod Azure Active Directory (AAD).  Pokud se potřebujete připojit k prostředku pomocí jiných přihlašovacích údajů, použijte TokenLibrary přímo.  TokenLibrary zjednodušuje proces načítání tokenů SAS, tokenů AAD, připojovacích řetězců a tajných klíčů uložených v propojené službě nebo v Azure Key Vault.

Při načítání tajných kódů z Azure Key Vault doporučujeme vytvořit propojenou službu pro vaši Azure Key Vault.  Ujistěte se, že identita spravované služby synapse pracovního prostoru (MSI) má tajný klíč získat oprávnění pro váš Azure Key Vault.  Synapse se ověří pro Azure Key Vault pomocí identity spravované služby synapse pracovního prostoru. Pokud se připojujete přímo k Azure Key Vault bez propojené služby, ověří se pomocí přihlašovacích údajů uživatele Azure Active Directory.

Další informace najdete v tématu [propojené služby](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="usage"></a>Využití

### <a name="tokenlibraryhelp"></a>TokenLibrary. Help ()
Tato funkce zobrazí nápovědu k TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>TokenLibrary pro Azure Data Lake Storage Gen2

#### <a name="adls-gen2-primary-storage"></a>ADLS Gen2 primární úložiště

Přístup k souborům z primárního Azure Data Lake Storage používá ve výchozím nastavení Azure Active Directory průchozí ověřování a nevyžaduje explicitní použití TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>ADLS Gen2 úložiště s propojenými službami

Synapse poskytuje integrované prostředí propojené služby při připojování k Azure Data Lake Storage Gen2.  Propojené služby je možné nakonfigurovat tak, aby se ověřovaly pomocí **klíče účtu**, **instančního objektu**, **spravované identity** nebo **přihlašovacích údajů**.

Pokud je metoda ověřování propojené služby nastavená na **klíč účtu**, propojí se tato propojená služba s použitím poskytnutého klíče účtu úložiště, vyžádá si klíč SAS a automaticky ho použije pro požadavek úložiště pomocí **LinkedServiceBasedSASProvider**.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

Pokud je metoda ověřování propojené služby nastavená na **spravovanou identitu** nebo **instanční objekt**, propojená služba bude používat spravovanou identitu nebo token instančního objektu u poskytovatele **LinkedServiceBasedTokenProvider** .  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>ADLS Gen2 Storage (bez propojených služeb)

Připojte se k ADLS Gen2 úložišti přímo pomocí klíče SAS, použijte **ConfBasedSASProvider** a zadejte klíč SAS do nastavení konfigurace **Spark. Storage. synapse. SAS** .

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>ADLS Gen2 úložiště s Azure Key Vault

Připojte se k ADLS Gen2 úložiště pomocí tokenu SAS uloženého v Azure Key Vault tajného klíče.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>TokenLibrary pro další propojené služby

Pokud se chcete připojit k jiným propojeným službám, můžete vytvořit přímé volání TokenLibrary.

#### <a name="getconnectionstring"></a>GetConnectionString ()

 Chcete-li načíst připojovací řetězec, použijte funkci **GetConnectionString** a předejte **název propojené služby**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

GetConnectionStringAsMap je pomocná funkce, která je k dispozici v Scala a Pythonu k analýze konkrétních hodnot z dvojice _klíč = hodnota_ v připojovacím řetězci, jako je například

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

použijte funkci **getConnectionStringAsMap** a předejte klíč k vrácení hodnoty.  V příkladu výše uvedeného připojovacího řetězce 

_**TokenLibrary. getConnectionStringAsMap ("DefaultEndpointsProtocol")**_

Vrátí

**_https_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getsecret ()

Pokud chcete načíst tajný kód uložený z Azure Key Vault, doporučujeme vytvořit propojenou službu, která se Azure Key Vault v pracovním prostoru synapse. Identitě spravované služby synapse pracovního prostoru bude potřeba udělit oprávnění **získat** tajná práva k Azure Key Vault.  Propojená služba bude používat identitu spravované služby pro připojení k Azure Key Vault službě pro načtení tajného klíče.  V opačném případě se přímé připojení k Azure Key Vault použijí přihlašovací údaje uživatele v Azure Active Directory (AAD).  V takovém případě bude nutné uživateli udělit oprávnění získat tajný kód v Azure Key Vault.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

K načtení tajného kódu z Azure Key Vault použijte funkci **TokenLibrary. getsecret ()** .

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Další kroky

- [Zapsat do vyhrazeného fondu SQL](./synapse-spark-sql-pool-import-export.md)
