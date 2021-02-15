---
title: Kopírovat data do/z Azure SQL Database
description: Naučte se, jak kopírovat data do a z Azure SQL Database pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 738b875a273faddd20a67be0f6feb90825f66c9f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370508"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopírování dat do a z Azure SQL Database pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-azure-sql-connector.md)
> * [Verze 2 (aktuální verze)](../connector-azure-sql-database.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Azure SQL Database v v2](../connector-azure-sql-database.md).

Tento článek vysvětluje, jak používat aktivitu kopírování v Azure Data Factory k přesunu dat do a z Azure SQL Database. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

## <a name="supported-scenarios"></a>Podporované scénáře
Data **z Azure SQL Database** můžete kopírovat do následujících úložišť dat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Data z následujících úložišť dat můžete zkopírovat **do Azure SQL Database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Typ podporovaného ověřování
Konektor Azure SQL Database podporuje základní ověřování.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data do nebo z Azure SQL Database pomocí různých nástrojů/rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **datovou továrnu**. Datová továrna může obsahovat jeden nebo více kanálů.
2. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou. Pokud například kopírujete data z úložiště objektů BLOB v Azure do Azure SQL Database, vytvoříte dvě propojené služby, které propojí váš účet služby Azure Storage a Azure SQL Database k vašemu objektu pro vytváření dat. Vlastnosti propojené služby, které jsou specifické pro Azure SQL Database, najdete v části [Vlastnosti propojené služby](#linked-service-properties) .
3. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. V příkladu uvedeném v posledním kroku vytvoříte datovou sadu pro určení kontejneru objektů BLOB a složky, která obsahuje vstupní data. A můžete vytvořit další datovou sadu pro určení tabulky SQL v Azure SQL Database, která obsahuje data zkopírovaná z úložiště objektů BLOB. Vlastnosti datové sady, které jsou specifické pro Azure Data Lake Store, najdete v části [Vlastnosti datové sady](#dataset-properties) .
4. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. V předchozím příkladu použijete jako jímku aktivity kopírování BlobSource jako zdroj a SqlSink. Podobně pokud kopírujete z Azure SQL Database do Azure Blob Storage, v aktivitě kopírování použijete SqlSource a BlobSink. Vlastnosti aktivity kopírování, které jsou specifické pro Azure SQL Database, najdete v části [vlastnosti aktivity kopírování](#copy-activity-properties) . Podrobnosti o tom, jak používat úložiště dat jako zdroj nebo jímku, získáte kliknutím na odkaz v předchozí části úložiště dat.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázky s definicemi JSON pro Entity Data Factory používané ke kopírování dat do a z Azure SQL Database najdete v části [Příklady JSON](#json-examples-for-copying-data-to-and-from-sql-database) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro Azure SQL Database:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Odkazy propojené služby Azure SQL Azure SQL Database do vaší datové továrny. Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu Azure SQL.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **AzureSqlDatabase** . |Yes |
| připojovací řetězec |Zadejte informace potřebné pro připojení k instanci Azure SQL Database pro vlastnost connectionString. Podporuje se jenom základní ověřování. |Yes |

> [!IMPORTANT]
> Nakonfigurujte [Azure SQL Database brány firewall](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) databázového serveru, aby měly [služby Azure přístup k serveru](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Pokud navíc kopírujete data Azure SQL Database mimo Azure, včetně z místních zdrojů dat pomocí brány služby Data Factory, nakonfigurujte odpovídající rozsah IP adres pro počítač, který odesílá data do Azure SQL Database.

## <a name="dataset-properties"></a>Vlastnosti datové sady
Chcete-li určit datovou sadu, která bude představovat vstupní nebo výstupní data v Azure SQL Database, nastavte vlastnost Type datové sady na: **AzureSqlTable**. Nastavte vlastnost **linkedServiceName** datové sady na název propojené služby Azure SQL.

Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl typeProperties se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro sadu dat typu **AzureSqlTable** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky nebo zobrazení v instanci Azure SQL Database, na kterou odkazuje propojená služba. |Yes |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

> [!NOTE]
> Aktivita kopírování používá pouze jeden vstup a vytváří pouze jeden výstup.

V takovém případě se vlastnosti dostupné v části **typeProperties** v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud přesouváte data z Azure SQL Database, nastavíte typ zdroje v aktivitě kopírování na **SqlSource**. Podobně Pokud přesouváte data na Azure SQL Database, nastavíte typ jímky v aktivitě kopírování na **SqlSink**. V této části najdete seznam vlastností podporovaných SqlSource a SqlSink.

### <a name="sqlsource"></a>SqlSource
V aktivitě kopírování je-li zdrojem typu **SqlSource**, jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| sqlReaderQuery |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Název uložené procedury, která čte data ze zdrojové tabulky. |Název uložené procedury Poslední příkaz SQL musí být příkaz SELECT v uložené proceduře. |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |

Pokud je pro SqlSource určena **sqlReaderQuery** , aktivita kopírování spustí tento dotaz proti zdroji Azure SQL Database, aby získala data. Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přijímá parametry).

Pokud nezadáte buď sqlReaderQuery nebo sqlReaderStoredProcedureName, budou použity sloupce definované v oddílu struktury JSON datové sady k vytvoření dotazu ( `select column1, column2 from mytable` ) ke spuštění na Azure SQL Database. Pokud definice datové sady nemá strukturu, všechny sloupce jsou vybrány z tabulky.

> [!NOTE]
> Při použití **sqlReaderStoredProcedureName** je stále nutné zadat hodnotu pro vlastnost **TableName** v datové sadě JSON. V této tabulce neexistují žádná ověření, která by byla provedena.
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

### <a name="sqlsink"></a>SqlSink
**SqlSink** podporuje následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| writeBatchTimeout |Počkejte, než se operace dávkového vložení dokončí předtím, než vyprší časový limit. |timespan<br/><br/> Příklad: "00:30:00" (30 minut). |No |
| writeBatchSize |Když velikost vyrovnávací paměti dosáhne writeBatchSize, vloží data do tabulky SQL. |Integer (počet řádků) |Ne (výchozí: 10000) |
| sqlWriterCleanupScript |Zadejte dotaz pro aktivitu kopírování, která se má provést, aby se vyčistila data konkrétního řezu. Další informace najdete v tématu [opakované kopírování](#repeatable-copy). |Příkaz dotazu. |No |
| sliceIdentifierColumnName |Zadejte název sloupce pro aktivitu kopírování, která se má vyplnit automaticky generovaným identifikátorem řezu, který se použije k vyčištění dat určitého řezu při opakovaném spuštění. Další informace najdete v tématu [opakované kopírování](#repeatable-copy). |Název sloupce sloupce s datovým typem Binary (32). |No |
| sqlWriterStoredProcedureName |Název uložené procedury definující, jak se mají použít zdrojová data na cílovou tabulku, například upsertuje nebo transformaci pomocí vlastní obchodní logiky. <br/><br/>Poznámka: Tato uložená procedura se **vyvolá na každou dávku**. Pokud chcete provést operaci, která se spustí pouze jednou a nemá nic ke zdroji dat, např. Delete/zkrácení, použijte `sqlWriterCleanupScript` vlastnost. |Název uložené procedury |No |
| storedProcedureParameters |Parametry pro uloženou proceduru. |Páry název-hodnota. Názvy a malá písmena parametrů se musí shodovat s názvy a písmeny parametrů uložené procedury. |No |
| sqlWriterTableType |Zadejte název typu tabulky, který se použije v uložené proceduře. Aktivita kopírování zpřístupňuje data, která jsou k dispozici v dočasné tabulce s tímto typem tabulky. Uložený kód procedury pak může sloučit data zkopírovaná se stávajícími daty. |Název typu tabulky |No |

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Příklady JSON pro kopírování dat do a z SQL Database
V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data z Azure SQL Database a z Azure Blob Storage. Data se ale dají zkopírovat **přímo** z libovolného zdroje do kterékoli z těchto umyvadel, které jsou [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedené, pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Příklad: kopírování dat z Azure SQL Database do objektu blob Azure
Stejný definuje následující Entity Data Factory:

1. Propojená služba typu [AzureSqlDatabase](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [SqlSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data časových řad (každou hodinu, každý den atd.) z tabulky v Azure SQL Database do objektu BLOB každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

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
Seznam vlastností podporovaných touto propojenou službou najdete v části propojená služba Azure SQL.

**Propojená služba úložiště objektů BLOB v Azure:**

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
Seznam vlastností podporovaných touto propojenou službou najdete v článku o [objektu blob Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) .


**Vstupní datová sada Azure SQL:**

Ukázka předpokládá, že jste v Azure SQL vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "timestampcolumn" pro data časových řad.

Nastavení "externí": "true" informuje službu Azure Data Factory o tom, že datová sada je externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

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

Seznam vlastností, které tento typ datové sady podporuje, najdete v části vlastnosti typu datové sady Azure SQL.

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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
Seznam vlastností, které tento typ datové sady podporuje, najdete v části [vlastnosti typu datové sady objektů BLOB v Azure](data-factory-azure-blob-connector.md#dataset-properties) .

**Aktivita kopírování v kanálu se zdrojem SQL a jímkou objektů BLOB:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **SqlSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **SqlReaderQuery** vybere data během uplynulé hodiny ke zkopírování.

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
V příkladu je **sqlReaderQuery** určena pro SqlSource. Aktivita kopírování spustí tento dotaz proti zdroji Azure SQL Database, aby získala data. Alternativně můžete zadat uloženou proceduru zadáním **sqlReaderStoredProcedureName** a **storedProcedureParameters** (Pokud uložená procedura přijímá parametry).

Pokud nezadáte buď sqlReaderQuery nebo sqlReaderStoredProcedureName, budou použity sloupce definované v oddílu struktury JSON datové sady k vytvoření dotazu pro spuštění proti Azure SQL Database. Příklad: `select column1, column2 from mytable`. Pokud definice datové sady nemá strukturu, všechny sloupce jsou vybrány z tabulky.

Seznam vlastností podporovaných SqlSource a BlobSink najdete v části [zdroje SQL](#sqlsource) a v [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) .

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Příklad: kopírování dat z objektu blob Azure do Azure SQL Database
Ukázka definuje následující Entity Data Factory:

1. Propojená služba typu [AzureSqlDatabase](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureSqlTable](#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) a [SqlSink](#copy-activity-properties).

Ukázka kopíruje data časových řad (každou hodinu, každý den atd.) z objektu blob Azure do tabulky v Azure SQL Database každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

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
Seznam vlastností podporovaných touto propojenou službou najdete v části propojená služba Azure SQL.

**Propojená služba úložiště objektů BLOB v Azure:**

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
Seznam vlastností podporovaných touto propojenou službou najdete v článku o [objektu blob Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) .


**Vstupní datová sada Azure Blob:**

Data se vybírají z nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce a název souboru pro objekt BLOB jsou dynamicky vyhodnocovány na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část rok, měsíc a den v čase zahájení a název souboru používá hodinovou část času spuštění. nastavení externí: "true" informuje službu Data Factory o tom, že tato tabulka je pro objekt pro vytváření dat externá a není vytvořená aktivitou v datové továrně.

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
Seznam vlastností, které tento typ datové sady podporuje, najdete v části [vlastnosti typu datové sady objektů BLOB v Azure](data-factory-azure-blob-connector.md#dataset-properties) .

**Výstupní datová sada Azure SQL Database:**

Ukázka zkopíruje data do tabulky s názvem "MyTable" v Azure SQL. Vytvořte tabulku v Azure SQL se stejným počtem sloupců, jako byste očekávali, že soubor CSV BLOB bude obsahovat. Nové řádky jsou do tabulky přidány každou hodinu.

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
Seznam vlastností, které tento typ datové sady podporuje, najdete v části vlastnosti typu datové sady Azure SQL.

**Aktivita kopírování v kanálu se zdrojem objektů BLOB a jímkou SQL:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **BlobSource** a typ **jímky** je nastavený na **SqlSink**.

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
Seznam vlastností podporovaných SqlSink a BlobSource najdete v části [jímka SQL](#sqlsink) a [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="identity-columns-in-the-target-database"></a>Sloupce identity v cílové databázi
V této části najdete příklad kopírování dat ze zdrojové tabulky bez sloupce identity do cílové tabulky se sloupcem identity.

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

**Definice JSON zdrojové datové sady**

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

Všimněte si, že protože vaše zdrojová a cílová tabulka mají jiné schéma (cíl má další sloupec s identitou). V tomto scénáři je nutné zadat vlastnost **struktury** v definici cílové datové sady, která neobsahuje sloupec identity.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Vyvolat uloženou proceduru z jímky SQL
Příklad vyvolání uložené procedury z jímky SQL v aktivitě kopírování kanálu najdete v tématu [vyvolání uložené procedury pro JÍMKU SQL v článku o aktivitě kopírování](data-factory-invoke-stored-procedure-from-copy-activity.md) .

## <a name="type-mapping-for-azure-sql-database"></a>Mapování typů pro Azure SQL Database
Jak je uvedeno v článku aktivity [přesunu dat](data-factory-data-movement-activities.md) , provádí se automatické převody typů ze zdrojových typů do typů jímky s následujícím přístupem ke dvěma krokům:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat do a z Azure SQL Database se z typu SQL do typu .NET použijí následující mapování a naopak. Mapování je stejné jako SQL Server mapování datových typů pro ADO.NET.

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

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Opakující se kopie
Při kopírování dat do SQL Server databáze aktivita kopírování ve výchozím nastavení připojí data do tabulky jímky. Pokud chcete místo toho provést UPSERT, přečtěte si článek s [možností opakovaného zápisu do SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) .

Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .