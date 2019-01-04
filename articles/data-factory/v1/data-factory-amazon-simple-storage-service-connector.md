---
title: Přesun dat z Amazon Simple Storage Service pomocí služby Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak přesunout data z Amazon Simple Storage Service (S3) pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fda80455cf3504bf992fabc3018be2d5c05612ae
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019142"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Přesun dat z Amazon Simple Storage Service pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Verze 2 (aktuální verze)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor Amazon S3 ve verzi V2](../connector-amazon-simple-storage-service.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování ve službě Azure Data Factory k přesunu dat z Amazon Simple Storage Service (S3). Je nástavbou [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který nabízí obecný přehled o přesun dat pomocí aktivity kopírování.

Kopírování dat z Amazon S3 do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky, najdete v článku [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Data Factory v současné době podporuje pouze přesouvá data z Amazonu S3 do jiných úložišť dat, ale ne přesouvá data z jiných data ukládá na Amazon S3.

## <a name="required-permissions"></a>Požadovaná oprávnění
Zkopírujte data z Amazonu S3, ujistěte se, že máte následující oprávnění:

* `s3:GetObject` a `s3:GetObjectVersion` pro operace objektu Amazon S3.
* `s3:ListBucket` pro operace kbelíku Amazon S3. Pokud použijete Průvodce kopírováním objekt pro vytváření dat, `s3:ListAllMyBuckets` je také nutný.

Podrobnosti o úplný seznam Amazon S3 oprávnění najdete v tématu [určení oprávnění v zásadách](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z Amazonu S3 zdroje pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý postup najdete v části [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

Tyto nástroje můžete také použít k vytvoření kanálu: **Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **rozhraní .NET API**a  **Rozhraní REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Ať už používáte, nástrojů nebo rozhraní API, proveďte následující kroky k vytvoření kanálu pro přesouvání dat ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do služby data factory.
2. Vytvoření **datových sad** k představují vstupní a výstupní data pro operaci kopírování.
3. Vytvoření **kanálu** s aktivitou kopírování, která přijímá jako vstupní datovou sadu a datovou sadu jako výstup.

Při použití Průvodce definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál) se automaticky vytvoří za vás. Při použití nástroje nebo rozhraní API (s výjimkou rozhraní .NET API), můžete definovat tyto entity služby Data Factory ve formátu JSON. Tady je příklad s definice JSON entit služby Data Factory, které se používají ke kopírování dat z úložiště dat Amazon S3, najdete v článku [příklad JSON: Zkopírujte data z Amazonu S3 do objektů Blob v Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) části tohoto článku.

> [!NOTE]
> Podrobnosti o podporovaných formátech souborů a komprese pro aktivitu kopírování, která najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Následující části obsahují podrobnosti o vlastnostech JSON, které se používají k definování entit služby Data Factory konkrétní Amazon S3.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Propojená služba propojuje úložiště dat do služby data factory. Vytvoření propojené služby typu **typu AwsAccessKey** k propojení vašeho úložiště Amazon S3 do služby data factory. Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro Amazon S3 (typu AwsAccessKey) propojenou službu.

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| accessKeyID |ID tajný přístupový klíč. |řetězec |Ano |
| secretAccessKey |Vlastního klíče přístupu k tajným klíčům. |Zašifrovaný řetězec tajného kódu |Ano |

>[!NOTE]
>Tento konektor vyžaduje přístupové klíče pro účet IAM zkopírovat data z Amazonu S3. [Dočasné bezpečnostním pověřením](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) se nepodporuje.
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
Pokud chcete datovou sadu, která představuje vstupní data v Azure Blob storage, nastavte vlastnost typ datové sady na **AmazonS3**. Nastavte **linkedServiceName** vlastnosti datové sady na název Amazon S3 propojenou službu. Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [vytváření datových sad](data-factory-create-datasets.md). 

Oddíly, jako je například struktura, dostupnost a zásady jsou podobné pro všechny typy datovou sadu (jako je SQL database, Azure blob a tabulek v Azure). **TypeProperties** oddílu se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti. **TypeProperties** části datové sady typu **AmazonS3** (což zahrnuje datová sada Amazon S3) má následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| bucketName |Název sektoru S3. |Řetězec |Ano |
| key |Klíč objektu S3. |Řetězec |Ne |
| Předpona |Předpona klíče objektu S3. Objekty, jejichž klíče začínat touto předponou vybráno. Platí pouze v případě, klíč je prázdný. |Řetězec |Ne |
| version |Verze objektu S3, pokud je povolená Správa verzí S3. |Řetězec |Ne |
| formát | Jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formát Orc](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet ](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. <br><br> Pokud chcete zkopírovat soubory jako-je mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. |Ne | |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimální** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne | |


> [!NOTE]
> **bucketName + klávesy** Určuje umístění objektu S3, pokud blok je kořenový kontejner pro objekty S3 a klíč je úplná cesta k objektu S3.

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
### <a name="sample-dataset-with-version"></a>Ukázkové datové sadě (verze)

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
V předchozím příkladu používá pevné hodnoty **klíč** a **bucketName** vlastnosti v datovou sadu Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Může mít tyto vlastnosti dynamicky za běhu, vypočítat pomocí systémové proměnné, jako jsou SliceStart služby Data Factory.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Totéž proveďte pro **předponu** vlastnost datová sada služby Amazon S3. Seznam podporovaných funkcí a proměnných najdete v tématu [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md). Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit. K dispozici ve vlastnosti **typeProperties** části aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování vlastnosti lišit v závislosti na typy zdroje a jímky. Pokud je zdroj v aktivitě kopírování typu **FileSystemSource** (která zahrnuje Amazon S3), následující vlastnost je k dispozici v **typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| rekurzivní |Určuje, zda rekurzivně seznamu S3 objekty v adresáři. |True nebo false |Ne |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Příklad JSON: Zkopírujte data z Amazonu S3 do úložiště objektů Blob v Azure
Tento příklad ukazuje, jak kopírovat data z Amazonu S3 do úložiště objektů Blob v Azure. Ale data se dají zkopírovat přímo na [libovolné jímky, které jsou podporovány](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování ve službě Data Factory.

Tento vzorový definice JSON pro následující entity služby Data Factory. Tyto definice můžete použít k vytvoření kanálu pro kopírování dat z Amazonu S3 do úložiště objektů Blob pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [Powershellu](data-factory-copy-activity-tutorial-using-powershell.md).   

* Propojené služby typu [typu AwsAccessKey](#linked-service-properties).
* Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [AmazonS3](#dataset-properties).
* Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázce kopíruje data z Amazonu S3 do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v tyto ukázky jsou popsány v části podle ukázky.

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

Nastavení **"externí": true** informuje služby Data Factory, že tato datová sada je externí s datovou továrnou. Nastavte tuto vlastnost na hodnotu true pro vstupní datovou sadu, která není vytvořena podle aktivity v kanálu.

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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob se dynamicky vyhodnocuje na základě doby spuštění řez, který se právě zpracovává. Cesta ke složce používá rok, měsíc, den a části hodin čas spuštění.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Aktivita kopírování v kanálu s zdroj Amazon S3 a objektů blob jímky

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **FileSystemSource**, a **jímky** je typ nastaven na **BlobSink**.

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
> Pokud chcete namapovat sloupce ze zdrojovou datovou sadu na sloupce z datové sady jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Další postup
Viz následující články:

* Další informace o klíčových faktorů této ovlivnit výkon přesouvání dat (aktivita kopírování) ve službě Data Factory a různé způsoby, jak optimalizovat, najdete v článku [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md).

* Podrobné pokyny pro vytvoření kanálu s aktivitou kopírování najdete v tématu [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
