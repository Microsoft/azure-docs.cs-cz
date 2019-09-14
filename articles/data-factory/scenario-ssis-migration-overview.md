---
title: Migrace místních SSIS úloh do SSIS v Azure Data Factory | Microsoft Docs
description: Migrujte místní SSIS úlohy do SSIS v ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 3bf5ddebd59c95d00d0d3270f0e8e1a2d29b379a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968468"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrace místních SSIS úloh do SSIS v ADF

## <a name="overview"></a>Přehled

Když migrujete databázové úlohy z SQL Server místně do služby Azure Database Services, konkrétně Azure SQL Database nebo Azure SQL Database spravované instance, úlohy ETL na služba SSIS (SQL Server Integration Services) (SSIS) jako jedna z primární přidané hodnoty služby bude nutné migrovat také.

Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF) podporuje spouštění balíčků SSIS. Po zřízení Azure-SSIS IR můžete pro nasazení a spouštění balíčků v Azure použít známé nástroje SQL Server, jako je například/SQL (SSDT) serveru Management Studio (SSMS) a nástroje příkazového řádku, jako je například dtinstall/dtutil/DTExec. Další informace najdete v tématu [Přehled služby Azure SSIS Rewind a Shift](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Tento článek popisuje proces migrace úloh ETL z místního SSIS na SSIS v ADF. Proces migrace se skládá ze dvou fází: **Posouzení** a **migrace**.

## <a name="assessment"></a>Posouzení

K navázání úplného plánu migrace pomůže důkladné posouzení, které vám pomůžou identifikovat problémy se zdrojovými SSIS balíčky, které by mohly bránit úspěšné migraci.

Data Migration Assistant (DMA) je pro tento účel volně zaváděný nástroj, který se dá nainstalovat a spustit místně. Pro vyhodnocení balíčků SSIS v dávkách a identifikaci problémů s kompatibilitou, které jsou uvedeny v následujících kategoriích, je možné vytvořit projekt posouzení DMA typu **integrační služby** .

- Blokování migrace: Jedná se o problémy s kompatibilitou, které blokují zdrojové balíčky migrace pro spuštění v Azure-SSIS IR. DMA poskytuje pokyny, které vám pomůžou tyto problémy vyřešit.

- Informativní problémy: Jedná se o částečně podporované nebo zastaralé funkce, které se používají ve zdrojových balíčcích. DMA poskytuje komplexní sadu doporučení, alternativní přístup k dispozici v Azure a zmírnění kroků, které je potřeba vyřešit.

### <a name="four-storage-types-for-ssis-packages"></a>Čtyři typy úložišť pro balíčky SSIS

- SSIS Catalog (SSISDB). Tato funkce byla představena s SQL Server 2012 a obsahuje sadu uložených procedur, zobrazení a funkcí s hodnotou tabulky, které se používají pro práci s projekty nebo balíčky SSIS.
- Systém souborů.
- SQL Server systémovou databázi (MSDB).
- Úložiště balíčků SSIS Toto je vrstva správy balíčků nad dvěma podtypy:
  - MSDB, což je systémová databáze v SQL Server používaná k ukládání balíčků SSIS.
  - Spravovaný systém souborů, což je specifická složka v SQL Server cesta instalace používaná k ukládání balíčků SSIS.

DMA aktuálně podporuje dávkové vyhodnocení balíčků uložených v typu úložiště **systému souborů** od **verze DMA verze 4.5**.

Získejte [přímý přístup do paměti](https://docs.microsoft.com/sql/dma/dma-overview)a [proveďte posouzení balíčku](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migrace

V závislosti na [typech úložiště](#four-storage-types-for-ssis-packages) ZDROJOVÝCH balíčků SSIS a cíli migrace databázových úloh se může změnit postup migrace **balíčků SSIS** a **úloh agenta SQL Server** , které naplánují spouštění balíčků SSIS. Existují dva scénáře:

- [**Azure SQL Database Managed instance** jako cíl pro databázovou úlohu](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** jako cíl pro databázovou úlohu](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Azure SQL Database Managed instance** jako cíl pro databázovou úlohu

| **Typ úložiště balíčku** |Postup dávkové migrace balíčků SSIS|Postup dávkové migrace úloh SSIS|
|-|-|-|
|SSISDB|[Migrace **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[Migrace úloh SSIS do Azure SQL Database agenta spravované instance](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Systém souborů|Znovu je nasaďte do sdílených složek nebo souborů Azure prostřednictvím dtinstall/dtutil/ručního kopírování, nebo pokud chcete zachovat přístup k systémům souborů pro přístup přes prostředí IR VNet/Autohosted. Další informace najdete v tématu [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportujte je do systémů souborů/sdílených složek/souborů Azure prostřednictvím SSMS/dtutil. Další informace najdete v tématu [export balíčků SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Úložiště balíčků|Exportujte je do systémů souborů/sdílených složek/souborů Azure prostřednictvím SSMS/dtutil nebo je znovu nasaďte do sdílených složek/souborů Azure prostřednictvím dtinstall/dtutil/ruční kopie nebo je Zachovejte v systémech souborů pro přístup přes prostředí IR VNet/Autohosted. Další informace najdete v tématu dtutil Utility. Další informace najdete v tématu [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** jako cíl pro databázovou úlohu

| **Typ úložiště balíčku** |Postup dávkové migrace balíčků SSIS|Postup při dávkové migraci úloh|
|-|-|-|
|SSISDB|Znovu nasaďte do Azure-SSISDB prostřednictvím SSDT/SSMS. Další informace najdete v tématu [nasazení balíčků SSIS v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Systém souborů|Znovu je nasaďte do sdílených složek nebo souborů Azure prostřednictvím dtinstall/dtutil/ručního kopírování, nebo pokud chcete zachovat přístup k systémům souborů pro přístup přes prostředí IR VNet/Autohosted. Další informace najdete v tématu [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportujte je do systémů souborů/sdílených složek/souborů Azure prostřednictvím SSMS/dtutil. Další informace najdete v tématu [export balíčků SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Úložiště balíčků|Exportujte je do systémů souborů/sdílených složek/souborů Azure prostřednictvím SSMS/dtutil nebo je znovu nasaďte do sdílených složek/souborů Azure prostřednictvím dtinstall/dtutil/ruční kopie nebo je Zachovejte v systémech souborů pro přístup přes prostředí IR VNet/Autohosted. Další informace najdete v tématu dtutil Utility. Další informace najdete v tématu [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Další zdroje

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Pomocník s migrací databáze](https://docs.microsoft.com/sql/dma/dma-overview)
- [Nazvednutí a posunutí úloh SSIS do cloudu](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Migrace balíčků SSIS do Azure SQL Database spravované instance](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Znovu nasadit balíčky do Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Další kroky

- [Ověření balíčků SSIS nasazených do Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Spouštění balíčků SSIS nasazených v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitorování prostředí Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Plánování spouštění balíčků SSIS v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
