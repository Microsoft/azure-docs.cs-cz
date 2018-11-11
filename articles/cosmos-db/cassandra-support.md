---
title: Funkce a příkazy Apache Cassandra, které podporuje rozhraní API Cassandra pro Azure Cosmos DB
description: Informace o podpoře funkcí Apache Cassandra v rozhraní API Cassandra pro Azure Cosmos DB
services: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 5a12a65e03e7ac104eb7b09b116f7c463bbb5b98
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240711"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funkce Apache Cassandra, které podporuje rozhraní API Cassandra pro Azure Cosmos DB 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. S rozhraním API Cassandra pro Azure Cosmos DB můžete komunikovat prostřednictvím [přenosového protokolu](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) jazyka Cassandra Query Language (CQL) verze 4 kompatibilního s open source [ovladači](http://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) klienta Cassandra. 

Když použijete rozhraní API Cassandra pro Azure Cosmos DB, získáte nejen výhody rozhraní API pro Apache Cassandra, ale i podnikové funkce, které poskytuje Azure Cosmos DB. Mezi podnikové funkce patří [globální distribuce](distribute-data-globally.md), [automatické dělení škálování na více systémů](partition-data.md), záruky dostupnosti a latence, šifrování při nečinnosti, zálohování a mnoho dalšího.

## <a name="cassandra-protocol"></a>Protokol Cassandra 

Rozhraní API Cassandra pro Azure Cosmos DB je kompatibilní s CQL **verze v4**. Podporované příkazy CQL, nástroje, omezení a výjimky najdete níže. Jakýkoli ovladač klienta, který podporuje tyto protokoly, by se měl dokázat připojit k rozhraní API Cassandra pro Azure Cosmos DB.

## <a name="cassandra-driver"></a>Ovladač Cassandra

Rozhraní API Cassandra pro Azure Cosmos DB podporuje tyto verze ovladačů Cassandra:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [NodeJS 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>Datové typy CQL 

Rozhraní API Cassandra pro Azure Cosmos DB podporuje následující datové typy CQL:

* ascii  
* bigint  
* blob  
* Boolean  
* counter  
* date  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* text  
* time  
* časové razítko  
* timeuuid  
* tinyint  
* tuple  
* Uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>Funkce CQL

Rozhraní API Cassandra pro Azure Cosmos DB podporuje tyto funkce CQL:

* Podpisový  
* Funkce pro převod objektů blob 
  * typeAsBlob(value)  
  * blobAsType(value)
* UUID a funkce timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toTimestamp(timeuuid)  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toTimestamp(date)  
  * toTimestamp(date)  


## <a name="cassandra-query-language-limits"></a>Omezení jazyka Cassandra Query Language

Rozhraní API Cassandra pro Azure Cosmos DB nemá žádná omezení velikosti dat uložených v tabulce. Když se dodrží limity klíče oddílu, je možné uložit stovky terabajtů nebo petabajtů dat. Podobně žádný ekvivalent entity nebo řádku nemá žádná omezení počtu sloupců, ale celková velikost entity by neměla překročit 2 MB.

## <a name="tools"></a>Nástroje 

Rozhraní API Cassandra pro Azure Cosmos DB je platforma pro spravované služby. Ke správě clusteru nevyžaduje žádnou režii ani nástroje řízení, jako jsou například systém uvolňování paměti, Java Virtual Machine (JVM) a nodetool. Podporuje nástroje, jako je cqlsh, které využívá kompatibilitu Binary CQLv4. 

* Průzkumník dat portálu Azure Portal, metriky, diagnostika protokolů, PowerShell a rozhraní příkazového řádku jsou dalšími podporovanými mechanismy správy účtu.

## <a name="cql-shell"></a>CQL Shell  

Nástroj příkazového řádku CQLSH se dodává s Apache Cassandra 3.1.1 a funguje bez dalšího nastavování s následujícími povolenými proměnnými prostředí:

Před spuštěním následujících příkazů [přidejte kořenový certifikát Baltimore do úložiště cacerts](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store). 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```

## <a name="cql-commands"></a>Příkazy CQL

Azure Cosmos DB podporuje u účtů rozhraní API Cassandra následující databázové příkazy.

* CREATE KEYSPACE 
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH – podporují se jen neprotokolované příkazy. 
* DELETE

Všechny operace CRUD, pokud se spustí pomocí sady SDK kompatibilní s CQLV4, vrátí dodatečné informace o chybě, využitých jednotek žádosti a ID aktivity. Příkazy pro odstranění a aktualizaci je nutné zpracovávat s ohledem na správu zdrojů, aby se zabránilo nadměrnému využívání zřízených prostředků. 
* Poznámka: Pokud je zadaná hodnota gc_grace_seconds, musí být nulová.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            string value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mapování konzistence 

Rozhraní API Cassandra pro Azure Cosmos DB poskytuje volbu konzistence pro operace čtení. Všechny operace zápisu, bez ohledu na konzistenci účtu, se vždy zapíšou, tak, aby se dodržely smlouvy SLA o výkonu zápisu.

## <a name="permission-and-role-management"></a>Správa oprávnění a rolí

Azure Cosmos DB podporuje řízení přístupu na základě role (RBAC) a hesla/klíče pro čtení a zápis nebo jen pro čtení, které se dají získat prostřednictvím webu [Azure Portal](https://portal.azure.com). Azure Cosmos DB zatím nepodporuje uživatele a role pro aktivity v rovině dat. 

## <a name="planned-support"></a>Plánovaná podpora 
* Společné použití časového razítka a hodnoty TTL  
* V současné době se v příkazu create keyspace ignoruje název oblasti – distribuce dat se implementuje na základní platformě Cosmos DB a pro daný účet se zveřejňuje prostřednictvím portálu nebo PowerShellu. 





## <a name="next-steps"></a>Další kroky

- Začínáme s [vytvořením účtu rozhraní API Cassandra, databáze a tabulky](create-cassandra-api-account-java.md) prostřednictvím aplikace Java

