---
title: Kopírování aktivity ve Službě Azure Data Factory
description: Přečtěte si o aktivitě kopírování v Azure Data Factory. Můžete ji použít ke kopírování dat z úložiště dat podporovaného zdroje do úložiště dat podporovanéjím.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: edb80c604951a140d21e3775eec3f1dc6d55af73
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421417"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopírování aktivity ve Službě Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Datové továrny, kterou používáte:"]
> * [Verze 1](v1/data-factory-data-movement-activities.md)
> * [Aktuální verze](copy-activity-overview.md)

V Azure Data Factory můžete použít aktivitu Kopírování ke kopírování dat mezi úložišti dat umístěnými místně a v cloudu. Po zkopírování dat můžete použít jiné aktivity k jejich další transformaci a analýze. Aktivitu Kopírovat můžete také použít k publikování výsledků transformace a analýzy pro business intelligence (BI) a spotřebu aplikací.

![Role aktivity kopírování](media/copy-activity-overview/copy-activity.png)

Aktivita kopírování je spuštěna při [integračním běhu](concepts-integration-runtime.md). Můžete použít různé typy integračních runtimesů pro různé scénáře kopírování dat:

* Když kopírujete data mezi dvěma datovými úložišti, která jsou veřejně přístupná přes internet z libovolné IP adresy, můžete pro aktivitu kopírování použít runtime integrace Azure. Tento integrační runtime je bezpečný, spolehlivý, škálovatelný a [globálně dostupný](concepts-integration-runtime.md#integration-runtime-location).
* Když kopírujete data do a z datových úložišť, která jsou umístěná místně nebo v síti s řízením přístupu (například virtuální síť Azure), musíte nastavit runtime integrace s vlastním hostitelem.

Integrační runtime musí být přidruženy ke každému zdrojovému úložišti a úložišti dat jímky. Informace o tom, jak aktivita Kopírování určuje, který integrační runtime použít, naleznete [v tématu Určení, které infračervené odznané prostředí použít](concepts-integration-runtime.md#determining-which-ir-to-use).

Chcete-li zkopírovat data ze zdroje do jímky, služba, která spouští aktivitu Copy provádí tyto kroky:

1. Čte data ze zdrojového úložiště dat.
2. Provádí serializaci/deserializaci, kompresi/dekompresi, mapování sloupců a tak dále. Provádí tyto operace na základě konfigurace vstupní datové sady, výstupní datové sady a aktivity Kopírování.
3. Zapíše data do úložiště dat jímky a cíle.

![Přehled aktivit kopírování](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Podporovaná úložiště a formáty dat

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Podporované formáty souborů

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Aktivitu Kopírovat můžete použít ke kopírování souborů tak, jak jsou mezi dvěma úložišti dat založených na souborech, v takovém případě jsou data zkopírována efektivně bez serializace nebo deserializace. Kromě toho můžete také analyzovat nebo generovat soubory daného formátu, například můžete provést následující:

* Zkopírujte data z místní databáze SERVERU SQL Server a zapište do Azure Data Lake Storage Gen2 ve formátu Parket.
* Zkopírujte soubory v textovém formátu (CSV) z místního systému souborů a zapište do úložiště objektů Blob Azure ve formátu Avro.
* Zkopírujte soubory z webu z místního systému souborů, dekomprimujte je průběžně a zapisujte extrahované soubory do azure data lake storage gen2.
* Zkopírujte data ve formátu Gzip pro komprimovaný text (CSV) z úložiště objektů Blob Azure a zapište je do Azure SQL Database.
* Mnoho dalších aktivit, které vyžadují serializaci/deserializaci nebo kompresi/dekompresi.

## <a name="supported-regions"></a>Podporované oblasti

Služba, která umožňuje aktivitu Kopírování, je dostupná globálně v oblastech a zeměpisných oblastech uvedených v [umístěních runtime integrace Azure](concepts-integration-runtime.md#integration-runtime-location). Globálně dostupná topologie zajišťuje efektivní přesun dat, který se obvykle vyhýbá směrování mezi oblastmi. Podívejte se [na produkty podle oblasti,](https://azure.microsoft.com/regions/#services) kde zkontrolujte dostupnost data factory a přesun dat v určité oblasti.

## <a name="configuration"></a>Konfigurace

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Obecně platí, že chcete-li použít aktivitu kopírování v Azure Data Factory, musíte:

1. **Vytvořte propojené služby pro zdrojové úložiště dat a úložiště dat jímky.** Seznam podporovaných konektorů najdete v části [Podporovaná úložiště a formáty dat](#supported-data-stores-and-formats) v tomto článku. Informace o konfiguraci a podporované vlastnosti naleznete v části Vlastnosti propojené služby v článku konektoru. 
2. **Vytvořte datové sady pro zdroj a jímky.** Informace o konfiguraci a podporované vlastnosti naleznete v částech "Vlastnosti datové sady" v článcích zdrojové a jímky.
3. **Vytvořte kanál s aktivitou Kopírovat.** Další část obsahuje příklad.

### <a name="syntax"></a>Syntaxe

Následující šablona aktivity kopírování obsahuje úplný seznam podporovaných vlastností. Zadejte ty, které odpovídají vašemu scénáři.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Podrobnosti syntaxe

| Vlastnost | Popis | Povinné? |
|:--- |:--- |:--- |
| type | U aktivity kopírování nastavte`Copy` | Ano |
| Vstupy | Zadejte datovou sadu, kterou jste vytvořili a která odkazuje na zdrojová data. Aktivita kopírování podporuje pouze jeden vstup. | Ano |
| Výstupy | Zadejte datovou sadu, kterou jste vytvořili a která odkazuje na data jímky. Aktivita kopírování podporuje pouze jeden výstup. | Ano |
| typeProperties | Zadejte vlastnosti pro konfiguraci aktivity kopírování. | Ano |
| source | Zadejte typ zdroje kopírování a odpovídající vlastnosti pro načítání dat.<br/>Další informace naleznete v části Kopírovat vlastnosti aktivity v článku spojnice uvedeném v [části Podporovaná úložiště dat a formáty](#supported-data-stores-and-formats). | Ano |
| Dřez | Zadejte typ jímky kopie a odpovídající vlastnosti pro zápis dat.<br/>Další informace naleznete v části Kopírovat vlastnosti aktivity v článku spojnice uvedeném v [části Podporovaná úložiště dat a formáty](#supported-data-stores-and-formats). | Ano |
| Překladatel | Zadejte explicitní mapování sloupců ze zdroje do jímky. Tato vlastnost platí, když výchozí chování kopírování nevyhovuje vašim potřebám.<br/>Další informace naleznete [v tématu Mapování schématu v aktivitě kopírování](copy-activity-schema-and-type-mapping.md). | Ne |
| dataIntegrationUnits | Zadejte míru, která představuje množství energie, které [prostředí integrace Azure](concepts-integration-runtime.md) používá pro kopírování dat. Tyto jednotky byly dříve známé jako cloudové jednotky pohybu dat (DMU). <br/>Další informace naleznete v [tématu Jednotky integrace dat](copy-activity-performance-features.md#data-integration-units). | Ne |
| parallelCopies | Zadejte paralelismus, který má aktivita Kopírovat použít při čtení dat ze zdroje a zápisu dat do jímky.<br/>Další informace naleznete v [tématu Paralelní kopírování](copy-activity-performance-features.md#parallel-copy). | Ne |
| Zachovat | Určete, zda se mají během kopírování dat zachovat metadata/akl. <br/>Další informace naleznete v tématu [Preserve metadata](copy-activity-preserve-metadata.md). |Ne |
| enableStaging<br/>pracovní nastavení | Určete, zda chcete zinscenovat průběžná data v úložišti objektů Blob namísto přímého kopírování dat ze zdroje do jímky.<br/>Informace o užitečných scénářích a podrobnostech konfigurace naleznete v [tématu Fázovaná kopie](copy-activity-performance-features.md#staged-copy). | Ne |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Zvolte způsob zpracování nekompatibilních řádků při kopírování dat ze zdroje do jímky.<br/>Další informace naleznete v tématu [Tolerance poruchy](copy-activity-fault-tolerance.md). | Ne |

## <a name="monitoring"></a>Monitorování

Můžete sledovat aktivitu kopírování spuštěnou v Azure Data Factory vizuálně i programově. Podrobnosti naleznete v tématu [Sledování aktivity kopírování](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Přírůstková kopie

Data Factory umožňuje postupně kopírovat rozdílová data ze zdrojového úložiště dat do úložiště dat jímky. Podrobnosti naleznete v [tématu Výuka: Přírůstkové kopírování dat](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Výkon a ladění

Prostředí [sledování aktivity kopírování](copy-activity-monitoring.md) zobrazuje statistiky výkonu kopírování pro každou spuštěnou aktivitu. [Průvodce výkonem a škálovatelností aktivity kopírování](copy-activity-performance.md) popisuje klíčové faktory, které ovlivňují výkon přesunu dat prostřednictvím aktivity kopírování v Azure Data Factory. Obsahuje také seznam hodnot výkonu pozorovaných během testování a popisuje, jak optimalizovat výkon aktivity kopírování.

## <a name="resume-from-last-failed-run"></a>Pokračovat od posledního neúspěšného spuštění

Aktivita kopírování podporuje obnovení z posledního neúspěšného spuštění při kopírování velké velikosti souborů, jako je binární formát mezi obchody založenými na souborech a zvolte zachovat hierarchii složek nebo souborů ze zdroje do jímky, například migrovat data z Amazon S3 do Azure Data Lake Storage Gen2. Vztahuje se na následující konektory založené na souborech: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)a [SFTP](connector-sftp.md).

Obnovení aktivity kopírování můžete využít následujícími dvěma způsoby:

- **Opakování úrovně aktivity:** Můžete nastavit počet opakování aktivity kopírování. Během spuštění kanálu, pokud se toto spuštění aktivity kopírování nezdaří, další automatické opakování se spustí z bodu selhání poslední zkušební verze.
- **Znovu spustit z neúspěšné aktivity:** Po dokončení spuštění kanálu můžete také aktivovat opakované spuštění z neúspěšné aktivity v zobrazení monitorování u pou z u adf nebo programově. Pokud se nezdařilo aktivity je aktivita kopírování, bude kanál nejen znovu spustit z této aktivity, ale také pokračovat z bodu selhání předchozího spuštění.

    ![Kopírovat životopis](media/copy-activity-overview/resume-copy.png)

Několik bodů na vědomí:

- Životopis se děje na úrovni souboru. Pokud se při kopírování souboru nezdaří aktivita kopírování, bude tento konkrétní soubor při dalším spuštění znovu zkopírován.
- Chcete-li, aby životopis fungoval správně, neměňte nastavení aktivity kopírování mezi opakováními.
- Když kopírujete data z AmazonS3, Azure Blob, Azure Data Lake Storage Gen2 a Google Cloud Storage, aktivita kopírování může pokračovat z libovolného počtu zkopírovaných souborů. Zatímco pro zbytek spojnic založených na souborech jako zdroj, aktuálně kopírovací aktivita podporuje obnovení z omezeného počtu souborů, obvykle v rozsahu desítek tisíc a liší se v závislosti na délce cest souborů; soubory nad rámec tohoto čísla budou během opakování znovu zkopírovány.

U jiných scénářů než binární kopie souboru začíná opětovné spuštění aktivity kopírování od začátku.

## <a name="preserve-metadata-along-with-data"></a>Zachování metadat spolu s daty

Při kopírování dat ze zdroje do jímky, ve scénářích, jako je migrace datových jezev, můžete také zachovat metadata a akly spolu s daty pomocí aktivity kopírování. Podrobnosti [najdete v tématu Zachovat metadata.](copy-activity-preserve-metadata.md)

## <a name="schema-and-data-type-mapping"></a>Mapování schématu a datového typu

Informace o tom, jak aktivita kopírování mapuje zdrojová data do jímky, najdete v tématu [Schéma a mapování datových typů.](copy-activity-schema-and-type-mapping.md)

## <a name="add-additional-columns-during-copy"></a>Přidání dalších sloupců během kopírování

Kromě kopírování dat ze zdrojového úložiště dat do jímky můžete také nakonfigurovat přidání dalších datových sloupců, které chcete zkopírovat do jímky. Například:

- Při kopírování ze zdroje založeného na souboru uložte relativní cestu k souboru jako další sloupec, který bude sledovat, ze kterého souboru data pocházejí.
- Přidejte sloupec s výrazem ADF, který připojí systémové proměnné ADF, jako je id názvu kanálu/kanálu, nebo uloží jinou dynamickou hodnotu z výstupu aktivity upstream.
- Přidejte sloupec se statickou hodnotou, který splní vaši potřebu následné spotřeby.

Na kartě zdroj aktivity kopírování najdete následující konfiguraci: 

![Přidání dalších sloupců v aktivitě kopírování](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Tato funkce pracuje s nejnovějším modelem datové sady. Pokud tuto možnost nevidíte z ui, zkuste vytvořit novou datovou sadu.

Chcete-li ji nakonfigurovat `additionalColumns` programově, přidejte vlastnost do zdroje aktivity kopírování:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| additionalColumns | Přidejte další sloupce dat, které chcete zkopírovat do jímky.<br><br>Každý objekt `additionalColumns` pod polepředstavuje další sloupec. Definuje `name` název sloupce a `value` označuje hodnotu dat tohoto sloupce.<br><br>Povolené hodnoty dat jsou:<br>- **`$$FILEPATH`**- rezervovaná proměnná označuje uložení relativní cesty zdrojových souborů k cestě ke složce zadané v datové sadě. Platí pro zdroj založený na souborech.<br>- **Výraz**<br>- **Statická hodnota** | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="fault-tolerance"></a>Odolnost proti chybám

Ve výchozím nastavení aktivita kopírování zastaví kopírování dat a vrátí selhání, pokud jsou řádky zdrojových dat nekompatibilní s řádky dat jímky. Chcete-li, aby byla kopie úspěšná, můžete nakonfigurovat aktivitu kopírování tak, aby přeskakovala a protokolovala nekompatibilní řádky a zkopírovala pouze kompatibilní data. Podrobnosti naleznete v [tématu Kopírování tolerance poruchy aktivity.](copy-activity-fault-tolerance.md)

## <a name="next-steps"></a>Další kroky
Podívejte se na následující rychlé starty, kurzy a ukázky:

- [Kopírování dat z jednoho umístění do jiného umístění ve stejném účtu úložiště objektů blob Azure](quickstart-create-data-factory-dot-net.md)
- [Kopírování dat z úložiště objektů blob Azure do azure SQL databáze](tutorial-copy-data-dot-net.md)
- [Kopírování dat z místní databáze SERVERU SQL Server do Azure](tutorial-hybrid-copy-powershell.md)
