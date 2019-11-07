---
title: Formát Parquet v Azure Data Factory
description: Toto téma popisuje, jak v Azure Data Factory pracovat s formátem Parquet.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9047e82713b709027275c75b17eb955877c62f08
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674774"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formát Parquet v Azure Data Factory

Pokud chcete **analyzovat soubory Parquet nebo zapsat data do formátu Parquet**, postupujte podle tohoto článku. 

Formát Parquet se podporuje pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google. Cloudové úložiště](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Parquet.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost Type datové sady musí být nastavená na **Parquet**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location`. **Podrobnosti najdete v článku o konektoru – > Vlastnosti datové sady**. | Ano      |
| compressionCodec | Kompresní kodek, který se má použít při zápisu do souborů Parquet Při čtení ze souborů Parquet Data Factory automaticky určit Kompresní kodek na základě metadat souboru.<br>Podporované typy jsou "**none**", "**gzip**", "**přichycení**" (výchozí) a "**LZO**". Poznámka: v současné době kopírování nepodporuje LZO. | Ne       |

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

V části ***\*zdrojového\**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **ParquetSource**. | Ano      |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají ve `storeSettings`své vlastní podporované nastavení pro čtení. **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

### <a name="parquet-as-sink"></a>Parquet jako jímka

V části\*aktivita kopírování ***\*jímka*** jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **ParquetSink**. | Ano      |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Jednotlivé konektory založené na souborech mají vlastní podporované nastavení zápisu v rámci `storeSettings`. **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Přečtěte si podrobnosti o [transformaci zdrojového kódu](data-flow-source.md) a [transformaci jímky](data-flow-sink.md) v mapování toku dat.

## <a name="data-type-support"></a>Podpora datových typů

Parquet komplexní datové typy se momentálně nepodporují (např. MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Použití Integration Runtime pro místní hostování

> [!IMPORTANT]
> Pro kopii, která je oprávněná pro místní hostování Integration Runtime například mezi místními a cloudovým úložištěm dat, pokud soubory Parquet nekopírujete **tak, jak jsou**, je nutné na počítač IR nainstalovat **64-bit JRE 8 (Java Runtime Environment) nebo OpenJDK** . Další podrobnosti najdete v následujícím článku.

Pro kopírování běžící v místním prostředí IR s Parquet serializací/deserializace vyhledá ADF modul runtime Java tím, že nejprve zkontroluje *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* registru pro JRE, pokud se nenalezne, podruhé kontroluje proměnnou systému *`JAVA_HOME`* pro OpenJDK.

- **Použití JRE**: 64-bit IR vyžaduje 64-bit JRE. Můžete ho najít [tady](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Použití OpenJDK**: podporuje se od verze IR 3,13. Zabalit soubor JVM. dll se všemi ostatními požadovanými sestaveními OpenJDK do místního počítače IR a nastavte proměnnou prostředí systému JAVA_HOME odpovídajícím způsobem.

> [!TIP]
> Pokud kopírujete data do nebo z formátu Parquet pomocí Integration Runtime v místním prostředí a dojde k chybě, při vyvolání Java došlo k chybě. zpráva: **Java. lang. OutOfMemoryError: prostor haldy Java**", můžete přidat proměnnou prostředí `_JAVA_OPTIONS` v počítač, který je hostitelem prostředí IR v místním prostředí, aby upravil minimální/maximální velikost haldy pro JVM, aby mohl takovou kopii provést, pak znovu spusťte kanál.

![Nastavení velikosti haldy JVM v místním prostředí IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Příklad: nastavte proměnnou `_JAVA_OPTIONS` s hodnotou `-Xms256m -Xmx16g`. Příznak `Xms` Určuje počáteční fond přidělení paměti pro prostředí Java Virtual Machine (JVM), zatímco `Xmx` určuje maximální fond přidělení paměti. To znamená, že JVM se spustí s `Xms` velikostí paměti a bude moci využít maximálně `Xmx` množství paměti. Ve výchozím nastavení ADF používá minimální 64 MB a maximální 1G.

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
