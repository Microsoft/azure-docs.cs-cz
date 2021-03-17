---
title: Funkce Apache Cassandra, které podporuje rozhraní API Cassandra pro Azure Cosmos DB
description: Informace o podpoře funkcí Apache Cassandra v rozhraní API Cassandra pro Azure Cosmos DB
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f48935edd5746b0f3f3440e249024cbe2901317b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374367"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funkce Apache Cassandra, které podporuje rozhraní API Cassandra pro Azure Cosmos DB 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete komunikovat s Azure Cosmos DB rozhraní API Cassandra prostřednictvím klientských [ovladačů](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)Open-Source Cassandra kompatibilního s [protokolem](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) CQL Binary Protocol v4. 

Když použijete rozhraní API Cassandra pro Azure Cosmos DB, získáte nejen výhody rozhraní API pro Apache Cassandra, ale i podnikové funkce, které poskytuje Azure Cosmos DB. Mezi podnikové funkce patří [globální distribuce](distribute-data-globally.md), [automatické dělení škálování na více systémů](cassandra-partitioning.md), záruky dostupnosti a latence, šifrování při nečinnosti, zálohování a mnoho dalšího.

## <a name="cassandra-protocol"></a>Protokol Cassandra 

Rozhraní API Cassandra Azure Cosmos DB je kompatibilní s rozhraním API CQL (Cassandra Query Language) v systému 3.11 (zpětně kompatibilní s verzí 2. x). Podporované příkazy CQL, nástroje, omezení a výjimky najdete níže. Jakýkoli ovladač klienta, který podporuje tyto protokoly, by se měl dokázat připojit k rozhraní API Cassandra pro Azure Cosmos DB.

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

|Typ  |Podporováno |
|---------|---------|
| ascii  | Yes |
| bigint  | Yes |
| blob  | Yes |
| boolean  | Yes |
| counter  | Yes |
| date  | Yes |
| decimal  | Yes |
| double  | Yes |
| float  | Yes |
| frozen  | Yes |
| inet  | Yes |
| int  | Yes |
| list  | Yes |
| set  | Yes |
| smallint  | Yes |
| text  | Yes |
| time  | Yes |
| časové razítko  | Yes |
| timeuuid  | Yes |
| tinyint  | Yes |
| tuple  | Yes |
| uuid  | Yes |
| varchar  | Yes |
| varint  | Yes |
| tuples | Yes | 
| udts  | Yes |
| mapa | Yes |

Pro deklaraci datového typu je podporovaná statická.

## <a name="cql-functions"></a>Funkce CQL

Rozhraní API Cassandra pro Azure Cosmos DB podporuje tyto funkce CQL:

|Příkaz  |Podporováno |
|---------|---------|
| Klíčové | Yes |
| TTL * * * | Yes |
| writetime *** | Yes |
| přetypování * * | Yes |

> [!NOTE] 
> \* Rozhraní API Cassandra podporuje token jako projekci nebo selektor a povoluje token (PK) na levé straně klauzule WHERE. Například `WHERE token(pk) > 1024` je podporován, ale není `WHERE token(pk) > token(100)` podporován.   
> \*\*`cast()`Funkce nemůže být vnořena v rozhraní API Cassandra. Například `SELECT cast(count as double) FROM myTable` je podporován, ale není `SELECT avg(cast(count as double)) FROM myTable` podporován.     
> \*\*\* Vlastní časová razítka a hodnota TTL zadané s `USING` možností se aplikují na úrovni řádků (a ne na buňku).



Agregační funkce:

|Příkaz  |Podporováno |
|---------|---------|
| volání | Yes |
| count | Yes |
| min | Yes |
| max | Yes |
| Součet | Yes |

> [!NOTE]
> Agregační funkce fungují na běžných sloupcích, ale agregace ve sloupcích clusteringu **nejsou podporovány.**


Funkce pro převod objektů BLOB:
 
|Příkaz  |Podporováno |
|---------|---------|
| typeAsBlob(value)   | Yes |
| blobAsType(value) | Yes |


