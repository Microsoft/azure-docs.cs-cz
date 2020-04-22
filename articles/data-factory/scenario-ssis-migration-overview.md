---
title: Migrace místních úloh SSIS do SSIS v Azure Data Factory
description: Migrace místních úloh SSIS do SSIS v ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 2b23ffec76de3fa644abe3b65876a60c65c05eb8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686008"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrace místních úloh SSIS do SSIS v ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Přehled

Když migrujete databázové úlohy z SQL Serveru v místním prostředí do databázových služeb Azure, konkrétně Azure SQL Database nebo Azure SQL Database spravované instance, vaše etl úlohy na SQL Server Integration Services (SSIS) jako jednu z primárních služeb s přidanou hodnotou bude muset být migrována také.

Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF) podporuje spuštěné balíčky SSIS. Po zřízení služby Azure-SSIS IR můžete k nasazení a spuštění balíčků v Azure použít známé nástroje, jako jsou například nástroje SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) a nástroje příkazového řádku, jako je dtinstall/dtutil/dtexec. Další informace najdete [v tématu Přehled nárůstu a posunu Azure SSIS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Tento článek upozorňuje na proces migrace úloh ETL z místního SSIS do SSIS v ADF. Proces migrace se skládá ze dvou fází: **Hodnocení** a **Migrace**.

## <a name="assessment"></a>Posouzení

Chcete-li vytvořit úplný plán migrace, důkladné posouzení pomůže identifikovat problémy se zdrojovými balíčky SSIS, které by zabránily úspěšné migraci.

Data Migration Assistant (DMA) je volně ke stažení nástroj pro tento účel, který lze nainstalovat a spustit místně. Projekt hodnocení DMA typu **Integration Services** lze vytvořit k posouzení balíčků SSIS v dávkách a identifikaci problémů s kompatibilitou, které jsou uvedeny v následujících kategoriích:

- Blokování migrace: Jedná se o problémy s kompatibilitou, které blokují zdrojové balíčky migrace, které mají být spuštěny v infračerveném systému Azure-SSIS. DMA poskytuje pokyny, které vám pomohou tyto problémy vyřešit.

- Informativní problémy: Tyto funkce jsou částečně podporované nebo zastaralé funkce, které se používají ve zdrojových balíčcích. DMA poskytuje komplexní sadu doporučení, alternativní přístupy dostupné v Azure a zmírňující kroky k vyřešení.

### <a name="four-storage-types-for-ssis-packages"></a>Čtyři typy úložišť pro balíčky SSIS

- katalog SSIS (SSISDB). To byl zaveden s SQL Server 2012 a obsahuje sadu uložených procedur, zobrazení a funkce s hodnotou tabulky používané pro práci s projekty SSIS nebo balíčky.
- systému souborů.
- databáze systému SQL Server (MSDB).
- úložiště balíčků SSIS. Toto je vrstva správy balíčků nad dvěma podtypy:
  - MSDB, což je systémová databáze v SQL Serveru slouží k ukládání balíčků SSIS.
  - Spravovaný systém souborů, což je určitá složka v instalační cestě serveru SQL Server, která slouží k ukládání balíčků SSIS.

DMA v současné době podporuje dávkové posouzení balíčků uložených v **souborovém systému**, **úložiště balíčků**a **katalogu SSIS** od **verze DMA verze v5.0**.

Získejte [DMA](https://docs.microsoft.com/sql/dma/dma-overview)a [proveďte posouzení balíčku s ním](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migrace

V závislosti na [typech úložiště](#four-storage-types-for-ssis-packages) zdrojových balíčků SSIS a cíli migrace databázových úloh se mohou kroky migrace **balíčků SSIS** a **úloh agenta serveru SQL Server,** které plánují spuštění balíčků SSIS, lišit. Existují dva scénáře:

- [**Spravovaná instance Azure SQL Database** jako cíl úlohy databáze](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** jako cíl úlohy databáze](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Spravovaná instance Azure SQL Database** jako cíl úlohy databáze

| **Typ úložiště balíčků** |Jak dávkově migrovat balíčky SSIS|Jak dávkově migrovat úlohy SSIS|
|-|-|-|
|SSISDB|[Migrace **sissisdb**](scenario-ssis-migration-ssisdb-mi.md)|[Migrace úloh SSIS do agenta spravované instance Azure SQL Database](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Systém souborů|Znovu je nasaďte do sdílených složek/souborů Azure prostřednictvím dtinstall/dtutil/ruční kopírování nebo k uchovávejte v souborových systémech přístup přes infračervený přenos hostované na virtuální síti/vlastní hostované. Další informace naleznete v tématu [nástroj dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Migrace pomocí [Průvodce migrací úlohy SSIS v SSMS](how-to-migrate-ssis-job-ssms.md) <li>Převeďte je na kanály ADF/aktivity/aktivační události prostřednictvím skriptů/portálu SSMS/ADF. Další informace naleznete v [tématu SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportujte je do systémů souborů/sdílených složek souborů/souborů Azure přes SSMS/dtutil. Další informace naleznete v [tématu Export balíčků SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Převeďte je na kanály ADF/aktivity/aktivační události prostřednictvím skriptů/portálu SSMS/ADF. Další informace naleznete v [tématu SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Úložiště balíčků|Exportujte je do systémů souborů/sdílených složek souborů/souborů Azure prostřednictvím SSMS/dtutil nebo je znovu nasaďte do sdílených složek/souborů Azure pomocí dtinstall/dtutil/ruční kopírování nebo je uchovávejte v souborových systémech pro přístup přes infračervený přenos v síti/vlastní hostované. Další informace naleznete v tématu nástroj dtutil. Další informace naleznete v tématu [nástroj dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Převeďte je na kanály ADF/aktivity/aktivační události prostřednictvím skriptů/portálu SSMS/ADF. Další informace naleznete v [tématu SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** jako cíl úlohy databáze

| **Typ úložiště balíčků** |Jak dávkově migrovat balíčky SSIS|Jak dávkově migrovat úlohy|
|-|-|-|
|SSISDB|Znovu nasadit do Azure-SSISDB přes SSDT/SSMS. Další informace najdete [v tématu nasazení balíčků SSIS v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Převeďte je na kanály ADF/aktivity/aktivační události prostřednictvím skriptů/portálu SSMS/ADF. Další informace naleznete v [tématu SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Systém souborů|Znovu je nasaďte do sdílených složek/souborů Azure prostřednictvím dtinstall/dtutil/ruční kopírování nebo k uchovávejte v souborových systémech přístup přes infračervený přenos hostované na virtuální síti/vlastní hostované. Další informace naleznete v tématu [nástroj dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Migrace pomocí [Průvodce migrací úlohy SSIS v SSMS](how-to-migrate-ssis-job-ssms.md) <li> Převeďte je na kanály ADF/aktivity/aktivační události prostřednictvím skriptů/portálu SSMS/ADF. Další informace naleznete v [tématu SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportujte je do systémů souborů/sdílených složek souborů/souborů Azure přes SSMS/dtutil. Další informace naleznete v [tématu Export balíčků SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Převeďte je na kanály ADF/aktivity/aktivační události prostřednictvím skriptů/portálu SSMS/ADF. Další informace naleznete v [tématu SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Úložiště balíčků|Exportujte je do systémů souborů/sdílených složek souborů/souborů Azure prostřednictvím SSMS/dtutil nebo je znovu nasaďte do sdílených složek/souborů Azure pomocí dtinstall/dtutil/ruční kopírování nebo je uchovávejte v souborových systémech pro přístup přes infračervený přenos v síti/vlastní hostované. Další informace naleznete v tématu nástroj dtutil. Další informace naleznete v tématu [nástroj dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Převeďte je na kanály ADF/aktivity/aktivační události prostřednictvím skriptů/portálu SSMS/ADF. Další informace naleznete v [tématu SSMS scheduling feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Další zdroje

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Pomocník pro migraci databáze](https://docs.microsoft.com/sql/dma/dma-overview)
- [Zvedání a přesouvání úloh SSIS do cloudu](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Migrace balíčků SSIS do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Opětovné nasazení balíčků do Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Další kroky

- [Ověření balíčků SSIS nasazených v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Spouštět balíčky SSIS nasazené v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitorování runtime integrace Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Naplánování spuštění balíčků SSIS v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
