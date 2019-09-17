---
title: Kopírování dat z HDFS pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data z cloudu nebo místního zdroje HDFS do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: d8773b330349356410589cf66e8f50636d92b601
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009173"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopírování dat z HDFS pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-hdfs-connector.md)
> * [Aktuální verze](connector-hdfs.md)

Tento článek popisuje, jak kopírovat data ze HDFS serveru. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor HDFS se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou zdrojovou maticí](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Konkrétně tento konektor HDFS podporuje:

- Kopírování souborů pomocí **Windows** (Kerberos) nebo **anonymního** ověřování.
- Kopírování souborů pomocí protokolu **webhdfs** nebo **integrované podpory DistCp** .
- Kopírování souborů jako-je nebo analýze a generování souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Ujistěte se, že Integration Runtime mít přístup ke **všem** [názvový server Node]: [název uzlu port] a [servery datových uzlů]: [port datového uzlu] clusteru Hadoop. Výchozí [název uzlu port] je 50070 a výchozí [port datového uzlu] je 50075.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro HDFS.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu HDFS jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na: **HDFS**. | Ano |
| url |Adresa URL k HDFS |Ano |
| authenticationType | Povolené hodnoty jsou: **Anonymní**nebo **Windows**. <br><br> Pokud chcete pro konektor HDFS použít **ověřování pomocí protokolu Kerberos** , v [této části](#use-kerberos-authentication-for-hdfs-connector) si odpovídajícím způsobem nastavte místní prostředí. |Ano |
| userName |Uživatelské jméno pro ověřování systému Windows. Pro ověřování protokolem Kerberos `<username>@<domain>.com`zadejte. |Ano (pro ověřování systému Windows) |
| password |Heslo pro ověřování systému Windows. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano (pro ověřování systému Windows) |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

**Příklad: použití anonymního ověřování**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: použití ověřování systému Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

- Pro **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si část [Parquet, oddělený text, JSON, Avro a binární formát binárního formátu](#format-based-dataset) .
- Pro jiné formáty, jako je **Formát ORC**, se podívejte na [jiný oddíl formátu DataSet](#other-format-dataset) .

### <a name="format-based-dataset"></a>Datová sada Parquet, oddělený text, JSON, Avro a binární formát

Chcete-li kopírovat data z **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si článek [Parquet Format](format-parquet.md), formated [Text Format](format-delimited-text.md), [Avro Format](format-avro.md) a [Binary Format](format-binary.md) pro datovou sadu založenou na formátu a podporovaná nastavení. Následující vlastnosti jsou podporovány pro HDFS v `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v rámci `location` datové sady musí být nastavená na **HdfsLocation**. | Ano      |
| folderPath | Cesta ke složce Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |

> [!NOTE]
> Datová sada typu **Shared** s Parquet/textovým formátem, která je uvedená v dalším oddílu, je stále podporovaná tak, jak je pro aktivitu kopírování a vyhledávání pro zpětnou kompatibilitu. Budete navrženi, že budete chtít tento nový model použít a že uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování těchto nových typů.

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

Pro kopírování dat ze HDFS ve **formátu ORC**jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **FileShare** |Ano |
| folderPath | Cesta ke složce. Filtr zástupných znaků je podporován, povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá `^` žádnému nebo jednomu znaku); Pokud skutečný název souboru obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte příkaz. <br/><br/>Příklady: RootFolder/podsložce/, další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ano |
| fileName |  **Název nebo zástupný filtr** pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Pokud `^` vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte k ukončení. |Ne |
| modifiedDatetimeStart | Filtr souborů na základě atributu: Poslední změna Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě atributu: Poslední změna Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná.| Ne |
| format | Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **Gzip**, **Deflate**, **bzip2**a **ZipDeflate**.<br/>Podporované úrovně jsou: **Optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **folderPath** s složkovou část a **fileName** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s složkovou část a **fileName** s filtr zástupných znaků.

**Příklad:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem HDFS.

### <a name="hdfs-as-source"></a>HDFS jako zdroj

- Chcete-li kopírovat z **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si část [zdrojové části Parquet, oddělovače text, JSON, Avro a Binary Format](#format-based-source) .
- Chcete-li kopírovat z jiných formátů, jako je například **Formát ORC**, přečtěte si část [Další zdrojový formát](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, oddělený text, JSON, Avro a binární formát formátu

Chcete-li kopírovat data z **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si článek [Parquet](format-parquet.md), formát [textu s oddělovači](format-delimited-text.md), formát [Avro](format-avro.md) a [binární formát](format-binary.md) pro zdroj aktivity kopírování na základě formátu a podporovaný. možnost. Následující vlastnosti jsou podporovány pro HDFS v `storeSettings` nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavená na **HdfsReadSetting**. | Ano                                           |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Všimněte si, že pokud rekurzivní je nastavena na hodnotu true a jímku je souborové úložiště prázdnou složku nebo podsložku není zkopírován či vytvořili na jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné znaky `*` jsou: (odpovídá žádnému nebo více `?` znakům) a (odpovídá žádnému nebo `^` jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte k Escape. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky `*` jsou: (odpovídá žádnému nebo více `?` znakům) a (odpovídá žádnému nebo `^` jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte k Escape.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, `fileName` Pokud není v datové sadě zadáno |
| modifiedDatetimeStart    | Filtr souborů na základě atributu: Poslední změna Soubory bude vybrána, pokud jejich poslední úpravy jsou v rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časovém pásmu UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti může mít hodnotu NULL, což znamená, že žádný soubor filtr atributu se použijí k datové sadě.  Když `modifiedDatetimeStart` má hodnotu data a času, ale `modifiedDatetimeEnd` má hodnotu NULL, to znamená, že soubory, jejichž poslední změny atributů je větší než nebo rovná s hodnotou data a času bude vybrána.  Když `modifiedDatetimeEnd` má hodnotu data a času, ale `modifiedDatetimeStart` má hodnotu NULL, to znamená, že soubory, jejichž poslední upravené atribut je menší než hodnota data a času bude vybraná. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| distcpSettings | Skupina vlastností při použití HDFS DistCp | Ne |
| resourceManagerEndpoint | Správce prostředků koncový bod příze | Ano, pokud se používá DistCp |
| tempScriptPath | Cesta ke složce, která se používá k uložení DistCp skriptu pro dočasný příkaz. Soubor skriptu je vygenerován nástrojem Data Factory a po dokončení úlohy kopírování bude odebrán. | Ano, pokud se používá DistCp |
| distcpOptions | Další možnosti, které jsou k dispozici pro příkaz DistCp | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

> [!NOTE]
> Pro formát textu Parquet/s oddělovači, který je uveden v další části, je zdroj aktivity kopírování typu **FileSystemSource** stále podporován tak, jak je z důvodu zpětné kompatibility. Budete navrženi, že budete chtít tento nový model použít a že uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování těchto nových typů.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSetting",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
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

Chcete-li kopírovat data ze HDFS ve **formátu ORC**, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **HdfsSource** |Ano |
| recursive | Určuje, jestli se data ze složek sub nebo pouze z určené složky Číst rekurzivně. Poznámka: Pokud rekurzivní je nastavena na hodnotu true a datovou sadu jímky souborové úložiště je prázdné složky/dílčí-folder nebudou zkopírovány/vytvořili na jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| distcpSettings | Skupina vlastností při použití HDFS DistCp | Ne |
| resourceManagerEndpoint | Správce prostředků koncový bod příze | Ano, pokud se používá DistCp |
| tempScriptPath | Cesta ke složce, která se používá k uložení DistCp skriptu pro dočasný příkaz. Soubor skriptu je vygenerován nástrojem Data Factory a po dokončení úlohy kopírování bude odebrán. | Ano, pokud se používá DistCp |
| distcpOptions | Další možnosti, které jsou k dispozici pro příkaz DistCp | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad: Zdroj HDFS v aktivitě kopírování pomocí DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Další informace o tom, jak používat DistCp ke kopírování dat z HDFS efektivně, najdete v další části.

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| folderPath | fileName             | recursive | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** ) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (empty, use default) | false     | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | (empty, use default) | true      | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | false     | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | true      | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Kopírování dat ze HDFS pomocí DistCp

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) je nativní nástroj příkazového řádku Hadoop pro distribuované kopírování v clusteru Hadoop. Když se spustí příkaz Distcp, nejdřív se vypíše všechny soubory, které se mají zkopírovat, vytvoří se několik mapových úloh do clusteru Hadoop a Každá úloha mapování provede binární kopírování ze zdroje do jímky.

Podpora kopírování aktivit pomocí DistCp ke kopírování souborů do objektu blob Azure (včetně [dvoufázové kopie](copy-activity-performance.md)) nebo Azure Data Lake Store. v takovém případě může plně využít výkon vašeho clusteru, ale nemusíte ho spouštět na samoobslužném Integration runtime. Poskytne lepší propustnost při kopírování, zejména pokud je cluster velmi výkonný. V závislosti na vaší konfiguraci v Azure Data Factory se aktivita kopírování automaticky vytvoří příkaz Distcp, odešle se do vašeho clusteru Hadoop a monitoruje stav kopírování.

### <a name="prerequisites"></a>Požadavky

Pokud chcete použít DistCp ke kopírování souborů z HDFS do Azure Blob (včetně připravené kopie) nebo Azure Data Lake Store, ujistěte se, že váš cluster Hadoop splňuje tyto požadavky:

1. MapReduce a přízové služby jsou povoleny.
2. Verze příze je 2,5 nebo vyšší.
3. HDFS Server je integrovaný s vaším cílovým úložištěm dat – Azure Blob nebo Azure Data Lake Store:

    - Systém souborů BLOB v Azure je nativně podporovaný od Hadoop 2,7. Stačí zadat cestu jar v konfiguraci Hadoop env.
    - Systém souborů Azure Data Lake Store je zabalený počínaje systémem Hadoop 3.0.0-Alpha1. Pokud je cluster Hadoop nižší než tato verze, budete z něj muset ručně naimportovat ADLS balíčky jar (Azure-datalake-Store. jar) do clusteru [](https://hadoop.apache.org/releases.html)a zadat cestu jar v konfiguraci Hadoop env.

4. Připravte dočasnou složku v HDFS. Tato dočasná složka se používá k uložení skriptu prostředí DistCp, takže bude zabírat místo na úrovni KB.
5. Ujistěte se, že uživatelský účet, který je součástí HDFS, má oprávnění k odeslání aplikace v přízi; b) mít oprávnění k vytváření podsložek, čtení a zápisu souborů v rámci složky Temp.

### <a name="configurations"></a>Konfigurace

Viz konfigurace a příklady související s DistCp v [HDFS jako zdrojový](#hdfs-as-source) oddíl.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Použití ověřování protokolem Kerberos pro konektor HDFS

Existují dvě možnosti nastavení místního prostředí, aby se používalo ověřování pomocí protokolu Kerberos v konektoru HDFS. Můžete si vybrat, který lépe vyhovuje vašemu případu.
* Možnost 1: [Připojit se k místním počítačům Integration Runtime počítače ve sféře Kerberos](#kerberos-join-realm)
* Možnost 2: [Povolit vzájemný vztah důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Možnost 1: Připojit se k místním počítačům Integration Runtime počítače ve sféře Kerberos

#### <a name="requirements"></a>Požadavky

* Počítač s místním prostředím Integration Runtime se musí připojit k sféře protokolu Kerberos a nemůže se připojit k žádné doméně systému Windows.

#### <a name="how-to-configure"></a>Jak nakonfigurovat

**V počítači Integration Runtime v místním prostředí:**

1.  Spusťte nástroj **Ksetup** a nakonfigurujte server a sféru protokolu Kerberos.

    Počítač musí být nakonfigurován jako člen pracovní skupiny, protože se liší sféra protokolu Kerberos od domény systému Windows. Toho je možné dosáhnout nastavením sféry protokolu Kerberos a přidáním serveru KDC následujícím způsobem. V případě potřeby nahraďte *REALM.com* vlastním odpovídajícím sférou.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    Po provedení těchto dvou příkazů **restartujte** počítač.

2.  Ověřte konfiguraci pomocí příkazu **Ksetup** . Výstup by měl vypadat takto:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**V Azure Data Factory:**

* Nakonfigurujte konektor HDFS pomocí **ověřování systému Windows** společně s vaším hlavním jménem a heslem protokolu Kerberos pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby HDFS](#linked-service-properties) .

### <a name="kerberos-mutual-trust"></a>Možnost 2: Povolit vzájemný vztah důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos

#### <a name="requirements"></a>Požadavky

*   Počítač Integration Runtime v místním prostředí se musí připojit k doméně systému Windows.
*   K aktualizaci nastavení řadiče domény potřebujete oprávnění.

#### <a name="how-to-configure"></a>Jak nakonfigurovat

> [!NOTE]
> V následujícím kurzu nahraďte REALM.COM a AD.COM podle potřeby vlastní odpovídající sférou a řadičem domény.

**Na serveru KDC:**

1. Upravte konfiguraci služby KDC v souboru **krb5. conf** , aby se doména Windows vztahu důvěryhodnosti služby KDC odkazovala na následující šablonu konfigurace. Ve výchozím nastavení se konfigurace nachází na adrese **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   Po dokončení konfigurace **restartujte** službu KDC.

2. Připravte objekt zabezpečení s názvem **KRBTGT/REALM.\@com AD.com** na serveru KDC pomocí následujícího příkazu:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. V **hadoop.security.auth_to_local**  konfigurace služby HDFS přidejte `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Na řadiči domény:**

1.  Chcete-li přidat položku sféry, spusťte následující příkazy **Ksetup** :

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Vytvořte vztah důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos. [heslo] je heslo pro objekt zabezpečení **KRBTGT/REALM.\@com AD.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Vyberte šifrovací algoritmus používaný v protokolu Kerberos.

    1. Přejít na Správce serveru > Zásady skupiny Správa > doméně > Zásady skupiny objekty > výchozí nebo aktivní zásady domény a upravit.

    2. V místním okně **Editor pro správu zásad skupiny** přejít na konfigurace počítače > zásady > Nastavení systému Windows > nastavení zabezpečení > místní zásady > Možnosti zabezpečení a konfigurace **zabezpečení sítě: Konfigurace typů šifrování povolených pro**protokol Kerberos.

    3. Vyberte šifrovací algoritmus, který chcete použít pro připojení ke službě KDC. Obvykle můžete jednoduše vybrat všechny možnosti.

        ![Typy šifrování konfigurace pro Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Pomocí příkazu **Ksetup** určete šifrovací algoritmus, který se má použít pro konkrétní sféru.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Vytvořte mapování mezi účtem domény a hlavním objektem Kerberos, aby bylo možné v doméně systému Windows použít zabezpečení Kerberos.

    1. Spusťte nástroje pro správu > **Uživatelé a počítače služby Active Directory**.

    2. Nakonfigurujete pokročilé funkce kliknutím na **Zobrazit** > **Pokročilé funkce**.

    3. Vyhledejte účet, pro který chcete vytvořit mapování, a kliknutím pravým tlačítkem zobrazte **mapování názvů** > klikněte na kartu **názvy protokolu Kerberos** .

    4. Přidejte objekt zabezpečení ze sféry.

        ![Mapovat identitu zabezpečení](media/connector-hdfs/map-security-identity.png)

**V počítači Integration Runtime v místním prostředí:**

* Spuštěním následujících příkazů **Ksetup** přidejte položku sféry.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**V Azure Data Factory:**

* Nakonfigurujte konektor HDFS pomocí **ověřování systému Windows** společně s účtem domény nebo objektem Kerberos pro připojení ke zdroji dat HDFS. Podrobnosti o konfiguraci najdete v části [Vlastnosti propojené služby HDFS](#linked-service-properties) .

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
