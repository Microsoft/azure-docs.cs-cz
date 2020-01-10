---
title: Kopírování dat z/do Azure File Storage
description: Zjistěte, jak kopírovat data z Azure File Storage pro úložiště dat jímky podporované (nebo) z podporované zdrojové úložiště dat do služby Azure File Storage pomocí Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/10/2019
ms.openlocfilehash: 5f45ce8e48c294fa81f3e02cfdee63088dc4949a
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830084"
---
# <a name="copy-data-from-or-to-azure-file-storage-by-using-azure-data-factory"></a>Kopírování dat z nebo do služby Azure File Storage pomocí Azure Data Factory

Tento článek popisuje, jak kopírovat data z a do Azure File Storage. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure File Storage se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně tento konektor Azure File Storage podporuje kopírování souborů jako-je nebo analýze a generování souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní do služby Azure File Storage.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro soubor propojenou službu Azure Storage jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **AzureFileStorage**. | Ano |
| hostitel | Určuje koncový bod Azure File Storage jako: <br/>-Using UI: zadejte `\\<storage name>.file.core.windows.net\<file service name>`<br/>-Using JSON: `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"`. | Ano |
| ID uživatele | Zadejte uživatele pro přístup do Azure File Storage jako: <br/>-Using UI: zadejte `AZURE\<storage name>`<br/>-Using JSON: `"userid": "AZURE\\<storage name>"`. | Ano |
| heslo | Zadejte přístupový klíč úložiště. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne pro zdroj, Ano pro jímku |

>[!IMPORTANT]
> - Můžete kopírovat data do Azure File Storage pomocí prostředí Azure Integration Runtime explicitně [vytvořit prostředí Azure IR](create-azure-integration-runtime.md#create-azure-ir) umístěním File Storage a jeho přidružení v propojené službě jako v následujícím příkladu.
> - Pro kopírování dat z/do Azure File Storage pomocí modul Integration Runtime mimo Azure, nezapomeňte ve vaší místní síti otevřít odchozí port TCP 445.

>[!TIP]
>Při použití uživatelského rozhraní ADF pro vytváření obsahu můžete pro vytváření propojených služeb najít konkrétní položku "Azure File Storage", která pod vygeneruje typ `FileServer` objekt.

**Příklad:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "host": "\\\\<storage name>.file.core.windows.net\\<file service name>",
            "userid": "AZURE\\<storage name>",
            "password": {
                "type": "SecureString",
                "value": "<storage access key>"
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

Následující vlastnosti jsou podporovány pro Azure File Storage v části nastavení `location` v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v rámci objektu DataSet `location` musí být nastavena na hodnotu **FileServerLocation**. | Ano      |
| folderPath | Cesta ke složce Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
                "folderPath": "root/folder/subfolder"
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastnosti podporované službou Azure File Storage zdroje a jímky.

### <a name="azure-file-storage-as-source"></a>Azure File Storage jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Azure File Storage v části nastavení `storeSettings` ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **FileServerReadSettings**. | Ano                                           |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Všimněte si, že pokud rekurzivní je nastavena na hodnotu true a jímku je souborové úložiště prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, pokud v sadě dat není zadaný `fileName` |
| modifiedDatetimeStart    | Soubory filtru na základě atributu: naposledy upraveno. Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souborů.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
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
                    "type": "FileServerReadSettings",
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

### <a name="azure-file-storage-as-sink"></a>Azure File Storage jako jímka

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Azure File Storage v části nastavení `storeSettings` v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **FileServerWriteSettings**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě je automaticky generovaným názvem souboru. | Ne       |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| folderPath | fileName | recursive | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `Folder*` | (empty, use default) | false | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (empty, use default) | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="recursive-and-copybehavior-examples"></a>rekurzivní a copyBehavior příklady

Tato část popisuje výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| recursive | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | cílové složky složku1 se vytvoří s stejnou strukturu jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File5 |
| true |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor1 + soubor2 + file3 + file4 + obsah souboru 5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;obsah soubor1 + soubor2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky generovaným názvem File1<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |

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
| type | Vlastnost typ datové sady, musí být nastavena na: **sdílenou složku.** |Ano |
| folderPath | Cesta ke složce. <br/><br/>Filtr zástupných znaků je podporován, povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br/><br/>Příklady: RootFolder/podsložce/, další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ano |
| fileName | **Název nebo zástupný filtr** pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` dostala mimo vašeho skutečného názvu souboru má zástupných znaků nebo tento znak escape uvnitř.<br/><br/>Když není zadaný název souboru pro výstupní datovou sadu a v jímky aktivity není zadaný **preserveHierarchy** , aktivita kopírování automaticky vygeneruje název souboru s následujícím vzorem: "*data. [ identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese, je-li nakonfigurována]* ", například" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Pokud kopírujete z tabulkového zdroje pomocí názvu tabulky místo dotazu, vzor názvu je " *[název tabulky]. [ formát]. [komprese, je-li nakonfigurována]* ", například" myTable. csv ". |Ne |
| modifiedDatetimeStart | Soubory filtru na základě atributu: naposledy upraveno. Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| modifiedDatetimeEnd | Soubory filtru na základě atributu: naposledy upraveno. Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| formát | Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat a generovat soubory s konkrétním formátu, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs-legacy.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimal** a **nejrychlejší**. |Ne |

>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **folderPath** s složkovou část a **fileName** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s složkovou část a **fileName** s filtr zástupných znaků.

>[!NOTE]
>Pokud jste používali vlastnost "fileFilter" pro filtr souborů, je stále podporovány jako-se, když jsou navržena nová funkce filtru, které jsou přidány do "název_souboru" do budoucna.

**Příklad:**

```json
{
    "name": "AzureFileStorageDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

### <a name="legacy-copy-activity-source-model"></a>Starší zdrojový model aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **FileSystemSource** |Ano |
| recursive | Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. Poznámka: Pokud rekurzivní je nastavena na hodnotu true a datovou sadu jímky souborové úložiště je prázdné složky/dílčí-folder nebudou zkopírovány/vytvořili na jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure File Storage input dataset name>",
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

### <a name="legacy-copy-activity-sink-model"></a>Starší model jímky aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování: **FileSystemSink** |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složky. Cílové soubory mají automaticky vygenerovaný název. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Je-li zadán název souboru, bude název sloučeného souboru uveden jako název. jinak by byl automaticky vygenerován název souboru. | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure File Storage output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
