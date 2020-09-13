---
title: Funkce optimalizace výkonu aktivity kopírování
description: Seznamte se s klíčovými funkcemi, které vám pomůžou optimalizovat výkon aktivity kopírování v Azure Data Factory Marketplace.
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
ms.date: 08/05/2020
ms.openlocfilehash: d93ff81bacbb537cc5891e0b869f164e0d6824c6
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440537"
---
# <a name="copy-activity-performance-optimization-features"></a>Funkce optimalizace výkonu aktivity kopírování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje funkce optimalizace výkonu aktivity kopírování, které můžete využít v Azure Data Factory.

## <a name="data-integration-units"></a>Jednotky integrace dat

Jednotka Integration data je míra, která představuje napájení (kombinace procesoru, paměti a přidělení síťových prostředků) jedné jednotky v Azure Data Factory. Jednotka pro integraci dat platí jenom pro [prostředí Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime), ale ne pro místní [prostředí Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

Povolený DIUs k tomu, aby mohl provádět kopírování aktivit, je **mezi 2 a 256**. Pokud není zadaný nebo jste v uživatelském rozhraní zvolili "automaticky", Data Factory dynamicky aplikuje optimální nastavení DIÚ na základě páru zdrojových a datových vzorů. Následující tabulka uvádí podporované rozsahy DIÚ a výchozí chování v různých scénářích kopírování:

| Scénář kopírování | Podporovaný rozsah DIÚ | Výchozí DIUs určuje služba |
|:--- |:--- |---- |
| Mezi úložišti souborů |- **Kopírovat z nebo do jednoho souboru**: 2-4 <br>- **Kopírování z a do více souborů**: 2-256 v závislosti na počtu a velikosti souborů <br><br>Pokud například kopírujete data ze složky se 4 velkými soubory a zvolíte možnost zachovat hierarchii, maximální efektivní DIÚ je 16; Pokud se rozhodnete sloučit soubor, maximální efektivní DIÚ je 4. |Mezi 4 a 32 v závislosti na počtu a velikosti souborů |
| Z úložiště souborů do jiného než souborového úložiště |- **Kopírovat z jednoho souboru**: 2-4 <br/>- **Kopírovat z více souborů**: 2-256 v závislosti na počtu a velikosti souborů <br/><br/>Pokud například kopírujete data ze složky se 4 velkými soubory, maximální efektivní DIÚ je 16. |- **Kopírovat do Azure SQL Database nebo Azure Cosmos DB**: mezi 4 a 16 v závislosti na úrovni jímky (DTU/ru) a vzoru zdrojového souboru<br>- **Kopírování do Azure synapse Analytics** pomocí příkazu Base nebo Copy: 2<br>– Jiný scénář: 4 |
| Z jiného než souborového úložiště do úložiště souborů |- **Kopírování z úložišť dat s povolenými možnostmi pro oddíly** (včetně [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [spravované instance Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)a [Teradata](connector-teradata.md#teradata-as-source)): 2-256 při zápisu do složky a 2-4 při zápisu do jednoho jediného souboru. Poznámka: každý oddíl zdrojového datového oddílu může využívat až 4 DIUs.<br>- **Další scénáře**: 2-4 |- **Kopírovat z REST nebo http**: 1<br/>- **Kopírovat z Amazon RedShift** pomocí uvolnění: 2<br>- **Jiný scénář**: 4 |
| Mezi úložištěmi bez souborů |- **Kopírování z úložišť dat s povolenými možnostmi pro oddíly** (včetně [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [spravované instance Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)a [Teradata](connector-teradata.md#teradata-as-source)): 2-256 při zápisu do složky a 2-4 při zápisu do jednoho jediného souboru. Poznámka: každý oddíl zdrojového datového oddílu může využívat až 4 DIUs.<br/>- **Další scénáře**: 2-4 |- **Kopírovat z REST nebo http**: 1<br>- **Jiný scénář**: 4 |

V zobrazení monitorování nebo výstupu aktivity můžete zobrazit DIUs, která se používá pro každé spuštění kopírování. Další informace najdete v tématu [monitorování aktivit kopírování](copy-activity-monitoring.md). Chcete-li přepsat toto výchozí nastavení, zadejte hodnotu `dataIntegrationUnits` vlastnosti následujícím způsobem. *Skutečný počet DIUs* , který operace kopírování používá v době běhu, se rovná nebo je menší než nakonfigurovaná hodnota v závislosti na datovém vzoru.

Bude se vám účtovat počet **využitých \* jednotek doby trvání kopírování DIUs \* a cena za diú za hodinu**. [Tady se můžete](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)podívat na aktuální ceny. Pro každý typ předplatného se můžou použít místní měna a samostatná sleva.

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

## <a name="self-hosted-integration-runtime-scalability"></a>Škálovatelnost prostředí Integration runtime v místním prostředí

Pokud chcete dosáhnout vyšší propustnosti, můžete buď horizontální navýšení nebo horizontální navýšení kapacity v místním prostředí IR:

- Pokud procesor a dostupná paměť v uzlu IR v místním prostředí nejsou plně využívány, ale spuštění souběžných úloh dosáhlo limitu, měli byste škálovat kapacitu tak, že zvýšíte počet souběžných úloh, které lze spustit na uzlu.  Pokyny najdete [tady](create-self-hosted-integration-runtime.md#scale-up) .
- Pokud je na druhé straně procesor vysoký v místním prostředí IR nebo je dostupná paměť nízká, můžete přidat nový uzel, který vám umožní lépe škálovat zatížení napříč více uzly.  Pokyny najdete [tady](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

Poznámka: v následujících scénářích může spuštění jedné aktivity kopírování využívat několik uzlů IR s místním hostováním:

- Kopírování dat z úložišť založených na souborech v závislosti na počtu a velikosti souborů.
- Kopírovat data z úložiště dat s povolenými možnostmi oddílu ( [včetně Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [spravované instance Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [otevřeného centra SAP](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [tabulky SAP](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)a [Teradata](connector-teradata.md#teradata-as-source)) v závislosti na počtu datových oddílů.

## <a name="parallel-copy"></a>Paralelní kopírování

V aktivitě kopírování můžete nastavit paralelní kopírování ( `parallelCopies` vlastnost) a označit paralelismus, které má aktivita kopírování použít. Tuto vlastnost si můžete představit jako maximální počet vláken v rámci aktivity kopírování, která je načítána ze zdroje nebo zapisuje do úložiště dat jímky paralelně.

Paralelní kopírování je kolmé k [jednotkám integrace dat](#data-integration-units) nebo k [uzlům IR](#self-hosted-integration-runtime-scalability)v místním prostředí. Počítá se ve všech uzlech IR DIUs nebo v místním prostředí.

Při každém spuštění aktivity kopírování se ve výchozím nastavení Azure Data Factory dynamicky aplikuje optimální paralelní kopírování na základě páru zdrojových a datových vzorů. 

> [!TIP]
> Výchozí chování paralelní kopie obvykle poskytuje nejlepší propustnost, která se automaticky určuje pomocí ADF, a to na základě páru zdrojové-jímky, vzorce dat a počtu DIUs nebo procesoru, paměti nebo počtu procesorů v místním prostředí. V tématu [řešení potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md) v případě, kdy je nutné ladit paralelní kopírování.

V následující tabulce je uveden postup paralelního kopírování:

| Scénář kopírování | Chování paralelního kopírování |
| --- | --- |
| Mezi úložišti souborů | `parallelCopies` Určuje paralelismus **na úrovni souboru**. Bloky dat v každém souboru se nacházejí pod automatickým a transparentně. Je navržena tak, aby používala nejvhodnější velikost bloku dat pro daný typ úložiště dat k paralelnímu načtení dat. <br/><br/>Skutečný počet paralelních kopií, které aktivita kopírování používá v době běhu, není vyšší než počet souborů, které máte. Pokud je chování kopírování **mergeFile** do jímky souborů, aktivita kopírování nemůže využít paralelismus na úrovni souborů. |
| Z úložiště souborů do jiného než souborového úložiště | – Při kopírování dat do Azure SQL Database nebo Azure Cosmos DB závisí výchozí paralelní kopírování i na úrovni jímky (počet DTU/ru).<br>– Při kopírování dat do tabulky Azure je výchozí paralelní kopírování 4. |
| Z jiného než souborového úložiště do úložiště souborů | – Při kopírování dat z úložiště dat s povolenými možnostmi volby oddílu (včetně [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [spravované instance Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [otevřeného centra SAP](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [tabulky SAP](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)a [Teradata](connector-teradata.md#teradata-as-source)) je výchozí paralelní kopírování 4. Skutečný počet paralelních kopií, které aktivita kopírování používá v době běhu, není vyšší než počet oddílů dat, které máte. Pokud používáte místní Integration Runtime a kopírujete do Azure Blob/ADLS Gen2, poznamenejte si maximální efektivní paralelní kopírování 4 nebo 5 na uzel IR.<br>– Pro jiné scénáře se paralelní kopírování neprojeví. I v případě, že je zadán paralelismu, není použit. |
| Mezi úložištěmi bez souborů | – Při kopírování dat do Azure SQL Database nebo Azure Cosmos DB závisí výchozí paralelní kopírování i na úrovni jímky (počet DTU/ru).<br/>– Při kopírování dat z úložiště dat s povolenými možnostmi volby oddílu (včetně [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [spravované instance Azure SQL](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [otevřeného centra SAP](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [tabulky SAP](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)a [Teradata](connector-teradata.md#teradata-as-source)) je výchozí paralelní kopírování 4.<br>– Při kopírování dat do tabulky Azure je výchozí paralelní kopírování 4. |

Pro řízení zatížení počítačů, které hostují vaše úložiště dat nebo pro optimalizaci výkonu kopírování, můžete přepsat výchozí hodnotu a zadat hodnotu pro `parallelCopies` vlastnost. Hodnota musí být celé číslo větší nebo rovno 1. V době běhu používá aktivita kopírování hodnotu, která je menší nebo rovna hodnotě, kterou jste nastavili.

Když zadáte hodnotu `parallelCopies` vlastnosti, požádejte o navýšení zatížení pro úložiště dat zdroje a jímky v účtu. Zvažte také zvýšení zatížení v místním prostředí Integration runtime, pokud je aktivita kopírování oprávněná. Toto zvýšení zatížení nastane hlavně v případě, že máte více aktivit nebo souběžných spuštění stejných aktivit, které se spouštějí ve stejném úložišti dat. Pokud si všimnete, že úložiště dat nebo místní prostředí Integration runtime je zahlcené zatížením, snižte `parallelCopies` hodnotu pro uvolnění zátěže.

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

## <a name="staged-copy"></a>Připravené kopírování

Když kopírujete data ze zdrojového úložiště dat do úložiště dat jímky, můžete použít úložiště objektů BLOB jako dočasné pracovní úložiště. Příprava je užitečná hlavně v následujících případech:

- **Chcete ingestovat data z různých úložišť dat do služby Azure synapse Analytics (dříve SQL Data Warehouse) prostřednictvím základu.** Azure synapse Analytics používá základ jako mechanismus vysoké propustnosti k načtení velkého množství dat do Azure synapse Analytics. Zdrojová data musí být ve službě BLOB Storage nebo Azure Data Lake Store a musí splňovat další kritéria. Při načítání dat z jiného úložiště dat než do úložiště objektů BLOB nebo z Azure Data Lake Store můžete aktivovat kopírování dat přes dočasné pracovní úložiště objektů BLOB. V takovém případě Azure Data Factory provádí požadované transformace dat, aby se zajistilo, že splňuje požadavky základny. Pak používá základnu k efektivnímu načítání dat do služby Azure synapse Analytics. Další informace najdete v tématu [použití základny k načtení dat do služby Azure synapse Analytics](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics).
- **V některých případech trvá i v průběhu provádění hybridního přesunu dat (tedy kopírování z místního úložiště dat do cloudového úložiště dat) prostřednictvím pomalého síťového připojení.** Za účelem zvýšení výkonu můžete pomocí připravené kopie komprimovat data v místním prostředí, aby při přesunu dat do pracovního úložiště dat v cloudu trvalo méně času. Pak můžete data v pracovním úložišti dekomprimovat ještě předtím, než se načtou do cílového úložiště dat.
- **Nechcete v bráně firewall otevírat jiné porty než port 80 a port 443 kvůli podnikovým zásadám IT.** Když například kopírujete data z místního úložiště dat do jímky Azure SQL Database nebo do jímky služby Azure synapse Analytics, musíte aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows Firewall i firemní bránu firewall. V tomto scénáři může připravené kopírování využít výhod místního prostředí Integration runtime k prvnímu kopírování dat do pracovní instance úložiště objektů BLOB přes protokol HTTP nebo HTTPS na portu 443. Pak může data načíst do SQL Database nebo Azure synapse Analytics z přípravy úložiště objektů BLOB. V tomto toku nemusíte povolit port 1433.

### <a name="how-staged-copy-works"></a>Jak funguje dvoufázové kopírování

Když aktivujete pracovní funkci, nejdřív se data zkopírují ze zdrojového úložiště dat do pracovního úložiště objektů BLOB (Přineste si vlastní). V dalším kroku se data zkopírují z pracovního úložiště dat do úložiště dat jímky. Azure Data Factory pro vás automaticky spravuje tok se dvěma fázemi. Po dokončení přesunu dat Azure Data Factory taky vyčistit dočasná data z pracovního úložiště.

![Připravené kopírování](media/copy-activity-performance/staged-copy.png)

Když provedete přesun dat pomocí pracovního úložiště, můžete určit, jestli chcete data zkomprimovat před přesunem dat ze zdrojového úložiště dat do dočasného nebo přípravného úložiště dat a pak je dekomprimovat předtím, než přesunete data z dočasného nebo přípravného úložiště dat do úložiště dat jímky.

V současné době nemůžete kopírovat data mezi dvěma datovými úložišti, která jsou propojena prostřednictvím jiného samoobslužného úřadu pro hostování, ani bez dvoufázové kopie. V takovém případě můžete nakonfigurovat dvě explicitně zřetězené aktivity kopírování ke kopírování ze zdroje do přípravy, a to z přípravy do jímky.

### <a name="configuration"></a>Konfigurace

Nakonfigurujte nastavení **enableStaging** v aktivitě kopírování, abyste určili, jestli chcete data připravit v úložišti objektů blob, než je načtete do cílového úložiště dat. Při nastavování **enableStaging** na `TRUE` Zadejte další vlastnosti uvedené v následující tabulce. Je také potřeba vytvořit sdílenou službu Azure Storage nebo sdílený přístupový podpis s úložištěm pro přípravu, pokud ji ještě nemáte.

| Vlastnost | Popis | Výchozí hodnota | Vyžadováno |
| --- | --- | --- | --- |
| enableStaging |Určete, zda chcete kopírovat data prostřednictvím dočasného přípravného úložiště. |Nepravda |No |
| linkedServiceName |Zadejte název propojené služby [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) , která odkazuje na instanci úložiště, kterou používáte jako dočasné pracovní úložiště. <br/><br/> Úložiště se sdíleným přístupovým podpisem se nedá použít k načtení dat do služby Azure synapse Analytics prostřednictvím základu. Můžete ho použít ve všech ostatních scénářích. |– |Ano, pokud je **enableStaging** nastavené na true |
| program |Zadejte cestu k úložišti objektů blob, kterou chcete, aby obsahovala zpracovaná data. Pokud cestu nezadáte, služba vytvoří kontejner, do kterého budou ukládat dočasná data. <br/><br/> Zadejte cestu pouze v případě, že používáte úložiště se sdíleným přístupovým podpisem, nebo pokud chcete, aby byla dočasná data v určitém umístění. |– |No |
| Hodnotou EnableCompression |Určuje, zda mají být data před kopírováním do cíle komprimována. Toto nastavení snižuje objem přenášených dat. |Nepravda |No |

>[!NOTE]
> Pokud použijete připravené kopírování s povolenou kompresí, instanční objekt nebo ověřování MSI pro propojenou službu pracovního objektu BLOB se nepodporuje.

Tady je ukázková definice aktivity kopírování s vlastnostmi popsanými v předchozí tabulce:

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

### <a name="staged-copy-billing-impact"></a>Dopad na fázi kopírování při fakturaci

Účtují se vám poplatky podle dvou kroků: doba kopírování a typ kopírování.

* Při použití přípravy během kopírování do cloudu, který kopíruje data z cloudového úložiště dat do jiného cloudového úložiště dat, se v obou fázích, které zmocňuje prostředí Azure Integration runtime, účtují [součet doby kopírování pro krok 1 a krok 2] x [cena za jednotku cloudového kopírování].
* Když použijete fázování během hybridní kopie, která kopíruje data z místního úložiště dat do cloudového úložiště dat, jedna fáze, kterou používá místní prostředí Integration runtime, vám bude účtována za [doba trvání hybridního kopírování] × [cena za jednotku Hybrid Copy] + [doba kopírování v cloudu] x [cena za jednotku cloudové kopie].

## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Průvodce škálovatelností a výkonem aktivity kopírování](copy-activity-performance.md)
- [Řešení potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md)
- [Použití Azure Data Factory k migraci dat ze služby Data Lake nebo datového skladu do Azure](data-migration-guidance-overview.md)
- [Migrace dat z Amazonu S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md)