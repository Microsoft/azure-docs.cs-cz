---
title: Přesun dat ze služby Amazon Simple Storage pomocí Data Factory
description: Přečtěte si, jak přesunout data ze služby Amazon Simple Storage Service (S3) pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6d254a535b1db53478772b481bd029a8c4db6f3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361342"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Přesun dat ze služby Amazon Simple Storage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Verze 2 (aktuální verze)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Amazon S3 Connector ve verzi v2](../connector-amazon-simple-storage-service.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data ze služby Amazon Simple Storage Service (S3). Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data z databáze Amazon S3 můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje pouze přesun dat z Amazon S3 do jiných úložišť dat, ale nepřesouvá data z jiných úložišť dat do Amazon S3.

## <a name="required-permissions"></a>Požadovaná oprávnění
Pokud chcete kopírovat data ze služby Amazon S3, ujistěte se, že máte udělena následující oprávnění:

* `s3:GetObject` a `s3:GetObjectVersion` pro operace s objekty Amazon S3.
* `s3:ListBucket` pro operace s kontejnerem Amazon S3. Pokud používáte Průvodce kopírováním Data Factory, `s3:ListAllMyBuckets` je také vyžadován.

Podrobnosti o úplném seznamu oprávnění Amazon S3 najdete v tématu [určení oprávnění v zásadě](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data ze zdroje Amazon S3 pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje nebo rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázku s definicemi JSON pro Data Factory entity, které se používají ke kopírování dat z úložiště dat Amazon S3, najdete v části [příklad JSON: kopírování dat z databáze Amazon S3 do Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) tohoto článku.

> [!NOTE]
> Podrobnosti o podporovaných formátech souborů a kompresi pro aktivitu kopírování naleznete [v tématu formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Následující části obsahují podrobné informace o vlastnostech JSON, které slouží k definování Data Factory entit specifických pro službu Amazon S3.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Propojená služba propojuje úložiště dat s datovou továrnou. Vytvoříte propojenou službu typu **AwsAccessKey** a propojíte úložiště dat Amazon S3 s datovou továrnou. Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu Amazon S3 (AwsAccessKey).

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| accessKeyID |ID tajného přístupového klíče |řetězec |Yes |
| secretAccessKey |Tajný přístupový klíč sám o sobě. |Šifrovaný tajný řetězec |Yes |

>[!NOTE]
>Tento konektor vyžaduje ke kopírování dat z Amazon S3 přístupové klíče pro účet IAM. [Dočasné přihlašovací údaje zabezpečení](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nejsou podporovány.
>

Tady je příklad:

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
Pokud chcete určit datovou sadu, která bude představovat vstupní data ve službě Azure Blob Storage, nastavte vlastnost Type datové sady na **AmazonS3**. Nastavte vlastnost **linkedServiceName** datové sady na název propojené služby Amazon S3. Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [vytváření datových sad](data-factory-create-datasets.md). 

Oddíly, jako jsou struktura, dostupnost a zásady, jsou podobné pro všechny typy datových sad (například SQL Database, Azure Blob a Azure Table). Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu typu **AmazonS3** (která zahrnuje datovou sadu Amazon S3) má následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| interval intervalu |Název intervalu S3. |Řetězec |Yes |
| key |Klíč objektu S3. |Řetězec |No |
| směr |Prefix pro klíč objektu S3 Jsou vybrány objekty, jejichž klíče začínají touto předponou. Platí pouze v případě, že klíč je prázdný. |Řetězec |No |
| verze |Verze objektu S3, pokud je povolená Správa verzí S3 |Řetězec |No |
| formát | Podporovány jsou následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v oddílech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), formát [ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a formát [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Pokud chcete kopírovat soubory mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady. | |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy jsou: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně jsou: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |No |


> [!NOTE]
> **interval intervalu + klíč** určuje umístění objektu S3, kde je kontejner kořenovým kontejnerem pro objekty S3 a klíč je úplná cesta k objektu S3.

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
Předchozí příklad používá v datové sadě Amazon S3 pevné hodnoty vlastností **klíč** a **interval** .

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Tyto vlastnosti můžete Data Factory vypočítat dynamicky za běhu, a to pomocí systémových proměnných, jako je vlastnosti slicestart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Můžete to samé udělat pro vlastnost **prefix** datové sady Amazon S3. Seznam podporovaných funkcí a proměnných naleznete v tématu [Data Factory functions a System Variables](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md). Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásady. Vlastnosti, které jsou k dispozici v části **typeProperties** v aktivitě, se liší podle typu aktivity. U aktivity kopírování se vlastnosti liší v závislosti na typech zdrojů a jímky. Pokud je zdroj v aktivitě kopírování typu **FileSystemSource** (který zahrnuje Amazon S3), je v části **typeProperties** k dispozici následující vlastnost:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| zahrnout |Určuje, jestli se mají rekurzivně zobrazovat objekty S3 v rámci adresáře. |true nebo false |No |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>Příklad JSON: kopírování dat z Amazon S3 do Azure Blob Storage
V této ukázce se dozvíte, jak kopírovat data z Amazon S3 do úložiště objektů BLOB v Azure. Data se ale dají zkopírovat přímo do [kterékoli z jímky, které jsou podporované](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Data Factory.

Ukázka poskytuje definice JSON pro následující Data Factory entity. Tyto definice můžete použít k vytvoření kanálu pro kopírování dat z Amazon S3 do úložiště objektů BLOB pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [PowerShellu](data-factory-copy-activity-tutorial-using-powershell.md).   

* Propojená služba typu [AwsAccessKey](#linked-service-properties).
* Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Vstupní [datová sada](data-factory-create-datasets.md) typu [AmazonS3](#dataset-properties).
* Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
* [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [FileSystemSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z Amazon S3 do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

### <a name="amazon-s3-linked-service"></a>Propojená služba Amazon S3

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

Nastavení **"External": hodnota true** informuje službu Data Factory, že datová sada je externí pro datovou továrnu. Nastavte tuto vlastnost na true pro vstupní datovou sadu, která není vytvořená aktivitou v kanálu.

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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část roku, měsíce, dne a hodiny počátečního času.

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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Aktivita kopírování v kanálu se zdrojem Amazon S3 a jímkou objektů BLOB

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady, a je naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **FileSystemSource** a typ **jímky** je nastavený na **BlobSink**.

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
> Chcete-li namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Další kroky
Viz následující články:

* Další informace o klíčových faktorech, které mají vliv na výkon pohybu dat (aktivita kopírování) v Data Factory a různé způsoby jejich optimalizace, najdete v [Průvodci výkonem a laděním aktivity kopírování](data-factory-copy-activity-performance.md).

* Podrobné pokyny pro vytvoření kanálu s aktivitou kopírování najdete v [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