Funkce UUID a timeuuid:
 
|Příkaz  |Podporováno |
|---------|---------|
| dateOf()  | Yes |
| now()  | Yes |
| minTimeuuid()  | Yes |
| unixTimestampOf()  | Yes |
| toDate(timeuuid)  | Yes |
| toTimestamp(timeuuid)  | Yes |
| toTimestamp(timeuuid)  | Yes |
| toDate(timeuuid)  | Yes |
| toTimestamp(timeuuid)  | Yes |
| toTimestamp(date)  | Yes |
| toTimestamp(date) | Yes |


  
## <a name="cql-commands"></a>Příkazy CQL

Azure Cosmos DB podporuje u účtů rozhraní API Cassandra následující databázové příkazy.

|Příkaz  |Podporováno |
|---------|---------|
| POVOLENÍ FILTROVÁNÍ | Yes |
| ZMĚNIT MÍSTO NA DISKU | Není k dispozici (služba PaaS, interně spravovaná replikace)|
| ZMĚNIT MATERIALIZOVANÉ ZOBRAZENÍ | No |
| ZMĚNIT ROLI | No |
| ALTER TABLE | Yes |
| ZMĚNIT TYP | No |
| ZMĚNIT UŽIVATELE | No |
| PARTIE | Ano (pouze nezaznamenaná dávka)|
| KOMPAKTNÍ ÚLOŽIŠTĚ | Není k dispozici (služba PaaS) |
| VYTVOŘIT AGREGOVANOU | No | 
| VYTVOŘIT VLASTNÍ INDEX (SASI) | No |
| CREATE INDEX | Ano (bez [zadání názvu indexu](cassandra-secondary-index.md)a indexů na klíčích clusteringu nebo celá zmrazená kolekce není podporovaná) |
| CREATE FUNCTION | No |
| VYTVOŘIT prostor (nastavení replikace se ignorují) | Yes |
| VYTVOŘIT MATERIALIZOVANÉ ZOBRAZENÍ | No |
| CREATE TABLE | Yes |
| VYTVOŘIT AKTIVAČNÍ UDÁLOST | No |
| VYTVOŘIT TYP | Yes |
| VYTVOŘIT ROLI | No |
| VYTVOŘIT uživatele (zastaralé v nativní Apache Cassandra) | No |
| DELETE | Yes |
| DISTINCT | No |
| ZRUŠIT AGREGAČNÍ | No |
| DROP FUNCTION | No |
| DROP INDEX | Yes |
| ODKLÁDACÍ MÍSTO | Yes |
| VYŘADIT MATERIALIZOVANÉ ZOBRAZENÍ | No |
| ROLE ZRUŠENÍ | No |
| DROP TABLE | Yes |
| VYŘADIT AKTIVAČNÍ UDÁLOST | No | 
| TYP PŘETAŽENÍ | Yes |
| Přetažení uživatele (zastaralé v nativní Apache Cassandra) | No |
| GRANT | No |
| INSERT | Yes |
| OPRÁVNĚNÍ K VYPSÁNÍ | No |
| SEZNAM ROLÍ | No |
| SEZNAM uživatelů (zastaralých v nativních Apache Cassandra) | No |
| REVOKE | No |
| SELECT | Yes |
| UPDATE | Yes |
| ZKRÁTIT | No |
| USE | Yes |

## <a name="lightweight-transactions-lwt"></a>Jednoduché transakce (LWT)

| Součást  |Podporováno |
|---------|---------|
| ODSTRANIT, POKUD EXISTUJE | Yes |
| Odstranit podmínky | No |
| VLOŽIT, POKUD NEEXISTUJE | Yes |
| AKTUALIZOVAT, POKUD EXISTUJE | Yes |
| AKTUALIZOVAT, POKUD NEEXISTUJE | Yes |
| AKTUALIZOVAT podmínky | No |

## <a name="cql-shell-commands"></a>Příkazy prostředí CQL

Azure Cosmos DB podporuje u účtů rozhraní API Cassandra následující databázové příkazy.

|Příkaz  |Podporováno |
|---------|---------|
| SNÍMKY | Yes |
| JEJICH | Yes |
| SHODY | – |
| KOPIÍ | No |
| OZNAČOVAT | Yes |
| cqlshExpand | No |
| AKCI | Yes |
| HLAS | Není k dispozici (funkce CQL není `USER` podporována, proto `LOGIN` je redundantní) |
| PŘENOSU | Yes |
| SÉRIOVÉ KONZISTENCE * | – |
| UVÁDÍ | Yes |
| ZDROJ | Yes |
| PROBÍHÁ | Není k dispozici (rozhraní API Cassandra se zálohuje Azure Cosmos DB – použijte [protokolování diagnostiky](cosmosdb-monitor-resource-logs.md) pro řešení potíží). |

> [!NOTE] 
> \* Konzistence funguje v Azure Cosmos DB odlišně, další informace najdete [tady](cassandra-consistency.md) .  


## <a name="json-support"></a>Podpora JSON
|Příkaz  |Podporováno |
|---------|---------|
| VYBRAT JSON | Yes |
| VLOŽIT JSON | Yes |
| fromJson() | No |
| toJson () | No |


## <a name="cassandra-api-limits"></a>Omezení rozhraní API Cassandra

Rozhraní API Cassandra pro Azure Cosmos DB nemá žádná omezení velikosti dat uložených v tabulce. Když se dodrží limity klíče oddílu, je možné uložit stovky terabajtů nebo petabajtů dat. Podobně všechny ekvivalenty entit nebo řádků nemají omezení počtu sloupců. Celková velikost entity však nesmí překročit 2 MB. Data na klíč oddílu nesmí být větší než 20 GB jako u všech ostatních rozhraní API.

## <a name="tools"></a>nástroje 

Rozhraní API Cassandra pro Azure Cosmos DB je platforma pro spravované služby. Ke správě clusteru nevyžaduje žádnou režii ani nástroje řízení, jako jsou například systém uvolňování paměti, Java Virtual Machine (JVM) a nodetool. Podporuje nástroje, jako je cqlsh, které využívá kompatibilitu Binary CQLv4. 

* Průzkumník dat Azure Portal, metriky, diagnostika protokolů, PowerShell a rozhraní příkazového řádku jsou dalšími podporovanými mechanismy pro správu účtu.

## <a name="hosted-cql-shell-preview"></a>Hostované prostředí CQL (Preview)

