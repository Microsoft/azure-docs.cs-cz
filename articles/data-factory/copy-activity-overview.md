---
title: Aktivita kopírování ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o aktivitě kopírování ve službě Azure Data Factory, můžete použít ke zkopírování dat z podporovaných zdrojů úložišť dat do úložiště dat jímky podporované.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: jingwang
ms.openlocfilehash: 154e0dcefab6d5bcdfc9532ba4258d09593f0970
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311126"
---
# <a name="copy-activity-in-azure-data-factory"></a>Aktivita kopírování ve službě Azure Data Factory

## <a name="overview"></a>Přehled

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-data-movement-activities.md)
> * [Aktuální verze](copy-activity-overview.md)

Ve službě Azure Data Factory můžete použít aktivitu kopírování ke kopírování dat mezi datového úložiště v místním prostředí i v cloudu. Po zkopírování dat, může být dále transformují a analyzují. Aktivitu kopírování, která můžete použít také k publikování, transformace a analýza výsledků pro business intelligence (BI) a využití aplikace.

![Pro dané aktivity kopírování](media/copy-activity-overview/copy-activity.png)

Aktivita kopírování se provádí na [prostředí Integration Runtime](concepts-integration-runtime.md). Různé datové kopie scénáři můžete využít různé flavor prostředí Integration Runtime:

