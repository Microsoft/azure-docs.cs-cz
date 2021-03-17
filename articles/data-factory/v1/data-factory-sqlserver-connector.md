---
title: Přesun dat do a z SQL Server
description: Přečtěte si, jak přesunout data do nebo z SQL Server databáze, která je místní nebo na virtuálním počítači Azure pomocí Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fbd1e1d652db3bbd91344ea828278d057baeb060
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368808"
---
# <a name="move-data-to-and-from-sql-server-using-azure-data-factory"></a>Přesun dat do a z SQL Server pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-sqlserver-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sql-server.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor SQL Server v v2](../connector-sql-server.md).

Tento článek vysvětluje, jak používat aktivitu kopírování v Azure Data Factory k přesunu dat do a z databáze SQL Server. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Podporované scénáře
Data **z databáze SQL Server** můžete kopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat **do databáze SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Podporované verze SQL Server
Tento SQL Server konektor podporuje kopírování dat z/do následujících verzí hostovaných místně nebo v Azure IaaS pomocí ověřování SQL a ověřování systému Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Povolení připojení
Koncepty a kroky potřebné pro připojení SQL Server hostovaných místně nebo v Azure IaaS (infrastruktura jako služba) jsou stejné. V obou případech je potřeba použít bránu Správa dat pro připojení.

Další informace o Správa dat bráně a podrobné pokyny k nastavení brány najdete v tématu [přesun dat mezi místními umístěními a v cloudovém](data-factory-move-data-between-onprem-and-cloud.md) článku. Nastavení instance brány je předpokladem pro připojení k SQL Server.

