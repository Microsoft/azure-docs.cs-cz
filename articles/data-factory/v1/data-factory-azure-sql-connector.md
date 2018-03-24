---
title: Kopírování dat do/z Azure SQL Database | Microsoft Docs
description: Zjistěte, jak ke zkopírování dat z Azure SQL Database pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bf47ad3f401abced4e74260dc9180413d69268c3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-azure-sql-connector.md)
> * [Verze 2 – Preview](../connector-azure-sql-database.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [konektor Azure SQL Database v V2](../connector-azure-sql-database.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat do a z Azure SQL Database. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.  

## <a name="supported-scenarios"></a>Podporované scénáře
Může kopírovat data **z databáze Azure SQL Database** ukládá do následující data:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Může kopírovat data z následujících datových úložišť **do Azure SQL Database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Podporované typ ověřování
Konektor služby Azure SQL Database podporuje základní ověřování.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z Azure SQL Database pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření **objekt pro vytváření dat**. Objekt pro vytváření dat může obsahovat jeden nebo víc kanálů. 
2. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory. Pokud jsou kopírování dat z Azure blob storage do Azure SQL database, například vytvoříte dvě propojené služby pro vytváření dat. propojení účtu úložiště Azure a Azure SQL database. Vlastnosti propojené služby, které jsou specifické pro Azure SQL Database, najdete v části [propojené vlastnosti služby](#linked-service-properties) části. 
3. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. V příkladu uvedených v posledním kroku vytvoříte datové sady a zadat kontejner objektů blob a složky, která obsahuje vstupní data. A vytvořte jinou datovou sadu, která zadejte tabulku SQL ve službě Azure SQL database, který obsahuje data zkopírovat z úložiště objektů blob. Vlastnosti datové sady, které jsou specifické pro Azure Data Lake Store, naleznete v části [vlastnosti datové sady](#dataset-properties) části.
4. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. V příkladu již bylo zmíněno dříve použijete BlobSource jako zdroj a SqlSink jako jímku pro aktivitu kopírování. Podobně pokud kopírujete z databáze SQL Azure do Azure Blob Storage, můžete použít SqlSource a BlobSink v aktivitě kopírování. Kopírovat vlastnosti aktivity, které jsou specifické pro Azure SQL Database, najdete v části [zkopírovat vlastnosti aktivity](#copy-activity-properties) části. Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímka klikněte na odkaz v předchozí části pro data store.

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázky s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat do/z Azure SQL Database, najdete v části [JSON příklady](#json-examples-for-copying-data-to-and-from-sql-database) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení entit služby Data Factory konkrétní do Azure SQL Database: 

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Azure SQL propojená propojuje službu Azure SQL database pro vytváření dat. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro propojené služby Azure SQL.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **azuresqldatabase.** |Ano |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci databáze SQL Azure pro vlastnost connectionString. Podporováno je pouze základní ověřování. |Ano |

> [!IMPORTANT]
> Konfigurace [Firewall databáze Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) databázový server, který [povolit službám Azure přístup k serveru](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Kromě toho pokud data kopírujete do Azure SQL Database z mimo Azure včetně z místního zdroje dat se brána objekt pro vytváření dat, nakonfigurujte příslušné rozsah IP adres pro počítač, který odesílá data do Azure SQL Database.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Pokud chcete zadat datové sady představují vstupních nebo výstupních dat v Azure SQL database, nastavíte vlastnost type datové sady, která: **AzureSqlTable**. Nastavte **linkedServiceName** vlastnosti datové sady, která název Azure SQL, propojené služby.  

Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

V rámci typeProperties části se liší pro jednotlivé typy datovou sadu a poskytuje informace o umístění dat v úložišti. **Rámci typeProperties** části datové sady typu **AzureSqlTable** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení instance databáze SQL Azure, kterou propojená služba odkazuje. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

> [!NOTE]
> Aktivita kopírování přijímá pouze jeden vstup a vytváří jenom jeden výstup.

Vzhledem k tomu, vlastnosti dostupné ve **rámci typeProperties** části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pokud přesouváte data z databáze Azure SQL, nastavíte typ zdroje v aktivitě kopírování do **SqlSource**. Podobně pokud přesouváte data do Azure SQL database, nastavíte typ jímky v aktivitě kopírování do **SqlSink**. Tato část obsahuje seznam vlastností, které jsou podporované SqlSource a SqlSink.

### <a name="sqlsource"></a>SqlSource
Při aktivitě kopírování, pokud je zdroj typu **SqlSource**, následující vlastnosti jsou k dispozici v **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| sqlReaderQuery |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |Ne |
| sqlReaderStoredProcedureName |Název uložené procedury, který čte data ze zdrojové tabulky. |Název uložené procedury. Poslední příkaz jazyka SQL musí být příkaz SELECT v uložené proceduře. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |

Pokud **sqlReaderQuery** je zadán pro SqlSource, aktivitě kopírování spouští tento dotaz zdrojů databáze SQL Azure a získat data. Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části Struktura sady dat JSON se používají k vytvoření dotazu (`select column1, column2 from mytable`) ke spouštění na databázi SQL Azure. Pokud definice datové sady nemá strukturu, jsou vybrány všechny sloupce z tabulky.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName**, stále je třeba zadat hodnotu pro **tableName** vlastnost v datové sadě JSON. Neexistují žádné ověření, ale adresovat této tabulky.
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
| writeBatchTimeout |Počkejte, než čas na dokončení předtím, než vyprší časový limit operace dávkové vložení. |Časový interval<br/><br/> Příklad: "00: 30:00" (30 minut). |Ne |
| writeBatchSize |Vloží data do tabulky SQL, když velikost vyrovnávací paměti dosáhne writeBatchSize. |Celé číslo (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování provést tak, aby se vyčistit data určitý řez. Další informace najdete v tématu [opakovatelných kopie](#repeatable-copy). |Příkaz dotazu. |Ne |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování vyplníte identifikátor automaticky generovány řez, který se používá k vyčištění dat určitý řez při spusťte znovu. Další informace najdete v tématu [opakovatelných kopie](#repeatable-copy). |Název sloupce sloupce s datovým typem binary(32). |Ne |
| sqlWriterStoredProcedureName |Název uložené procedury, která definuje, jak použít zdroj dat do cílové tabulky, například na proveďte upserts nebo transformace pomocí vlastní obchodní logiku. <br/><br/>Tuto uloženou proceduru bude **vyvolat na jednu dávku**. Pokud budete chtít provést operaci, která pouze se spustí jednou a nijak nesouvisí se zdrojovými daty, například odstranit nebo zkrátit proveďte pomocí `sqlWriterCleanupScript` vlastnost. |Název uložené procedury. |Ne |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název/hodnota. Názvy a malá a velká písmena parametry musí odpovídat názvům a malá a velká písmena parametry uložené procedury. |Ne |
| sqlWriterTableType |Zadejte název typu tabulky má být použit v uložené proceduře. Aktivita kopírování zpřístupní přesouvání dat v dočasné tabulce s tímto typem tabulky. Uložená procedura kód pak sloučit data kopírovány s existujícími daty. |Zadejte název tabulky. |Ne |

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Příklady JSON pro kopírování dat do a z databáze SQL
Následující příklady poskytují ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak ke zkopírování dat do a z Azure SQL Database a Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** ze všech zdrojů do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Příklad: Kopírování dat z databáze SQL Azure do Azure Blob
Stejné definuje následující entity služby Data Factory:

1. Propojené služby typu [azuresqldatabase](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [objektů Blob v Azure](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data časové řady (hodinový, denní, atd.) z tabulky v databázi Azure SQL do objektu blob každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.  

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
Najdete v článku [propojená služba Azure SQL](#linked-service) části seznamu vlastností podporuje tuto propojenou službu.

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
Najdete v článku [objektů Blob v Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) článku seznamu vlastností podporuje tuto propojenou službu.


**Azure SQL vstupní datové sady:**

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v Azure SQL a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady.

Nastavení "externí": "PRAVDA" informuje služby Azure Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

Najdete v článku [vlastnosti typu datové sady Azure SQL](#dataset) části seznamu vlastností nepodporuje tento typ dataset.  

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

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
Najdete v článku [vlastnosti typu datovou sadu objektu Blob Azure](data-factory-azure-blob-connector.md#dataset-properties) části seznamu vlastností nepodporuje tento typ dataset.  

**Aktivita kopírování v kanálu s SQL zdrojový a podřízený objekt Blob:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **SqlSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **SqlReaderQuery** vlastnost vybere data za poslední hodinu pro kopírování.

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
V příkladu **sqlReaderQuery** je zadán pro SqlSource. Aktivita kopírování spouští tento dotaz zdrojů databáze SQL Azure a získat data. Alternativně můžete zadat uložené procedury zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přebírá parametry).

Pokud nezadáte sqlReaderQuery nebo sqlReaderStoredProcedureName, sloupce definované v části Struktura sady dat JSON se používají k vytvoření dotazu ke spouštění na databázi SQL Azure. Například: `select column1, column2 from mytable`. Pokud definice datové sady nemá strukturu, jsou vybrány všechny sloupce z tabulky.

Najdete v článku [zdroje Sql](#sqlsource) části a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) pro seznam vlastností, které jsou podporované SqlSource a BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Příklad: Kopírování dat z objektu Blob Azure do Azure SQL Database
Ukázka definuje následující entity služby Data Factory:  

1. Propojené služby typu [azuresqldatabase](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlSink](#copy-activity-properties).

Kopie ukázka časové řady dat (hodinový, denní, atd.) z Azure blob do tabulky v Azure SQL databáze každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Azure SQL propojené služby:**

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
Najdete v článku [propojená služba Azure SQL](#linked-service) části seznamu vlastností podporuje tuto propojenou službu.

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
Najdete v článku [objektů Blob v Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) článku seznamu vlastností podporuje tuto propojenou službu.


**Azure vstupní datovou sadu objektu Blob:**

Data je převzata z nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Název složky a cesta k souboru pro tento objekt blob se vyhodnocují dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc a den součástí čas spuštění a název souboru používá hodinu součástí čas spuštění. "externí": "PRAVDA" nastavení informuje služba Data Factory, že tato tabulka je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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
Najdete v článku [vlastnosti typu datovou sadu objektu Blob Azure](data-factory-azure-blob-connector.md#dataset-properties) části seznamu vlastností nepodporuje tento typ dataset.

**Azure SQL Database výstupní datovou sadu:**

Ukázka zkopíruje data na tabulku s názvem "MyTable" v Azure SQL. Vytvořte v tabulce v Azure SQL s stejný počet sloupců, podle očekávání souboru CSV objektů Blob tak, aby obsahovala. Nové záznamy se přidají do tabulky každou hodinu.

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
Najdete v článku [vlastnosti typu datové sady Azure SQL](#dataset) části seznamu vlastností nepodporuje tento typ dataset.

**Aktivita kopírování v kanálu s Blob zdroj a jímka SQL:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **BlobSource** a **podřízený** je typ nastaven na **SqlSink**.

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
Najdete v článku [jímku Sql](#sqlsink) části a [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) pro seznam vlastností, které jsou podporované SqlSink a BlobSource.

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
**Cílové tabulky:**

```SQL
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```
Všimněte si, že cílová tabulka obsahuje sloupec identity.

**Definice JSON datové sady zdroje**

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
**Cílový definici JSON datové sady**

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

Všimněte si, že jako zdrojové a cílové tabulky jiné schéma (cíl má sloupec s identitou). V tomto scénáři budete muset zadat **struktura** vlastnost v definici datové sady cíl, který neobsahuje sloupec identity.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Volání uložené procedury jímku SQL
Příklad volání uložené procedury z jímku SQL při aktivitě kopírování kanálu, naleznete v části [vyvolat uloženou proceduru SQL jímka v aktivitě kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md) článku. 

## <a name="type-mapping-for-azure-sql-database"></a>Mapování typu pro databázi SQL Azure
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup krok 2:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesunu dat do a z Azure SQL Database, se používají následující mapování z typu SQL na typ .NET a naopak. Mapování je stejný jako mapování SQL Server datového typu pro technologii ADO.NET.

| Typ databázového stroje SQL Server | Typ rozhraní .NET framework |
| --- | --- |
| bigint |Int64 |
| Binární |Byte[] |
| Bit |Logická hodnota |
| Char |Řetězec, Char] |
| datum |DateTime |
| Datum a čas |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atribut FILESTREAM (varbinary(max)) |Byte[] |
| Plovoucí desetinná čárka |Dvojitý |
| Bitové kopie |Byte[] |
| celá čísla |Int32 |
| money |Decimal |
| nchar |Řetězec, Char] |
| ntext |Řetězec, Char] |
| číselné |Decimal |
| nvarchar |Řetězec, Char] |
| skutečné |Svobodný/svobodná |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| Smallmoney |Decimal |
| sql_variant |Objekt * |
| Text |Řetězec, Char] |
| time |TimeSpan |
| časové razítko |Byte[] |
| tinyint |Bajtů |
| Typ UniqueIdentifier |Guid |
| varbinary |Byte[] |
| varchar |Řetězec, Char] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Opakovatelných kopie
Při kopírování dat do databáze serveru SQL, připojí aktivitě kopírování dat do tabulky jímky ve výchozím nastavení. Místo toho provést UPSERT, najdete v tématu [Repeatable zapisovat do SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) článku. 

Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
