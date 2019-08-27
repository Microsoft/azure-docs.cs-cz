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
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 59ac4b36a4bc2b3ff454b3a2ae98ce60f6bfcb5f
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996606"
---
# <a name="copy-activity-in-azure-data-factory"></a>Aktivita kopírování ve službě Azure Data Factory

## <a name="overview"></a>Přehled

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-data-movement-activities.md)
> * [Aktuální verze](copy-activity-overview.md)

Ve službě Azure Data Factory můžete použít aktivitu kopírování ke kopírování dat mezi datového úložiště v místním prostředí i v cloudu. Po zkopírování dat je lze dále transformovat a analyzovat pomocí jiných aktivit. Aktivitu kopírování, která můžete použít také k publikování, transformace a analýza výsledků pro business intelligence (BI) a využití aplikace.

![Pro dané aktivity kopírování](media/copy-activity-overview/copy-activity.png)

Aktivita kopírování se provádí na [prostředí Integration Runtime](concepts-integration-runtime.md). U různých scénářů kopírování dat je možné využít různé typy Integration Runtime:

* Při kopírování dat mezi úložišti dat, která jsou veřejně přístupná prostřednictvím Internetu z jakékoli IP adresy, může být aktivita kopírování oprávněná **Azure Integration runtime**, která je zabezpečená, spolehlivá, škálovatelná a [globálně dostupná](concepts-integration-runtime.md#integration-runtime-location).
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

Aktivita kopírování také podporuje čtení a zápis do souborů v zadaných formátech: **Text, JSON, Avro, orc a Parquet**a komprimace a dekomprimace souborů pomocí následujících kodeků: **Gzip, Deflate, bzip2 a ZipDeflate**. Zobrazit [podporované formáty souborů a komprese](supported-file-formats-and-compression-codecs.md) s podrobnostmi.

Například můžete provést následující aktivity kopírování:

* Zkopírujte data v místních SQL Server a zapište do Azure Data Lake Storage Gen2 ve formátu Parquet.
* Zkopírujte soubory ve formátu textu (CSV) v místním systému souborů a zápis do objektu Blob Azure ve formátu Avro.
* Zkopírujte soubory zip z místního systému souborů a dekomprimujte je na Azure Data Lake Storage Gen2.
* Kopírování dat z objektů Blob v Azure ve formátu GZip komprimované text (CSV) a zápis do služby Azure SQL Database.
* A mnoho dalších případů s potřebou serializace/deserializace nebo komprese/dekomprese.

## <a name="supported-regions"></a>Podporované oblasti

Služba, která využívá aktivitu kopírování, která je dostupná globálně v oblastech a zeměpisných oblastech uvedených v [umístění prostředí Azure Integration Runtime](concepts-integration-runtime.md#integration-runtime-location). Globálně dostupné topologie zajišťuje přesun efektivní dat, které se obvykle vyhýbají segmentů směrování mezi oblastmi. Zobrazit [služby v jednotlivých oblastech](https://azure.microsoft.com/regions/#services) dostupnosti služby Data Factory a přesun dat v oblasti.

## <a name="configuration"></a>Konfigurace

Chcete-li použít aktivitu kopírování, která ve službě Azure Data Factory, budete muset:

1. **Vytvoření propojených služeb pro zdrojové úložiště dat a úložiště dat jímky.** Najdete v části "Vlastnostem propojených služeb" konektor článek o tom, jak nakonfigurovat a podporovaných vlastností. Můžete najít v seznamu podporovaných konektor v [podporovaných úložišť dat a formáty](#supported-data-stores-and-formats) oddílu.
2. **Vytvoření datové sady pro zdroje a jímky.** Informace o tom, jak nakonfigurovat a jaké vlastnosti podporují, najdete v části Vlastnosti datové sady v konektorech zdroje a jímky.
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
| type | Vlastnost Type aktivity kopírování musí být nastavená na: **Copy** | Ano |
| inputs | Zadejte datovou sadu vytvoříte, která odkazuje na zdroj dat. Aktivitu kopírování, která podporuje pouze jeden vstup. | Ano |
| outputs | Zadejte datovou sadu vytvoříte, které body k datům jímky. Aktivitu kopírování, která podporuje pouze jeden výstup. | Ano |
| typeProperties | Skupina vlastností konfigurace aktivity kopírování. | Ano |
| source | Zadejte typ zdroje kopie a odpovídající vlastnosti o tom, jak načíst data.<br/><br/>Přečtěte si podrobnosti v článku konektor uvedené v části "Vlastnosti aktivity kopírování" [podporovaných úložišť dat a formáty](#supported-data-stores-and-formats). | Ano |
| sink | Zadejte typ jímky kopírování a odpovídajících vlastností o tom, jak zapsat data.<br/><br/>Přečtěte si podrobnosti v článku konektor uvedené v části "Vlastnosti aktivity kopírování" [podporovaných úložišť dat a formáty](#supported-data-stores-and-formats). | Ano |
| translator | Zadejte mapování sloupce explicitní ze zdroje do jímky. Platí při kopírování chování nejde splnit vaše potřeby.<br/><br/>Další podrobnosti o [schéma a data mapování typu](copy-activity-schema-and-type-mapping.md). | Ne |
| dataIntegrationUnits | Zadejte powerfulness z [prostředí Azure Integration Runtime](concepts-integration-runtime.md) pro kopírování dat. Dříve označované jako cloudové jednotky přesunu dat (DMU). <br/><br/>Další podrobnosti o [jednotky integrace dat](copy-activity-performance.md#data-integration-units). | Ne |
| parallelCopies | Zadejte paralelismu, který chcete použít při čtení dat ze zdroje a zápis dat do jímky aktivity kopírování.<br/><br/>Další podrobnosti o [paralelní kopírování](copy-activity-performance.md#parallel-copy). | Ne |
| enableStaging<br/>stagingSettings | Vyberte, chcete-li připravit dočasná data v úložišti objektů BLOB místo přímého kopírování dat ze zdroje do jímky.<br/><br/>Další užitečné scénáře a podrobnosti o konfiguraci z [fázovaného kopírování](copy-activity-performance.md#staged-copy). | Ne |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Zvolte způsob zpracování nekompatibilních řádků při kopírování dat ze zdroje do jímky.<br/><br/>Další podrobnosti o [odolnost proti chybám](copy-activity-fault-tolerance.md). | Ne |

## <a name="monitoring"></a>Monitorování

Můžete monitorovat spuštění v Azure Data Factory "Vytvořit a monitorovat" uživatelské rozhraní nebo programově aktivity kopírování.

### <a name="monitor-visually"></a>Vizuální monitorování

Chcete-li vizuálně sledovat spuštění aktivity kopírování, přejděte do služby data factory -> **vytvořit a monitorovat** -> **kartu monitorování**, uvidíte seznam kanálu spouští pomocí odkazu v "Zobrazitspuštěníaktivit" **Akce** sloupce.

![Monitorování spuštění kanálu](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Kliknutím zobrazíte seznam aktivit v tomto běhu kanálu. V **akce** sloupce, máte odkazy na vstup aktivity kopírování, výstup, chyby (Pokud se nezdaří spuštění aktivity kopírování) a podrobnosti.

![Monitorování spuštění aktivit](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Klikněte "**podrobnosti**" propojit v rámci **akce** zobrazíte podrobnosti o spuštění aktivity kopírování a výkonové charakteristiky. To se dozvíte informace, včetně svazek/řádků/soubory dat zkopíruje ze zdroje do jímky, propustnost, kroky prochází s určitou dobu a použít konfigurace pro váš scénář kopírování.

>[!TIP]
>V některých scénářích se v horní části stránky pro monitorování kopírování zobrazí také "Tipy k vyladění**výkonu**", které vám sdělí zjištěné kritické body a provede vás v tom, co se dá změnit, aby se zvýšila propustnost kopírování [](#performance-and-tuning), viz příklad s podrobnostmi.

**Příklad: kopírování z Amazon S3 do Azure Data Lake Store**
![podrobnosti o spuštění aktivit monitorování](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Příklad: kopírování ze služby Azure SQL Database do Azure SQL Data Warehouse pomocí fázovaného kopírování**
![podrobnosti o spuštění aktivit monitorování](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorování prostřednictvím kódu programu

V části výsledek spuštění aktivity kopírování – > výstup se vrátí taky podrobnosti o spuštění aktivity kopírování a výkonnostní charakteristiky. Níže je úplný seznam; se zobrazí pouze ty použitelné pro váš scénář kopírování. Zjistěte, jak sledovat činnost spustit z [části sledování pomocí rychlého startu](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Název vlastnosti  | Popis | Jednotka |
|:--- |:--- |:--- |
| dataRead | Velikost dat číst ze zdroje | Hodnota Int64 v **bajtů** |
| dataWritten | Velikost dat zapsaných do jímky | Hodnota Int64 v **bajtů** |
| filesRead | Počet souborů, které jsou kopírovány při kopírování dat z úložiště souborů. | Hodnota Int64 (žádná jednotka) |
| filesWritten | Počet souborů, které jsou kopírovány při kopírování dat do služby file storage. | Hodnota Int64 (žádná jednotka) |
| sourcePeakConnections | Počet souběžných připojení k zdrojovému úložišti dat během spuštění aktivity kopírování. | Hodnota Int64 (žádná jednotka) |
| sinkPeakConnections | Počet souběžných připojení, které byly během spuštění aktivity kopírování navázány na úložiště dat jímky. | Hodnota Int64 (žádná jednotka) |
| rowsRead | Počet řádků, které jsou čteny ze zdroje (neplatí pro binární kopii). | Hodnota Int64 (žádná jednotka) |
| rowsCopied | Počet řádků, které se zkopírují do jímky (neplatí pro binární kopie). | Hodnota Int64 (žádná jednotka) |
| rowsSkipped | Počet nekompatibilních řádků se přeskakuje. Funkci můžete zapnout pomocí nastavení "enableSkipIncompatibleRow" na hodnotu true. | Hodnota Int64 (žádná jednotka) |
| copyDuration | Doba trvání kopie. | Hodnota Int32 v řádu sekund |
| throughput | Poměr přenesených dat. | Plovoucí desetinné číslo s desetinnou čárkou v **KB/s** |
| sourcePeakConnections | Nejvyšší počet souběžných připojení navázaných ke zdrojovému úložišti dat během kopírování. | Hodnota Int32 |
| sinkPeakConnections| Nejvyšší počet souběžných připojení navázaných na úložiště dat jímky během kopírování.| Hodnota Int32 |
| sqlDwPolyBase | Pokud při kopírování dat do SQL Data Warehouse PolyBase používá. | Logická hodnota |
| redshiftUnload | Pokud uvolnění se používá při kopírování dat z Redshift. | Logická hodnota |
| hdfsDistcp | Pokud DistCp se používá při kopírování dat z HDFS. | Logická hodnota |
| effectiveIntegrationRuntime | Ukazují, které integrace Runtime(s) se používá pro spuštění ve formátu aktivit `<IR name> (<region if it's Azure IR>)`. | Text (řetězec) |
| usedDataIntegrationUnits | Efektivní jednotky integrace dat během kopírování. | Hodnota Int32 |
| usedParallelCopies | Efektivní parallelCopies během kopírování. | Hodnota Int32 |
| redirectRowPath | Cesta k protokolu přeskočené nekompatibilních řádků v úložišti objektů blob, které jste nakonfigurovali v části "redirectIncompatibleRowSettings". Najdete v následujícím příkladu. | Text (řetězec) |
| executionDetails | Další informace o fázích prochází aktivitu kopírování, a příslušné postupy, doba trvání, používaných konfigurací, atd. Není doporučuje se v této části analyzovat, jak může změnit.<br/><br/>ADF také hlásí podrobné doby trvání (v sekundách) strávené podle příslušných kroků v `detailedDurations`části. Doba trvání těchto kroků je exkluzivní a zobrazí se jenom ty, které se vztahují k danému spuštění aktivity kopírování:<br/>- **Doba** zařazení do fronty (`queuingDuration`): Uplynulý čas do chvíle, kdy se aktivita kopírování ve skutečnosti spustí v prostředí Integration runtime. Pokud používáte prostředí IR v místním prostředí a tato hodnota je velká, navrhněte, zda chcete zjistit kapacitu a využití infračerveného přenosu, a podle vašich úloh nahorizontální navýšení nebo zmenšení kapacity. <br/>- **Doba trvání skriptu před kopírováním** (`preCopyScriptDuration`): Uplynulý čas mezi aktivitou kopírování začínající v rámci aktivity IR a kopírování dokončuje provádění skriptu před kopírováním v úložišti dat jímky. Použijte při konfiguraci skriptu před kopírováním. <br/>- **Čas do prvního bajtu** (`timeToFirstByte`): Uplynulý čas mezi koncem předchozího kroku a infračervený příjem prvního bajtu ze zdrojového úložiště dat. Použít pro zdroj nezaložen na souborech. Pokud je tato hodnota velká, navrhněte kontrolu a optimalizaci dotazu nebo serveru.<br/>- **Doba trvání přenosu** (`transferDuration`): Uplynulý čas mezi koncem předchozího kroku a IR, který přenáší všechna data ze zdroje do jímky. | Array |
| perfRecommendation | Kopírování tipů pro ladění výkonu. Podrobnosti najdete v části o [výkonu a ladění](#performance-and-tuning) . | Array |

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

## <a name="schema-and-data-type-mapping"></a>Schéma a mapování datového typu

Zobrazit [schéma a data mapování typu](copy-activity-schema-and-type-mapping.md), která popisuje, jak aktivitu kopírování, která mapuje data zdroje do jímky.

## <a name="fault-tolerance"></a>Odolnost proti chybám

Ve výchozím nastavení zastaví aktivita kopírování data kopírováním a vrátí chybu, pokud narazí na nekompatibilní data mezi zdrojem a jímkou. Můžete explicitně nakonfigurovat přeskočit a protokolování nekompatibilních řádků a kopírovat pouze ty kompatibilní data k vytvoření kopie byla úspěšná. Zobrazit [aktivity kopírování odolnost proti chybám](copy-activity-fault-tolerance.md) na další podrobnosti.

## <a name="performance-and-tuning"></a>Výkon a ladění

Zobrazit [Průvodce laděním a výkonem aktivity kopírování](copy-activity-performance.md), která popisuje klíčové faktory, které ovlivňují výkon přesun dat (aktivita kopírování) ve službě Azure Data Factory. Také uvádí zjištěnou výkon při interní testování a tento článek popisuje různé způsoby, jak optimalizovat výkon aktivitu kopírování.

V některých případech se při provádění aktivity kopírování v podavači ADF přímo zobrazí zpráva "Tipy pro**ladění výkonu**" na [stránce monitorování aktivity kopírování](#monitor-visually) , jak je znázorněno v následujícím příkladu. Neoznamuje vám pouze kritická místa identifikovaná pro danou kopii kopírování, ale také vás provede tím, co se dá změnit, aby se zvýšila propustnost kopírování. Tipy pro ladění výkonu aktuálně poskytují návrhy, jako je například použití základu při kopírování dat do Azure SQL Data Warehouse, pro zvýšení Azure Cosmos DB RU nebo DTU databáze SQL Azure, když je na straně úložiště dat k dispozici kritické místo pro odebrání zbytečných fází. Kopírovat atd. Pravidla optimalizace výkonu se postupně rozšiřují.

**Příklad: kopírování do Azure SQL DB s tipy pro ladění výkonu**

V této ukázce si během kopírování ADF vypíše oznámení, že jímka služby Azure SQL DB dosáhne vysokého využití DTU, což zpomaluje operace zápisu, takže návrh je zvýšit úroveň databáze SQL Azure s větší DTU.

![Sledování kopírování pomocí tipů pro ladění výkonu](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Přírůstkové kopírování
Data Factory podporuje scénáře přírůstkového kopírování rozdílových dat ze zdrojového úložiště dat do úložiště dat jímky. Zobrazit [kurz: přírůstkové kopírování dat](tutorial-incremental-copy-overview.md).

## <a name="next-steps"></a>Další kroky
Tyto rychlé starty, kurzy a ukázky, naleznete v tématu:

- [Kopírování dat z jednoho umístění do jiného umístění ve stejném úložišti objektů Blob v Azure](quickstart-create-data-factory-dot-net.md)
- [Kopírování dat z Azure Blob Storage do služby Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopírování dat z místního SQL serveru do Azure](tutorial-hybrid-copy-powershell.md)
