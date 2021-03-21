---
title: Formát Avro v Azure Data Factory
description: Toto téma popisuje, jak v Azure Data Factory pracovat s formátem Avro.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 4be499e8e304f34b1cab10aed41b5b98a5f24e9b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392591"
---
# <a name="avro-format-in-azure-data-factory"></a>Formát Avro v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pokud chcete **analyzovat soubory Avro nebo zapsat data do formátu Avro**, postupujte podle tohoto článku. 

Formát Avro se podporuje pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Avro.

| Vlastnost         | Popis                                                  | Povinné |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **Avro**. | Yes      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` . **Podrobnosti najdete v článku o konektoru – > vlastnosti datové sady**. | Yes      |
| avroCompressionCodec | Kompresní kodek, který se má použít při zápisu do souborů Avro Při čtení ze souborů Avro Data Factory automaticky zjišťuje Kompresní kodek na základě metadat souboru.<br>Podporované typy jsou **none**(výchozí), "**uprostřed**", "**přichycení**". Poznámka: v současné době kopírování není při čtení a zápisu souborů Avro podporovat přichycení. | No       |

> [!NOTE]
> Prázdné znaky v názvu sloupce nejsou pro soubory Avro podporovány.

Níže je příklad datové sady Avro v Azure Blob Storage:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které Avro zdroj a jímka podporuje.

### <a name="avro-as-source"></a>Avro as source

V části ***\* zdroj \**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **AvroSource**. | Yes      |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | No       |

### <a name="avro-as-sink"></a>Avro jako jímka

V části ***\* jímka \**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **AvroSink**. | Yes      |
| formatSettings          | Skupina vlastností V tabulce **nastavení zápisu Avro** najdete níže.| No      |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má vlastní podporované nastavení zápisu v rámci `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | No       |

Podporovaná **nastavení zápisu Avro** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Povinné                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **AvroWriteSettings**. | Yes                                                   |
| maxRowsPerFile | Při zápisu dat do složky můžete zvolit zápis do více souborů a zadat maximální počet řádků na soubor.  | No |
| fileNamePrefix | Platí při `maxRowsPerFile` konfiguraci.<br> Při zápisu dat do více souborů zadejte předponu názvu souboru. Výsledkem je tento vzor: `<fileNamePrefix>_00000.<fileExtension>` . Pokud tento parametr nezadáte, automaticky se vygeneruje Předpona názvu souboru. Tato vlastnost se nevztahuje na to, že zdroj je úložiště založené na souborech nebo v [úložišti dat s povolenými možnostmi pro oddíly](copy-activity-performance-features.md).  | No |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

V části mapování toků dat můžete číst a zapisovat do formátu Avro v následujících úložištích dat: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem Avro. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Cesty k zástupným kartám | Budou zpracovány všechny soubory, které odpovídají zástupné cestě. Přepíše složku a cestu k souboru nastavenou v datové sadě. | ne | Řetězec [] | wildcardPaths |
| Kořenová cesta oddílu | Pro souborová data, která jsou rozdělená na oddíly, můžete zadat kořenovou cestu oddílu, aby bylo možné číst rozdělené složky jako sloupce. | ne | Řetězec | partitionRootPath |
| Seznam souborů | Určuje, zda váš zdroj odkazuje na textový soubor se seznamem souborů, které se mají zpracovat. | ne | `true` nebo `false` | fileList |
| Sloupec, ve kterém se má uložit název souboru | Vytvoří nový sloupec s názvem a cestou ke zdrojovému souboru. | ne | Řetězec | rowUrlColumn |
| Po dokončení | Odstraní nebo přesune soubory po zpracování. Cesta k souboru začíná z kořene kontejneru | ne | Odstranit: `true` nebo `false` <br> Pøesunout `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtrovat podle poslední změny | Zvolit filtrování souborů podle toho, kdy se naposledy změnily | ne | Timestamp | modifiedAfter <br> modifiedBefore |
| Nenalezeny žádné soubory | Pokud je nastaveno na true, chyba není vyvolána, pokud nebyly nalezeny žádné soubory. | ne | `true` nebo `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>Vlastnosti jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které Avro jímka podporuje. Tyto vlastnosti můžete upravit na kartě **Nastavení** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Vymazat složku | Pokud před zápisem není cílová složka smazána | ne | `true` nebo `false` | zkrátit |
| Možnost názvu souboru | Formát názvů zapsaných dat. Ve výchozím nastavení je jeden soubor na oddíl ve formátu `part-#####-tid-<guid>` | ne | Vzor: řetězec <br> Na oddíl: řetězec [] <br> Jako data ve sloupci: String <br> Výstup do jednoho souboru: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Citace – vše | Uzavření všech hodnot do uvozovek | ne | `true` nebo `false` | quoteAll |

## <a name="data-type-support"></a>Podpora datových typů

### <a name="copy-activity"></a>Aktivita kopírování
Avro [komplexní datové typy](https://avro.apache.org/docs/current/spec.html#schema_complex) se v aktivitě kopírování nepodporují (záznamy, výčty, pole, mapy, sjednocení a pevné).

### <a name="data-flows"></a>Toky dat
Při práci se soubory Avro v datových tocích můžete číst a zapisovat komplexní datové typy, ale nezapomeňte nejdřív vymazat fyzické schéma z datové sady. V datových tocích můžete nastavit logickou projekci a odvodit sloupce, které jsou komplexní struktury, a pak tato pole automaticky mapovat na soubor Avro.

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
