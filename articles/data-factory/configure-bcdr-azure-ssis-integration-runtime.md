---
title: Konfigurace prostředí Azure-SSIS Integration runtime pro provozní kontinuitu a zotavení po havárii (BCDR)
description: Tento článek popisuje, jak nakonfigurovat prostředí Azure-SSIS Integration runtime ve službě Azure Data Factory se skupinou převzetí služeb při selhání Azure SQL Database/spravované instance pro zajištění kontinuity podnikových aplikací a zotavení po havárii (BCDR).
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: 2744d51b6d68ed494050be10a9f0e4d1f59cdc49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204061"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Konfigurace prostředí Azure-SSIS Integration runtime pro provozní kontinuitu a zotavení po havárii (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure SQL Database/spravované instance a služba SSIS (SQL Server Integration Services) (SSIS) v Azure Data Factory (ADF) se dají pro SQL Server migraci kombinovat jako doporučené řešení PaaS (All-Platform as a Service). Projekty SSIS můžete nasadit do služby SSIS Catalog Database (SSISDB) hostované pomocí Azure SQL Database/spravované instance a své balíčky SSIS Spustit v Azure SSIS Integration runtime (IR) v ADF.

Pro zajištění provozní kontinuity a zotavení po havárii (BCDR) se dá Azure SQL Database/spravovaná instance nakonfigurovat se [skupinou geografické replikace/převzetí služeb při selhání](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview), kde SSISDB v primární oblasti Azure s přístupem pro čtení i zápis (primární role) se průběžně replikuje do sekundární oblasti s přístupem jen pro čtení (sekundární role). Pokud dojde k havárii v primární oblasti, aktivuje se převzetí služeb při selhání, ve kterém se primární a sekundární SSISDBs přemění role.

V případě BCDR můžete také nakonfigurovat dvojice SSIS IR s duálním pohotovostním režimem, která funguje v synchronizaci se skupinou převzetí služeb při selhání Azure SQL Database/Managed instance. To vám umožňuje mít dvojici běžícího finančního úřadu Azure-SSIS, který v kterémkoli okamžiku získá přístup k primárnímu SSISDBu, aby bylo možné načíst a spustit balíčky, a také zapisovat protokoly spouštění balíčků (primární role), zatímco druhá může stejnou akci udělat jenom pro balíčky nasazené někde jinde, například v souborech Azure (sekundární role). Když dojde k převzetí služeb při selhání SSISDB, primární a sekundární finanční úřad Azure-SSIS také přemění role a pokud budou obě spuštěné, dojde k téměř nulovému výpadku.

Tento článek popisuje, jak nakonfigurovat Azure-SSIS IR se skupinou převzetí služeb při selhání Azure SQL Database/Managed instance pro BCDR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Nakonfigurujte dvojici dvou úspor v pohotovostním režimu Azure-SSIS IR se skupinou převzetí služeb Azure SQL Database Failover

Pokud chcete nakonfigurovat dvojici s duálním pohotovostním Azure-SSIS IR, která funguje v synchronizaci se skupinou převzetí služeb při selhání Azure SQL Database, proveďte následující kroky.

1. Pomocí Azure Portal/uživatelského rozhraní ADF můžete vytvořit nový Azure-SSIS IR s primárním Azure SQL Database serverem pro hostování SSISDB v primární oblasti. Pokud máte existující Azure-SSIS IR, která je už připojená k SSIDB, která je hostovaná vaším primárním Azure SQL Database serverem a pořád je spuštěná, musíte ji nejdřív zastavit a znovu ji nakonfigurovat. Toto bude primární Azure-SSIS IR.

   Pokud se [rozhodnete použít SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) na stránce **nastavení nasazení** v podokně **instalace prostředí Integration runtime** , zaškrtněte políčko **použít dvojici duálního úsporného režimu Azure-SSIS Integration runtime dvojici s převzetím služeb při selhání SSISDB** . Jako **název dvojice duálního úsporného režimu** zadejte název pro identifikaci páru primárních a sekundárních úřadů Azure-SSIS. Po dokončení vytváření primárního Azure-SSIS IR se spustí a připojí k primárnímu SSISDB, který bude vytvořen vaším jménem s přístupem pro čtení i zápis. Pokud jste ho právě překonfigurovali, musíte ho restartovat.

