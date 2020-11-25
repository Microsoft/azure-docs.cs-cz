---
title: Textový formát s oddělovači v Azure Data Factory
description: Toto téma popisuje, jak v Azure Data Factory pracovat s odděleným formátem textu.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: db0808edae2baf3aec3f524bcbe92bb544925dfe
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019935"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Textový formát s oddělovači v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte tento článek, pokud chcete **analyzovat soubory s oddělovači nebo zapsat data do textového formátu s oddělovači**. 

Pro následující konektory se podporuje textový formát s oddělovači [: Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou textů s oddělovači.

| Vlastnost         | Popis                                                  | Povinné |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **DelimitedText**. | Yes      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` .  | Yes      |
| columnDelimiter  | Znak (y) použitý k oddělení sloupců v souboru. <br>Výchozí hodnota je **čárka `,`**. Pokud je Oddělovač sloupců definován jako prázdný řetězec, což znamená, že se nejedná o oddělovač, je celý řádek považován za jeden sloupec.<br>V současné době je Oddělovač sloupců jako prázdný řetězec nebo vícenásobný znak podporován pouze pro tok dat mapování, ale nikoli pro aktivitu kopírování.  | No       |
| rowDelimiter     | Jeden znak nebo "\r\n", který se používá k oddělení řádků v souboru. <br>Výchozí hodnota je libovolná z následujících hodnot pro **čtení: ["\r\n", "\r", "\n"]** a **"\n" nebo "\r\n" při zápisu** pomocí mapování toku dat a aktivity kopírování v uvedeném pořadí. <br>Je-li oddělovač řádků nastaven na hodnotu bez oddělovače (prázdný řetězec), musí být Oddělovač sloupců nastaven jako bez oddělovače (prázdný řetězec), což znamená, že bude celý obsah považován za jedinou hodnotu.<br>V současné době je oddělovač řádků jako prázdný řetězec podporován pouze pro tok dat mapování, ale nikoli pro aktivitu kopírování. | No       |
| quoteChar        | Jediný znak pro hodnoty sloupce uvozovek, pokud obsahuje oddělovač sloupců. <br>Výchozí hodnota je **dvojité uvozovky** `"` . <br>Pokud `quoteChar` je definován jako prázdný řetězec, znamená to, že žádný znak citace ani hodnota sloupce nejsou v uvozovkách a `escapeChar` slouží k opuštění oddělovače sloupců a sebe samé. | No       |
| escapeChar       | Jeden znak pro řídicí uvozovky uvnitř hodnoty v uvozovkách.<br>Výchozí hodnota je **zpětné lomítko `\`**. <br>Pokud `escapeChar` je definován jako prázdný řetězec, `quoteChar` musí být nastavena jako prázdný řetězec. v takovém případě zajistěte, aby všechny hodnoty sloupce neobsahovaly oddělovače. | No       |
| firstRowAsHeader | Určuje, zda má být první řádek považován za řádek záhlaví s názvy sloupců.<br>Povolené hodnoty jsou **true** a **false** (výchozí).<br>Pokud je první řádek jako záhlaví nepravdivý, poznamenejte si výstup dat uživatelského rozhraní a aktivity vyhledávání automaticky vygeneruje názvy sloupců, jako Prop_ {n} (počínaje 0), aktivita kopírování vyžaduje [explicitní mapování](copy-activity-schema-and-type-mapping.md#explicit-mapping) ze zdroje na jímku a vyhledává sloupce podle pořadového čísla (počínaje 1) a namapováním seznamu toku dat a vyhledává sloupce s názvem jako Column_ {n} (počínaje 1).  | No       |
| nullValue        | Určuje řetězcovou reprezentaci hodnoty null. <br>Výchozí hodnota je **prázdný řetězec**. | No       |
| encodingName     | Typ kódování používaný pro čtení a zápis testovacích souborů. <br>Povolené hodnoty jsou následující: UTF-8, UTF-16, "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "Shift-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860"; "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "", "" 2022, "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13"; , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Datový tok mapování poznámek nepodporuje kódování UTF-7. | No       |
| compressionCodec | Kompresní kodek používaný pro čtení a zápis textových souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **TarGzip**, **tar**, **přichycení** a **LZ4**. Výchozí hodnota není komprimovaná. <br>**Poznámka:** aktivita kopírování nepodporuje "přichycení" & "LZ4" a tok dat mapování nepodporuje "ZipDeflate", "TarGzip" a "tar". <br>**Poznámka:** při použití aktivity kopírování k dekompresi souborů **ZipDeflate** / **TarGzip** / **Tar** a zápisu do úložiště dat jímky založeného na souborech se ve výchozím nastavení extrahují soubory do složky: `<path specified in dataset>/<folder named as source compressed file>/` pomocí `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [zdroje aktivity kopírování](#delimited-text-as-source) můžete určit, jestli se má název komprimovaných souborů zachovat jako struktura složek. | No       |
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

V oddílu aktivita kopírování **_ \_ zdroj \**** jsou podporovány následující vlastnosti.

| Vlastnost       | Popis                                                  | Povinné |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **DelimitedTextSource**. | Yes      |
| formatSettings | Skupina vlastností Viz tabulka **nastavení čtení s oddělovači textu** níže. |  No       |
| storeSettings  | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . | No       |

Podporované **nastavení čtení textu s oddělovači** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **DelimitedTextReadSettings**. | Yes      |
| skipLineCount | Označuje počet **neprázdných** řádků, které se mají přeskočit při čtení dat ze vstupních souborů. <br>Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. | No       |
| compressionProperties | Skupina vlastností, jak dekomprimovat data pro daný Kompresní kodek. | No       |
| preserveZipFileNameAsFolder<br>(*v části `compressionProperties` -> `type` jako `ZipDeflateReadSettings`*) |  Platí v případě, že je vstupní datová sada nakonfigurovaná s **ZipDeflate** kompresí. Určuje, jestli se má při kopírování zachovat název zdrojového souboru ZIP jako struktura složek.<br>-Když je nastavená **hodnota true (výchozí)**, Data Factory zapisuje soubory unzip do `<path specified in dataset>/<folder named as source zip file>/` .<br>– Pokud je nastavené na **false**, Data Factory zapisuje soubory unzip přímo do `<path specified in dataset>` . Ujistěte se, že nemáte duplicitní názvy souborů v různých zdrojových souborech ZIP, abyste se vyhnuli neočekávanému chování.  | No |
| preserveCompressionFileNameAsFolder<br>(*v části `compressionProperties` -> `type` jako `TarGZipReadSettings` nebo `TarReadSettings`*)  | Použije se, když je pro vstupní datovou sadu nakonfigurovaná komprese **TarGzip** / **tar** . Určuje, zda se má při kopírování zachovat zdrojový komprimovaný název souboru jako struktura složky.<br>-Když je nastavená **hodnota true (výchozí)**, Data Factory zapisuje dekomprimované soubory do `<path specified in dataset>/<folder named as source compressed file>/` . <br>– Pokud je nastavené na **false**, Data Factory zapisuje dekomprimované soubory přímo do `<path specified in dataset>` . Ujistěte se, že nemáte duplicitní názvy souborů v různých zdrojových souborech, abyste se vyhnuli neočekávanému chování. | No |

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

V části **\_ jímka \*** aktivity kopírování _ * jsou podporovány následující vlastnosti.

| Vlastnost       | Popis                                                  | Povinné |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **DelimitedTextSink**. | Yes      |
| formatSettings | Skupina vlastností Viz tabulka **nastavení zápisu s oddělovači textu** níže. |    No      |
| storeSettings  | Skupina vlastností, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má vlastní podporované nastavení zápisu v rámci `storeSettings` .  | No       |

Podporované **nastavení zápisu s oddělovači textu** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Povinné                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **DelimitedTextWriteSettings**. | Yes                                                   |
| fileExtension | Přípona souboru, která slouží k pojmenování výstupních souborů, například `.csv` , `.txt` . Je nutné ji zadat, pokud `fileName` není zadána ve výstupní datové sadě DelimitedText. Když je ve výstupní datové sadě nakonfigurovaný název souboru, použije se jako název souboru jímky a nastavení přípony souboru se bude ignorovat.  | Ano, pokud není zadaný název souboru v výstupní datové sadě |
| maxRowsPerFile | Při zápisu dat do složky můžete zvolit zápis do více souborů a zadat maximální počet řádků na soubor.  | No |
| fileNamePrefix | Platí při `maxRowsPerFile` konfiguraci.<br> Při zápisu dat do více souborů zadejte předponu názvu souboru. Výsledkem je tento vzor: `<fileNamePrefix>_00000.<fileExtension>` . Pokud tento parametr nezadáte, automaticky se vygeneruje Předpona názvu souboru. Tato vlastnost se nevztahuje na to, že zdroj je úložiště založené na souborech nebo v [úložišti dat s povolenými možnostmi pro oddíly](copy-activity-performance-features.md).  | No |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

V části mapování toků dat můžete číst a zapisovat textový formát s oddělovači v následujících úložištích dat: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem odděleného textu. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Cesty k zástupným kartám | Budou zpracovány všechny soubory, které odpovídají zástupné cestě. Přepíše složku a cestu k souboru nastavenou v datové sadě. | ne | Řetězec [] | wildcardPaths |
| Kořenová cesta oddílu | Pro souborová data, která jsou rozdělená na oddíly, můžete zadat kořenovou cestu oddílu, aby bylo možné číst rozdělené složky jako sloupce. | ne | Řetězec | partitionRootPath |
| Seznam souborů | Určuje, zda váš zdroj odkazuje na textový soubor se seznamem souborů, které se mají zpracovat. | ne | `true` nebo `false` | fileList |
| Víceřádkové řádky | Obsahuje zdrojový soubor řádky, které jsou rozloženy na více řádků. Víceřádkové hodnoty musí být v uvozovkách. | Ne `true` nebo `false` | multiLineRow |
| Sloupec, ve kterém se má uložit název souboru | Vytvoří nový sloupec s názvem a cestou ke zdrojovému souboru. | ne | Řetězec | rowUrlColumn |
| Po dokončení | Odstraní nebo přesune soubory po zpracování. Cesta k souboru začíná z kořene kontejneru | ne | Odstranit: `true` nebo `false` <br> Pøesunout `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtrovat podle poslední změny | Zvolit filtrování souborů podle toho, kdy se naposledy změnily | ne | Timestamp | modifiedAfter <br> modifiedBefore |
| Nenalezeny žádné soubory | Pokud je nastaveno na true, chyba není vyvolána, pokud nebyly nalezeny žádné soubory. | ne | `true` nebo `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Zdrojový příklad

Níže uvedený obrázek je příkladem konfigurace neomezeného textu v části mapování toků dat.

![DelimitedText zdroj](media/data-flow/delimited-text-source.png)

Přidružený skript toku dat je:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

### <a name="sink-properties"></a>Vlastnosti jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které jsou podporovány jímkou s oddělovači textu. Tyto vlastnosti můžete upravit na kartě **Nastavení** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Vymazat složku | Pokud před zápisem není cílová složka smazána | ne | `true` nebo `false` | zkrátit |
| Možnost názvu souboru | Formát názvů zapsaných dat. Ve výchozím nastavení je jeden soubor na oddíl ve formátu `part-#####-tid-<guid>` | ne | Vzor: řetězec <br> Na oddíl: řetězec [] <br> Jako data ve sloupci: String <br> Výstup do jednoho souboru: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Citace – vše | Uzavření všech hodnot do uvozovek | ne | `true` nebo `false` | quoteAll |

### <a name="sink-example"></a>Příklad jímky

Níže uvedený obrázek je příkladem konfigurace jímky s odděleným textem v části mapování toků dat.

![Jímka DelimitedText](media/data-flow/delimited-text-sink.png)

Přidružený skript toku dat je:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)