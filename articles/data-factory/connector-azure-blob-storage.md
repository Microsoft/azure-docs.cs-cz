---
title: Kopírovat data do nebo z Azure Blob storage pomocí služby Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z podporované zdrojové úložiště dat do úložiště objektů Blob v Azure nebo z úložiště objektů Blob do úložišť dat podporovaných podřízený pomocí služby Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: 3fdece082401ca57beabe6334a0ea0ca292ba298
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052346"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Kopírovat data do nebo z úložiště objektů Blob v Azure pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuální verze](connector-azure-blob-storage.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat do a z Azure Blob storage. Vychází [aktivity kopírování přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Jakékoli úložiště podporované zdroje dat může kopírovat data do úložiště objektů Blob. Také můžete zkopírovat data z úložiště objektů Blob do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md) tabulky.

Konkrétně podporuje tento konektor úložiště objektů Blob:

- Kopírování objektů BLOB do a z úložiště objektů blob v horké nebo nástrojů a účet úložiště pro obecné účely Azure. 
- Kopírování objektů BLOB s použitím klíč účtu a služby sdílený přístup k ověření podpisu.
- Kopírování objektů blob z bloku, připojit nebo objekty BLOB stránky a kopírování dat do pouze objekty BLOB bloků. Azure Storage úrovně Premium není podporován jako jímka, protože je zálohovaný díky objekty BLOB stránky.
- Kopírování objektů BLOB nebo analýza nebo generování objektů BLOB s [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení entit služby Data Factory konkrétní do úložiště objektů Blob.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

### <a name="use-an-account-key"></a>Použít klíč účtu

Propojenou službu úložiště můžete vytvořit pomocí klíč účtu. Poskytuje objekt pro vytváření dat s globálním přístupem k úložišti. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **azurestorage**. |Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k úložišti pro vlastnost connectionString. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat je v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

**Příklad:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-service-shared-access-signature-authentication"></a>Pomocí služby ověřování podpisu sdíleného přístupu

Propojenou službu úložiště můžete vytvořit také pomocí sdíleného přístupového podpisu. Poskytuje objekt pro vytváření dat omezený nebo časově vázaných přístup k prostředkům všechna nebo konkrétní (kontejner nebo objektů blob) v úložišti.

Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Sdílený přístupový podpis můžete použít k udělení, že klient omezené oprávnění k objektům v účtu úložiště pro určitou dobu. Nemáte sdílet klíče pro přístup k účtu. Sdílený přístupový podpis je identifikátor URI, který zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště v jeho parametry dotazu. Pro přístup k prostředkům úložiště s sdílený přístupový podpis, klient pouze musí předat sdílený přístupový podpis metodu, nebo odpovídající konstruktor. Další informace o sdílených přístupových podpisů najdete v tématu [sdílené přístupové podpisy: pochopení modelu sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Objekt pro vytváření dat se teď podporuje služby sdílené přístupové podpisy a účet sdílené přístupové podpisy. Další informace o těchto dvou typů a konstruování je najdete v tématu [druhy sdílených přístupových podpisů](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> Chcete-li vygenerovat sdílený přístupový podpis služby pro účet úložiště, můžete spustit následující příkazy prostředí PowerShell. Nahraďte zástupné symboly a udělení potřebných oprávnění.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Pokud chcete používat ověřování podpisu sdíleného přístupu služby, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **azurestorage**. |Ano |
| sasUri | Zadejte sdílený přístupový podpis URI k prostředkům úložiště, například objekt blob, kontejneru nebo tabulky. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Můžete použít modul Runtime integrace Azure nebo Runtime integrace Self-hosted (Pokud je vaše úložiště dat se nachází v privátní síti). Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

**Příklad:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Když vytvoříte sdílený přístupový podpis identifikátor URI, zvažte následující:

- Nastavit oprávnění vhodné pro čtení a zápis na objekty podle použití propojené služby (pro čtení, zápisu, čtení/zápis) ve službě data factory.
- Nastavit **čas vypršení platnosti** správně. Ujistěte se, že nemá přístup k objektům úložiště vyprší do aktivního období kanálu.
- Identifikátor URI by se vytvořit na správnou úroveň kontejner nebo objekt blob nebo table podle potřeby. Sdílený přístupový podpis URI do objektu blob umožní Data Factory pro přístup k tomuto konkrétní objektu blob. Sdílený přístupový podpis identifikátor URI pro kontejner úložiště objektů Blob umožňuje Data Factory k iteraci v rámci objektů BLOB v kontejneru. K poskytování přístupu k více nebo méně objektů později, nebo aktualizovat sdílený přístupový podpis identifikátor URI, nezapomeňte aktualizovat propojenou službu s nový identifikátor URI.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností nepodporuje datovou sadu objektu Blob úložiště.

Ke zkopírování dat do a z úložiště objektů Blob, nastavte vlastnost typu datové sady, která **AzureBlob**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na **AzureBlob**. |Ano |
| folderPath | Cesta ke kontejneru a složce v úložišti objektů blob. Zástupný filtr není podporován. Příkladem je myblobcontainer/myblobfolder /. |Ano |
| fileName | **Název nebo zástupný filtr** pro blob(s) pod zadanou "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny objekty BLOB ve složce. <br/><br/>Pro filtr, povoleny zástupné znaky jsou: `*` (odpovídá žádnému nebo více znaků) a `?` (odpovídá nula nebo jeden znak).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Použití `^` abyste se vyhnuli, pokud jejich název zástupných znaků nebo tento řídicí znak uvnitř.<br/><br/>Pokud není zadán název souboru pro datovou sadu výstupů a **preserveHierarchy** není zadané v jímce aktivity aktivitě kopírování automaticky vygeneruje název objektu blob pomocí následující vzoru: "*Data. [ Aktivita běžet id Identifikátor GUID.] [Identifikátor GUID Pokud FlattenHierarchy]. [Formát pokud nakonfigurovaný]. [Pokud nakonfigurovaný komprese]* ". Příkladem je "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". |Ne |
| Formát | Pokud chcete zkopírovat soubory, jako je mezi souborové úložiště (binární kopie), přejděte v části formátu v definicích vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo vygenerování soubory s konkrétním formátu, jsou podporovány následující typy souboru formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, a **ParquetFormat**. Nastavte **typ** vlastnost pod **formát** na jednu z těchto hodnot. Další informace najdete v tématu [textovém formátu](supported-file-formats-and-compression-codecs.md#text-format), [formátu JSON](supported-file-formats-and-compression-codecs.md#json-format), [formát Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátu](supported-file-formats-and-compression-codecs.md#orc-format), a [Parquet formátu ](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro scénář binární kopie) |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně **Optimal** a **nejrychlejší**. |Ne |

>[!TIP]
>Pokud chcete zkopírovat všechny objekty BLOB ve složce, zadejte **folderPath** pouze.<br>Pokud chcete zkopírovat jediného objektu blob se zadaným názvem, zadejte **folderPath** s částí složky a **fileName** s názvem souboru.<br>Pokud chcete zkopírovat podmnožinu objektů BLOB ve složce, zadejte **folderPath** s částí složky a **fileName** s filtrem zástupný znak. 

**Příklad:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje objekt Blob úložiště zdroj a jímka.

### <a name="blob-storage-as-a-source-type"></a>Úložiště objektů BLOB jako typ zdroje

Ke zkopírování dat z úložiště objektů Blob, nastavte typ zdroje v aktivitě kopírování do **BlobSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na **BlobSource**. |Ano |
| rekurzivní | Označuje, zda je data načíst rekurzivně z podsložky nebo pouze do zadané složky. Všimněte si, že když rekurzivní nastavena na hodnotu true a jímky je úložiště na základě souborů prázdnou složku nebo podsložku není zkopírovat nebo vytvořený na jímky.<br/>Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Úložiště objektů BLOB jako typ jímky

Pokud chcete zkopírovat data do úložiště objektů Blob, nastavit typ jímky v aktivitě kopírování do **BlobSink**. Následující vlastnosti jsou podporovány v **podřízený** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky aktivity kopírování musíte nastavit **BlobSink**. |Ano |
| copyBehavior | Definuje chování kopie, pokud je zdroj souborů z úložiště dat na základě souborů.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru do zdrojové složky je stejný jako relativní cestu k souboru cíl k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úroveň cílové složky. Cílové soubory mít názvy generován automaticky. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky pro jeden soubor. Pokud je zadaný název souboru nebo objekt blob, název souboru sloučené je zadaný název. Jinak je název souboru generován automaticky. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivní a copyBehavior

Tato část popisuje jejich výsledné chování pro různé kombinace hodnot rekurzivní a copyBehavior operace kopírování.

| rekurzivní | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true (pravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 je vytvořen s stejná struktura jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true (pravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File5 |
| true (pravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + soubor3 + File4 + File5 obsah jsou sloučeny do jednoho souboru s názvem souboru generován automaticky. |
| false (nepravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s soubor3, File4 a File5 není zachyceny. |
| false (nepravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílové složky složku1 je vytvořen s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název File2<br/><br/>Subfolder1 s soubor3, File4 a File5 není zachyceny. |
| false (nepravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Vytvoření cílové složky složku1 s následující strukturou<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem souboru generován automaticky. automaticky generovaný název File1<br/><br/>Subfolder1 s soubor3, File4 a File5 není zachyceny. |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v objektu pro vytváření dat najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
