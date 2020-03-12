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
ms.date: 03/09/2020
ms.openlocfilehash: a31c6229220142acea9ded571128ab54c50d34b7
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125781"
---
# <a name="copy-activity-performance-optimization-features"></a>Funkce optimalizace výkonu aktivity kopírování

Tento článek popisuje funkce optimalizace výkonu aktivity kopírování, které můžete využít v Azure Data Factory.

## <a name="data-integration-units"></a>Jednotky pro integraci dat

Jednotka Integration data je míra, která představuje napájení (kombinace procesoru, paměti a přidělení síťových prostředků) jedné jednotky v Azure Data Factory. Jednotka pro integraci dat platí jenom pro [prostředí Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime), ale ne pro místní [prostředí Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

Povolený DIUs k tomu, aby mohl provádět kopírování aktivit, je **mezi 2 a 256**. Pokud není zadaný nebo jste v uživatelském rozhraní zvolili "automaticky", Data Factory dynamicky použít optimální nastavení DIÚ na základě páru zdroj-jímka a datového vzoru. Následující tabulka uvádí podporované rozsahy DIÚ a výchozí chování v různých scénářích kopírování:

| Kopírování | Podporovaný rozsah DIÚ | Výchozí DIUs určené služby |
|:--- |:--- |---- |
| Mezi úložišti souborů |- **kopírování z nebo do jednoho souboru**: 2-4 <br>- **Kopírovat z a do více souborů**: 2-256 v závislosti na počtu a velikosti souborů <br><br>Pokud například kopírujete data ze složky se 4 velkými soubory a zvolíte možnost zachovat hierarchii, maximální efektivní DIÚ je 16; Pokud se rozhodnete sloučit soubor, maximální efektivní DIÚ je 4. |Mezi 4 a 32 v závislosti na počtu a velikosti souborů |
| Z úložiště souborů do jiného než souborového úložiště |- **Kopírovat z jednoho souboru**: 2-4 <br/>- **Kopírovat z více souborů**: 2-256 v závislosti na počtu a velikosti souborů <br/><br/>Pokud například kopírujete data ze složky se 4 velkými soubory, maximální efektivní DIÚ je 16. |- **Kopírovat do Azure SQL Database nebo Azure Cosmos DB**: mezi 4 a 16 v závislosti na úrovni jímky (DTU/ru) a vzoru zdrojového souboru<br>- **ke kopírování do Azure synapse Analytics** pomocí příkazu Base nebo Copy: 2<br>– Jiný scénář: 4 |
| Z jiného než souborového úložiště do úložiště souborů |- **kopírování z úložišť dat s povolenými možnostmi pro oddíly** (včetně [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 při zápisu do složky a 2-4 při zápisu do jednoho jediného souboru. Poznámka: každý oddíl zdrojového datového oddílu může využívat až 4 DIUs.<br>- **dalších scénářích**: 2-4 |- **kopírování z REST nebo http**: 1<br/>- **Kopírovat z Amazon RedShift** pomocí uvolnění: 2<br>- **jiný scénář**: 4 |
| Mezi úložištěmi bez souborů |- **kopírování z úložišť dat s povolenými možnostmi pro oddíly** (včetně [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 při zápisu do složky a 2-4 při zápisu do jednoho jediného souboru. Poznámka: každý oddíl zdrojového datového oddílu může využívat až 4 DIUs.<br/>- **dalších scénářích**: 2-4 |- **kopírování z REST nebo http**: 1<br>- **jiný scénář**: 4 |

V zobrazení monitorování nebo výstupu aktivity můžete zobrazit DIUs, která se používá pro každé spuštění kopírování. Další informace najdete v tématu [monitorování aktivit kopírování](copy-activity-monitoring.md). Chcete-li přepsat toto výchozí nastavení, zadejte hodnotu vlastnosti `dataIntegrationUnits` následujícím způsobem. *Skutečný počet DIUs* , který operace kopírování používá v době běhu, se rovná nebo je menší než nakonfigurovaná hodnota v závislosti na datovém vzoru.

Bude se vám účtovat počet **využitých DIUs \* doba kopírování \* Jednotková cena/hodina diú**. [Tady se můžete](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)podívat na aktuální ceny. Pro každý typ předplatného se můžou použít místní měna a samostatná sleva.

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
- Kopírování dat z úložiště dat s povolenými možnostmi volby oddílu (včetně [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [tabulky SAP](connector-sap-table.md#sap-table-as-source)a [otevřeného centra SAP](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) v závislosti na počtu datových oddílů.

## <a name="parallel-copy"></a>Paralelní kopírování

Můžete nastavit paralelní kopírování (`parallelCopies` vlastnost) pro aktivitu kopírování a označit paralelismus, které má aktivita kopírování použít. Tuto vlastnost si můžete představit jako maximální počet vláken v rámci aktivity kopírování, která je načítána ze zdroje nebo zapisuje do úložiště dat jímky paralelně.

Paralelní kopírování je kolmé k [jednotkám integrace dat](#data-integration-units) nebo k [uzlům IR](#self-hosted-integration-runtime-scalability)v místním prostředí. Počítá se ve všech uzlech IR DIUs nebo v místním prostředí.

Pro každou spuštěnou aktivitu kopírování se ve výchozím nastavení Azure Data Factory dynamicky aplikují optimální paralelní kopírování na základě páru zdrojových a datových vzorů. 

> [!TIP]
> Výchozí chování paralelní kopie obvykle poskytuje nejlepší propustnost, která se automaticky určuje pomocí ADF, a to na základě páru zdrojové-jímky, vzorce dat a počtu DIUs nebo procesoru, paměti nebo počtu procesorů v místním prostředí. V tématu [řešení potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md) v případě, kdy je nutné ladit paralelní kopírování.

V následující tabulce je uveden postup paralelního kopírování:

| Kopírování | Chování paralelního kopírování |
| --- | --- |
| Mezi úložišti souborů | `parallelCopies` určuje paralelismus **na úrovni souboru**. Bloky dat v každém souboru se nacházejí pod automatickým a transparentně. Je navržena tak, aby používala nejvhodnější velikost bloku dat pro daný typ úložiště dat k paralelnímu načtení dat. <br/><br/>Skutečný počet paralelních kopií, které aktivita kopírování používá v době běhu, není vyšší než počet souborů, které máte. Pokud je chování kopírování **mergeFile** do jímky souborů, aktivita kopírování nemůže využít paralelismus na úrovni souborů. |
| Z úložiště souborů do jiného než souborového úložiště | – Při kopírování dat do Azure SQL Database nebo Azure Cosmos DB závisí výchozí paralelní kopírování i na úrovni jímky (počet DTU/ru).<br>– Při kopírování dat do tabulky Azure je výchozí paralelní kopírování 4. |
| Z jiného než souborového úložiště do úložiště souborů | – Při kopírování dat z úložiště dat s povolenými možnostmi volby oddílu (včetně [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [tabulky SAP](connector-sap-table.md#sap-table-as-source)a [otevřeného centra SAP](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) je výchozí paralelní kopírování 4. Skutečný počet paralelních kopií, které aktivita kopírování používá v době běhu, není vyšší než počet oddílů dat, které máte. Pokud používáte místní Integration Runtime a kopírujete do Azure Blob/ADLS Gen2, poznamenejte si maximální efektivní paralelní kopírování 4 nebo 5 na uzel IR.<br>– Pro jiné scénáře se paralelní kopírování neprojeví. I v případě, že je zadán paralelismu, není použit. |
| Mezi úložištěmi bez souborů | – Při kopírování dat do Azure SQL Database nebo Azure Cosmos DB závisí výchozí paralelní kopírování i na úrovni jímky (počet DTU/ru).<br/>– Při kopírování dat do tabulky Azure je výchozí paralelní kopírování 4. |

Pro řízení zatížení počítačů, které hostují vaše úložiště dat nebo pro optimalizaci výkonu kopírování, můžete přepsat výchozí hodnotu a zadat hodnotu pro vlastnost `parallelCopies`. Hodnota musí být celé číslo větší než nebo rovno 1. V době běhu používá aktivita kopírování hodnotu, která je menší nebo rovna hodnotě, kterou jste nastavili.

Když zadáte hodnotu vlastnosti `parallelCopies`, požádejte o zvýšení zátěže na vaše zdrojová data a úložiště dat jímky. Zvažte také zvýšení zatížení v místním prostředí Integration runtime, pokud je aktivita kopírování oprávněná. Toto zvýšení zatížení nastane hlavně v případě, že máte více aktivit nebo souběžných spuštění stejných aktivit, které se spouštějí ve stejném úložišti dat. Pokud si všimnete, že úložiště dat nebo místní prostředí Integration runtime je zahlcené zatížením, snižte `parallelCopies` hodnotu pro uvolnění zátěže.

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

## <a name="staged-copy"></a>Kopírování dvoufázové instalace

Při kopírování dat ze zdrojového úložiště dat do úložiště dat jímky, můžete zvolit použití Blob storage jako dočasné pracovní úložiště. Pracovní je zvláště užitečná v následujících případech:

- **Chcete ingestovat data z různých úložišť dat do SQL Data Warehouse prostřednictvím základny.** SQL Data Warehouse používá k načtení velkých objemů dat do SQL Data Warehouse PolyBase jako vhodný mechanismus vysokou propustnost. Zdrojová data musí být ve službě BLOB Storage nebo Azure Data Lake Store a musí splňovat další kritéria. Při načítání dat z úložiště dat než Blob storage nebo Azure Data Lake Store můžete aktivovat kopírování prostřednictvím Blob storage dočasné pracovní data. V takovém případě Azure Data Factory provádí požadované transformace dat, aby se zajistilo, že splňuje požadavky základny. Potom použije PolyBase k načtení dat do SQL Data Warehouse efektivně. Další informace najdete v tématu [použití základny k načtení dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **V některých případech trvá i v průběhu provádění hybridního přesunu dat (tedy kopírování z místního úložiště dat do cloudového úložiště dat) prostřednictvím pomalého síťového připojení.** Za účelem zvýšení výkonu můžete pomocí připravené kopie komprimovat data v místním prostředí, aby při přesunu dat do pracovního úložiště dat v cloudu trvalo méně času. Pak můžete data v pracovním úložišti dekomprimovat ještě předtím, než se načtou do cílového úložiště dat.
- **Nechcete v bráně firewall otevírat jiné porty než port 80 a port 443 kvůli podnikovým zásadám IT.** Například při kopírování dat z do místního úložiště dat jímky Azure SQL Database nebo Azure SQL Data Warehouse jímky, budete muset aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows firewall a váš podnikový firewall. V tomto scénáři může připravené kopírování využít výhod místního prostředí Integration runtime k prvnímu kopírování dat do pracovní instance úložiště objektů BLOB přes protokol HTTP nebo HTTPS na portu 443. Pak může data načíst do SQL Database nebo SQL Data Warehouse z přípravy úložiště objektů BLOB. V tomto toku není nutné povolit port 1433.

### <a name="how-staged-copy-works"></a>Kopírování funguje jak dvoufázové instalace

Při aktivaci pracovní funkce data se zkopíruje ze zdrojového úložiště dat do přípravného úložiště objektů Blob (funkce přineste si vlastní). V dalším kroku data zkopírována z pracovní úložiště dat do úložiště dat jímky. Azure Data Factory pro vás automaticky spravuje tok se dvěma fázemi. Po dokončení přesunu dat Azure Data Factory taky vyčistit dočasná data z pracovního úložiště.

![Kopírování dvoufázové instalace](media/copy-activity-performance/staged-copy.png)

Když provedete přesun dat pomocí pracovního úložiště, můžete určit, jestli chcete data před přesunem dat ze zdrojového úložiště dat do dočasného nebo přípravného úložiště dat a pak je dekomprimovat, před přesunem dat z dočasného nebo přípravného dat. úložiště dat jímky.

V současné době nemůžete kopírovat data mezi dvěma datovými úložišti, která jsou propojena prostřednictvím jiného samoobslužného úřadu pro hostování, ani bez dvoufázové kopie. V takovém případě můžete nakonfigurovat dvě explicitně zřetězené aktivity kopírování ke kopírování ze zdroje do přípravy, a to z přípravy do jímky.

### <a name="configuration"></a>Konfigurace

Nakonfigurujte nastavení **enableStaging** v aktivitě kopírování, abyste určili, jestli chcete data připravit v úložišti objektů blob, než je načtete do cílového úložiště dat. Při nastavování **enableStaging** na `TRUE`zadejte další vlastnosti uvedené v následující tabulce. Je také potřeba vytvořit sdílenou službu Azure Storage nebo sdílený přístupový podpis s úložištěm pro přípravu, pokud ji ještě nemáte.

| Vlastnost | Popis | Výchozí hodnota | Požaduje se |
| --- | --- | --- | --- |
| enableStaging |Určete, jestli chcete kopírovat data prostřednictvím jako dočasné pracovní úložiště. |False |Ne |
| linkedServiceName |Zadejte název propojené služby [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) , která odkazuje na instanci úložiště, kterou používáte jako dočasné pracovní úložiště. <br/><br/> Úložiště se sdíleným přístupovým podpisem nelze použít k načtení dat do SQL Data Warehouse prostřednictvím základny. Můžete ji použít v jiných scénářích. |neuvedeno |Ano, pokud je **enableStaging** nastavené na true |
| path |Zadejte cestu úložiště objektů Blob, který chcete s daty, dvoufázové instalace. Pokud cestu nezadáte, služba vytvoří kontejner, do kterého budou ukládat dočasná data. <br/><br/> Zadejte cestu, pouze v případě, že používáte úložiště pomocí sdíleného přístupového podpisu nebo vyžadujete dočasných dat v konkrétním umístění. |neuvedeno |Ne |
| Hodnotou EnableCompression |Určuje, zda mají být data před kopírováním do cíle komprimována. Toto nastavení omezuje objem dat přenášených. |False |Ne |

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

### <a name="staged-copy-billing-impact"></a>Fázovaného kopírování fakturace dopad

Účtují se vám poplatky podle dvou kroků: doba kopírování a typ kopírování.

* Při použití přípravy během kopírování do cloudu, který kopíruje data z cloudového úložiště dat do jiného cloudového úložiště dat, se v obou fázích, které zmocňuje prostředí Azure Integration runtime, účtují [součet doby kopírování pro krok 1 a krok 2] x [cena za jednotku cloudového kopírování].
* Při použití přípravy během hybridní kopie, která kopíruje data z místního úložiště dat do cloudového úložiště dat, je jedna fáze, kterou má samoobslužný modul runtime integrace v místním prostředí, účtována za [doba trvání hybridního kopírování] × [cena za jednotku Hybrid Copy] + [doba kopírování v cloudu] x [Jednotková cena za kopii v cloudu].

## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Průvodce škálovatelností a výkonem aktivity kopírování](copy-activity-performance.md)
- [Řešení potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md)
- [Použití Azure Data Factory k migraci dat ze služby Data Lake nebo datového skladu do Azure](data-migration-guidance-overview.md)
- [Migrace dat ze služby Amazon S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md)