---
title: Kopírování dat do a z Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do a z Azure SQL Database pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a7789f9a3f3da46305a9d8cd7cda24019658f2ad
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811474"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-azure-sql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-sql-database.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Azure SQL Database ve verzi V2](../connector-azure-sql-database.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat do a ze služby Azure SQL Database. Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Data můžete kopírovat **ze služby Azure SQL Database** ukládá následující data:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových skladů **ke službě Azure SQL Database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Podporovaný typ ověřování
Konektor Azure SQL Database podporuje základní ověřování.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z Azure SQL Database s použitím různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Zobrazit [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Zobrazit [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování.

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **služby data factory**. Datová továrna může obsahovat jeden nebo víc kanálů.
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory. Například pokud kopírujete data ze služby Azure blob storage do Azure SQL database, vytvoříte dvě propojené služby pro propojení účtu služby Azure storage a Azure SQL database do služby data factory. Vlastnosti propojené služby, které jsou specifické pro Azure SQL Database, najdete v části [vlastnostem propojených služeb](#linked-service-properties) oddílu.
3. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování. V příkladu uvedených v posledním kroku vytvoříte datovou sadu, která zadejte kontejner objektů blob a složku obsahující vstupní data. A vytvořte jinou datovou sadu, která zadejte tabulky SQL ve službě Azure SQL database, která obsahuje data zkopírovaná z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure Data Lake Store, naleznete v tématu [vlastnosti datové sady](#dataset-properties) oddílu.
4. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve pomocí BlobSource jako zdroj a SqlSink jako jímka pro aktivitu kopírování. Podobně pokud kopírujete z Azure SQL Database do Azure Blob Storage, můžete použít SqlSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro Azure SQL Database, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) oddílu. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku klikněte na odkaz v předchozí části datového úložiště.

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje a rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON. Ukázky s definicemi JSON entit služby Data Factory, které se používají ke kopírování dat do a z Azure SQL Database najdete v tématu [JSON příklady](#json-examples-for-copying-data-to-and-from-sql-database) části tohoto článku.

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní ke službě Azure SQL Database:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Azure SQL propojená služba propojuje službu Azure SQL database do služby data factory. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro propojené služby Azure SQL.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavená na: **AzureSqlDatabase** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost připojovací řetězec. Je podporován pouze základní ověřování. |Ano |

> [!IMPORTANT]
> Konfigurace [bránu Firewall služby Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) serveru databáze za účelem [povolit službám Azure přístup k serveru](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Kromě toho pokud kopírujete data do služby Azure SQL Database z mimo Azure včetně místních zdrojů dat pomocí brány pro objekt pro vytváření dat, nakonfigurujte odpovídající rozsah IP adres pro počítač, který odesílá data do služby Azure SQL Database.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Chcete-li určit datové sady reprezentující vstupní nebo výstupní data ve službě Azure SQL database, nastavíte vlastnost typ datové sady na: **AzureSqlTable**. Nastavte **linkedServiceName** vlastnosti datové sady na název serveru Azure SQL propojenou službu.

Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny datové sady typy (Azure SQL, Azure blob, tabulky Azure, atd.).

V části typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. **TypeProperties** části datové sady typu **AzureSqlTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci Azure SQL Database, která je propojená služba odkazuje na. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytvoří jenom jeden výstup.

Vzhledem k tomu, k dispozici ve vlastnosti **typeProperties** části aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování se liší v závislosti na typy zdroje a jímky.

Pokud přesouváte data ze služby Azure SQL database, nastavíte typ zdroje v aktivitě kopírování do **SqlSource**. Podobně pokud přesouváte data do služby Azure SQL database, nastavíte typ jímky v aktivitě kopírování do **SqlSink**. Tato část obsahuje seznam vlastností, které jsou podporované SqlSource a SqlSink.

### <a name="sqlsource"></a>SqlSource
V aktivitě kopírování, pokud je zdroj typu **SqlSource**, následující vlastnosti jsou k dispozici v **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Použijte vlastní dotaz číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury. Příkaz SELECT v uložené proceduře musí být poslední příkaz jazyka SQL. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |

Pokud **sqlReaderQuery** je určená pro SqlSource, spustí aktivita kopírování tohoto dotazu na zdroji Azure SQL Database se mají získat data. Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName sloupce definované v části struktury datové sady JSON se používají k vytvoření dotazu (`select column1, column2 from mytable`) ke spouštění ve službě Azure SQL Database. Pokud struktura nemá žádné definice datové sady, vyberou se všechny sloupce z tabulky.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName**, je stále potřeba zadat hodnotu **tableName** vlastnost v datové sadě JSON. Neexistují žádné ověření, ale provedeny v této tabulce.
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

**Uložená procedura definice:**

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

### <a name="sqlsink"></a>SqlSink
**SqlSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| writeBatchTimeout |Čekací doba pro dávkové operace insert dokončit před vypršením časového limitu. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |
| WriteBatchSize |Vloží data do tabulky SQL writeBatchSize dosáhne velikosti vyrovnávací paměti. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování ke spuštění tak, že po vyčištění dat určitý řez. Další informace najdete v tématu [opakovatelné kopírování](#repeatable-copy). |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování k vyplnění s identifikátorem automaticky generovány řez, který se používá k vyčištění dat určitý řez, kdy se znovu spustit. Další informace najdete v tématu [opakovatelné kopírování](#repeatable-copy). |Název sloupce pro sloupec s datovým typem binary(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky, například na upsertuje proveďte nebo transformace pomocí vlastní obchodní logikou. <br/><br/>Mějte na paměti, bude tuto uloženou proceduru **za batch**. Pokud budete chtít provádět operace, která pouze spustí jednou a nemá nic dělat se zdrojovými daty, třeba delete nebo truncate, použijte `sqlWriterCleanupScript` vlastnost. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a použití malých a velkých parametry musí odpovídat názvům a použití malých a velkých parametrů uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název tabulky typu použitého v uložené proceduře. Aktivitu kopírování, která zpřístupňuje data přesouvá do dočasné tabulky s tímto typem tabulky. Uloženou proceduru kód pak sloučit data kopírovaná s existujícími daty. |Zadejte název tabulky. |Ne |

#### <a name="sqlsink-example"></a>Příklad SqlSink

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Příklady JSON pro kopírování dat do a ze služby SQL Database
Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Zobrazí se kopírování dat do a z Azure SQL Database a Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** z libovolného zdroje do libovolné jímky uvedeno [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivit kopírování ve službě Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Příklad: Kopírování dat ze služby Azure SQL Database do Azure Blob
Stejné definuje následující entity služby Data Factory:

1. Propojené služby typu [AzureSqlDatabase](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [objektů Blob v Azure](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data časových řad (každou hodinu, denně, atd.) z tabulky v databázi Azure SQL do objektu blob každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

**Azure SQL Database propojené služby:**

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
Naleznete v části propojená služba Azure SQL v seznamu vlastností podporuje tuto propojenou službu.

**Propojená služba Azure Blob storage:**

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
Zobrazit [objektů Blob v Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) článku seznamu vlastností podporuje tuto propojenou službu.


**Vstupní datová sada Azure SQL:**

Ukázka předpokládá vytvoříte tabulku "MyTable" ve službě Azure SQL a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení "externí": "PRAVDA" informuje službu Azure Data Factory, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

V části Azure SQL datové sady typu vlastnosti pro seznam vlastností podporovaných tímto typem datové sady.

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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
Zobrazit [vlastnosti typ datové sady objektů Blob v Azure](data-factory-azure-blob-connector.md#dataset-properties) naleznete seznam vlastností podporovaných tímto typem datové sady.

**Aktivita kopírování v kanálu s SQL zdroje a jímky objektu Blob:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource** a **jímky** je typ nastaven na **BlobSink**. Zadaná pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

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
V tomto příkladu **sqlReaderQuery** je určená pro SqlSource. Tento dotaz spustí aktivita kopírování na zdroji Azure SQL Database se mají získat data. Alternativně můžete zadat uložené procedury tak, že zadáte **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName sloupce definované v části struktury datové sady JSON se používají k vytvoření dotazu ke spouštění ve službě Azure SQL Database. Například: `select column1, column2 from mytable`. Pokud struktura nemá žádné definice datové sady, vyberou se všechny sloupce z tabulky.

Zobrazit [zdroje Sql](#sqlsource) části a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) pro seznam vlastností, které jsou podporované SqlSource a BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Příklad: Kopírování dat z objektů Blob v Azure do Azure SQL Database
Ukázka definuje následující entity služby Data Factory:

1. Propojené služby typu [AzureSqlDatabase](#linked-service-properties).
2. Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlSink](#copy-activity-properties).

Ukázka zkopíruje časových řad dat (každou hodinu, denně, atd.) z Azure blob do tabulky v Azure SQL databáze každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

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
Naleznete v části propojená služba Azure SQL v seznamu vlastností podporuje tuto propojenou službu.

**Propojená služba Azure Blob storage:**

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
Zobrazit [objektů Blob v Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) článku seznamu vlastností podporuje tuto propojenou službu.


**Azure vstupní datovou sadou objektů Blob:**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Název složky a cesta k souboru pro tento objekt blob se dynamicky vyhodnocuje podle času spuštění řezu, který se právě zpracovává. Cesta ke složce používá rok, měsíc a den čas spuštění a název souboru používá hodinová část času zahájení. "externí": "PRAVDA" nastavení informuje služby Data Factory, že tato tabulka je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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
Zobrazit [vlastnosti typ datové sady objektů Blob v Azure](data-factory-azure-blob-connector.md#dataset-properties) naleznete seznam vlastností podporovaných tímto typem datové sady.

**Azure SQL Database výstupní datovou sadu:**

Ukázce kopíruje data do tabulky s názvem "MyTable" ve službě Azure SQL. Vytvoření tabulky ve službě Azure SQL s stejný počet sloupců tak, jak očekáváte objektů Blob CSV soubor obsahovat. Nové řádky do tabulky přidají každou hodinu.

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
V části Azure SQL datové sady typu vlastnosti pro seznam vlastností podporovaných tímto typem datové sady.

**Aktivita kopírování v kanálu s Blob zdroje a jímky SQL:**

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **jímky** je typ nastaven na **SqlSink**.

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
Zobrazit [Sql jímky](#sqlsink) části a [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) pro seznam vlastností, které jsou podporované SqlSink a BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Sloupce identity v cílové databázi
Tato část poskytuje příklad pro kopírování dat ze zdrojové tabulky bez sloupec identity do cílové tabulky se sloupcem identity.

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
Všimněte si, že cílová tabulka obsahuje sloupec identity.

**Zdroj definice JSON datové sady**

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
**Určení definice JSON datové sady**

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

Všimněte si, že jako zdrojové a cílové tabulce mají jiné schéma (cílový má další sloupec s identitou). V tomto scénáři budete muset zadat **struktura** vlastnost v definici datové sady target, která neobsahuje sloupec identity.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Vyvolání uložené procedury SQL jímky
Příklad volání uložené procedury z SQL jímky v aktivitě kopírování kanálu najdete v tématu [volání uložené procedury pro SQL jímky v aktivitě kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md) článku.

## <a name="type-mapping-for-azure-sql-database"></a>Mapování typu pro službu Azure SQL Database
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typy s přístupem následující krok 2:

1. Převést na typ formátu .NET typy nativních zdrojů
2. Převést z typu .NET native jímky typu

Při přesouvání dat do a z Azure SQL Database, se používají následující mapování z typu SQL pro typ formátu .NET a naopak. Mapování je stejná jako SQL Server mapování datového typu pro technologii ADO.NET.

| Typ databázového stroje SQL serveru | Typ rozhraní .NET framework |
| --- | --- |
| bigint |Int64 |
| Binární |Byte] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| date |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte] |
| Float |Double |
| image |Byte] |
| int |Datový typ Int32 |
| peníze |Decimal |
| nchar |Řetězec, Char] |
| ntext |Řetězec, Char] |
| Číselné |Decimal |
| nvarchar |Řetězec, Char] |
| Real |Jednoduchá |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Decimal |
| SQL_VARIANT |Objekt * |
| text |Řetězec, Char] |
| time |Časový interval |
| časové razítko |Byte] |
| tinyint |Bajt |
| UniqueIdentifier |Guid |
| varbinary |Byte] |
| varchar |Řetězec, Char] |
| xml |XML |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje do jímky sloupce
Další informace o mapování sloupců v datové sadě zdroje do sloupců v datové sadě jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Opakovatelné kopírování
Kopírování dat do databáze SQL serveru, aktivita kopírování připojí data do tabulky jímky ve výchozím nastavení. Místo toho provést funkcí UPSERT, najdete v článku [Repeatable zapisovat do SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) článku.

Při kopírování dat z relačních dat ukládá, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. V obou případech se znovu spustí určitý řez, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát spustit určitý řez. Zobrazit [Repeatable z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Zobrazit [výkonem aktivity kopírování & Průvodci optimalizací](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Azure Data Factory a různé způsoby, jak optimalizovat.