I když můžete nainstalovat bránu na stejný místní počítač nebo na instanci cloudového virtuálního počítače jako SQL Server pro lepší výkon, doporučujeme nainstalovat je na samostatné počítače. Brána a SQL Server v samostatných počítačích snižuje kolize prostředků.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do/z databáze SQL Server pomocí různých nástrojů/rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Datová továrna může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou. Pokud například kopírujete data z databáze SQL Server do úložiště objektů BLOB v Azure, vytvoříte dvě propojené služby, které propojí vaši databázi SQL Server a účet Azure Storage s datovou továrnou. Vlastnosti propojené služby, které jsou specifické pro službu SQL Server Database, najdete v části [vlastnosti propojených služeb](#linked-service-properties) .
3. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu pro určení tabulky SQL ve vaší databázi SQL Server, která obsahuje vstupní data. A vytvoříte další datovou sadu pro určení kontejneru objektů BLOB a složky, která obsahuje data zkopírovaná z databáze SQL Server. Vlastnosti datové sady, které jsou specifické pro SQL Server databázi, najdete v části [Vlastnosti datové sady](#dataset-properties) .
4. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. V předchozím příkladu použijete jako jímku aktivity kopírování SqlSource jako zdroj a BlobSink. Podobně pokud kopírujete z Azure Blob Storage do databáze SQL Server, v aktivitě kopírování použijete BlobSource a SqlSink. Informace o vlastnostech aktivity kopírování, které jsou specifické pro SQL Server databázi, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) . Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, získáte kliknutím na odkaz v předchozí části úložiště dat.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázky s definicemi JSON pro Entity Data Factory používané ke kopírování dat do nebo z databáze SQL Server najdete v části [Příklady JSON](#json-examples-for-copying-data-from-and-to-sql-server) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro SQL Server:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Vytvoříte propojenou službu typu **OnPremisesSqlServer** , která bude propojit databázi SQL Server s datovou továrnou. Následující tabulka uvádí popis pro prvky JSON specifické pro SQL Server propojenou službu.

Následující tabulka uvádí popis pro prvky JSON specifické pro SQL Server propojenou službu.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type by měla být nastavená na: **OnPremisesSqlServer**. |Yes |
| připojovací řetězec |Zadejte informace připojovacího řetězce potřebné pro připojení k databázi SQL Server pomocí ověřování SQL nebo ověřování systému Windows. |Yes |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k databázi SQL Server. |Yes |
| username |Pokud používáte ověřování systému Windows, zadejte uživatelské jméno. Příklad: **DomainName \\ uživatelské_jméno**. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |

Přihlašovací údaje můžete šifrovat pomocí rutiny **New-AzDataFactoryEncryptValue** a použít je v připojovacím řetězci, jak je znázorněno v následujícím příkladu (vlastnost **EncryptedCredential** ):

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

Správa dat brána zosobní zadaný uživatelský účet pro připojení k databázi SQL Server.

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
V ukázkách jste použili datovou sadu typu **SQLServer** , která představuje tabulku v databázi SQL Server.

Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada formátu JSON datové sady, jsou podobné pro všechny typy datových sad (SQL Server, Azure Blob, tabulka Azure atd.).

Oddíl typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu typu **SQLServer** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci databáze SQL Server, na kterou odkazuje propojená služba |Yes |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Pokud přesouváte data z databáze SQL Server, nastavíte typ zdroje v aktivitě kopírování na **SqlSource**. Podobně Pokud přesouváte data do databáze SQL Server, nastavíte typ jímky v aktivitě kopírování na **SqlSink**. V této části najdete seznam vlastností podporovaných SqlSource a SqlSink.

Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásady.

> [!NOTE]
> Aktivita kopírování používá pouze jeden vstup a vytváří pouze jeden výstup.

V takovém případě se vlastnosti dostupné v části typeProperties v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

### <a name="sqlsource"></a>SqlSource
Pokud je zdroj v aktivitě kopírování typu **SqlSource**, jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| sqlReaderQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: SELECT * FROM MyTable. Může odkazovat na více tabulek z databáze, na kterou odkazuje vstupní datová sada. Pokud není zadaný, provede se příkaz SQL: vyberte z MyTable. |No |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |

Pokud je pro SqlSource určena **sqlReaderQuery** , aktivita kopírování spustí tento dotaz proti SQL Server zdroji databáze, aby získala data.

Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přijímá parametry).

Pokud nezadáte buď sqlReaderQuery nebo sqlReaderStoredProcedureName, jsou sloupce definované v oddílu Structure použity k vytvoření dotazu SELECT pro spuštění v databázi SQL Server. Pokud definice datové sady nemá strukturu, všechny sloupce jsou vybrány z tabulky.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName** je stále nutné zadat hodnotu pro vlastnost **TableName** v datové sadě JSON. V této tabulce neexistují žádná ověření, která by byla provedena.

### <a name="sqlsink"></a>SqlSink
**SqlSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit. |timespan<br/><br/> Příklad: "00:30:00" (30 minut). |No |
| writeBatchSize |Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky SQL. |Integer (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, která se má provést, aby se vyčistila data konkrétního řezu. Další informace najdete v části s možností [opakovaného kopírování](#repeatable-copy) . |Příkaz dotazu. |No |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, která se má vyplnit automaticky generovaným identifikátorem řezu, který se použije k vyčištění dat určitého řezu při opakovaném spuštění. Další informace najdete v části s možností [opakovaného kopírování](#repeatable-copy) . |Název sloupce sloupce s datovým typem Binary (32). |No |
| sqlWriterStoredProcedureName |Název uložené procedury definující, jak se mají použít zdrojová data na cílovou tabulku, například upsertuje nebo transformaci pomocí vlastní obchodní logiky. <br/><br/>Poznámka: Tato uložená procedura se **vyvolá na každou dávku**. Pokud chcete provést operaci, která se spustí pouze jednou a nemá nic ke zdroji dat, např. Delete/zkrácení, použijte `sqlWriterCleanupScript` vlastnost. |Název uložené procedury |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |
| sqlWriterTableType |Zadejte název typu tabulky, který se použije v uložené proceduře. Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data zkopírovaná se stávajícími daty. |Název typu tabulky |No |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Příklady JSON pro kopírování dat z a do SQL Server
V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Následující ukázky ukazují, jak kopírovat data z SQL Server a z Azure Blob Storage. Data se ale dají zkopírovat **přímo** z libovolného zdroje do kterékoli z těchto umyvadel, které jsou [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedené, pomocí aktivity kopírování v Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Příklad: kopírování dat z SQL Server do objektu blob Azure
Následující příklad ukazuje:

1. Propojená služba typu [OnPremisesSqlServer](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [SQLServer](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data časových řad z SQL Server tabulky do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat. Pokyny najdete v článku [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

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
**Propojená služba Azure Blob Storage**

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
**Vstupní datová sada SQL Server**

Ukázka předpokládá, že jste v SQL Server vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady. Můžete zadávat dotazy na více tabulek ve stejné databázi pomocí jedné datové sady, ale pro vlastnost tableName typeProperty datové sady se musí použít jedna tabulka.

Nastavení "externí": "true" informuje Data Factory služby, že datová sada je pro objekt pro vytváření dat externá a není vytvořená aktivitou v datové továrně.

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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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
**Kanál s aktivitou kopírování**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala tyto vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **SqlSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data během uplynulé hodiny ke zkopírování.

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
V tomto příkladu je **sqlReaderQuery** určena pro SqlSource. Aktivita kopírování spustí tento dotaz proti SQL Server zdroji databáze, aby získala data. Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přijímá parametry). SqlReaderQuery může odkazovat na více tabulek v rámci databáze, na kterou odkazuje vstupní datová sada. Není omezena pouze na sadu tabulek, jako je typeProperty tableName sady dat.

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, použijí se sloupce definované v oddílu Structure k vytvoření dotazu SELECT pro spuštění v databázi SQL Server. Pokud definice datové sady nemá strukturu, všechny sloupce jsou vybrány z tabulky.

Seznam vlastností podporovaných SqlSource a BlobSink najdete v části [zdroje SQL](#sqlsource) a v [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Příklad: kopírování dat z objektu blob Azure do SQL Server
Následující příklad ukazuje:

1. Propojená služba typu [OnPremisesSqlServer](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [SQLServer](data-factory-sqlserver-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a SqlSink.

Ukázka kopíruje data časových řad z objektu blob Azure do tabulky SQL Server každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

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
**Propojená služba Azure Blob Storage**

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
**Vstupní datová sada Azure Blob**

Data se vybírají z nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt BLOB jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část rok, měsíc a den v čase zahájení a název souboru používá hodinovou část času spuštění. nastavení externí: "true" informuje službu Data Factory o tom, že datová sada je externí pro objekt pro vytváření dat a není vytvořená aktivitou v datové továrně.

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
**Výstupní datová sada SQL Server**

Ukázka zkopíruje data do tabulky s názvem "MyTable" v SQL Server. Vytvoří tabulku v SQL Server se stejným počtem sloupců, protože očekáváte, že soubor CSV BLOB bude obsahovat. Nové řádky jsou do tabulky přidány každou hodinu.

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
**Kanál s aktivitou kopírování**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala tyto vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **BlobSource** a typ **jímky** je nastavený na **SqlSink**.

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
1. Nakonfigurujte SQL Server, aby přijímala vzdálená připojení. Spusťte **SQL Server Management Studio**, klikněte pravým tlačítkem na **Server** a pak klikněte na **vlastnosti**. V seznamu vyberte **připojení** a zaškrtněte políčko **povoluje vzdálená připojení k serveru**.

    ![Povolit vzdálená připojení](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Podrobné pokyny najdete v části [Konfigurace serveru vzdáleného přístupu](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option) .
2. Spusťte **SQL Server Configuration Manager**. Rozbalte **SQL Server konfigurace sítě** pro požadovanou instanci a vyberte **protokoly pro MSSQLSERVER**. V pravém podokně by se měly zobrazit protokoly. Povolte protokol TCP/IP kliknutím pravým tlačítkem na **TCP/IP** a kliknutím na **Povolit**.

    ![Povolit protokol TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Podrobnosti a alternativní způsoby povolení protokolu TCP/IP najdete v tématu [Povolení nebo zakázání síťového protokolu serveru](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol) .
3. Ve stejném okně poklikejte na **TCP/IP** a spustí se okno **vlastností protokolu TCP/IP** .
4. Přepněte na kartu **IP adresy** . Posuňte se dolů a podívejte se na část **IPAll** . Poznamenejte si **port TCP**(výchozí hodnota je **1433**).
5. Vytvořte **pravidlo pro bránu Windows Firewall** na počítači, aby se povolil příchozí přenos prostřednictvím tohoto portu.
6. **Ověřit připojení**: Pokud se chcete připojit k SQL Server pomocí plně kvalifikovaného názvu, použijte SQL Server Management Studio z jiného počítače. Například: " \<machine\> . \<domain\> . Corp. \<company\> .. com, 1433. "

   > [!IMPORTANT]
   > 
   > Podrobné informace najdete v tématu [přesun dat mezi místními zdroji a cloudem pomocí služby Správa dat Gateway](data-factory-move-data-between-onprem-and-cloud.md) .
   > 
   > Tipy k odstraňování potíží souvisejících s připojením nebo bránou najdete v tématu řešení potíží s [bránou](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .


## <a name="identity-columns-in-the-target-database"></a>Sloupce identity v cílové databázi
V této části najdete příklad, který kopíruje data ze zdrojové tabulky bez sloupce identity do cílové tabulky se sloupcem identity.

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

**Definice JSON zdrojové datové sady**

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

Všimněte si, že protože vaše zdrojová a cílová tabulka mají jiné schéma (cíl má další sloupec s identitou). V tomto scénáři je nutné zadat vlastnost **struktury** v definici cílové datové sady, která neobsahuje sloupec identity.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Vyvolat uloženou proceduru z jímky SQL
Příklad vyvolání uložené procedury z jímky SQL v aktivitě kopírování kanálu najdete v tématu [vyvolání uložené procedury pro JÍMKU SQL v článku aktivita kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md) .

## <a name="type-mapping-for-sql-server"></a>Mapování typů pro SQL Server
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím přístupem ke dvěma krokům:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat na & z SQL serveru se z typu SQL do typu .NET, a naopak, používají následující mapování.

Mapování je stejné jako SQL Server mapování datových typů pro ADO.NET.

| Typ databázového stroje SQL Server | Typ rozhraní .NET Framework |
| --- | --- |
| bigint |Int64 |
| binární |Byte [] |
| bit |Logická hodnota |
| char |Řetězec, znak [] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary (max)) |Byte [] |
| Float |dvojité |
| image |Byte [] |
| int |Int32 |
| papír |Decimal |
| nchar |Řetězec, znak [] |
| ntext |Řetězec, znak [] |
| numerické |Decimal |
| nvarchar |Řetězec, znak [] |
| real |Jednoduché |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Předmětů |
| text |Řetězec, znak [] |
| time |TimeSpan |
| časové razítko |Byte [] |
| tinyint |Byte |
| uniqueidentifier |Identifikátor GUID |
| varbinary |Byte [] |
| varchar |Řetězec, znak [] |
| xml |XML |

## <a name="mapping-source-to-sink-columns"></a>Mapování zdroje na sloupce jímky
Chcete-li mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Opakující se kopie
Při kopírování dat do SQL Server databáze aktivita kopírování ve výchozím nastavení připojí data do tabulky jímky. Pokud chcete místo toho provést UPSERT, přečtěte si článek s [možností opakovaného zápisu do SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) .

Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .