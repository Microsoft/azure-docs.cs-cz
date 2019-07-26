---
title: Kopírování dat z Amazon Simple Storage Service (S3) pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak pomocí služby Azure Data Factory kopírování dat z Amazon Simple Storage Service (S3) do úložišť dat podporovaných jímky.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 088dfdbfbadfa43dc2bd161f56f0e2a6dbb94bb7
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311993"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopírování dat z Amazon Simple Storage Service pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, který používáte:"]
>
> * [Verze 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuální verze](connector-amazon-simple-storage-service.md)

Tento článek popisuje, jak kopírovat data z Amazon Simple Storage Service (Amazon S3). Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Amazon S3 je podporována pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporované matice zdroj/jímka](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Konkrétně tento konektor Amazon S3 podporuje kopírování souborů jako-je nebo analýza souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md). Používá [AWS podpis verze 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) k ověření požadavků na S3.

>[!TIP]
>Tento konektor Amazon S3 můžete použít ke zkopírování dat z **všechny zprostředkovatele úložiště kompatibilního S3** například [Google Cloud Storage](connector-google-cloud-storage.md). Zadejte adresu URL odpovídající služby v konfiguraci propojenou službu.

## <a name="required-permissions"></a>Požadovaná oprávnění

Zkopírujte data z Amazonu S3, ujistěte se, že máte následující oprávnění:

- **Pro spuštění aktivity kopírování:** : `s3:GetObject` a `s3:GetObjectVersion` pro operace objektu Amazon S3.
- **Pro grafické uživatelské rozhraní Data Factory pro vytváření**: `s3:ListAllMyBuckets` a `s3:ListBucket` / `s3:GetBucketLocation` pro Amazon S3 sady Operations se navíc vyžaduje oprávnění, pro cesty k souborům operací, jako jsou test připojení a procházení a přejděte. Pokud nechcete, aby pro udělení těchto oprávnění, přeskočit test připojení na stránce pro vytvoření propojené služby a zadejte cestu přímo v nastavení datové sady.

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
| serviceUrl | Zadejte vlastní koncový bod S3, pokud data kopírujete text z poskytovatele úložiště kompatibilního se S3 než oficiální služby Amazon S3. Například pokud chcete zkopírovat data z Google Cloud Storage, zadejte `https://storage.googleapis.com`. | Ne |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!TIP]
>Zadejte vlastní adresu URL služby S3, pokud se kopírování dat z úložiště kompatibilního se S3 než oficiální služby Amazon S3.

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

- Pro **Parquet a formátu odděleného textu**, odkazovat na [datovou sadu formátu Parquet a text s oddělovači](#parquet-and-delimited-text-format-dataset) oddílu.
- Pro ostatní formáty, jako jsou **formát ORC nebo Avro/JSON nebo binární**, odkazovat na [jiné datové sady formátu](#other-format-dataset) oddílu.

### <a name="parquet-and-delimited-text-format-dataset"></a>Datová sada formátu parquet a text s oddělovači

Zkopírovat data z Amazonu S3 v **Parquet nebo formátu odděleného textu**, odkazovat na [formát Parquet](format-parquet.md) a [formátu textu odděleného](format-delimited-text.md) článek na datové sadě založené na formátu a podporované nastavení. Následující vlastnosti jsou podporovány pro Amazon S3 ve skupinovém rámečku `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type v rámci `location` v datové sadě musí být nastaveno na **AmazonS3Location**. | Ano      |
| bucketName | Název sektoru S3.                                          | Ano      |
| folderPath | Cesta ke složce v daném kontejneru. Pokud chcete použít zástupný znak, do složky filtr, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru v daném intervalu + folderPath. Pokud chcete použít zástupný znak pro filtr souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| version | Verze objektu S3, pokud je povolená Správa verzí S3. Pokud není zadán, se načtou nejnovější verzi. |Ne |

> [!NOTE]
> **AmazonS3Object** typ datové sady uvedené v další části formát Parquet/textu je stále podporovány jako-pro aktivitu kopírování a vyhledávání/GetMetadata pro zpětnou kompatibilitu, ale nefunguje s mapování toku dat je. Byly navrženy používat tento nový model do budoucna a ADF vytváření uživatelského rozhraní se přepnulo na generování tyto nové typy.

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

### <a name="other-format-dataset"></a>Další datové sady formátu

Chcete-li kopírovat data z Amazonu S3 v **formát ORC nebo Avro/JSON nebo binární**, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **AmazonS3Object** |Ano |
| bucketName | Název sektoru S3. Filtr zástupných znaků není podporován. |Ano pro aktivitu kopírování a vyhledávání, ne pro aktivitě GetMetadata |
| key | **Filtrů názvu nebo zástupný znak** S3 klíči objektu určený interval. Platí, pouze když, není-li zadána vlastnost "předponu". <br/><br/>Filtr zástupných znaků je podporován pro složkovou část i část názvu souboru. Povolené zástupné znaky jsou: `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak).<br/>– Příklad 1: `"key": "rootfolder/subfolder/*.csv"`<br/>– Příklad 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Další příklad v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). Použití `^` řídicí, pokud má vaše skutečná složka či soubor název zástupných znaků nebo tento znak escape uvnitř. |Ne |
| prefix | Předpona klíče objektu S3. Objekty, jejichž klíče začínat touto předponou vybráno. Platí pouze v případě, že není zadána vlastnost "klíče". |Ne |
| version | Verze objektu S3, pokud je povolená Správa verzí S3. Pokud není zadán, se načtou nejnovější verzi. |Ne |
| modifiedDatetimeStart | Filtr souborů na základě atributu: Poslední změny. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Mějte na paměti, že bude mít vliv na celkový výkon přesun dat povolení tohoto nastavení, pokud chcete soubor filtrů z obrovské množství souborů. <br/><br/> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě atributu: Poslední změny. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Mějte na paměti, že bude mít vliv na celkový výkon přesun dat povolení tohoto nastavení, pokud chcete soubor filtrů z obrovské množství souborů. <br/><br/> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| format | Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat a generovat soubory s konkrétním formátu, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimální** a **nejrychlejší**. |Ne |

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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaný zdroj Amazon S3.

