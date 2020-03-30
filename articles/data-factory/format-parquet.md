---
title: Formát parket ve službě Azure Data Factory
description: Toto téma popisuje, jak zajít s formátem parket v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597622"
---
# <a name="parquet-format-in-azure-data-factory"></a>Formát parket ve službě Azure Data Factory

Postupujte podle tohoto článku, pokud chcete **analyzovat parketové soubory nebo zapsat data do formátu parkety**. 

Formát parket je podporovaný pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Parket.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost type datové sady musí být nastavena na **parkety**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souboru má svůj `location`vlastní typ umístění a podporované vlastnosti v části . **Viz podrobnosti v článku konektoru -> část vlastností datové sady**. | Ano      |
| kompreseKodlič | Kompresní kodek, který se má použít při psaní do parketových souborů. Při čtení z parketových souborů data továrny automaticky určit kompresní kodek na základě metadat souboru.<br>Podporované typy jsou "**none**", "**gzip**", "**snappy**" (výchozí) a "**lzo**". Poznámka: Aktuálně kopírovat aktivity nepodporuje LZO při čtení a zápisu parketových souborů. | Ne       |

> [!NOTE]
> Prázdné místo v názvu sloupce není pro parketové soubory podporováno.

Níže je uveden příklad datové sady parket v azure blob storage:

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou parket.

### <a name="parquet-as-source"></a>Parkety jako zdroj

Následující vlastnosti jsou podporovány v části *** \*zdroje aktivity\* *** kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **Název Pole- Zdroj**. | Ano      |
| storeSettings | Skupina vlastností o čtení dat z úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení čtení v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

### <a name="parquet-as-sink"></a>Parkety jako umyvadlo

Následující vlastnosti jsou podporovány v části *** \*jímky\* *** aktivity kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **ParketquetSink**. | Ano      |
| storeSettings | Skupina vlastností o tom, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení zápisu v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Naučte se podrobnosti z [transformace zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v toku dat mapování.

## <a name="data-type-support"></a>Podpora datového typu

Datové typy parketových komplexů nejsou v současné době podporovány (např.

## <a name="using-self-hosted-integration-runtime"></a>Použití prostředí Runtime integrace s vlastním hostitelem

> [!IMPORTANT]
> Pro kopírování zmocněný Self-hosted Integrace Runtime například mezi on-premises a cloud úložiště dat, pokud nejste kopírování parketové **soubory, jak-je**, je třeba nainstalovat **64-bit JRE 8 (Java Runtime Environment) nebo OpenJDK** a **Microsoft Visual C + + 2010 Redistribuovatelný balíček** na vašem počítači IR. Podívejte se na následující odstavec s dalšími podrobnostmi.

Pro kopírování spuštěné na samoobslužné ir s serializací par- souboru serializace/ *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* deserializace, ADF vyhledá java runtime *`JAVA_HOME`* za prvé kontrolou registru pro JRE, pokud nebyl nalezen, za druhé kontrolu systémové proměnné pro OpenJDK.

- **Použití JRE**: 64bitové infračervené záření vyžaduje 64bitový jre. Najdete ji [zde](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Chcete-li použít OpenJDK**: Je podporován od IR verze 3.13. Zabalte soubor jvm.dll se všemi ostatními požadovanými sestaveními openjdk do samoobslužného infračerveného počítače a odpovídajícím způsobem nastavte proměnnou prostředí systému JAVA_HOME.
- **Instalace redistribuovatelného balíčku Visual C++ 2010**: Redistribuovatelný balíček Visual C++ 2010 není nainstalován s infračervenými instalacemi hostovanými samostatně. Najdete ji [zde](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Pokud zkopírujete data do/z formátu Parketu pomocí prostředí s vlastním hostitelem integračního běhu a stisknete chybu s tím, že "Při vyvolání java došlo k chybě, zpráva: **java.lang.OutOfMemoryError:Java hedžprostor**", můžete přidat proměnnou `_JAVA_OPTIONS` prostředí v počítači, který je hostitelem samoobslužné infračervené hodu, a upravit velikost haldy min/max pro JVM, aby se tato kopie uvolnila, a pak znovu spustit kanál.

![Nastavit velikost haldy JVM na samoobslužné infračervené ovládání](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Příklad: nastavená `_JAVA_OPTIONS` `-Xms256m -Xmx16g`proměnná s hodnotou . Příznak `Xms` určuje počáteční fond přidělení paměti pro virtuální počítač Java `Xmx` (JVM), zatímco určuje maximální fond přidělení paměti. To znamená, že JVM `Xms` bude spuštěn s množstvím paměti a `Xmx` bude moci používat maximální množství paměti. Ve výchozím nastavení používá ADF min 64 MB a max 1G.

## <a name="next-steps"></a>Další kroky

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
