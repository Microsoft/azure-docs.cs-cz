---
title: Přesun dat ze služby Amazon Simple Storage Service pomocí data factory
description: Přečtěte si, jak přesunout data ze služby Amazon Simple Storage Service (S3) pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b23be9901df7ca435f412d9f49e1a7ad88382ade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281649"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Přesunutí dat ze služby Amazon Simple Storage Service pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Verze 2 (aktuální verze)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma Konektor Amazon S3 ve verzi 2](../connector-amazon-simple-storage-service.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat ze služby Amazon Simple Storage Service (S3). Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled pohybu dat s aktivitou kopírování.

Můžete kopírovat data z Amazon S3 do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v tabulce [Podporovaná úložiště dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory v současné době podporuje pouze přesun dat z Amazon S3 do jiných datových úložišť, ale nepřesouvá data z jiných datových úložišť na Amazon S3.

## <a name="required-permissions"></a>Požadovaná oprávnění
Chcete-li kopírovat data z Amazon S3, ujistěte se, že vám byla udělena následující oprávnění:

* `s3:GetObject`a `s3:GetObjectVersion` pro operace S3 Object Společnosti Amazon.
* `s3:ListBucket`pro amazonské operace S3 Bucket. Pokud používáte Průvodce kopírováním `s3:ListAllMyBuckets` do datové továrny, je také vyžadován.

Podrobnosti o úplném seznamu oprávnění Amazon S3 naleznete [v tématu Zadání oprávnění v zásadách](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data ze zdroje Amazon S3 pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod najdete v [tématu Výuka: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování naleznete v [tématu Kopírování aktivity kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON. Ukázka s definicemi JSON pro entity data factory, které se používají ke kopírování dat z úložiště dat Amazon S3, najdete v [příkladu JSON: Kopírování dat z Amazon S3 do Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) části tohoto článku.

> [!NOTE]
> Podrobnosti o podporovaných formátech souborů a komprese pro aktivitu kopírování najdete [v tématu Soubory a formáty komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Datové továrny specifické pro Amazon S3.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Propojená služba propojuje úložiště dat s továrnou na data. Vytvoříte propojenou službu typu **AwsAccessKey** pro propojení úložiště dat Amazon S3 s vaší továrně dat. Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu Amazon S3 (AwsAccessKey).

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| accessKeyID |ID tajného přístupového klíče. |řetězec |Ano |
| tajný klíč AccessKey |Samotný tajný přístupový klíč. |Šifrovaný tajný řetězec |Ano |

>[!NOTE]
>Tento konektor vyžaduje přístupové klíče pro účet IAM ke kopírování dat z Amazon S3. [Dočasné pověření zabezpečení](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) není podporováno.
>

Zde naleznete příklad:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Chcete-li zadat datovou sadu představující vstupní data v úložišti objektů Blob Azure, nastavte vlastnost type datové sady na **AmazonS3**. Nastavte vlastnost **linkedServiceName** datové sady na název propojené služby Amazon S3. Úplný seznam oddílů a vlastností dostupných pro definování datových sad naleznete v [tématu Vytváření datových sad](data-factory-create-datasets.md). 

Oddíly, jako je struktura, dostupnost a zásady jsou podobné pro všechny typy datových sad (například databáze SQL, objekt blob Azure a tabulka Azure). Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Sekce **typeProperties** pro datovou sadu typu **AmazonS3** (která obsahuje datovou sadu Amazon S3) má následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| název_bloku |Název bloku S3. |Řetězec |Ano |
| key |Klíč objektu S3. |Řetězec |Ne |
| Předponu |Předpona pro klíč objektu S3. Objekty, jejichž klávesy začínají touto předponou, jsou vybrány. Platí pouze v případě, že klíč je prázdný. |Řetězec |Ne |
| version |Verze S3 objektu, pokud je povolena správu verzí S3. |Řetězec |Ne |
| formát | Podporovány jsou následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát Text](data-factory-supported-file-and-compression-formats.md#text-format), [Formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Parkety.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Pokud chcete kopírovat soubory jako -je mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady. | |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně jsou: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |Ne |


> [!NOTE]
> **bucketName + klíč** určuje umístění ObjektU S3, kde kontejner je kořenový kontejner pro objekty S3 a klíč je úplná cesta k objektu S3.

### <a name="sample-dataset-with-prefix"></a>Ukázková datová sada s předponou

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>Ukázková datová sada (s verzí)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Dynamické cesty pro S3
Předchozí ukázka používá pevné hodnoty pro **vlastnosti klíče** a **bucketName** v datové sadě Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Data Factory můžete vypočítat tyto vlastnosti dynamicky za běhu pomocí systémových proměnných, jako je například SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Totéž můžete udělat pro vlastnost **předpony** datové sady Amazon S3. Seznam podporovaných funkcí a proměnných naleznete v tématu [Funkce datové továrny a systémové proměnné](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností dostupných pro definování aktivit naleznete v [tématu Vytváření kanálů](data-factory-create-pipelines.md). Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit. Vlastnosti dostupné v části **typeProperties** aktivity se liší podle jednotlivých typů aktivit. Pro aktivitu kopírování vlastnosti se liší v závislosti na typech zdrojů a jímky. Pokud je zdroj v aktivitě kopírování typu **FileSystemSource** (který zahrnuje Amazon S3), je v části **typeProperties** k dispozici následující vlastnost:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| Rekurzivní |Určuje, zda se mají v adresáři znovu zobrazit seznam objektů S3. |pravda/nepravda |Ne |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Příklad JSON: Kopírování dat z Amazons3 do úložiště objektů blob Azure
Tato ukázka ukazuje, jak zkopírovat data z Amazon S3 do úložiště objektů blob Azure. Data však lze zkopírovat přímo do [některého z jímky, které jsou podporovány](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v factory dat.

Ukázka poskytuje definice JSON pro následující entity Data Factory. Tyto definice můžete použít k vytvoření kanálu ke kopírování dat z Amazon S3 do úložiště objektů Blob pomocí [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Propojená služba typu [AwsAccessKey](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [AmazonS3](#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z AmazonS3 do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

### <a name="amazon-s3-linked-service"></a>Amazon S3 propojené služby

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage

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

### <a name="amazon-s3-input-dataset"></a>Vstupní datová sada Amazon S3

Nastavení **"externí": true** informuje službu Data Factory, že datová sada je externí pro datové továrny. Nastavte tuto vlastnost na hodnotu true na vstupní datové sadě, která není vytvořena aktivitou v kanálu.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Výstupní datová sada Azure Blob

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Kopírování aktivity v kanálu se zdrojem Amazon S3 a jímkou blob

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **FileSystemSource**a typ **jímky** je nastaven na **objekt BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Další kroky
Viz následující články:

* Informace o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivity kopírování) v datové továrně, a o různých způsobech jeho optimalizace najdete v [tématu Průvodce sledováním aktivity kopírování a laděním](data-factory-copy-activity-performance.md).

* Podrobné pokyny k vytvoření kanálu s aktivitou kopírování naleznete v [tématu Kopírování aktivity kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
