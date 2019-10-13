---
title: Funkce a příkazy Apache Cassandra, které podporuje rozhraní API Cassandra pro Azure Cosmos DB
description: Informace o podpoře funkcí Apache Cassandra v rozhraní API Cassandra pro Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 0dcca2175d6ccc35a51bccb1e47f75d25cb8b11f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299184"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funkce Apache Cassandra, které podporuje rozhraní API Cassandra pro Azure Cosmos DB 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. S rozhraním API Cassandra pro Azure Cosmos DB můžete komunikovat prostřednictvím [přenosového protokolu](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) jazyka Cassandra Query Language (CQL) verze 4 kompatibilního s open source [ovladači](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) klienta Cassandra. 

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
* Agregační funkce
  * minimum, maximum, prům. Count
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
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>Příkazy CQL

Azure Cosmos DB podporuje u účtů rozhraní API Cassandra následující databázové příkazy.

* VYTVOŘIT prostor (nastavení replikace pro tento příkaz se ignoruje, systém použije základní [model replikace Azure Cosmos DB](global-dist-under-the-hood.md). Pokud potřebujete data mezi oblastmi, můžete ji povolit na úrovni účtu pomocí PowerShellu, rozhraní příkazového řádku nebo portálu. Další informace najdete v článku [jak přidat nebo odebrat oblasti pro svůj účet](how-to-manage-database-account.md#addremove-regions-from-your-database-account) .
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* AKTUALIZACE 
* BATCH – podporují se jen neprotokolované příkazy. 
* DELETE

Všechny operace CRUD, když se spustí prostřednictvím sady SDK kompatibilní s CQLV4, vrátí další informace o chybě, spotřebované jednotky žádosti. Příkazy DELETE a Update je potřeba zpracovat s ohledem na dodržování zásad správného řízení prostředků, aby nedocházelo ke správnému používání zřízené propustnosti. 
* Poznámka: Pokud je zadaná hodnota gc_grace_seconds, musí být nulová.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mapování konzistence 

Rozhraní API Cassandra pro Azure Cosmos DB poskytuje volbu konzistence pro operace čtení.  Mapování konzistence je popsáno [zde [(https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping).

## <a name="permission-and-role-management"></a>Správa oprávnění a rolí

Azure Cosmos DB podporuje řízení přístupu na základě role (RBAC) pro zřizování, otáčení klíčů, zobrazení metrik a hesla pro čtení a zápis a klíče nebo klíče, které lze získat prostřednictvím [Azure Portal](https://portal.azure.com). Azure Cosmos DB nepodporuje role pro aktivity CRUD. 

## <a name="keyspace-and-table-options"></a>Možnosti prostoru klíčů a tabulek

V současné době se v současnosti ignorují možnosti název oblasti, třída, replication_factor, datacentrum v příkazu vytvořit prostor pro místo. Pokud přidáte požadované oblasti, systém používá [globální distribuci](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) Azure Cosmos DB základní distribuce. Pokud potřebujete data mezi oblastmi, můžete ji povolit na úrovni účtu pomocí PowerShellu, CLI nebo portálu. Další informace najdete v tomto dokumentu: https://docs.microsoft.com/en-us/azure/cosmos-db/how-to-manage-database-account#addremove-regions-from-your-database-account. Durable_writes nejde zakázat – protože Cosmos DB zajišťuje, že každý zápis je trvalý. V každé oblasti Cosmos DB replikuje data napříč REPLICASET tvořenými 4 replikami a tato [Konfigurace](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) REPLICASET se nedá změnit. Všechny možnosti vytváření tabulek se ignorují, s výjimkou gc_grace_seconds, které by měly mít nulovou hodnotu.
Místo na úrovni a v tabulce je potřeba mít možnost cosmosdb_provisioned_throughput s minimální hodnotou 400. Propustnost místa na místě umožňuje sdílení propustnosti napříč několika tabulkami a hodí se pro scénáře, kdy všechny tabulky nevyužívají propustnost. ALTER TABLE umožňuje změnit zřízenou propustnost napříč oblastmi. VYTVOŘIT sampleks PROSTORů s REPLIKACÍ = {' class ': ' SimpleStrategy '} a cosmosdb_provisioned_throughput = 2000;  
CREATE TABLE sampleks. T1 (user_id int PRIMARY KEY, LastName text) s cosmosdb_provisioned_throughput = 2000; ALTER TABLE gks1. T1 s cosmosdb_provisioned_throughput = 10000;

## <a name="usage-of-cassandra-retry-connection-policy"></a>Použití zásad připojení Cassandra opakování

Azure Cosmos DB je systém řídí prostředky. To znamená, že můžete provést určitý počet operací v určité druhé omezené zřízené propustností na základě jednotek žádostí spotřebovaných operacemi. Pokud aplikace překročí tento limit, bude vyvolána výjimka s omezením míry požadavků druhé žádosti. Cosmos DB rozhraní API Cassandra překládá tyto výjimky na přetížené chyby v nativním protokolu Cassandra. Chcete-li zajistit, aby aplikace mohla zachytit a provést opakování pro omezení četnosti, poskytne se podpora [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) a [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) . Pokud k přístupu rozhraní API Cassandra Cosmos DB používáte jiné sady SDK, vytvořte prosím zásady připojení a pokuste se o tyto výjimky znovu. 

## <a name="next-steps"></a>Další kroky

- Začínáme s [vytvořením účtu rozhraní API Cassandra, databáze a tabulky](create-cassandra-api-account-java.md) prostřednictvím aplikace Java