### <a name="amazon-s3-as-source"></a>Jako zdroj Amazon S3

- Pro kopírování z **Parquet a formátu odděleného textu**, odkazovat na [Parquet a zdroj obsahující text oddělený znaky formátu](#parquet-and-delimited-text-format-source) oddílu.
- Pro kopírování z dalších formátech, jako jsou **formát ORC nebo Avro/JSON nebo binární**, odkazovat na [jiný formát zdroj](#other-format-source) oddílu.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet a zdroj formátu odděleného textu

Zkopírovat data z Amazonu S3 v **Parquet nebo formátu odděleného textu**, odkazovat na [formát Parquet](format-parquet.md) a [formátu textu odděleného](format-delimited-text.md) článek věnovaný tomu zdroj aktivity kopírování založená na formát a podporovaná nastavení. Následující vlastnosti jsou podporovány pro Amazon S3 ve skupinovém rámečku `storeSettings` nastavení zdroje kopírování založená na formát:

| Vlastnost                 | Popis                                                  | Požaduje se                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Vlastnost type v rámci `storeSettings` musí být nastaveno na **AmazonS3ReadSetting**. | Ano                                                         |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Všimněte si, že pokud rekurzivní je nastavena na hodnotu true a jímku je souborové úložiště prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                                          |
| prefix                   | Předpona pro S3 klíče objektu v rámci dané sady nakonfigurované v datové sadě na filtr zdrojové objekty. Objekty, jejichž klíče začínat touto předponou vybráno. <br>Platí pouze tehdy, když `wildcardFolderPath` a `wildcardFileName` nejsou zadané vlastnosti. | Ne                                                          |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky v rámci dané sady nakonfigurované v datové sadě do složky zdrojového filtru. <br>Povolené zástupné znaky jsou: `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak); použijte `^` řídicí Pokud název skutečné složky obsahuje zástupný znak nebo tento znak escape uvnitř. <br>Další příklady naleznete v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). | Ne                                                          |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci dané sady + folderPath/wildcardFolderPath ke zdrojovým souborům filtru. <br>Povolené zástupné znaky jsou: `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak); použijte `^` řídicí Pokud název skutečné složky obsahuje zástupný znak nebo tento znak escape uvnitř.  Další příklady naleznete v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). | Ano, pokud `fileName` v datové sadě a `prefix` nejsou zadané |
| modifiedDatetimeStart    | Filtr souborů na základě atributu: Poslední změny. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná. | Ne                                                          |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                                          |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti storage současně. Zadejte pouze v případě, že chcete omezit souběžných připojení k úložišti. | Ne                                                          |

> [!NOTE]
> Pro formátu Parquet nebo odděleného textu **FileSystemSource** zdroj aktivity kopírování typů uvedených v následující části je stále podporovány jako-je z důvodu zpětné kompatibility. Byly navrženy používat tento nový model do budoucna a ADF vytváření uživatelského rozhraní se přepnulo na generování tyto nové typy.

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
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

#### <a name="other-format-source"></a>Jiný zdroj formátu

Zkopírovat data z Amazonu S3 v **formát ORC nebo Avro/JSON nebo binární**, jsou podporovány následující vlastnosti v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **FileSystemSource** |Ano |
| recursive | Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. Poznámka: Pokud rekurzivní je nastavena na hodnotu true a datovou sadu jímky souborové úložiště je prázdné složky/dílčí-folder nebudou zkopírovány/vytvořili na jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžných připojení k úložišti. | Ne |

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

### <a name="folder-and-file-filter-examples"></a>Složky a příklady filtr souborů

Tato část popisuje výsledné chování název složky a cesta k souboru s filtry zástupný znak.

| bucket | key | recursive | Zdrojové složky struktury a filtrování výsledků (soubory tučným písmem jsou načteny)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false (nepravda) | plechovka<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true (pravda) | plechovka<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false (nepravda) | plechovka<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true (pravda) | plechovka<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných aktivitou kopírování ve službě Azure Data Factory jako zdroje a jímky najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
