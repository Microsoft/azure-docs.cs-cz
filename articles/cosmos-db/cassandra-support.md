---
title: Funkce Apache Cassandra, které podporuje rozhraní API Cassandra pro Azure Cosmos DB
description: Informace o podpoře funkcí Apache Cassandra v rozhraní API Cassandra pro Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 0a2ace3f73379cff0b9289a8cebb10cb7930348d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240237"
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
  * min, max, avg, počet
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
  


## <a name="cassandra-api-limits"></a>Omezení rozhraní API Cassandra

Rozhraní API Cassandra pro Azure Cosmos DB nemá žádná omezení velikosti dat uložených v tabulce. Když se dodrží limity klíče oddílu, je možné uložit stovky terabajtů nebo petabajtů dat. Podobně každá entita nebo ekvivalent řádku nemá žádné omezení počtu sloupců. Celková velikost entity by však neměla překročit 2 MB. Data na klíč oddílu nesmí překročit 20 GB jako ve všech ostatních souborech API.

## <a name="tools"></a>Nástroje 

Rozhraní API Cassandra pro Azure Cosmos DB je platforma pro spravované služby. Ke správě clusteru nevyžaduje žádnou režii ani nástroje řízení, jako jsou například systém uvolňování paměti, Java Virtual Machine (JVM) a nodetool. Podporuje nástroje, jako je cqlsh, které využívá kompatibilitu Binary CQLv4. 

* Průzkumník dat na webu Azure Portal, metriky, diagnostika protokolů, PowerShell a rozhraní příkazového příkazového příkazu jsou další podporované mechanismy pro správu účtu.

## <a name="cql-shell"></a>CQL Shell  

Nástroj příkazového řádku CQLSH je dodáván s Apache Cassandra 3.1.1 a funguje impozařně nastavením některých proměnných prostředí.

**Windows:**

Pokud používáte windows, doporučujeme povolit [souborový systém Windows pro Linux](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Poté můžete sledovat níže uvedené příkazy linuxu.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>Příkazy CQL

Azure Cosmos DB podporuje u účtů rozhraní API Cassandra následující databázové příkazy.

* VYTVOŘIT KEYSPACE (Nastavení replikace pro tento příkaz jsou ignorovány)
* CREATE TABLE 
* VYTVOŘIT INDEX (bez zadání názvu indexu a úplné zmrazené indexy ještě nejsou podporovány)
* POVOLIT FILTROVÁNÍ
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH – podporují se jen neprotokolované příkazy. 
* DELETE

Všechny operace CRUD, které jsou prováděny prostřednictvím sady SDK kompatibilní s cql v4, vrátí další informace o spotřebovaných chybových jednotkách a jednotkách požadavků. Příkazy DELETE a UPDATE by měly být zpracovány s ohledem na zásady správného řízení prostředků, aby bylo zajištěno co nejefektivnější využití zřízené propustnosti.

* Poznámka: Pokud je zadaná hodnota gc_grace_seconds, musí být nulová.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Mapování konzistence 

Rozhraní API Cassandra pro Azure Cosmos DB poskytuje volbu konzistence pro operace čtení.  Mapování konzistence je podrobně [popsáno zde](consistency-levels-across-apis.md#cassandra-mapping).

## <a name="permission-and-role-management"></a>Správa oprávnění a rolí

Azure Cosmos DB podporuje řízení přístupu na základě rolí (RBAC) pro zřizování, otáčení klíčů, zobrazení metrik a hesla/klíče jen pro čtení a jen pro čtení, které lze získat prostřednictvím [portálu Azure](https://portal.azure.com). Azure Cosmos DB nepodporuje role pro aktivity CRUD.

## <a name="keyspace-and-table-options"></a>Možnosti klíčového prostoru a tabulky

Možnosti pro název oblasti, třídy, replication_factor a datové centrum v příkazu "Vytvořit keyspace" jsou aktuálně ignorovány. Systém používá základní metodu [replikace globální distribuce](global-dist-under-the-hood.md) azure cosmos DB k přidání oblastí. Pokud potřebujete přítomnost dat mezi oblastmi, můžete povolit na úrovni účtu s PowerShell, CLI nebo portál, chcete-li se dozvědět více, [přečtěte si článek jak přidat oblasti.](how-to-manage-database-account.md#addremove-regions-from-your-database-account) Durable_writes nelze zakázat, protože Azure Cosmos DB zajišťuje, že každý zápis je trvalý. V každé oblasti Azure Cosmos DB replikuje data v celé sadě replik, která se skládá ze čtyř replik a tuto [konfiguraci](global-dist-under-the-hood.md) sady replik nelze změnit.
 
Všechny možnosti jsou ignorovány při vytváření tabulky, s výjimkou gc_grace_seconds, který by měl být nastaven na nulu.
Keyspace a tabulka mají další možnost s názvem "cosmosdb_provisioned_throughput" s minimální hodnotou 400 RU/s. Propustnost Keyspace umožňuje sdílení propustnosti ve více tabulkách a je užitečné pro scénáře, kdy všechny tabulky nejsou s využitím zřízená propustnost. Příkaz Změnit tabulku umožňuje změnu zřízené propustnosti napříč oblastmi. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Používání zásad opakování připojení Cassandra

Azure Cosmos DB je systém řízený prostředky. To znamená, že můžete provést určitý počet operací v dané sekundě na základě jednotek požadavku spotřebovaných operacemi. Pokud aplikace překročí tento limit v dané sekundě, požadavky jsou omezeny a výjimky budou vyvolány. Rozhraní CASSSandra API v Azure Cosmos DB překládá tyto výjimky na přetížené chyby v nativním protokolu Cassandra. Chcete-li zajistit, že vaše aplikace může zachytit a opakovat požadavky v případě omezení sazby, [jiskra](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) a rozšíření [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) jsou k dispozici. Pokud používáte jiné sady SDK pro přístup k rozhraní CASSANDRA API v Azure Cosmos DB, vytvořte zásady připojení opakovat na tyto výjimky.

## <a name="next-steps"></a>Další kroky

- Začínáme s [vytvořením účtu rozhraní API Cassandra, databáze a tabulky](create-cassandra-api-account-java.md) prostřednictvím aplikace Java

