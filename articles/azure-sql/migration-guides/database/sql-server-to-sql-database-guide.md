---
title: 'SQL Server SQL Database: Průvodce migrací'
description: Podle tohoto průvodce migrujte databáze SQL Server do Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 03/19/2021
ms.openlocfilehash: 9205301cb77941e4ea7ca026710d44ba82f6a937
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563840"
---
# <a name="migration-guide-sql-server-to-sql-database"></a>Průvodce migrací: SQL Server SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Tato příručka vám pomůže s migrací instance SQL Server do Azure SQL Database. 

Můžete migrovat SQL Server spuštěná místně nebo na: 

- SQL Server na virtuálních počítačích  
- Amazon Web Services (AWS) EC2 
- Služba Amazon relačních databází (AWS RDS) 
- Výpočetní modul (Google Cloud Platform – GCP)  
- Cloud SQL pro SQL Server (Google Cloud Platform – GCP) 

Další informace o migraci najdete v tématu [Přehled migrace](sql-server-to-sql-database-overview.md). Další scénáře najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/).

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Tok procesu migrace":::

## <a name="prerequisites"></a>Předpoklady 

Chcete-li migrovat SQL Server do Azure SQL Database, ujistěte se, že máte následující požadavky: 

- Zvolená [Metoda migrace](sql-server-to-sql-database-overview.md#compare-migration-options) a odpovídající nástroje 
- [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) nainstalovaný na počítači, který se může připojit ke svému zdrojovému SQL Server
- Cílový [Azure SQL Database](../../database/single-database-create-quickstart.md)


## <a name="pre-migration"></a>Před migrací

Až ověříte, že je vaše zdrojové prostředí podporované, začněte s fází před migrací. Seznamte se se všemi existujícími zdroji dat, vyhodnoťte proveditelnost migrace a Identifikujte případné blokující problémy, které by mohly bránit vaší migraci. 

### <a name="discover"></a>Zjišťování

Ve fázi zjišťování zkontrolujte síť a identifikujte všechny SQL Server instance a funkce používané vaší organizací. 

Použijte [Azure Migrate](../../../migrate/migrate-services-overview.md) k vyhodnocení vhodnosti migrace místních serverů, provádění určení velikosti na základě výkonu a poskytování odhadů nákladů na jejich spuštění v Azure. 

Případně můžete pomocí sady [nástrojů Microsoft Assessment and Planning Toolkit ("mapová sada")](https://www.microsoft.com/download/details.aspx?id=7826) posoudit svou aktuální infrastrukturu IT. Sada nástrojů poskytuje výkonný nástroj pro inventarizaci, posuzování a vytváření sestav, který zjednodušuje proces plánování migrace. 

Další informace o nástrojích, které jsou k dispozici pro fázi zjišťování, najdete v tématu [služby a nástroje dostupné pro scénáře migrace dat](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Posouzení 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Po zjištění zdrojů dat vyhodnoťte všechny místní SQL Server databáze, které je možné migrovat do Azure SQL Database a Identifikujte tak blokování migrace nebo problémy s kompatibilitou. 

K vyhodnocení databází pro získání můžete použít Data Migration Assistant (verze 4,1 a novější): 

- [Doporučení pro cíle Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Doporučení k Azure SKU](/sql/dma/dma-sku-recommend-sql-db)

Pokud chcete posoudit své prostředí pomocí vyhodnocení migrace databáze, postupujte podle těchto kroků: 

1. Otevřete [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Vyberte **soubor** a klikněte na tlačítko **nové posouzení**. 
1. Zadejte název projektu, jako typ zdrojového serveru vyberte SQL Server a jako typ cílového serveru vyberte Azure SQL Database. 
1. Vyberte typy sestav hodnocení, které chcete vygenerovat. Například kompatibilita databáze a parita funkcí. Na základě typu posouzení se oprávnění požadovaná na zdrojovém SQL Server můžou lišit.  Před spuštěním posouzení zvýrazní rozhraní DMA oprávnění požadovaná pro vybraný poradce.
    - Kategorie **parity funkcí** poskytuje komplexní sadu doporučení, alternativy dostupné v Azure a zmírnění kroků, které vám pomůžou při plánování projektu migrace. (vyžaduje se oprávnění správce systému.)
    - Kategorie **problémy s kompatibilitou** identifikuje částečně podporované nebo nepodporované problémy s kompatibilitou funkcí, které by mohly blokovat migraci, a také doporučení pro jejich řešení ( `CONNECT SQL` , `VIEW SERVER STATE` a `VIEW ANY DEFINITION` požadovaná oprávnění).
1. Zadejte podrobnosti o zdrojovém připojení pro vaši SQL Server a připojte se ke zdrojové databázi.
1. Vyberte **Spustit posouzení**. 
1. Po dokončení procesu vyberte a zkontrolujte zprávy posouzení pro blokování migrace a problémy s paritou funkcí. Sestavu posouzení je taky možné exportovat do souboru, který se dá sdílet s ostatními týmy nebo zaměstnanci ve vaší organizaci. 
1. Určete úroveň kompatibility databáze, která minimalizuje úsilí po migraci.  
1. Identifikujte nejlepší Azure SQL Database SKLADOVOU položku pro vaše místní úlohy. 

Další informace najdete v tématu věnovaném [SQL Server hodnocení migrace pomocí Data Migration Assistant](/sql/dma/dma-assesssqlonprem).

Pokud posouzení narazí na více blokování a potvrdí, že vaše databáze není připravena na migraci Azure SQL Database, zvažte také:

- [Spravovaná instance Azure SQL](../managed-instance/sql-server-to-managed-instance-overview.md) , pokud existuje více závislostí v rozsahu instance
- [SQL Server v Azure Virtual Machines](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) , pokud SQL Database i spravované instance SQL nemůžou být vhodnými cíli. 



#### <a name="scaled-assessments-and-analysis"></a>Měření a analýza škálované
Data Migration Assistant podporuje provádění hodnocení a konsolidaci hodnotících sestav pro účely analýzy. 

Pokud máte více serverů a databází, které je potřeba posoudit a analyzovat ve velkém měřítku, aby poskytovaly širší pohled na datovou oblast, přečtěte si následující odkazy, kde najdete další informace:

- [Provádění hodnocení s měřítkem pomocí PowerShellu](/sql/dma/dma-consolidatereports)
- [Analýza sestav hodnocení pomocí Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> Průběžné hodnocení pro více databází, zejména velké, můžou být automatizované pomocí [nástroje příkazového řádku DMA](/sql/dma/dma-commandline) a nahrály na [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) pro další analýzy a cílení na cíle.

## <a name="migrate"></a>Migrate

Po dokončení úloh přidružených ke fázi před migrací jste připraveni provést migraci schématu a dat. 

Migrujte svá data pomocí zvolené [metody migrace](sql-server-to-sql-database-overview.md#compare-migration-options). 

Tato příručka popisuje dvě nejoblíbenější možnosti – Data Migration Assistant a Azure Database Migration Service. 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant (DMA)

K migraci databáze z SQL Server na Azure SQL Database pomocí DMA použijte tento postup: 

1. Stáhněte a nainstalujte [Pomocník s migrací databáze](https://www.microsoft.com/download/details.aspx?id=53595).
1. Vytvořte nový projekt a vyberte možnost **migrace** jako typ projektu.
1. Nastavte typ zdrojového serveru na **SQL Server** a typ cílového serveru, který se má **Azure SQL Database**, vyberte obor migrace jako **schéma a data** a vyberte **vytvořit**.
1. V projektu migrace zadejte podrobnosti zdrojového serveru, jako je název serveru, přihlašovací údaje pro připojení k serveru a zdrojovou databázi, kterou chcete migrovat.
1. V podrobnostech cílového serveru zadejte název serveru Azure SQL Database, přihlašovací údaje pro připojení k serveru a cílovou databázi, do které se má migrovat.
1. Vyberte objekty schématu a nasaďte je do cílového Azure SQL Database.
1. Nakonec vyberte **Spustit migraci dat** a monitorovat průběh migrace.

Podrobný kurz najdete v tématu [migrace místních SQL Server nebo SQL Server na virtuálních počítačích Azure pro Azure SQL Database používání Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb). 


> [!NOTE]
> - Škálujte svou databázi na vyšší úroveň služby a výpočetní velikost během procesu importu a maximalizujte tak rychlost importu tím, že poskytnete další prostředky. Po úspěšném dokončení importu pak můžete kapacitu vertikálně snížit.</br>
> - Úroveň kompatibility importované databáze je založena na úrovni kompatibility zdrojové databáze. 


### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

Pokud chcete migrovat databáze z SQL Server do Azure SQL Database pomocí DMS, postupujte podle následujících kroků:

1. Pokud jste to ještě neudělali, zaregistrujte poskytovatele prostředků **Microsoft. datamigration** do svého předplatného. 
1. Vytvořte instanci Azure Database Migration Service v požadovaném umístění (nejlépe ve stejné oblasti jako cílový Azure SQL Database). Vyberte existující virtuální síť nebo vytvořte novou pro hostování instance DMS.
1. Po vytvoření instance DMS vytvořte nový projekt migrace a zadejte jako **SQL Server** typ zdrojového serveru a jako **Azure SQL Database** zadejte cílový server. V okně pro vytvoření projektu migrace vyberte možnost **migrace offline dat** jako typ aktivity.
1. Na stránce Podrobnosti o **zdroji migrace** zadejte podrobnosti o zdrojovém SQL Server a na stránce Podrobnosti o **cíli migrace** určete cílovou Azure SQL Database podrobnosti.
1. Namapujte zdrojové a cílové databáze pro migraci a pak vyberte tabulky, které chcete migrovat.
1. Zkontrolujte souhrn migrace a vyberte **Spustit migraci**. Pak můžete sledovat aktivitu migrace a kontrolovat průběh migrace databáze.

Podrobný kurz najdete v tématu [migrace SQL Server do Azure SQL Database pomocí DMS](../../../dms/tutorial-sql-server-to-azure-sql.md). 

## <a name="data-sync-and-cutover"></a>Synchronizace dat a přímou migraci

Při použití možností migrace, které průběžně replikují nebo synchronizují změny dat ze zdroje do cíle, se zdrojová data a schéma můžou měnit a přenášet od cíle. Při synchronizaci dat se ujistěte, že jsou všechny změny ve zdroji zachyceny a aplikovány na cíl během procesu migrace. 

Jakmile ověříte, že jsou data stejná na zdrojovém i cílovém cíli, můžete přímou migraci ze zdroje do cílového prostředí. Je důležité naplánovat proces přímou migraci s využitím obchodních nebo aplikačních týmů, aby bylo zajištěno minimální přerušení během přímou migraci, které nemá vliv na kontinuitu podnikových procesů. 

> [!IMPORTANT]
> Podrobnosti o konkrétních krocích spojených s prováděním přímou migraci jako součást migrace pomocí DMS najdete v tématu [provádění migrace přímou migraci](../../../dms/tutorial-sql-server-azure-sql-online.md#perform-migration-cutover).

## <a name="migration-recommendations"></a>Doporučení pro migraci

Pokud chcete urychlit migraci na Azure SQL Database, měli byste zvážit následující doporučení:

|  | Spory prostředků | Doporučení |
|--|--|--|
| **Zdroj (obvykle místní)** |Hlavním kritickým bodem během migrace ve zdroji jsou vstupně-výstupní operace s daty a latence v DATOVÉm souboru, který je potřeba monitorovat pečlivě.  |Na základě latence vstupně-výstupních operací a datových souborů a v závislosti na tom, jestli se jedná o virtuální počítač nebo fyzický server, budete muset zapojit Správce úložiště a prozkoumat možnosti, jak zmírnit kritické body. |
|**Cíl (Azure SQL Database)**|Největší faktor omezení je míra generování protokolu a latence v souboru protokolu. V případě Azure SQL Database můžete získat maximálně 96 MB/s rychlost generování protokolu. | Pokud chcete zrychlit migraci, naplánujte cílovou databázi SQL tak, aby Pro důležité obchodní informace Gen5 8 vCore, aby se získala maximální rychlost generování protokolu 96 MB/s, a taky pro soubor protokolu zajistěte nízkou latenci. Úroveň služby pro [škálování](../../database/service-tier-hyperscale.md) na úrovni služeb poskytuje rychlost protokolu 100 MB/s bez ohledu na zvolenou úroveň služby. |
|**Síť** |Požadovaná šířka pásma sítě je rovna maximální rychlosti ingestování protokolu 96 MB/s (768 MB/s). |V závislosti na připojení k síti z místního datového centra k Azure se podívejte na šířku pásma vaší sítě (obvykle [Azure ExpressRoute](../../../expressroute/expressroute-introduction.md#bandwidth-options)), která bude vyhovovat maximální rychlosti ingestování protokolů. |
|**Virtuální počítač použitý pro Data Migration Assistant (DMA)** |CPU je primárním kritickým bodem pro virtuální počítač, na kterém běží DMA. |Věci, které je potřeba zvážit při urychlení migrace dat pomocí </br>– Virtuální počítače náročné na výpočetní výkon Azure </br>-Použít aspoň F8s_v2 (8 Vcore) virtuální počítač pro provoz DMA </br>– Ujistěte se, že je virtuální počítač spuštěný ve stejné oblasti Azure jako cíl. |
|**Azure Database Migration Service (DMS)** |Spory výpočetních prostředků a databázové objekty, které je potřeba pro DMS |Použijte vCore úrovně Premium 4. DMS automaticky postará o databázové objekty, jako jsou cizí klíče, triggery, omezení a neclusterované indexy, a nepotřebuje ruční zásah.  |


## <a name="post-migration"></a>Po migraci

Po úspěšném dokončení fáze migrace Projděte řadu úkolů po migraci, abyste zajistili, že všechno funguje hladce a efektivně. 

Fáze po migraci je zásadní pro sjednocení problémů s přesností dat a ověření úplnosti a také řešení potíží s výkonem s úlohou. 

### <a name="remediate-applications"></a>Opravit aplikace 

Po migraci dat do cílového prostředí všechny aplikace, které dříve využily zdroj, musí začít spotřebovávat cíl. Výsledkem bude, že v některých případech bude nutné v aplikacích provádět změny.

### <a name="perform-tests"></a>Provést testy

Testovací přístup pro migraci databáze se skládá z následujících aktivit:

1. **Vývoj ověřovacích testů**: k otestování migrace databáze je nutné použít dotazy SQL. Je nutné vytvořit ověřovací dotazy ke spuštění proti zdrojové i cílové databázi. Dotazy na ověřování by se měly pokrývat s definovaným oborem.
1. **Nastavení testovacího prostředí**: testovací prostředí by mělo obsahovat kopii zdrojové databáze a cílovou databázi. Nezapomeňte izolovat testovací prostředí.
1. **Spustit ověřovací testy**: Spusťte ověřovací testy proti zdroji a cíli a pak Analyzujte výsledky.
1. **Spustit testy výkonu**: spustit test výkonnosti proti zdroji a cíli a pak analyzovat a porovnat výsledky.


## <a name="leverage-advanced-features"></a>Využití pokročilých funkcí 

Nezapomeňte využít výhod pokročilých cloudových funkcí, které nabízí SQL Database, jako je [integrovaná vysoká dostupnost](../../database/high-availability-sla.md), [detekce hrozeb](../../database/azure-defender-for-sql.md)a [monitorování a optimalizace vašich úloh](../../database/monitor-tune-overview.md). 

Některé funkce SQL Server jsou dostupné až po změně [úrovně kompatibility databáze](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) na nejnovější úroveň kompatibility (150). 

Další informace najdete v tématu [správa Azure SQL Database po migraci](../../database/manage-data-after-migrating-to-database.md) .


## <a name="next-steps"></a>Další kroky

- Matrici služeb a nástrojů společnosti Microsoft, které jsou k dispozici, aby vám pomohla při různých scénářích databáze a migrace dat i v speciálních úlohách, najdete v tématu [služba a nástroje pro migraci dat](../../../dms/dms-tools-matrix.md).

- Další informace o SQL Database najdete v těchto tématech:
    - [Přehled Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulačka celkových nákladů na vlastnictví Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Další informace o cyklu rozhraní a přijetí pro migrace do cloudu najdete v tématu.
   -  [Cloud Adoption Framework pro Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Osvědčené postupy pro výpočet nákladů a úloh migrace do Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Postup vyhodnocení vrstvy přístupu k aplikaci najdete v tématu [sada Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) .
- Podrobnosti o tom, jak provádět testování vrstvy přístupu k datům A/B, najdete [Pomocník pro experimentování s databázemi](/sql/dea/database-experimentation-assistant-overview).