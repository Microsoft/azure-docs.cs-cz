---
title: Přesun dat z Amazon RedShift pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data z Amazon RedShift pomocí aktivity kopírování Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 025250f47bf0630be5ae988140a5feeecfd0eaf0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377546"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Přesun dat z Amazon RedShift pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-amazon-redshift-connector.md)
> * [Verze 2 (aktuální verze)](../connector-amazon-redshift.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Amazon RedShift Connector ve verzi v2](../connector-amazon-redshift.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z Amazon RedShift. Článek se staví na článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data Factory aktuálně podporuje pouze přesun dat z Amazon RedShift do [podporovaného úložiště dat jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Přesun dat z jiných úložišť dat do Amazon RedShift se nepodporuje.

> [!TIP]
> Pokud chcete dosáhnout nejlepšího výkonu při kopírování velkých objemů dat z Amazon RedShift, zvažte použití integrovaného příkazu RedShift **Unload** prostřednictvím služby Amazon Simple Storage Service (Amazon S3). Podrobnosti najdete v tématu [použití uvolnění ke kopírování dat z Amazon RedShift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Požadavky
* Pokud přesouváte data do místního úložiště dat, nainstalujte [Správa dat bránu](data-factory-data-management-gateway.md) na místní počítač. Udělte bráně přístup ke clusteru Amazon RedShift pomocí IP adresy místního počítače. Pokyny najdete v tématu [autorizace přístupu ke clusteru](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Pokud chcete přesunout data do úložiště dat Azure, přečtěte si část [výpočetní IP adresa a rozsahy SQL používané datacentry Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování pro přesun dat ze zdroje Amazon RedShift pomocí různých nástrojů a rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít Průvodce kopírováním Azure Data Factory. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním najdete v tomto [kurzu: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Kanál můžete vytvořit také pomocí sady Visual Studio, Azure PowerShell nebo jiných nástrojů. Azure Resource Manager šablony, rozhraní .NET API nebo REST API lze použít také k vytvoření kanálu. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte propojené služby, které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové sady, které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte kanál s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce kopírováním se automaticky vytvoří definice JSON pro tyto Data Factory entity. Pokud používáte nástroje nebo rozhraní API (s výjimkou rozhraní .NET API), definujete Data Factory entit pomocí formátu JSON. Příklad JSON: kopírování dat z Amazon RedShift do Azure Blob Storage zobrazuje definice JSON pro Data Factory entity, které se používají ke kopírování dat z úložiště dat Amazon RedShift.

Následující části popisují vlastnosti JSON, které se používají k definování Data Factory entit pro Amazon RedShift.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka uvádí popisy pro prvky JSON, které jsou specifické pro propojenou službu Amazon RedShift.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| **textový** |Tato vlastnost musí být nastavená na **AmazonRedshift**. |Yes |
| **WebServer** |IP adresa nebo název hostitele serveru Amazon RedShift. |Yes |
| **přístavní** |Číslo portu TCP, který server Amazon RedShift používá k naslouchání klientským připojením. |Ne (výchozí hodnota je 5439) |
| **databáze** |Název databáze Amazon RedShift. |Yes |
| **jmen** |Jméno uživatele, který má přístup k databázi. |Yes |
| **heslo** |Heslo pro uživatelský účet. |Yes |

## <a name="dataset-properties"></a>Vlastnosti datové sady

Seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Sekce **Struktura**, **dostupnost** a **zásady** jsou podobné pro všechny typy datových sad. Mezi typy datových sad patří Azure SQL, Azure Blob Storage a Azure Table Storage.

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat ve Storu. Oddíl **typeProperties** pro datovou sadu **relačních** typů, která zahrnuje datovou sadu Amazon RedShift, má následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| **tableName** |Název tabulky v databázi Amazon RedShift, na kterou odkazuje propojená služba |Ne (Pokud je zadaná vlastnost **dotazu** aktivity kopírování typu **RelationalSource** ) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Vlastnosti **název**, **Popis**, **vstupy** , tabulka **výstupů** a **zásady** jsou k dispozici pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v části **typeProperties** , se liší pro každý typ aktivity. V případě aktivity kopírování se vlastnosti liší v závislosti na typech zdrojů dat a jímky.

V případě aktivity kopírování je-li zdrojem typu **AmazonRedshiftSource**, jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| **zadávání** | K načtení dat použijte vlastní dotaz. |Ne (Pokud je určena vlastnost **TableName** objektu DataSet) |
| **redshiftUnloadSettings** | Obsahuje skupinu vlastností při použití příkazu RedShift **Unload** . | No |
| **s3LinkedServiceName** | Amazon S3 pro použití jako dočasné úložiště. Propojená služba je určena pomocí Azure Data Factory název typu **AwsAccessKey**. | Povinné při použití vlastnosti **redshiftUnloadSettings** |
| **interval intervalu** | Určuje, který blok Amazon S3 se má použít k uložení dočasných dat. Pokud tato vlastnost není k dispozici, aktivita kopírování automaticky vygeneruje kontejner. | Povinné při použití vlastnosti **redshiftUnloadSettings** |

Alternativně můžete použít typ **RelationalSource** , který zahrnuje Amazon RedShift, s následující vlastností v oddílu **typeProperties** . Poznámka: Tento typ zdroje nepodporuje příkaz RedShift **Unload** .

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| **zadávání** |K načtení dat použijte vlastní dotaz. | Ne (Pokud je určena vlastnost **TableName** objektu DataSet) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Použití Unload ke kopírování dat z Amazon RedShift

[**Příkaz Amazon RedShift Unload uvolní**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) výsledky dotazu do jednoho nebo více souborů v Amazon S3. Tento příkaz doporučuje Amazon pro kopírování velkých datových sad z RedShift.

**Příklad: kopírování dat z Amazon RedShift do Azure synapse Analytics**

Tento příklad kopíruje data z Amazon RedShift do Azure synapse Analytics. V příkladu se používá příkaz RedShift **Unload** , připravené kopírování dat a Microsoft – základ.

U tohoto ukázkového případu aktivita kopírování nejprve uvolní data z Amazon RedShift do Amazon S3, jak je nakonfigurované v možnosti  **redshiftUnloadSettings** . V dalším kroku se data zkopírují z Amazon S3 do úložiště objektů BLOB v Azure, jak je zadané v možnosti **stagingSettings** . Nakonec základ načte data do služby Azure synapse Analytics. Všechny dočasné formáty jsou zpracovávány aktivitou kopírování.

![Kopírování pracovního postupu z Amazon RedShift do Azure synapse Analytics](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>Příklad JSON: kopírování dat z Amazon RedShift do Azure Blob Storage
V této ukázce se dozvíte, jak kopírovat data z databáze Amazon RedShift do Azure Blob Storage. Data lze zkopírovat přímo do jakékoli [podporované jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování.

Ukázka má následující Entity Data Factory:

* Propojená služba typu [AmazonRedshift](#linked-service-properties)
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) [relačních](#dataset-properties) objektů typu
* Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties)
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá vlastnosti [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Ukázka kopíruje data z výsledku dotazu v Amazon RedShift do objektu blob Azure za hodinu. Vlastnosti JSON, které jsou používány v ukázce, jsou popsány v částech, které následují za definicemi entit.

**Propojená služba Amazon RedShift**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Propojená služba Azure Blob Storage**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Vstupní datová sada Amazon RedShift**

Vlastnost **External** je nastavena na hodnotu "true", aby informovala službu Data Factory, že datová sada je externí pro objekt pro vytváření dat. Toto nastavení vlastnosti indikuje, že datová sada není vytvořená aktivitou v datové továrně. Nastavte vlastnost na hodnotu true pro vstupní datovou sadu, která není vytvořená aktivitou v kanálu.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu BLOB každou hodinu nastavením vlastnosti **frekvence** na "hour" (hodina) a vlastností **interval** na 1. Vlastnost **FolderPath** objektu BLOB je dynamicky vyhodnocena. Hodnota vlastnosti je založena na počátečním čase řezu, který je zpracováván. Cesta ke složce používá část roku, měsíce, dne a hodiny počátečního času.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Aktivita kopírování v kanálu se zdrojem Azure RedShift (typu RelationalSource) a jímkou objektů BLOB v Azure**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady. Spuštění kanálu je naplánováno každou hodinu. V definici JSON pro kanál je typ **zdroje** nastavený na **RelationalSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybírá data, která se mají zkopírovat za poslední hodinu.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Mapování typů pro Amazon RedShift
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojového typu na typ jímky. Typy jsou převedeny pomocí dvou kroků přístupu:

1. Převod z nativního zdrojového typu na typ .NET
2. Převod z typu .NET na nativní typ jímky

Následující mapování se používají, když aktivita kopírování převede data z typu Amazon RedShift na typ .NET:

| Typ Amazon RedShift | Typ .NET |
| --- | --- |
| SMALLINT |Int16 |
| CELÉ ČÍSLO |Int32 |
| BIGINT |Int64 |
| NOTACI |Decimal |
| REÁLNÉ |Jednoduché |
| DVOJITÁ PŘESNOST |dvojité |
| DATOVÉHO |Řetězec |
| CHAR |Řetězec |
| VARCHAR |Řetězec |
| DATE |DateTime |
| ČASOVÉ RAZÍTKO |DateTime |
| TEXT |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Informace o tom, jak namapovat sloupce ve zdrojové datové sadě ke sloupcům v datové sadě jímky, najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Opakující se čtení z relačních zdrojů
Při kopírování dat z relačního úložiště dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat **zásady** opakování pro datovou sadu pro opětovné spuštění řezu, když dojde k selhání. Ujistěte se, že jsou stejná data čtena bez ohledu na to, kolikrát je řez znovu spuštěn. Také se ujistěte, že jsou stejná data čtena bez ohledu na to, jak znovu vytváříte řez. Další informace najdete v tématu [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
Přečtěte si o klíčových faktorech, které mají vliv na výkon aktivity kopírování a způsoby optimalizace výkonu v [Průvodci výkonem a optimalizací aktivity kopírování](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Další kroky
Podrobné pokyny pro vytvoření kanálu s aktivitou kopírování najdete v [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
