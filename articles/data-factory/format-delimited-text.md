---
title: Textový formát s oddělovači v Azure Data Factory
description: Toto téma popisuje, jak v Azure Data Factory pracovat s odděleným formátem textu.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: jingwang
ms.openlocfilehash: 1b32685aa060363d00f1566e009beee36bbf9680
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298546"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Textový formát s oddělovači v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte tento článek, pokud chcete **analyzovat soubory s oddělovači nebo zapsat data do textového formátu s oddělovači**. 

Pro následující konektory se podporuje textový formát s oddělovači [: Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou textů s oddělovači.

| Vlastnost         | Popis                                                  | Vyžadováno |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **DelimitedText**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` .  | Ano      |
| columnDelimiter  | Znak (y) použitý k oddělení sloupců v souboru. <br>Výchozí hodnota je **čárka `,` **. Pokud je Oddělovač sloupců definován jako prázdný řetězec, což znamená, že se nejedná o oddělovač, je celý řádek považován za jeden sloupec.<br>V současné době je Oddělovač sloupců jako prázdný řetězec nebo vícenásobný znak podporován pouze pro tok dat mapování, ale nikoli pro aktivitu kopírování.  | No       |
| rowDelimiter     | Jeden znak nebo "\r\n", který se používá k oddělení řádků v souboru. <br>Výchozí hodnota je libovolná z následujících hodnot pro **čtení: ["\r\n", "\r", "\n"]** a **"\n" nebo "\r\n" při zápisu** pomocí mapování toku dat a aktivity kopírování v uvedeném pořadí. <br>Je-li oddělovač řádků nastaven na hodnotu bez oddělovače (prázdný řetězec), musí být Oddělovač sloupců nastaven jako bez oddělovače (prázdný řetězec), což znamená, že bude celý obsah považován za jedinou hodnotu.<br>V současné době je oddělovač řádků jako prázdný řetězec podporován pouze pro tok dat mapování, ale nikoli pro aktivitu kopírování. | No       |
| quoteChar        | Jediný znak pro hodnoty sloupce uvozovek, pokud obsahuje oddělovač sloupců. <br>Výchozí hodnota je **dvojité uvozovky** `"` . <br>Pro mapování toku dat `quoteChar` nemůže být prázdný řetězec. <br>U aktivity kopírování, pokud `quoteChar` je definována jako prázdný řetězec, znamená, že se nezobrazuje žádná znak citace ani hodnota sloupce není v uvozovkách a `escapeChar` používá se k úniku oddělovače sloupců a sebe sama. | No       |
| escapeChar       | Jeden znak pro řídicí uvozovky uvnitř hodnoty v uvozovkách.<br>Výchozí hodnota je **zpětné lomítko `\` **. <br>Pro mapování toku dat `escapeChar` nemůže být prázdný řetězec. <br/>U aktivity kopírování, když `escapeChar` je definována jako prázdný řetězec, `quoteChar` musí být také nastaveno jako prázdný řetězec. v takovém případě zajistěte, aby všechny hodnoty sloupce neobsahovaly oddělovače. | No       |
| firstRowAsHeader | Určuje, zda má být první řádek považován za řádek záhlaví s názvy sloupců.<br>Povolené hodnoty jsou **true** a **false** (výchozí). | No       |
| nullValue        | Určuje řetězcovou reprezentaci hodnoty null. <br>Výchozí hodnota je **prázdný řetězec**. | No       |
| encodingName     | Typ kódování používaný pro čtení a zápis testovacích souborů. <br>Povolené hodnoty jsou následující: UTF-8, UTF-16, "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "Shift-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860"; "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "", "" 2022, "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13"; , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Datový tok mapování poznámek nepodporuje kódování UTF-7. | No       |
| compressionCodec | Kompresní kodek používaný pro čtení a zápis textových souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **přichycení**nebo **LZ4**. Výchozí hodnota není komprimovaná. <br>**Poznámka:** aktivita kopírování nepodporuje "přichycení" & "LZ4" a tok dat mapování nepodporuje "ZipDeflate". <br>**Poznámka:** při použití aktivity kopírování k dekompresi souborů **ZipDeflate** a zápisu do úložiště dat jímky založeného na souborech se ve výchozím nastavení extrahují soubory do složky: `<path specified in dataset>/<folder named as source zip file>/` pomocí `preserveZipFileNameAsFolder` [zdroje aktivity kopírování](#delimited-text-as-source) můžete určit, jestli se má zachovat název souboru ZIP jako struktura složek. | No       |
| compressionLevel | Kompresní poměr <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **Nejrychlejší:** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální**: komprese by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu. Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Níže je příklad objektu s oddělovači textu v Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . Tato část uvádí seznam vlastností, které jsou podporovány zdrojem a jímkou textu s oddělovači.

### <a name="delimited-text-as-source"></a>Text s oddělovači jako zdroj 

V části *** \* zdroj \* *** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost       | Popis                                                  | Vyžadováno |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **DelimitedTextSource**. | Ano      |
| formatSettings | Skupina vlastností Viz tabulka **nastavení čtení s oddělovači textu** níže. | No       |
| storeSettings  | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . | No       |

Podporované **nastavení čtení textu s oddělovači** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Vyžadováno |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **DelimitedTextReadSettings**. | Ano      |
| skipLineCount | Označuje počet **neprázdných** řádků, které se mají přeskočit při čtení dat ze vstupních souborů. <br>Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. | No       |
| compressionProperties | Skupina vlastností, jak dekomprimovat data pro daný Kompresní kodek. | No       |
| preserveZipFileNameAsFolder<br>(*pod `compressionProperties` *) | Platí v případě, že je vstupní datová sada nakonfigurovaná s **ZipDeflate** kompresí. Určuje, jestli se má při kopírování zachovat název zdrojového souboru ZIP jako struktura složek. Pokud je nastavená hodnota true (výchozí), Data Factory zapisuje soubory unzip do `<path specified in dataset>/<folder named as source zip file>/` ; při nastavení na hodnotu false Data Factory zapisuje soubory unzip přímo do `<path specified in dataset>` .  | No |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>Text s oddělovači jako jímku

V části *** \* jímka \* *** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost       | Popis                                                  | Vyžadováno |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **DelimitedTextSink**. | Ano      |
| formatSettings | Skupina vlastností Viz tabulka **nastavení zápisu s oddělovači textu** níže. |          |
| storeSettings  | Skupina vlastností, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má vlastní podporované nastavení zápisu v rámci `storeSettings` .  | No       |

Podporované **nastavení zápisu s oddělovači textu** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Vyžadováno                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **DelimitedTextWriteSettings**. | Ano                                                   |
| fileExtension | Přípona souboru, která slouží k pojmenování výstupních souborů, např. `.csv` `.txt` . Je nutné ji zadat, pokud `fileName` není zadána ve výstupní datové sadě DelimitedText. Když je ve výstupní datové sadě nakonfigurovaný název souboru, použije se jako název souboru jímky a nastavení přípony souboru se bude ignorovat.  | Ano, pokud není zadaný název souboru v výstupní datové sadě |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Přečtěte si podrobnosti o [transformaci zdrojového kódu](data-flow-source.md) a [transformaci jímky](data-flow-sink.md) v mapování toku dat.

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
