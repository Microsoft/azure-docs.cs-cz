---
title: Vývoj místně pomocí emulátoru Azure Cosmos
description: Pomocí emulátoru Azure Cosmos můžete bezplatně vyvíjet a testovat aplikaci místně bez nutnosti vytvářet předplatné Azure.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 01/31/2020
ms.openlocfilehash: 9650bb3214c22926427717569f718ca0426ed729
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618751"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Použití emulátoru Azure Cosmos pro místní vývoj a testování

Emulátor Azure Cosmos poskytuje místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Pomocí emulátoru Azure Cosmos můžete vyvíjet a testovat aplikaci místně, bez vytvoření předplatného Azure nebo vzniklých jakýchkoli nákladů. Když jste spokojeni s tím, jak vaše aplikace funguje v emulátoru Azure Cosmos, můžete přepnout na použití účtu Azure Cosmos v cloudu.

Můžete vyvíjet pomocí Azure Cosmos emulátor u [sql](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)a [table](local-emulator.md#table-api) api účty. V tuto chvíli však zobrazení Průzkumníka dat v emulátoru plně podporuje klienty pouze pro rozhraní SQL API. 

## <a name="how-the-emulator-works"></a>Jak emulátor funguje

Emulátor Azure Cosmos poskytuje emulaci služby Azure Cosmos DB s vysokou věrností. Podporuje identické funkce jako Azure Cosmos DB, včetně podpory pro vytváření a dotazování dat, zřizování a škálování kontejnerů a provádění uložených procedur a aktivačních událostí. Můžete vyvíjet a testovat aplikace pomocí emulátoru Azure Cosmos a nasadit je do Azure v globálním měřítku pouhým provedením jediné změny konfigurace koncového bodu připojení pro Azure Cosmos DB.

Přestože je emulace služby Azure Cosmos DB věrná, implementace emulátoru se od služby liší. Emulátor například používá standardní součásti operačního systému, jako je místní systém souborů pro trvalost a sada protokolů HTTPS pro připojení. Funkce, které závisí na infrastruktuře Azure, jako je globální replikace, jednociferná milisekundová latence pro čtení a zápisy a laditelné úrovně konzistence, se nepoužívají.

Data mezi emulátorem Azure Cosmos a službou Azure Cosmos DB můžete migrovat pomocí [nástroje pro migraci dat Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Můžete spustit Azure Cosmos Emulátor na kontejneru Windows Docker, najdete [v dockeru Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) pro příkaz pro vyžádat docker u a [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) pro `Dockerfile` další informace.

## <a name="differences-between-the-emulator-and-the-service"></a>Rozdíly mezi emulátorem a službou

Vzhledem k tomu, že emulátor Azure Cosmos poskytuje emulované prostředí spuštěné na místní pracovní stanici pro vývojáře, existují určité rozdíly ve funkčnosti mezi emulátorem a účtem Azure Cosmos v cloudu:

* V současné době Průzkumník dat v emulátoru podporuje klienty pro SQL API. Zobrazení Průzkumníka dat a operace pro rozhraní API DB Azure Cosmos, jako jsou MongoDB, Tabulka, Graf a Cassandra rozhraní API nejsou plně podporovány.
* Emulátor Azure Cosmos podporuje jenom jeden pevný účet a známý hlavní klíč. Regenerace klíčů není možné v Emulátoru Azure Cosmos, ale výchozí klíč lze změnit pomocí možnosti příkazového řádku.
* Emulátor Azure Cosmos není škálovatelná služba a nebude podporovat velký počet kontejnerů.
* Emulátor Azure Cosmos nenabízí různé [úrovně konzistence Azure Cosmos DB](consistency-levels.md).
* Emulátor Azure Cosmos nenabízí [replikaci více oblastí](distribute-data-globally.md).
* Jako vaše kopie Emulátoru Azure Cosmos nemusí být vždy aktuální s nejnovějšími změnami ve službě Azure Cosmos DB, měli byste odkazovat na [plánovač kapacity Azure Cosmos DB](https://www.documentdb.com/capacityplanner) přesně odhadnout propustnost výroby (RU) potřeby vaší aplikace.
* Při použití emulátoru Azure Cosmos můžete ve výchozím nastavení vytvořit až 25 kontejnerů s pevnou velikostí (podporované jenom pomocí sad Azure Cosmos DB SDK) nebo 5 neomezených kontejnerů pomocí emulátoru Azure Cosmos. Další informace o změně této hodnoty najdete v části [Nastavení hodnoty PartitionCount](#set-partitioncount).

## <a name="system-requirements"></a>Požadavky na systém

Emulátor Azure Cosmos má následující požadavky na hardware a software:

* Požadavky na software
  * Windows Server 2012 R2, Windows Server 2016 nebo Windows 10
  * 64bitový operační systém
* Minimální požadavky na hardware
  * 2 GB RAM
  * 10 GB volného místa na disku

## <a name="installation"></a>Instalace

Emulátor Azure Cosmos můžete stáhnout a nainstalovat ze [služby Stažení softwaru společnosti Microsoft](https://aka.ms/cosmosdb-emulator) nebo můžete spustit emulátor v Dockeru pro Windows. Pokyny k použití emulátoru v Dockeru pro Windows najdete v [tématu Spuštěno v Dockeru](#running-on-docker).

> [!NOTE]
> Chcete-li nainstalovat, nakonfigurovat a spustit emulátor Azure Cosmos, musíte mít v počítači oprávnění správce. Emulátor vytvoří nebo přidá certifikát a také nastaví pravidla brány firewall pro spuštění svých služeb. proto je nutné, aby emulátor mohl provádět takové operace.

## <a name="running-on-windows"></a>Spuštění v systému Windows

Pokud chcete spustit emulátor Azure Cosmos, vyberte tlačítko Start nebo stiskněte klávesu Windows. Začněte psát **Emulátor Azure Cosmos**a vyberte emulátor ze seznamu aplikací.

![Vyberte tlačítko Start nebo stiskněte klávesu Windows, začněte psát **Azure Cosmos Emulátor**a vyberte emulátor ze seznamu aplikací.](./media/local-emulator/database-local-emulator-start.png)

Po spuštění emulátoru se v oznamovací oblasti hlavního panelu Windows zobrazí jeho ikona. ![Oznámení o hlavním panelu hlavního panelu místního emulátoru Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

Emulátor Azure Cosmos ve výchozím nastavení běží na místním počítači ("localhost") naslouchání na portu 8081.

Emulátor Azure Cosmos se `C:\Program Files\Azure Cosmos DB Emulator` ve výchozím nastavení nainstaluje. Emulátor můžete také spustit a zastavit z příkazového řádku. Další informace najdete v [referenčních informacích k nástroji příkazového řádku](#command-line).

## <a name="start-data-explorer"></a>Spuštění Průzkumníka dat

Když se emulátor Azure Cosmos spustí, automaticky otevře Průzkumník dat Azure Cosmos ve vašem prohlížeči. Adresa se zobrazí jako `https://localhost:8081/_explorer/index.html`. Pokud zavřete průzkumníka a chcete ho později znovu otevřít, můžete adresu URL otevřít v prohlížeči nebo ji spustit z emulátoru Azure Cosmos na ikoně panelu Windows, jak je znázorněno níže.

![Spouštěč místního emulátoru dat Azure Cosmos](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Kontrola aktualizací

Průzkumník dat zjistí, zda je k dispozici nová aktualizace ke stažení.

> [!NOTE]
> Data vytvořená v jedné verzi emulátoru Azure Cosmos (viz %LOCALAPPDATA%\CosmosDBEmulator nebo volitelná nastavení datové cesty) není zaručeno, že budou přístupná při použití jiné verze. Pokud potřebujete zachovat data pro dlouhodobé, doporučujeme ukládat tato data v účtu Azure Cosmos, nikoli v Emulátoru Azure Cosmos.

## <a name="authenticating-requests"></a>Ověřování požadavků

Stejně jako u Azure Cosmos DB v cloudu, musí být ověřen každý požadavek, který provedete proti emulátoru Azure Cosmos. Emulátor Azure Cosmos podporuje jeden pevný účet a známý ověřovací klíč pro ověřování hlavního klíče. Tento účet a klíč jsou pouze pověření povolená pro použití s emulátorem Azure Cosmos. Jsou to tyto:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Hlavní klíč podporovaný emulátorem Azure Cosmos je určen pouze pro použití s emulátorem. S emulátorem Azure Cosmos emulátoru nelze použít produkční účet Azure Cosmos DB a klíč.

> [!NOTE]
> Pokud jste začali emulátor s /Key možnost, použijte generovaný `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`klíč namísto . Další informace o možnosti /Key naleznete v [tématu Reference nástroje příkazového řádku.](#command-line)

Stejně jako u Azure Cosmos DB podporuje emulátor Azure Cosmos jenom zabezpečenou komunikaci prostřednictvím TLS.

## <a name="running-on-a-local-network"></a>Spuštění v místní síti

Emulátor můžete spustit v místní síti. Chcete-li povolit `/AllowNetworkAccess` přístup k síti, zadejte možnost na `/Key=key_string` `/KeyFile=file_name` [příkazovém řádku](#command-line-syntax), která také vyžaduje zadání nebo . Můžete použít `/GenKeyFile=file_name` ke generování souboru s náhodným klíčem předem. Pak můžete předat, `/KeyFile=file_name` `/Key=contents_of_file`že nebo .

Chcete-li povolit přístup k síti poprvé uživatel by měl vypnout emulátor a odstranit adresář dat emulátoru (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Vývoj v emulátoru

### <a name="sql-api"></a>SQL API

Jakmile máte na ploše spuštěný emulátor Azure Cosmos, můžete k interakci s emulátorem použít libovolné podporované rozhraní [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) nebo [rozhraní AZURE Cosmos DB REST.](/rest/api/cosmos-db/) Emulátor Azure Cosmos také obsahuje předdefinovaný Průzkumník dat, který umožňuje vytvářet kontejnery pro rozhraní SQL API nebo Cosmos DB pro rozhraní Mongo DB API a zobrazovat a upravovat položky bez psaní kódu.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB

Jakmile máte na ploše spuštěný emulátor Azure Cosmos, můžete k interakci s emulátorem použít [rozhraní API služby Azure Cosmos DB pro MongoDB.](mongodb-introduction.md) Spusťte emulátor z příkazového řádku jako správce s "/EnableMongoDbEndpoint". Potom se pomocí následujícího připojovacího řetězce připojte k účtu rozhraní MongoDB API:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Rozhraní Table API

Jakmile máte na ploše spuštěný emulátor Azure Cosmos, můžete k interakci s emulátorem použít sytbu [rozhraní Azure Cosmos DB Table API.](table-storage-how-to-use-dotnet.md) Spusťte emulátor z příkazového řádku jako správce s "/EnableTableEndpoint". Další spuštění následujícího kódu pro připojení k účtu rozhraní API tabulky:

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

Spusťte emulátor z příkazového řádku správce s příkazovým příkazem "/EnableCassandraEndpoint". Případně můžete také nastavit proměnnou `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`prostředí .

* [Instalace Pythonu 2.7](https://www.python.org/downloads/release/python-2716/)

* [Instalace Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* V pravidelném okně příkazového řádku spusťte následující příkazy:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* V prostředí CQLSH spusťte následující příkazy pro připojení ke koncovému bodu Cassandra:

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

Spusťte emulátor z příkazového řádku správce s příkazovým příkazem "/EnableGremlinEndpoint". Případně můžete také nastavit proměnnou prostředí`AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Nainstalujte apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

* V průzkumníku dat emulátoru vytvořte databázi "db1" a kolekci "coll1"; pro klíč oddílu zvolte "/name"

* V pravidelném okně příkazového řádku spusťte následující příkazy:

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

* V prostředí Gremlin spusťte následující příkazy pro připojení ke koncovému bodu Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-tlsssl-certificate"></a>Export certifikátu TLS/SSL

Jazyky a moduly runtime rozhraní .NET používají k bezpečnému připojení k místnímu emulátoru služby Azure Cosmos DB úložiště certifikátů systému Windows. Další jazyky mají vlastní metody správy a použití certifikátů. Java používá vlastní [úložiště certifikátů](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), zatímco Python používá [obálky soketu](https://docs.python.org/2/library/ssl.html).

Pokud chcete získat certifikát pro použití s jazyky a moduly runtime, které se neintegrují s úložištěm certifikátů systému Windows, budete ho muset exportovat pomocí Správce certifikátů systému Windows. Můžete ji spustit spuštěním certlm.msc nebo postupujte podle pokynů krok za krokem v [exportu certifikátů emulátoru Azure Cosmos](./local-emulator-export-ssl-certificates.md). Jakmile je správce certifikátů spuštěn, otevřete osobní certifikáty, jak je zobrazeno níže, a exportujte certifikát s popisným názvem „DocumentDBEmulatorCertificate“ jako soubor X.509 (.cer) s kódováním BASE-64.

![Certifikát TLS/SSL místního emulátoru Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Certifikát X.509 lze importovat do úložiště certifikátů Javy podle pokynů v tématu o [přidání certifikátu do úložiště certifikátů certifikační autority Javy](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Po importu certifikátu do úložiště certifikátů se klienti rozhraní API SQL a Azure Cosmos DB pro MongoDB budou moct připojit k emulátoru Azure Cosmos.

Při připojování k emulátoru z pythonu a node.js SDK je ověření TLS zakázáno.

## <a name="command-line-tool-reference"></a><a id="command-line"></a>Reference nástroje příkazového řádku
Z umístění instalace můžete pomocí příkazového řádku spustit a zastavit emulátor, nakonfigurovat možnosti a provádět další operace.

### <a name="command-line-syntax"></a>Syntaxe příkazového řádku

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Pokud chcete zobrazit seznam možností, na příkazovém řádku zadejte `Microsoft.Azure.Cosmos.Emulator.exe /?`.

|**Možnost** | **Popis** | **Příkaz**| **Argumenty**|
|---|---|---|---|
|[Žádné argumenty] | Spustí emulátor Azure Cosmos s výchozím nastavením. |Microsoft.Azure.cosmos.Emulator.exe| |
|[Nápověda] |Zobrazí seznam podporovaných argumentů příkazového řádku.|Microsoft.Azure.cosmos.Emulator.exe /? | |
| GetStatus |Získá stav emulátoru Azure Cosmos. Stav je indikován ukončovacím kódem: 1 = spouštění, 2 = spuštěno, 3 = zastaveno. Záporný ukončovací kód označuje, že došlo k chybě. Žádný jiný výstup neexistuje. | Microsoft.Azure.cosmos.Emulator.exe /GetStatus| |
| Shutdown| Vypne emulátor Azure Cosmos.| Microsoft.Azure.cosmos.Emulator.exe /Vypnutí | |
|DataPath | Určuje cestu, do které chcete uložit datové soubory. Výchozí hodnota je %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<cesta k datům\> | \<cesta k datům\>: přístupná cesta |
|Port | Určuje číslo portu pro emulátor. Výchozí hodnota je 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<\> | \<port\>: číslo jednoho portu |
| ComputePort | Zadali číslo portu, které se má použít pro službu Compute Interop Gateway. Port sondy http služby brány se vypočítá jako ComputePort + 79. Proto computeport a ComputePort + 79 musí být otevřené a dostupné. Výchozí hodnota je 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>: Číslo jednoho portu |
| EnableMongoDbEndpoint=3.2 | Povolí Rozhraní API MongoDB 3.2 | Microsoft.Azure.cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Umožňuje MongoDB API 3.6 | Microsoft.Azure.cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Určuje číslo portu, který chcete použít pro rozhraní API kompatibility MongoDB. Výchozí hodnota je 10255. |Microsoft.Azure.cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongo port\>: číslo jednoho portu|
| PovolitCassandraEndpoint | Umožňuje Cassandra API | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Určuje číslo portu, které má být pro koncový bod Cassandra používáno. Výchozí hodnota je 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: Číslo jednoho portu |
| PovolitGremlinEndpoint | Povolí gremlin API | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Číslo portu pro koncový bod Gremlin. Výchozí hodnota je 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>: číslo jednoho portu |
|EnableTableEndpoint | Povolí rozhraní Azure Table API | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Číslo portu, které se má použít pro koncový bod tabulky Azure. Výchozí hodnota je 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>: číslo jednoho portu|
| Keyfile | Přečtěte si autorizační klíč ze zadaného souboru. Ke generování souboru klíčů použijte možnost /GenKeyFile. | Microsoft.Azure.cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Cesta k souboru |
| Obnovit cestu Dat | Rekurzivně odebere všechny soubory v zadané cestě. Pokud cestu nezadáte, bude výchozí hodnota %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<cesta> | \<cesta\>: Cesta k souboru  |
| StartTraces  |  Začněte shromažďovat protokoly trasování ladění pomocí LOGMAN. | Microsoft.Azure.cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Zastavit shromažďování protokoly trasování ladění pomocí LOGMAN. | Microsoft.Azure.cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Začněte shromažďovat protokoly trasování ladění pomocí nástroje Windows Performance Recording Tool. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Ukončení shromažďování protokolů trasování ladění pomocí nástroje Windows Performance Recording Tool. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Ve výchozím nastavení emulátor regeneruje svůj certifikát TLS/SSL podepsaný svým držitelem, pokud zabezpečení certifikátu neobsahuje název domény hostitele emulátoru, místní adresu IPv4, "localhost" a "127.0.0.1". S touto volbou emulátor se nezdaří při spuštění místo. Potom byste měli použít /GenCert možnost vytvořit a nainstalovat nový certifikát TLS/SSL s vlastním podpisem. | Microsoft.Azure.cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Vygenerujte a nainstalujte nový certifikát TLS/SSL podepsaný svým držitelem. volitelně včetně seznamu dalších názvů DNS oddělených čárkami pro přístup k emulátoru v síti. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>: Volitelný seznam dalších názvů DNS oddělených čárkami  |
| DirectPorts |Určuje porty, které chcete použít pro přímé připojení. Výchozí hodnoty jsou 10251,10252,10253,10254. | Microsoft.Azure.cosmos.Emulator.exe /DirectPorts:\<directports\> | \<přímé porty\>: seznam 4 portů oddělených čárkami |
| Klíč |Autorizační klíč pro emulátor. Klíč musí být 64bajtový vektor s kódováním base-64. | Microsoft.Azure.Cosmos.Emulator.exe /Klíč:\<klíč\> | \<klíč\>: klíč musí být 64bajtový vektor s kódováním base-64.|
| EnableRateLimiting | Určuje, že je povoleno chování omezující četnost požadavků. |Microsoft.Azure.cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Určuje, že je zakázáno chování omezující četnost požadavků. |Microsoft.Azure.cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Nezobrazuje uživatelské rozhraní emulátoru. | Microsoft.Azure.cosmos.Emulator.exe /NoUI | |
| NoExplorer | Nezobrazuje Průzkumníka dat při spuštění. |Microsoft.Azure.cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Určuje maximální počet dělených kontejnerů. Další informace [najdete v tématu Změna počtu kontejnerů.](#set-partitioncount) | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<počet oddílů\> | \<partitioncount\>: Maximální počet povolených kontejnerů s jedním oddílem. Výchozí hodnota je 25. Maximální povolený počet je 250.|
| DefaultPartitionCount| Určuje výchozí počet oddílů pro dělený kontejner. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<výchozí počet\> oddílů Výchozí hodnota je 25.|
| AllowNetworkAccess | Povolí přístup k emulátoru přes síť. Pokud chcete povolit přístup k síti, je nutné předat taky možnosti /Key =\<řetězec_klíče\> nebo/KeyFile =\<název_souboru\>. | Microsoft.Azure.cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> nebo Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Při použití možnosti /AllowNetworkAccess neupravujte pravidla brány firewall. |Microsoft.Azure.cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Vygeneruje nový autorizační klíč a uloží ho do zadaného souboru. Generovaný klíč lze použít s možností /Key nebo/KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<cesta ke souboru klíče\> | |
| Konzistence | Nastaví výchozí úroveň konzistence pro účet. | Konzistence microsoft.azure.cosmos.emulator.exe\</konzistence=\> | \<konzistence\>: hodnota musí být jedna z následujících [úrovní konzistence](consistency-levels.md): Session, Strong, Eventual nebo BoundedStaleness. Výchozí hodnota je Session. |
| ? | Zobrazí zprávu nápovědy.| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>Změna počtu kontejnerů

Ve výchozím nastavení můžete vytvořit až 25 kontejnerů s pevnou velikostí (podporované jenom pomocí sad Azure Cosmos DB SDK) nebo 5 neomezených kontejnerů pomocí emulátoru Azure Cosmos. Úpravou **PartitionCount** hodnotu, můžete vytvořit až 250 kontejnerů s pevnou velikostí nebo 50 neomezenýkontejnery nebo libovolnou kombinaci dvou, která nepřesahuje 250 kontejnery s pevnou velikostí (kde jeden neomezený kontejner = 5 kontejnerů s pevnou velikostí). Nedoporučuje se však nastavit emulátor tak, aby běžel s více než 200 kontejnery s pevnou velikostí. Vzhledem k režii, která přidá do operací vi diskových vi, které mají za následek nepředvídatelné časové limity při použití koncový bod API.

Pokud se pokusíte vytvořit kontejner po překročení aktuálního počtu oddílů, emulátor vyvolá výjimku ServiceUnavailable s následující zprávou.

"Je nám líto, v současné době máme v tomto regionu vysokou poptávku a v tuto chvíli nemůžeme splnit vaši žádost. Neustále pracujeme na tom, abychom na internetu přinášeli stále větší kapacitu, a doporučujeme vám to zkusit znovu.
ActivityId: 12345678-1234-1234-1234-123456789abc"

Chcete-li změnit počet kontejnerů dostupných v emulátoru Azure Cosmos, spusťte následující kroky:

1. Odstraňte všechna místní data emulátoru Azure Cosmos kliknutím pravým tlačítkem myši na ikonu **emulátoru Azure Cosmos DB** na hlavním panelu systému a potom klikněte na **tlačítko Obnovit data...**.
2. Odstranit všechna data emulátoru `%LOCALAPPDATA%\CosmosDBEmulator`v této složce .
3. Ukončete všechny otevřené instance tak, že kliknete pravým tlačítkem myši na ikonu **emulátoru služby Azure Cosmos DB** na hlavním panelu systému a potom kliknete na **Exit** (Konec). Ukončení všech instancí může chvíli trvat.
4. Nainstalujte nejnovější verzi [emulátoru Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Spusťte emulátor s příznakem PartitionCount nastaveným na hodnotu < = 250. Například: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Řízení emulátoru

Emulátor je dodáván s modulem Prostředí PowerShell pro spuštění, zastavení, odinstalaci a načtení stavu služby. Spusťte následující rutinu, chcete-li použít modul Prostředí PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

nebo umístěte `PSModules` adresář `PSModulesPath` na váš a importujte jej, jak je znázorněno v následujícím příkazu:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Zde je uveden seznam příkazů pro řízení emulátoru z PowerShellu:

### `Get-CosmosDbEmulatorStatus`

**Syntaxe**

`Get-CosmosDbEmulatorStatus`

**Poznámky**

Vrátí jednu z těchto hodnot ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running nebo ServiceControllerStatus.Stopped.

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

Emulátor Azure Cosmos se dá spouštět v Dockeru pro Windows. Emulátor nefunguje v aplikaci Docker for Oracle Linux.

Jakmile [Docker for Windows](https://www.docker.com/docker-windows) nainstalujete, přepněte na kontejnery Windows tak, že kliknete pravým tlačítkem myši na ikonu Dockeru na panelu nástrojů a vyberete **Switch to Windows containers** (Přepnout na kontejnery Windows).

Potom si spuštěním následujícího příkazu z vašeho oblíbeného prostředí stáhněte image emulátoru z Centra Dockeru.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Pokud chcete image spustit, spusťte následující příkazy.

Z příkazového řádku:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Pokud se při spuštění příkazu docker run zobrazí chyba konfliktu portu (zadaný port je již používán), můžete předat vlastní port změnou čísel portů. Můžete například změnit "-p 8081:8081" na "-p 443:8081"

Z PowerShellu:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

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

Nyní použijte koncový bod a hlavní klíč z odpovědi ve vašem klientovi a importujte certifikát TLS/SSL do hostitele. Chcete-li importovat certifikát TLS/SSL, postupujte z příkazového řádku správce následujícím:

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

Zavření interaktivní prostředí po spuštění emulátoru vypne kontejner emulátoru.

Pokud chcete otevřít Průzkumníka dat, přejděte v prohlížeči na následující adresu URL. Koncový bod emulátoru je uveden ve zprávě s odpovědí uvedené výše.

    https://<emulator endpoint provided in response>/_explorer/index.html

Pokud máte klientskou aplikaci .NET spuštěnou v kontejneru dockeru linuxu a pokud používáte emulátor Azure Cosmos na hostitelském počítači, postupujte podle následující části pro Linux importovat certifikát do kontejneru dockeru Linuxu.

## <a name="running-on-mac-or-linux"></a>Běh na Macu nebo Linuxu<a id="mac"></a>

V současné době cosmos emulátor lze spustit pouze v systému Windows. Uživatelé s Macem nebo Linuxem můžou spouštět emulátor ve virtuálním počítači windows hostovaném hypervisoru, jako jsou Parallels nebo VirtualBox. Níže jsou uvedeny kroky k povolení tohoto.

V rámci virtuálního provozu systému Windows spustit příkaz níže a poznamenejte si adresu IPv4.

```cmd
ipconfig.exe
```

V rámci aplikace je třeba změnit identifikátor URI použitý jako koncový bod, aby bylo nutné použít adresu IPv4 vrácenou `ipconfig.exe` místo `localhost`.

Dalším krokem je spuštění emulátoru Cosmos z příkazového řádku pomocí následujících možností.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Nakonec musíme importovat certifikát certifikační autority emulátoru do prostředí Linuxu nebo Macu.

### <a name="linux"></a>Linux

Pokud pracujete na Linuxu, .NET relé na OpenSSL provést ověření:

1. [Exportujte certifikát ve formátu PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX je k dispozici při výběru exportu soukromého klíče). 

1. Zkopírujte tento soubor PFX do vašeho linuxového prostředí.

1. Převést soubor PFX na soubor CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Zkopírujte soubor CRT do složky, která obsahuje vlastní certifikáty v distribuci Linuxu. Běžně na debianích distribucích `/usr/local/share/ca-certificates/`je umístěn na .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Aktualizujte certifikáty certifikační autority, které budou `/etc/ssl/certs/` aktualizovat složku.

   ```bash
   update-ca-certificates
   ```

### <a name="mac-os"></a>Mac OS

Pokud pracujete na Macu, postupujte takto:

1. [Exportujte certifikát ve formátu PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX je k dispozici při výběru exportu soukromého klíče).

1. Zkopírujte tento soubor PFX do prostředí Macu.

1. Otevřete aplikaci *Přístup ke řetězci klíčů* a importujte soubor PFX.

1. Otevřete seznam certifikátů a identifikujte certifikát s názvem `localhost`.

1. Otevřete místní nabídku pro tuto konkrétní položku, vyberte *Získat položku* a v části *Důvěřovat* > Při použití této možnosti*certifikátu* vyberte *Vždy důvěřovat*. 

   ![Otevřete místní nabídku pro tuto konkrétní položku, vyberte Získat položku a v části Důvěryhodnost – při použití této možnosti certifikátu vyberte Vždy důvěřovat.](./media/local-emulator/mac-trust-certificate.png)

Po provedení těchto kroků bude vaše prostředí důvěřovat certifikátu používanému emulátorem `/AllowNetworkAccess`při připojování k ip adrese, kterou poskytuje aplikace .

## <a name="troubleshooting"></a>Řešení potíží

Pomocí následujících tipů vám pomůžete vyřešit problémy, se kterými se setkáte s emulátorem Azure Cosmos:

- Pokud jste nainstalovali novou verzi emulátoru a dochází k chybám, proveďte obnovení dat. Data můžete obnovit kliknutím pravým tlačítkem myši na ikonu emulátoru Azure Cosmos na hlavním panelu systému a potom klikněte na obnovit data.... Pokud se tím chyby nevyřeší, můžete odinstalovat emulátor a všechny starší verze emulátoru, pokud je nalezen, odeberte adresář "C:\Program files\Azure Cosmos DB Emulátor" a znovu nainstalujte emulátor. Pokyny najdete v části [Odinstalace místního emulátoru](#uninstall).

- Pokud dojde k chybě emulátoru Azure Cosmos, shromažďujte soubory s výpisem stavu paměti ze složky %LOCALAPPDATA%\CrashDumps, komprimujte je a otevřete lístek podpory z [portálu Azure](https://portal.azure.com).

- Pokud dojde k `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`chybě v , může to být příznak, kde čítače výkonu jsou v poškozeném stavu. Problém se problémem obvykle řeší spuštění následujícího příkazu z příkazového řádku správce:

  ```cmd
  lodctr /R
   ```

- Pokud narazíte na problém s připojením, [shromážděte trasovací soubory](#trace-files), komprimujte je a otevřete lístek podpory na [webu Azure Portal](https://portal.azure.com).

- Pokud se zobrazí zpráva **Služba není dostupná**, pravděpodobně se emulátoru nedaří inicializovat sadu síťových protokolů. Zkontrolujte, zda máte nainstalovaného klienta Pulse Secure nebo klienta Juniper Networks, protože potíže mohou způsobovat jejich ovladače síťových filtrů. Odinstalace ovladačů síťových filtrů třetích stran obvykle potíže vyřeší. Případně spusťte emulátor s /DisableRIO, který přepne síťovou komunikaci emulátoru na běžné Winsock. 

- Pokud emulátor běží, když počítač přechází do režimu spánku nebo instaluje nějaké aktualizace operačního systému, může se zobrazit zpráva, že **služba momentálně není dostupná**. Obnovte data emulátoru kliknutím pravým tlačítkem myši na ikonu, která se zobrazí na panelu s oznámením systému Windows, a vyberte **obnovit data**.

### <a name="collect-trace-files"></a><a id="trace-files"></a>Shromažďování trasovacích souborů

Pokud chcete shromažďovat trasovací soubory pro ladění, spusťte z příkazového řádku pro správu následující příkazy:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. Sledujte hlavní panel systému a ujistěte se, že program je vypnutý. Může to chvíli trvat. Můžete taky kliknout na **Exit** v uživatelském rozhraní Emulátoru Azure Cosmos.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Reprodukujte problém. Pokud Průzkumník dat nefunguje, stačí několik sekund čekat na otevření prohlížeče a zachytit chybu.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Přejděte do složky `%ProgramFiles%\Azure Cosmos DB Emulator` a vyhledejte soubor docdbemulator_000001.etl.
8. Otevřete lístek podpory na [webu Azure portal](https://portal.azure.com) a zahrnout soubor .etl spolu s kroky reprodukci.

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Odinstalace místního emulátoru

1. Ukončit všechny otevřené instance místního emulátoru kliknutím pravým tlačítkem myši na ikonu Emulátor u Azure Cosmos na hlavním panelu systému a potom klepněte na tlačítko Ukončit. Ukončení všech instancí může chvíli trvat.
2. Do vyhledávacího pole ve Windows zadejte **Programy a funkce** a klikněte na výsledek **Programy a funkce (nastavení systému)**.
3. V seznamu aplikací se posuňte na položku **Azure Cosmos DB Emulator**, vyberte ji, klikněte na **Odinstalovat**, potvrďte a znovu klikněte na **Odinstalovat**.
4. Když je aplikace odinstalovaná, přejděte do složky `%LOCALAPPDATA%\CosmosDBEmulator` a odstraňte ji.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat místní emulátor pro bezplatný místní vývoj. Nyní můžete přejít k dalšímu kurzu a naučit se exportovat certifikáty TLS/SSL emulátoru.

> [!div class="nextstepaction"]
> [Export certifikátů emulátoru Azure Cosmos](local-emulator-export-ssl-certificates.md)
