---
title: Řešení potíží s spouštění balíčku v prostředí SSIS integration runtime | Dokumentace Microsoftu
description: Tento článek obsahuje pokyny k odstraňování problémů pro spouštění balíčku služby SSIS v prostředí SSIS integration runtime
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
ms.openlocfilehash: 68a5d5278e1181695695647cff187d4b95624b40
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537642"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Řešení potíží s spouštění balíčku v prostředí SSIS integration runtime

Tento článek obsahuje většinu běžných chyb, které je možné, když provedete balíčky SQL Server Integration Services (SSIS) v prostředí SSIS integration runtime. Popisuje možných příčinách a akce k vyřešení chyby.

## <a name="where-to-find-logs-for-troubleshooting"></a>Kde najít protokoly pro řešení potíží

Zkontrolujte výstup aktivity spouštění balíčku služby SSIS pomocí portálu Azure Data Factory. Výstup obsahuje výsledek spuštění, chybové zprávy a ID operace. Podrobnosti najdete v tématu [monitorování kanálu](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

V protokolech podrobností pro provádění pomocí katalogu služby SSIS (SSISDB). Podrobnosti najdete v tématu [balíčky systémem monitorování a další operace](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Běžné chyby, příčiny a řešení

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Chybová zpráva: "Vypršel časový limit připojení" nebo "služba zjistil chybu při zpracování vaší žádosti. Zkuste to prosím znovu."

Tady jsou možné příčiny a doporučené akce:
* Zdroj nebo cíl je přetížen. Zkontrolujte zatížení na zdroj nebo cíl a podívejte se, zda má dostatečnou kapacitu. Pokud jste použili Azure SQL Database, zvažte například vertikální navýšení kapacity, pokud je databáze pravděpodobně vypršení časového limitu.
* Síť mezi prostředí SSIS integration runtime a zdroj nebo cíl není stabilní, zejména v případě, že je připojení mezi různými oblastmi nebo mezi místními a Azure. Použití modelu opakování v balíčku služby SSIS pomocí následujících kroků:
  * Zajistěte, aby že balíčků služby SSIS, můžete znovu spustit při selhání bez vedlejších účinků (třeba ztrátu dat nebo duplicitních dat.).
  * Konfigurace **opakujte** a **interval opakování** z **spuštění balíčku služby SSIS** aktivity **Obecné** kartu. ![Nastavte vlastnosti na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Pro technologii ADO.NET a technologie OLE DB zdroji nebo cíli komponentu, nastavte **ConnectRetryCount** a **ConnectRetryInterval** ve Správci připojení v balíčku služby SSIS nebo aktivity SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Chybová zpráva: "ADO NET zdroj se nepodařilo získat připojení '...'" s "při navazování připojení k serveru SQL Server došlo k chybě související se sítí nebo s instancí. Server nebyl nalezen nebo nebyl přístupný. "

Tento problém obvykle znamená, že zdroj dat nebo cíl je nedostupný z prostředí SSIS integration runtime. Důvody se může lišit. Vyzkoušejte tyto akce:
* Ujistěte se, že jste předávání dat zdrojový nebo cílový název nebo IP správně.
* Ujistěte se, že že brána firewall je nastavená správně.
* Ujistěte se, že vaše virtuální síť je správně nakonfigurována, pokud je zdroj dat nebo cílové místní:
  * Můžete ověřit, zda tento problém je z konfigurace virtuální sítě tím, že zajistíte Virtuálním počítači Azure ve stejné virtuální síti. Zkontrolujte, zda zdroj nebo cíl je přístupný z virtuálních počítačů Azure.
  * Můžete najít další podrobnosti o použití virtuální sítě pomocí prostředí SSIS integration runtime v [připojit k prostředí Azure-SSIS integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Chybová zpráva: "ADO NET zdroj se nepodařilo získat připojení '...'" s "Nelze vytvořit spravovaného připojení správce."

Možnou příčinou je, že poskytovatele ADO.NET použité v balíku není nainstalován v prostředí SSIS integration runtime. Zprostředkovatel můžete nainstalovat pomocí vlastního nastavení. Můžete najít další podrobnosti o vlastním nastavením v [přizpůsobit nastavení pro prostředí Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Chybová zpráva: "Připojení"..." nebyl nalezen"

Tuto chybu může způsobovat známý problém ve starších verzích systému SQL Server Management Studio (SSMS). Pokud balíček obsahuje vlastní součásti (například součásti služby SSIS Azure Feature Pack nebo partnerem), která není nainstalovaná na počítači použití SSMS provedete nasazení, aplikace SSMS se odebrat komponentu a způsobit chybu. Upgrade [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) na nejnovější verzi, která má tento problém opravili.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Chybová zpráva: "Není dostatek místa na disku"

Tato chyba znamená, že v uzlu integration runtime služby SSIS je použít na místní disk. Zkontrolujte, zda balíček nebo vlastní nastavení spotřebovává velké množství místa na disku:
* Pokud disk je využívána vašeho balíčku, ho uvolní po dokončení spouštění balíčku.
* Pokud disk spotřebují vaše vlastní nastavení, je budete potřebovat zastavit prostředí SSIS integration runtime, váš skript upravit a znovu spusťte modul runtime integrace. Kontejner objektů blob v Azure celou, který jste zadali pro vlastní nastavení se zkopírují do uzlu integration runtime služby SSIS, tak zkontrolujte, jestli všechny nepotřebný obsah v tomto kontejneru.

### <a name="error-message-cannot-open-file-"></a>Chybová zpráva: "Nelze otevřít soubor"..."

Této chybě dochází při spuštění balíčku nemůže najít soubor na místní disk v prostředí SSIS integration runtime. Vyzkoušejte tyto akce:
* Nepoužívejte absolutní cestu v balíčku, která je spouštěna v prostředí SSIS integration runtime. Použít aktuální pracovní adresář pro spuštění (.) nebo dočasné složky (% TEMP %) Místo toho.
* Pokud potřebujete zachovat některé soubory na uzly integration runtime služby SSIS, připravte soubory podle postupu v [přizpůsobit nastavení](how-to-configure-azure-ssis-ir-custom-setup.md). Všechny soubory v pracovním adresáři se vyčistí po dokončení provádění.
* Místo uložení souboru uzel prostředí SSIS integration runtime použijte soubory Azure. Podrobnosti najdete v tématu [sdílených složek Azure pomocí](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Chybová zpráva: "" SSISDB"databáze dosáhla své kvóty velikosti"

Možnou příčinou je, že databáze SSISDB vytvořené v Azure SQL database nebo spravované instance při vytváření prostředí SSIS integration runtime dosáhla své kvóty. Vyzkoušejte tyto akce:
* Zvažte zvýšení DTU databáze. Můžete najít podrobnosti v [limity prostředků SQL Database pro server Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Zkontrolujte, zda váš balíček vygeneruje mnoho protokolů. Pokud ano, můžete nakonfigurovat elastické úlohy k vyčištění tyto protokoly. Podrobnosti najdete v tématu [vyčištění databáze SSISDB protokoly s úlohy elastické databáze Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Chybová zpráva: "Požadavek limit pro databázi je... a bylo ho dosaženo."

Pokud mnoho balíčků běží paralelně v prostředí SSIS integration runtime, této chybě může dojít, protože databáze SSISDB dosáhl svého limitu požadavku. Zvažte zvýšení SSISDB DTC, chcete-li vyřešit tento problém. Můžete najít podrobnosti v [limity prostředků SQL Database pro server Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Chybová zpráva: "SSIS operace se nezdařila se stavem neočekávaná operace:..."

Chyba je nejčastěji způsobeno o přechodný problém, to zkuste znovu spustit spouštění balíčku. Použití modelu opakování v balíčku služby SSIS pomocí následujících kroků:

* Zajistěte, aby že balíčků služby SSIS, můžete znovu spustit při selhání bez vedlejších účinků (třeba ztrátu dat nebo duplicitních dat.).
* Konfigurace **opakujte** a **interval opakování** z **spuštění balíčku služby SSIS** aktivity **Obecné** kartu. ![Nastavte vlastnosti na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Pro technologii ADO.NET a technologie OLE DB zdroji nebo cíli komponentu, nastavte **ConnectRetryCount** a **ConnectRetryInterval** ve Správci připojení v balíčku služby SSIS nebo aktivity SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Chybová zpráva: "Není žádná aktivní pracovní."

Tato chyba obvykle znamená, že prostředí SSIS integration runtime se stavem není v pořádku. Zkontrolujte na webu Azure portal pro stav a podrobné chyby. Další informace najdete v tématu [prostředí Azure-SSIS integration runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Chybová zpráva: "Vašeho prostředí integration runtime nelze upgradovat a nakonec kód přestane fungovat, protože nemůže přistupovat k kontejneru objektů Blob v Azure, které jste zadali pro vlastní nastavení."

Tato chyba nastane, pokud prostředí SSIS integration runtime nelze získat přístup k úložišti nakonfigurované pro vlastní nastavení. Zkontrolujte, zda sdílený přístupový podpis (SAS) identifikátor URI, který jste zadali platné a že nevypršela platnost.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Chybová zpráva: "Zprostředkovatel Microsoft OLE DB pro Analysis Services. ' Hresult: 0x80004005 Popis: " Chyba COM: Chyba COM: mscorlib; Došlo k výjimce způsobené cílem vyvolání"

Jednou z možných příčin je, že uživatelské jméno nebo heslo s povolené ověřování Azure Multi-Factor Authentication je nakonfigurován pro ověřování služby Azure Analysis Services. Toto ověřování není podporována v prostředí SSIS integration runtime. Zkuste použít instanční objekt služby pro ověřování služby Azure Analysis Services:
1. Příprava instanční objekt služby, jak je popsáno v [automatizace pomocí instančních objektů](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. Nakonfigurujte ve Správci připojení **použít konkrétní uživatelské jméno a heslo**: nastavte **AppID** jako uživatelské jméno a **clientSecret** jako heslo.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Chybová zpráva: "ADONET zdroj se nepodařilo získat připojení {GUID} chybová zpráva: Přihlášení uživatele "NT AUTHORITY\ANONYMOUS přihlásit" se nezdařilo "při použití spravované identity

Ujistěte se, že není konfigurace metody ověření sady Správce připojení jako **ověřování hesla Active Directory** při parametr *ConnectUsingManagedIdentity* je **True** . Můžete je nakonfigurovat jako **ověřování SQL** místo toho, která se ignoruje, pokud *ConnectUsingManagedIdentity* nastavena.

### <a name="package-execution-takes-too-long"></a>Spouštění balíčků trvá příliš dlouho

Tady jsou možné příčiny a doporučené akce:
* Příliš mnoho spuštění balíčku bylo naplánováno na prostředí SSIS integration runtime. Všechny tyto spuštění bude čekat ve frontě jejich zapnout.
  * Určete maximální pomocí tohoto vzorce: 
    
    Maximální počet souběžných spuštění počet na prostředí IR = počet uzlů * maximální počet souběžných spuštění na uzel
  * Zjistěte, jak nastavit počet uzlů a maximální paralelní provádění na jeden uzel, najdete v článku [vytvořit prostředí Azure-SSIS integration runtime ve službě Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Prostředí SSIS integration runtime se zastavil nebo má chybný stav. Zjistěte, jak zkontrolovat stav služby SSIS integration runtime a chyb, naleznete v tématu [prostředí Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Doporučujeme také, že nastavíte vypršení časového limitu pro **Obecné** kartu: ![Nastavte vlastnosti na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png).

### <a name="poor-performance-in-package-execution"></a>Nízký výkon při provádění balíčku

Vyzkoušejte tyto akce:

* Ujistěte se, že prostředí SSIS integration runtime je ve stejné oblasti jako zdroj a cíl.

* Nastavení úrovně protokolování spuštění balíčku **výkonu** shromažďovat informace o době trvání pro jednotlivé komponenty za běhu. Podrobnosti najdete v tématu [Integration Services (SSIS) protokolování](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Kontrola výkonu uzel reakcí na Incidenty v portálu Azure portal:
  * Informace o tom, jak monitorovat prostředí SSIS integration runtime najdete v tématu [prostředí Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Historie využití procesoru/paměti pro prostředí SSIS integration runtime najdete zobrazením metriky služby data factory na webu Azure Portal.
    ![Monitorování metrik prostředí SSIS integration runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
