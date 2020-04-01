---
title: Poradce při potížích s výkonem aktivity kopírování
description: Přečtěte si, jak řešit potíže s výkonem aktivity kopírování v Azure Data Factory.
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
ms.date: 03/11/2020
ms.openlocfilehash: a14f4d548053fb7aaf6f450176fdc49bc7b119bf
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421039"
---
# <a name="troubleshoot-copy-activity-performance"></a>Poradce při potížích s výkonem aktivity kopírování

Tento článek popisuje, jak řešit problém s výkonem aktivity kopírování v Azure Data Factory. 

Po spuštění aktivity kopírování můžete shromažďovat statistiky výsledků spuštění a výkonu v zobrazení [sledování aktivity kopírování.](copy-activity-monitoring.md) Následuje příklad.

![Sledování podrobností spuštění aktivity kopírování](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Tipy pro ladění výkonu

V některých případech při spuštění aktivity kopírování v datové továrně, uvidíte **"Tipy pro optimalizaci výkonu"** v horní části, jak je znázorněno ve výše uvedeném příkladu. Tipy vám řeknou kritické místo identifikované adf pro tento konkrétní spuštění kopie, spolu s návrhem, jak zvýšit propustnost kopírování. Zkuste změnu s příkazem přeposanou a potom kopii spusťte znovu.

Jako odkaz v současné době tipy pro ladění výkonu poskytují návrhy pro následující případy:

| Kategorie              | Tipy pro ladění výkonu                                      |
| --------------------- | ------------------------------------------------------------ |
| Specifické pro úložiště dat   | Načítání dat do **Azure Synpase Analytics (dříve SQL DW):** navrhnout použití příkazu PolyBase nebo COPY, pokud se nepoužívá. |
| &nbsp;                | Kopírování dat z/do **Azure SQL Database:** když je DTU s vysokým využitím, navrhněte upgrade na vyšší úroveň. |
| &nbsp;                | Kopírování dat z/do **Azure Cosmos DB**: když je Ru s vysokým využitím, navrhněte upgrade na větší Ru. |
| &nbsp;                | Požití dat z **Amazon Redshift**: doporučujeme použít UNLOAD, pokud se nepoužívá. |
| Omezení úložiště dat | Pokud je během kopírování omezen počet operací čtení a zápisu v úložišti dat, navrhněte kontrolu a zvyšte povolenou rychlost požadavků pro úložiště dat nebo snižte souběžné zatížení. |
| Doba runtime integrace  | Pokud používáte **prostředí Runtime integrace s vlastním hostitelem (IR)** a aktivita kopírování čeká dlouho ve frontě, dokud infračervený přenos nebude mít k dispozici prostředek ke spuštění, navrhněte horizontální navýšení kapacity/ zvýšení infračerveného zařízení. |
| &nbsp;                | Pokud používáte **prostředí Azure Integration Runtime,** který je v neoptimální oblasti, což vede k pomalému čtení a zápisu, navrhněte konfiguraci pro použití infračerveného prostředí v jiné oblasti. |
| Odolnost proti chybám       | Pokud nakonfigurujete odolnost proti chybám a přeskočení nekompatibilní řádky má za následek pomalý výkon, navrhnout zajištění zdroj a jímky data jsou kompatibilní. |
| Fázovaná kopie           | Pokud je fázovaná kopie nakonfigurována, ale není užitečná pro dvojici zdrojového jímky, navrhněte ji odebrat. |
| Obnovit                | Při obnovení aktivity kopírování z posledního bodu selhání, ale dojde ke změně nastavení DIU po původním spuštění, všimněte si, že nové nastavení DIU se neprojeví. |

## <a name="understand-copy-activity-execution-details"></a>Porozumět podrobnostem spuštění aktivity kopírování

Podrobnosti o spuštění a doby trvání v dolní části zobrazení sledování aktivity kopírování popisuje klíčové fáze, kterými prochází vaše aktivita kopírování (viz příklad na začátku tohoto článku), což je zvláště užitečné pro řešení potíží s výkonem kopírování. Kritickým bodem spuštění kopírování je ten s nejdelší dobou trvání. Podívejte se na následující tabulku v definici každé fáze a [zjistěte,](#troubleshoot-copy-activity-on-azure-ir) jak řešit potíže s aktivitou kopírování v Azure IR a poradce při [potížích s kopírováním na samoobslužné infračervené oddělovací chodsu](#troubleshoot-copy-activity-on-self-hosted-ir) s těmito informacemi.

| Krok           | Popis                                                  |
| --------------- | ------------------------------------------------------------ |
| Fronta           | Uplynulý čas, dokud aktivita kopírování skutečně spustí v době spuštění integrace. |
| Skript předběžného kopírování | Uplynulý čas mezi kopírování aktivity začínající na infračerveného odznažení a kopírování aktivity dokončení provádění skriptu předkopírování minv v úložišti dat jímky. Použít při konfiguraci skriptu předkopírování pro jímky databáze, například při zápisu dat do Azure SQL Database provést vyčištění před zkopírováním nových dat. |
| Přenos        | Uplynulý čas mezi koncem předchozího kroku a infračerveným přenosem všech dat ze zdroje do jímky. Dílčí kroky v části "Přenos" běží paralelně.<br><br>- **Čas do prvního bajtu:** Čas, který uplynul mezi koncem předchozího kroku a časem, kdy infračervený přenos obdrží první bajt ze zdrojového úložiště dat. Platí pro zdroje nezaložené na souborech.<br>- **Zdroj výpisu:** Množství času stráveného výčet zdrojových souborů nebo datových oddílů. To platí při konfiguraci možností oddílu pro databázové zdroje, například při kopírování dat z databází, jako je Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Čtení ze zdroje:** Množství času stráveného načítáním dat ze zdrojového úložiště dat.<br/>- **Zápis do potoce:** Množství času stráveného na psaní dat do úložiště dat jímky. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Poradce při potížích s aktivitou kopírování v azure ir

Postupujte podle [kroků optimalizace výkonu](copy-activity-performance.md#performance-tuning-steps) a naplánujte a proveďte test výkonu pro váš scénář. 

Pokud výkon aktivity kopírování nesplňuje vaše očekávání, chcete-li vyřešit jednu aktivitu kopírování spuštěnou v prostředí Azure Integration Runtime, pokud se v zobrazení sledování kopírování zobrazí [tipy pro optimalizaci výkonu,](#performance-tuning-tips) použijte návrh a zkuste to znovu. V opačném případě [pochopte podrobnosti spuštění aktivity kopírování](#understand-copy-activity-execution-details), zkontrolujte, která fáze má **nejdelší** dobu trvání, a použijte níže uvedené pokyny pro zvýšení výkonu kopírování:

- **"Pre-copy script" zkušený dlouhou dobu trvání:** to znamená, že předkopírování skript spuštěný v databázi jímky trvá dlouho dokončit. Vylaďte zadanou logiku skriptu před kopírováním, abyste zvýšili výkon. Pokud potřebujete další pomoc při vylepšování skriptu, obraťte se na databázový tým.

- **"Přenos - doba do prvního bajtu" zkušený dlouhou dobu trvání :** to znamená, že zdrojový dotaz trvá dlouho vrátit všechna data. Zkontrolujte a optimalizujte dotaz nebo server. Pokud potřebujete další pomoc, obraťte se na tým úložiště dat.

- **"Přenos - Výpis zdroje" zkušený dlouhou dobu trvání práce**: to znamená, že výčet zdrojových souborů nebo zdrojové databáze datových oddílů je pomalý.

  - Při kopírování dat ze zdroje založeného na souborech, pokud používáte`wildcardFolderPath` `wildcardFileName`filtr **se zástupnými kódy** na cestě ke složce nebo názvu souboru ( nebo ), nebo použít **soubor naposledy upravený časový filtr** (`modifiedDatetimeStart` nebo`modifiedDatetimeEnd`), všimněte si, že takový filtr by měl za následek kopírování aktivity výpis všech souborů v zadané složce na straně klienta pak použít filtr. Takový výčet souborů by se mohl stát kritickým bodem, zejména pokud pravidlo filtru splnila pouze malá sada souborů.

    - Zkontrolujte, zda můžete [kopírovat soubory na základě datetime rozdělené cesty nebo názvu souboru](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Takový způsob nepřináší zátěž na výpis zdroj straně.

    - Zkontrolujte, jestli místo toho můžete použít nativní filtr úložiště dat, konkrétně **"předponu**" pro Amazon S3 a Azure Blob. Předpona filtr je úložiště dat na straně serveru filtr a bude mít mnohem lepší výkon.

    - Zvažte rozdělení jedné velké datové sady do několika menších datových sad a nechte tyto úlohy kopírování běžet souběžně, každá část dat řeší. Můžete to provést s Vyhledávání/GetMetadata + ForEach + Kopie. Odkazovat na [kopírovat soubory z více kontejnerů](solution-template-copy-files-multiple-containers.md) nebo [migrovat data z Amazon S3 na Šablony řešení ADLS Gen2](solution-template-migration-s3-azure.md) jako obecný příklad.

  - Zkontrolujte, zda adf hlásí všechny chyby omezení na zdroj nebo pokud úložiště dat je ve stavu vysoké využití. Pokud ano, snižte úlohy v úložišti dat nebo se pokuste kontaktovat správce úložiště dat, abyste zvýšili limit omezení nebo dostupný prostředek.

  - Azure IR použijte ve stejné oblasti úložiště zdrojových dat nebo v jejich blízkosti.

- **"Transfer - čtení ze zdroje" zkušený dlouhou pracovní dobu**: 

  - Pokud platí, přijměte osvědčený postup načítání dat specifických pro konektor. Například při kopírování dat z [Amazon Redshift](connector-amazon-redshift.md), nakonfigurujte použít Redshift UNLOAD.

  - Zkontrolujte, zda adf hlásí všechny chyby omezení na zdroj nebo pokud úložiště dat je pod vysokou využití. Pokud ano, snižte úlohy v úložišti dat nebo se pokuste kontaktovat správce úložiště dat, abyste zvýšili limit omezení nebo dostupný prostředek.

  - Zkontrolujte zdroj kopie a vzorek jímky: 

    - Pokud váš vzor kopírování podporuje větší než 4 jednotky integrace dat (DIU) - viz [tato část](copy-activity-performance-features.md#data-integration-units) na podrobnosti, obecně můžete zkusit zvýšení DIU získat lepší výkon. 

    - V opačném případě zvažte rozdělení jedné velké datové sady do několika menších datových sad a nechte tyto úlohy kopírování běžet souběžně, každá část dat řeší. Můžete to provést s Vyhledávání/GetMetadata + ForEach + Kopie. Viz [Kopírovat soubory z více kontejnerů](solution-template-copy-files-multiple-containers.md), [migrovat data z Amazon S3 na ADLS Gen2](solution-template-migration-s3-azure.md)nebo [hromadné kopírování se šablonami](solution-template-bulk-copy-with-control-table.md) řešení ovládací tabulky jako obecný příklad.

  - Azure IR použijte ve stejné oblasti úložiště zdrojových dat nebo v jejich blízkosti.

- **"Transfer - psaní do umyvadla" zkušený dlouhou dobu trvání :**

  - Pokud platí, přijměte osvědčený postup načítání dat specifických pro konektor. Například při kopírování dat do [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (dříve SQL DW), použijte Příkaz PolyBase nebo COPY. 

  - Zkontrolujte, zda adf hlásí všechny chyby omezení na jímce nebo pokud úložiště dat je pod vysokou využití. Pokud ano, snižte úlohy v úložišti dat nebo se pokuste kontaktovat správce úložiště dat, abyste zvýšili limit omezení nebo dostupný prostředek.

  - Zkontrolujte zdroj kopie a vzorek jímky: 

    - Pokud váš vzor kopírování podporuje větší než 4 jednotky integrace dat (DIU) - viz [tato část](copy-activity-performance-features.md#data-integration-units) na podrobnosti, obecně můžete zkusit zvýšení DIU získat lepší výkon. 

    - V opačném případě postupně naladit [paralelní kopie](copy-activity-performance-features.md), všimněte si, že příliš mnoho paralelních kopií může dokonce poškodit výkon.

  - Použijte Azure IR ve stejné nebo v blízkosti oblasti úložiště dat jímky.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Poradce při potížích s aktivitou kopírování v samoobslužné infračervené službě

Postupujte podle [kroků optimalizace výkonu](copy-activity-performance.md#performance-tuning-steps) a naplánujte a proveďte test výkonu pro váš scénář. 

Pokud výkon kopírování nesplňuje vaše očekávání, chcete-li vyřešit jednu aktivitu kopírování spuštěnou v prostředí Azure Integration Runtime, pokud se v zobrazení sledování kopírování zobrazí [tipy pro optimalizaci výkonu,](#performance-tuning-tips) použijte návrh a zkuste to znovu. V opačném případě [pochopte podrobnosti spuštění aktivity kopírování](#understand-copy-activity-execution-details), zkontrolujte, která fáze má **nejdelší** dobu trvání, a použijte níže uvedené pokyny pro zvýšení výkonu kopírování:

- **"Fronta" došlo k dlouhé trvání:** znamená, že aktivita kopírování čeká dlouho ve frontě, dokud vaše refračerveného přenosové operace hostovaného na vlastní seznam nebude mít prostředek ke spuštění. Zkontrolujte kapacitu a využití infračerveného zařízení a [navýšiněte nebo zvětšete](create-self-hosted-integration-runtime.md#high-availability-and-scalability) kapacitu podle vašeho pracovního vytížení.

- **"Přenos - doba do prvního bajtu" zkušený dlouhou dobu trvání :** to znamená, že zdrojový dotaz trvá dlouho vrátit všechna data. Zkontrolujte a optimalizujte dotaz nebo server. Pokud potřebujete další pomoc, obraťte se na tým úložiště dat.

- **"Přenos - Výpis zdroje" zkušený dlouhou dobu trvání práce**: to znamená, že výčet zdrojových souborů nebo zdrojové databáze datových oddílů je pomalý.

  - Zkontrolujte, jestli samoobslužný infračervený počítač má nízkou latenci připojení k úložišti zdrojových dat. Pokud je váš zdroj v Azure, můžete [pomocí tohoto nástroje](http://www.azurespeed.com/Azure/Latency) zkontrolovat latenci z počítače infračerveného počítače hostovaného samoobslužným počítačem do oblasti Azure, čím méně, tím lépe.

  - Při kopírování dat ze zdroje založeného na souborech, pokud používáte`wildcardFolderPath` `wildcardFileName`filtr **se zástupnými kódy** na cestě ke složce nebo názvu souboru ( nebo ), nebo použít **soubor naposledy upravený časový filtr** (`modifiedDatetimeStart` nebo`modifiedDatetimeEnd`), všimněte si, že takový filtr by měl za následek kopírování aktivity výpis všech souborů v zadané složce na straně klienta pak použít filtr. Takový výčet souborů by se mohl stát kritickým bodem, zejména pokud pravidlo filtru splnila pouze malá sada souborů.

    - Zkontrolujte, zda můžete [kopírovat soubory na základě datetime rozdělené cesty nebo názvu souboru](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Takový způsob nepřináší zátěž na výpis zdroj straně.

    - Zkontrolujte, jestli místo toho můžete použít nativní filtr úložiště dat, konkrétně **"předponu**" pro Amazon S3 a Azure Blob. Předpona filtr je úložiště dat na straně serveru filtr a bude mít mnohem lepší výkon.

    - Zvažte rozdělení jedné velké datové sady do několika menších datových sad a nechte tyto úlohy kopírování běžet souběžně, každá část dat řeší. Můžete to provést s Vyhledávání/GetMetadata + ForEach + Kopie. Odkazovat na [kopírovat soubory z více kontejnerů](solution-template-copy-files-multiple-containers.md) nebo [migrovat data z Amazon S3 na Šablony řešení ADLS Gen2](solution-template-migration-s3-azure.md) jako obecný příklad.

  - Zkontrolujte, zda adf hlásí všechny chyby omezení na zdroj nebo pokud úložiště dat je ve stavu vysoké využití. Pokud ano, snižte úlohy v úložišti dat nebo se pokuste kontaktovat správce úložiště dat, abyste zvýšili limit omezení nebo dostupný prostředek.

- **"Transfer - čtení ze zdroje" zkušený dlouhou pracovní dobu**: 

  - Zkontrolujte, jestli samoobslužný infračervený počítač má nízkou latenci připojení k úložišti zdrojových dat. Pokud je váš zdroj v Azure, můžete [pomocí tohoto nástroje](http://www.azurespeed.com/Azure/Latency) zkontrolovat latenci z počítače infračerveného počítače hostovaného samoobslužným počítačem do oblastí Azure, čím méně, tím lépe.

  - Zkontrolujte, zda má samoobslužný infračervený počítač dostatečnou příchozí šířku pásma pro efektivní čtení a přenos dat. Pokud je úložiště zdrojových dat v Azure, můžete [pomocí tohoto nástroje](https://www.azurespeed.com/Azure/Download) zkontrolovat rychlost stahování.

  - Zkontrolujte, zda samoobslužné INFRAčerveného procesoru a využití paměti trend na portálu Azure -> vaše data factory -> přehled stránky. Zvažte [horizontální navýšení kapacity nebo vynětí ir,](create-self-hosted-integration-runtime.md#high-availability-and-scalability) pokud je využití procesoru vysoké nebo je nedostatek dostupné paměti.

  - Pokud platí, přijměte osvědčený postup načítání dat specifických pro konektor. Například:

    - Při kopírování dat z [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)a SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) povolte možnosti datového oddílu pro paralelní kopírování dat.

    - Při kopírování dat z [HDFS](connector-hdfs.md)nakonfigurujte použití programu DistCp.

    - Při kopírování dat z [Amazon Redshift](connector-amazon-redshift.md), nakonfigurujte použít Redshift UNLOAD.

  - Zkontrolujte, zda adf hlásí nějaké chyby omezení na zdroj nebo pokud úložiště dat je pod vysokou využití. Pokud ano, snižte úlohy v úložišti dat nebo se pokuste kontaktovat správce úložiště dat, abyste zvýšili limit omezení nebo dostupný prostředek.

  - Zkontrolujte zdroj kopie a vzorek jímky: 

    - Pokud zkopírujete data z úložišť dat s povolenou možností oddílu, zvažte postupné ladění [paralelních kopií](copy-activity-performance-features.md), všimněte si, že příliš mnoho paralelních kopií může dokonce poškodit výkon.

    - V opačném případě zvažte rozdělení jedné velké datové sady do několika menších datových sad a nechte tyto úlohy kopírování běžet souběžně, každá část dat řeší. Můžete to provést s Vyhledávání/GetMetadata + ForEach + Kopie. Viz [Kopírovat soubory z více kontejnerů](solution-template-copy-files-multiple-containers.md), [migrovat data z Amazon S3 na ADLS Gen2](solution-template-migration-s3-azure.md)nebo [hromadné kopírování se šablonami](solution-template-bulk-copy-with-control-table.md) řešení ovládací tabulky jako obecný příklad.

- **"Transfer - psaní do umyvadla" zkušený dlouhou dobu trvání :**

  - Pokud platí, přijměte osvědčený postup načítání dat specifických pro konektor. Například při kopírování dat do [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (dříve SQL DW), použijte Příkaz PolyBase nebo COPY. 

  - Zkontrolujte, jestli samoobslužný infračervený počítač má nízkou latenci připojení k úložišti dat jímky. Pokud je vaše dřez v Azure, můžete pomocí [tohoto nástroje](http://www.azurespeed.com/Azure/Latency) zkontrolovat latenci z počítače infračerveného počítače hostovaného samoobslužným počítačem do oblasti Azure, čím méně, tím lépe.

  - Zkontrolujte, zda má samoobslužný infračervený počítač dostatečnou odchozí šířku pásma pro efektivní přenos a zápis dat. Pokud je úložiště dat jímky v Azure, můžete [pomocí tohoto nástroje](https://www.azurespeed.com/Azure/UploadLargeFile) zkontrolovat rychlost nahrávání.

  - Zkontrolujte, jestli je samoobslužné IR využití procesoru a využití paměti na webu Azure Portal -> vaše data factory -> přehled stránky. Zvažte [horizontální navýšení kapacity nebo vynětí ir,](create-self-hosted-integration-runtime.md#high-availability-and-scalability) pokud je využití procesoru vysoké nebo je nedostatek dostupné paměti.

  - Zkontrolujte, zda adf hlásí všechny chyby omezení na jímce nebo pokud úložiště dat je pod vysokou využití. Pokud ano, snižte úlohy v úložišti dat nebo se pokuste kontaktovat správce úložiště dat, abyste zvýšili limit omezení nebo dostupný prostředek.

  - Zvažte postupné ladění [paralelních kopií](copy-activity-performance-features.md), všimněte si, že příliš mnoho paralelních kopií může dokonce poškodit výkon.

## <a name="other-references"></a>Další odkazy

Zde je sledování výkonu a ladění odkazy pro některé z podporovaných úložišť dat:

* Úložiště objektů blob Azure: [Škálovatelnost a cíle výkonu pro úložiště objektů blob](../storage/blobs/scalability-targets.md) a [kontrolní seznam výkonu a škálovatelnosti pro úložiště objektů blob](../storage/blobs/storage-performance-checklist.md).
* Azure Table storage: [Škálovatelnost a výkonnostní cíle pro úložiště tabulek](../storage/tables/scalability-targets.md) a [kontrolní seznam výkonu a škálovatelnosti pro table storage](../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: Můžete [sledovat výkon](../sql-database/sql-database-single-database-monitor.md) a zkontrolovat procento jednotky transakcí databáze (DTU).
* Azure SQL Data Warehouse: Jeho schopnost se měří v jednotkách datového skladu (DWUs). Viz [Správa výpočetního výkonu v Azure SQL Data Warehouse (Přehled)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Úrovně výkonu v Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Místní SQL Server: [Sledování a ladění výkonu](https://msdn.microsoft.com/library/ms189081.aspx).
* Místní souborový server: [Optimalizace výkonu pro souborové servery](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Další kroky
Podívejte se na další články o aktivitách kopírování:

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Kopírovat průvodce výkonem a škálovatelností aktivit](copy-activity-performance.md)
- [Kopírování funkcí optimalizace výkonu aktivity](copy-activity-performance-features.md)
- [Migrace dat z datového jezera nebo datového skladu do Azure pomocí Azure Data Factory](data-migration-guidance-overview.md)
- [Migrace dat z Amazonu S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md)