1. Pomocí Azure Portal můžete ověřit, zda byl primární SSISDB vytvořen na stránce **Přehled** na primárním serveru Azure SQL Database. Po vytvoření můžete [pro primární a sekundární Azure SQL Database servery vytvořit skupinu převzetí služeb při selhání a přidat do ní SSISDB](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#2---create-the-failover-group) na stránce **skupiny převzetí služeb při selhání** . Po vytvoření skupiny převzetí služeb při selhání můžete ověřit, zda byl primární SSISDB replikován do sekundárního serveru s přístupem jen pro čtení na stránce **Přehled** sekundárního serveru Azure SQL Database.

1. Pomocí Azure Portal/uživatelského rozhraní ADF můžete vytvořit další Azure-SSIS IR se sekundárním serverem Azure SQL Database k hostování SSISDB v sekundární oblasti. Toto budou sekundární Azure-SSIS IR. V případě úplných BCDR se ujistěte, že jsou v sekundární oblasti také vytvořeny všechny prostředky, na kterých závisí, například Azure Storage pro uložení vlastního instalačního skriptu nebo souborů, ADF pro orchestraci/plánování spouštění balíčků atd.

   Pokud se [rozhodnete použít SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) na stránce **nastavení nasazení** v podokně **instalace prostředí Integration runtime** , zaškrtněte políčko **použít dvojici duálního úsporného režimu Azure-SSIS Integration runtime dvojici s převzetím služeb při selhání SSISDB** . Pro **název dvojice duálního úsporného režimu** zadejte stejný název pro identifikaci páru primárních a sekundárních úřadů Azure-SSIS. Po dokončení vytváření sekundárního Azure-SSIS IR se spustí a připojí se k sekundárnímu SSISDB.

1. Pokud chcete mít téměř nulové prostoje, když dojde k převzetí služeb při selhání SSISDB, ponechte oba vaše finanční služby Azure-SSIS spuštěné. Pouze vaše primární Azure-SSIS IR mají přístup k primárnímu SSISDB pro načtení a spuštění balíčků a také zápis protokolů spouštění balíčků, zatímco sekundární Azure-SSIS IR může provádět stejnou akci pouze pro balíčky nasazené jinde, například v souborech Azure.

   Pokud chcete minimalizovat průběžné náklady, můžete po jejím vytvoření zastavit sekundární Azure-SSIS IR. Když dojde k převzetí služeb při selhání SSISDB, vaše primární a sekundární finanční úřad Azure-SSIS se zahodí role. Pokud je primární Azure-SSIS IR zastavený, musíte ho restartovat. V závislosti na tom, zda je vložena do virtuální sítě a použitá metoda vložení, bude trvat 5 minut nebo přibližně 20-30 minut, než se spustí.

1. Pokud [použijete ADF pro orchestraci nebo plánování provádění balíčků](./how-to-invoke-ssis-package-ssis-activity.md), ujistěte se, že všechny relevantní kanály ADF s SSIS aktivitami balíčků a přidruženými aktivačními událostmi se zkopírují do sekundárního ADF s zpočátku zakázanými triggery. Když dojde k převzetí služeb při selhání SSISDB, je potřeba je povolit.

1. Můžete [otestovat svou Azure SQL Database skupinu převzetí služeb při selhání](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#3---test-failover) a zkontrolovat na [stránce monitorování Azure-SSIS IR na portálu ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) , jestli vaše primární a sekundární finanční úřad Azure-SSIS přeměnila role. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Nakonfigurujte dvojici s duálním pohotovostním Azure-SSIS IR se skupinou převzetí služeb při selhání spravované instance SQL Azure.

Pokud chcete nakonfigurovat dvojici s duálním pohotovostním Azure-SSIS IR, která funguje v synchronizaci se skupinou převzetí služeb při selhání spravované instance Azure SQL, proveďte následující kroky.

1. Pomocí Azure Portal můžete [vytvořit skupinu převzetí služeb při selhání pro primární a sekundární spravované instance SQL Azure](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal) na stránce **skupiny převzetí služeb při selhání** vaší primární spravované instance Azure SQL.

1. Pomocí Azure Portal/uživatelského rozhraní ADF můžete vytvořit novou Azure-SSIS IR pomocí primární spravované instance Azure SQL k hostování SSISDB v primární oblasti. Pokud máte existující Azure-SSIS IR, která je už připojená k SSIDB, jejímž hostitelem je vaše primární spravovaná instance Azure SQL, a pořád je spuštěná, musíte ji nejdřív zastavit a znovu ji nakonfigurovat. Toto bude primární Azure-SSIS IR.

   Pokud se [rozhodnete použít SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) na stránce **nastavení nasazení** v podokně **instalace prostředí Integration runtime** , zaškrtněte políčko **použít dvojici duálního úsporného režimu Azure-SSIS Integration runtime dvojici s převzetím služeb při selhání SSISDB** . Jako **název dvojice duálního úsporného režimu** zadejte název pro identifikaci páru primárních a sekundárních úřadů Azure-SSIS. Po dokončení vytváření primárního Azure-SSIS IR se spustí a připojí k primárnímu SSISDB, který bude vytvořen vaším jménem s přístupem pro čtení i zápis. Pokud jste ho právě překonfigurovali, musíte ho restartovat. Můžete také ověřit, zda byl primární SSISDB replikován do sekundárního typu s přístupem jen pro čtení na stránce **Přehled** sekundární spravované instance Azure SQL.

1. Pomocí Azure Portal/uživatelského rozhraní ADF můžete vytvořit další Azure-SSIS IR pomocí sekundární spravované instance Azure SQL k hostování SSISDB v sekundární oblasti. Toto budou sekundární Azure-SSIS IR. V případě úplných BCDR se ujistěte, že jsou v sekundární oblasti také vytvořeny všechny prostředky, na kterých závisí, například Azure Storage pro uložení vlastního instalačního skriptu nebo souborů, ADF pro orchestraci/plánování spouštění balíčků atd.

   Pokud se [rozhodnete použít SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) na stránce **nastavení nasazení** v podokně **instalace prostředí Integration runtime** , zaškrtněte políčko **použít dvojici duálního úsporného režimu Azure-SSIS Integration runtime dvojici s převzetím služeb při selhání SSISDB** . Pro **název dvojice duálního úsporného režimu** zadejte stejný název pro identifikaci páru primárních a sekundárních úřadů Azure-SSIS. Po dokončení vytváření sekundárního Azure-SSIS IR se spustí a připojí se k sekundárnímu SSISDB.

1. Spravovaná instance Azure SQL dokáže zabezpečit citlivá data v databázích, jako je třeba SSISDB, a to tak, že je šifruje pomocí hlavního klíče databáze (DMK). Samotný DMK je ve výchozím nastavení šifrovaný pomocí hlavního klíče služby (SMK). V době psaní se skupina převzetí služeb při selhání spravované instance Azure SQL nereplikuje SMK z primární spravované instance Azure SQL, takže DMK a in Turn SSISDB se po převzetí služeb při selhání nedá dešifrovat v sekundární spravované instanci SQL Azure. Pokud chcete tento problém obejít, můžete přidat šifrování hesla pro DMK k dešifrování sekundární instance Azure SQL Managed. Pomocí SSMS proveďte následující kroky.

   1. Spusťte následující příkaz pro SSISDB v primární spravované instanci Azure SQL a přidejte heslo pro šifrování DMK.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. Spusťte následující příkaz pro SSISDB v primární i sekundární spravované instanci SQL Azure a přidejte nové heslo pro dešifrování DMK.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. Pokud chcete mít téměř nulové prostoje, když dojde k převzetí služeb při selhání SSISDB, ponechte oba vaše finanční služby Azure-SSIS spuštěné. Pouze vaše primární Azure-SSIS IR mají přístup k primárnímu SSISDB pro načtení a spuštění balíčků a také zápis protokolů spouštění balíčků, zatímco sekundární Azure-SSIS IR může provádět stejnou akci pouze pro balíčky nasazené jinde, například v souborech Azure.

   Pokud chcete minimalizovat průběžné náklady, můžete po jejím vytvoření zastavit sekundární Azure-SSIS IR. Když dojde k převzetí služeb při selhání SSISDB, vaše primární a sekundární finanční úřad Azure-SSIS se zahodí role. Pokud je primární Azure-SSIS IR zastavený, musíte ho restartovat. V závislosti na tom, zda je vložena do virtuální sítě a použitá metoda vložení, bude trvat 5 minut nebo přibližně 20-30 minut, než se spustí.

1. Pokud používáte [agenta spravované instance Azure SQL pro orchestraci nebo plánování provádění balíčků](./how-to-invoke-ssis-package-managed-instance-agent.md), ujistěte se, že se všechny relevantní úlohy SSIS se svými kroky úlohy a přidruženými plány zkopírují do sekundární spravované instance Azure SQL s počátečními plány, které jsou zpočátku zakázané. Pomocí SSMS proveďte následující kroky.

   1. Pro každou úlohu SSIS klikněte pravým tlačítkem myši a vyberte položku **úloha skriptu jako**, **vytvořit do** a nové položky rozevírací nabídky **okna editoru dotazů** pro vygenerování skriptu.

      ![Generovat skript úlohy SSIS](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. Pro každý generovaný skript úlohy SSIS Najděte příkaz pro spuštění `sp_add_job` uložené procedury a podle potřeby upravte nebo odeberte přiřazení hodnoty k `@owner_login_name` argumentu.

   1. U každého aktualizovaného skriptu úlohy SSIS spusťte ho na sekundární spravované instanci SQL Azure a zkopírujte úlohu s kroky úlohy a přidruženými plány.

   1. Pomocí následujícího skriptu vytvořte novou úlohu T-SQL, která povolí nebo zakáže plány úloh SSIS na základě role primárního/sekundárního SSISDB, v uvedeném pořadí, v primární i sekundární spravované instanci SQL Azure a pravidelně je spouští. Když dojde k převzetí služeb při selhání SSISDB, budou povolené plány úloh SSIS, které byly zakázané, a naopak.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Pokud [použijete ADF pro orchestraci nebo plánování provádění balíčků](./how-to-invoke-ssis-package-ssis-activity.md), ujistěte se, že všechny relevantní kanály ADF s SSIS aktivitami balíčků a přidruženými aktivačními událostmi se zkopírují do sekundárního ADF s zpočátku zakázanými triggery. Když dojde k převzetí služeb při selhání SSISDB, je potřeba je povolit.

1. Můžete [otestovat skupinu převzetí služeb při selhání spravované instance SQL Azure](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal#test-failover) a zkontrolovat [Azure-SSIS IR stránku monitorování na portálu ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) , ať už vaše primární a sekundární finanční úřad Azure-SSIS prohozené role. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Připojení nového Azure-SSIS IR k existujícímu SSISDB hostovanému pomocí Azure SQL Database/spravované instance

Pokud dojde k havárii a má vliv na existující Azure-SSIS IR, ale ne na Azure SQL Database/spravovanou instanci ve stejné oblasti, můžete ji nahradit novou v jiné oblasti. Pokud chcete připojit stávající SSISDB hostovaný pomocí Azure SQL Database/spravované instance k novému Azure-SSIS IR, proveďte následující kroky.

1. Pokud je stále spuštěná stávající Azure-SSIS IR, je nutné ji nejprve zastavit pomocí uživatelského rozhraní Azure Portal/ADF nebo Azure PowerShell. Pokud dojde k havárii i k ADF ve stejné oblasti, můžete tento krok přeskočit.

1. Pomocí SSMS spusťte následující příkaz pro SSISDB ve vaší Azure SQL Database/spravované instanci, abyste aktualizovali metadata, která umožní připojení z nového ADF nebo Azure-SSIS IR.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. Pomocí [Azure Portal/uživatelského rozhraní ADF](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) nebo [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)vytvořte nový ADF/Azure-SSIS IR s názvem *YourNewADF* / *YourNewAzureSSISIR* v jiné oblasti. Pokud používáte Azure Portal nebo uživatelské rozhraní ADF, můžete ignorovat chybu testu připojení na stránce **nastavení nasazení** **prostředí Integration runtime** .

## <a name="next-steps"></a>Další kroky

Tyto další možnosti konfigurace můžete zvážit pro Azure-SSIS IR:

- [Konfigurace úložišť balíčků pro Azure-SSIS IR](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Konfigurace vlastních nastavení pro Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Konfigurace vkládání virtuální sítě pro Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Konfigurace prostředí IR v místním prostředí jako proxy serveru pro Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)
