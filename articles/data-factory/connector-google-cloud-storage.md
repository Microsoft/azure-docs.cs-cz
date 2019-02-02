---
title: Kopírování dat z Google Cloud Storage pomocí Azure Data Factory | Dokumentace Microsoftu
description: Další informace o tom, jak pomocí služby Azure Data Factory kopírování dat z Google Cloud Storage do úložišť dat podporovaných jímky.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: jingwang
ms.openlocfilehash: 815ee569f0919f32b38b7b7cdf848be184b7aea8
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661849"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Kopírování dat z Google Cloud Storage pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z Google Cloud Storage. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírování dat Google Cloud Storage do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor Google Cloud Storage podporuje kopírování souborů jako-je nebo analýza souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Kopírování dat z Google Cloud Storage. využívá [konektor Amazon S3](connector-amazon-simple-storage-service.md) s odpovídající vlastní S3 koncový bod, jako Google Cloud Storage nabízí interoperabilitu mezi S3 kompatibilní.

## <a name="required-permissions"></a>Požadovaná oprávnění

Ke zkopírování dat z Google Cloud Storage, ujistěte se, že máte následující oprávnění:

- **Pro spuštění aktivity kopírování:**: `s3:GetObject` a `s3:GetObjectVersion` pro objekt operace.
- **Pro grafické uživatelské rozhraní Data Factory pro vytváření**: `s3:ListAllMyBuckets` a `s3:ListBucket` / `s3:GetBucketLocation` pro operace kontejneru oprávnění kromě u je vyžadováno cesty operací, jako jsou test připojení a procházení a přejděte k souborům. Pokud nechcete, aby pro udělení těchto oprávnění, přeskočit test připojení na stránce pro vytvoření propojené služby a zadejte cestu přímo v nastavení datové sady.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory, které jsou specifické pro Google Cloud Storage.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu Google Cloud Storage. propojený jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AmazonS3**. | Ano |
| accessKeyId | ID tajný přístupový klíč. Pokud chcete najít přístupový klíč a tajný kód, přejděte na **Google Cloud Storage** > **nastavení** > **Interoperability**. |Ano |
| secretAccessKey | Vlastního klíče přístupu k tajným klíčům. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| serviceUrl | Zadejte vlastní koncový bod S3 jako **`https://storage.googleapis.com`**. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

Zde naleznete příklad:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností podporovaných datovou sadu Google Cloud Storage.

Ke zkopírování dat z Google Cloud Storage, nastavte vlastnost typ datové sady na **AmazonS3Object**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na: **AmazonS3Object** |Ano |
| bucketName | Název sektoru S3. Filtr zástupných znaků není podporován. |Ano pro aktivitu kopírování a vyhledávání, ne pro aktivitě GetMetadata |
| key | **Filtrů názvu nebo zástupný znak** S3 klíči objektu určený interval. Platí, pouze když, není-li zadána vlastnost "předponu". <br/><br/>Filtr zástupných znaků je podporován pro složkovou část i část názvu souboru. Povolené zástupné znaky jsou: `*` (odpovídá nula nebo více znaků) a `?` (porovnává nulu nebo jeden znak).<br/>– Příklad 1: `"key": "rootfolder/subfolder/*.csv"`<br/>– Příklad 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Další příklady naleznete v [složky a souboru filtrů příklady](#folder-and-file-filter-examples). Použití `^` řídicí, pokud má vaše skutečná složka či soubor název zástupných znaků nebo tento znak escape uvnitř. |Ne |
| Předpona | Předpona klíče objektu S3. Objekty, jejichž klíče začínat touto předponou vybráno. Platí pouze v případě, že není zadána vlastnost "klíče". |Ne |
| version | Verze objektu S3, pokud je povolená Správa verzí S3. |Ne |
| modifiedDatetimeStart | Filtr souborů na základě atributu: Poslední změny. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě atributu: Poslední změny. Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| formát | Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat a generovat soubory s konkrétním formátu, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **bucketName** sady a **předponu** pro složkovou část.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **bucketName** sady a **klíč** část plus souboru názvu složky.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **bucketName** sady a **klíč** pro část plus zástupný filtr složek.

**Příklad: použití předpony**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage jako zdroj

Ke zkopírování dat z Google Cloud Storage, nastavte typ zdroje v aktivitě kopírování do **FileSystemSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na: **FileSystemSource** |Ano |
| rekurzivní | Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. Poznámka: Pokud rekurzivní je nastavena na hodnotu true a datovou sadu jímky souborové úložiště je prázdné složky/dílčí-folder nebudou zkopírovány/vytvořili na jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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

| plechovka | key | rekurzivní | Zdrojové složky struktury a filtrování výsledků (soubory tučným písmem jsou načteny)|
|:--- |:--- |:--- |:--- |
| plechovka | `Folder*/*` | false (nepravda) | plechovka<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| plechovka | `Folder*/*` | true (pravda) | plechovka<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| plechovka | `Folder*/*.csv` | false (nepravda) | plechovka<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| plechovka | `Folder*/*.csv` | true (pravda) | plechovka<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných aktivitou kopírování ve službě Azure Data Factory jako zdroje a jímky najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
