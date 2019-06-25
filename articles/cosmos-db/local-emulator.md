---
title: Místní vývoj s využitím emulátor služby Azure Cosmos
description: Emulátor služby Azure Cosmos můžete vyvíjet a testovat aplikace místně pro zdarma, bez vytváření předplatného Azure.
ms.service: cosmos-db
ms.topic: tutorial
author: deborahc
ms.author: dech
ms.date: 06/21/2019
ms.openlocfilehash: d7d9d62525161e6871cafd65cf5cd2c403cf0579
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331776"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Pro místní vývoj a testování používat emulátor služby Azure Cosmos

Emulátor služby Azure Cosmos nabízí místní prostředí, které emuluje služby Azure Cosmos DB pro účely vývoje. Emulátor služby Azure Cosmos můžete vyvíjet a testovat aplikace místně bez vytváření předplatného Azure a bez jakýchkoli nákladů. Jakmile budete spokojeni s fungováním aplikace v emulátoru služby Azure Cosmos, můžete přejít na účet Azure Cosmos v cloudu.

Můžete vyvíjet pomocí emulátoru služby Azure Cosmos pomocí [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api), a [tabulky](local-emulator.md#table-api) Účty rozhraní API. Ale v tuto chvíli zobrazení Průzkumníku dat se spustila v emulátoru plně podporuje klienty pro rozhraní SQL API pouze. 

## <a name="how-the-emulator-works"></a>Jak emulátor funguje

Emulátor služby Azure Cosmos nabízí vysokou věrností emulace služby Azure Cosmos DB. Podporuje stejné funkce jako Azure Cosmos DB, včetně podpory pro vytvoření a dotazování na data, zřizování a škálování kontejnerů a spouštění uložených procedur a aktivačních událostí. Vývoj a testování aplikací s využitím emulátor služby Azure Cosmos a jejich nasazení do Azure v globálním měřítku tak, že pouze jediné konfiguraci změnit na koncový bod připojení pro službu Azure Cosmos DB.

Přestože je emulace služby Azure Cosmos DB věrná, implementace emulátoru se od služby liší. Emulátor například používá standardní součásti operačního systému, jako je místní systém souborů pro trvalost a sada protokolů HTTPS pro připojení. Funkce, která závisí na infrastrukturu Azure, jako je globální replikaci, latence v řádu milisekund pro čtení/zápisu a přizpůsobitelných úrovní konzistence se nedají použít.

Můžete migrovat data mezi emulátor služby Azure Cosmos a služby Azure Cosmos DB s použitím [nástroj pro migraci dat Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Můžete spustit emulátor služby Azure Cosmos v kontejneru Windows Docker, najdete v článku [Docker Hubu](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) příkazu docker pull a [Githubu](https://github.com/Azure/azure-cosmos-db-emulator-docker) pro zdrojový kód emulátoru.

## <a name="differences-between-the-emulator-and-the-service"></a>Rozdíly mezi emulátorem a službou

Protože emulátor služby Azure Cosmos nabízí prostředí emulované běžící na místních vývojářskou pracovní stanici, existují určité rozdíly ve funkcích mezi emulátorem a účet Azure Cosmos v cloudu:

* Průzkumník dat se spustila v emulátoru teď podporuje klienty pro rozhraní SQL API. Zobrazení Průzkumníka dat a operací pro Azure Cosmos DB API, například MongoDB, tabulka, graf a rozhraní Cassandra API nejsou plně podporované.
* Emulátor služby Azure Cosmos podporuje pouze jeden účet pevné a dobře známé hlavní klíč. Obnovování klíčů se nedá v emulátor služby Azure Cosmos, ale výchozí klíč lze změnit pomocí parametru příkazového řádku.
* Emulátor služby Azure Cosmos není škálovatelné služby a nebude podporovat velký počet kontejnerů.
* Emulátor služby Azure Cosmos se nebude poskytovat různé [úrovně konzistence služby Azure Cosmos DB](consistency-levels.md).
* Emulátor služby Azure Cosmos se nebude poskytovat [replikace ve více oblastech](distribute-data-globally.md).
* Jak si kopii emulátor služby Azure Cosmos nemusí být vždy aktuální pomocí nejnovější změny ve službě Azure Cosmos DB, by měla odkazovat na [Plánovač kapacity služby Azure Cosmos DB](https://www.documentdb.com/capacityplanner) pro přesný odhad produkční propustnost (ru) potřebám vaší aplikace.
* Pokud používáte emulátor služby Azure Cosmos, ve výchozím nastavení, můžete vytvořit až 25 pevné velikosti kontejnerů (podporovány pouze při použití sady SDK služby Azure Cosmos DB) nebo 5 neomezené kontejnery pomocí emulátoru služby Azure Cosmos. Další informace o změně této hodnoty najdete v části [Nastavení hodnoty PartitionCount](#set-partitioncount).

## <a name="system-requirements"></a>Požadavky na systém

Emulátor služby Azure Cosmos má následující požadavky na hardware a software:

* Požadavky na software
  * Windows Server 2012 R2, Windows Server 2016 nebo Windows 10
  * 64bitová verze operačního systému
* Minimální požadavky na hardware
  * 2 GB RAM
  * 10 GB volného místa na disku

## <a name="installation"></a>Instalace

Můžete stáhnout a nainstalovat emulátor služby Azure Cosmos z [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) nebo můžete spustit emulátor v Dockeru pro Windows. Pokyny týkající se použití emulátoru v aplikaci Docker for Windows najdete v části [Spuštění v Dockeru](#running-on-docker).

> [!NOTE]
> Pokud chcete nainstalovat, nakonfigurovat a spustit emulátor služby Azure Cosmos, musí mít oprávnění správce v počítači. Emulátor se vytvořit nebo přidat certifikát a také nastavit pravidla brány firewall Chcete-li spustit jejích služeb; Proto je nezbytné pro emulátor, abyste mohli provádět tyto operace.

## <a name="running-on-windows"></a>Spuštění v systému Windows

Pokud chcete spustit emulátor služby Azure Cosmos, vybrat tlačítko Start nebo stisknutím klávesy Windows. Začněte psát **emulátor služby Azure Cosmos**a vyberte emulátor ze seznamu aplikací.

![Vybrat tlačítko Start nebo stiskněte klávesu Windows, začněte psát ** Azure Cosmos emulátor ** a vyberte emulátor ze seznamu aplikací](./media/local-emulator/database-local-emulator-start.png)

Po spuštění emulátoru se v oznamovací oblasti hlavního panelu Windows zobrazí jeho ikona. ![Azure Cosmos DB místní emulátor panel oznámení o úlohy](./media/local-emulator/database-local-emulator-taskbar.png)

Emulátor služby Azure Cosmos ve výchozím nastavení se spustí v místním počítači ("localhost") naslouchá na portu 8081.

Emulátor služby Azure Cosmos je nainstalován do `C:\Program Files\Azure Cosmos DB Emulator` ve výchozím nastavení. Emulátor můžete také spustit a zastavit z příkazového řádku. Další informace najdete v [referenčních informacích k nástroji příkazového řádku](#command-line).

## <a name="start-data-explorer"></a>Spuštění Průzkumníka dat

Když spustí se emulátor služby Azure Cosmos, automaticky se otevře Průzkumník dat Azure Cosmos v prohlížeči. Adresa se zobrazí jako `https://localhost:8081/_explorer/index.html`. Pokud ho zavřete Průzkumníka a chcete ho později znovu otevřete, můžete otevřít adresu URL v prohlížeči nebo spouštět z emulátoru služby Azure Cosmos v ikonu na hlavním panelu Windows, jak je znázorněno níže.

![Azure Cosmos místní emulátor data Průzkumníka Spouštěče](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Kontrola aktualizací

Průzkumník dat zjistí, zda je k dispozici nová aktualizace ke stažení.

> [!NOTE]
> Data vytvořená v jedné verzi emulátor služby Azure Cosmos (viz %LOCALAPPDATA%\CosmosDBEmulator nebo data volitelné nastavení cesty) nemusí být dostupné při používání jiné verze. Pokud je potřeba pro dlouhodobé uchovávání dat, doporučujeme ukládat data v účtu služby Azure Cosmos, nikoli emulátor služby Azure Cosmos.

## <a name="authenticating-requests"></a>Ověřování požadavků

Jako pomocí služby Azure Cosmos DB v cloudu, musí být ověřeny všechny požadavky, které vytváříte, proti emulátor služby Azure Cosmos. Emulátor služby Azure Cosmos podporuje jeden účet pevné a dobře známý ověřovací klíč pro ověřování hlavní klíč. Tento účet a klíče jsou pouze pověření pro použití s emulátor služby Azure Cosmos. Jsou to tyto:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Hlavní klíč nepodporuje emulátor služby Azure Cosmos je určena pouze pro použití s emulátorem. Váš účet Azure Cosmos DB v produkčním prostředí a klíč nelze použít s emulátor služby Azure Cosmos.

> [!NOTE]
> Pokud jste spustili v emulátoru s možností uveden, použijte vygenerovaný klíč, místo `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Další informace o možnosti uveden v tématu [odkaz na nástroj příkazového řádku.](#command-line-syntax)

Jako službou Azure Cosmos DB, emulátor služby Azure Cosmos podporuje pouze zabezpečenou komunikaci protokolem SSL.

## <a name="running-on-a-local-network"></a>Spuštění v místní síti

Emulátor můžete spustit v místní síti. Chcete-li povolit přístup k síti, zadejte `/AllowNetworkAccess` možnost na [příkazového řádku](#command-line-syntax), což také vyžaduje, že zadáte `/Key=key_string` nebo `/KeyFile=file_name`. Můžete použít `/GenKeyFile=file_name` vygenerovat soubor s předem náhodný klíč. Pak můžete předat ho do `/KeyFile=file_name` nebo `/Key=contents_of_file`.

Povolení přístupu k síti pro první by měl uživatel vypněte emulátor a odstraňte adresář data v emulátoru (% LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Vývoj v emulátoru

### <a name="sql-api"></a>SQL API

Jakmile budete mít emulátor služby Azure Cosmos spuštěná na ploše, lze použít podporované [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) nebo [REST API služby Azure Cosmos DB](/rest/api/cosmos-db/) k interakci s emulátorem. Emulátor služby Azure Cosmos zahrnuje také integrované Průzkumník dat, který vám umožní vytvořit kontejnery pro rozhraní SQL API nebo Cosmos DB pro rozhraní Mongodb API a zobrazit a upravit položky bez psaní kódu.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

Pokud používáte [rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-introduction.md), použijte následující připojovací řetězec:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Rozhraní Table API

Jakmile budete mít emulátor služby Azure Cosmos spuštěná v počítači, můžete použít [Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) k interakci s emulátorem. Spusťte emulátor z příkazového řádku jako správce pomocí "/ EnableTableEndpoint". Následně spusťte následující kód k připojení k účtu rozhraní API tabulky:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Rozhraní Cassandra API

Spusťte emulátor z příkazového řádku správce pomocí "/ EnableCassandraEndpoint". Případně můžete také nastavit proměnnou prostředí `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Nainstalujte Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Install Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* V okně regulární příkazového řádku spusťte následující příkazy:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* V prostředí CQLSH spusťte následující příkazy pro připojení na koncový bod Cassandra:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>Rozhraní Gremlin API

Spusťte emulátor z příkazového řádku správce pomocí "/ EnableGremlinEndpoint". Případně můžete také nastavit proměnné prostředí `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Instalace apache tinkerpop-gremlin konzoly-3.3.4](https://tinkerpop.apache.org/downloads.html)

* V Průzkumníku dat na emulátor vytvořte databázi "db1" a kolekce "coll1"; pro klíč oddílu zvolte možnost "/ name"

* V okně regulární příkazového řádku spusťte následující příkazy:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* V prostředí Gremlin spusťte následující příkazy pro připojení na koncový bod Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Export certifikátu SSL

Jazyky a moduly runtime rozhraní .NET používají k bezpečnému připojení k místnímu emulátoru služby Azure Cosmos DB úložiště certifikátů systému Windows. Další jazyky mají vlastní metody správy a použití certifikátů. Java používá vlastní [úložiště certifikátů](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), zatímco Python používá [obálky soketu](https://docs.python.org/2/library/ssl.html).

Pokud chcete získat certifikát pro použití s jazyky a moduly runtime, které se neintegrují s úložištěm certifikátů systému Windows, budete ho muset exportovat pomocí Správce certifikátů systému Windows. Můžete spustit spuštěním certlm.msc nebo pokyny krok za krokem v [Export certifikátů emulátoru Azure Cosmos](./local-emulator-export-ssl-certificates.md). Jakmile je správce certifikátů spuštěn, otevřete osobní certifikáty, jak je zobrazeno níže, a exportujte certifikát s popisným názvem „DocumentDBEmulatorCertificate“ jako soubor X.509 (.cer) s kódováním BASE-64.

![Certifikát SSL místního emulátoru služby Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Certifikát X.509 lze importovat do úložiště certifikátů Javy podle pokynů v tématu o [přidání certifikátu do úložiště certifikátů certifikační autority Javy](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Jakmile je certifikát importován do úložiště certifikátů, klienti SQL a Azure Cosmos DB: rozhraní API pro MongoDB bude moct připojit k emulátoru služby Azure Cosmos.

Při připojování k emulátoru ze sad SDK Pythonu a Node.js je zakázáno ověřování SSL.

## <a id="command-line"></a>Reference nástroje příkazového řádku
Z umístění instalace můžete příkazový řádek pro spuštění a zastavení emulátor, nakonfigurujte možnosti a provádět jiné operace.

### <a name="command-line-syntax"></a>Syntaxe příkazového řádku

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Pokud chcete zobrazit seznam možností, na příkazovém řádku zadejte `CosmosDB.Emulator.exe /?`.

|**Možnost** | **Popis** | **Příkaz**| **Argumenty**|
|---|---|---|---|
|[Žádné argumenty] | Spustí se emulátor služby Azure Cosmos pomocí výchozích nastavení. |CosmosDB.Emulator.exe| |
|[Nápověda] |Zobrazí seznam podporovaných argumentů příkazového řádku.|CosmosDB.Emulator.exe /? | |
| GetStatus |Získá stav emulátor služby Azure Cosmos. Stav je indikován ukončovací kód: 1 = od, 2 = spuštěný, 3 = zastavena. Záporný ukončovací kód označuje, že došlo k chybě. Žádný jiný výstup neexistuje. | CosmosDB.Emulator.exe /GetStatus| |
| Shutdown| Vypne emulátor služby Azure Cosmos.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Určuje cestu, do které chcete uložit datové soubory. Výchozí hodnota je % LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<datapath\> | \<DataPath\>: Přístupná cesta |
|Port | Určuje číslo portu pro emulátor. Výchozí hodnota je 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<Port\>: Jeden port číslo |
| MongoPort | Určuje číslo portu, který chcete použít pro rozhraní API kompatibility MongoDB. Výchozí hodnota je 10255. |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<mongoport\>: Jeden port číslo|
| CassandraPort | Udává číslo portu pro koncový bod Cassandra. Výchozí hodnota je 10350. | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>: Jeden port číslo |
| ComputePort | Zadané číslo portu pro službu Compute brány zprostředkovatele komunikace s objekty. Port testu Koncový bod brány HTTP se počítá jako ComputePort + 79. Proto ComputePort a ComputePort + 79 musí být otevřený a dostupný. Výchozí hodnoty jsou 8900, 8979. | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>: Jeden port číslo |
| EnableCassandraEndpoint | Umožňuje rozhraní Cassandra API | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| EnableGremlinEndpoint | Umožňuje Gremlin API | CosmosDB.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Číslo portu pro koncový bod Gremlin. Výchozí hodnota je 8901. | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<Port\>: Jeden port číslo |
|TablePort | Číslo portu pro koncový bod tabulky Azure. Výchozí hodnota je 8902. | CosmosDB.Emulator.exe /TablePort=\<port\> | \<Port\>: Jeden port číslo|
| KeyFile | Přečíst autorizační klíč ze zadaného souboru. Pomocí možnosti /GenKeyFile generovat keyfile | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<název_souboru\>: Cesta k souboru |
| ResetDataPath | Rekurzivně odstraní všechny soubory v zadané cestě. Pokud cestu nezadáte, použije se výchozí %LOCALAPPDATA%\CosmosDbEmulator | CosmosDB.Emulator.exe /ResetDataPath[=\<path>] | \<Cesta\>: Cesta k souboru  |
| StartTraces  |  Bylo zahájeno shromažďování protokolů trasování ladění. | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | Zastavte shromažďování protokolů trasování ladění. | CosmosDB.Emulator.exe /StopTraces  | |
|EnableTableEndpoint | Umožňuje rozhraní API tabulky Azure | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|FailOnSslCertificateNameMismatch | Ve výchozím nastavení emulátoru obnoví jeho certifikátu SSL podepsaného držitelem, pokud tento certifikát SAN neobsahuje hostitele emulátor název domény, místní IPv4 adresu, "localhost" a "127.0.0.1". Pomocí této možnosti emulátoru se místo toho nezdaří při spuštění. Pomocí možnosti /GenCert byste pak vytvořte a nainstalujte nový certifikát SSL podepsaný svým držitelem. | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Vygenerujte a nainstalujte nový certifikát SSL podepsaný svým držitelem. Volitelně včetně čárkou oddělený seznam dalších názvy DNS pro přístup k emulátoru přes síť. | CosmosDB.Emulator.exe /GenCert [ \<čárkami oddělený seznam dalších názvů dns\>] | |
| DirectPorts |Určuje porty, které chcete použít pro přímé připojení. Výchozí hodnoty jsou 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<přímé porty\> | \<directports\>: Čárkami oddělený seznam portů 4 |
| Klíč |Autorizační klíč pro emulátor. Klíč musí být 64bajtový vektor s kódováním base-64. | CosmosDB.Emulator.exe /Key:\<klíč\> | \<Klíč\>: Klíč musí být v kódování base-64 vektoru 64 bajtů|
| EnableRateLimiting | Určuje, že je povoleno chování omezující četnost požadavků. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Určuje, že je zakázáno chování omezující četnost požadavků. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Nezobrazuje uživatelské rozhraní emulátoru. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Nezobrazuje Průzkumníka dat při spuštění. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Určuje maximální počet oddílů kontejnerů. Zobrazit [změnit počet kontejnerů](#set-partitioncount) Další informace. | CosmosDB.Emulator.exe /PartitionCount=\<počet oddílů\> | \<partitioncount\>: Maximální počet povolených jednoho oddílu kontejnerů. Výchozí hodnota je 25. Maximální povolený počet je 250.|
| DefaultPartitionCount| Určuje výchozí počet oddílů pro dělený kontejner. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> výchozí hodnota je 25.|
| AllowNetworkAccess | Povolí přístup k emulátoru přes síť. Pokud chcete povolit přístup k síti, je nutné předat taky možnosti /Key =\<řetězec_klíče\> nebo/KeyFile =\<název_souboru\>. | CosmosDB.Emulator.exe AllowNetworkAccess uveden =\<key_string\> nebo/keyfile /AllowNetworkAccess CosmosDB.Emulator.exe =\<název_souboru\>| |
| NoFirewall | Neupravujte pravidla brány firewall, pokud není použita možnost /AllowNetworkAccess. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Vygeneruje nový autorizační klíč a uloží ho do zadaného souboru. Generovaný klíč lze použít s možností /Key nebo/KeyFile. | CosmosDB.Emulator.exe /GenKeyFile =\<cestu k souboru klíče\> | |
| Konzistence | Nastaví výchozí úroveň konzistence pro účet. | CosmosDB.Emulator.exe /Consistency=\<konzistence\> | \<Konzistence\>: Hodnota musí být jedna z následujících [úrovně konzistence](consistency-levels.md): Relace, silné, konečný výsledek, nebo BoundedStaleness. Výchozí hodnota je Session. |
| ? | Zobrazí zprávu nápovědy.| | |

## <a id="set-partitioncount"></a>Změňte počet kontejnerů

Ve výchozím nastavení můžete vytvořit až 25 pevné velikosti kontejnerů (podporovány pouze při použití sady SDK služby Azure Cosmos DB) nebo 5 neomezené kontejnery pomocí emulátoru služby Azure Cosmos. Úpravou **PartitionCount** hodnotu, můžete vytvořit až 250 pevné velikosti kontejnerů nebo 50 neomezené kontejnery nebo libovolnou kombinaci obou, které není delší než 250 pevné velikosti kontejnerů (kde jedna neomezený kontejner = 5 pevné velikosti kontejnery). Ale ne doporučujeme nastavte emulátor pomocí více než 200 pevné velikosti kontejnerů. Z důvodu režie, která se přidá na v/v operace disku, který způsobit neočekávané vypršení časového limitu při použití koncového bodu rozhraní API.


Pokud se pokusíte vytvořit kontejner po překročil aktuální počet oddílů, emulátor výjimku ServiceUnavailable, s následující zprávou.

"Je nám líto, ale jsou aktuálně dochází k vysokému zatížení v této oblasti jsme v tuto chvíli se nedá splnit vaše žádost. Jsme neustále pracovat zpřístupnit víc a víc kapacity online a můžete to zkusit znovu.
Prosím neváhejte k e-mailu askcosmosdb@microsoft.com kdykoli nebo z jakéhokoli důvodu. ID aktivity: 12345678-1234-1234-1234-123456789abc"

Chcete-li změnit počet kontejnerů, které jsou k dispozici v emulátoru služby Azure Cosmos, proveďte následující kroky:

1. Odstranit všechna data místní emulátor služby Azure Cosmos kliknutím pravým tlačítkem myši **emulátor služby Azure Cosmos DB** ikonu na hlavním panelu a následným kliknutím na položku **obnovit Data...** .
2. Odstranit všechna data emulátor v této složce `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Ukončete všechny otevřené instance tak, že kliknete pravým tlačítkem myši na ikonu **emulátoru služby Azure Cosmos DB** na hlavním panelu systému a potom kliknete na **Exit** (Konec). Ukončení všech instancí může chvíli trvat.
4. Nainstalujte nejnovější verzi [emulátor služby Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Spusťte emulátor s příznakem PartitionCount nastaveným na hodnotu < = 250. Například: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Řízení emulátoru

Emulátor se instaluje spolu s modul PowerShell pro spuštění, zastavení, odinstalace a načíst stav služby. Spusťte následující rutiny můžete použít modul prostředí PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

nebo umístit `PSModules` ve vaší `PSModulesPath` a naimportujete, jak je znázorněno v následujícím příkazu:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Zde je uveden seznam příkazů pro řízení emulátoru z PowerShellu:

### `Get-CosmosDbEmulatorStatus`

**Syntaxe**

`Get-CosmosDbEmulatorStatus`

**Poznámky**

Vrátí jednu z těchto hodnot ServiceControllerStatus: ServiceControllerStatus.StartPending ServiceControllerStatus.Running či ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntaxe**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Poznámky**

Spustí emulátor. Ve výchozím nastavení tento příkaz čeká, dokud emulátor nebude připraven přijímat požadavky. Pokud chcete, aby se rutina vrátila, jakmile spustí emulátor, použijte možnost -NoWait.

### `Stop-CosmosDbEmulator`

**Syntaxe**

 `Stop-CosmosDbEmulator [-NoWait]`

**Poznámky**

Zastaví emulátor. Ve výchozím nastavení tento příkaz čeká, až emulátor je zcela vypnutý. Pokud chcete, aby se rutina vrátila, jakmile se emulátor začne vypínat, použijte možnost -NoWait.

### `Uninstall-CosmosDbEmulator`

**Syntaxe**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Poznámky**

Odinstaluje emulátor a volitelně odstraní úplný obsah $env:LOCALAPPDATA\CosmosDbEmulator.
Rutina zajišťuje zastavení emulátoru před jeho odinstalací.

## <a name="running-on-docker"></a>Spuštění v Dockeru

Emulátor služby Azure Cosmos můžete spustit na Docker pro Windows. Emulátor nefunguje v aplikaci Docker for Oracle Linux.

Jakmile [Docker for Windows](https://www.docker.com/docker-windows) nainstalujete, přepněte na kontejnery Windows tak, že kliknete pravým tlačítkem myši na ikonu Dockeru na panelu nástrojů a vyberete **Switch to Windows containers** (Přepnout na kontejnery Windows).

Potom si spuštěním následujícího příkazu z vašeho oblíbeného prostředí stáhněte image emulátoru z Centra Dockeru.

```bash
docker pull microsoft/azure-cosmosdb-emulator
```
Pokud chcete image spustit, spusťte následující příkazy.

Z příkazového řádku:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator
```

Z PowerShellu:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 microsoft/azure-cosmosdb-emulator

```

Odpověď vypadá podobně jako následující zpráva:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Nyní použijte koncový bod a hlavní klíč z odpovědi ve svém klientovi a importujte certifikát SSL do svého hostitele. Pokud chcete importovat certifikát SSL, spusťte následující příkazy z příkazového řádku správce:

Z příkazového řádku:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

Z PowerShellu:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Zavřením interaktivního prostředí po spuštění emulátoru se ukončí kontejner emulátoru.

Pokud chcete otevřít Průzkumníka dat, přejděte v prohlížeči na následující adresu URL. Koncový bod emulátoru je uveden ve zprávě s odpovědí uvedené výše.

    https://<emulator endpoint provided in response>/_explorer/index.html

## Se systémem Mac nebo Linux<a id="mac"></a>

Aktuálně můžete emulátor Cosmos spustit jenom na Windows. Uživatele, které spuštěné Mac nebo Linux ve virtuálním počítači Windows můžete spustit emulátor hostované hypervisoru, jako jsou Parallels nebo VirtualBox. Dále jsou uvedené kroky, aby to bylo.

V rámci virtuálního počítače Windows spusťte následující příkaz a poznamenejte si IPv4 adresu.

```cmd
ipconfig.exe
```

V rámci vaší aplikace budete muset změnit identifikátor URI objektu DocumentClient IPv4 adresu vrácenou `ipconfig.exe`. Dalším krokem je obejít ověření certifikační Autority při vytváření objektu DocumentClient. K tomu budete muset zadat HttpClientHandler pro konstruktor DocumentClient, který má vlastní implementace ServerCertificateCustomValidationCallback.

Níže je příklad kódu by měla vypadat.

```csharp
using System;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using System.Net.Http;

namespace emulator
{
    class Program
    {
        static async void Main(string[] args)
        {
            string strEndpoint = "https://10.135.16.197:8081/";  //IPv4 address from ipconfig.exe
            string strKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";

            //Work around the CA validation
            var httpHandler = new HttpClientHandler()
            {
                ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
            };

            //Pass http handler to document client
            using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
            {
                Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "myDatabase" });
                Console.WriteLine($"Created Database: id - {database.Id} and selfLink - {database.SelfLink}");
            }
        }
    }
}
```

Nakonec z na virtuálním počítači Windows spusťte emulátor Cosmos z příkazového řádku pomocí následujících možností.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

## <a name="troubleshooting"></a>Řešení potíží

Použijte následující tipy k řešení potíží, se kterými se emulátor služby Azure Cosmos:

- Pokud jste nainstalovali novou verzi emulátoru a dochází k chybám, proveďte obnovení dat. Pravým tlačítkem myši na ikonu emulátoru služby Azure Cosmos na hlavním panelu systému a potom kliknutím na Obnovit Data můžete obnovit data... Pokud se tím nevyřeší chyby, můžete odinstalovat emulátor a všechny starší verze emulátoru-li nalezeno, odebrat adresář "C:\Program files\Azure emulátor služby Cosmos DB" a znovu nainstalujte emulátor. Pokyny najdete v části [Odinstalace místního emulátoru](#uninstall).

- Pokud dojde k chybě emulátor služby Azure Cosmos, shromažďování výpisu stavu systému souborů ze složky "% LOCALAPPDATA%\CrashDumps", zkomprimovat a připojte je k e-mailu [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Pokud dochází k selhání v `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, to může být příznakem kde čítače výkonu jsou v poškozeném stavu. Obvykle spustíte tento příkaz z příkazového řádku správce řeší problém:

  ```cmd
  lodctr /R
   ```

- Pokud dojde k potížím s připojením, [shromážděte trasovací soubory](#trace-files), zkomprimujte je a připojte je k e-mailu, který odešlete na adresu [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Pokud se zobrazí zpráva **Služba není dostupná**, pravděpodobně se emulátoru nedaří inicializovat sadu síťových protokolů. Zkontrolujte, zda máte nainstalovaného klienta Pulse Secure nebo klienta Juniper Networks, protože potíže mohou způsobovat jejich ovladače síťových filtrů. Odinstalace ovladačů síťových filtrů třetích stran obvykle potíže vyřeší. Alternativně spusťte emulátor s /DisableRIO, který přepne na regulární Winsock emulátor síťové komunikace. 

- Pokud emulátor běží, když počítač přechází do režimu spánku nebo instaluje nějaké aktualizace operačního systému, může se zobrazit zpráva, že **služba momentálně není dostupná**. Obnovit data v emulátoru, kliknutím pravým tlačítkem na ikonu, která se zobrazí v oznamovací oblasti systému windows a vyberte **obnovit Data**.

### <a id="trace-files"></a>Shromažďování trasovacích souborů

Pokud chcete shromažďovat trasovací soubory pro ladění, spusťte z příkazového řádku pro správu následující příkazy:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Sledujte hlavní panel systému a ujistěte se, že program je vypnutý. Může to chvíli trvat. Můžete také stačí kliknout na **ukončovací** v uživatelském rozhraní emulátor služby Azure Cosmos.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reprodukujte problém. Pokud Průzkumník dat nefunguje, stačí několik sekund čekat na otevření prohlížeče a zachytit chybu.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Přejděte do složky `%ProgramFiles%\Azure Cosmos DB Emulator` a vyhledejte soubor docdbemulator_000001.etl.
7. Odešlete soubor .etl spolu s kroky pro zopakování potíží k ladění na adresu [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

### <a id="uninstall"></a>Odinstalace místního emulátoru

1. Ukončete všechny otevřené instance místní emulátor kliknutím pravým tlačítkem na emulátor služby Azure Cosmos ikonu na hlavním panelu systému a potom klepněte na tlačítko. Ukončení všech instancí může chvíli trvat.
2. Do vyhledávacího pole ve Windows zadejte **Programy a funkce** a klikněte na výsledek **Programy a funkce (nastavení systému)** .
3. V seznamu aplikací se posuňte na položku **Azure Cosmos DB Emulator**, vyberte ji, klikněte na **Odinstalovat**, potvrďte a znovu klikněte na **Odinstalovat**.
4. Když je aplikace odinstalovaná, přejděte do složky `%LOCALAPPDATA%\CosmosDBEmulator` a odstraňte ji.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat místní emulátor pro bezplatný místní vývoj. Teď můžete pokračovat k dalšímu kurzu, kde se dozvíte, jak exportovat certifikáty SSL emulátoru.

> [!div class="nextstepaction"]
> [Export certifikátů emulátoru služby Azure Cosmos](local-emulator-export-ssl-certificates.md)
