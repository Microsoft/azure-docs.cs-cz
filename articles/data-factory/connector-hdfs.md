---
title: Kopírování dat z HDFS pomocí Azure Data Factory
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
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 49285ac4a15cb52f04f6d629d80449eb515c33fa
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680871"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopírování dat z HDFS pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-hdfs-connector.md)
> * [Aktuální verze](connector-hdfs.md)

Tento článek popisuje, jak kopírovat data ze HDFS serveru. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor HDFS se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Konkrétně tento konektor HDFS podporuje:

- Kopírování souborů pomocí **Windows** (Kerberos) nebo **anonymního** ověřování.
- Kopírování souborů pomocí protokolu **webhdfs** nebo **integrované podpory DistCp** .
- Soubory se kopírují jako soubory nebo se analyzují nebo generují pomocí [podporovaných formátů souborů a kompresních kodeků](supported-file-formats-and-compression-codecs.md).

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
| authenticationType | Povolené hodnoty jsou: **anonymní**nebo **Windows**. <br><br> Pokud chcete pro konektor HDFS použít **ověřování pomocí protokolu Kerberos** , v [této části](#use-kerberos-authentication-for-hdfs-connector) si odpovídajícím způsobem nastavte místní prostředí. |Ano |
| Jmen |Uživatelské jméno pro ověřování systému Windows. Pro ověřování protokolem Kerberos zadejte `<username>@<domain>.com`. |Ano (pro ověřování systému Windows) |
| heslo |Heslo pro ověřování systému Windows. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Ano (pro ověřování systému Windows) |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadaný, použije se výchozí Azure Integration Runtime. |Ne |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

V rámci `location` nastavení v datové sadě založené na formátu jsou podporovány následující vlastnosti HDFS:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v rámci objektu DataSet `location` musí být nastavena na hodnotu **HdfsLocation**. | Ano      |
| folderPath | Cesta ke složce Pokud chcete použít zástupný znak k filtrování složky, toto nastavení nechejte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |

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

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

>[!NOTE]
>Následující model DataSet je stále podporován tak, jak je kvůli zpětné kompatibilitě. Navrhnete použití nového modelu uvedeného výše v oddílu dál a uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování nového modelu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na: **Shared** . |Ano |
| folderPath | Cesta ke složce Filtr zástupných znaků je podporován, povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud skutečný název souboru obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br/><br/>Příklady: RootFolder/podsložce/, další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ano |
| fileName |  **Název nebo zástupný filtr** pro soubory v rámci zadaného "FolderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada bude ukazovat na všechny soubory ve složce. <br/><br/>V případě filtru jsou povoleny zástupné znaky: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>-Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. |Ne |
| modifiedDatetimeStart | Filtr souborů na základě atributu: Naposledy změněno Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Pokud `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je NULL, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je NULL, znamená to, že jsou soubory, jejichž naposledy upravený atribut je menší než hodnota DateTime, bude vybrána.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě atributu: Naposledy změněno Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Počítejte s tím, že bude ovlivněn celkový výkon přesunu dat tím, že toto nastavení povolíte, pokud chcete provádět filtr souborů z obrovských objemů souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Pokud `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je NULL, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je NULL, znamená to, že jsou soubory, jejichž naposledy upravený atribut je menší než hodnota DateTime, bude vybrána.| Ne |
| formátovat | Pokud chcete **Kopírovat soubory** mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady.<br/><br/>Chcete-li analyzovat soubory s konkrétním formátem, jsou podporovány následující typy formátu souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. V části formát nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v částech [Formát textu](supported-file-formats-and-compression-codecs.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formát ORC](supported-file-formats-and-compression-codecs.md#orc-format)a formátování [Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Ne (jenom pro binární scénář kopírování) |
| komprese | Zadejte typ a úroveň komprese dat. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **gzip**, **Deflate**, **bzip2**a **ZipDeflate**.<br/>Podporované úrovně: **optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory ve složce, zadejte pouze **FolderPath** .<br>Chcete-li zkopírovat jeden soubor se zadaným názvem, zadejte **FolderPath** s částí **složky a názvem souboru s** názvem.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **FolderPath** s částí složky a **názvem souboru** s filtrem zástupných znaků.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem HDFS.

### <a name="hdfs-as-source"></a>HDFS jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro HDFS v části nastavení `storeSettings` ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **HdfsReadSetting**. | Ano                                           |
| zahrnout                | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou: `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku); Pokud vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v rámci, použijte `^`.  Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, pokud v sadě dat není zadaný `fileName` |
| modifiedDatetimeStart    | Filtr souborů na základě atributu: Naposledy změněno Soubory budou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu nebude použit filtr atributů souboru.  Pokud `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je NULL, znamená to, že budou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime.  Pokud `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je NULL, znamená to, že jsou soubory, jejichž naposledy upravený atribut je menší než hodnota DateTime, bude vybrána. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| distcpSettings | Skupina vlastností při použití HDFS DistCp | Ne |
| resourceManagerEndpoint | Správce prostředků koncový bod příze | Ano, pokud se používá DistCp |
| tempScriptPath | Cesta ke složce, která se používá k uložení DistCp skriptu pro dočasný příkaz. Soubor skriptu je vygenerován nástrojem Data Factory a po dokončení úlohy kopírování bude odebrán. | Ano, pokud se používá DistCp |
| distcpOptions | Další možnosti, které jsou k dispozici pro příkaz DistCp | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

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

#### <a name="legacy-source-model"></a>Starší zdrojový model

>[!NOTE]
>Následující zdrojový model kopírování je stále podporován tak, jak je kvůli zpětné kompatibilitě. Navrhnete použití nového modelu, který je uvedený výše, a uživatelské rozhraní pro vytváření ADF se přepnulo na generování nového modelu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **HdfsSource** . |Ano |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z dílčích složek nebo pouze ze zadané složky. Poznámka: Pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka/podsložka se nekopíruje/nevytvoří při jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| distcpSettings | Skupina vlastností při použití HDFS DistCp | Ne |
| resourceManagerEndpoint | Správce prostředků koncový bod příze | Ano, pokud se používá DistCp |
| tempScriptPath | Cesta ke složce, která se používá k uložení DistCp skriptu pro dočasný příkaz. Soubor skriptu je vygenerován nástrojem Data Factory a po dokončení úlohy kopírování bude odebrán. | Ano, pokud se používá DistCp |
| distcpOptions | Další možnosti, které jsou k dispozici pro příkaz DistCp | Ne |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad: HDFS zdroj v aktivitě kopírování pomocí DistCp**

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

| folderPath | fileName             | zahrnout | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** ) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (prázdné, použít výchozí) | false (nepravda)     | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | (prázdné, použít výchozí) | true (pravda)      | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | false (nepravda)     | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | true (pravda)      | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Kopírování dat ze HDFS pomocí DistCp

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) je nativní nástroj příkazového řádku Hadoop pro distribuované kopírování v clusteru Hadoop. Když se spustí příkaz Distcp, nejdřív se vypíše všechny soubory, které se mají zkopírovat, vytvoří se několik mapových úloh do clusteru Hadoop a Každá úloha mapování provede binární kopírování ze zdroje do jímky.

Podpora kopírování aktivit pomocí DistCp ke kopírování souborů do objektu blob Azure (včetně [dvoufázové kopie](copy-activity-performance.md)) nebo Azure Data Lake Store. v takovém případě může plně využít výkon vašeho clusteru, ale nemusíte ho spouštět na samoobslužném Integration runtime. Poskytne lepší propustnost při kopírování, zejména pokud je cluster velmi výkonný. V závislosti na vaší konfiguraci v Azure Data Factory se aktivita kopírování automaticky vytvoří příkaz Distcp, odešle se do vašeho clusteru Hadoop a monitoruje stav kopírování.

### <a name="prerequisites"></a>Požadavky

Pokud chcete použít DistCp ke kopírování souborů z HDFS do Azure Blob (včetně připravené kopie) nebo Azure Data Lake Store, ujistěte se, že váš cluster Hadoop splňuje tyto požadavky:

1. MapReduce a přízové služby jsou povoleny.
2. Verze příze je 2,5 nebo vyšší.
3. HDFS Server je integrovaný s vaším cílovým úložištěm dat – Azure Blob nebo Azure Data Lake Store:

    - Systém souborů BLOB v Azure je nativně podporovaný od Hadoop 2,7. Stačí zadat cestu jar v konfiguraci Hadoop env.
    - Systém souborů Azure Data Lake Store je zabalený počínaje systémem Hadoop 3.0.0-Alpha1. Pokud je cluster Hadoop nižší než tato [verze, budete z něj](https://hadoop.apache.org/releases.html)muset ručně naimportovat adls balíčky jar (Azure-datalake-Store. jar) do clusteru a zadat cestu jar v konfiguraci Hadoop env.

4. Připravte dočasnou složku v HDFS. Tato dočasná složka se používá k uložení skriptu prostředí DistCp, takže bude zabírat místo na úrovni KB.
5. Ujistěte se, že uživatelský účet, který je součástí HDFS, má oprávnění k odeslání aplikace v přízi; b) mít oprávnění k vytváření podsložek, čtení a zápisu souborů v rámci složky Temp.

### <a name="configurations"></a>Konfigurace

Viz konfigurace a příklady související s DistCp v [HDFS jako zdrojový](#hdfs-as-source) oddíl.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Použití ověřování protokolem Kerberos pro konektor HDFS

Existují dvě možnosti nastavení místního prostředí, aby se používalo ověřování pomocí protokolu Kerberos v konektoru HDFS. Můžete si vybrat, který lépe vyhovuje vašemu případu.
* Možnost 1: [připojení počítače s místním hostováním Integration runtime ve sféře protokolu Kerberos](#kerberos-join-realm)
* Možnost 2: [Povolení vzájemné důvěry mezi doménou systému Windows a sférou protokolu Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Možnost 1: připojení počítače s místním hostováním Integration Runtime ve sféře protokolu Kerberos

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

### <a name="kerberos-mutual-trust"></a>Možnost 2: povolení vzájemné důvěry mezi doménou systému Windows a sférou protokolu Kerberos

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

2. Připravte objekt zabezpečení s názvem **KRBTGT/REALM. COM\@AD.com** na serveru KDC pomocí následujícího příkazu:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. V konfiguračním souboru služby **Hadoop. Security. auth_to_local** HDFS přidejte `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Na řadiči domény:**

1.  Chcete-li přidat položku sféry, spusťte následující příkazy **Ksetup** :

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Vytvořte vztah důvěryhodnosti mezi doménou systému Windows a sférou protokolu Kerberos. [heslo] je heslo pro objekt zabezpečení **KRBTGT/REALM. COM\@AD.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Vyberte šifrovací algoritmus používaný v protokolu Kerberos.

    1. Přejít na Správce serveru > Zásady skupiny Správa > doméně > Zásady skupiny objekty > výchozí nebo aktivní zásady domény a upravit.

    2. V místním okně **Editor pro správu zásad skupiny** přejít na konfigurace počítače > zásady > nastavení systému Windows > nastavení zabezpečení > místní zásady > možnosti zabezpečení a konfigurace **zabezpečení sítě: Konfigurace typů šifrování povoluje se pro Kerberos**.

    3. Vyberte šifrovací algoritmus, který chcete použít pro připojení ke službě KDC. Obvykle můžete jednoduše vybrat všechny možnosti.

        ![Typy šifrování konfigurace pro Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Pomocí příkazu **Ksetup** určete šifrovací algoritmus, který se má použít pro konkrétní sféru.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Vytvořte mapování mezi účtem domény a hlavním objektem Kerberos, aby bylo možné v doméně systému Windows použít zabezpečení Kerberos.

    1. Spusťte nástroje pro správu > **Uživatelé a počítače služby Active Directory**.

    2. Nakonfigurujte pokročilé funkce kliknutím na **zobrazit** > **Pokročilé funkce**.

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


## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
