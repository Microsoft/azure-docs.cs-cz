---
title: Řešení potíží s výkonem aktivity kopírování
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
ms.date: 06/10/2020
ms.openlocfilehash: d464124c6841cb2e3186d521b93d7ae08f94c9e9
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440520"
---
# <a name="troubleshoot-copy-activity-performance"></a>Řešení potíží s výkonem aktivity kopírování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak řešit potíže s výkonem aktivity kopírování v Azure Data Factory. 

Po spuštění aktivity kopírování můžete shromáždit statistiky výsledků spuštění a výkonu v zobrazení [monitorování aktivity kopírování](copy-activity-monitoring.md) . Následuje příklad.

![Sledovat podrobnosti o spuštění aktivity kopírování](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Tipy pro ladění výkonu

V některých scénářích se při spuštění aktivity kopírování v Data Factory zobrazí v horní části **tipy "Optimalizace výkonu"** , jak je znázorněno v předchozím příkladu. Díky tipům se dozvíte, jak u tohoto konkrétního spuštění kopírování poznáte kritické body identifikované pomocí ADF, a navrhněte, jak zvýšit propustnost kopírování. Zkuste provést změnu příkazu znovu a pak znovu spusťte kopii.

V současnosti obsahují tipy pro ladění výkonu návrhy pro následující případy:

| Kategorie              | Tipy pro ladění výkonu                                      |
| --------------------- | ------------------------------------------------------------ |
| Specifické úložiště dat   | Načítají se data do **Azure Synpase Analytics (dřív SQL DW)**: Pokud se nepoužívá, navrhněte použití příkazu Base nebo Copy. |
| &nbsp;                | Kopírování dat z/do **Azure SQL Database**: Pokud je v oblasti vysokého využití DTU, navrhněte upgrade na vyšší úroveň. |
| &nbsp;                | Kopírování dat z/do **Azure Cosmos DB**: Pokud je vysoká úroveň vysokého využití, navrhněte upgrade na větší ru. |
|                       | Kopírování dat z **tabulky SAP**: při kopírování velkých objemů dat doporučujeme, abyste pomocí možnosti oddílu SAP Connectoru povolili paralelní načítání a zvýšili maximální počet oddílů. |
| &nbsp;                | Ingestování dat z **Amazon RedShift**: Navrhněte použití uvolnění, pokud se nepoužívá. |
| Omezování úložiště dat | Pokud úložiště dat během kopírování omezuje počet operací čtení a zápisu, navrhněte kontrolu a zvyšte povolenou míru požadavků pro úložiště dat nebo snižte souběžnou úlohu. |
| Prostředí Integration runtime  | Pokud používáte Integration Runtime v místním prostředí **(IR)** a aktivita kopírování čeká ve frontě dlouho, dokud procesor IR neuvolní dostupný prostředek, navrhněte možnost horizontálního navýšení kapacity a ŠKÁLOVÁNÍ na dálku. |
| &nbsp;                | Pokud použijete **Azure Integration runtime** , která je v neoptimální oblasti, což má za následek pomalé čtení a zápis, navrhněte konfiguraci pro použití jazyka IR v jiné oblasti. |
| Odolnost proti chybám       | Pokud nakonfigurujete odolnost proti chybám a přeskočíte nekompatibilní řádky s výsledky, můžete zajistit, aby data o zdrojovém a jímky byly kompatibilní. |
| Připravené kopírování           | Pokud je příprava na přípravu nakonfigurovaná, ale není užitečná pro vaši dvojici ve zdrojovém kódu, doporučujeme ji odebrat. |
| Obnovit                | Když se aktivita kopírování obnoví z posledního bodu selhání, ale po původním spuštění budete chtít změnit nastavení DIÚ, pamatujte na to, že nové nastavení DIÚ se neprojeví. |

## <a name="understand-copy-activity-execution-details"></a>Vysvětlení podrobností o spuštění aktivity kopírování

Podrobnosti o spuštění a doby trvání v dolní části zobrazení monitorování aktivity kopírování popisují hlavní fáze, přes které aktivita kopírování prochází (viz příklad na začátku tohoto článku), což je zvláště užitečné pro řešení potíží s výkonem kopírování. Kritické místo pro váš běh kopírování je ten, který má nejdelší dobu trvání. Podívejte se na následující tabulku v definici každé fáze a Naučte se [řešit problémy s kopírováním na Azure IR](#troubleshoot-copy-activity-on-azure-ir) a [řešit potíže s aktivitami kopírování v místním prostředí IR](#troubleshoot-copy-activity-on-self-hosted-ir) s těmito informacemi.

| Fáze           | Popis                                                  |
| --------------- | ------------------------------------------------------------ |
| Fronta           | Uplynulý čas do chvíle, kdy se aktivita kopírování ve skutečnosti spustí v prostředí Integration runtime. |
| Skript před kopírováním | Uplynulý čas mezi aktivitou kopírování začínající v rámci aktivity IR a kopírování dokončuje provádění skriptu před kopírováním v úložišti dat jímky. Použijte, když nakonfigurujete skript před kopírováním pro jímky databáze, například při zápisu dat do Azure SQL Database proveďte vyčištění před kopírováním nových dat. |
| Přenos        | Uplynulý čas mezi koncem předchozího kroku a IR, který přenáší všechna data ze zdroje do jímky. <br/>Všimněte si, že dílčí kroky v části přenos běží paralelně a některé operace teď nejsou zobrazené, například analýza/generování formátu souboru.<br><br/>- **Čas do prvního bajtu:** Čas uplynulý mezi koncem předchozího kroku a čas, kdy IR obdrží první bajt ze zdrojového úložiště dat. Platí pro zdroje nezaložené na souborech.<br>- **Zdroj výpisu:** Množství času stráveného při vytváření výčtu zdrojových souborů nebo datových oddílů. Druhá platí při konfiguraci možností oddílu pro zdroje databáze, například při kopírování dat z databází, jako je Oracle/SAP HANA/Teradata/Netezza/atd.<br/>-**Čtení ze zdroje:** Množství času stráveného načítáním dat ze zdrojového úložiště dat.<br/>- **Zápis do jímky:** Množství času stráveného při zápisu dat do úložiště dat jímky. Poznámka: některé konektory v tuto chvíli nemají tuto metriku, jako je Azure Kognitivní hledání, Azure Průzkumník dat, Azure Table Storage, Oracle, SQL Server, Common Data Service, Dynamics 365, Dynamics CRM, Salesforce/Salesforce Service Cloud. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Řešení potíží s aktivitou kopírování v Azure IR

Použijte [Postup ladění výkonu](copy-activity-performance.md#performance-tuning-steps) k naplánování a provádění testu výkonnosti pro váš scénář. 

Pokud výkon aktivity kopírování nevyhovuje vaší očekávání, při řešení potíží s jednou aktivitou kopírování běžící na Azure Integration Runtime, pokud se v zobrazení monitorování kopírování zobrazí [tipy pro ladění výkonu](#performance-tuning-tips) , použijte návrh a zkuste to znovu. V opačném případě [porozumět podrobnostem o spuštění aktivity kopírování](#understand-copy-activity-execution-details), ověřte, která fáze má **nejdelší** dobu trvání a použijte následující pokyny ke zvýšení výkonu kopírování:

- **"Skript před kopírováním" má dlouhou dobu trvání:** to znamená, že dokončení skriptu spouštěného v databázi jímky trvá dlouho. Vyladěním zadané předdefinované skriptové logiky můžete zvýšit výkon. Pokud potřebujete další pomoc s vylepšením skriptu, obraťte se na tým databáze.

- **"Přenos-doba do prvního bajtu" zjistil dlouhou pracovní dobu**. znamená to, že váš zdrojový dotaz trvá vrácení jakýchkoli dat dlouho. Zkontroluje a optimalizuje dotaz nebo server. Pokud potřebujete další pomoc, obraťte se na tým úložiště dat.

- **"Zdroj výpisu přenosu" má nefunkční dobu trvání**: znamená to, že výčet zdrojových souborů nebo oddílů dat zdrojové databáze je pomalý.
  - Pokud kopírujete data ze zdrojového souboru, pokud použijete **Filtr zástupných znaků** pro cestu ke složce nebo název souboru ( `wildcardFolderPath` nebo) `wildcardFileName` , nebo použijete **Filtr pro čas poslední změny** souboru ( `modifiedDatetimeStart` nebo), `modifiedDatetimeEnd` Poznamenejte si, že by tento filtr způsobil, že aktivita kopírování obsahuje všechny soubory v zadané složce na straně klienta a potom použije filtr. Tento výčet souborů by se mohl stát kritickým bodem, zejména když pravidlo filtru splní jenom malá sada souborů.

    - Ověřte, zda můžete [Kopírovat soubory založené na cestě k oddílu DateTime a názvu souboru](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Takovým způsobem nepřinese zatížení při výpisu zdrojové strany.

    - Ověřte, jestli můžete místo toho použít nativní filtr úložiště dat, konkrétně "**prefix**" pro službu Amazon S3 a Azure Blob. Filtr předpon je filtr na straně serveru úložiště dat, který by měl mnohem lepší výkon.

    - Zvažte, jak rozdělit jednu velkou datovou sadu do několika menších datových sad a nechat tyto úlohy kopírování běžet souběžně, přičemž každý z nich vybere část dat. Můžete to provést pomocí Lookup/GetMetadata + ForEach + Copy. Přečtěte si téma [kopírování souborů z více kontejnerů](solution-template-copy-files-multiple-containers.md) nebo [migrace dat ze služby Amazon S3 do](solution-template-migration-s3-azure.md) šablon řešení adls Gen2 jako obecné příklad.

  - Ověřte, jestli se na zdrojovém ADF hlásí jakákoli chyba omezování, nebo jestli je úložiště dat ve stavu vysokého využití. Pokud ano, buď Snižte své zatížení v úložišti dat, nebo se pokuste kontaktovat správce úložiště dat, aby se zvýšil limit omezení nebo dostupný prostředek.

  - Použijte Azure IR ve stejné nebo blízko oblasti zdrojového úložiště dat.

- **"Přenos – čtení ze zdroje" zjistil dlouhou pracovní dobu trvání**: 

  - V případě, že platí, přijmout osvědčené postupy načítání dat pro jednotlivé konektory. Například při kopírování dat z [Amazon RedShift](connector-amazon-redshift.md)nakonfigurujte pro použití RedShift Unload.

  - Ověřte, jestli se na zdrojovém ADF hlásí jakákoli chyba omezování, nebo jestli je vaše úložiště dat pod vysokým využitím. Pokud ano, buď Snižte své zatížení v úložišti dat, nebo se pokuste kontaktovat správce úložiště dat, aby se zvýšil limit omezení nebo dostupný prostředek.

  - Ověřte zdroj kopírování a vzor jímky: 

    - Pokud váš vzor kopírování podporuje víc než 4 jednotky pro integraci dat (DIUs) – Přečtěte si [Tento oddíl](copy-activity-performance-features.md#data-integration-units) o podrobnostech, obecně se můžete pokusit zvýšit výkon tak, aby se zvýšila velikost DIUs. 

    - V opačném případě zvažte možnost rozdělit jednu velkou datovou sadu na několik menších datových sad a nechat tyto úlohy kopírování běžet souběžně, přičemž každý z nich vybere část dat. Můžete to provést pomocí Lookup/GetMetadata + ForEach + Copy. Přečtěte si téma [kopírování souborů z více kontejnerů](solution-template-copy-files-multiple-containers.md), [migrace dat ze služby Amazon S3 do adls Gen2](solution-template-migration-s3-azure.md)nebo [hromadné kopírování pomocí šablon řešení tabulky ovládacích prvků](solution-template-bulk-copy-with-control-table.md) jako obecné příklad.

  - Použijte Azure IR ve stejné nebo blízko oblasti zdrojového úložiště dat.

- **"Přenosový zápis do jímky" zjistil dlouhou pracovní dobu**:

  - V případě, že platí, přijmout osvědčené postupy načítání dat pro jednotlivé konektory. Například při kopírování dat do služby [Azure synapse Analytics](connector-azure-sql-data-warehouse.md) (dříve SQL DW) použijte příkaz Base nebo Copy. 

  - Ověřte, jestli se v případě vysokého využití v rámci jímky hlásí jakákoli chyba omezování, nebo jestli je úložiště dat omezené. Pokud ano, buď Snižte své zatížení v úložišti dat, nebo se pokuste kontaktovat správce úložiště dat, aby se zvýšil limit omezení nebo dostupný prostředek.

  - Ověřte zdroj kopírování a vzor jímky: 

    - Pokud váš vzor kopírování podporuje víc než 4 jednotky pro integraci dat (DIUs) – Přečtěte si [Tento oddíl](copy-activity-performance-features.md#data-integration-units) o podrobnostech, obecně se můžete pokusit zvýšit výkon tak, aby se zvýšila velikost DIUs. 

    - V opačném případě můžete [paralelní kopie](copy-activity-performance-features.md)postupně ladit, Pamatujte si, že příliš mnoho paralelních kopií může dokonce snížit výkon.

  - Použijte Azure IR ve stejné nebo blízko oblasti úložiště dat jímky.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Řešení potíží s aktivitou kopírování v místním prostředí IR

Použijte [Postup ladění výkonu](copy-activity-performance.md#performance-tuning-steps) k naplánování a provádění testu výkonnosti pro váš scénář. 

Pokud výkon kopírování nevyhovuje vaší očekávání, při odstraňování potíží s jednou aktivitou kopírování běžící na Azure Integration Runtime, pokud se v zobrazení monitorování kopírování zobrazí [tipy pro ladění výkonu](#performance-tuning-tips) , použijte návrh a zkuste to znovu. V opačném případě [porozumět podrobnostem o spuštění aktivity kopírování](#understand-copy-activity-execution-details), ověřte, která fáze má **nejdelší** dobu trvání a použijte následující pokyny ke zvýšení výkonu kopírování:

- **"Fronta" má dlouhou dobu.** znamená to, že aktivita kopírování čeká na dlouhou dobu ve frontě, dokud se prostředek IR v místním prostředí nespustí. Prohlédněte si kapacitu a využití IR a [nahorizontální navýšení nebo navýšení](create-self-hosted-integration-runtime.md#high-availability-and-scalability) kapacity podle vašich úloh.

- **"Přenos-doba do prvního bajtu" zjistil dlouhou pracovní dobu**. znamená to, že váš zdrojový dotaz trvá vrácení jakýchkoli dat dlouho. Zkontroluje a optimalizuje dotaz nebo server. Pokud potřebujete další pomoc, obraťte se na tým úložiště dat.

- **"Zdroj výpisu přenosu" má nefunkční dobu trvání**: znamená to, že výčet zdrojových souborů nebo oddílů dat zdrojové databáze je pomalý.

  - Ověřte, jestli je v místním počítači IR počítač s nízkou latencí připojení ke zdrojovému úložišti dat. Pokud je váš zdroj v Azure, můžete [Tento nástroj](http://www.azurespeed.com/Azure/Latency) použít ke kontrole latence z místního počítače IR v místním prostředí do oblasti Azure. tím menší je lepší.

  - Pokud kopírujete data ze zdrojového souboru, pokud použijete **Filtr zástupných znaků** pro cestu ke složce nebo název souboru ( `wildcardFolderPath` nebo) `wildcardFileName` , nebo použijete **Filtr pro čas poslední změny** souboru ( `modifiedDatetimeStart` nebo), `modifiedDatetimeEnd` Poznamenejte si, že by tento filtr způsobil, že aktivita kopírování obsahuje všechny soubory v zadané složce na straně klienta a potom použije filtr. Tento výčet souborů by se mohl stát kritickým bodem, zejména když pravidlo filtru splní jenom malá sada souborů.

    - Ověřte, zda můžete [Kopírovat soubory založené na cestě k oddílu DateTime a názvu souboru](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Takovým způsobem nepřinese zatížení při výpisu zdrojové strany.

    - Ověřte, jestli můžete místo toho použít nativní filtr úložiště dat, konkrétně "**prefix**" pro službu Amazon S3 a Azure Blob. Filtr předpon je filtr na straně serveru úložiště dat, který by měl mnohem lepší výkon.

    - Zvažte, jak rozdělit jednu velkou datovou sadu do několika menších datových sad a nechat tyto úlohy kopírování běžet souběžně, přičemž každý z nich vybere část dat. Můžete to provést pomocí Lookup/GetMetadata + ForEach + Copy. Přečtěte si téma [kopírování souborů z více kontejnerů](solution-template-copy-files-multiple-containers.md) nebo [migrace dat ze služby Amazon S3 do](solution-template-migration-s3-azure.md) šablon řešení adls Gen2 jako obecné příklad.

  - Ověřte, jestli se na zdrojovém ADF hlásí jakákoli chyba omezování, nebo jestli je úložiště dat ve stavu vysokého využití. Pokud ano, buď Snižte své zatížení v úložišti dat, nebo se pokuste kontaktovat správce úložiště dat, aby se zvýšil limit omezení nebo dostupný prostředek.

- **"Přenos – čtení ze zdroje" zjistil dlouhou pracovní dobu trvání**: 

  - Ověřte, jestli je v místním počítači IR počítač s nízkou latencí připojení ke zdrojovému úložišti dat. Pokud je váš zdroj v Azure, můžete [Tento nástroj](http://www.azurespeed.com/Azure/Latency) použít ke kontrole latence z místního počítače IR v místním prostředí do oblastí Azure. tím menší je lepší.

  - Ověřte, jestli je v místním počítači IR k dispozici dostatek příchozí šířky pásma pro efektivní čtení a přenos dat. Pokud je zdrojové úložiště dat v Azure, můžete pomocí [tohoto nástroje](https://www.azurespeed.com/Azure/Download) ověřit rychlost stahování.

  - Projděte si trend využití procesoru a paměti v místním prostředí IR v Azure Portal-> vaší datové továrny – přehled >. Pokud je využití procesoru vysoké nebo málo dostupné paměti, zvažte možnost [horizontálního navýšení nebo navýšení kapacity](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

  - V případě, že platí, přijmout osvědčené postupy načítání dat pro jednotlivé konektory. Například:

    - Při kopírování dat z [databází Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP](connector-sap-table.md#sap-table-as-source)a [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)povolte možnosti datového oddílu, aby se data kopírovala paralelně.

    - Při kopírování dat z [HDFS](connector-hdfs.md)nakonfigurujte pro použití DistCp.

    - Při kopírování dat z [Amazon RedShift](connector-amazon-redshift.md)nakonfigurujte tak, aby používala RedShift uvolnění.

  - Ověřte, jestli se na zdrojovém ADF nahlásí jakákoli chyba omezování, nebo jestli je vaše úložiště dat vysoce využité. Pokud ano, buď Snižte své zatížení v úložišti dat, nebo se pokuste kontaktovat správce úložiště dat, aby se zvýšil limit omezení nebo dostupný prostředek.

  - Ověřte zdroj kopírování a vzor jímky: 

    - Pokud kopírujete data z úložišť s povolenými možnostmi pro oddíly, zvažte, jestli je možné postupně ladit [paralelní kopie](copy-activity-performance-features.md), a Všimněte si, že příliš mnoho paralelních kopií může dokonce snížit výkon.

    - V opačném případě zvažte možnost rozdělit jednu velkou datovou sadu na několik menších datových sad a nechat tyto úlohy kopírování běžet souběžně, přičemž každý z nich vybere část dat. Můžete to provést pomocí Lookup/GetMetadata + ForEach + Copy. Přečtěte si téma [kopírování souborů z více kontejnerů](solution-template-copy-files-multiple-containers.md), [migrace dat ze služby Amazon S3 do adls Gen2](solution-template-migration-s3-azure.md)nebo [hromadné kopírování pomocí šablon řešení tabulky ovládacích prvků](solution-template-bulk-copy-with-control-table.md) jako obecné příklad.

- **"Přenosový zápis do jímky" zjistil dlouhou pracovní dobu**:

  - V případě, že platí, přijmout osvědčené postupy načítání dat pro jednotlivé konektory. Například při kopírování dat do služby [Azure synapse Analytics](connector-azure-sql-data-warehouse.md) (dříve SQL DW) použijte příkaz Base nebo Copy. 

  - Ověřte, jestli je v místním počítači IR počítač s připojením k úložišti dat jímky nízká latence. Pokud je vaše jímka v Azure, můžete [Tento nástroj](http://www.azurespeed.com/Azure/Latency) použít ke kontrole latence z místního počítače IR v místním prostředí do oblasti Azure. tím menší je lepší.

  - Ověřte, jestli je v místním počítači IR k dispozici dostatek odchozí šířky pásma pro efektivní přenos a zápis dat. Pokud je úložiště dat jímky v Azure, můžete pomocí [tohoto nástroje](https://www.azurespeed.com/Azure/UploadLargeFile) ověřit rychlost nahrávání.

  - Podívejte se, jestli trend využití procesoru a paměti v místním prostředí IR v Azure Portal-> vaší datové továrně > stránce s přehledem. Pokud je využití procesoru vysoké nebo málo dostupné paměti, zvažte možnost [horizontálního navýšení nebo navýšení kapacity](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

  - Ověřte, jestli se v případě vysokého využití v rámci jímky hlásí jakákoli chyba omezování, nebo jestli je úložiště dat omezené. Pokud ano, buď Snižte své zatížení v úložišti dat, nebo se pokuste kontaktovat správce úložiště dat, aby se zvýšil limit omezení nebo dostupný prostředek.

  - Zvažte možnost postupného vyladění [paralelních kopií](copy-activity-performance-features.md), Všimněte si, že příliš mnoho paralelních kopií může dokonce snížit výkon.

## <a name="other-references"></a>Další odkazy

Tady je sledování výkonu a ladění odkazů pro některá z podporovaných úložišť dat:

* Azure Blob Storage: [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../storage/blobs/scalability-targets.md) a [Kontrolní seznam výkonu a škálovatelnosti pro úložiště objektů BLOB](../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: [škálovatelnost a výkonnostní cíle pro úložiště tabulek](../storage/tables/scalability-targets.md) a pro [Kontrolní seznam výkonu a škálovatelnosti pro úložiště tabulek](../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: můžete [monitorovat výkon](../sql-database/sql-database-single-database-monitor.md) a kontrolovat procento transakčních jednotek databáze (DTU).
* Azure synapse Analytics (dříve SQL Data Warehouse): jeho schopnost se měří v jednotkách datového skladu (DWU). Viz [Správa výpočetní výkon v Azure synapse Analytics (přehled)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [úrovně výkonu v Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server: [monitorování a optimalizace výkonu](https://msdn.microsoft.com/library/ms189081.aspx).
* Místní souborový server: [optimalizace výkonu pro souborové servery](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Průvodce škálovatelností a výkonem aktivity kopírování](copy-activity-performance.md)
- [Funkce optimalizace výkonu aktivity kopírování](copy-activity-performance-features.md)
- [Použití Azure Data Factory k migraci dat ze služby Data Lake nebo datového skladu do Azure](data-migration-guidance-overview.md)
- [Migrace dat z Amazonu S3 do Azure Storage](data-migration-guidance-s3-azure-storage.md)
