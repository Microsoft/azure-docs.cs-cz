---
title: Formát Parquet v Azure Data Factory
description: Toto téma popisuje, jak v Azure Data Factory pracovat s formátem Parquet.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/27/2020
ms.author: jingwang
ms.openlocfilehash: c99225b53266fc74ea357151de824cd8d8ed2088
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946140"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formát Parquet v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pokud chcete **analyzovat soubory Parquet nebo zapsat data do formátu Parquet**, postupujte podle tohoto článku. 

Formát Parquet se podporuje pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Parquet.

| Vlastnost         | Popis                                                  | Povinné |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **Parquet**. | Yes      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` . **Podrobnosti najdete v článku o konektoru – > vlastnosti datové sady**. | Yes      |
| compressionCodec | Kompresní kodek, který se má použít při zápisu do souborů Parquet Při čtení ze souborů Parquet určují datové továrny automaticky kodek komprese na základě metadat souboru.<br>Podporované typy jsou "**none**", "**gzip**", "**přichycení**" (výchozí) a "**LZO**". Poznámka: v současné době kopírování nepodporuje LZO při čtení a zápisu Parquet souborů. | No       |

> [!NOTE]
> Prázdné znaky v názvu sloupce nejsou pro soubory Parquet podporovány.

Níže je příklad datové sady Parquet v Azure Blob Storage:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností, které Parquet zdroj a jímka podporuje.

### <a name="parquet-as-source"></a>Parquet as source

V části *** \* zdroj \* *** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **ParquetSource**. | Yes      |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | No       |

### <a name="parquet-as-sink"></a>Parquet jako jímka

V části *** \* jímka \* *** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type jímky aktivity kopírování musí být nastavená na **ParquetSink**. | Yes      |
| formatSettings | Skupina vlastností V tabulce **nastavení zápisu Parquet** najdete níže. |    No      |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má vlastní podporované nastavení zápisu v rámci `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | No       |

Podporovaná **nastavení zápisu Parquet** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Povinné                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **ParquetWriteSettings**. | Yes                                                   |
| maxRowsPerFile | Při zápisu dat do složky můžete zvolit zápis do více souborů a zadat maximální počet řádků na soubor.  | No |
| fileNamePrefix | Platí při `maxRowsPerFile` konfiguraci.<br> Při zápisu dat do více souborů zadejte předponu názvu souboru. Výsledkem je tento vzor: `<fileNamePrefix>_00000.<fileExtension>` . Pokud tento parametr nezadáte, automaticky se vygeneruje Předpona názvu souboru. Tato vlastnost se nevztahuje na to, že zdroj je úložiště založené na souborech nebo v [úložišti dat s povolenými možnostmi pro oddíly](copy-activity-performance-features.md).  | No |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

V části mapování toků dat můžete číst a zapisovat do formátu Parquet v následujících úložištích dat: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem Parquet. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Formát musí být `parquet` | ano | `parquet` | formát |
| Cesty k zástupným kartám | Budou zpracovány všechny soubory, které odpovídají zástupné cestě. Přepíše složku a cestu k souboru nastavenou v datové sadě. | ne | Řetězec [] | wildcardPaths |
| Kořenová cesta oddílu | Pro souborová data, která jsou rozdělená na oddíly, můžete zadat kořenovou cestu oddílu, aby bylo možné číst rozdělené složky jako sloupce. | ne | Řetězec | partitionRootPath |
| Seznam souborů | Určuje, zda váš zdroj odkazuje na textový soubor se seznamem souborů, které se mají zpracovat. | ne | `true` nebo `false` | fileList |
| Sloupec, ve kterém se má uložit název souboru | Vytvoří nový sloupec s názvem a cestou ke zdrojovému souboru. | ne | Řetězec | rowUrlColumn |
| Po dokončení | Odstraní nebo přesune soubory po zpracování. Cesta k souboru začíná z kořene kontejneru | ne | Odstranit: `true` nebo `false` <br> Pøesunout `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Filtrovat podle poslední změny | Zvolit filtrování souborů podle toho, kdy se naposledy změnily | ne | Timestamp | modifiedAfter <br> modifiedBefore |
| Nenalezeny žádné soubory | Pokud je nastaveno na true, chyba není vyvolána, pokud nebyly nalezeny žádné soubory. | ne | `true` nebo `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Zdrojový příklad

