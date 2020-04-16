---
title: Formát ORC ve službě Azure Data Factory
description: Toto téma popisuje, jak zajít s formátem ORC v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 9b68d3724c6390fc5d30745924451e27ef9855b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417721"
---
# <a name="orc-format-in-azure-data-factory"></a>Formát ORC ve službě Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postupujte podle tohoto článku, pokud chcete **analyzovat soubory ORC nebo zapsat data do formátu ORC**. 

Formát ORC je podporovaný pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou ORC.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost type datové sady musí být nastavena na **orc**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souboru má svůj `location`vlastní typ umístění a podporované vlastnosti v části . **Viz podrobnosti v článku konektoru -> část vlastností datové sady**. | Ano      |

Níže je příklad datové sady ORC v úložišti objektů blob Azure:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Je třeba počítat s následujícím:

* Komplexní datové typy nejsou podporovány (STRUCT, MAP, LIST, UNION).
* Prázdné místo v názvu sloupce není podporováno.
* Soubory ORC mají tři [možnosti komprese](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB a SNAPPY. Data Factory podporuje čtení dat ze souborů ORC v libovolném z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru ORC ale Data Factory využívá možnost ZLIB, která je pro formát ORC výchozí. V současnosti toto chování nejde potlačit.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem orc a jímkou.

### <a name="orc-as-source"></a>ORC jako zdroj

Následující vlastnosti jsou podporovány v části *** \*zdroje aktivity\* *** kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **OrcSource**. | Ano      |
| storeSettings | Skupina vlastností o čtení dat z úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení čtení v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

### <a name="orc-as-sink"></a>ORC jako umyvadlo

Následující vlastnosti jsou podporovány v části *** \*jímky\* *** aktivity kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **OrcSink**. | Ano      |
| storeSettings | Skupina vlastností o tom, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení zápisu v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

## <a name="using-self-hosted-integration-runtime"></a>Použití prostředí Runtime integrace s vlastním hostitelem

> [!IMPORTANT]
> Pro kopírování zmocněný Self-hosted Integrace Runtime například mezi on-premises a cloud úložiště dat, pokud nejste kopírování ORC **soubory, jak-je**, je třeba nainstalovat **64-bit JRE 8 (Java Runtime Environment) nebo OpenJDK** a **Microsoft Visual C + + 2010 Redistribuovatelný balíček** na vašem počítači IR. Podívejte se na následující odstavec s dalšími podrobnostmi.

Pro kopírování spuštěné na samoobslužné infračervené kontrole se serializací/deserializací *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* souboru ORC vyhledá ADF zaběhu *`JAVA_HOME`* java nejprve kontrolou registru pro JRE, pokud není nalezen, za druhé kontrolou systémové proměnné pro OpenJDK.

- **Použití JRE**: 64bitové infračervené záření vyžaduje 64bitový jre. Najdete ji [zde](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Chcete-li použít OpenJDK**: Je podporován od IR verze 3.13. Zabalte soubor jvm.dll se všemi ostatními požadovanými sestaveními openjdk do samoobslužného infračerveného počítače a odpovídajícím způsobem nastavte proměnnou prostředí systému JAVA_HOME.
- **Instalace redistribuovatelného balíčku Visual C++ 2010**: Redistribuovatelný balíček Visual C++ 2010 není nainstalován s infračervenými instalacemi hostovanými samostatně. Najdete ji [zde](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Pokud zkopírujete data do/z formátu ORC pomocí prostředí s vlastním hostitelem integrace Runtime a stiskněte chybu říká "Došlo k chybě při vyvolání java, `_JAVA_OPTIONS` zpráva: **java.lang.OutOfMemoryError:Java hedž prostor**", můžete přidat proměnnou prostředí v počítači, který je hostitelem Self-hostované IR upravit velikost haldy min/max pro JVM k posílení takové kopie, pak znovu spustit kanál.

![Nastavit velikost haldy JVM na samoobslužné infračervené ovládání](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Příklad: nastavená `_JAVA_OPTIONS` `-Xms256m -Xmx16g`proměnná s hodnotou . Příznak `Xms` určuje počáteční fond přidělení paměti pro virtuální počítač Java `Xmx` (JVM), zatímco určuje maximální fond přidělení paměti. To znamená, že JVM `Xms` bude spuštěn s množstvím paměti a `Xmx` bude moci používat maximální množství paměti. Ve výchozím nastavení používá ADF min 64 MB a max 1G.

## <a name="next-steps"></a>Další kroky

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
