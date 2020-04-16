---
title: Kopírování funkcí optimalizace výkonu aktivity
description: Seznamte se s klíčovými funkcemi, které vám pomohou optimalizovat výkon aktivity kopírování v Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/09/2020
ms.openlocfilehash: fd7844340553809e1429097a9dda70f6bdb3e075
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414193"
---
# <a name="copy-activity-performance-optimization-features"></a>Kopírování funkcí optimalizace výkonu aktivity

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje funkce optimalizace výkonu aktivity kopírování, které můžete využít v Azure Data Factory.

## <a name="data-integration-units"></a>Jednotky integrace dat

Jednotka integrace dat je míra, která představuje výkon (kombinace procesoru, paměti a přidělení síťových prostředků) jedné jednotky v Azure Data Factory. Jednotka integrace dat se vztahuje pouze na [prostředí runtime integrace Azure](concepts-integration-runtime.md#azure-integration-runtime), ale ne na prostředí [runtime integrace s vlastním hostitelem](concepts-integration-runtime.md#self-hosted-integration-runtime).

Povolené DIU pro posílení spuštění aktivity kopírování je **mezi 2 a 256**. Pokud není zadán nebo zvolíte "Auto" v uI, Data Factory dynamicky použít optimální nastavení DIU na základě vašeho dvojice zdroj-jímka a vzor dat. V následující tabulce jsou uvedeny podporované rozsahy DIU a výchozí chování v různých scénářích kopírování:

| Kopírovat scénář | Podporovaný rozsah DIU | Výchozí DIU určené službou |
|:--- |:--- |---- |
| Mezi úložišti souborů |- **Kopírování z nebo do jednoho souboru**: 2-4 <br>- **Kopírování z a do více souborů**: 2-256 v závislosti na počtu a velikosti souborů <br><br>Pokud například zkopírujete data ze složky se 4 velkými soubory a rozhodnete se zachovat hierarchii, maximální efektivní diu je 16; pokud se rozhodnete sloučit soubor, maximální efektivní DIU je 4. |Mezi 4 a 32 v závislosti na počtu a velikosti souborů |
| Z úložiště souborů do jiného než souborového úložiště |- **Kopírování z jednoho souboru**: 2-4 <br/>- **Kopírování z více souborů**: 2-256 v závislosti na počtu a velikosti souborů <br/><br/>Pokud například zkopírujete data ze složky se 4 velkými soubory, maximální efektivní diu je 16. |- **Kopírování do Azure SQL Database nebo Azure Cosmos DB:** mezi 4 a 16 v závislosti na vrstvě jímky (DTU/RU) a vzor zdrojového souboru<br>- **Kopírování do Azure Synapse Analytics** pomocí příkazu PolyBase nebo COPY: 2<br>- Jiný scénář: 4 |
| Z nesouborového úložiště do úložiště souborů |- **Kopírování z úložišť dat s povolenou možností oddílu** (včetně [oracle](connector-oracle.md#oracle-as-source)/[netezza](connector-netezza.md#netezza-as-source)/[teradata):](connector-teradata.md#teradata-as-source)2-256 při zápisu do složky a 2-4 při zápisu do jednoho souboru. Poznámka: Na zdrojový datový oddíl můžete použít až 4 DIU.<br>- **Další scénáře**: 2-4 |- **Kopírování z REST nebo HTTP**: 1<br/>- **Kopie z Amazon Redshift** pomocí UNLOAD: 2<br>- **Jiný scénář**: 4 |
| Mezi nesouborovými obchody |- **Kopírování z úložišť dat s povolenou možností oddílu** (včetně [oracle](connector-oracle.md#oracle-as-source)/[netezza](connector-netezza.md#netezza-as-source)/[teradata):](connector-teradata.md#teradata-as-source)2-256 při zápisu do složky a 2-4 při zápisu do jednoho souboru. Poznámka: Na zdrojový datový oddíl můžete použít až 4 DIU.<br/>- **Další scénáře**: 2-4 |- **Kopírování z REST nebo HTTP**: 1<br>- **Jiný scénář**: 4 |

Můžete vidět DIU používané pro každou kopii spustit v zobrazení sledování aktivity kopírování nebo výstup aktivity. Další informace naleznete v [tématu Kopírování sledování aktivity](copy-activity-monitoring.md). Chcete-li přepsat toto výchozí `dataIntegrationUnits` nastavení, zadejte hodnotu vlastnosti následujícím způsobem. *Skutečný počet DIU,* které operace kopírování používá za běhu, je roven nebo menší než nakonfigurovaná hodnota v závislosti na datovém vzoru.

Bude vám **účtovánpočet použitých \* jednotek \* trvání kopírování DIUs/Hodina DIU**. Aktuální ceny naleznete [zde](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Pro každý typ předplatného může platit místní měna a samostatné slevy.

**Příklad:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Škálovatelnost prostředí runtime integrace s vlastním hostitelem

Pokud chcete dosáhnout vyšší propustnost, můžete buď vertikálně navýšit kapacitu, nebo vertikálně navýšit kapacitu pro infračervený přenos hostovaný na vlastním propustku:

- Pokud procesor a dostupná paměť na samostatně hostovaném infračerveném uzlu nejsou plně využity, ale provádění souběžných úloh dosahuje limitu, měli byste vertikálně navýšit kapacitu zvýšením počtu souběžných úloh, které lze spustit na uzlu.  Pokyny naleznete [zde.](create-self-hosted-integration-runtime.md#scale-up)
- Pokud je na druhé straně procesor vysoko na samoobslužném infračerveném uzlu nebo je nedostatek dostupné paměti, můžete přidat nový uzel, který vám pomůže horizontální navýšení kapacity zatížení napříč více uzly.  Pokyny naleznete [zde.](create-self-hosted-integration-runtime.md#high-availability-and-scalability)

Poznámka: V následujících scénářích může spuštění aktivity jedné kopie využít více samoobslužných infračervených uzlů:

- Kopírování dat z úložišť založených na souborech v závislosti na počtu a velikosti souborů.
- Kopírování dat z úložiště dat s povolenou možností oddílu (včetně [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)a SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) v závislosti na počtu datových oddílů.

## <a name="parallel-copy"></a>Paralelní kopírování

Můžete nastavit paralelní`parallelCopies` kopírování ( vlastnost) na aktivitu kopírování k označení paralelismu, který má být aktivita kopírování používána. Tuto vlastnost si můžete myslet jako maximální počet podprocesů v rámci aktivity kopírování, které číst ze zdroje nebo zapisovat do úložiště dat jímky paralelně.

Paralelní kopie je ortogonová do [jednotek integrace dat](#data-integration-units) nebo [do samoobslužných infračervených uzlů](#self-hosted-integration-runtime-scalability). Počítá se ve všech uzlech DIU nebo infračervených uzlů hostovaných samostatně.

Pro každou spuštěnou aktivitu kopírování ve výchozím nastavení Azure Data Factory dynamicky použít optimální paralelní nastavení kopírování na základě vašeho páru zdroj-jímka a vzor dat. 

> [!TIP]
> Výchozí chování paralelní kopie obvykle poskytuje nejlepší propustnost, která je automaticky určena ADF na základě vašeho páru zdroj-jímka, datový vzor a počet DIU nebo self-hostované IR CPU / paměti / počet uzlů. Informace o tom, kdy chcete vyladit paralelní kopírování, naleznete v článek Poradce při potížích s [výkonem aktivity kopírování.](copy-activity-performance-troubleshooting.md)

V následující tabulce je uvedeno chování paralelního kopírování:

| Kopírovat scénář | Chování paralelního kopírování |
| --- | --- |
| Mezi úložišti souborů | `parallelCopies`určuje paralelismus **na úrovni souboru**. Bloků v rámci každého souboru se děje pod automaticky a transparentně. Je navržen tak, aby používal nejvhodnější velikost bloku dat pro daný typ úložiště dat k paralelnímu načítání dat. <br/><br/>Skutečný počet paralelních kopií kopie aktivity používá za běhu není větší než počet souborů, které máte. Pokud je chování kopírování **mergeFile** do jímky souborů, aktivita kopírování nemůže využít paralelismu na úrovni souboru. |
| Z úložiště souborů do jiného než souborového úložiště | - Při kopírování dat do Azure SQL Database nebo Azure Cosmos DB výchozí paralelní kopie závisí také na dřezové vrstvy (počet DTU/RU).<br>- Při kopírování dat do Azure Table je výchozí paralelní kopie 4. |
| Z nesouborového úložiště do úložiště souborů | - Při kopírování dat z úložiště dat s povolenou možností oddílu (včetně [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)a SAP [Open Hub)](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)je výchozí paralelní kopie 4. Skutečný počet paralelních kopií kopie aktivity používá za běhu není větší než počet datových oddílů, které máte. Při použití prostředí Runtime integrace s vlastním hostitelem a kopírování do azure blob/ADLS Gen2, všimněte si, že maximální efektivní paralelní kopie je 4 nebo 5 na infračervený uzel.<br>- Pro jiné scénáře paralelní kopie se neprojeví. I když je zadán paralelismus, není použit. |
| Mezi nesouborovými obchody | - Při kopírování dat do Azure SQL Database nebo Azure Cosmos DB výchozí paralelní kopie závisí také na dřezové vrstvy (počet DTU/RU).<br/>- Při kopírování dat z úložiště dat s povolenou možností oddílu (včetně [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)a SAP [Open Hub)](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)je výchozí paralelní kopie 4.<br>- Při kopírování dat do Azure Table je výchozí paralelní kopie 4. |

Chcete-li řídit zatížení počítačů, které hostují úložiště dat, nebo optimalizovat výkon kopírování, `parallelCopies` můžete přepsat výchozí hodnotu a zadat hodnotu vlastnosti. Hodnota musí být celé číslo větší nebo rovno 1. Za běhu pro nejlepší výkon aktivity kopírování používá hodnotu, která je menší nebo rovna hodnotě, kterou nastavíte.

Když zadáte hodnotu `parallelCopies` vlastnosti, vezměte v úvahu zvýšení zatížení na zdroj ovém a jímacím úložišti dat. Zvažte také zvýšení zatížení za běhu integrace s vlastním hostitelem, pokud je aktivita kopírování zmocněna. K tomuto nárůstu zatížení dochází zejména v případě, že máte více aktivit nebo souběžných spuštění stejné aktivity, které běží proti stejnému úložišti dat. Pokud zjistíte, že úložiště dat nebo vlastní hostované integrace za běhu je `parallelCopies` zahlceni zatížení, snížit hodnotu zmírnit zatížení.

**Příklad:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Fázovaná kopie

Když zkopírujete data ze zdrojového úložiště dat do úložiště dat jímky, můžete použít úložiště objektů blob jako dočasné pracovní úložiště. Pracovní je zvláště užitečné v následujících případech:

- **Chcete ingestovat data z různých úložišť dat do datového skladu SQL přes PolyBase.** SQL Data Warehouse používá PolyBase jako mechanismus vysoké propustnosti k načtení velkého množství dat do datového skladu SQL. Zdrojová data musí být ve službě Blob storage nebo Azure Data Lake Store a musí splňovat další kritéria. Když načtete data z jiného úložiště dat než úložiště objektů blob nebo Azure Data Lake Store, můžete aktivovat kopírování dat prostřednictvím dočasného pracovního úložiště objektů blob. V takovém případě Azure Data Factory provádí požadované transformace dat k zajištění, že splňuje požadavky PolyBase. Pak používá PolyBase efektivně načítat data do datového skladu SQL. Další informace najdete [v tématu Použití PolyBase k načtení dat do datového skladu Azure SQL](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Někdy trvá nějakou dobu provést hybridní přesun dat (to znamená zkopírovat z místního úložiště dat do úložiště dat v cloudu) přes pomalé síťové připojení.** Chcete-li zvýšit výkon, můžete použít fázovanou kopii komprimovat data v místním tak, aby trvalo méně času přesunout data do pracovníúložiště dat v cloudu. Potom můžete dekomprimovat data v pracovním úložišti před načtením do cílového úložiště dat.
- **Nechcete otevírat jiné porty než port 80 a port 443 v bráně firewall z důvodu podnikových zásad IT.** Pokud například zkopírujete data z místního úložiště dat do jímky databáze Azure SQL nebo jímky datového skladu Azure SQL, musíte aktivovat odchozí komunikaci TCP na portu 1433 pro bránu firewall systému Windows i podnikovou bránu firewall. V tomto scénáři fázované kopírování můžete využít runtime vlastní hostované integrace nejprve zkopírovat data do pracovní instance úložiště objektů blob přes HTTP nebo HTTPS na portu 443. Pak může načíst data do databáze SQL nebo sql datového skladu z fáze úložiště objektů Blob. V tomto toku není nutné povolit port 1433.

### <a name="how-staged-copy-works"></a>Jak fázované kopie funguje

Když aktivujete pracovní funkci, nejprve se data zkopírují ze zdrojového úložiště dat do pracovního úložiště objektů Blob (přineste si vlastní). Dále se data zkopírují z pracovníúložiště dat do úložiště dat jímky. Azure Data Factory automaticky spravuje dvoustupňový tok za vás. Azure Data Factory také vyčistí dočasná data z pracovního úložiště po dokončení přesunu dat.

![Fázovaná kopie](media/copy-activity-performance/staged-copy.png)

Při aktivaci přesunu dat pomocí pracovního úložiště můžete určit, zda mají být data komprimována před přesunutím dat ze zdrojového úložiště dat do dočasného nebo pracovního úložiště dat a dekomprimována před přesunutím dat z dočasného nebo pracovního úložiště dat do úložiště dat jímky.

V současné době nelze kopírovat data mezi dvěma úložišti dat, která jsou připojena prostřednictvím různých samoobslužných irs, ani s ani bez fázované kopie. Pro takový scénář můžete nakonfigurovat dvě explicitně zřetězené aktivity kopírování ke kopírování ze zdroje do pracovní hodu pak z pracovní do jímky.

### <a name="configuration"></a>Konfigurace

Nakonfigurujte nastavení **enableStaging** v aktivitě kopírování a určete, zda mají být data připravena k přípravě v úložišti objektů Blob před jejich načtením do cílového úložiště dat. Pokud nastavíte `TRUE` **funkci enableStaging** to , zadejte další vlastnosti uvedené v následující tabulce. Pokud ji nemáte, musíte taky vytvořit službu vázanou na podpis azure storage nebo úložiště pro sdílený přístup.

| Vlastnost | Popis | Výchozí hodnota | Požaduje se |
| --- | --- | --- | --- |
| enableStaging |Určete, zda chcete kopírovat data prostřednictvím dočasného pracovního úložiště. |False |Ne |
| linkedServiceName |Zadejte název propojené služby [AzureStorage,](connector-azure-blob-storage.md#linked-service-properties) která odkazuje na instanci úložiště, které používáte jako dočasné pracovní úložiště. <br/><br/> Úložiště se sdíleným přístupovým podpisem nelze použít k načtení dat do datového skladu SQL prostřednictvím PolyBase. Můžete jej použít ve všech ostatních scénářích. |– |Ano, pokud je **enableStaging** nastaven na hodnotu TRUE |
| cesta |Zadejte cestu úložiště objektů blob, kterou chcete obsahovat fázovaná data. Pokud nezadáte cestu, služba vytvoří kontejner pro ukládání dočasných dat. <br/><br/> Cestu zadejte pouze v případě, že používáte úložiště se sdíleným přístupovým podpisem nebo požadujete, aby dočasná data byla v určitém umístění. |– |Ne |
| povolitKomprese |Určuje, zda mají být data komprimována před zkopírováním do cílového umístění. Toto nastavení snižuje objem přenášených dat. |False |Ne |

>[!NOTE]
> Pokud používáte fázovanou kopii s povolenou kompresí, není podporován instanční objekt nebo ověřování MSI pro pracovní službu propojenou objektem blob.

Zde je ukázková definice aktivity kopírování s vlastnostmi popsanými v předchozí tabulce:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Dopad fakturace fázované kopie

Poplatky vám budou účtovány na základě dvou kroků: doba trvání kopírování a typ kopírování.

* Když používáte pracovní během kopie cloudu, která kopíruje data z úložiště cloudových dat do jiného cloudového úložiště dat, obě fáze zmocněné runtimem integrace Azure, bude vám účtována [součet doby trvání kopírování pro krok 1 a krok 2] x [cena jednotky kopírování cloudu].
* Když používáte pracovní během hybridní kopie, která kopíruje data z místního úložiště dat do cloudového úložiště dat, o jednu fázi posílenou runtimem integrace s vlastním hostitelem, bude vám účtována [doba trvání hybridní kopie] x [hybridní cena kopie jednotky] + [doba trvání kopie cloudu] x [cena jednotky kopie cloudu].

## <a name="next-steps"></a>Další kroky
Podívejte se na další články o aktivitách kopírování:

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Kopírovat průvodce výkonem a škálovatelností aktivit](copy-activity-performance.md)
- [Poradce při potížích s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md)
- [Migrace dat z datového jezera nebo datového skladu do Azure pomocí Azure Data Factory](data-migration-guidance-overview.md)
- [Migrace dat z Amazonu S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md)