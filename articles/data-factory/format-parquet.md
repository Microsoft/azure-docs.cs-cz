---
title: Formát parquet ve službě Azure Data Factory | Dokumentace Microsoftu
description: Toto téma popisuje, jak zacházet s formát Parquet ve službě Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 360b794f0d8ba9c145a92f015f264eb624fbb0f1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144882"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formát parquet ve službě Azure Data Factory

Pokud chcete postupovat podle tohoto článku **analyzovat soubory Parquet nebo zapisovat data do formátu Parquet**. 

Formát parquet je podporován pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [objektů Blob v Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje Parquet datové sady.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost type datové sady, musí být nastavená na **Parquet**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor souborové má svůj vlastní typ umístění a nepodporuje vlastnosti z kategorie `location`. **Podrobnosti najdete v článku konektor -> vlastnosti části datové sady**. | Ano      |
| compressionCodec | Kompresní kodek k použití při zápisu do souborů Parquet. Při čtení ze souborů Parquet, Data Factory automaticky určit kompresní kodek na základě souboru metadat.<br>Podporované typy jsou "**žádný**","**gzip**","**Tenhle**" (výchozí), a "**lzo**". Poznámka: aktuálně aktivita kopírování nepodporuje LZO. | Ne       |

> [!NOTE]
> Prázdné znaky v názvu sloupce se nepodporuje pro soubory Parquet.

Níže je příklad Parquet datová sada Azure Blob Storage:

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Parquet zdroje a jímky.

### <a name="parquet-as-source"></a>Parquet jako zdroj

Následující vlastnosti jsou podporovány v aktivitě kopírování ***\*zdroj\**** oddílu.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavená na **ParquetSource**. | Ano      |
| storeSettings | Skupina vlastností o tom, jak přečíst data z úložiště dat. Každý konektor souborové má svůj vlastní podporovaných čtení nastavení v části `storeSettings`. **Podrobnosti najdete v článku konektor -> oddíl properties aktivity kopírování**. | Ne       |

### <a name="parquet-as-sink"></a>Parquet jako jímka

Následující vlastnosti jsou podporovány v aktivitě kopírování ***\*jímky\**** oddílu.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavená na **ParquetSink**. | Ano      |
| storeSettings | Skupina vlastností o tom, jak zapsat data do úložiště dat. Každý konektor souborové má vlastní podporované zápisu nastavení v části `storeSettings`. **Podrobnosti najdete v článku konektor -> oddíl properties aktivity kopírování**. | Ne       |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Další podrobnosti o [zdroje transformace](data-flow-source.md) a [jímky transformace](data-flow-sink.md) v mapování se předávají Data.

## <a name="data-type-support"></a>Podpora typ dat

Parquet, komplexní datové typy se momentálně nepodporuje (např. MAPY, seznam, struktura).

## <a name="using-self-hosted-integration-runtime"></a>Pomocí místního prostředí Integration Runtime

> [!IMPORTANT]
> Pro kopírování pověřený modul Integration Runtime například mezi místním prostředím a cloudem úložiště dat, pokud soubory Parquet nekopírujete **jako-je**, je potřeba nainstalovat **64bitové prostředí JRE 8 (Java Runtime Environment) nebo OpenJDK** na svém počítači reakcí na Incidenty. Viz odstavec s dalšími podrobnostmi.

Pro kopírování běží na místní prostředí IR prostřednictvím souboru Parquet serializace/deserializace, ADF vyhledá modul runtime Java za prvé kontrolou registru *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* pro prostředí JRE, pokud není nalezen, potom na základě kontroly systémová proměnná *`JAVA_HOME`* pro OpenJDK.

- **Použití prostředí JRE**: Prostředí IR 64-bit vyžaduje 64bitovou platformu JRE. Najdete ho z [tady](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Chcete-li použít OpenJDK**: ta se podporuje od verze 3.13 IR. Balíček jvm.dll se všechny ostatní požadované sestavení OpenJDK do místní prostředí IR počítačů a nastavte systém proměnnou prostředí JAVA_HOME odpovídajícím způsobem.

> [!TIP]
> Při kopírování dat do a z Parquet naformátovat pomocí modul Integration Runtime a přístupů o tom, že chyba "došlo k chybě při spouštění Javy, zpráva: **místo v haldě java.lang.OutOfMemoryError:Java**", můžete přidat proměnné prostředí `_JAVA_OPTIONS` na počítači, který je hostitelem místní prostředí IR upravit minimální/maximální velikost haldy pro JVM pro tyto kopie, pak znovu spusťte kanál.

![Nastavení velikosti haldy JVM na místní prostředí IR](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Příklad: nastavená proměnná `_JAVA_OPTIONS` s hodnotou `-Xms256m -Xmx16g`. Příznak `Xms` Určuje počáteční paměť přidělení fondu pro Java Virtual Machine (JVM), zatímco `Xmx` určuje maximální velikost paměti přidělení fondu. To znamená, že se bude spuštěn JVM `Xms` množství paměti a budete moct použít maximálně `Xmx` množství paměti. Ve výchozím nastavení ADF pomocí min 64MB a maximální počet 1G.

## <a name="next-steps"></a>Další postup

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)