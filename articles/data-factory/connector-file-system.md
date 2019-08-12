---
title: Kopírování dat z/do systému souborů pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze systému souborů do úložiště dat jímky podporované (nebo) z podporované zdrojové úložiště dat do systému souborů pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 411f5912a2fb83b97fa02ab9b91cee14c3f6cd1b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840300"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Kopírování dat do nebo ze systému souborů pomocí služby Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-file-system-connector.md)
> * [Aktuální verze](connector-file-system.md)

Tento článek popisuje, jak kopírovat data do a ze systému souborů. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor systému souborů je podporovaný pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Konkrétně podporuje tohoto konektoru systému souborů:

- Kopírování souborů z/do místního počítače nebo síťové sdílené složky. Použití sdílené složky Linux, instalace [Samba](https://www.samba.org/) na serveru s Linuxem.
- Kopírování souborů pomocí **Windows** ověřování.
- Kopírování souborů jako-je nebo analýze a generování souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

Pro kopírování dat z/do systému souborů, který není veřejně přístupná, budete muset nastavit modul Integration Runtime. Zobrazit [modul Integration Runtime](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory, které jsou specifické pro systém souborů.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro službu propojenou systému souborů jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **FileServer**. | Ano |
| host | Určuje kořenová cesta ke složce, která chcete kopírovat. Použijte řídicí znak "\" pro zvláštní znaky v řetězci. Zobrazit [ukázka propojené služby a datové sady definice](#sample-linked-service-and-dataset-definitions) příklady. | Ano |
| userid | Zadejte ID uživatele, který má přístup k serveru. | Ano |
| password | Zadejte heslo pro uživatele (ID uživatele). Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít modul Integration Runtime nebo prostředí Azure Integration Runtime (Pokud vaše úložiště dat je veřejně dostupná). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

### <a name="sample-linked-service-and-dataset-definitions"></a>Ukázkové propojené služby a definicích datových sad

| Scénář | "hostitel" v definici propojené služby | "folderPath" v definici datové sady |
|:--- |:--- |:--- |
| Místní složky v počítači s modulem Runtime integrace: <br/><br/>Příklady: D:\\\* nebo D:\folder\subfolder\\* |Ve formátu JSON: `D:\\`<br/>Na uživatelské rozhraní: `D:\` |Ve formátu JSON: `.\\` nebo `folder\\subfolder`<br>Na uživatelské rozhraní: `.\` nebo `folder\subfolder` |
| Vzdálené sdílené složky: <br/><br/>Příklady: \\ \\myserver\\sdílet\\ \* nebo \\ \\myserver\\sdílet\\složky\\podsložky\\* |Ve formátu JSON: `\\\\myserver\\share`<br/>Na uživatelské rozhraní: `\\myserver\share` |Ve formátu JSON: `.\\` nebo `folder\\subfolder`<br/>Na uživatelské rozhraní: `.\` nebo `folder\subfolder` |

>[!NOTE]
>Při vytváření prostřednictvím uživatelského rozhraní, není nutné zadat dvojité zpětné lomítko (`\\`) k uvození stejným způsobem jako prostřednictvím formátu JSON, zadejte jedno zpětné lomítko.

**Příklad:**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

- V případě **Parquet, oddělený text a binární formát**, přečtěte si část [Parquet, text a datovou sadu binárního formátu](#format-based-dataset) s oddělovači.
- Pro jiné formáty, jako je **Formát ORC/Avro/JSON**, se podívejte na [jiný oddíl formátu DataSet](#other-format-dataset) .

### <a name="format-based-dataset"></a>Parquet, text a datová sada binárních formátů s oddělovači

Chcete-li kopírovat data do a z **Parquet, oddělený text nebo binární formát**, přečtěte si článek [Formát Parquet](format-parquet.md), [Formát textu](format-delimited-text.md) s oddělovači a [binární formát](format-binary.md) pro datovou sadu založenou na formátu a podporovaná nastavení. V části `location` nastavení v datové sadě založené na formátu jsou podporovány následující vlastnosti systému souborů:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v rámci `location` datové sady musí být nastavená na **FileServerLocation**. | Ano      |
| folderPath | Cesta ke složce Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |

> [!NOTE]
> Datová sada typu Shared s Parquet/textovým formátem, která je uvedená v dalším oddílu, je stále podporovaná tak, jak je pro aktivitu kopírování/vyhledávání nebo GetMetadata, ale nefunguje s tokem dat mapování. Budete navrženi, že budete chtít tento nový model použít a že uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování těchto nových typů.

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
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

### <a name="other-format-dataset"></a>Jiná Formátová datová sada

Chcete-li kopírovat data do systému souborů ve **formátu ORC/Avro/JSON**, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **FileShare** |Ano |
| folderPath | Cesta ke složce. Filtr zástupných znaků je podporovaný, povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá `^` žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte příkaz. <br/><br/>Příklady: RootFolder/podsložce/, viz další příklady v [ukázce propojená služba a definice datových sad](#sample-linked-service-and-dataset-definitions) a [Příklady filtru souborů a složek](#folder-and-file-filter-examples). |Ne |
| fileName | **Název nebo zástupný filtr** pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` dostala mimo vašeho skutečného názvu souboru má zástupných znaků nebo tento znak escape uvnitř.<br/><br/>Když není zadaný název souboru pro výstupní datovou sadu a v jímky aktivity není zadané **preserveHierarchy** , aktivita kopírování automaticky vygeneruje název souboru s následujícím vzorem: "*Data. [identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese, pokud je nakonfigurována]* ", např. "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; Pokud zkopírujete z tabulkové zdroje místo názvu tabulky dotazů, vzor názvů je " *[název tabulky]. [ formát]. [Pokud nakonfigurované komprese]* ", například "MyTable.csv". |Ne |
| modifiedDatetimeStart | Filtr souborů na základě atributu: Poslední změna Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě atributu: Poslední změna Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| format | Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **Gzip**, **Deflate**, **bzip2**a **ZipDeflate**.<br/>Podporované úrovně jsou: **Optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **folderPath** s složkovou část a **fileName** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s složkovou část a **fileName** s filtr zástupných znaků.

>[!NOTE]
>Pokud jste používali vlastnost "fileFilter" pro filtr souborů, je stále podporovány jako-se, když jsou navržena nová funkce filtru, které jsou přidány do "název_souboru" do budoucna.

**Příklad:**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných souborů systému zdroje a jímky.

### <a name="file-system-as-source"></a>Systém souborů jako zdroj

- Chcete-li kopírovat z **Parquet, textového a binárního formátu**, přečtěte si oddíl [Parquet, text s oddělovači textu a binární formát](#format-based-source) .
- Chcete-li kopírovat z jiných formátů, jako je **Formát ORC/Avro/JSON**, přečtěte si část [source source Format](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, text a zdroj binárního formátu s oddělovači

Chcete-li kopírovat data z **Parquet, textu nebo binárního formátu**, přečtěte si článek [Formát Parquet](format-parquet.md), [Formát textu](format-delimited-text.md) s oddělovači a [binární formát](format-binary.md) pro zdroj aktivity kopírování založené na formátu a podporovaná nastavení. Pro systém `storeSettings` souborů jsou podporovány následující vlastnosti v nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavená na **FileServerReadSetting**. | Ano                                           |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Všimněte si, že pokud rekurzivní je nastavena na hodnotu true a jímku je souborové úložiště prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné znaky `*` jsou: (odpovídá žádnému nebo více `?` znakům) a (odpovídá žádnému nebo `^` jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte k Escape. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky `*` jsou: (odpovídá žádnému nebo více `?` znakům) a (odpovídá žádnému nebo `^` jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte k Escape.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, `fileName` Pokud není v datové sadě zadáno |
| modifiedDatetimeStart    | Filtr souborů na základě atributu: Poslední změna Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

> [!NOTE]
> Pro formát textu Parquet/s oddělovači, který je uveden v další části, je zdroj aktivity kopírování typu **FileSystemSource** stále podporován tak, jak je z důvodu zpětné kompatibility. Budete navrženi, že budete chtít tento nový model použít a že uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování těchto nových typů.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
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
                    "type": "FileServerReadSetting",
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

Chcete-li kopírovat data ze systému souborů ve **formátu ORC/Avro/JSON**, v části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **FileSystemSource** |Ano |
| recursive | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Poznámka: Pokud rekurzivní je nastavena na hodnotu true a datovou sadu jímky souborové úložiště je prázdné složky/dílčí-folder nebudou zkopírovány/vytvořili na jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
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

### <a name="file-system-as-sink"></a>Systém souborů jako jímka

- Chcete-li kopírovat na **Parquet, textový nebo binární formát**, přečtěte si část [Parquet, text s oddělovači a binární formát](#format-based-sink) .
- Chcete-li kopírovat do jiných formátů, jako je **Formát ORC/Avro/JSON**, přečtěte si část s [dalšími formátovacími jímkami](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, oddělený text a jímka binárního formátu

Chcete-li kopírovat data v **Parquet, textovém nebo binárním formátu**, přečtěte si článek [Formát Parquet](format-parquet.md), [Formát textu](format-delimited-text.md) s oddělovači a [binární formát](format-binary.md) pro jímku aktivity kopírování založené na formátu a podporovaná nastavení. V nastavení v části `storeSettings` nastavení v jímky kopírování na základě formátu jsou podporovány následující vlastnosti systému souborů:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavená na **FileServerWriteSetting**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><b>-FlattenHierarchy</b>: Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě je automaticky generovaným názvem souboru. | Ne       |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

> [!NOTE]
> V případě textového formátu Parquet/Unlimited je jímka aktivity kopírování typu **FileSystemSink** uvedená v další části stále podporovaná tak, jak je kvůli zpětné kompatibilitě. Budete navrženi, že budete chtít tento nový model použít a že uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování těchto nových typů.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
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
                    "type": "FileServerWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Další jímka formátu

Chcete-li kopírovat data do systému souborů ve **formátu ORC/Avro/JSON**, v oddílu **jímky** jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na: **FileSystemSink** |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachová hierarchií souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cesta cílový soubor do cílové složky.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složky. Cílové soubory mají název automaticky generovány. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Je-li zadán název souboru, bude název sloučeného souboru uveden jako název. jinak by byl automaticky vygenerovaný název souboru. | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
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

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| folderPath | fileName | recursive | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `Folder*` | (empty, use default) | false | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (empty, use default) | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="recursive-and-copybehavior-examples"></a>rekurzivní a copyBehavior příklady

Tato část popisuje výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| recursive | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | cílové složky složku1 se vytvoří s stejnou strukturu jako zdroj:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true (pravda) |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 File2 + soubor3 + File4 + 5 souboru obsahu jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor |
| false (nepravda) |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false (nepravda) |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |
| false (nepravda) |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 se vytvoří s následující strukturou<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaného souboru. Automaticky generovaný název File1<br/><br/>Subfolder1 s soubor3 File4 a File5 se nenačítají. |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
