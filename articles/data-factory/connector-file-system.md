---
title: Kopírování dat z/do systému souborů pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data ze systému souborů do podporovaných úložišť dat jímky (nebo) z podporovaných zdrojových úložišť dat do systému souborů pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 21329e3fada62036bc5b8d2bf6cdf6b3be5adad3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417409"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Kopírování dat do nebo ze systému souborů pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-onprem-file-system-connector.md)
> * [Aktuální verze](connector-file-system.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak kopírovat data do a ze systému souborů. Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor systému souborů je podporován pro následující činnosti:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně tento konektor systému souborů podporuje:

- Kopírování souborů z/do místního počítače nebo sdílené síťové složky. Chcete-li použít sdílenou složku Linuxu, nainstalujte [Sambu](https://www.samba.org/) na svůj linuxový server.
- Kopírování souborů pomocí ověřování **systému Windows.**
- Kopírování souborů tak, jak je nebo analýza / generování souborů s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro systém souborů.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu systému souborů jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na: **FileServer**. | Ano |
| host | Určuje kořenovou cestu složky, kterou chcete zkopírovat. Použijte řídicí znak\" " pro speciální znaky v řetězci. Viz [Ukázka propojené služby a definice datových sad](#sample-linked-service-and-dataset-definitions) pro příklady. | Ano |
| Userid | Zadejte ID uživatele, který má přístup k serveru. | Ano |
| heslo | Zadejte heslo pro uživatele (userid). Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

### <a name="sample-linked-service-and-dataset-definitions"></a>Ukázkové definice propojených služeb a datových sad

| Scénář | "hostitel" v definici propojené služby | "folderPath" v definici datové sady |
|:--- |:--- |:--- |
| Místní složka v integračním stroji Runtime: <br/><br/>Příklady: D:\\ \* nebo D:\folder\subfolder\\* |V JSON:`D:\\`<br/>V ui:`D:\` |V JSON: `.\\` nebo`folder\\subfolder`<br>V ui: `.\` nebo`folder\subfolder` |
| Vzdálená sdílená složka: <br/><br/>Příklady: \\ \\podsložka\\ \* \\ \\myserver\\\\\\share\\nebo myserver share folder\\* |V JSON:`\\\\myserver\\share`<br/>V ui:`\\myserver\share` |V JSON: `.\\` nebo`folder\\subfolder`<br/>V ui: `.\` nebo`folder\subfolder` |

>[!NOTE]
>Při vytváření pomocí ui, nemusíte zadávat dvojité zpětné`\\`lomítko ( ) uniknout, jako to děláte přes JSON, zadejte jedno zpětné lomítko.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `location` pro systém souborů v nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type `location` under in dataset musí být nastavena na **Umístění serveru FileServer .** | Ano      |
| folderPath | Cesta ke složce. Chcete-li k filtrování složky použít zástupný znak, přeskočte toto nastavení a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod danou složkouPath. Chcete-li k filtrování souborů použít zástupný znak, přeskočte toto nastavení a určete v nastavení zdroje aktivity. | Ne       |

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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou systému souborů.

### <a name="file-system-as-source"></a>Systém souborů jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `storeSettings` pro systém souborů v rámci nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost type `storeSettings` pod musí být nastavena na **Soubor FileServerReadSettings**. | Ano                                           |
| Rekurzivní                | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Všimněte si, že pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| program se zástupnými_znakem       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br>Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ne                                            |
| název souboru se zástupnými_znakové_         | Název souboru se zástupnými znaky pod danou složkouPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné `*` znaky jsou: (odpovídá `?` nula nebo více znaků) a (odpovídá nula nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř.  Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). [Vezměte prosím na vědomí, že to bude převládat vaše fileName nastavení] | Ano, `fileName` pokud není zadán v datové sadě |
| modifiedDatetimeStart    | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime. | Ne                                            |
| modifiedDatetimeEnd      | Stejně jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

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

### <a name="file-system-as-sink"></a>Systém souborů jako jímka

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány `storeSettings` pro systém souborů v rámci nastavení v jímce kopírování založené na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost type `storeSettings` pod musí být nastavena na **Soubor FileServerWriteSettings**. | Ano      |
| copyBehavior             | Definuje chování kopírování, když zdroj jsou soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>- PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru do cílové složky.<br/><b>- Sloučení :</b>Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>- MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, je zadaným názvem sloučený název souboru. V opačném případě se jedná o název automaticky generovaného souboru. | Ne       |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

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
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtrů složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru pomocí filtrů se zástupnými kódy.

| folderPath | fileName | Rekurzivní | Struktura zdrojové složky a výsledek filtru (soubory **tučně** jsou načteny)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (prázdný, použijte výchozí) | false (nepravda) | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | (prázdný, použijte výchozí) | true | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | `*.csv` | false (nepravda) | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | `*.csv` | true | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |

### <a name="recursive-and-copybehavior-examples"></a>rekurzivní a copyBehavior příklady

Tato část popisuje výsledné chování operace Copy pro různé kombinace rekurzivních a copyBehavior hodnot.

| Rekurzivní | copyBehavior | Struktura zdrojových složek | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |zachovat hierarchii | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena se stejnou strukturou jako zdroj:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5. |
| true |sloučení hierarchie | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru5 |
| true |mergeFiles | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 obsah jsou sloučeny do jednoho souboru s automaticky generovaným názvem souboru |
| false (nepravda) |zachovat hierarchii | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |sloučení hierarchie | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |mergeFiles | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah File1 + File2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky generovaný název souboru1<br/><br/>Podsložka1 s File3, File4 a File5 nejsou zachyceny. |

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
| type | Vlastnost type datové sady musí být nastavena na: **FileShare** |Ano |
| folderPath | Cesta ke složce. Filtr se zástupnými znaky je `*` podporován, povolené zástupné `?` znaky jsou: (odpovídá nule nebo více znaků) a (odpovídá nule nebo jeden znak); použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br/><br/>Příklady: kořenová složka/podsložka/, další příklady v [ukázkových definicích propojených služeb a datových sadách](#sample-linked-service-and-dataset-definitions) a [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). |Ne |
| fileName | **Filtr názvu nebo zástupných symbolů** pro soubor (soubory) pod zadanou "folderPath". Pokud pro tuto vlastnost nezadáte hodnotu, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr jsou povoleny `*` zástupné znaky: (odpovídá nula nebo více znaků) a `?` (odpovídá nule nebo jeden znak).<br/>- Příklad 1:`"fileName": "*.csv"`<br/>- Příklad 2:`"fileName": "???20180427.txt"`<br/>Použijte `^` k útěku, pokud váš skutečný název souboru má zástupný znak nebo tento escape char uvnitř.<br/><br/>Když fileName není zadán pro výstupní datovou sadu a **preserveHierarchy** není zadán v jímce aktivity, aktivita kopírování automaticky generuje název souboru s následujícím vzorem: "*Data.[ aktivita spustit Identifikátor GUID]. [GUID pokud sloučení hierarchie]. [formát, pokud je nakonfigurován]. [komprese, pokud je nakonfigurována]*", například "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; Pokud kopírujete z tabulkového zdroje pomocí názvu tabulky namísto dotazu, bude se vzor názvu "*[název tabulky].[ formátu]. [komprese, pokud je nakonfigurována]*", například "MyTable.csv". |Ne |
| modifiedDatetimeStart | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| modifiedDatetimeEnd | Filtr souborů založený na atributu: Poslední změna. Soubory budou vybrány, pokud jejich poslední upravený `modifiedDatetimeStart` čas `modifiedDatetimeEnd`je v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Uvědomte si, že celkový výkon přesunu dat bude ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů z velkého množství souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že žádný filtr atributů souboru bude použit pro datovou sadu.  Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime.  Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že budou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| formát | Pokud chcete **kopírovat soubory jako -je** mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory v určitém formátu, jsou podporovány následující typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte vlastnost **type** ve formátu na jednu z těchto hodnot. Další informace naleznete v části [Formát textu](supported-file-formats-and-compression-codecs-legacy.md#text-format), Formát [Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), Formát [orků](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a [Formát parket.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Ne (pouze pro binární scénář kopírování) |
| komprese | Určete typ a úroveň komprese dat. Další informace naleznete [v tématu Podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**.<br/>Podporované úrovně jsou: **Optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory pod složku, zadejte pouze **složkuCesta.**<br>Chcete-li zkopírovat jeden soubor s daným názvem, zadejte **složkuCesta** s částí složky a **název_souboru** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů pod složku, zadejte **složkuCesta** s částí složky a **název_souboru** s filtrem se zástupnými symboly.

>[!NOTE]
>Pokud jste pro filtr souborů používali vlastnost "fileFilter", je stále podporována tak, jak je, zatímco se doporučuje použít novou funkci filtru přidanou do "fileName" do budoucna.

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

### <a name="legacy-copy-activity-source-model"></a>Starší model zdroje aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena **na: FileSystemSource** |Ano |
| Rekurzivní | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Poznámka: Pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka nebudou zkopírovány nebo vytvořeny na jímce.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

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

### <a name="legacy-copy-activity-sink-model"></a>Starší model jímky aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na: **FileSystemSink.** |Ano |
| copyBehavior | Definuje chování kopírování, když zdroj je soubory z úložiště dat založené na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>- PreserveHierarchy (výchozí)</b>: zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru do cílové složky.<br/><b>- Sloučení:</b>všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generovaný název. <br/><b>- MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Během sloučení se neprovádí deduplikace záznamu. Pokud je zadán název souboru, sloučený název souboru by byl zadaný název; v opačném případě by byl automaticky generovaný název souboru. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

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

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
