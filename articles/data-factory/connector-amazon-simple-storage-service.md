---
title: Kopírování dat ze služby Amazon Simple Storage Service (S3) pomocí Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak kopírovat data ze služby Amazon Simple Storage Service (S3) do podporovaných úložišť dat jímky pomocí Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 3aa1284212ce12db2e94d8e753d3d75cf8a44ba8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931182"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopírování dat ze služby Amazon Simple Storage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
>
> * [Verze 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuální verze](connector-amazon-simple-storage-service.md)

Tento článek popisuje, jak kopírovat data ze služby Amazon Simple Storage Service (Amazon S3). Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

>[!TIP]
>V případě scénáře migrace dat ze služby Amazon S3 do Azure Storage se dozvíte víc o tom, jak [pomocí Azure Data Factory migrovat data z Amazon S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Amazon S3 se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně tato konektor Amazon S3 podporuje kopírování souborů tak, jak jsou, nebo k analýze souborů s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md). K ověření požadavků na S3 používá [signaturu AWS verze 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) .

>[!TIP]
>Tento konektor Amazon S3 můžete použít ke kopírování dat z **jakéhokoli poskytovatele úložiště kompatibilního se systémem S3** , např. [Google Cloud Storage](connector-google-cloud-storage.md). Zadejte odpovídající adresu URL služby v konfiguraci propojené služby.

## <a name="required-permissions"></a>Požadovaná oprávnění

Pokud chcete kopírovat data ze služby Amazon S3, ujistěte se, že máte udělena následující oprávnění:

- **Pro provádění aktivit kopírování:** : `s3:GetObject` a `s3:GetObjectVersion` pro operace s objekty Amazon S3.
- **Data Factory vytváření grafického uživatelského rozhraní**: `s3:ListAllMyBuckets` a `s3:ListBucket`/`s3:GetBucketLocation` pro přístup k operacím s kontejnerem Amazon S3 se navíc vyžadují pro operace, jako je test připojení a procházení/procházení cest k souborům. Pokud toto oprávnění nechcete udělit, vynechejte test připojení na stránce pro vytvoření propojené služby a zadejte cestu přímo v nastavení datové sady.

Podrobnosti o úplném seznamu oprávnění Amazon S3 najdete v tématu [určení oprávnění v zásadě](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro službu Amazon S3.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Amazon S3 jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AmazonS3**. | Ano |
| accessKeyId | ID tajného přístupového klíče |Ano |
| secretAccessKey | Tajný přístupový klíč sám o sobě. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| serviceUrl | Pokud kopírujete data z jiného poskytovatele úložiště kompatibilního se systémem S3, než je oficiální služba Amazon S3, určete vlastní koncový bod S3. Pokud například chcete kopírovat data ze služby Google Cloud Storage, zadejte `https://storage.googleapis.com`. | Ne |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud je úložiště dat umístěné v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

>[!TIP]
>Pokud kopírujete data z úložiště kompatibilního se systémem S3, než je oficiální služba Amazon S3, zadejte adresu URL vlastní služby S3.

>[!NOTE]
>Tento konektor vyžaduje ke kopírování dat z Amazon S3 přístupové klíče pro účet IAM. [Dočasné přihlašovací údaje zabezpečení](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nejsou podporovány.
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro službu Amazon S3 v nastavení `location` v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v objektu DataSet `location` v datové sadě musí být nastavena na hodnotu **AmazonS3Location**. | Ano      |
| interval intervalu | Název intervalu S3.                                          | Ano      |
| folderPath | Cesta ke složce v daném kontejneru. Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru v daném intervalu + folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| version | Verze objektu S3, pokud je povolená Správa verzí S3 Pokud není zadán, bude načtena nejnovější verze. |Ne |

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

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

>[!NOTE]
>Následující model DataSet je stále podporován tak, jak je kvůli zpětné kompatibilitě. Navrhnete použití nového modelu, který je uvedený výše, a uživatelské rozhraní pro vytváření ADF se přepnulo na generování nového modelu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **AmazonS3Object** . |Ano |
| interval intervalu | Název intervalu S3. Filtr zástupných znaků se nepodporuje. |Ano pro aktivitu kopírování/vyhledávání, ne pro aktivitu GetMetadata |
| key | **Název nebo zástupný filtr** klíče objektu S3 v zadaném intervalu. Platí pouze v případě, že není zadána vlastnost prefix. <br/><br/>Filtr zástupných znaků je podporován pro část složky i pro název souboru. Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>-Příklad 1: `"key": "rootfolder/subfolder/*.csv"`<br/>-Příklad 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Další příklady najdete v příkladech [složky a filtru souborů](#folder-and-file-filter-examples). Pokud vlastní název složky nebo souboru obsahuje zástupný znak nebo řídicí znak v rámci, použijte `^`. |Ne |
| směr | Prefix pro klíč objektu S3 Jsou vybrány objekty, jejichž klíče začínají touto předponou. Platí pouze v případě, že není zadána vlastnost Key. |Ne |
| version | Verze objektu S3, pokud je povolená Správa verzí S3 Pokud není zadán, bude načtena nejnovější verze. |Ne |
| modifiedDatetimeStart | Filtr souborů na základě atributu: Naposledy změněno Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeStart`, ale `modifiedDatetimeEnd` má hodnotu NULL, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeEnd`, ale `modifiedDatetimeStart` má hodnotu NULL, znamená to, že se jedná o soubory, jejichž atribut Last Modified je nižší, než je vybrána hodnota DateTime.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě atributu: Naposledy změněno Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeStart`, ale `modifiedDatetimeEnd` má hodnotu NULL, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeEnd`, ale `modifiedDatetimeStart` má hodnotu NULL, znamená to, že se jedná o soubory, jejichž atribut Last Modified je nižší, než je vybrána hodnota DateTime.| Ne |
| formátovat | Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](supported-file-formats-and-compression-codecs.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formát ORC](supported-file-formats-and-compression-codecs.md#orc-format)a formátování [Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Ne (jenom pro binární scénář kopírování) |
| komprese | Zadejte typ a úroveň komprese dat. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **gzip**, **Deflate**, **bzip2**a **ZipDeflate**.<br/>Podporované úrovně: **optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory ve složce, zadejte **interval** pro část interval a **prefix** pro součást složky.<br>Chcete-li zkopírovat jeden soubor se zadaným názvem, zadejte název **intervalu** pro interval a **klíč** pro část složky plus název souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **interval** pro odblokování a **klíč** pro část složky a filtr zástupných znaků.

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

**Příklad: použití klíče a verze (volitelné)**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Amazon S3 v nastavení `storeSettings` ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavena na hodnotu **AmazonS3ReadSetting**. | Ano                                                         |
| zahrnout                | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                                          |
| směr                   | Předpona klíče objektu S3 v daném kontejneru nakonfigurovaném v datové sadě pro filtrování zdrojových objektů Jsou vybrány objekty, jejichž klíče začínají touto předponou. <br>Platí pouze v případě, že nejsou zadány vlastnosti `wildcardFolderPath` a `wildcardFileName`. | Ne                                                          |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky v daném kontejneru nakonfigurovaném v datové sadě pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                                          |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného intervalu + folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, pokud `fileName` v datové sadě a `prefix` nejsou zadány |
| modifiedDatetimeStart    | Filtr souborů na základě atributu: Naposledy změněno Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeStart`, ale `modifiedDatetimeEnd` má hodnotu NULL, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud má hodnota DateTime hodnotu `modifiedDatetimeEnd`, ale `modifiedDatetimeStart` má hodnotu NULL, znamená to, že se jedná o soubory, jejichž atribut Last Modified je nižší, než je vybrána hodnota DateTime. | Ne                                                          |
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

#### <a name="legacy-source-model"></a>Starší zdrojový model

>[!NOTE]
>Následující zdrojový model kopírování je stále podporován tak, jak je kvůli zpětné kompatibilitě. Navrhnete použití nového modelu, který je uvedený výše, a uživatelské rozhraní pro vytváření ADF se přepnulo na generování nového modelu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **FileSystemSource** . |Ano |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z dílčích složek nebo pouze ze zadané složky. Poznámka: Pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka/podsložka se nekopíruje/nevytvoří při jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
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

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| blocích | key | zahrnout | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory tučně)|
|:--- |:--- |:--- |:--- |
| blocích | `Folder*/*` | false | blocích<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| blocích | `Folder*/*` | true | blocích<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| blocích | `Folder*/*.csv` | false | blocích<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| blocích | `Folder*/*.csv` | true | blocích<br/>&nbsp;&nbsp;&nbsp;&nbsp;složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Další informace o vlastnostech najdete v části [Odstranění aktivity](delete-activity.md) .

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, pomocí aktivity kopírování v Azure Data Factory najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md##supported-data-stores-and-formats).
