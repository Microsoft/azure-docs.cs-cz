---
title: Poradce při potížích s prováděním balíčků v době spuštění integrace SSIS
description: Tento článek obsahuje pokyny pro řešení potíží pro spuštění balíčku SSIS v prostředí integrace SSIS
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
ms.openlocfilehash: 8c85a652cde840336c51e1a5b5459f9dc591e0be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414691"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Poradce při potížích s prováděním balíčků v době spuštění integrace SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek obsahuje nejběžnější chyby, které můžete najít při provádění balíčků SQL Server Integration Services (SSIS) v modulu runtime integrace SSIS. Popisuje možné příčiny a akce k vyřešení chyb.

## <a name="where-to-find-logs-for-troubleshooting"></a>Kde najít protokoly pro řešení potíží

Portál Azure Data Factory slouží ke kontrole výstupu aktivity provádění balíčků SSIS. Výstup zahrnuje výsledek spuštění, chybové zprávy a ID operace. Podrobnosti naleznete [v tématu Sledování kanálu](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Pomocí katalogu SSIS (SSISDB) zkontrolujte protokoly podrobností pro spuštění. Podrobnosti naleznete v [tématu Sledování spuštěné balíčky a další operace](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Běžné chyby, jejich příčiny a řešení

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Chybová zpráva" Časový limit připojení vypršel nebo služba zjistila chybu při zpracování vašeho požadavku. Zkuste to znovu.

Zde jsou možné příčiny a doporučené akce:
* Zdroj dat nebo cíl je přetížený. Zkontrolujte zatížení zdroje dat nebo cíle a zjistěte, zda má dostatečnou kapacitu. Například pokud jste použili Azure SQL Database, zvažte škálování, pokud databáze je pravděpodobné, že časový plán.
* Síť mezi runtime integrace SSIS a zdrojem dat nebo cíl je nestabilní, zejména v případě, že připojení je mezi oblastmi nebo mezi místní a Azure. Použijte vzor opakování v balíčku SSIS následujícím postupem:
  * Ujistěte se, že vaše balíčky SSIS lze znovu spustit při selhání bez vedlejších účinků (například ztráta dat nebo duplikace dat).
  * Konfigurovat **interval opakování** a **opakování** aktivity **balíčku SSIS** ![na kartě **Obecné.**](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Pro zdrojovou nebo cílovou součást ADO.NET a OLE DB nastavte **connectretrycount** a **ConnectRetryInterval** ve Správci připojení v balíčku SSIS nebo aktivitě SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Chybová zpráva: Zdroj rozhraní ADO NET nezískal připojení ..." s "Došlo k chybě související se sítí nebo instancí při navazování připojení k serveru SQL Server. Server se nenašel nebo nebyl dostupný.“

Tento problém obvykle znamená, že prostředí SSIS Integration Runtime nemá přístup ke zdroji nebo cíli dat. Důvodů může být několik. Vyzkoušejte tyto akce:
* Ujistěte se, že předáváte zdroj dat nebo název cíle/IP správně.
* Zkontrolujte, zda je brána firewall správně nastavena.
* Ujistěte se, že je vaše virtuální síť správně nakonfigurovaná, pokud je zdroj dat nebo cíl místní:
  * Můžete ověřit, zda problém pochází z konfigurace virtuální sítě zřízením virtuálního počítače Azure ve stejné virtuální síti. Pak zkontrolujte, jestli ke zdroji dat nebo cíli se dá přístup z virtuálního počítače Azure.
  * Další podrobnosti o používání virtuální sítě s runtime množením integrace SSIS najdete v [části Připojení za běhu integrace Azure-SSIS do virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Chybová zpráva: Zdroj rozhraní ADO NET nezískal připojení ..." s "Nelze vytvořit správce spravovaného připojení."

Potenciální příčinou je, že zprostředkovatel ADO.NET použitý v balíčku není nainstalován v prostředí integrace SSIS. Zprostředkovatele můžete nainstalovat pomocí vlastního nastavení. Další podrobnosti o vlastním nastavení najdete v [části Přizpůsobit nastavení pro prostředí integrace Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Chybová zpráva: "Připojení '...' nebyl nalezen"

Tuto chybu může způsobit známý problém ve starších verzích aplikace SQL Server Management Studio (SSMS). Pokud balíček obsahuje vlastní komponentu (například Azure Feature Pack pro SSIS nebo komponenty partnerů), která není nainstalovaná na počítači, kde se provádí nasazení pomocí SSMS, SSMS komponentu odebere a způsobí chybu. Upgradujte [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) na nejnovější verzi, ve které je tento problém opravený.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Chybová zpráva:"Ukončovací kód prováděcího modulu SSIS: -1073741819."

* Potenciální příčina a doporučená akce:
  * Tato chyba může být z důvodu omezení pro zdroj a cíl aplikace Excel, když více zdrojů aplikace Excel nebo cílů se provádí paralelně ve více vláknech. Toto omezení můžete vyřešit změnou komponent aplikace Excel, které se mají spouštět postupně, nebo je oddělit do různých balíčků a aktivovat pomocí příkazu Spustit úlohu balíčku s vlastností ExecuteOutOfProcess nastavenou jako True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Chybová zpráva: "Na disku není dostatek místa"

Tato chyba znamená, že místní disk je vychláštěn v runtime uzlu integrace SSIS. Zkontrolujte, zda váš balíček nebo vlastní nastavení spotřebovává velké množství místa na disku:
* Pokud je disk spotřebován balíčkem, bude uvolněn po dokončení spuštění balíčku.
* Pokud je disk spotřebován vlastním nastavením, budete muset zastavit modul runtime integrace SSIS, upravit skript a znovu spustit modul integrace. Celý kontejner objektů blob Azure, který jste zadali pro vlastní nastavení, se zkopíruje do runtime uzlu integrace SSIS, takže zkontrolujte, jestli je pod tímto kontejnerem nějaký nepotřebný obsah.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Chybová zpráva: "Načtení prostředku z hlavního serveru se nezdařilo. Microsoft.SqlServer.IntegrationServices.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Popis:Načtení souboru "***" se nezdařilo."

* Potenciální příčina a doporučená akce:
  * Pokud aktivita SSIS provádí balíček ze systému souborů (soubor balíčku nebo soubor projektu), dojde k této chybě, pokud projekt, balíček nebo konfigurační soubor není přístupný s pověřením pro přístup k balíčku, které jste zadali v aktivitě SSIS
    * Pokud používáte Soubor Azure:
      * Cesta k souboru \\ \\ \<by\>měla\\\<začínat názvem účtu úložiště .file.core.windows.net cesta ke sdílení souborů.\>
      * Doména by měla být "Azure"
      * Uživatelské jméno by \<mělo být název účtu úložiště.\>
      * Heslo by \<mělo být přístupový klíč úložiště\>
    * Pokud používáte místní soubor, zkontrolujte, jestli virtuální síť, přihlašovací údaje pro přístup k balíčkům a oprávnění jsou správně nakonfigurované tak, aby váš runtime integrace Azure-SSIS měl přístup k vaší místní sdílené složce.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Chybová zpráva: "Název souboru '...' uvedené v připojení nebyla platná"

* Potenciální příčina a doporučená akce:
  * Je zadán neplatný název souboru.
  * Ujistěte se, že používáte plně kvalifikovaný název domény (plně kvalifikovaný název domény) namísto krátkého času ve správci připojení

### <a name="error-message-cannot-open-file-"></a>Chybová zpráva: "Nelze otevřít soubor ...".

K této chybě dochází, když spuštění balíčku nemůže najít soubor na místním disku v modulu runtime integrace SSIS. Vyzkoušejte tyto akce:
* Nepoužívejte absolutní cestu v balíčku, který je spuštěn v modulu runtime integrace SSIS. Použijte aktuální pracovní adresář spuštění (.) nebo dočasnou složku (%TEMP%) Místo toho.
* Pokud potřebujete zachovat některé soubory v uzlech runtime integrace SSIS, připravte soubory, jak je popsáno v [části Vlastní nastavení](how-to-configure-azure-ssis-ir-custom-setup.md). Všechny soubory v pracovním adresáři budou po dokončení spuštění vyčištěny.
* Místo ukládání souboru do runtime uzlu integrace SSIS použijte soubory Azure. Podrobnosti najdete [v tématu Použití sdílených složek Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Chybová zpráva: Databáze SSISDB dosáhla své kvóty velikosti.

Možnou příčinou je, že databáze SSISDB vytvořená v databázi Azure SQL nebo ve spravované instanci, ve které vytváříte prostředí SSIS Integration Runtime, dosáhla své kvóty. Vyzkoušejte tyto akce:
* Zvažte navýšení počtu DTU vaší databáze. Podrobnosti najdete v tématu [Omezení prostředků SQL Database pro server služby Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Zkontrolujte, jestli váš balíček negeneruje velké množství protokolů. Pokud ano, můžete nakonfigurovat elastickou úlohu, která tyto protokoly vyčistí. Podrobnosti najdete v tématu [Vyčištění protokolů SSISDB s využitím úloh elastické databáze Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Chybová zpráva: "Limit požadavku pro databázi je ... a bylo dosaženo."

Pokud mnoho balíčků jsou spuštěny paralelně v prostředí integrace SSIS, k této chybě může dojít, protože SSISDB dosáhla svého limitu požadavku. Zvažte zvýšení DTC SSISDB k vyřešení tohoto problému. Podrobnosti najdete v tématu [Omezení prostředků SQL Database pro server služby Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Chybová zpráva: "Operace SSIS se nezdařila s neočekávaným stavem operace: ..."

Chyba je většinou způsobena přechodným problémem, proto zkuste znovu spustit spuštění balíčku. Použijte vzor opakování v balíčku SSIS následujícím postupem:

* Ujistěte se, že vaše balíčky SSIS lze znovu spustit při selhání bez vedlejších účinků (například ztráta dat nebo duplikace dat).
* Konfigurovat **interval opakování** a **opakování** aktivity **balíčku SSIS** ![na kartě **Obecné.**](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Pro zdrojovou nebo cílovou součást ADO.NET a OLE DB nastavte **connectretrycount** a **ConnectRetryInterval** ve Správci připojení v balíčku SSIS nebo aktivitě SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Chybová zpráva: "Neexistuje žádný aktivní pracovník."

Tato chyba obvykle znamená, že za běhu integrace SSIS má stav není v pořádku. Zkontrolujte, zda na portálu Azure stav a podrobné chyby. Další informace naleznete v [tématu Azure-SSIS integrace runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Chybová zpráva: "Váš integrační runtime nelze upgradovat a nakonec přestane fungovat, protože nemáme přístup ke kontejneru objektů Blob Azure, který jste poskytli pro vlastní nastavení."

K této chybě dochází, když prostředí integrace SSIS nemá přístup k úložišti nakonfigurovanému pro vlastní nastavení. Zkontrolujte, zda je zadaný identifikátor URI sdílený přístupový podpis (SAS) platný a jeho platnost nevypršela.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Chybová zpráva: Zprostředkovatel služby Microsoft OLE DB pro analysis services. 'Hresult: 0x80004005 Popis:' Chyba COM: Chyba COM: mscorlib; Výjimka byla vyvolána cílem vyvolání"

Jednou z možných příčin je, že uživatelské jméno nebo heslo s povoleným vícefaktorovým ověřováním Azure je nakonfigurované pro ověřování služby Azure Analysis Services. Toto ověřování není podporováno v modulu runtime integrace SSIS. Zkuste použít instanční objekt pro ověřování služby Azure Analysis Services:

1. Připravte instanční objekt, jak je popsáno v [automatizaci s instančními objekty](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. Ve Správci připojení nakonfigurujte **Použít konkrétní uživatelské jméno a heslo**: nastavte **AppID** jako uživatelské jméno a **clientSecret** jako heslo.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Chybová zpráva "Zdroj ADONET nezískal připojení {GUID} s následující chybovou zprávou: Při použití spravované identity se nezdařilo přihlášení pro uživatele NT AUTHORITY\ANONYMOUS LOGON.

Ujistěte se, že nenakonfigurujete metodu ověřování programu Connection Manager jako **ověřování hesla služby Active Directory,** pokud je parametr *ConnectUsingManagedIdentity* **true**. Místo toho jej můžete nakonfigurovat jako **ověřování SQL,** které je ignorováno, pokud je nastavena *funkce ConnectUsingManagedIdentity.*

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Chybová zpráva: "0xC020801F at ..., OData Source [...]: Nelze získat spravované připojení ze správce připojení za běhu"

Jednou z možných příčin je, že zabezpečení transportní vrstvy (TLS) není povolena v prostředí integrace SSIS, který je vyžadován zdrojem OData. TLS můžete povolit v prostředí integrace SSIS pomocí nastavení Přizpůsobit. Další podrobnosti najdete na webu [Nelze připojit data Project online z SSIS](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) a přizpůsobit nastavení pro [runtime integrace Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Chybová zpráva: "Požadavek na pracovní úlohu s operačním identifikátorem GUID ... chyba se nezdařila, protože chyba: Odeslání pracovní operace s chybovou zprávou: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Načtení datového proxy serveru se nezdařilo."

Ujistěte se, že váš runtime integrace Azure-SSIS je nakonfigurovaný s runtime integrace s vlastním hostitelem. Další podrobnosti najdete na [adrese Konfigurace samoobslužné infračerveného serveru jako proxy serveru pro službu Azure-SSIS IR v adf](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Chybová zpráva: Stav pracovní úlohy: Nezdařilo se. Chyba pracovní úlohy: ErrorCode: 2010, ErrorMessage: The Self-hosted Integration Runtime ... je offline"

Zkontrolujte, zda je váš runtime integrace s vlastním hostitelem nainstalován a spuštěn. Další podrobnosti naleznete na adrese [Vytvoření a konfigurace prostředí runtime integrace](create-self-hosted-integration-runtime.md) s vlastním hostitelem

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Chybová zpráva: "Chyba pracovní úlohy: ErrorCode: 2906, ErrorMessage: Spuštění balíčku se nezdařilo., Výstup: {"OperationErrorMessages": "Chyba: Požadovaný zprostředkovatel TECHNOLOGIE OLE DB ... není registrována. Pokud 64bitový ovladač není nainstalován, spusťte balíček v 32bitovém režimu..."

Ujistěte se, že odpovídající zprostředkovatel používaný konektory OLE DB v balíčku jsou nainstalovány na počítači runtime integrace s vlastním hostitelem správně. Další podrobnosti naleznete na [adrese Configure Self-Hosted IR jako proxy pro Azure-SSIS IR v ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Chybová zpráva: "Pracovní chyba úlohy: ErrorCode: 2906, ErrorMessage: Spuštění balíčku se nezdařilo., Výstup: {"OperationErrorMessages": "Chyba: System.IO.FileLoadException: Nelze načíst soubor nebo sestavení 'Microsoft.WindowsAzure.Storage, Version=..., Culture=neutral, PublicKeyToken=31bf3856ad364e35' nebo jednu z jeho závislostí. Definice manifestu lokalisně sestavení neodpovídá odkazu na sestavení." ..."

Jednou z možných příčin je, že váš vlastní hostovaný integrační runtime není správně nainstalován nebo upgradován. Navrhněte stažení a přeinstalaci nejnovějšího runtime integrace s vlastním hostitelem. Další podrobnosti naleznete na adrese [Vytvoření a konfigurace prostředí runtime integrace](create-self-hosted-integration-runtime.md#installation-best-practices) s vlastním hostitelem

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Chybová zpráva: "Při vyžádání metadat je vyžadováno připojení. Pokud pracujete offline, povolte připojení zaškrtnutím políčka Pracovat offline v nabídce SSIS"

* Potenciální příčina a doporučená akce:
  * Pokud je také varovná zpráva "Komponenta nepodporuje použití správce připojení s ConnectByProxy nastavení hodnoty true" v protokolu spuštění, to znamená, že správce připojení se používá na součást, která ještě nepodporuje "ConnectByProxy" dosud. Podporované součásti najdete na [adrese Configure Self-Hosted IR jako proxy server pro Azure-SSIS IR v ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Protokol spuštění lze nalézt v [sestavě SSMS](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) nebo ve složce protokolu, kterou jste zadali v aktivitě spuštění balíčku SSIS.
  * virtuální síť lze také použít pro přístup k místním datům jako alternativu. Další podrobnosti najdete na webu [Připojení k integračnímu runtime Azure-SSIS do virtuální sítě.](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Chybová zpráva: Stav pracovní úlohy: Nezdařilo se. Chyba pracovní úlohy: ErrorCode: 2906, ErrorMessage: Spuštění balíčku se nezdařilo., Výstup: {"OperationErrorMessages": "Kód ukončení prováděcího příkazu SSIS: -1.\n", "LogLocation": "... \\SSISTelemetry\\ExecutionLog\\...", "effectiveIntegrationRuntime": "...", "executionDuration": ..., "durationInQueue": { "integrationRuntimeQueue": ... }}"

Ujistěte se, že visual c++ runtime je nainstalován na self-hosted integrace runtime machine. Další podrobnosti naleznete na [adrese Configure Self-Hosted IR jako proxy pro Azure-SSIS IR v ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Neočekávaně se spustí více spuštění balíčků.

* Potenciální příčina a doporučená akce:
  * ADF uložené aktivity procedury nebo vyhledávací aktivity se používají k aktivaci spuštění balíčku SSIS. Příkaz t-sql může zasáhnout přechodný problém a spustit opakované spuštění, které by způsobilo více spuštění balíčku.
  * Místo toho použijte aktivitu ExecuteSSPACKAGE, která zajistí, že se spuštění balíčku znovu nespustí, pokud uživatel nenastaví počet opakování v aktivitě. Detail lze nalézt na adrese[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * Upřesněte příkaz t-sql tak, aby bylo možné znovu spustit kontrolou, zda již bylo spuštěno spuštění

### <a name="package-execution-takes-too-long"></a>Spuštění balíčku trvá příliš dlouho

Zde jsou možné příčiny a doporučené akce:

* V době spuštění integrace SSIS bylo naplánováno příliš mnoho spuštění balíčků. Všechny tyto popravy budou čekat ve frontě na jejich pořadí.
  * Maximální určení pomocí tohoto vzorce:

    Maximální počet paralelního spuštění na infračervený přenos = počet uzlů * Maximální paralelní spuštění na uzel
  * Informace o tom, jak nastavit počet uzlů a maximální paralelní spuštění na uzel, najdete v [tématu vytvoření runtime integrace Azure-SSIS v Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Za běhu integrace SSIS je zastavena nebo má stav není v pořádku. Informace o tom, jak zkontrolovat stav běhu integrace SSIS a chyby, naleznete v [tématu Azure-SSIS integrace runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Doporučujeme také nastavit časový čas na kartě **Obecné:** ![Nastavení](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)vlastností na kartě Obecné .

### <a name="poor-performance-in-package-execution"></a>Nízký výkon při spouštění balíčků

Vyzkoušejte tyto akce:

* Ujistěte se, že za běhu integrace SSIS je ve stejné oblasti jako zdroj dat a cíl.

* Nastavte úroveň protokolování spuštění balíčku na **výkon** shromažďovat informace o době trvání pro každou součást v provádění. Podrobnosti naleznete [v tématu Protokolování integration services (SSIS).](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

* Zkontrolujte výkon infračerveného uzlu na webu Azure Portal:
  * Informace o tom, jak sledovat za běhu integrace SSIS, naleznete v [tématu Azure-SSIS integrace runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Historii procesoru a paměti pro běh integrace SSIS můžete zobrazit metriky datové továrny na webu Azure Portal.
    ![Sledování metrik za běhu integrace SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
