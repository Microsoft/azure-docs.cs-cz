---
title: Kopírovat data ze služby Amazon Simple Storage Service (S3)
description: Další informace o tom, jak pomocí služby Azure Data Factory kopírování dat z Amazon Simple Storage Service (S3) do úložišť dat podporovaných jímky.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.openlocfilehash: cfad657306d1073e16ac6f3197c96b4413e97bea
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830271"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopírování dat z Amazon Simple Storage Service pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
>
> * [Verze 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuální verze](connector-amazon-simple-storage-service.md)

Tento článek popisuje, jak kopírovat data ze služby Amazon Simple Storage Service (Amazon S3). Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

>[!TIP]
>V případě scénáře migrace dat ze služby Amazon S3 do Azure Storage se dozvíte víc o tom, jak [pomocí Azure Data Factory migrovat data z Amazon S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Amazon S3 se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně tento konektor Amazon S3 podporuje kopírování souborů jako-je nebo analýza souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md). [Při kopírování můžete také zachovat metadata souboru](#preserve-metadata-during-copy). Konektor používá [AWS Signature verze 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) k ověřování požadavků na S3.

>[!TIP]
>Tento konektor Amazon S3 můžete použít ke kopírování dat z **jakéhokoli poskytovatele úložiště kompatibilního se systémem S3** , např. [Google Cloud Storage](connector-google-cloud-storage.md). Zadejte odpovídající adresu URL služby v konfiguraci propojené služby.

## <a name="required-permissions"></a>Požadovaná oprávnění

Zkopírujte data z Amazonu S3, ujistěte se, že máte následující oprávnění:

- **Pro spuštění aktivity kopírování:** : `s3:GetObject` a `s3:GetObjectVersion` pro operace objektu Amazon S3.
- **Pro grafické uživatelské rozhraní Data Factory pro vytváření**: `s3:ListAllMyBuckets` a `s3:ListBucket` / `s3:GetBucketLocation` pro Amazon S3 sady Operations se navíc vyžaduje oprávnění, pro cesty k souborům operací, jako jsou test připojení a procházení a přejděte. Pokud toto oprávnění nechcete udělit, vynechejte test připojení na stránce pro vytvoření propojené služby a zadejte cestu přímo v nastavení datové sady.

Podrobnosti o úplný seznam Amazon S3 oprávnění najdete v tématu [určení oprávnění v zásadách](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní Amazon S3.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Amazon S3 propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AmazonS3**. | Ano |
| accessKeyId | ID tajný přístupový klíč. |Ano |
| secretAccessKey | Vlastního klíče přístupu k tajným klíčům. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| serviceUrl | Pokud kopírujete data z jiného poskytovatele úložiště kompatibilního se systémem S3, než je oficiální služba Amazon S3, určete vlastní koncový bod S3. Pokud například chcete kopírovat data ze služby Google Cloud Storage, zadejte `https://storage.googleapis.com`. | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!TIP]
>Pokud kopírujete data z úložiště kompatibilního se systémem S3, než je oficiální služba Amazon S3, zadejte adresu URL vlastní služby S3.

>[!NOTE]
>Tento konektor vyžaduje přístupové klíče pro účet IAM zkopírovat data z Amazonu S3. [Dočasné bezpečnostním pověřením](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) se nepodporuje.
>

Zde naleznete příklad:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro službu Amazon S3 v části nastavení `location` v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v rámci objektu DataSet `location` musí být nastavena na hodnotu **AmazonS3Location**. | Ano      |
| bucketName | Název sektoru S3.                                          | Ano      |
| folderPath | Cesta ke složce v daném kontejneru. Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru v daném intervalu + folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| version | Verze objektu S3, pokud je povolená Správa verzí S3. Pokud není zadán, bude načtena nejnovější verze. |Ne |

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaný zdroj Amazon S3.

### <a name="amazon-s3-as-source"></a>Jako zdroj Amazon S3

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro službu Amazon S3 v části nastavení `storeSettings` ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **AmazonS3ReadSettings**. | Ano                                                         |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Všimněte si, že pokud rekurzivní je nastavena na hodnotu true a jímku je souborové úložiště prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                                          |
| prefix                   | Předpona klíče objektu S3 v daném kontejneru nakonfigurovaném v datové sadě pro filtrování zdrojových objektů Objekty, jejichž klíče začínat touto předponou vybráno. <br>Platí pouze v případě, že nejsou zadány vlastnosti `wildcardFolderPath` a `wildcardFileName`. | Ne                                                          |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky v daném kontejneru nakonfigurovaném v datové sadě pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                                          |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného intervalu + folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, pokud `fileName` v datové sadě a `prefix` nejsou zadány |
| modifiedDatetimeStart    | Soubory filtru na základě atributu: naposledy upraveno. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná. | Ne                                                          |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                                          |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                                          |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| bucket | key | recursive | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory tučně)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="preserve-metadata-during-copy"></a>Zachovat metadata během kopírování

Když kopírujete soubory ze služby Amazon S3 do Azure Data Lake Storage Gen2/Azure Blob, můžete zvolit, aby se metadata souborů zachovala spolu s daty. Další informace o [zachování metadat](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Další informace o vlastnostech najdete v části [Odstranění aktivity](delete-activity.md) .

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány, protože jsou z důvodu zpětné kompatibility. Navrhnete použití nového modelu uvedeného výše v předchozích částech a uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování nového modelu.

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ datové sady, musí být nastavena na: **AmazonS3Object** |Ano |
| bucketName | Název sektoru S3. Filtr zástupných znaků není podporován. |Ano pro aktivitu kopírování a vyhledávání, ne pro aktivitě GetMetadata |
| key | **Filtrů názvu nebo zástupný znak** S3 klíči objektu určený interval. Platí, pouze když, není-li zadána vlastnost "předponu". <br/><br/>Filtr zástupných znaků je podporován pro část složky i pro název souboru. Povolené zástupné znaky jsou: `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak).<br/>– Příklad 1: `"key": "rootfolder/subfolder/*.csv"`<br/>– Příklad 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Další příklady najdete v příkladech [složky a filtru souborů](#folder-and-file-filter-examples). Pokud vlastní název složky nebo souboru obsahuje zástupný znak nebo řídicí znak v rámci, použijte `^`. |Ne |
| prefix | Předpona klíče objektu S3. Objekty, jejichž klíče začínat touto předponou vybráno. Platí pouze v případě, že není zadána vlastnost "klíče". |Ne |
| version | Verze objektu S3, pokud je povolená Správa verzí S3. Pokud není zadán, bude načtena nejnovější verze. |Ne |
| modifiedDatetimeStart | Soubory filtru na základě atributu: naposledy upraveno. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| modifiedDatetimeEnd | Soubory filtru na základě atributu: naposledy upraveno. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| formát | Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat a generovat soubory s konkrétním formátu, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs-legacy.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimal** a **nejrychlejší**. |Ne |

>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **bucketName** sady a **předponu** pro složkovou část.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **bucketName** sady a **klíč** část plus souboru názvu složky.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **bucketName** sady a **klíč** pro část plus zástupný filtr složek.

**Příklad: použití předpony**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Příklad: použití klíče a verzí (volitelné)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Starší zdrojový model aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **FileSystemSource** |Ano |
| recursive | Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. Poznámka: Pokud rekurzivní je nastavena na hodnotu true a datovou sadu jímky souborové úložiště je prázdné složky/dílčí-folder nebudou zkopírovány/vytvořili na jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, pomocí aktivity kopírování v Azure Data Factory najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md##supported-data-stores-and-formats).
