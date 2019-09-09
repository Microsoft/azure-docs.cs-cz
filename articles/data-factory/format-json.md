---
title: Formát JSON v Azure Data Factory | Microsoft Docs
description: Toto téma popisuje, jak se zabývat formátem JSON v Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 7ff164b378ac6981fcb9686d6264b0bcf50cfafb
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814808"
---
# <a name="json-format-in-azure-data-factory"></a>Formát JSON v Azure Data Factory

Použijte tento článek, pokud chcete **analyzovat soubory JSON nebo zapsat data do formátu JSON**. 

Formát JSON se podporuje pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a Protokol [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných datovou sadou JSON.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost Type datové sady musí být nastavená na **JSON**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v `location`rámci. **Podrobnosti najdete v článku o konektoru – > Vlastnosti datové sady**. | Ano      |
| encodingName     | Typ kódování používaný pro čtení a zápis testovacích souborů. <br>Povolené hodnoty jsou následující: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", " IBM852 "," IBM855 "," IBM857 "," IBM860 "," IBM861 "," IBM863 "," IBM864 "," IBM865 "," IBM869 "," IBM870 "," IBM01140 "," IBM01141 "," IBM01142 "," IBM01143 "," IBM01144 "," IBM01145 "," IBM01146 "," IBM01147 "," IBM01148 "," IBM01149 "," ISO-2022 2022 "," ISO-8859-1 "," ISO-8859-2 "," ISO-8859-3 "," ISO-8859-4 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," ISO-8859-13 "," ISO-8859-15 "," WINDOWS-874 "," WINDOWS-1250 "," WINDOWS-1251 "," WINDOWS-1252 "," WINDOWS-1253 ";" WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".| Ne       |
| compressionCodec | Kompresní kodek používaný pro čtení a zápis textových souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **přichycení**nebo **LZ4**. pro použití při ukládání souboru. <br>Poznámka: v tuto chvíli aktivita kopírování nepodporuje "přichycení" & "LZ4". | Ne       |
| compressionLevel | Kompresní poměr <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **Způsobem** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální**: Kompresní operace by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu. Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tématu. | Ne       |

Níže je příklad datové sady JSON v Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem a jímkou JSON.

### <a name="json-as-source"></a>JSON jako zdroj

V části ***\*zdroj\**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **JSONSource**. | Ano      |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci `storeSettings`své vlastní podporované nastavení pro čtení. **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

### <a name="json-as-sink"></a>JSON jako jímka

V části ***\*jímka\**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **JSONSink**. | Ano      |
| formatSettings | Skupina vlastností Další informace najdete v tabulce **nastavení zápisu JSON** níže. | Ne       |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má vlastní podporované nastavení zápisu v rámci `storeSettings`. **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

Podporovaná **nastavení zápisu JSON** v rámci `formatSettings`:

| Vlastnost      | Popis                                                  | Požaduje se                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatSettings musí být nastaven na hodnotu **JsonWriteSetting**. | Ano                                                   |
| filePattern |Určete vzor dat uložených v jednotlivých souborech JSON. Povolené hodnoty jsou **setOfObjects** a **arrayOfObjects**. **Výchozí hodnota** je **setOfObjects**. Podrobné informace o těchto vzorech najdete v tématu [Vzory souborů JSON](#json-file-patterns). |Ne |

### <a name="json-file-patterns"></a>Vzory souborů JSON

Aktivita kopírování může automaticky detekovat a analyzovat následující vzory souborů JSON. 

- **Typ I: setOfObjects**

    Každý soubor obsahuje jeden objekt nebo několik objektů, které jsou zřetězené nebo oddělené řádkem. 
    Pokud je tato možnost vybrána v jímky aktivity kopírování, aktivita kopírování vytvoří jeden soubor JSON s každým objektem na řádek (oddělený řádek).

    * **Příklad JSON s jedním objektem**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Příklad JSON s oddělením řádky**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Příklad JSON se zřetězením**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Každý soubor obsahuje pole objektů.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
