---
title: Řešení potíží s spouštění balíčku v prostředí SSIS integration runtime | Dokumentace Microsoftu
description: Tento článek obsahuje pokyny k odstraňování pro spouštění balíčku služby SSIS v prostředí SSIS Integration Runtime
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 7789970b47f0e55adee5bbe9da9f303aee6cdb25
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190119"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>Řešení potíží s spouštění balíčku v prostředí SSIS integration runtime

Tento článek obsahuje většinu běžných chyb, které pravděpodobně dojde při spouštění služby SSIS balíčky v prostředí SSIS Integration Runtime, potenciálních příčin a akce k vyřešení chyby.

## <a name="where-can-i-find-logs-for-troubleshoot"></a>Kde můžu najít protokoly pro řešení potíží

* Portál ADF slouží ke kontrole výstup aktivity spouštění balíčků služby SSIS včetně výsledek spuštění, chybové zprávy a ID operace. Podrobnosti najdete v [monitorování kanálu](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* Katalog služby SSIS (SSISDB) lze použít ke kontrole podrobností protokoly pro spuštění. Podrobnosti najdete tady [balíčky systémem monitorování a další operace](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>Běžné chyby, příčiny a řešení

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Chybová zpráva: `"Connection Timeout Expired."` nebo `"The service has encountered an error processing your request. Please try again."`

* Možná příčina a doporučená akce:
  * Datový zdroj/cíl je přetížen. Zkontrolujte zatížení na vaše Data zdroji nebo cíli a podívejte se, zda má dostatečnou kapacitu. Například pokud se používá Azure SQL, doporučujeme zvážit škálování nahoru v případě, že databáze je pravděpodobně vypršení časového limitu.
  * Síť mezi prostředí SSIS Integration Runtime a zdroje/cíle dat není stabilní, zejména v případě, že je připojení mezi různými oblastmi nebo mezi místní a azure. Doporučuje se použití modelu opakování v balíčku služby SSIS podle následujících kroků:
    * Ujistěte se, že balíčků služby SSIS můžete znovu spustit při selhání bez vedlejší účinek (např. ke ztrátě dat, data dup...)
    * Konfigurace **opakujte** a **interval opakování** spuštění aktivity SSIS balíčku na kartě Obecné ![nastavte vlastnosti na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Pro součást rozhraní ADO.NET a OLEDB. zdrojová a cílová ConnectRetryCount a ConnectRetryInterval lze nastavit ve Správci připojení v balíčku služby SSIS nebo aktivity SSIS

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Chybová zpráva: `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* Možná příčina a doporučená akce:
  * Tento problém obvykle znamená, že datový zdroj/cíl je nedostupný z prostředí SSIS Integration Runtime, který může být způsobeno různých důvodů, proč:
    * Ujistěte se, že jste předávání dat zdrojový/cílový název nebo IP správně
    * Ujistěte se, že brána firewall je správně nastavené.
    * Zajistěte, aby že vaše virtuální síť je správně nakonfigurované, pokud vaše zdrojová a cílová Data jsou v místní.
      * Můžete ověřit, zda tento problém je z konfigurace virtuální sítě tím, že zajistíte Virtuálním počítači Azure ve stejné virtuální síti. A potom zkontrolujte, zda datový zdroj/cíl je přístupný z virtuálních počítačů Azure
      * Můžete najít další podrobnosti o používání virtuální sítě pomocí prostředí SSIS Integration Runtime na [připojit k prostředí Azure-SSIS integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>Chybová zpráva: "`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`"

* Možná příčina a doporučená akce:
  * Poskytovatel ADO.NET použité v balíku není nainstalován v prostředí SSIS Integration Runtime. Zprostředkovatel můžete nainstalovat pomocí instalačního programu vlastní. Další podrobnosti o vlastní instalace najdete v [přizpůsobit nastavení pro prostředí Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Chybová zpráva: "`The connection '...' is not found`"

* Možná příčina a doporučená akce:
  * Tato chyba mohla vzniknout, protože je známý problém ve starší verze aplikace SSMS. Pokud balíček obsahuje vlastní součást (například služby SSIS Azure Feature Pack nebo 3. stran součásti), která není nainstalovaná na počítači použití SSMS provedete nasazení, komponenty aplikace SSMS se odebere a způsobí chybu. Upgrade [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) na nejnovější verzi, která má tento problém opravili.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Chybová zpráva: "Není dostatek místa na disku"

* Možná příčina a doporučená akce:
  * Tato chyba znamená, že v uzlu Integration Runtime služby SSIS je použít na místní disk. Zkontrolujte, jestli balíček nebo vlastní instalace by využívat mnoho prostory disku.
    * Pokud disk je využívána vašeho balíčku, ho uvolní po dokončení spouštění balíčku.
    * Pokud disk spotřebují vaše vlastní nastavení, budete muset zastavit prostředí SSIS Integration Runtime, váš skript upravit a znovu spusťte prostředí SSIS Integration Runtime. Celého kontejneru objektů Blob Azure, který jste zadali pro vlastní nastavení se překopírovaly SSIS IR uzlu, proto ověřte, zda je všechny nepotřebný obsah v tomto kontejneru.

### <a name="error-message-cannot-open-file-"></a>Chybová zpráva: "Nelze otevřít soubor"..."

* Možná příčina a doporučená akce:
  * Této chybě dochází při spuštění balíčku nelze najít soubor na místním disku v prostředí SSIS Integration Runtime.
    * Není určeno pro použití absolutní cestu v balíčku, provádí se v prostředí SSIS Integration Runtime. Pomocí aktuálního spuštění pracovního adresáře (.) nebo dočasné složky (% TEMP %) Místo toho.
    * Pokud ho potřebujete zachovat některé soubory na uzlech prostředí SSIS Integration Runtime, doporučujeme, abyste Příprava souborů pomocí [přizpůsobit nastavení](how-to-configure-azure-ssis-ir-custom-setup.md). Všechny soubory v pracovním adresáři spuštění se vyčistí po dokončení provádění.
    * Další možností je použít Azure File místo uložení souboru v uzlu Integration Runtime služby SSIS. Další podrobnosti najdete tady [ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Chybová zpráva: "" SSISDB"databáze dosáhla své kvóty velikosti"

* Možná příčina a doporučená akce:
  * Databáze SSISDB vytvořené v Azure SQL nebo spravované Instance při vytváření prostředí SSIS Integration Runtime dosáhla své kvóty.
    * Zvažte zvýšení DTU databáze k vyřešení tohoto problému. Podrobnosti najdete v [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * Zkontrolujte, zda váš balíček vygeneruje mnoho protokolů. Pokud ano, dá se Elastická úloha k vyčištění tyto protokoly. Odkazovat na [vyčištění databáze SSISDB protokoly s Elastickými úlohami databáze Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) podrobnosti.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Chybová zpráva: "Požadavek limit pro databázi je... a bylo ho dosaženo."

* Možná příčina a doporučená akce:
  * Pokud mnoho balíčků jsou spuštěny souběžně v prostředí SSIS Integration Runtime, této chybě může dojít, protože dosažení omezení požadavku SSISDB. Zvažte zvýšení DTC vaší databáze SSISDB k vyřešení tohoto problému. Podrobnosti najdete v [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Chybová zpráva: "SSIS operace se nezdařila se stavem neočekávaná operace:..."

* Možná příčina a doporučená akce:
  * Chyba je nejčastěji způsobeno o přechodnou chybu, to zkuste znovu spustit spouštění balíčku. Doporučuje se použití modelu opakování v balíčku služby SSIS podle následujících kroků:
    * Ujistěte se, že balíčků služby SSIS, můžete znovu spustit při selhání bez vedlejší účinek (třeba ztrátu dat, data dup...)
    * Konfigurace **opakujte** a **interval opakování** spuštění aktivity SSIS balíčku na kartě Obecné ![nastavte vlastnosti na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Pro součást rozhraní ADO.NET a OLEDB. zdrojová a cílová ConnectRetryCount a ConnectRetryInterval lze nastavit ve Správci připojení v balíčku služby SSIS nebo aktivity SSIS

### <a name="error-message-there-is-no-active-worker"></a>Chybová zpráva: "Není žádná aktivní pracovní."

* Možná příčina a doporučená akce:
  * Tato chyba obvykle znamená, že prostředí SSIS Integration Runtime je ve stavu není v pořádku. Zkontrolujte Azure portal pro stav a podrobnosti chyby: [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Chybová zpráva: "Vašeho prostředí integration runtime nelze upgradovat a nakonec kód přestane fungovat, protože nemůže přistupovat k kontejneru objektů Blob v Azure, které jste zadali pro vlastní nastavení."

* Tato chyba nastane, pokud prostředí SSIS Integration Runtime nelze získat přístup k úložišti nakonfigurovaný pro vlastní nastavení. Zkontrolujte, zda je zadaný identifikátor Uri SAS je platný a že nevypršela platnost.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Chybová zpráva: "Zprostředkovatel Microsoft OLE DB pro Analysis Services. ' Hresult: 0x80004005 Popis: " Chyba COM: Chyba COM: mscorlib; Došlo k výjimce způsobené cílem vyvolání"

* Možná příčina a doporučená akce:
  * Jednou možnou příčinou je tohoto uživatelského jména a hesla pomocí MFA povoleno je nakonfigurován pro ověřování služby Azure Analysis Services, které se zatím nepodporuje v prostředí SSIS integration runtime. Zkuste použít instanční objekt služby pro ověřování pomocí služby Azure Analysis Service:
    1. Příprava AAS instančního objektu [https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
    2. Správce připojení, nakonfigurujte "Pomocí konkrétní uživatelské jméno a heslo": "AppID" nastavit jako uživatelské jméno a "clientSecret" jako heslo

### <a name="package-takes-unexpected-long-time-to-execute"></a>Balíček má neočekávaný dlouhý čas ke spuštění

* Možná příčina a doporučená akce:
  * Příliš mnoho spuštění balíčku bylo naplánováno na prostředí SSIS Integration Runtime. V takovém případě tyto spuštění bude čekat ve frontě pro jejich zapnout ke spuštění.
    * Maximální počet souběžných spuštění počet na prostředí IR = počet uzlů * maximální počet souběžných spuštění na uzel
    * Odkazovat na [vytvořit prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory](create-azure-ssis-integration-runtime.md) ke způsobu nastavení počtu uzlů a maximální počet paralelní provádění na jeden uzel.
  * Zastavení prostředí SSIS Integration Runtime nebo ve stavu není v pořádku. Zkontrolujte [prostředí Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime) pro zjištění stavu prostředí SSIS Integration Runtime a chyb.
  * Doporučuje se nastavit časový limit, pokud si nejste jisti, že spouštění balíčku by měl být dokončeno za určitou dobu: ![Nastavte vlastnosti na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>Nízký výkon při provádění balíčku

* Možná příčina a doporučená akce:

  * Zkontrolujte, jestli je prostředí SSIS Integration Runtime ve stejné oblasti jako zdroj a cíl.

  * Povolit úroveň protokolování "Výkonu"

      Můžete nastavit úroveň protokolování spouštění balíčku "Výkonu" shromažďovat další podrobné informace doby trvání pro jednotlivé komponenty za běhu. Podrobnosti najdete na následujících stránkách: [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * Kontrola výkonu uzel reakcí na Incidenty na stránce monitorování reakcí na Incidenty v portálu Azure portal.
    * Jak monitorovat prostředí SSIS Integration Runtime: [Prostředí Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * Historie využití procesoru/paměti prostředí SSIS Integration runtime je k dispozici na metriky služby Data Factory na webu Azure portal ![monitorovat metriky prostředí SSIS Integration runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
