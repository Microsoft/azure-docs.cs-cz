---
title: Aktivita kopírování v Azure Data Factory
description: Přečtěte si o aktivitě kopírování v Azure Data Factory. Můžete ji použít ke kopírování dat z podporovaného zdrojového úložiště dat do podporovaného úložiště dat jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: jingwang
ms.openlocfilehash: 0e138e954501df3cf3c3c8819d0198ad9a9288f0
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754459"
---
# <a name="copy-activity-in-azure-data-factory"></a>Aktivita kopírování v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-data-movement-activities.md)
> * [Aktuální verze](copy-activity-overview.md)

V Azure Data Factory můžete použít aktivitu kopírování ke kopírování dat mezi úložišti dat umístěnými místně a v cloudu. Po zkopírování dat můžete použít jiné aktivity k dalšímu transformaci a analýze. Aktivitu kopírování můžete použít také k publikování transformačních a analytických výsledků pro business intelligence (BI) a využití aplikací.

![Role aktivity kopírování](media/copy-activity-overview/copy-activity.png)

Aktivita kopírování se spustí v [prostředí Integration runtime](concepts-integration-runtime.md). Pro různé scénáře kopírování dat můžete použít různé typy prostředí Integration Runtime:

* Pokud kopírujete data mezi dvěma úložišti dat, která jsou veřejně přístupná prostřednictvím Internetu z jakékoli IP adresy, můžete pro aktivitu kopírování použít prostředí Azure Integration runtime. Tento modul runtime integrace je zabezpečený, spolehlivý, škálovatelný a [globálně dostupný](concepts-integration-runtime.md#integration-runtime-location).
* Pokud kopírujete data do a z úložišť dat umístěných místně nebo v síti s řízením přístupu (například virtuální sítí Azure), musíte nastavit prostředí Integration runtime v místním prostředí.

Prostředí Integration runtime musí být spojeno s každým zdrojem a úložištěm dat jímky. Informace o tom, jak aktivita kopírování určuje, který modul runtime integrace se má použít, najdete v tématu [určení toho, který IR se má použít](concepts-integration-runtime.md#determining-which-ir-to-use).

Chcete-li kopírovat data ze zdroje do jímky, služba, která spouští aktivitu kopírování, provede tyto kroky:

1. Přečte data ze zdrojového úložiště dat.
2. Provádí serializaci/deserializaci, kompresi/dekompresi, mapování sloupců atd. Provede tyto operace na základě konfigurace vstupní datové sady, výstupní datové sady a aktivity kopírování.
3. Zapíše data do úložiště dat jímky/cíl.

![Přehled aktivit kopírování](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Podporované zdroje dat a formáty

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Podporované formáty souborů

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Aktivitu kopírování můžete použít ke kopírování souborů mezi dvěma úložišti dat založených na souborech. v takovém případě se data zkopírují bez jakékoli serializace nebo deserializace. Kromě toho můžete také analyzovat nebo generovat soubory daného formátu, například můžete provést následující:

* Kopírování dat z místní databáze SQL Server a zápis do Azure Data Lake Storage Gen2 ve formátu Parquet
* Kopírování souborů ve formátu textu (CSV) z místního systému souborů a zápis do úložiště objektů BLOB v Azure ve formátu Avro
* Zkopírujte soubory zip z místního systému souborů, dekomprimujte je průběžně a zapište extrahované soubory do Azure Data Lake Storage Gen2.
* Z úložiště objektů BLOB v Azure zkopírujte data ve formátu. komprimovaný text (CSV) gzip a napište ho do Azure SQL Database.
* Mnoho dalších aktivit, které vyžadují serializaci/deserializaci nebo kompresi/dekompresi.

## <a name="supported-regions"></a>Podporované oblasti

Služba, která umožňuje aktivitu kopírování, je globálně dostupná v oblastech a zeměpisných oblastech uvedených v [umístěních prostředí Azure Integration runtime](concepts-integration-runtime.md#integration-runtime-location). Globálně dostupné topologie zajišťuje přesun efektivní dat, které se obvykle vyhýbají segmentů směrování mezi oblastmi. Chcete-li zjistit dostupnost Data Factory a přesunu dat v konkrétní oblasti, zobrazte [produkty podle oblasti](https://azure.microsoft.com/regions/#services) .

## <a name="configuration"></a>Konfigurace

Chcete-li použít aktivitu kopírování v Azure Data Factory, je nutné:

1. **Vytvořte propojené služby pro zdrojové úložiště dat a úložiště dat jímky.** Informace o konfiguraci a podporovaných vlastnostech najdete v části "vlastnosti propojených služeb" v článku konektoru. Seznam podporovaných konektorů najdete v části [podporované datové úložiště a formáty](#supported-data-stores-and-formats) v tomto článku.
2. **Vytvořte datové sady pro zdroj a jímku.** Informace o konfiguraci a podporovaných vlastnostech najdete v článcích "vlastnosti datové sady" v článcích konektor zdroje a jímky.
3. **Vytvořte kanál s aktivitou kopírování.** V další části najdete příklad.

### <a name="syntax"></a>Syntaxe

Následující šablona aktivity kopírování obsahuje úplný seznam podporovaných vlastností. Zadejte značky, které vyhovují vašemu scénáři.

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

#### <a name="syntax-details"></a>Podrobnosti o syntaxi

| Vlastnost | Popis | Povinné? |
|:--- |:--- |:--- |
| type | U aktivity kopírování nastavte na `Copy` | Ano |
| inputs | Určete datovou sadu, kterou jste vytvořili, která odkazuje na zdrojová data. Aktivita kopírování podporuje pouze jeden vstup. | Ano |
| outputs | Určete datovou sadu, kterou jste vytvořili, která odkazuje na data jímky. Aktivita kopírování podporuje pouze jeden výstup. | Ano |
| typeProperties | Zadejte vlastnosti pro konfiguraci aktivity kopírování. | Ano |
| source | Zadejte typ zdroje kopie a odpovídající vlastnosti pro načtení dat.<br/>Další informace najdete v části "vlastnosti aktivity kopírování" v článku konektoru, který je uvedený v části [podporovaná úložiště a formáty dat](#supported-data-stores-and-formats). | Ano |
| sink | Zadejte typ jímky kopírování a odpovídající vlastnosti pro zápis dat.<br/>Další informace najdete v části "vlastnosti aktivity kopírování" v článku konektoru, který je uvedený v části [podporovaná úložiště a formáty dat](#supported-data-stores-and-formats). | Ano |
| translator | Zadejte mapování sloupce explicitní ze zdroje do jímky. Tato vlastnost se používá v případě, že výchozí chování kopírování nevyhovuje vašim potřebám.<br/>Další informace najdete v tématu [mapování schématu v aktivitě kopírování](copy-activity-schema-and-type-mapping.md). | Ne |
| dataIntegrationUnits | Zadejte míru, která představuje množství energie, kterou [prostředí Azure Integration runtime](concepts-integration-runtime.md) používá pro kopírování dat. Tyto jednotky se dřív jmenovaly jako jednotky pro pohyb dat v cloudu (DMU). <br/>Další informace najdete v tématu [jednotky pro integraci dat](copy-activity-performance.md#data-integration-units). | Ne |
| parallelCopies | Zadejte paralelismus, které má aktivita kopírování použít při čtení dat ze zdroje a zápisu dat do jímky.<br/>Další informace najdete v tématu [paralelní kopírování](copy-activity-performance.md#parallel-copy). | Ne |
| zachovat | Určete, jestli se při kopírování dat mají zachovat metadata nebo seznamy ACL. <br/>Další informace najdete v tématu [zachování metadat](copy-activity-preserve-metadata.md). |Ne |
| enableStaging<br/>stagingSettings | Určete, jestli se mají připravit dočasná data v úložišti objektů BLOB místo přímého kopírování dat ze zdroje do jímky.<br/>Informace o užitečných scénářích a podrobnostech konfigurace najdete v tématu [Příprava kopírování](copy-activity-performance.md#staged-copy). | Ne |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Vyberte, jak se má při kopírování dat ze zdroje do jímky zpracovat nekompatibilní řádky.<br/>Další informace najdete v tématu odolnost [proti chybám](copy-activity-fault-tolerance.md). | Ne |

## <a name="monitoring"></a>Sledování

Běh aktivity kopírování můžete monitorovat v uživatelském rozhraní Azure Data Factory **Author & monitorovat** nebo programově.

### <a name="monitor-visually"></a>Vizuální monitorování

Pokud chcete vizuálně monitorovat běh aktivity kopírování, klikněte na objekt pro vytváření dat a pak na možnost **autor & monitorování**. Na kartě **monitorování** se zobrazí seznam spuštění kanálu s tlačítkem **Zobrazit spuštění aktivity** ve sloupci **Akce** :

![Monitorování spuštění kanálu](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Výběrem možnosti **Zobrazit spuštění aktivit** zobrazíte seznam aktivit v běhu kanálu. Ve sloupci **Actions (akce** ) uvidíte odkazy na vstup aktivity kopírování, výstup, chyby (Pokud se aktivita kopírování nezdařila) a podrobnosti:

![Monitorování spuštění aktivit](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Výběrem tlačítka **Details (podrobnosti** ) ve sloupci **Akce** zobrazíte podrobnosti o spuštění aktivity kopírování a výkonnostní charakteristiky. Zobrazí se informace, jako je objem/počet řádků nebo počet souborů dat zkopírovaných ze zdroje do jímky, propustnosti, kroky, které aktivita kopírování prochází s odpovídajícími dobami trvání a konfigurace použitá pro váš scénář kopírování.

>[!TIP]
>V některých scénářích se v horní části stránky pro monitorování kopírování zobrazí také **tipy pro ladění výkonu** . Tyto tipy vám pomůžou identifikovat kritická místa a poskytovat informace o tom, co se má pro zvýšení propustnosti kopírování. Příklad najdete v části [výkon a optimalizace](#performance-and-tuning) tohoto článku.

**Příklad: zkopírování ze služby Amazon S3 do Azure Data Lake Store**
![podrobnosti o spuštění sledování aktivit](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Příklad: zkopírování z Azure SQL Database do Azure SQL Data Warehouse pomocí dvoufázové kopie**
![podrobnosti o spuštění sledování aktivit](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorování prostřednictvím kódu programu

V části **výsledek spuštění aktivity kopírování** > **výstup** se vrátí taky podrobnosti o spuštění aktivity kopírování a výkonnostní charakteristiky. Následuje úplný seznam vlastností, které mohou být vráceny. Uvidíte jenom vlastnosti, které se vztahují k vašemu scénáři kopírování. Informace o tom, jak monitorovat spuštění aktivit, najdete v tématu [monitorování spuštění kanálu](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Název vlastnosti  | Popis | Jednotka |
|:--- |:--- |:--- |
| dataRead | Objem dat načtených ze zdroje. | Hodnota Int64, v bajtech |
| dataWritten | Množství dat zapsaných do jímky. | Hodnota Int64, v bajtech |
| filesRead | Počet souborů zkopírovaných během kopírování z úložiště souborů | Hodnota Int64 (žádná jednotka) |
| filesWritten | Počet souborů zkopírovaných během kopírování do úložiště souborů | Hodnota Int64 (žádná jednotka) |
| sourcePeakConnections | Nejvyšší počet souběžných připojení navázaných ke zdrojovému úložišti dat během spuštění aktivity kopírování. | Hodnota Int64 (žádná jednotka) |
| sinkPeakConnections | Nejvyšší počet souběžných připojení navázaných na úložiště dat jímky během spuštění aktivity kopírování. | Hodnota Int64 (žádná jednotka) |
| rowsRead | Počet načtených řádků ze zdroje (nelze použít pro binární kopii). | Hodnota Int64 (žádná jednotka) |
| rowsCopied | Počet řádků zkopírovaných do jímky (neplatí pro binární kopii) | Hodnota Int64 (žádná jednotka) |
| rowsSkipped | Počet vynechaných nekompatibilních řádků. Nastavením `enableSkipIncompatibleRow` na hodnotu true můžete povolit přeskočení nekompatibilních řádků. | Hodnota Int64 (žádná jednotka) |
| copyDuration | Doba trvání spuštění kopírování | Hodnota Int32 v sekundách |
| Propustnost | Rychlost přenosu dat | Číslo s plovoucí desetinnou čárkou, v KB/s |
| sourcePeakConnections | Nejvyšší počet souběžných připojení navázaných ke zdrojovému úložišti dat během spuštění aktivity kopírování. | Hodnota Int32 (žádná jednotka) |
| sinkPeakConnections| Nejvyšší počet souběžných připojení navázaných na úložiště dat jímky během spuštění aktivity kopírování.| Hodnota Int32 (žádná jednotka) |
| sqlDwPolyBase | Určuje, zda se používá základna při kopírování dat do SQL Data Warehouse. | Logická hodnota |
| redshiftUnload | Určuje, zda je při kopírování dat z RedShift použito uvolnění. | Logická hodnota |
| hdfsDistcp | Určuje, zda se při kopírování dat ze HDFS používá DistCp. | Logická hodnota |
| effectiveIntegrationRuntime | Prostředí Integration runtime (IR) nebo moduly runtime používané pro spuštění aktivity ve formátu `<IR name> (<region if it's Azure IR>)`. | Text (řetězec) |
| usedDataIntegrationUnits | Efektivní jednotky integrace dat během kopírování. | Hodnota Int32 |
| usedParallelCopies | Efektivní parallelCopies během kopírování. | Hodnota Int32 |
| redirectRowPath | Cesta k protokolu vynechaných nekompatibilních řádků v úložišti objektů blob, které nakonfigurujete ve vlastnosti `redirectIncompatibleRowSettings`. Podívejte se na [odolnost proti chybám](#fault-tolerance) později v tomto článku. | Text (řetězec) |
| executionDetails | Další podrobnosti o fázích, které aktivita kopírování prochází, a o příslušných krocích, trváních, konfiguracích a tak dále. Nedoporučujeme tuto část analyzovat, protože by se mohla změnit.<br/><br/>Data Factory také hlásí podrobné doby trvání (v sekundách) strávené v různých fázích `detailedDurations`. Doba trvání těchto kroků je exkluzivní. Zobrazí se pouze doby trvání, které se vztahují k dané spuštěné aktivitě kopírování:<br/>**Doba** zařazení do fronty (`queuingDuration`): doba před tím, než se aktivita kopírování ve skutečnosti spustí v prostředí Integration runtime. Pokud používáte prostředí IR v místním prostředí a tato hodnota je velká, podívejte se na kapacitu a využití IR a nahorizontální navýšení nebo zmenšení kapacity podle vašich úloh. <br/>**Doba trvání skriptu před kopírováním** (`preCopyScriptDuration`): uplynulý čas mezi tím, kdy se aktivita kopírování SPUSTÍ na IR a když aktivita kopírování dokončí běh skriptu před kopírováním v úložišti dat jímky. Platí při konfiguraci skriptu před kopírováním. <br/>**Čas do prvního bajtu** (`timeToFirstByte`): doba uplynulá mezi koncem předchozího kroku a čas, kdy infračervený přenos obdrží první bajt ze zdrojového úložiště dat. Platí pro zdroje nezaložené na souborech. Pokud je tato hodnota velká, ověřte a optimalizujte dotaz nebo server.<br/>**Doba trvání přenosu** (`transferDuration`): uplynulý čas mezi koncem předchozího kroku a časem, kdy infračervený přenos všechna data ze zdroje do jímky. | Pole |
| perfRecommendation | Kopírování tipů pro ladění výkonu. Podrobnosti najdete v článku o [výkonu a ladění](#performance-and-tuning) . | Pole |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="incremental-copy"></a>Přírůstkové kopírování

Data Factory umožňuje přírůstkově kopírovat rozdílová data ze zdrojového úložiště dat do úložiště dat jímky. Podrobnosti najdete v tématu [kurz: přírůstkové kopírování dat](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Výkon a ladění

[Průvodce výkonem a škálovatelností aktivity kopírování](copy-activity-performance.md) popisuje klíčové faktory, které ovlivňují výkon přesunu dat prostřednictvím aktivity kopírování v Azure Data Factory. Také uvádí hodnoty výkonu zjištěné během testování a popisuje, jak optimalizovat výkon aktivity kopírování.

V některých scénářích při spuštění aktivity kopírování v Data Factory uvidíte **tipy pro ladění výkonu** v horní části [stránky monitorování aktivity kopírování](#monitor-visually), jak je znázorněno v následujícím příkladu. V tipech se dozvíte, že u daného spuštění kopírování bylo zjištěno kritické místo. Poskytují taky informace o tom, co se má změnit, aby se zvýšila propustnost kopírování. Tipy pro ladění výkonu aktuálně poskytují návrhy, jako je například použití základny při kopírování dat do Azure SQL Data Warehouse, zvýšení Azure Cosmos DB ru nebo Azure SQL Database DTU, když je prostředek na straně úložiště k dispozici jako kritický bod a odebírá se. zbytečné připravené kopie.

**Příklad: kopírování do Azure SQL Database s tipem pro ladění výkonu**

V této ukázce Data Factory v průběhu kopírování sledovat vysoké využití DTU v Azure SQL Database jímky. Tento stav zpomaluje operace zápisu. Návrh je zvýšit DTU na úrovni Azure SQL Database:

![Sledování kopírování pomocí tipů pro ladění výkonu](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

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

## <a name="schema-and-data-type-mapping"></a>Schéma a mapování datového typu

Informace o tom, jak aktivita kopírování mapuje vaše zdrojová data do jímky, najdete v tématu [mapování schématu a datového typu](copy-activity-schema-and-type-mapping.md) .

## <a name="fault-tolerance"></a>Odolnost proti chybám

Ve výchozím nastavení aktivita kopírování zastaví kopírování dat a vrátí chybu, pokud jsou řádky zdrojového data nekompatibilní s řádky dat jímky. Aby bylo kopírování úspěšné, můžete nakonfigurovat aktivitu kopírování, která přeskočí a zaprotokoluje nekompatibilní řádky a kopíruje pouze kompatibilní data. Podrobnosti najdete v tématu odolnost [proti chybám aktivity kopírování](copy-activity-fault-tolerance.md) .

## <a name="next-steps"></a>Další kroky
Tyto rychlé starty, kurzy a ukázky, naleznete v tématu:

- [Kopírování dat z jednoho umístění do jiného umístění ve stejném účtu úložiště Azure Blob](quickstart-create-data-factory-dot-net.md)
- [Kopírování dat z úložiště objektů BLOB v Azure do Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopírování dat z místní databáze SQL Server do Azure](tutorial-hybrid-copy-powershell.md)
