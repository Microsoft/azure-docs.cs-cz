---
title: Kopírování dat do/z databáze Azure SQL Database
description: Zjistěte, jak kopírovat data do/z Azure SQL Database pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7fc0b2822195d952c2a4f9c02bf3758c0e2b809a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260498"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-azure-sql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-sql-database.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Konektor azure SQL database ve V2](../connector-azure-sql-database.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunu dat do a z Azure SQL Database. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Data z **Azure SQL Database** můžete zkopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat **do azure sql database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Podporovaný typ ověřování
Konektor Azure SQL Database podporuje základní ověřování.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z databáze Azure SQL pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Továrna dat může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat. Například pokud kopírujete data z úložiště objektů blob Azure do databáze Azure SQL, vytvoříte dvě propojené služby, které propojí váš účet úložiště Azure a databázi Azure SQL s vaší továrně dat. Vlastnosti propojené služby, které jsou specifické pro Azure SQL Database, najdete v tématu [vlastnosti propojené služby](#linked-service-properties) části.
3. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu, která určí kontejner objektů blob a složku, která obsahuje vstupní data. A vytvoříte další datovou sadu k určení tabulky SQL v databázi Azure SQL, která obsahuje data zkopírovaná z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure Data Lake Store, najdete v tématu [vlastnosti datové sady](#dataset-properties) části.
4. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu uvedeném výše použijete Objekt blobSource jako zdroj a SqlSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z Azure SQL Database do azure blob storage, použijete sqlsource a blobsink v aktivitě kopírování. Informace o vlastnostech aktivity kopírování, které jsou specifické pro Azure SQL Database, najdete v tématu [kopírování vlastností aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, klikněte na odkaz v předchozí části úložiště dat.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázky s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat do/z databáze Azure SQL, najdete v části [příklady JSON](#json-examples-for-copying-data-to-and-from-sql-database) v tomto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Datové továrny specifické pro Azure SQL Database:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Propojená služba Azure SQL propojuje databázi Azure SQL s vaší továrně dat. Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu Azure SQL.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena na: **AzureSqlDatabase.** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost connectionString. Je podporováno pouze základní ověřování. |Ano |

> [!IMPORTANT]
> Nakonfigurujte databázový server [Azure SQL Database Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) tak, aby [umožňoval přístup ke serveru službám Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Navíc pokud kopírujete data do Azure SQL Database z mimo Azure, včetně místních zdrojů dat s bránou pro tovární data, nakonfigurujte příslušný rozsah IP adres pro počítač, který odesílá data do Azure SQL Database.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Chcete-li zadat datovou sadu představující vstupní nebo výstupní data v databázi Azure SQL, nastavte vlastnost type datové sady **na: AzureSqlTable**. Nastavte vlastnost **linkedServiceName** datové sady na název propojené služby Azure SQL.

Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Část **typeProperties** pro datovou sadu typu **AzureSqlTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci Azure SQL Database, na kterou odkazuje propojená služba. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování trvá pouze jeden vstup a vytváří pouze jeden výstup.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v **typeProperties** části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pokud přesouváte data z databáze Azure SQL, nastavíte typ zdroje v aktivitě kopírování na **SqlSource**. Podobně pokud přesouváte data do databáze Azure SQL, nastavíte typ jímky v aktivitě kopírování do **SqlSink**. Tato část obsahuje seznam vlastností podporovaných sqlsource a sqlsink.

### <a name="sqlsource"></a>Zdroj Sql
V aktivitě kopírování, pokud je zdroj typu **SqlSource**, jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |

Pokud **sqlReaderQuery** je zadán pro SqlSource, aktivita kopírování spustí tento dotaz proti zdroji Azure SQL Database získat data. Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktury datové sady JSON se`select column1, column2 from mytable`použijí k vytvoření dotazu ( ) ke spuštění v databázi Azure SQL. Pokud definice datové sady nemá strukturu, jsou z tabulky vybrány všechny sloupce.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat hodnotu **vlastnosti tableName** v datové sadě JSON. V šaku tabulce nejsou provedena žádná ověření.
>
>

### <a name="sqlsource-example"></a>Příklad SqlSource

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**Definice uložené procedury:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>Systém SqlSink
**SqlSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte čas pro operaci dávkové vložení k dokončení před časovým výpadkem. |Timespan<br/><br/> Příklad: "00:30:00" (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, který má být proveden tak, aby byla vyčištěna data určitého řezu. Další informace naleznete v [tématu opakovatelná kopie](#repeatable-copy). |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, který se má vyplnit automaticky generovaným identifikátorem řezu, který se používá k vyčištění dat určitého řezu při opětovném spuštění. Další informace naleznete v [tématu opakovatelná kopie](#repeatable-copy). |Název sloupce s datovým typem binárního(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak aplikovat zdrojová data do cílové tabulky, například k upserts nebo transformaci pomocí vlastní obchodní logiky. <br/><br/>Všimněte si, že tato uložená procedura bude **vyvolána na dávku**. Pokud chcete provést operaci, která běží pouze jednou a nemá nic společného se zdrojovými daty, např. `sqlWriterCleanupScript` |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Dvojice název/hodnota. Názvy a písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název typu tabulky, který má být použit v uložené proceduře. Aktivita kopírování zpřístupňuje data přesunutá v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit zkopírovaná data s existujícími daty. |Název typu tabulky. |Ne |

#### <a name="sqlsink-example"></a>Příklad aplikace SqlSink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Příklady JSON pro kopírování dat do a z databáze SQL
Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data do a z Azure SQL Database a Azure Blob Storage. Data však můžete zkopírovat **přímo** z libovolného zdroje do libovolného [jímky uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Příklad: Kopírování dat z Azure SQL Database do objektu Blob Azure
Totéž definuje následující entity Data Factory:

1. Propojená služba typu [AzureSqlDatabase](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou Copy, která používá [SqlSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data časových řad (každou hodinu, denně atd.) z tabulky v databázi Azure SQL do objektu blob každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Propojená služba Azure SQL Database:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Seznam vlastností podporovaných touto propojenou službou najdete v části Azure SQL Linked Service.

**Propojená služba úložiště objektů blob Azure:**

```JSON
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
Seznam vlastností podporovaných touto propojenou službou najdete v článku azure [blob.](data-factory-azure-blob-connector.md#azure-storage-linked-service)


**Vstupní datová sada Azure SQL:**

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v Azure SQL a obsahuje sloupec s názvem "sloupec časového razítka" pro data časových řad.

Nastavení "externí": "true" informuje službu Azure Data Factory, že datová sada je externí pro datovou továrnu a není vyráběna aktivitou v datové továrně.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

Seznam vlastností podporovaných tímto typem datové sady najdete v části Vlastnosti typu datové sady Azure SQL.

**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
V části [Vlastnosti typu datové sady Azure blob](data-factory-azure-blob-connector.md#dataset-properties) najdete v seznamu vlastností podporovaných tímto typem datové sady.

**Aktivita kopírování v kanálu se zdrojem SQL a jímkou blob:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **SqlSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data za poslední hodinu ke kopírování.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
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
V příkladu **sqlReaderQuery** je určen pro SqlSource. Aktivita kopírování spustí tento dotaz proti zdroji databáze Azure SQL, aby získala data. Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části struktura datové sady JSON se používají k vytvoření dotazu ke spuštění proti Azure SQL Database. Například: `select column1, column2 from mytable`. Pokud definice datové sady nemá strukturu, jsou z tabulky vybrány všechny sloupce.

Seznam vlastností podporovaných technologiemi SqlSource a BlobSink naleznete v části [Zdroj sql](#sqlsource) a [blobsink.](data-factory-azure-blob-connector.md#copy-activity-properties)

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Příklad: Kopírování dat z objektu Blob Azure do azure sql databáze
Ukázka definuje následující entity Data Factory:

1. Propojená služba typu [AzureSqlDatabase](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou Copy, která používá [objekty BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlSink](#copy-activity-properties).

Ukázka zkopíruje data časových řad (každou hodinu, denně atd.) z objektu blob Azure do tabulky v databázi Azure SQL každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**Propojená služba Azure SQL:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Seznam vlastností podporovaných touto propojenou službou najdete v části Azure SQL Linked Service.

**Propojená služba úložiště objektů blob Azure:**

```JSON
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
Seznam vlastností podporovaných touto propojenou službou najdete v článku azure [blob.](data-factory-azure-blob-connector.md#azure-storage-linked-service)


**Vstupní datová sada objektu Blob Azure:**

Data se zírají z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt blob jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá rok, měsíc a den část počátečního času a název souboru používá hodinovou část počátečního času. "externí": nastavení "true" informuje službu Data Factory, že tato tabulka je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
V části [Vlastnosti typu datové sady Azure blob](data-factory-azure-blob-connector.md#dataset-properties) najdete v seznamu vlastností podporovaných tímto typem datové sady.

**Výstupní datová sada Azure SQL Database:**

Ukázka zkopíruje data do tabulky s názvem "MyTable" v Azure SQL. Vytvořte tabulku v Azure SQL se stejným počtem sloupců, jako očekáváte, že soubor BLOB CSV bude obsahovat. Nové řádky jsou přidávány do tabulky každou hodinu.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
Seznam vlastností podporovaných tímto typem datové sady najdete v části Vlastnosti typu datové sady Azure SQL.

**Aktivita kopírování v kanálu se zdrojem objektů blob a jímkou SQL:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **objekt BlobSource** a typ **jímky** je nastaven na **sqlsink**.

```JSON
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
            "name": "AzureSqlOutput"
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
Podívejte se na část [Sql Sink](#sqlsink) a [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) pro seznam vlastností podporovaných SqlSink a BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Sloupce identity v cílové databázi
Tato část obsahuje příklad kopírování dat ze zdrojové tabulky bez sloupce identity do cílové tabulky se sloupcem identity.

**Zdrojová tabulka:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Cílová tabulka:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Všimněte si, že cílová tabulka má sloupec identity.

**Definice zdrojového datového souboru JSON**

```JSON
{
    "name": "SampleSource",
    "properties": {
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

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
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
Příklad vyvolání uložené procedury z jímky SQL v aktivitě kopírování kanálu najdete v tématu [Invoke stored procedure for SQL sink in copy activity](data-factory-invoke-stored-procedure-from-copy-activity.md) article.

## <a name="type-mapping-for-azure-sql-database"></a>Mapování typů pro Azure SQL Database
Jak je uvedeno v [aktivitách přesunu dat](data-factory-data-movement-activities.md) článek Aktivita kopírování provádí automatické převody typů z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat do a z Azure SQL Database se používají následující mapování z typu SQL na typ .NET a naopak. Mapování je stejné jako mapování datového typu serveru SQL Server pro ADO.NET.

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

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Opakovatelná kopie
Při kopírování dat do databáze serveru SQL Server aktivita připojí data do tabulky jímky ve výchozím nastavení. Chcete-li provést UPSERT místo toho, viz opakovatelný zápis do článku [SqlSink.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
