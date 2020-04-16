---
title: Kopírování dat ze služby Amazon Simple Storage Service (S3)
description: Zjistěte, jak kopírovat data ze služby Amazon Simple Storage Service (S3) do podporovaných úložišť dat jímky pomocí Azure Data Factory.
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
ms.openlocfilehash: 6975c876bb31e36b7b6268d284f747fd51803d63
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415457"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopírování dat ze služby Amazon Simple Storage Service pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
>
> * [Verze 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuální verze](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak kopírovat data z Amazon Simple Storage Service (Amazon S3). Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).

>[!TIP]
>Pro scénář migrace dat z Amazon S3 do Azure Storage, další informace z [použití Azure Data Factory k migraci dat z Amazon S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Amazon S3 je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně tento konektor Amazon S3 podporuje kopírování souborů jako-je nebo analýzu souborů s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md). Můžete také zvolit [zachování metadat souboru během kopírování](#preserve-metadata-during-copy). Konektor používá [AWS podpis verze 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) k ověření požadavků na S3.

>[!TIP]
>Tento konektor Amazon S3 můžete použít ke kopírování dat od **všech poskytovatelů úložišť kompatibilních s Technologií S3,** například [služby Google Cloud Storage](connector-google-cloud-storage.md). Zadejte odpovídající adresu URL služby v konfiguraci propojené služby.

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li kopírovat data z Amazon S3, ujistěte se, že vám byla udělena následující oprávnění:

- **Pro provádění kopírovací chod:**: `s3:GetObject` a `s3:GetObjectVersion` pro operace s objektem Amazon S3.
- **Pro vytváření grafického uživatelského rozhraní data :** `s3:ListAllMyBuckets` a `s3:ListBucket` / `s3:GetBucketLocation` pro Amazon S3 Bucket Operations jsou navíc vyžadována oprávnění pro operace, jako je testovací připojení a procházení nebo procházení cest souborů. Pokud nechcete udělit tato oprávnění, přeskočte testovací připojení na stránce vytvoření propojené služby a zadejte cestu přímo v nastavení datové sady.

Podrobnosti o úplném seznamu oprávnění Amazon S3 naleznete [v tématu Zadání oprávnění v zásadách](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit datové továrny specifických pro Amazon S3.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Amazon S3 jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AmazonS3**. | Ano |
| accessKeyId | ID tajného přístupového klíče. |Ano |
| tajný klíč AccessKey | Samotný tajný přístupový klíč. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano |
| adresa serviceUrl | Zadejte vlastní koncový bod S3, pokud kopírujete data od jiného poskytovatele úložiště kompatibilního s S3, než je oficiální služba Amazon S3. Chcete-li například kopírovat data z `https://storage.googleapis.com`cloudového úložiště Google, zadejte . | Ne |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime (pokud vaše úložiště dat se nachází v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

>[!TIP]
>Zadejte vlastní adresu URL služby S3, pokud kopírujete data z jiného úložiště kompatibilního s S3, než je oficiální služba Amazon S3.

>[!NOTE]
>Tento konektor vyžaduje přístupové klíče pro účet IAM ke kopírování dat z Amazon S3. [Dočasné pověření zabezpečení](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) není podporováno.
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `location` pro Amazon S3 v rámci nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type `location` under v datové sadě musí být nastavena na **AmazonS3Location**. | Ano      |
| název_bloku | Název bloku S3.                                          | Ano      |
| folderPath | Cesta ke složce pod daným kontejnerem. Chcete-li k filtrování složky použít zástupný znak, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným bucket + folderPath. Chcete-li k filtrování souborů použít zástupný znak, přeskočte toto nastavení a určete v nastavení zdroje aktivity. | Ne       |
| version | Verze S3 objektu, pokud je povolena správu verzí S3. Pokud není zadán, bude načtena nejnovější verze. |Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `storeSettings` pro Amazon S3 v rámci nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Vlastnost type `storeSettings` pod musí být nastavena na **AmazonS3ReadSettings**. | Ano                                                         |
| Rekurzivní                | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                                          |
| Předponu                   | Předpona pro klíč objektu S3 pod daným košem nakonfigurovaným v datové sadě pro filtrování zdrojových objektů. Objekty, jejichž klávesy začínají touto předponou, jsou vybrány. <br>Platí pouze `wildcardFolderPath` v `wildcardFileName` případě, že vlastnosti nejsou zadány. | Ne                                                          |
| program se zástupnými_znakem       | Cesta ke složce se zástupnými znaky pod daným segmentem nakonfigurovaná v datové sadě pro filtrování zdrojových složek. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br>Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ne                                                          |
| název souboru se zástupnými_znakové_         | Název souboru se zástupnými znaky pod daným kontejnerem + složkaCesta/zástupný znakFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř.  Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ano, `fileName` pokud v `prefix` datové sadě nejsou zadány |
| modifiedDatetimeStart    | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime. | Ne                                                          |
| modifiedDatetimeEnd      | Stejně jako výše.                                               | Ne                                                          |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                                          |

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

### <a name="folder-and-file-filter-examples"></a>Příklady filtrů složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru pomocí filtrů se zástupnými kódy.

| Kbelík | key | Rekurzivní | Struktura zdrojové složky a výsledek filtru (soubory tučně jsou načteny)|
|:--- |:--- |:--- |:--- |
| Kbelík | `Folder*/*` | false (nepravda) | Kbelík<br/>&nbsp;&nbsp;&nbsp;&nbsp;SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| Kbelík | `Folder*/*` | true | Kbelík<br/>&nbsp;&nbsp;&nbsp;&nbsp;SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| Kbelík | `Folder*/*.csv` | false (nepravda) | Kbelík<br/>&nbsp;&nbsp;&nbsp;&nbsp;SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| Kbelík | `Folder*/*.csv` | true | Kbelík<br/>&nbsp;&nbsp;&nbsp;&nbsp;SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |

## <a name="preserve-metadata-during-copy"></a>Zachovat metadata během kopírování

Když kopírujete soubory z AmazonS3 do Azure Data Lake Storage Gen2/Azure Blob, můžete zachovat metadata souboru spolu s daty. Další informace najdete v [souboru Zachovat metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zaškrtněte [políčko Odstranit aktivitu.](delete-activity.md)

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model uvedený ve výše uvedených oddílech do budoucna a vývojové uontové pole ADF přešlo na generování nového modelu.

### <a name="legacy-dataset-model"></a>Starší model datové sady

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **AmazonS3Object.** |Ano |
| název_bloku | Název bloku S3. Filtr se zástupnými symboly není podporován. |Ano pro aktivitu kopírování/vyhledávání, Ne pro aktivitu GetMetadata |
| key | **Název nebo zástupný filtr** klíče objektu S3 pod zadaným kontejnerem. Použije pouze v případě, že není zadána vlastnost "prefix". <br/><br/>Filtr se zástupnými kódy je podporován jak pro část složky, tak pro část s názvem souboru. Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak).<br/>- Příklad 1:`"key": "rootfolder/subfolder/*.csv"`<br/>- Příklad 2:`"key": "rootfolder/subfolder/???20180427.txt"`<br/>Další příklad najdete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). Použijte `^` k útěku, pokud váš skutečný název složky / souboru má zástupný znak nebo tento escape char uvnitř. |Ne |
| Předponu | Předpona pro klíč objektu S3. Objekty, jejichž klávesy začínají touto předponou, jsou vybrány. Platí pouze v případě, že není zadána vlastnost "key". |Ne |
| version | Verze S3 objektu, pokud je povolena správu verzí S3. Pokud není zadán, bude načtena nejnovější verze. |Ne |
| modifiedDatetimeStart | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| modifiedDatetimeEnd | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| formát | Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory v určitém formátu, jsou podporovány následující typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](supported-file-formats-and-compression-codecs-legacy.md#text-format), Formát [Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), Formát [orků](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a [Formát parket.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Ne (pouze pro binární scénář kopírování) |
| komprese | Určete typ a úroveň komprese dat. Další informace naleznete [v tématu Podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**.<br/>Podporované úrovně jsou: **Optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory do složky, zadejte **bucketName** pro segment a **předponu** pro část složky.<br>Chcete-li zkopírovat jeden soubor s daným názvem, zadejte **název bloku** a **klíč** pro část složky plus název souboru.<br>Chcete-li zkopírovat podmnožinu souborů do složky, zadejte **název bloku** pro segment a **klíč** pro část složky plus filtr se zástupnými kódy.

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

### <a name="legacy-copy-activity-source-model"></a>Starší model zdroje aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: FileSystemSource** |Ano |
| Rekurzivní | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Poznámka: Pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka nebudou zkopírovány nebo vytvořeny na jímce.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

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
Seznam úložišť dat, které jsou podporovány jako zdroje a propady aktivity kopírování v Azure Data Factory, najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