* Při kopírování dat mezi daty ukládá, obě jsou veřejně dostupné, aktivita kopírování může zdokonalujte své schopnosti podle **prostředí Azure Integration Runtime**, což je zabezpečená, spolehlivá a škálovatelná, a [globálně dostupné](concepts-integration-runtime.md#integration-runtime-location).
* Při kopírování dat z/do úložiště dat umístěné v místním nebo v síti pomocí řízení přístupu (například Azure Virtual Network), budete muset nastavit **integrované modulu Runtime v místním prostředí** pro kopírování dat.

Prostředí Integration Runtime musí být spojen s každou úložiště dat zdroje a jímky. Další podrobnosti o aktivitě kopírování [Určuje, které prostředí IR používat](concepts-integration-runtime.md#determining-which-ir-to-use).

Aktivitu kopírování, která prochází následující fáze pro kopírování dat ze zdroje do jímky. Služba, kterou využívá aktivitu kopírování:

1. Přečte data ze zdrojového úložiště dat.
2. Provede serializaci/deserializaci komprese nebo dekomprese, mapování sloupců, atd. Provádí tyto operace konfigurace vstupní datové sady, výstupní datovou sadu a aktivitu kopírování.
3. Zapíše data do úložiště dat jímky/cíl.

![Přehled aktivit kopírování](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Podporované zdroje dat a formáty

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Podporované formáty souborů

Můžete použít aktivitu kopírování, která **kopírovat soubory jako-je** mezi dvěma souborových dat úložišti, ve kterých je efektivně zkopírují případ data bez serializaci nebo deserializaci.

Aktivita kopírování také podporuje čtení a zápis do souborů v určených formátů: **Text JSON, Avro, ORC a Parquet**a kompresní kodek **GZip, Deflate, BZip2 a ZipDeflate** jsou podporovány. Zobrazit [podporované formáty souborů a komprese](supported-file-formats-and-compression-codecs.md) s podrobnostmi.

Například můžete provést následující aktivity kopírování:

* Kopírování dat v místním SQL serveru a ve formátu ORC zápis do Azure Data Lake Store.
* Zkopírujte soubory ve formátu textu (CSV) v místním systému souborů a zápis do objektu Blob Azure ve formátu Avro.
* Kopírování souborů ZIP v místním systému souborů a pak dekomprimovat pozemního do Azure Data Lake Store.
* Kopírování dat z objektů Blob v Azure ve formátu GZip komprimované text (CSV) a zápis do služby Azure SQL Database.

## <a name="supported-regions"></a>Podporované oblasti

Služba, která využívá aktivitu kopírování, která je dostupná globálně v oblastech a zeměpisných oblastech uvedených v [umístění prostředí Azure Integration Runtime](concepts-integration-runtime.md#integration-runtime-location). Globálně dostupné topologie zajišťuje přesun efektivní dat, které se obvykle vyhýbají segmentů směrování mezi oblastmi. Zobrazit [služby v jednotlivých oblastech](https://azure.microsoft.com/regions/#services) dostupnosti služby Data Factory a přesun dat v oblasti.

## <a name="configuration"></a>Konfigurace

Chcete-li použít aktivitu kopírování, která ve službě Azure Data Factory, budete muset:

1. **Vytvoření propojených služeb pro zdrojové úložiště dat a úložiště dat jímky.** Najdete v části "Vlastnostem propojených služeb" konektor článek o tom, jak nakonfigurovat a podporovaných vlastností. Můžete najít v seznamu podporovaných konektor v [podporovaných úložišť dat a formáty](#supported-data-stores-and-formats) oddílu.
2. **Vytvoření datové sady pro zdroje a jímky.** Podívejte se do zdroje a jímky části "Vlastnosti datové sady" konektor články o tom, jak nakonfigurovat a podporovaných vlastností.
3. **Vytvoření kanálu s aktivitou kopírování.** V další části najdete příklad.  

### <a name="syntax"></a>Syntaxe

Následující šablony aktivitu kopírování, která obsahuje úplný seznam podporovaných vlastností. Zadejte značky, které vyhovují vašemu scénáři.

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

### <a name="syntax-details"></a>Podrobnosti o syntaxi

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type aktivitu kopírování, která musí být nastavena: **kopírování** | Ano |
| vstupy | Zadejte datovou sadu vytvoříte, která odkazuje na zdroj dat. Aktivitu kopírování, která podporuje pouze jeden vstup. | Ano |
| výstupy | Zadejte datovou sadu vytvoříte, které body k datům jímky. Aktivitu kopírování, která podporuje pouze jeden výstup. | Ano |
| typeProperties | Skupina vlastností konfigurace aktivity kopírování. | Ano |
| source | Zadejte typ zdroje kopie a odpovídající vlastnosti o tom, jak načíst data.<br/><br/>Přečtěte si podrobnosti v článku konektor uvedené v části "Vlastnosti aktivity kopírování" [podporovaných úložišť dat a formáty](#supported-data-stores-and-formats). | Ano |
| jímka | Zadejte typ jímky kopírování a odpovídajících vlastností o tom, jak zapsat data.<br/><br/>Přečtěte si podrobnosti v článku konektor uvedené v části "Vlastnosti aktivity kopírování" [podporovaných úložišť dat a formáty](#supported-data-stores-and-formats). | Ano |
| Translator | Zadejte mapování sloupce explicitní ze zdroje do jímky. Platí při kopírování chování nejde splnit vaše potřeby.<br/><br/>Další podrobnosti o [schéma a data mapování typu](copy-activity-schema-and-type-mapping.md). | Ne |
| dataIntegrationUnits | Zadejte powerfulness z [prostředí Azure Integration Runtime](concepts-integration-runtime.md) pro kopírování dat. Dříve označované jako cloudové jednotky přesunu dat (DMU). <br/><br/>Další podrobnosti o [jednotky integrace dat](copy-activity-performance.md#data-integration-units). | Ne |
| parallelCopies | Zadejte paralelismu, který chcete použít při čtení dat ze zdroje a zápis dat do jímky aktivity kopírování.<br/><br/>Další podrobnosti o [paralelní kopírování](copy-activity-performance.md#parallel-copy). | Ne |
| enableStaging<br/>stagingSettings | Zvolte připraví pomocný data v úložišti objektů blob aa místo přímo kopírování dat ze zdroje do jímky.<br/><br/>Další užitečné scénáře a podrobnosti o konfiguraci z [fázovaného kopírování](copy-activity-performance.md#staged-copy). | Ne |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Zvolte způsob zpracování nekompatibilních řádků při kopírování dat ze zdroje do jímky.<br/><br/>Další podrobnosti o [odolnost proti chybám](copy-activity-fault-tolerance.md). | Ne |

## <a name="monitoring"></a>Monitorování

Můžete monitorovat spuštění v Azure Data Factory "Vytvořit a monitorovat" uživatelské rozhraní nebo programově aktivity kopírování. Pak můžete porovnat výkon a konfigurace vašeho scénáře aktivitou kopírování [výkonu](copy-activity-performance.md#performance-reference) z interní testování.

### <a name="monitor-visually"></a>Vizuální monitorování

Chcete-li vizuálně sledovat spuštění aktivity kopírování, přejděte do služby data factory -> **vytvořit a monitorovat** -> **kartu monitorování**, uvidíte seznam kanálu spouští pomocí odkazu v "Zobrazitspuštěníaktivit" **Akce** sloupce. 

![Monitorování spuštění kanálu](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Kliknutím zobrazíte seznam aktivit v tomto běhu kanálu. V **akce** sloupce, máte odkazy na vstup aktivity kopírování, výstup, chyby (Pokud se nezdaří spuštění aktivity kopírování) a podrobnosti.

![Monitorování spuštění aktivit](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Klikněte "**podrobnosti**" propojit v rámci **akce** zobrazíte podrobnosti o spuštění aktivity kopírování a výkonové charakteristiky. To se dozvíte informace, včetně svazek/řádků/soubory dat zkopíruje ze zdroje do jímky, propustnost, kroky prochází s určitou dobu a použít konfigurace pro váš scénář kopírování. 

>[!TIP]
>Pro některé scénáře, zobrazí se také "**tipy pro optimalizaci výkonu**" nad kopírování monitorování stránky, které sděluje, identifikovat kritická místa a provede vás o tom, jak změnit tak, aby zvýšení propustnosti kopie, naleznete v části Příklad s podrobnostmi [tady](#performance-and-tuning).

**Příklad: kopírování z Amazon S3 do Azure Data Lake Store**
![podrobnosti o spuštění aktivit monitorování](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Příklad: kopírování ze služby Azure SQL Database do Azure SQL Data Warehouse pomocí fázovaného kopírování**
![podrobnosti o spuštění aktivit monitorování](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorování prostřednictvím kódu programu

Podrobnosti provádění aktivity kopírování a výkonové charakteristiky se vrátí taky v aktivitě kopírování výsledek spuštění -> výstupní sekce. Níže je úplný seznam; se zobrazí pouze ty použitelné pro váš scénář kopírování. Zjistěte, jak sledovat činnost spustit z [části sledování pomocí rychlého startu](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Název vlastnosti  | Popis | Jednotka |
|:--- |:--- |:--- |
| DataRead | Velikost dat číst ze zdroje | Hodnota Int64 v **bajtů** |
| DataWritten | Velikost dat zapsaných do jímky | Hodnota Int64 v **bajtů** |
| filesRead | Počet souborů, které jsou kopírovány při kopírování dat z úložiště souborů. | Hodnota Int64 (žádná jednotka) |
| fileScanned | Počet souborů, které se má zkontrolovat ze zdrojového souboru úložiště. | Hodnota Int64 (žádná jednotka) |
| filesWritten | Počet souborů, které jsou kopírovány při kopírování dat do služby file storage. | Hodnota Int64 (žádná jednotka) |
| rowsCopied | Počet řádků, které jsou kopírovány (není k dispozici pro binární kopie). | Hodnota Int64 (žádná jednotka) |
| rowsSkipped | Počet nekompatibilních řádků se přeskakuje. Funkci můžete zapnout pomocí nastavení "enableSkipIncompatibleRow" na hodnotu true. | Hodnota Int64 (žádná jednotka) |
| Propustnost | Poměr, ve kterém jsou přenesená data | Plovoucí desetinné číslo s desetinnou čárkou v **KB/s** |
| copyDuration | Doba kopírování | Hodnota Int32 v řádu sekund |
| sqlDwPolyBase | Pokud při kopírování dat do SQL Data Warehouse PolyBase používá. | Logická hodnota |
| redshiftUnload | Pokud uvolnění se používá při kopírování dat z Redshift. | Logická hodnota |
| hdfsDistcp | Pokud DistCp se používá při kopírování dat z HDFS. | Logická hodnota |
| effectiveIntegrationRuntime | Ukazují, které integrace Runtime(s) se používá pro spuštění ve formátu aktivit `<IR name> (<region if it's Azure IR>)`. | Text (řetězec) |
| usedDataIntegrationUnits | Efektivní jednotky integrace dat během kopírování. | Hodnota Int32 |
| usedParallelCopies | Efektivní parallelCopies během kopírování. | Hodnota Int32|
| redirectRowPath | Cesta k protokolu přeskočené nekompatibilních řádků v úložišti objektů blob, které jste nakonfigurovali v části "redirectIncompatibleRowSettings". Najdete v následujícím příkladu. | Text (řetězec) |
| executionDetails | Další informace o fázích prochází aktivitu kopírování, a příslušné postupy, doba trvání, používaných konfigurací, atd. Není doporučuje se v této části analyzovat, jak může změnit. | Pole |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedDataIntegrationUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedDataIntegrationUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Schéma a mapování datového typu

Zobrazit [schéma a data mapování typu](copy-activity-schema-and-type-mapping.md), která popisuje, jak aktivitu kopírování, která mapuje data zdroje do jímky.

## <a name="fault-tolerance"></a>Odolnost proti chybám

Ve výchozím nastavení aktivita kopírování kopírování dat se zastaví a vrátí chybu, pokud se setká s nekompatibilní data mezi zdrojem a jímkou. Můžete explicitně nakonfigurovat přeskočit a protokolování nekompatibilních řádků a kopírovat pouze ty kompatibilní data k vytvoření kopie byla úspěšná. Zobrazit [aktivity kopírování odolnost proti chybám](copy-activity-fault-tolerance.md) na další podrobnosti.

## <a name="performance-and-tuning"></a>Výkon a ladění

Zobrazit [Průvodce laděním a výkonem aktivity kopírování](copy-activity-performance.md), která popisuje klíčové faktory, které ovlivňují výkon přesun dat (aktivita kopírování) ve službě Azure Data Factory. Také uvádí zjištěnou výkon při interní testování a tento článek popisuje různé způsoby, jak optimalizovat výkon aktivitu kopírování.

V některých případech po provedení aktivity kopírování ve službě ADF, přímo uvidíte "**tipy pro optimalizaci výkonu**" nahoře [monitorování stránku aktivita kopírování](#monitor-visually) jak je znázorněno v následujícím příkladu. Jenom se říká kritickým bodem identifikovat pro spuštění dané kopie, ale provede vás také o tom, jak změnit tak, aby zvýšení propustnosti kopírování. Tipy pro ladění aktuálně poskytují návrhy, například při kopírování dat do Azure SQL Data Warehouse pomocí PolyBase zvýšit Azure Cosmos DB RU nebo Azure SQL DB DTU při ukládání zdroje dat na straně výkonu je kritický bod, chcete-li odebrat nepotřebné připravené kopírování atd. Výkon ladění pravidel bude postupně rozšiřují i.

**Příklad: kopírování do služby Azure SQL DB s tipy pro optimalizaci výkonu**

V této ukázce během kopírování spustit ADF Všimněte si, že jímky Azure SQL DB dosáhne vysokého využití DTU, což zpomalí operace zápisu návrh tedy ke zvýšení úrovně služby Azure SQL DB s více DTU. 

![Zkopírujte monitorování s tipy pro optimalizaci výkonu](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Přírůstkové kopírování 
Data Factory podporuje scénáře pro přírůstkově kopíruje rozdílová data ze zdrojového úložiště dat do cílového úložiště dat. Zobrazit [kurz: přírůstkové kopírování dat](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Čtení a zápis dělených dat
Ve verzi 1 služby Azure Data Factory nepodporuje čtení nebo zápis dělených dat pomocí vlastnosti SliceStart a SliceEnd/WindowStart/WindowEnd systémové proměnné. V aktuální verzi můžete toto chování dosáhnout s použitím parametru kanál a čas zahájení/naplánovaný čas triggeru jako hodnotu parametru. Další informace najdete v tématu [jak číst nebo zapisovat data rozdělit na oddíly](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Další postup
Tyto rychlé starty, kurzy a ukázky, naleznete v tématu:

- [Kopírování dat z jednoho umístění do jiného umístění ve stejném úložišti objektů Blob v Azure](quickstart-create-data-factory-dot-net.md)
- [Kopírování dat z Azure Blob Storage do služby Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopírování dat z místního SQL serveru do Azure](tutorial-hybrid-copy-powershell.md)
