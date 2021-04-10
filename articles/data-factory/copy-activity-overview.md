---
title: Aktivita kopírování v Azure Data Factory
description: Přečtěte si o aktivitě kopírování v Azure Data Factory. Můžete ji použít ke kopírování dat z podporovaného zdrojového úložiště dat do podporovaného úložiště dat jímky.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: d52a0bba5fddaa865b8fad74b778ba7a3838b2a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387899"
---
# <a name="copy-activity-in-azure-data-factory"></a>Aktivita kopírování v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-data-movement-activities.md)
> * [Aktuální verze](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V Azure Data Factory můžete použít aktivitu kopírování ke kopírování dat mezi úložišti dat umístěnými místně a v cloudu. Po zkopírování dat můžete použít jiné aktivity k dalšímu transformaci a analýze. Aktivitu kopírování můžete použít také k publikování transformačních a analytických výsledků pro business intelligence (BI) a využití aplikací.

![Role aktivity kopírování](media/copy-activity-overview/copy-activity.png)

Aktivita kopírování se spustí v [prostředí Integration runtime](concepts-integration-runtime.md). Pro různé scénáře kopírování dat můžete použít různé typy prostředí Integration Runtime:

* Pokud kopírujete data mezi dvěma úložišti dat, která jsou veřejně přístupná prostřednictvím Internetu z jakékoli IP adresy, můžete pro aktivitu kopírování použít prostředí Azure Integration runtime. Tento modul runtime integrace je zabezpečený, spolehlivý, škálovatelný a [globálně dostupný](concepts-integration-runtime.md#integration-runtime-location).
* Pokud kopírujete data do a z úložišť dat umístěných místně nebo v síti s řízením přístupu (například virtuální sítí Azure), musíte nastavit prostředí Integration runtime v místním prostředí.

Prostředí Integration runtime musí být spojeno s každým zdrojem a úložištěm dat jímky. Informace o tom, jak aktivita kopírování určuje, který modul runtime integrace se má použít, najdete v tématu [určení toho, který IR se má použít](concepts-integration-runtime.md#determining-which-ir-to-use).

Chcete-li kopírovat data ze zdroje do jímky, služba, která spouští aktivitu kopírování, provede tyto kroky:

1. Načte data ze zdrojového úložiště dat.
2. Provádí serializaci/deserializaci, kompresi/dekompresi, mapování sloupců atd. Provede tyto operace na základě konfigurace vstupní datové sady, výstupní datové sady a aktivity kopírování.
3. Zapisuje data do jímky nebo cílového úložiště dat.

![Přehled aktivit kopírování](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Podporovaná úložiště a formáty dat

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Podporované formáty souborů

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Aktivitu kopírování můžete použít ke kopírování souborů mezi dvěma úložišti dat založených na souborech. v takovém případě se data zkopírují bez jakékoli serializace nebo deserializace. Kromě toho můžete také analyzovat nebo generovat soubory daného formátu, například můžete provést následující:

* Zkopírujte data z databáze SQL Server a zapište do Azure Data Lake Storage Gen2 ve formátu Parquet.
* Kopírování souborů ve formátu textu (CSV) z místního systému souborů a zápis do úložiště objektů BLOB v Azure ve formátu Avro
* Zkopírujte soubory zip z místního systému souborů, dekomprimujte je průběžně a zapište extrahované soubory do Azure Data Lake Storage Gen2.
* Z úložiště objektů BLOB v Azure zkopírujte data ve formátu. komprimovaný text (CSV) gzip a napište ho do Azure SQL Database.
* Mnoho dalších aktivit, které vyžadují serializaci/deserializaci nebo kompresi/dekompresi.

## <a name="supported-regions"></a>Podporované oblasti

Služba, která umožňuje aktivitu kopírování, je globálně dostupná v oblastech a zeměpisných oblastech uvedených v [umístěních prostředí Azure Integration runtime](concepts-integration-runtime.md#integration-runtime-location). Globálně dostupná topologie zajišťuje efektivní přesun dat, který obvykle brání směrování mezi oblastmi. Chcete-li zjistit dostupnost Data Factory a přesunu dat v konkrétní oblasti, zobrazte [produkty podle oblasti](https://azure.microsoft.com/regions/#services) .

## <a name="configuration"></a>Konfigurace

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Obecně platí, že pokud chcete použít aktivitu kopírování v Azure Data Factory, musíte:

1. **Vytvořte propojené služby pro zdrojové úložiště dat a úložiště dat jímky.** Seznam podporovaných konektorů najdete v části [podporované datové úložiště a formáty](#supported-data-stores-and-formats) v tomto článku. Informace o konfiguraci a podporovaných vlastnostech najdete v části "vlastnosti propojených služeb" v článku konektoru. 
2. **Vytvořte datové sady pro zdroj a jímku.** Informace o konfiguraci a podporovaných vlastnostech najdete v článcích "vlastnosti datové sady" v článcích konektor zdroje a jímky.
3. **Vytvořte kanál s aktivitou kopírování.** V další části najdete příklad.

### <a name="syntax"></a>Syntax

Následující šablona aktivity kopírování obsahuje úplný seznam podporovaných vlastností. Zadejte ty, které vyhovují vašemu scénáři.

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
| typ | U aktivity kopírování nastavte na `Copy` | Yes |
| vztahují | Určete datovou sadu, kterou jste vytvořili, která odkazuje na zdrojová data. Aktivita kopírování podporuje pouze jeden vstup. | Yes |
| činnosti | Určete datovou sadu, kterou jste vytvořili, která odkazuje na data jímky. Aktivita kopírování podporuje pouze jeden výstup. | Yes |
| typeProperties | Zadejte vlastnosti pro konfiguraci aktivity kopírování. | Yes |
| source | Zadejte typ zdroje kopie a odpovídající vlastnosti pro načtení dat.<br/>Další informace najdete v části "vlastnosti aktivity kopírování" v článku konektoru, který je uvedený v části [podporovaná úložiště a formáty dat](#supported-data-stores-and-formats). | Yes |
| jímkou | Zadejte typ jímky kopírování a odpovídající vlastnosti pro zápis dat.<br/>Další informace najdete v části "vlastnosti aktivity kopírování" v článku konektoru, který je uvedený v části [podporovaná úložiště a formáty dat](#supported-data-stores-and-formats). | Yes |
| Překladač | Zadejte explicitní mapování sloupců ze zdroje do jímky. Tato vlastnost se používá v případě, že výchozí chování kopírování nevyhovuje vašim potřebám.<br/>Další informace najdete v tématu [mapování schématu v aktivitě kopírování](copy-activity-schema-and-type-mapping.md). | No |
| dataIntegrationUnits | Zadejte míru, která představuje množství energie, kterou [prostředí Azure Integration runtime](concepts-integration-runtime.md) používá pro kopírování dat. Tyto jednotky se dřív jmenovaly jako jednotky pro pohyb dat v cloudu (DMU). <br/>Další informace najdete v tématu [jednotky pro integraci dat](copy-activity-performance-features.md#data-integration-units). | No |
| parallelCopies | Zadejte paralelismus, které má aktivita kopírování použít při čtení dat ze zdroje a zápisu dat do jímky.<br/>Další informace najdete v tématu [paralelní kopírování](copy-activity-performance-features.md#parallel-copy). | No |
| chovají | Určete, jestli se při kopírování dat mají zachovat metadata nebo seznamy ACL. <br/>Další informace najdete v tématu [zachování metadat](copy-activity-preserve-metadata.md). |No |
| enableStaging<br/>stagingSettings | Určete, jestli se mají připravit dočasná data v úložišti objektů BLOB místo přímého kopírování dat ze zdroje do jímky.<br/>Informace o užitečných scénářích a podrobnostech konfigurace najdete v tématu [Příprava kopírování](copy-activity-performance-features.md#staged-copy). | No |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Vyberte, jak se má při kopírování dat ze zdroje do jímky zpracovat nekompatibilní řádky.<br/>Další informace najdete v tématu odolnost [proti chybám](copy-activity-fault-tolerance.md). | No |

## <a name="monitoring"></a>Monitorování

Můžete monitorovat běh aktivity kopírování v Azure Data Factory vizuálně i programově. Podrobnosti najdete v tématu [monitorování aktivity kopírování](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Přírůstková kopie

Data Factory umožňuje přírůstkově kopírovat rozdílová data ze zdrojového úložiště dat do úložiště dat jímky. Podrobnosti najdete v tématu [kurz: přírůstkové kopírování dat](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Výkon a ladění

Prostředí pro [monitorování aktivit kopírování](copy-activity-monitoring.md) vám ukáže statistiku o výkonu kopírování pro každé spuštění aktivity. [Průvodce výkonem a škálovatelností aktivity kopírování](copy-activity-performance.md) popisuje klíčové faktory, které ovlivňují výkon přesunu dat prostřednictvím aktivity kopírování v Azure Data Factory. Také uvádí hodnoty výkonu zjištěné během testování a popisuje, jak optimalizovat výkon aktivity kopírování.

## <a name="resume-from-last-failed-run"></a>Obnovit z posledního neúspěšného spuštění

Aktivita kopírování podporuje obnovení z posledního neúspěšného spuštění, když kopírujete velké velikosti souborů tak, aby byly v binárním formátu mezi úložišti založenými na souborech a zvolili, že se má zachovat hierarchie složky/souboru ze zdroje do jímky, třeba migrace dat ze služby Amazon S3 do Azure Data Lake Storage Gen2. Platí pro následující konektory založené na souborech: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)a [SFTP](connector-sftp.md).

Obnovení aktivity kopírováním můžete využít následujícími dvěma způsoby:

- **Opakování na úrovni aktivity:** U aktivity kopírování můžete nastavit počet opakování. Pokud při spuštění kanálu dojde k selhání této aktivity kopírování, bude příští automatické opakování znovu spuštěno z bodu selhání poslední zkušební verze.
- **Znovu spustit z neúspěšné aktivity:** Po dokončení spuštění kanálu můžete také aktivovat opětovné spuštění z neúspěšné aktivity v zobrazení monitorování uživatelského rozhraní ADF nebo programově. Pokud je neúspěšná aktivita aktivita kopírování, kanál nebude znovu spouštět z této aktivity, ale také obnoví z bodu selhání předchozího běhu.

    ![Kopírovat pokračování](media/copy-activity-overview/resume-copy.png)

Pár bodů k poznámení:

- K pokračování dochází na úrovni souboru. Pokud aktivita kopírování selže při kopírování souboru, bude tento konkrétní soubor znovu zkopírován.
- Aby obnovení fungovalo správně, neměňte nastavení aktivity kopírování mezi znovu spuštěnými.
- Když kopírujete data ze služby Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 a Google Cloud Storage, aktivita kopírování může pokračovat z libovolného počtu kopírovaných souborů. V případě, že ostatní konektory založené na souborech jako zdroj, v současné době kopírování podporuje obnovení z omezeného počtu souborů, obvykle v rozsahu desítkových tisíc a liší se v závislosti na délce cest souborů. soubory přesahující tento počet se během opakovaného spuštění znovu zkopírují.

Pro jiné scénáře než kopírování binárních souborů začíná opětovné spuštění aktivity kopírování od začátku.

## <a name="preserve-metadata-along-with-data"></a>Zachování metadat spolu s daty

Při kopírování dat ze zdroje do jímky ve scénářích, jako je třeba migrace Data Lake, můžete také zvolit, aby se metadata a seznamy ACL zachovaly spolu s daty pomocí aktivity kopírování. Podrobnosti najdete v tématu [zachování metadat](copy-activity-preserve-metadata.md) .

## <a name="schema-and-data-type-mapping"></a>Mapování schématu a datového typu

Informace o tom, jak aktivita kopírování mapuje vaše zdrojová data do jímky, najdete v tématu [mapování schématu a datového typu](copy-activity-schema-and-type-mapping.md) .

## <a name="add-additional-columns-during-copy"></a>Přidat další sloupce během kopírování

Kromě kopírování dat ze zdrojového úložiště dat do jímky můžete také nakonfigurovat a přidat další datové sloupce pro kopírování do jímky. Například:

- Při kopírování ze zdroje založeného na souborech uložte relativní cestu k souboru jako další sloupec, ze kterého se data pocházejí.
- Duplikuje zadaný zdrojový sloupec jako jiný sloupec. 
- Přidejte sloupec s výrazem ADF pro připojení systémových proměnných ADF, jako je název kanálu nebo ID kanálu, nebo uložte jinou dynamickou hodnotu z výstupu nadřazeného aktivity.
- Přidejte sloupec se statickou hodnotou, který bude vyhovovat potřebě pro vydanou spotřebu.

Na kartě Zdroj aktivity kopírování můžete najít následující konfiguraci. Tyto další sloupce v [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping) kopírování aktivity můžete také namapovat obvyklým způsobem pomocí definovaných názvů sloupců. 

![Přidání dalších sloupců v aktivitě kopírování](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Tato funkce funguje s nejnovějším modelem DataSet. Pokud tuto možnost nevidíte v uživatelském rozhraní, zkuste vytvořit novou datovou sadu.

Pokud ho chcete nakonfigurovat programově, přidejte `additionalColumns` do zdroje aktivity kopírování vlastnost:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| additionalColumns | Přidejte další datové sloupce ke zkopírování do jímky.<br><br>Každý objekt v poli `additionalColumns` představuje sloupec navíc. `name`Definuje název sloupce a `value` Určuje hodnotu dat daného sloupce.<br><br>Povolené hodnoty dat jsou:<br>- **`$$FILEPATH`** – Rezervovaná proměnná Určuje relativní cestu zdrojových souborů k cestě ke složce zadané v datové sadě. Platí pro zdroj založený na souborech.<br>- **`$$COLUMN:<source_column_name>`** – vyhrazený vzorec proměnné určuje duplikování zadaného zdrojového sloupce jako jiného sloupce.<br>- **Vyjádření**<br>- **Statická hodnota** | No |

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
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
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

## <a name="auto-create-sink-tables"></a>Automaticky vytvářet tabulky jímky

Když kopírujete data do služby SQL Database/Azure synapse Analytics, pokud cílová tabulka neexistuje, aktivita kopírování podporuje automatické vytvoření na základě zdrojových dat. Díky tomu je možné rychle začít načítat data a vyhodnocovat SQL Database nebo Azure synapse Analytics. Po přijetí dat můžete zkontrolovat a upravit schéma tabulky jímky podle vašich potřeb.

Tato funkce se podporuje při kopírování dat z libovolného zdroje do následujících úložišť dat jímky. Můžete najít možnost v *uživatelském rozhraní pro vytváření ADF* – >á *jímka aktivity kopírování* – > *možnost Tabulka* – > *Automatické vytvoření tabulky* nebo prostřednictvím `tableOption` vlastnosti v datové části jímky aktivity kopírování.

- [Azure SQL Database](connector-azure-sql-database.md)
- [Azure SQL Database spravovaná instance](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![Vytvoření tabulek jímky](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>Odolnost proti chybám

Ve výchozím nastavení aktivita kopírování zastaví kopírování dat a vrátí chybu, pokud jsou řádky zdrojového data nekompatibilní s řádky dat jímky. Aby bylo kopírování úspěšné, můžete nakonfigurovat aktivitu kopírování, která přeskočí a zaprotokoluje nekompatibilní řádky a kopíruje pouze kompatibilní data. Podrobnosti najdete v tématu odolnost [proti chybám aktivity kopírování](copy-activity-fault-tolerance.md) .

## <a name="data-consistency-verification"></a>Ověření konzistence dat

Když přesunete data ze zdrojového do cílového úložiště, Azure Data Factory aktivita kopírování poskytuje možnost provést další ověření konzistence dat, aby se zajistilo, že data nebudou úspěšně zkopírována ze zdroje do cílového úložiště, ale také ověřena tak, aby byla konzistentní mezi zdrojovým a cílovým úložištěm. Po nalezení nekonzistentních souborů během přesunu dat můžete buď přerušit aktivitu kopírování, nebo pokračovat ve kopírování zbývajících souborů tak, že povolíte nastavení odolnosti proti chybám, aby se přeskočily nekonzistentní soubory. Vynechané názvy souborů můžete získat povolením nastavení protokol relace v aktivitě kopírování. Podrobnosti najdete [v tématu ověření konzistence dat v aktivitě kopírování](copy-activity-data-consistency.md) .

## <a name="session-log"></a>Protokol relace
Můžete protokolovat názvy kopírovaných souborů, které vám pomůžou zajistit, aby data nebyla úspěšně zkopírována ze zdroje do cílového úložiště, ale také konzistentní mezi zdrojovým a cílovým úložištěm, a to kontrolou protokolů relace aktivity kopírování. Podrobnosti najdete [v tématu Protokol relace v aktivitě kopírování](copy-activity-log.md) .

## <a name="next-steps"></a>Další kroky
Podívejte se na následující rychlé starty, kurzy a ukázky:

- [Kopírování dat z jednoho umístění do jiného umístění ve stejném účtu úložiště Azure Blob](quickstart-create-data-factory-dot-net.md)
- [Kopírování dat z úložiště objektů BLOB v Azure do Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopírování dat z databáze SQL Server do Azure](tutorial-hybrid-copy-powershell.md)