Hosted Native Cassandra Shell (CQLSH v 5.0.1) můžete otevřít přímo z Průzkumník dat v [Azure Portal](data-explorer.md) nebo v [Průzkumníkovi Azure Cosmos DB](https://cosmos.azure.com/). Než povolíte prostředí CQL, musíte ve svém účtu [Povolit funkci poznámkových blocích](enable-notebooks.md) (Pokud ještě není povolená, zobrazí se vám výzva při kliknutí `Open Cassandra Shell` ). Přečtěte si článek [Povolení poznámkových bloků pro účty Azure Cosmos DB](enable-notebooks.md#supported-regions) pro podporované oblasti Azure.

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="Otevřít CQLSH":::

Můžete se také připojit k rozhraní API Cassandra v Azure Cosmos DB pomocí CQLSH nainstalovaného na místním počítači. Dodává se s Apache Cassandra 3.1.1 a funguje v poli nastavením proměnných prostředí. Následující části obsahují pokyny k instalaci, konfiguraci a připojení k rozhraní API Cassandra v Azure Cosmos DB, v systému Windows nebo Linux pomocí CQLSH.

> [!NOTE]
> Připojení k Azure Cosmos DB rozhraní API Cassandra nebudou fungovat s DataStax Enterprise (DSE) ve verzích CQLSH. Při připojování k rozhraní API Cassandra se prosím ujistěte, že používáte jenom open source verze Apache Cassandra CQLSH. 

**Systému**

Pokud používáte systém Windows, doporučujeme povolit [systém souborů systému Windows pro Linux](/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Pak můžete postupovat podle níže uvedených příkazů pro Linux.

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
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

Všechny operace CRUD, které jsou spouštěny prostřednictvím sady SDK kompatibilní s CQL v4, vrátí další informace o tom, jaké chybové a využívané jednotky žádosti se vybírají. Příkazy DELETE a UPDATE by se měly zpracovat s ohledem na dodržování zásad správného řízení prostředků, aby se zajistilo nejúčinnější využívání zřízené propustnosti.

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

Rozhraní API Cassandra pro Azure Cosmos DB poskytuje volbu konzistence pro operace čtení.  Mapování konzistence je popsáno [zde](./cassandra-consistency.md#mapping-consistency-levels).

## <a name="permission-and-role-management"></a>Správa oprávnění a rolí

Azure Cosmos DB podporuje řízení přístupu na základě role Azure (Azure RBAC) pro zřizování, otáčení klíčů, zobrazení metrik a hesla pro čtení a zápis a klíče nebo klíče, které lze získat prostřednictvím [Azure Portal](https://portal.azure.com). Azure Cosmos DB nepodporuje role pro aktivity CRUD.

## <a name="keyspace-and-table-options"></a>Možnosti prostoru klíčů a tabulek

Možnosti pro název oblasti, třídu, replication_factor a datacentrum v příkazu pro vytvoření prostoru klíčů se aktuálně ignorují. Systém používá k přidání oblastí základní metodu replikace [globální distribuce](global-dist-under-the-hood.md) Azure Cosmos DB. Pokud potřebujete data mezi oblastmi, můžete ji povolit na úrovni účtu pomocí PowerShellu, rozhraní příkazového řádku nebo portálu. Další informace najdete v článku [Přidání oblastí](how-to-manage-database-account.md#addremove-regions-from-your-database-account) . Durable_writes nejde zakázat, protože Azure Cosmos DB zajišťuje, že každý zápis je trvalý. V každé oblasti Azure Cosmos DB replikuje data napříč sadou replik, která je tvořená čtyřmi replikami a tuto [konfiguraci](global-dist-under-the-hood.md) sady replik nelze upravit.
 
Při vytváření tabulky se ignorují všechny možnosti, kromě gc_grace_seconds, které by měly být nastavené na hodnotu nula.
V prostoru a tabulce se nachází další možnost s názvem "cosmosdb_provisioned_throughput" s minimální hodnotou 400 RU/s. Propustnost místa na disku umožňuje sdílení propustnosti napříč několika tabulkami a je užitečná pro scénáře, kdy všechny tabulky nevyužívají zřízenou propustnost. Příkaz ALTER TABLE umožňuje změnit zřízenou propustnost napříč oblastmi. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>Sekundární index
Rozhraní API Cassandra podporuje sekundární indexy pro všechny datové typy s výjimkou zmrazených typů kolekce, typů Decimal a variant. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>Používání zásad opakování připojení Cassandra

Azure Cosmos DB je systém spravovaný systémem prostředků. To znamená, že v dané druhé operaci můžete provést určitý počet operací na základě jednotek žádostí spotřebovaných operacemi. Pokud aplikace během dané sekundy toto omezení překročí, požadavky jsou omezené na frekvenci a vyvolají se výjimky. Rozhraní API Cassandra v Azure Cosmos DB překládá tyto výjimky na přetížené chyby v nativním protokolu Cassandra. Chcete-li zajistit, aby vaše aplikace mohla zachytit a opakovat požadavky v případě omezení přenosové rychlosti, jsou k dispozici rozšíření [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) a [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) . Pokud se připojujete k rozhraní API Cassandra v Azure Cosmos DB, přečtěte si také ukázky kódu Java pro ovladače [verze 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) a [verze 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) DataStax. Pokud k přístupu k rozhraní API Cassandra v Azure Cosmos DB používáte jiné sady SDK, vytvořte zásadu připojení a zkuste to znovu s těmito výjimkami.

## <a name="next-steps"></a>Další kroky

- Začínáme s [vytvořením účtu rozhraní API Cassandra, databáze a tabulky](create-cassandra-api-account-java.md) prostřednictvím aplikace Java
