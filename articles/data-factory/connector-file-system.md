---
title: Kopírování dat z/do systému souborů pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak ke zkopírování dat z a podporovaných podřízený datová úložiště v systému souborů (nebo) z podporované zdrojové úložiště dat do systému souborů pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
ms.openlocfilehash: f7f3f8d28c44a0ecadb9fed895ec2d37a5469142
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046914"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Kopírovat data do nebo ze systému souborů pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-onprem-file-system-connector.md)
> * [Aktuální verze](connector-file-system.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z a do systému souborů. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Můžete zkopírovat data do úložiště dat žádné podporované podřízený ze systému souborů nebo zkopírování dat z jakékoli úložiště podporované zdroje dat do systému souborů. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor systém souborů podporuje:

- Kopírování souborů z/do místního počítače nebo síťové sdílené složky. Chcete-li použít sdílenou složku systému Linux, nainstalovat [Samba](https://www.samba.org/) na Linux server.
- Kopírování souborů pomocí **Windows** ověřování.
- Kopírování souborů jako-je nebo analýza nebo generování souborů pomocí [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Požadavky

Ke zkopírování dat z/do systému souborů, které nejsou veřejně přístupný, musíte nastavit Self-hosted integrace Runtime. V tématu [Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md) článku.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení entit služby Data Factory, které jsou specifické pro systém souborů.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro soubor systému propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavena na: **souborovém serveru**. | Ano |
| hostitel | Určuje cestu kořenové složky, kterou chcete zkopírovat. Použít řídicí znak "\" pro speciální znaky v řetězci. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady. | Ano |
| ID uživatele | Zadejte ID uživatele, který má přístup k serveru. | Ano |
| heslo | Zadejte heslo pro uživatele (ID uživatele). Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je veřejně přístupná data store), můžete použít modul Runtime integrace Self-hosted nebo Runtime integrace Azure. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

### <a name="sample-linked-service-and-dataset-definitions"></a>Ukázkové propojené služby a definice datové sady

| Scénář | "hostitel" v definici propojené služby | "folderPath" v definici datové sady |
|:--- |:--- |:--- |
| Místní složka na počítači Runtime integrace: <br/><br/>Příklady: D:\\ \* nebo D:\folder\subfolder\\* |Ve formátu JSON: `D:\\`<br/>Na uživatelské rozhraní: `D:\` |Ve formátu JSON: `.\\` nebo `folder\\subfolder`<br>Na uživatelské rozhraní: `.\` nebo `folder\subfolder` |
| Vzdálené sdílené složce: <br/><br/>Příklady: \\ \\myserver\\sdílet\\ \* nebo \\ \\myserver\\sdílet\\složky\\podsložky\\* |Ve formátu JSON: `\\\\myserver\\share`<br/>Na uživatelské rozhraní: `\\myserver\share` |Ve formátu JSON: `.\\` nebo `folder\\subfolder`<br/>Na uživatelské rozhraní: `.\` nebo `folder\subfolder` |

>[!NOTE]
>Při vytváření prostřednictvím uživatelského rozhraní, nemusíte vstupní dvojité zpětné lomítko (`\\`) abyste se vyhnuli, stejně jako prostřednictvím formátu JSON, zadejte jedno zpětné lomítko.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datovou sadu systému souborů.

Ke zkopírování dat z/do systému souborů, nastavte vlastnost typu datové sady, která **sdílení souborů**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na: **sdílení souborů** |Ano |
| folderPath | Cesta ke složce. Zástupný filtr není podporován. V tématu [ukázka propojené definice služby a datovou sadu](#sample-linked-service-and-dataset-definitions) příklady. |Ano |
| fileName | **Název nebo zástupný filtr** pro soubory v zadané "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` abyste se vyhnuli, pokud jejich název zástupných znaků nebo tento řídicí znak uvnitř.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů a **preserveHierarchy** není zadané v jímce aktivity aktivitě kopírování automaticky vygeneruje název souboru v následujícím formátu: "*Data. [ Aktivita běžet id Identifikátor GUID.] [Identifikátor GUID Pokud FlattenHierarchy]. [Formát pokud nakonfigurovaný]. [Pokud nakonfigurovaný komprese] "*. Příkladem je "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". |Ne |
| Formát | Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo vygenerování soubory s konkrétním formátu, jsou podporovány následující typy souboru formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](supported-file-formats-and-compression-codecs.md#text-format), [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátu](supported-file-formats-and-compression-codecs.md#orc-format), a [Parquet formát](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro scénář binární kopie) |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimal** a **nejrychlejší**. |Ne |

>[!TIP]
>Pokud chcete zkopírovat všechny soubory ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jeden soubor s daným názvem, zadejte **folderPath** s částí složky a **fileName** s názvem souboru.<br>Pokud chcete zkopírovat podmnožinu souborů ve složce, zadejte **folderPath** s částí složky a **fileName** s filtrem zástupný znak.

>[!NOTE]
>Pokud jste používali vlastnost "fileFilter" pro filtr souborů, je stále podporovány jako-se, když jsou navrhované nová funkce filtru, který byl přidán do "název souboru" do budoucna.

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
            "fileName": "myfile.csv.gz",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaný zdroj systému souborů a jímky.

### <a name="file-system-as-source"></a>Systém souborů jako zdroj

Ke kopírování dat ze systému souborů, nastavte typ zdroje v aktivitě kopírování do **FileSystemSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **FileSystemSource** |Ano |
| rekurzivní | Určuje, jestli se data z podřízené složky nebo pouze do zadané složky Číst rekurzivně. Poznámka: když rekurzivní nastavena na hodnotu true a jímka je na základě souborů úložiště, prázdné složky nebo dílčí-folder nebudou zkopírovat nebo vytvořit v jímky.<br/>Povolené hodnoty jsou: **true** (výchozí), **false** | Ne |

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

### <a name="file-system-as-sink"></a>Systém souborů jako jímku

Ke zkopírování dat do systému souborů, nastavte typ jímky v aktivitě kopírování do **FileSystemSink**. Následující vlastnosti jsou podporovány v **podřízený** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky kopie aktivity musí nastavena: **FileSystemSink** |Ano |
| copyBehavior | Definuje chování kopie, pokud je zdroj souborů z úložiště dat na základě souborů.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cestu k souboru cíl k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složce. Cílové soubory mít název automaticky generovány. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky pro jeden soubor. Pokud je zadán název souboru nebo objekt Blob, název souboru sloučené by být zadaný název; jinak by automaticky generovaný soubor název. | Ne |

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

### <a name="recursive-and-copybehavior-examples"></a>Příklady rekurzivní a copyBehavior

Tato část popisuje jejich výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| rekurzivní | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true (pravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 je vytvořen s stejná struktura jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true (pravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + soubor3 + File4 + soubor 5 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor |
| false (nepravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s soubor3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/>Subfolder1 s soubor3, File4 a File5 nejsou zachyceny. |
| false (nepravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky generovaný soubor. automaticky generovaný název File1<br/><br/>Subfolder1 s soubor3, File4 a File5 nejsou zachyceny. |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
