---
title: Přesunutí dat na sql server a z nich
description: Zjistěte, jak přesunout data do/z databáze SQL Serveru, která je místní nebo v virtuálním počítači Azure pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5e4bbe1e6bd944787d47c5e3ed98de582c088a52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265763"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Přesunutí dat do a z SQL Serveru místně nebo na IaaS (Azure VM) pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-sqlserver-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sql-server.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor SQL Server ve verzi 2](../connector-sql-server.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat do nebo z místní databáze SQL Serveru. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Podporované scénáře
Data z **databáze serveru SQL Server** můžete zkopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat **do databáze serveru SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Podporované verze serveru SQL Server
Tento konektor SQL Serveru podporuje kopírování dat z/do následujících verzí instancí hostovaných místně nebo v Azure IaaS pomocí ověřování SQL i ověřování windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Povolení připojení
Koncepty a kroky potřebné pro připojení k SQL Server hostované místně nebo v Azure IaaS (infrastruktura jako služba) virtuální počítače jsou stejné. V obou případech je nutné použít bránu pro správu dat pro připojení.

Podívejte [se na pohybující se data mezi místními lokacemi a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem, kde najdete informace o bráně pro správu dat a podrobné pokyny k nastavení brány. Nastavení instance brány je předpokladem pro připojení k serveru SQL Server.

Zatímco můžete nainstalovat bránu na stejné místní počítače nebo cloudové instanci virtuálních počítačů jako SQL Server pro lepší výkon, doporučujeme nainstalovat je na samostatných počítačích. S brána a SQL Server na samostatných počítačích snižuje konflikty prostředků.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z místní databáze SERVERU SQL Server pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Továrna dat může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat. Například pokud kopírujete data z databáze SQL Serveru do úložiště objektů blob Azure, vytvoříte dvě propojené služby pro propojení databáze SERVERU SQL Server a účtu úložiště Azure s vaší továrně dat. Vlastnosti propojené služby, které jsou specifické pro databázi serveru SQL Server, naleznete v části [Vlastnosti propojené služby.](#linked-service-properties)
3. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu pro určení tabulky SQL v databázi serveru SQL Server, která obsahuje vstupní data. A můžete vytvořit další datovou sadu k určení kontejneru objektů blob a složky, která obsahuje data zkopírovaná z databáze serveru SQL Server. Vlastnosti datové sady, které jsou specifické pro databázi serveru SQL Server, naleznete v části [Vlastnosti datové sady.](#dataset-properties)
4. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu uvedeném výše použít SqlSource jako zdroj a BlobSink jako jímky pro aktivitu kopírování. Podobně pokud kopírujete z azure blob storage do databáze SQL Serveru, použijete v aktivitě kopírování objekt blobSource a SqlSink. Informace o vlastnostech aktivity kopírování, které jsou specifické pro databázi serveru SQL Server, naleznete v části [Kopírování vlastností aktivity.](#copy-activity-properties) Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, klikněte na odkaz v předchozí části úložiště dat.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázky s definicemi JSON pro entity Factory dat, které se používají ke kopírování dat do nebo z místní databáze SQL Serveru, naleznete v části [JSON příklady](#json-examples-for-copying-data-from-and-to-sql-server) tohoto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro SQL Server:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Vytvoříte propojenou službu typu **OnPremisesSqlServer** pro propojení místní databáze serveru SQL Server s totožnou sítí dat. Následující tabulka obsahuje popis prvků JSON specifických pro místní propojenou službu SQL Server.

Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu SQL Server.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu by měla být nastavena **na: OnPremisesSqlServer**. |Ano |
| připojovací řetězec |Zadejte informace connectionString potřebné pro připojení k místní databázi serveru SQL Server pomocí ověřování SQL nebo ověřování systému Windows. |Ano |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní databázi serveru SQL Server. |Ano |
| uživatelské jméno |Pokud používáte ověřování systému Windows, zadejte uživatelské jméno. Příklad: **uživatelské\\jméno domény**. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |

Pověření můžete šifrovat pomocí rutiny **New-AzDataFactoryEncryptValue** a použít je v připojovacím řetězci, jak je znázorněno v následujícím příkladu (**Vlastnost EncryptedCredential):**

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>ukázky
**JSON pro použití ověřování SQL**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON pro použití ověřování systému Windows**

Brána pro správu dat bude zosobnit zadaný uživatelský účet pro připojení k místní databázi serveru SQL Server.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Ve vzorcích jste použili datovou sadu typu **SqlServerTable** k reprezentaci tabulky v databázi serveru SQL Server.

Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON jsou podobné pro všechny typy datových sad (SQL Server, Azure blob, Azure tabulka, atd.).

Sekce typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu typu **SqlServerTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci databáze serveru SQL Server, na které odkazuje propojená služba. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Pokud přesouváte data z databáze serveru SQL Server, nastavíte typ zdroje v aktivitě kopírování na **SqlSource**. Podobně pokud přesouváte data do databáze serveru SQL Server, nastavíte typ jímky v aktivitě kopírování do **aplikace SqlSink**. Tato část obsahuje seznam vlastností podporovaných sqlsource a sqlsink.

Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování trvá pouze jeden vstup a vytváří pouze jeden výstup.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v typeProperties části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

### <a name="sqlsource"></a>Zdroj Sql
Pokud je zdroj v aktivitě kopírování typu **SqlSource**, jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Příklad: vyberte * z MyTable. Může odkazovat na více tabulek z databáze, na kterou odkazuje vstupní datová sada. Pokud není zadán, příkaz SQL, který je proveden: vyberte z MyTable. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |

Pokud je pro sqlSource zadán **sqlReaderQuery,** spustí aktivita kopírování tento dotaz proti zdroji databáze serveru SQL Server, aby získala data.

Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura se používají k vytvoření výběrového dotazu spustit proti databázi serveru SQL Server. Pokud definice datové sady nemá strukturu, jsou z tabulky vybrány všechny sloupce.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat hodnotu **vlastnosti tableName** v datové sadě JSON. V šaku tabulce nejsou provedena žádná ověření.

### <a name="sqlsink"></a>Systém SqlSink
**SqlSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte čas pro operaci dávkové vložení k dokončení před časovým výpadkem. |Timespan<br/><br/> Příklad: "00:30:00" (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, který se má provést tak, aby byla vyčištěna data určitého řezu. Další informace naleznete v části [opakovatelné kopie.](#repeatable-copy) |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, který se má vyplnit automaticky generovaným identifikátorem řezu, který se používá k vyčištění dat určitého řezu při opětovném spuštění. Další informace naleznete v části [opakovatelné kopie.](#repeatable-copy) |Název sloupce s datovým typem binárního(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak aplikovat zdrojová data do cílové tabulky, například k upserts nebo transformaci pomocí vlastní obchodní logiky. <br/><br/>Všimněte si, že tato uložená procedura bude **vyvolána na dávku**. Pokud chcete provést operaci, která běží pouze jednou a nemá nic společného se zdrojovými daty, např. `sqlWriterCleanupScript` |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název typu tabulky, který má být použit v uložené proceduře. Aktivita kopírování zpřístupňuje data přesunutá v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit zkopírovaná data s existujícími daty. |Název typu tabulky. |Ne |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Příklady JSON pro kopírování dat z a na SQL Server
Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Následující ukázky ukazují, jak kopírovat data do a z SQL Server a Azure Blob Storage. Data však můžete zkopírovat **přímo** z libovolného zdroje do libovolného [jímky uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Příklad: Kopírování dat z SQL Serveru do objektu blob Azure
Následující ukázka ukazuje:

1. Propojená služba typu [OnPremisesSqlServer](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [SqlServerTable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou Copy, která používá [SqlSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data časových řad z tabulky serveru SQL Server do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

Jako první krok nastavte bránu pro správu dat. Pokyny jsou v [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

**Propojená služba SQL Server**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Propojená služba úložiště objektů blob Azure**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Vstupní datová sada serveru SQL Server**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v SQL Server a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad. Můžete dotaz přes více tabulek v rámci stejné databáze pomocí jedné datové sady, ale jedna tabulka musí být použita pro dataset tableName typeProperty.

Nastavení "externí": "true" informuje službu Data Factory, že datová sada je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Kanál s aktivitou Kopírování**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití těchto vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **SqlSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data za poslední hodinu ke kopírování.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
V tomto příkladu **sqlReaderQuery** je určen pro SqlSource. Aktivita kopírování spustí tento dotaz proti zdroji databáze serveru SQL Server, aby získala data. Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (pokud uložená procedura přebírá parametry). SqlReaderQuery může odkazovat na více tabulek v rámci databáze, na které odkazuje vstupní datová sada. Není omezena pouze na tabulku nastavenou jako vlastnost tableName vlastnosti datové sady.

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura se používají k vytvoření výběrového dotazu spustit proti databázi serveru SQL Server. Pokud definice datové sady nemá strukturu, jsou z tabulky vybrány všechny sloupce.

Seznam vlastností podporovaných technologiemi SqlSource a BlobSink naleznete v části [Zdroj sql](#sqlsource) a [blobsink.](data-factory-azure-blob-connector.md#copy-activity-properties)

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Příklad: Kopírování dat z objektu Blob Azure na SQL Server
Následující ukázka ukazuje:

1. Propojená služba typu [OnPremisesSqlServer](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s kopírovat aktivitu, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a SqlSink.

Ukázka zkopíruje data časových řad z objektu blob Azure do tabulky SQL Serveru každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Propojená služba SQL Server**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Propojená služba úložiště objektů blob Azure**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Vstupní datová sada objektů Blob Azure**

Data se zírají z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt blob jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá rok, měsíc a den část počátečního času a název souboru používá hodinovou část počátečního času. "externí": "true" nastavení informuje službu Data Factory, že datová sada je externí pro datové továrny a není vyráběna aktivitou v datové továrně.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Výstupní datová sada serveru SQL Server**

Ukázka zkopíruje data do tabulky s názvem "MyTable" v SQL Server. Vytvořte tabulku na serveru SQL Server se stejným počtem sloupců, jako u očekáváte, že soubor BLOB CSV bude obsahovat. Nové řádky jsou přidávány do tabulky každou hodinu.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Kanál s aktivitou Kopírování**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití těchto vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **objekt BlobSource** a typ **jímky** je nastaven na **sqlsink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": " SqlServerOutput "
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

## <a name="troubleshooting-connection-issues"></a>Řešení potíží s připojením
1. Nakonfigurujte sql server tak, aby přijímal vzdálená připojení. Spusťte **aplikaci SQL Server Management Studio**, klepněte pravým tlačítkem myši na **server**a klepněte na příkaz **Vlastnosti**. V seznamu vyberte **Možnost Připojení** a **zaškrtněte políčko Povolit vzdálená připojení k serveru**.

    ![Povolit vzdálená připojení](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Podrobné kroky naleznete [v tématu Konfigurace serveru pro vzdálený přístup.](https://msdn.microsoft.com/library/ms191464.aspx)
2. Spusťte **nástroj SQL Server Configuration Manager**. Rozbalte **konfiguraci sítě serveru SQL Server** pro požadovanou instanci a vyberte **protokoly pro mssqlserver**. V pravém podokně byste měli vidět protokoly. Povolte protokol TCP/IP klepnutím pravým tlačítkem myši na **protokol TCP/IP** a klepnutím na příkaz **Povolit**.

    ![Povolit protokol TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Podrobnosti a alternativní způsoby povolení protokolu TCP/IP [naleznete v tématu Povolení nebo zakázání síťového protokolu serveru.](https://msdn.microsoft.com/library/ms191294.aspx)
3. Ve stejném okně poklepejte na **protokol TCP/IP** a spusťte okno **Vlastnosti protokolu TCP/IP.**
4. Přepněte na kartu **IP adresy.** Posuňte se dolů a podívejte se na oddíl **IPAll.** Poznamenejte si **port TCP**(výchozí hodnota je **1433**).
5. Vytvořte **pravidlo pro bránu Windows Firewall** v počítači, které umožní příchozí přenosy prostřednictvím tohoto portu.
6. **Ověření připojení**: Chcete-li se připojit k serveru SQL Server pomocí plně kvalifikovaného názvu, použijte aplikaci SQL Server Management Studio z jiného počítače. Například:\<"\>stroj . \<domény\>\<.corp.\>společnosti .com,1433."

   > [!IMPORTANT]
   > 
   > Podrobné informace najdete [v tématu Přesun dat mezi místními zdroji a cloudem pomocí brány pro správu dat.](data-factory-move-data-between-onprem-and-cloud.md)
   > 
   > Tipy týkající se řešení problémů s připojením nebo bránou najdete v [tématu Poradce při potížích](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) s bránou.


## <a name="identity-columns-in-the-target-database"></a>Sloupce identity v cílové databázi
Tato část obsahuje příklad, který kopíruje data ze zdrojové tabulky bez sloupce identity do cílové tabulky se sloupcem identity.

**Zdrojová tabulka:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Cílová tabulka:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Všimněte si, že cílová tabulka má sloupec identity.

**Definice zdrojového datového souboru JSON**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definice JSON cílové datové sady**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Všimněte si, že jako zdroj a cílová tabulka mají různé schéma (cíl má další sloupec s identitou). V tomto scénáři je třeba zadat vlastnost **struktury** v definici cílové datové sady, která neobsahuje sloupec identity.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Vyvolat uloženou proceduru z jímky SQL
Viz [Invoke uložená procedura pro jímky SQL v](data-factory-invoke-stored-procedure-from-copy-activity.md) článku aktivity kopírování pro příklad vyvolání uložené procedury z jímky SQL v kopii aktivity kanálu.

## <a name="type-mapping-for-sql-server"></a>Mapování typů pro sql server
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) aktivita Copy provádí automatické převody typů z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat do & ze serveru SQL se používají následující mapování z typu SQL na typ .NET a naopak.

Mapování je stejné jako mapování datového typu serveru SQL Server pro ADO.NET.

| Typ databázového stroje serveru SQL Server | Typ rozhraní .NET Framework |
| --- | --- |
| bigint |Int64 |
| binární |Bajt[] |
| bitové |Logická hodnota |
| char |Řetězec, Znak[] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Desetinné číslo |Desetinné číslo |
| ATRIBUT FILESTREAM (varbinary(max)) |Bajt[] |
| Plovoucí desetinná čárka |Double |
| image |Bajt[] |
| int |Int32 |
| Peníze |Desetinné číslo |
| Nchar |Řetězec, Znak[] |
| Ntext |Řetězec, Znak[] |
| numerické |Desetinné číslo |
| nvarchar |Řetězec, Znak[] |
| reálná |Single |
| Rowversion |Bajt[] |
| Smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Desetinné číslo |
| Sql_variant |Objekt * |
| text |Řetězec, Znak[] |
| time |TimeSpan |
| časové razítko |Bajt[] |
| tinyint |Byte |
| uniqueidentifier |Identifikátor GUID |
| Varbinary |Bajt[] |
| varchar |Řetězec, Znak[] |
| xml |XML |

## <a name="mapping-source-to-sink-columns"></a>Mapování zdroje na jímacích sloupců
Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Opakovatelná kopie
Při kopírování dat do databáze serveru SQL Server aktivita připojí data do tabulky jímky ve výchozím nastavení. Chcete-li provést UPSERT místo toho, viz opakovatelný zápis do článku [SqlSink.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
