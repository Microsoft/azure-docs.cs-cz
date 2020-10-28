---
title: Formát XML v Azure Data Factory
description: Toto téma popisuje, jak pracovat s formátem XML v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: 2ca13ef3c6fa5bbd40ee239c50a023beee85a977
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637169"
---
# <a name="xml-format-in-azure-data-factory"></a>Formát XML v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pokud chcete **analyzovat soubory XML** , postupujte podle tohoto článku. 

Formát XML je podporovaný pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md). Podporuje se jako zdroj, ale ne jímka.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou XML.

| Vlastnost         | Popis                                                  | Povinné |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **XML** . | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` . **Podrobnosti najdete v článku o konektoru – > vlastnosti datové sady** . | Ano      |
| encodingName     | Typ kódování používaný pro čtení a zápis testovacích souborů. <br>Povolené hodnoty jsou následující: UTF-8, UTF-16, "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "Shift-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860"; "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "", "" 2022, "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13"; , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Ne       |
| nullValue | Určuje řetězcovou reprezentaci hodnoty null.<br/>Výchozí hodnota je **prázdný řetězec** . | Ne |
| komprese | Skupina vlastností pro konfiguraci komprese souborů. Tuto část nakonfigurujte, pokud chcete během provádění aktivit provést kompresi nebo dekompresi. | Ne |
| typ<br>( *pod `compression`* ) | Kompresní kodek používaný pro čtení a zápis souborů XML. <br>Povolené hodnoty jsou **bzip2** , **gzip** , **Deflate** , **ZipDeflate** , **TarGzip** , **přichycení** nebo **LZ4** . Výchozí hodnota není komprimovaná.<br>**Poznámka:** aktivita kopírování nepodporuje "přichycení" & "LZ4" a tok dat mapování nepodporuje "ZipDeflate".<br>**Poznámka:** při použití aktivity kopírování k dekompresi souborů **ZipDeflate** / **TarGzip** a zápisu do úložiště dat jímky založeného na souborech se ve výchozím nastavení extrahují soubory do složky: `<path specified in dataset>/<folder named as source compressed file>/` pomocí `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [zdroje aktivity kopírování](#xml-as-source) můžete určit, jestli se má název komprimovaných souborů zachovat jako struktura složek. | Ne.  |
| úroveň<br/>( *pod `compression`* ) | Kompresní poměr <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší** .<br>- **Nejrychlejší:** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální** : komprese by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu. Další informace najdete v tématu [úroveň komprese](/dotnet/api/system.io.compression.compressionlevel) . | Ne       |

Níže je příklad datové sady XML v Azure Blob Storage:

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem XML.

Přečtěte si, jak namapovat data XML a úložiště/formát dat jímky z [mapování schématu](copy-activity-schema-and-type-mapping.md). Při prohlížení náhledu souborů XML se data zobrazí s hierarchií JSON a k ukázání na pole se používá cesta JSON.

### <a name="xml-as-source"></a>XML as source

V oddílu aktivita kopírování **_ \_ zdroj \*** * jsou podporovány následující vlastnosti. Přečtěte si další informace z [chování konektoru XML](#xml-connector-behavior).

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **XmlSource** . | Ano      |
| formatSettings | Skupina vlastností Další informace najdete v tabulce **nastavení čtení XML** níže. | Ne       |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity** . | Ne       |

Podporovaná **nastavení čtení XML** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **XmlReadSettings** . | Ano      |
| validationMode | Určuje, zda se má ověřit schéma XML.<br>Povolené hodnoty jsou **none** (výchozí, bez ověřování), **XSD** (ověřování pomocí XSD), **DTD** (ověření pomocí DTD). | Ne |
| obsažené | Určuje, zda má být při analýze souborů XML povolen obor názvů. Povolené hodnoty jsou: **true** (výchozí), **false** . | Ne |
| namespacePrefixes | Identifikátor URI oboru názvů mapování předpon, který se používá k pojmenování polí při analýze souboru XML.<br/>Pokud je v souboru XML povolen obor názvů a obor názvů, ve výchozím nastavení je název pole stejný jako v dokumentu XML.<br>Pokud je v této mapě definována položka pro identifikátor URI oboru názvů, název pole je `prefix:fieldName` . | Ne |
| detectDataType | Určuje, zda mají být zjišťovány datové typy integer, Double a Boolean. Povolené hodnoty jsou: **true** (výchozí), **false** .| Ne |
| compressionProperties | Skupina vlastností, jak dekomprimovat data pro daný Kompresní kodek. | Ne       |
| preserveZipFileNameAsFolder<br>( *v části `compressionProperties` -> `type` jako `ZipDeflateReadSettings`* )  | Platí v případě, že je vstupní datová sada nakonfigurovaná s **ZipDeflate** kompresí. Určuje, jestli se má při kopírování zachovat název zdrojového souboru ZIP jako struktura složek.<br>-Když je nastavená **hodnota true (výchozí)** , Data Factory zapisuje soubory unzip do `<path specified in dataset>/<folder named as source zip file>/` .<br>– Pokud je nastavené na **false** , Data Factory zapisuje soubory unzip přímo do `<path specified in dataset>` . Ujistěte se, že nemáte duplicitní názvy souborů v různých zdrojových souborech ZIP, abyste se vyhnuli neočekávanému chování.  | Ne |
| preserveCompressionFileNameAsFolder<br>( *v části `compressionProperties` -> `type` jako `TarGZipReadSettings`* ) | Platí v případě, že je vstupní datová sada nakonfigurovaná s **TarGzip** kompresí. Určuje, zda se má při kopírování zachovat zdrojový komprimovaný název souboru jako struktura složky.<br>-Když je nastavená **hodnota true (výchozí)** , Data Factory zapisuje dekomprimované soubory do `<path specified in dataset>/<folder named as source compressed file>/` . <br>– Pokud je nastavené na **false** , Data Factory zapisuje dekomprimované soubory přímo do `<path specified in dataset>` . Ujistěte se, že nemáte duplicitní názvy souborů v různých zdrojových souborech, abyste se vyhnuli neočekávanému chování. | Ne |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

V části mapování toků dat můžete číst a zapisovat do formátu XML v následujících úložištích dat: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Můžete odkazovat na soubory XML buď pomocí datové sady XML, nebo pomocí [vložené datové sady](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem XML. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** . Přečtěte si další informace z [chování konektoru XML](#xml-connector-behavior). Při použití vložené datové sady se zobrazí další nastavení souborů, která jsou stejná jako vlastnosti popsané v části [Vlastnosti datové sady](#dataset-properties) . 

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Cesty k zástupným kartám | Budou zpracovány všechny soubory, které odpovídají zástupné cestě. Přepíše složku a cestu k souboru nastavenou v datové sadě. | Ne | Řetězec [] | wildcardPaths |
| Kořenová cesta oddílu | Pro souborová data, která jsou rozdělená na oddíly, můžete zadat kořenovou cestu oddílu, aby bylo možné číst rozdělené složky jako sloupce. | No | Řetězec | partitionRootPath |
| Seznam souborů | Určuje, zda váš zdroj odkazuje na textový soubor se seznamem souborů, které se mají zpracovat. | Ne | `true` nebo `false` | fileList |
| Sloupec, ve kterém se má uložit název souboru | Vytvoří nový sloupec s názvem a cestou ke zdrojovému souboru. | No | Řetězec | rowUrlColumn |
| Po dokončení | Odstraní nebo přesune soubory po zpracování. Cesta k souboru začíná z kořene kontejneru | Ne | Odstranit: `true` nebo `false` <br> Pøesunout `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| Filtrovat podle poslední změny | Zvolit filtrování souborů podle toho, kdy se naposledy změnily | Ne | Timestamp | modifiedAfter <br>modifiedBefore |
| Režim ověřování | Určuje, zda se má ověřit schéma XML. | Ne | `None` (výchozí, bez ověření)<br>`xsd` (ověřit pomocí XSD)<br>`dtd` (ověření pomocí DTD). | validationMode |
| Obory názvů | Určuje, zda má být při analýze souborů XML povolen obor názvů. | Ne | `true` (výchozí) nebo `false` | obsažené |
| Páry předpon oboru názvů | Identifikátor URI oboru názvů mapování předpon, který se používá k pojmenování polí při analýze souboru XML.<br/>Pokud je v souboru XML povolen obor názvů a obor názvů, ve výchozím nastavení je název pole stejný jako v dokumentu XML.<br>Pokud je v této mapě definována položka pro identifikátor URI oboru názvů, název pole je `prefix:fieldName` . | Ne | Pole se vzorem`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| Nenalezeny žádné soubory | Pokud je nastaveno na true, chyba není vyvolána, pokud nebyly nalezeny žádné soubory. | ne | `true` nebo `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>Příklad zdrojového skriptu XML

Níže uvedený skript je příkladem konfigurace zdroje XML v části mapování toků dat pomocí režimu DataSet.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

Níže uvedený skript je příkladem konfigurace zdroje XML pomocí režimu vložené datové sady.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>Chování konektoru XML

Při použití XML jako zdroje Pamatujte na toto:.

- Atributy XML:

    - Atributy prvku jsou analyzovány jako podpole elementu v hierarchii.
    - Název pole atributu se řídí vzorem `@attributeName` .

- Ověření schématu XML:

    - Můžete zvolit, že neověřit schéma, nebo ověřit schéma pomocí XSD nebo DTD.
    - Při použití XSD nebo DTD k ověřování XML soubory musí být XSD/DTD v rámci souborů XML uveden pomocí relativní cesty.

- Manipulace s oborem názvů:

    - Obor názvů lze zakázat při použití toku dat. v takovém případě atributy definující obor názvů budou analyzovány jako normální atributy.
    - Pokud je povolen obor názvů, názvy elementu a atributů následují podle vzoru       `namespaceUri,elementName` a `namespaceUri,@attributeName` ve výchozím nastavení. Můžete definovat předponu oboru názvů pro každý identifikátor URI oboru názvů ve zdroji. v takovém případě názvy elementů a atributů postupují podle vzoru `definedPrefix:elementName` nebo `definedPrefix:@attributeName` místo toho.

- Sloupec hodnoty:

    - Pokud má element XML jak jednoduchou textovou hodnotu, tak atributy/podřízené prvky, je hodnota jednoduchého textu analyzována jako hodnota sloupce "s názvem" s integrovaným názvem pole `_value_` . A dědí obor názvů elementu, i když platí.

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)