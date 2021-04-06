---
title: Migrace místních služba SSIS (SQL Server Integration Services) úloh (SSIS) na SSIS v Azure Data Factory (ADF)
description: Migrujte místní SSIS úlohy do SSIS v ADF.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 78c488302a874319f79a143e4657d161fe849855
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373602"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrace místních SSIS úloh do SSIS v ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Přehled

Když migrujete databázové úlohy z SQL Server místně do služby Azure Database Services, konkrétně Azure SQL Database nebo Azure SQL Managed instance, bude nutné migrovat úlohy ETL na služba SSIS (SQL Server Integration Services) (SSIS) jako jednu z primárních služeb přidaných hodnot.

Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF) podporuje spouštění balíčků SSIS. Po zřízení Azure-SSIS IR můžete pro nasazení a spouštění balíčků v Azure použít známé nástroje SQL Server, jako je například/SQL (SSDT) serveru Management Studio (SSMS) a nástroje příkazového řádku, jako je například dtinstall/dtutil/DTExec. Další informace najdete v tématu [Přehled služby Azure SSIS Rewind a Shift](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Tento článek popisuje proces migrace úloh ETL z místního SSIS na SSIS v ADF. Proces migrace se skládá ze dvou fází: **posouzení** a **migrace**.

## <a name="assessment"></a>Posouzení

K navázání úplného plánu migrace pomůže důkladné posouzení, které vám pomůžou identifikovat problémy se zdrojovými SSIS balíčky, které by mohly bránit úspěšné migraci.

Data Migration Assistant (DMA) je pro tento účel volně zaváděný nástroj, který se dá nainstalovat a spustit místně. Pro vyhodnocení balíčků SSIS v dávkách a identifikaci problémů s kompatibilitou, které jsou uvedeny v následujících kategoriích, je možné vytvořit projekt posouzení DMA typu **integrační služby** .

- Blokování migrace: problémy s kompatibilitou blokující zdrojové balíčky migrace, které se mají spustit na Azure-SSIS IR. DMA poskytuje pokyny, které vám pomůžou tyto problémy vyřešit.

- Informativní problémy: částečně podporované nebo zastaralé funkce, které se používají ve zdrojových balíčcích. DMA poskytuje komplexní sadu doporučení, alternativní přístup k dispozici v Azure a zmírnění kroků, které je potřeba vyřešit.

### <a name="four-storage-types-for-ssis-packages"></a>Čtyři typy úložišť pro balíčky SSIS

- SSIS Catalog (SSISDB). Zavedeno s SQL Server 2012 a obsahuje sadu uložených procedur, zobrazení a funkce vracející tabulku používané pro práci s projekty nebo balíčky SSIS.
- Systém souborů.
- SQL Server systémovou databázi (MSDB).
- Úložiště balíčků SSIS Vrstva správy balíčků nad dvěma podtypy:
  - MSDB, což je systémová databáze v SQL Server používaná k ukládání balíčků SSIS.
  - Spravovaný systém souborů, což je specifická složka v SQL Server cesta instalace používaná k ukládání balíčků SSIS.

DMA aktuálně podporuje dávkové vyhodnocení balíčků uložených v **systému souborů**, v obchodě s **balíčky** a v **katalogu SSIS** od **verze DMA verze 5.0**.

Získejte [přímý přístup do paměti](/sql/dma/dma-overview)a [proveďte posouzení balíčku](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migrace

V závislosti na [typech úložiště](#four-storage-types-for-ssis-packages) ZDROJOVÝCH balíčků SSIS a cíli migrace databázových úloh se může změnit postup migrace  **balíčků SSIS** a **úloh agenta SQL Server** , které naplánují spouštění balíčků SSIS. Existují dva scénáře:

- [**Spravovaná instance Azure SQL** jako cíl pro databázovou úlohu](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** jako cíl pro databázovou úlohu](#azure-sql-database-as-database-workload-destination)

Je také praktický způsob, jak používat [nástroje SSIS DevOps](/sql/integration-services/devops/ssis-devops-overview), aby bylo možné provést opětovné nasazení balíčku Batch do cíle migrace.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Spravovaná instance Azure SQL** jako cíl pro databázovou úlohu

| **Typ úložiště balíčku** |Postup dávkové migrace balíčků SSIS|Postup dávkové migrace úloh SSIS|
|-|-|-|
|SSISDB|[Migrace **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|<li>[Migrace úloh SSIS do agenta spravované instance Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Systém souborů|Znovu je nasaďte do sdílených složek nebo souborů Azure prostřednictvím dtinstall/dtutil/ručního kopírování, nebo pokud chcete zachovat přístup k systémům souborů pro přístup přes prostředí IR VNet/Autohosted. Další informace najdete v tématu [dtutil Utility](/sql/integration-services/dtutil-utility).|<li>[Migrace úloh SSIS do agenta spravované instance Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Migrace pomocí [Průvodce migrací úloh SSIS v SSMS](how-to-migrate-ssis-job-ssms.md) <li>Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportujte je do systémů souborů/sdílených složek/souborů Azure prostřednictvím SSMS/dtutil. Další informace najdete v tématu [export balíčků SSIS](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Úložiště balíčků|Exportujte je do úložiště balíčků prostřednictvím SSMS/dtutil nebo je znovu nasaďte do balíčku Package prostřednictvím dtinstall/dtutil/ruční kopie. Další informace najdete v tématu [Správa balíčků pomocí Azure-SSIS Integration Runtimeového úložiště balíčků](azure-ssis-integration-runtime-package-store.md).|<li>[Migrace úloh SSIS do agenta spravované instance Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** jako cíl pro databázovou úlohu

| **Typ úložiště balíčku** |Postup dávkové migrace balíčků SSIS|Postup při dávkové migraci úloh|
|-|-|-|
|SSISDB|Znovu nasaďte do Azure-SSISDB prostřednictvím SSDT/SSMS. Další informace najdete v tématu [nasazení balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Systém souborů|Znovu je nasaďte do sdílených složek nebo souborů Azure prostřednictvím dtinstall/dtutil/ručního kopírování, nebo pokud chcete zachovat přístup k systémům souborů pro přístup přes prostředí IR VNet/Autohosted. Další informace najdete v tématu [dtutil Utility](/sql/integration-services/dtutil-utility).|<li> Migrace pomocí [Průvodce migrací úloh SSIS v SSMS](how-to-migrate-ssis-job-ssms.md) <li> Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportujte je do systémů souborů/sdílených složek/souborů Azure prostřednictvím SSMS/dtutil. Další informace najdete v tématu [export balíčků SSIS](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Úložiště balíčků|Exportujte je do systémů souborů/sdílených složek/souborů Azure prostřednictvím SSMS/dtutil nebo je znovu nasaďte do sdílených složek/souborů Azure prostřednictvím dtinstall/dtutil/ruční kopie nebo je Zachovejte v systémech souborů pro přístup přes prostředí IR VNet/Autohosted. Další informace najdete v tématu dtutil Utility. Další informace najdete v tématu [dtutil Utility](/sql/integration-services/dtutil-utility).|Převeďte je na kanály ADF/aktivity/triggery přes skripty/SSMS/ADF Portal. Další informace najdete v tématu [funkce plánování SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Další zdroje informací

- [Azure Data Factory](./introduction.md)
- [Pomocník s migrací databáze](/sql/dma/dma-overview)
- [Nazvednutí a posunutí úloh SSIS do cloudu](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [SSIS DevOps Tools](/sql/integration-services/devops/ssis-devops-overview)
- [Migrace balíčků SSIS do spravované instance Azure SQL](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Znovu nasadit balíčky do Azure SQL Database](../dms/how-to-migrate-ssis-packages.md)

- [Přístup k místním datům z Azure-SSIS Integration Runtime](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Přizpůsobení nastavení pro Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Přístup k úložištím dat a sdíleným složkám s využitím ověřování systému Windows z balíčků SSIS v Azure](ssis-azure-connect-with-windows-auth.md)
- [Použít spravované ověřování identity](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Použití Azure Key Vault](store-credentials-in-key-vault.md)
- [Konfigurace Azure-SSIS Integration Runtime pro vysoký výkon](configure-azure-ssis-integration-runtime-performance.md)
- [Plánované spouštění a zastavování prostředí Azure-SSIS Integration Runtime](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Další kroky

- [Ověření balíčků SSIS nasazených do Azure](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Spouštění balíčků SSIS nasazených v Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS Integration Runtime monitorování](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Plánování spouštění balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)