Níže uvedený obrázek je příkladem konfigurace zdroje Parquet v části mapování toků dat.

![Parquet zdroj](media/data-flow/parquet-source.png)

Přidružený skript toku dat je:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'parquet') ~> ParquetSource
```

### <a name="sink-properties"></a>Vlastnosti jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které Parquet jímka podporuje. Tyto vlastnosti můžete upravit na kartě **Nastavení** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Formát musí být `parquet` | ano | `parquet` | formát |
| Vymazat složku | Pokud před zápisem není cílová složka smazána | ne | `true` nebo `false` | zkrátit |
| Možnost názvu souboru | Formát názvů zapsaných dat. Ve výchozím nastavení je jeden soubor na oddíl ve formátu `part-#####-tid-<guid>` | ne | Vzor: řetězec <br> Na oddíl: řetězec [] <br> Jako data ve sloupci: String <br> Výstup do jednoho souboru: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>Příklad jímky

Níže uvedený obrázek je příkladem konfigurace jímky Parquet v části mapování toků dat.

![Jímka Parquet](media/data-flow/parquet-sink.png)

Přidružený skript toku dat je:

```
ParquetSource sink(
    format: 'parquet',
    filePattern:'output[n].parquet',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> ParquetSink
```

## <a name="data-type-support"></a>Podpora datových typů

Parquet komplexní datové typy (například MAP, LIST, STRUCT) se aktuálně podporují jenom v datových tocích, nikoli v aktivitě kopírování. Chcete-li použít komplexní typy v datových tocích, neimportujte do datové sady schéma souborů a v datové sadě nechejte schéma prázdné. Pak ve zdrojové transformaci importujte projekci.

## <a name="using-self-hosted-integration-runtime"></a>Použití Integration Runtime pro místní hostování

> [!IMPORTANT]
> Pro kopii, která je oprávněná v místním prostředí Integration Runtime například mezi místními a cloudovým úložištěm dat, pokud soubory Parquet nekopírujete **tak, jak jsou**, je třeba na počítač IR nainstalovat **64-bit JRE 8 (Java Runtime Environment) nebo OpenJDK** and **Microsoft Visual C++ 2010 Redistributable Package** . Další podrobnosti najdete v následujícím odstavci.

Pro kopírování běžící v prostředí IR v místním prostředí s Parquet serializací/deserializace vyhledá ADF modul runtime Java tím, že se nejprve zkontroluje registr *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* pro JRE, pokud se nenajde, druhá kontroluje proměnnou systému *`JAVA_HOME`* pro OpenJDK.

- **Použití JRE**: 64-bit IR vyžaduje 64-bit JRE. Můžete ho najít [tady](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Použití OpenJDK**: podporuje se od verze IR 3,13. Zabalit jvm.dll se všemi ostatními požadovanými sestaveními OpenJDK do místně hostovaného počítače IR a nastavit proměnnou prostředí systému JAVA_HOME odpovídajícím způsobem.
- **Instalace balíčku Visual C++ 2010 Redistributable**: Visual C++ 2010 Distribuovatelný balíček není nainstalovaný s instalacemi v místním prostředí IR. Můžete ho najít [tady](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Pokud kopírujete data do nebo z formátu Parquet pomocí Integration Runtime v místním prostředí a omylem zaznamenáte chybu při vyvolání Java, zpráva: **Java. lang. OutOfMemoryError: prostor haldy Java**", můžete přidat proměnnou prostředí `_JAVA_OPTIONS` v počítači, který je hostitelem prostředí IR v místním prostředí, a upravit tak minimální/maximální velikost haldy pro JVM, abyste mohli takovou kopii provést, a pak znovu spustit kanál.

![Nastavení velikosti haldy JVM v místním prostředí IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Příklad: nastavte proměnnou `_JAVA_OPTIONS` s hodnotou `-Xms256m -Xmx16g` . Příznak `Xms` Určuje počáteční fond přidělení paměti pro prostředí Java Virtual Machine (JVM), zatímco `Xmx` Určuje maximální fond přidělení paměti. To znamená, že JVM bude spuštěn s `Xms` velikostí paměti a bude moci využít maximální `Xmx` množství paměti. Ve výchozím nastavení ADF používá minimální 64 MB a maximální 1G.

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
