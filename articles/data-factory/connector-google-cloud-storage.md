---
title: Kopírování dat z Google Cloud Storage pomocí Azure Data Factory
description: Přečtěte si, jak kopírovat data z Google Cloud Storage na podporovaná úložiště dat jímky pomocí Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: b8e518ba23c877ee80197ad94a6bc01b23b044a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588983"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>Kopírování dat z Google Cloud Storage pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak kopírovat data ze služby Google Cloud Storage (GC). Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Google Cloud Storage se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně tento konektor Google Cloud Storage podporuje kopírování souborů, jako je, nebo k analýze souborů s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md). Využívá interoperabilitu kompatibilní s GC S3.

## <a name="prerequisites"></a>Požadavky

V účtu úložiště Google Cloud se vyžaduje následující nastavení:

1. Povolení interoperability pro váš účet Google Cloud Storage
2. Nastavte výchozí projekt obsahující data, která chcete kopírovat z cílového GCho kontejneru.
3. Vytvořte účet služby a definujte správnou úroveň oprávnění pomocí cloudu IAM v GCP. 
4. Vygenerujte přístupové klíče pro tento účet služby.

![Načíst přístupový klíč pro Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Požadovaná oprávnění

Pokud chcete kopírovat data z Google Cloud Storage, ujistěte se, že vám byla udělena následující oprávnění pro operace s objekty: ` storage.objects.get` a ` storage.objects.list` .

Použijete-li k vytváření Data Factory uživatelské rozhraní, ` storage.buckets.list` vyžaduje se pro operace, jako je testování připojení k propojené službě, další oprávnění a procházení z kořenového adresáře. Pokud toto oprávnění nechcete udělit, můžete zvolit možnosti "testovat připojení k cestě k souboru" nebo "Procházet ze zadané cesty" z uživatelského rozhraní.

Úplný seznam rolí úložiště Google Cloud a přidružených oprávnění najdete v tématu [role IAM pro cloudové úložiště](https://cloud.google.com/storage/docs/access-control/iam-roles) na webu Google Cloud.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factorych entit specifických pro Google Cloud Storage.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojené služby Google Cloud Storage se podporují následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **GoogleCloudStorage**. | Yes |
| accessKeyId | ID tajného přístupového klíče Přístupovou klávesu a tajný klíč najdete v části [požadavky](#prerequisites). |Yes |
| secretAccessKey | Tajný přístupový klíč sám o sobě. Označte toto pole jako **SecureString** a bezpečně ho uložte do Data Factory nebo [odkazujte na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| serviceUrl | Zadejte vlastní koncový bod GC jako `https://storage.googleapis.com` . | Yes |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime (Pokud je vaše úložiště dat v privátní síti). Pokud tato vlastnost není zadaná, služba použije výchozí prostředí Azure Integration runtime. |No |

Tady je příklad:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro úložiště Google Cloud v části `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Povinné |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Vlastnost **Type** v rámci `location` datové sady musí být nastavená na **GoogleCloudStorageLocation**. | Yes      |
| interval intervalu | Název intervalu GC                                          | Yes      |
| folderPath | Cesta ke složce v daném kontejneru. Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a určete v nastavení zdroje aktivity. | No       |
| fileName   | Název souboru v daném intervalu a cestě ke složce. Pokud chcete k filtrování souborů použít zástupný znak, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | No       |

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které podporuje zdroj úložiště Google Cloud.

### <a name="google-cloud-storage-as-a-source-type"></a>Google Cloud Storage jako typ zdroje

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro úložiště Google Cloud v části `storeSettings` nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Povinné                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| typ                     | Vlastnost **Type** v poli `storeSettings` musí být nastavená na **GoogleCloudStorageReadSettings**. | Yes                                                         |
| ***Vyhledejte soubory ke zkopírování:*** |  |  |
| MOŽNOST 1: statická cesta<br> | Kopírovat z daného kontejneru nebo složky/cesty k souboru, které jsou zadány v datové sadě. Pokud chcete zkopírovat všechny soubory z intervalu nebo složky, zadejte také `wildcardFileName` jako `*` . |  |
| MOŽNOST 2: Předpona GC<br>-prefix | Předpona pro název klíče GC v rámci daného intervalu nakonfigurovaného v datové sadě pro filtrování zdrojových souborů GC Jsou vybrané GC klíče, jejichž názvy začínají na `bucket_in_dataset/this_prefix` . Využívá filtr na straně služby GC, který poskytuje lepší výkon než filtr zástupných znaků. | No |
| MOŽNOST 3: zástupný znak<br>- wildcardFolderPath | Cesta ke složce se zástupnými znaky v daném kontejneru nakonfigurovaném pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Použijte `^` k ukončení, jestli má název složky zástupný znak nebo tento řídicí znak uvnitř. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | No                                            |
| MOŽNOST 3: zástupný znak<br>- wildcardFileName | Název souboru se zástupnými znaky v daném intervalu a cestě ke složce (nebo cesta ke složce se zástupnými znaky) pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Použijte `^` pro Escape, jestli název souboru obsahuje zástupný znak nebo tento řídicí znak uvnitř.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Yes |
| MOŽNOST 3: seznam souborů<br>- fileListPath | Určuje, že se má zkopírovat daná sada souborů. Najeďte na textový soubor, který obsahuje seznam souborů, které chcete zkopírovat, jeden soubor na řádek, což je relativní cesta k cestě nakonfigurované v datové sadě.<br/>Při použití této možnosti nezadávejte název souboru v datové sadě. Další příklady najdete v [příkladech seznamu souborů](#file-list-examples). |No |
| ***Další nastavení:*** |  | |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je **rekurzivní** nastavení nastaveno na **hodnotu true** a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. <br>Povolené hodnoty jsou **true** (výchozí) a **false**.<br>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . |No |
| deleteFilesAfterCompletion | Uvádí, zda budou binární soubory po úspěšném přesunutí do cílového úložiště odstraněny ze zdrojového úložiště. Odstranění souboru je vázané na soubor, takže když aktivita kopírování selže, uvidíte, že některé soubory se už zkopírovaly do cílového umístění a odstranily ze zdroje, zatímco ostatní jsou pořád ve zdrojovém úložišti. <br/>Tato vlastnost je platná pouze ve scénáři kopírování binárních souborů. Výchozí hodnota: false. |No |
| modifiedDatetimeStart    | Soubory jsou filtrovány na základě atributu: Naposledy změněno. <br>Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd` . Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít **hodnotu null**, což znamená, že pro datovou sadu nebude použit filtr atributů souborů.  Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je **null**, budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je **null**, soubory, jejichž atribut Last Modified je menší než hodnota DateTime, se vybere.<br/>Tato vlastnost se při konfiguraci nepoužívá `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Platí to samé jako výše.                                               | No                                                          |
| enablePartitionDiscovery | U souborů, které jsou rozdělené na oddíly, určete, jestli se mají analyzovat oddíly z cesty k souboru, a přidejte je jako další zdrojové sloupce.<br/>Povolené hodnoty jsou **false** (výchozí) a **true**. | No                                            |
| partitionRootPath | Pokud je povoleno zjišťování oddílů, zadejte absolutní kořenovou cestu, aby bylo možné číst rozdělené složky jako sloupce dat.<br/><br/>Pokud není zadaný, ve výchozím nastavení<br/>– Pokud použijete cestu k souboru v datové sadě nebo v seznamu souborů na zdroji, je kořenová cesta oddílu cestou nakonfigurovanou v datové sadě.<br/>– Když použijete filtr složky se zástupnými znaky, kořenová cesta oddílu je dílčí cesta před prvním zástupným znakem.<br/><br/>Předpokládejme například, že nakonfigurujete cestu v datové sadě jako kořen/složka/rok = 2020/měsíc = 08/Day = 27:<br/>– Pokud zadáte kořenovou cestu oddílu jako "root/složka/Year = 2020", aktivita kopírování vygeneruje další dva sloupce `month` a `day` hodnoty "08" a "27" společně se sloupci uvnitř souborů.<br/>-Pokud není zadána kořenová cesta oddílu, nebude vygenerován žádný sloupec navíc. | No                                            |
| maxConcurrentConnections |Horní limit souběžných připojení navázaných na úložiště dat během spuštění aktivity. Zadejte hodnotu pouze v případě, že chcete omezit souběžná připojení.| No                                                          |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "GoogleCloudStorageReadSettings",
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

| blocích | key | zahrnout | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory tučně)|
|:--- |:--- |:--- |:--- |
| blocích | `Folder*/*` | false (nepravda) | blocích<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| blocích | `Folder*/*` | true | blocích<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| blocích | `Folder*/*.csv` | false (nepravda) | blocích<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| blocích | `Folder*/*.csv` | true | blocích<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Příklady seznamů souborů

Tato část popisuje výsledné chování při použití cesty seznamu souborů ve zdroji aktivity kopírování.

Předpokládejme, že máte následující strukturu zdrojové složky a chcete soubory zkopírovat tučně:

| Ukázka zdrojové struktury                                      | Obsah v FileListToCopy.txt                             | Konfigurace Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| blocích<br/>&nbsp;&nbsp;&nbsp;&nbsp;Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mezipaměť<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **V datové sadě:**<br>Blocích `bucket`<br>– Cesta ke složce: `FolderA`<br><br>**Ve zdroji aktivity kopírování:**<br>– Cesta k seznamu souborů: `bucket/Metadata/FileListToCopy.txt` <br><br>Cesta k seznamu souborů odkazuje na textový soubor ve stejném úložišti dat, který obsahuje seznam souborů, které chcete zkopírovat, jeden soubor na řádek s relativní cestou k cestě, která je nakonfigurovaná v datové sadě. |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li získat informace o vlastnostech, ověřte [aktivitu GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Chcete-li získat informace o vlastnostech, ověřte [aktivitu odstranit](delete-activity.md).

## <a name="legacy-models"></a>Starší modely

Pokud jste pomocí konektoru Amazon S3 zkopírovali data z Google Cloud Storage, je stále podporovaná, jak je kvůli zpětné kompatibilitě. Doporučujeme použít nový model uvedený výše. Uživatelské rozhraní pro vytváření Data Factory bylo přepnuto na generování nového modelu.

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat, která aktivita kopírování v Azure Data Factory podporuje jako zdroje a jímky, najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
