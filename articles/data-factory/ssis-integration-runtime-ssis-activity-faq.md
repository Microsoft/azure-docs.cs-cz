---
title: Řešení potíží se spuštěním balíčku v prostředí SSIS Integration runtime
description: Tento článek popisuje pokyny k odstraňování problémů s SSIS balíčkem v prostředí SSIS Integration runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: b4902e1fb7a2a181d3d5b2ce2ac6d1d458500fce
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844178"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Řešení potíží se spuštěním balíčku v prostředí SSIS Integration runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek obsahuje nejběžnější chyby, se kterými se můžete setkat při provádění balíčků služba SSIS (SQL Server Integration Services) (SSIS) v prostředí SSIS Integration runtime. Popisuje možné příčiny a akce pro řešení chyb.

## <a name="where-to-find-logs-for-troubleshooting"></a>Kde najít protokoly pro řešení potíží

Pomocí portálu Azure Data Factory můžete kontrolovat výstup aktivity provádění balíčku SSIS. Výstup obsahuje výsledky spuštění, chybové zprávy a ID operace. Podrobnosti najdete v tématu [monitorování kanálu](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Pomocí katalogu SSIS (SSISDB) si prohlédněte protokoly podrobností pro spuštění. Podrobnosti najdete v tématu [monitorování spuštěných balíčků a dalších operací](/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Běžné chyby, jejich příčiny a řešení

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Chybová zpráva: vypršel časový limit připojení nebo služba zjistila chybu při zpracování vaší žádosti. Zkuste to znovu.

Tady jsou možné příčiny a doporučené akce:
* Zdroj dat nebo cíl je přetížený. Zkontrolujte zatížení zdroje dat nebo cíle a zjistěte, zda má dostatečnou kapacitu. Pokud jste například použili Azure SQL Database, zvažte horizontální navýšení kapacity databáze, pokud je pravděpodobně vyprší časový limit databáze.
* Síť mezi integrací prostředí SSIS Integration runtime a zdrojem nebo cílem dat je nestabilní, zejména v případě, že je spojení mezi oblastmi nebo mezi místními a Azure. Použijte vzor opakování v balíčku SSIS pomocí následujících kroků:
  * Ujistěte se, že vaše balíčky SSIS můžou znovu spustit při selhání bez vedlejších účinků (například ztráty dat nebo duplikace dat).
  * Na kartě **Obecné** nakonfigurujte **akci opakovat** a **interval opakování** akce **SSIS balíčku pro spuštění** . ![ Nastavení vlastností na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * U ADO.NET OLE DB a zdrojové nebo cílové součásti nastavte **atributu ConnectRetryCount** a **atributu ConnectRetryInterval** ve Správci připojení v části balíček SSIS nebo aktivita SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Chybová zpráva: připojení k síti ADO se nepodařilo získat připojení... Při navazování připojení k SQL Server došlo k chybě související se sítí nebo instancí. Server se nenašel nebo nebyl dostupný.“

Tento problém obvykle znamená, že prostředí SSIS Integration Runtime nemá přístup ke zdroji nebo cíli dat. Důvodů může být několik. Vyzkoušejte tyto akce:
* Ujistěte se, že jste správně předali zdroj dat nebo cílový název nebo IP adresu.
* Ujistěte se, že je brána firewall správně nastavená.
* Ujistěte se, že je vaše virtuální síť správně nakonfigurovaná, pokud je zdroj dat nebo cíl místní:
  * Vytvořením virtuálního počítače Azure ve stejné virtuální síti můžete ověřit, jestli se jedná o problém z konfigurace virtuální sítě. Pak zkontrolujte, jestli je k dispozici zdroj dat nebo cíl z virtuálního počítače Azure.
  * Další podrobnosti o používání virtuální sítě s SSIS Integration runtime najdete v prostředí [Azure-SSIS Integration runtime do virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Chybová zpráva: připojení k síti ADO se nepodařilo získat připojení... pomocí příkazu se nepodařilo vytvořit spravovaného Správce připojení.

Potenciální příčinou je to, že zprostředkovatel ADO.NET použitý v balíčku není nainstalovaný v prostředí SSIS Integration runtime. Zprostředkovatele můžete nainstalovat pomocí vlastní instalace. Další podrobnosti o vlastní instalaci najdete v [části Přizpůsobení nastavení pro prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Chybová zpráva: "připojení"... nebyl nalezen "

Tuto chybu může způsobit známý problém ve starších verzích aplikace SQL Server Management Studio (SSMS). Pokud balíček obsahuje vlastní komponentu (například Azure Feature Pack pro SSIS nebo komponenty partnerů), která není nainstalovaná na počítači, kde se provádí nasazení pomocí SSMS, SSMS komponentu odebere a způsobí chybu. Upgradujte [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) na nejnovější verzi, ve které je tento problém opravený.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Chybová zpráva: "ukončovací kód prováděcího modulu SSIS:-1073741819."

* Potenciální příčina a doporučená akce:
  * Tato chyba může být způsobena omezením pro zdroj a cíl aplikace Excel, pokud je více zdrojů nebo cíle aplikace Excel prováděno paralelně ve více vláknech. Toto omezení můžete vyřešit tak, že změníte komponenty aplikace Excel tak, aby se spouštěly v pořadí, nebo je rozbalíte do různých balíčků a aktivujete je pomocí možnosti Spustit úlohu balíčku s vlastností ExecuteOutOfProcess nastavenou na hodnotu true.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Chybová zpráva: na disku není dostatek místa.

Tato chyba znamená, že se místní disk používá v uzlu SSIS Integration runtime. Ověřte, jestli váš balíček nebo vlastní instalační program spotřebovává spoustu místa na disku:
* Pokud je disk spotřebován balíčkem, bude po dokončení spuštění balíčku uvolněn.
* Pokud je disk spotřebován vlastním nastavením, budete muset zastavit SSIS Integration runtime, upravit skript a znovu spustit prostředí Integration runtime. Celý kontejner Azure Blob, který jste zadali pro vlastní instalaci, se zkopíruje do uzlu SSIS Integration runtime, takže ověřte, jestli je v tomto kontejneru nějaký nepotřebný obsah.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Chybová zpráva: Nepodařilo se načíst prostředek z hlavního serveru. Microsoft. SqlServer. IntegrationServices. Scale. ScaleoutContract. Common. MasterResponseFailedException: code: 300004. Popis: načtení souboru "* * *" se nezdařilo. "

* Potenciální příčina a doporučená akce:
  * Pokud aktivita SSIS spouští balíček ze systému souborů (soubor balíčku nebo soubor projektu), k této chybě dojde, pokud projekt, balíček nebo konfigurační soubor není přístupný s přihlašovacími údaji pro přístup k balíčku, které jste zadali v aktivitě SSIS
    * Pokud používáte soubor Azure:
      * Cesta k souboru by měla začínat na \\ \\ \<storage account name\> . File.Core.Windows.NET\\\<file share path\>
      * Doména by měla být "Azure".
      * Uživatelské jméno by mělo být \<storage account name\>
      * Heslo by mělo být \<storage access key\>
    * Pokud používáte místní soubor, zkontrolujte prosím, jestli je virtuální síť, přihlašovací údaje pro přístup k balíčku a oprávnění správně nakonfigurované, aby váš prostředí Azure-SSIS Integration runtime mělo přístup k místní sdílené složce.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Chybová zpráva: "název souboru"... zadaný v připojení není platný.

* Potenciální příčina a doporučená akce:
  * Je zadaný neplatný název souboru.
  * Ujistěte se, že používáte plně kvalifikovaný název domény (plně kvalifikovaný název domény) místo krátkého času ve Správci připojení.

### <a name="error-message-cannot-open-file-"></a>Chybová zpráva: nejde otevřít soubor...

K této chybě dojde, když spuštění balíčku nemůže najít soubor na místním disku v prostředí SSIS Integration runtime. Vyzkoušejte tyto akce:
* Nepoužívejte absolutní cestu v balíčku, který je spuštěn v prostředí SSIS Integration runtime. Použijte aktuální pracovní adresář spuštění (.) nebo složku Temp (% TEMP%). takové.
* Pokud potřebujete zachovat některé soubory v uzlech SSIS Integration runtime, připravte soubory, jak je popsáno v tématu [přizpůsobení instalace](how-to-configure-azure-ssis-ir-custom-setup.md). Po dokončení spuštění budou všechny soubory v pracovním adresáři vyčištěny.
* Místo uložení souboru v uzlu Integration runtime SSIS použijte soubory Azure. Podrobnosti najdete v tématu [použití sdílených složek Azure](/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Chybová zpráva: "databáze ' SSISDB ' dosáhla kvóty velikosti"

Možnou příčinou je, že databáze SSISDB vytvořená v Azure SQL Database nebo ve spravované instanci SQL dosáhla své kvóty. Vyzkoušejte tyto akce:
* Zvažte navýšení počtu DTU vaší databáze. Podrobnosti najdete v [SQL Database omezeních pro logický Server](../azure-sql/database/resource-limits-logical-server.md).
* Zkontrolujte, jestli váš balíček negeneruje velké množství protokolů. Pokud ano, můžete nakonfigurovat elastickou úlohu, která tyto protokoly vyčistí. Podrobnosti najdete v tématu [Vyčištění protokolů SSISDB s využitím úloh elastické databáze Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Chybová zpráva: "omezení požadavků pro databázi je... a byl dosažen. "

Pokud mnoho balíčků běží paralelně v prostředí SSIS Integration runtime, může dojít k této chybě, protože SSISDB dosáhla svého limitu žádosti. Pokud chcete tento problém vyřešit, zvažte zvýšení SSISDB DTC. Podrobnosti najdete v [SQL Database omezeních pro logický Server](../azure-sql/database/resource-limits-logical-server.md).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Chybová zpráva: operace SSIS se nezdařila, došlo k neočekávanému stavu operace:...

Tato chyba je většinou způsobena přechodným problémem, proto zkuste znovu spustit spuštění balíčku. Použijte vzor opakování v balíčku SSIS pomocí následujících kroků:

* Ujistěte se, že vaše balíčky SSIS můžou znovu spustit při selhání bez vedlejších účinků (například ztráty dat nebo duplikace dat).
* Na kartě **Obecné** nakonfigurujte **akci opakovat** a **interval opakování** akce **SSIS balíčku pro spuštění** . ![ Nastavení vlastností na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* U ADO.NET OLE DB a zdrojové nebo cílové součásti nastavte **atributu ConnectRetryCount** a **atributu ConnectRetryInterval** ve Správci připojení v části balíček SSIS nebo aktivita SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Chybová zpráva: neexistuje žádný aktivní pracovní proces.

Tato chyba obvykle znamená, že modul runtime integrace SSIS nemá stav v pořádku. Stav a podrobné chyby najdete v Azure Portal. Další informace najdete v tématu [prostředí Azure-SSIS Integration runtime](./monitor-integration-runtime.md#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Chybová zpráva: "váš modul runtime integrace nelze upgradovat a nakonec přestane fungovat, protože nemůžeme získat přístup k kontejneru objektů BLOB v Azure, který jste zadali pro vlastní instalaci."

K této chybě dojde, když prostředí Integration runtime SSIS nemá přístup k úložišti nakonfigurovanému pro vlastní instalaci. Ověřte, jestli zadaný identifikátor URI sdíleného přístupového podpisu (SAS) je platný a že nevypršela jeho platnost.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Chybová zpráva: poskytovatel Microsoft OLE DB pro Analysis Services. HRESULT: 0x80004005 Popis: ' Chyba modelu COM: Chyba COM: mscorlib; Cíl vyvolání způsobil výjimku.

Jednou z možných příčin je, že uživatelské jméno nebo heslo s povoleným Azure AD Multi-Factor Authentication je nakonfigurované pro ověřování Azure Analysis Services. Toto ověřování není podporováno v prostředí SSIS Integration runtime. Zkuste použít instanční objekt pro Azure Analysis Services ověřování:

1. Připravte instanční objekt, jak je popsáno v tématu [automatizace s instančními objekty](../analysis-services/analysis-services-service-principal.md).
2. V programu Správce připojení nakonfigurujte **použití konkrétního uživatelského jména a hesla**: jako uživatelské jméno nastavte **AppID** a jako heslo **clientSecret** .

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Chybová zpráva: "zdroji ADONET se nepodařilo získat připojení {GUID} pomocí následující chybové zprávy: přihlášení uživatele ' NT AUTHORITY\ANONYMOUS LOGON ' při použití spravované identity se nezdařilo.

Pokud má parametr *ConnectUsingManagedIdentity* **hodnotu true**, ujistěte se, že jste nenakonfigurovali metodu ověřování Správce připojení jako **ověřování hesla služby Active Directory** . Místo toho ho můžete nakonfigurovat jako **ověřování SQL** , které se ignoruje, pokud je nastavená možnost *ConnectUsingManagedIdentity* .

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Chybová zpráva: 0xC020801F na..., zdroj OData [...]: nejde získat spravované připojení ze Správce připojení za běhu.

Jednou z možných příčin je, že protokol TLS (Transport Layer Security) není povolený v prostředí SSIS Integration runtime, které vyžaduje váš zdroj OData. Protokol TLS v prostředí SSIS Integration runtime můžete povolit pomocí přizpůsobení nastavení. Další podrobnosti najdete v části [nejde připojit Project Online OData od SSIS](/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) a [přizpůsobení nastavení pro prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Chybová zpráva: "žádost o pracovní úlohu s identifikátorem GUID operace... selhání z důvodu chyby: odeslání pracovní operace se nezdařilo. chybová zpráva: Microsoft. SqlServer. IntegrationServices. AisAgentCore. AisAgentException: Nepodařilo se načíst data proxy. "

Ujistěte se, že je prostředí Azure-SSIS Integration runtime nakonfigurované s modulem runtime integrace Self-Hosted. Další podrobnosti najdete v části [konfigurace Self-Hosted IR jako proxy pro Azure-SSIS IR v ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Chybová zpráva: stav pracovní úlohy: nezdařilo se. Chyba přípravného úkolu: ErrorCode: 2010, ErrorMessage: Integration Runtime v místním prostředí... je offline

Ujistěte se, že je nainstalovaný a spuštěný modul runtime integrace Self-Hosted. Další podrobnosti najdete v článku [Vytvoření a konfigurace prostředí Integration runtime](create-self-hosted-integration-runtime.md) v místním prostředí.

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Chybová zpráva: Chyba přípravného úkolu: ErrorCode: 2906, ErrorMessage: provedení balíčku se nezdařilo., výstup: {"OperationErrorMessages": "Chyba: požadovaný poskytovatel OLE DB... není zaregistrováno. Pokud není nainstalován ovladač 64, spusťte balíček v režimu 32... "

Zajistěte, aby odpovídající zprostředkovatel používaný konektory OLE DB ve vašem balíčku byl správně nainstalovaný v počítači Self-Hosted Integration runtime. Další podrobnosti najdete v části [konfigurace Self-Hosted IR jako proxy pro Azure-SSIS IR v ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir) .

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Chybová zpráva: Chyba pracovní úlohy: ErrorCode: 2906, ErrorMessage: provedení balíčku se nezdařilo., výstup: {"OperationErrorMessages": "Error: System. IO. FileLoadException: nelze načíst soubor nebo sestavení" Microsoft. WindowsAzure. Storage, verze =..., Culture = neutral, PublicKeyToken = 31bf3856ad364e35 "nebo jedna z jeho závislostí. Nalezená definice manifestu sestavení neodpovídá odkazu na sestavení. ' ..."

Jednou z možných příčin je, že váš Self-Hosted Integration runtime není správně nainstalovaný nebo upgradovaný. Navrhněte stažení a opětovnou instalaci nejnovějšího prostředí Integration runtime v místním prostředí. Další podrobnosti najdete v článku [Vytvoření a konfigurace prostředí Integration runtime](create-self-hosted-integration-runtime.md#installation-best-practices) v místním prostředí.

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Chybová zpráva: při vyžádání metadat se vyžaduje připojení. Pokud pracujete offline, zrušte v nabídce SSIS možnost pracovat offline, aby se povolilo připojení.

* Potenciální příčina a doporučená akce:
  * Pokud se zobrazí také varovná zpráva "komponenta nepodporuje použití Správce připojení s hodnotou ConnectByProxy Value true" v protokolu spuštění to znamená, že se správce připojení používá u komponenty, která ještě není podporována "ConnectByProxy". Podporované součásti najdete v části [konfigurace Self-Hosted IR jako proxy pro Azure-SSIS IR v ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy) .
  * Protokol spuštění najdete v [sestavě SSMS](/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) nebo ve složce protokolu, kterou jste zadali v aktivitě spuštění balíčku SSIS.
  * Virtuální síť se dá také použít pro přístup k místním datům jako alternativu. Další podrobnosti najdete v článku [připojení prostředí Azure-SSIS Integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md) .

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Chybová zpráva: stav pracovní úlohy: nezdařilo se. Chyba přípravné úlohy: ErrorCode: 2906, ErrorMessage: provedení balíčku se nezdařilo., výstup: {"OperationErrorMessages": "SSIS prováděcího modulu vykonavatele:-1. \ n"; "LogLocation": "... \\ SSISTelemetry \\ ExecutionLog \\ ... "," effectiveIntegrationRuntime ":"... "," executionDuration ":...," durationInQueue ": {" integrationRuntimeQueue ":...}}"

Ujistěte se, že je v počítači Self-Hosted Integration Runtime nainstalován modul runtime Visual C++. Další podrobnosti najdete v části [konfigurace Self-Hosted IR jako proxy pro Azure-SSIS IR v ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir) .

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Neočekávaně se spouští více spuštění balíčku.

* Potenciální příčina a doporučená akce:
  * Aktivita uložená procedura ADF nebo aktivita vyhledávání slouží ke spuštění SSIS balíčku. Příkaz t-SQL může způsobit přechodný problém a aktivovat opětovné spuštění, které by způsobilo více spuštění balíčku.
  * Místo toho použijte aktivitu ExecuteSSISPackage, která zajistí, že spuštění balíčku nebude znovu spuštěno, pokud uživatel nenastaví počet opakování v aktivitě. Podrobnosti najdete na adrese. [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](./how-to-invoke-ssis-package-ssis-activity.md)
  * Upřesněte svůj příkaz t-SQL, aby se mohl znovu spustit, a to tak, že zkontroluje, jestli už se spustilo spuštění.

### <a name="package-execution-takes-too-long"></a>Provádění balíčku trvá moc dlouho.

Tady jsou možné příčiny a doporučené akce:

* V prostředí SSIS Integration runtime bylo naplánováno příliš mnoho spuštění balíčku. Všechna tato spuštění budou čekat ve frontě, aby ji vypnula.
  * Určete maximum pomocí tohoto vzorce:

    Maximální počet paralelních spuštění na hodnotu IR = počet uzlů * maximální paralelní spuštění na uzel
  * Informace o tom, jak nastavit počet uzlů a maximální paralelní spouštění na uzel, najdete [v tématu Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Prostředí SSIS Integration runtime je zastavené nebo má stav není v pořádku. Informace o tom, jak zjistit stav a chyby prostředí Integration runtime SSIS, najdete v tématu [prostředí Azure-SSIS Integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Na **kartě Obecné** také doporučujeme nastavit časový limit: ![ nastavte vlastnosti na kartě Obecné ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) .

### <a name="poor-performance-in-package-execution"></a>Nízký výkon při spuštění balíčku

Vyzkoušejte tyto akce:

* Ujistěte se, že SSIS Integration runtime je ve stejné oblasti jako zdroj dat a cíl.

* Nastavte úroveň protokolování provedení balíčku na **výkon** , aby se shromáždily informace o době trvání každé součásti v provádění. Podrobnosti najdete v tématu [protokolování integračních služeb (SSIS)](/sql/integration-services/performance/integration-services-ssis-logging).

* Ověřte výkon uzlu IR v Azure Portal:
  * Informace o tom, jak monitorovat prostředí SSIS Integration runtime, najdete v tématu [prostředí Azure-SSIS Integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Historii procesoru/paměti pro prostředí SSIS Integration runtime můžete najít zobrazením metriky datové továrny v Azure Portal.
    ![Monitorování metrik prostředí SSIS Integration runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)