---
title: Přesun dat ze služby Amazon jednoduché úložiště pomocí služby Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data ze služby úložiště jednoduché Amazon (S3) pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d895dcdf9eefac01790aab6dc3f36a3feb0a8b12
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051146"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Přesun dat ze služby Amazon jednoduché úložiště pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Verze 2 (aktuální verze)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [Amazon S3 konektoru V2](../connector-amazon-simple-storage-service.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat ze služby úložiště jednoduché Amazon (S3). Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Do úložiště dat žádné podporované podřízený může kopírovat data z Amazonu S3. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z Amazon S3 jiným úložištím dat, ale není přesouvání dat od ostatních dat ukládá do Amazon S3.

## <a name="required-permissions"></a>Požadovaná oprávnění
Pokud chcete zkopírovat data z Amazonu S3, zkontrolujte, zda že máte následující oprávnění:

* `s3:GetObject` a `s3:GetObjectVersion` pro Amazon S3 objekt operace.
* `s3:ListBucket` pro operace sady Amazon S3. Pokud použijete Průvodce kopírováním objekt pro vytváření dat `s3:ListAllMyBuckets` je také nutný.

Podrobnosti o úplný seznam Amazon S3 oprávnění najdete v tématu [zadání oprávnění v zásadách](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z zdroje Amazon S3 s použitím rozhraní API nebo jiných nástrojů.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. Rychlé návod najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **.NET API**, a **rozhraní REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování.
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Pokud používáte rozhraní API (s výjimkou .NET API) nebo nástroje, definujete tyto entity služby Data Factory pomocí formátu JSON. Ukázku s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat Amazon S3, najdete [JSON příklad: kopírování dat z Amazon S3 do objektu Blob Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) tohoto článku.

> [!NOTE]
> Podrobnosti o podporovaných formátech souborů a komprese pro aktivitu kopírování najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k definování entit služby Data Factory, které jsou specifické pro Amazon S3.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Propojená služba odkazuje na objekt pro vytváření dat úložiště dat. Vytvoření propojené služby typu **AwsAccessKey** propojit data store Amazon S3 s datovou továrnu. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro službu Amazon S3 (AwsAccessKey) propojená služba.

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| accessKeyID |ID tajný přístupový klíč. |řetězec |Ano |
| secretAccessKey |Tajný přístupový klíč sám sebe. |Šifrované tajné řetězec |Ano |

>[!NOTE]
>Tento konektor vyžaduje přístupové klíče pro účet IAM ke zkopírování dat z Amazon S3. [Dočasné pověření zabezpečení](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) není podporován.
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
Chcete-li zadat datové sady představují vstupní data v úložišti objektů Blob v Azure, nastavte vlastnost typu datové sady, která **AmazonS3**. Nastavte **linkedServiceName** vlastnosti datové sady, která název Amazon S3 propojené služby. Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v části [vytváření datových sad](data-factory-create-datasets.md). 

Oddíly jako je například struktura, dostupnost a zásady jsou podobné pro všechny typy datové sady (například databáze SQL, objektů blob v Azure a tabulky Azure). **Rámci typeProperties** části se liší pro jednotlivé typy datovou sadu a poskytuje informace o umístění dat v úložišti. **Rámci typeProperties** části datové sady typu **AmazonS3** (což zahrnuje datová sada Amazon S3) má následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| bucketName |Název sady S3. |Řetězec |Ano |
| key |Klíč objektu S3. |Řetězec |Ne |
| Předpona |Předpona pro klíč objektu S3. Jsou vybrané objekty, jejichž klíče začít s touto předponou. Platí pouze v případě, klíč je prázdný. |Řetězec |Ne |
| verze |Verze objektu S3, pokud je povolena Správa verzí S3. |Řetězec |Ne |
| Formát | Jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formátu ](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete zkopírovat soubory jako-je mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. |Ne | |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne | |


> [!NOTE]
> **bucketName + klíč** Určuje umístění objektu S3, kde sady je kořenový kontejner pro objekty S3 a klíč je úplná cesta k objektu S3.

### <a name="sample-dataset-with-prefix"></a>Ukázkovou datovou sadu s předponou

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
### <a name="sample-dataset-with-version"></a>Ukázkovou datovou sadu (verze)

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
V předchozím příkladu používá pevné hodnoty **klíč** a **bucketName** vlastnosti v datové sadě Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Můžete mít vypočítat tyto vlastnosti dynamicky za běhu, pomocí systémové proměnné, jako je například SliceStart služby Data Factory.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Totéž proveďte pro **předponu** vlastnost datové sadě služby Amazon S3. Seznam podporovaných funkce a proměnné, naleznete v části [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md). Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v **rámci typeProperties** části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování vlastnosti lišit v závislosti na typech zdrojů a jímky. Pokud je zdroj v aktivitě kopírování typu **FileSystemSource** (která zahrnuje Amazon S3), je k dispozici v této vlastnosti **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, jestli k rekurzivnímu seznamu S3 objekty v adresáři. |hodnotu true nebo false |Ne |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Příklad JSON: kopírování dat z Amazonu S3 do úložiště objektů Blob v Azure
Tento příklad ukazuje, jak zkopírovat data z Amazonu S3 do Azure Blob storage. Ale data se dají zkopírovat přímo na [žádné jímky, které jsou podporovány](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v datové továrně.

Ukázka poskytuje JSON definice pro následující entity služby Data Factory. Tyto definice můžete použít k vytvoření kanálu zkopírovat data z Amazonu S3 do úložiště objektů Blob, pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Propojené služby typu [AwsAccessKey](#linked-service-properties).
* Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstup [datovou sadu](data-factory-create-datasets.md) typu [AmazonS3](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z Amazonu S3 do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

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

### <a name="amazon-s3-input-dataset"></a>Vstupní datové sady Amazon S3

Nastavení **"externí": true** informuje služba Data Factory, že je externí k objektu pro vytváření dat datové sady. Nastavte tuto vlastnost na hodnotu true vstupní datovou sadu, která není vyprodukované aktivitu v kanálu.

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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas části čas spuštění.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Aktivita kopírování v kanálu s na Amazon S3 zdroj a jímka objektů blob

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource**, a **podřízený** je typ nastaven na **BlobSink**.

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
> Mapování sloupců z datové sady zdroje na sloupce ze sady dat podřízený naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Další postup
Viz následující články:

* Další informace o klíčových faktorů této dopad výkon přesun dat (aktivita kopírování) v objektu pro vytváření dat a různé způsoby, jak ji optimalizovat, najdete v článku [zkopírujte aktivity výkonu a vyladění průvodce](data-factory-copy-activity-performance.md).

* Podrobné pokyny pro vytvoření kanálu s aktivitou kopírování najdete v tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
