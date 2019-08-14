---
title: Řešení potíží se spuštěním balíčku v modulu runtime integrace SSIS | Microsoft Docs
description: Tento článek popisuje pokyny k odstraňování problémů s SSIS balíčkem v prostředí SSIS Integration runtime.
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
ms.openlocfilehash: a7ad0f3be754029c654b04d19750aab7bbcd210d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933638"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Řešení potíží se spuštěním balíčku v prostředí SSIS Integration runtime

Tento článek obsahuje nejběžnější chyby, se kterými se můžete setkat při provádění balíčků služba SSIS (SQL Server Integration Services) (SSIS) v prostředí SSIS Integration runtime. Popisuje možné příčiny a akce pro řešení chyb.

## <a name="where-to-find-logs-for-troubleshooting"></a>Kde najít protokoly pro řešení potíží

Pomocí portálu Azure Data Factory můžete kontrolovat výstup aktivity provádění balíčku SSIS. Výstup obsahuje výsledky spuštění, chybové zprávy a ID operace. Podrobnosti najdete v tématu [monitorování kanálu](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Pomocí katalogu SSIS (SSISDB) si prohlédněte protokoly podrobností pro spuštění. Podrobnosti najdete v tématu [monitorování spuštěných balíčků a dalších operací](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Běžné chyby, příčiny a řešení

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Chybová zpráva: Časový limit připojení vypršel nebo služba zjistila chybu při zpracování vaší žádosti. Zkuste to prosím znovu. "

Tady jsou možné příčiny a doporučené akce:
* Zdroj dat nebo cíl je přetížený. Zkontrolujte zatížení zdroje dat nebo cíle a zjistěte, zda má dostatečnou kapacitu. Pokud jste například použili Azure SQL Database, zvažte horizontální navýšení kapacity databáze, pokud je pravděpodobně vyprší časový limit databáze.
* Síť mezi integrací prostředí SSIS Integration runtime a zdrojem nebo cílem dat je nestabilní, zejména v případě, že je spojení mezi oblastmi nebo mezi místními a Azure. Použijte vzor opakování v balíčku SSIS pomocí následujících kroků:
  * Ujistěte se, že vaše balíčky SSIS můžou znovu spustit při selhání bez vedlejších účinků (například ztráty dat nebo duplikace dat).
  * Na kartě **Obecné** nakonfigurujte **akci opakovat** a **interval opakování** akce **SSIS balíčku pro spuštění** . ![Nastavení vlastností na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * U ADO.NET OLE DB a zdrojové nebo cílové součásti nastavte **atributu ConnectRetryCount** a **atributu ConnectRetryInterval** ve Správci připojení v části balíček SSIS nebo aktivita SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Chybová zpráva: Rozhraní ADO NET Source nedokázalo získat připojení... Při navazování připojení k SQL Server došlo k chybě související se sítí nebo instancí. Server nebyl nalezen nebo nebyl přístupný. "

Tento problém obvykle znamená, že prostředí SSIS Integration Runtime nemá přístup ke zdroji nebo cíli dat. Důvodů může být několik. Vyzkoušejte tyto akce:
* Ujistěte se, že jste správně předali zdroj dat nebo cílový název nebo IP adresu.
* Ujistěte se, že je brána firewall správně nastavená.
* Ujistěte se, že je vaše virtuální síť správně nakonfigurovaná, pokud je zdroj dat nebo cíl místní:
  * Vytvořením virtuálního počítače Azure ve stejné virtuální síti můžete ověřit, jestli se jedná o problém z konfigurace virtuální sítě. Pak zkontrolujte, jestli je k dispozici zdroj dat nebo cíl z virtuálního počítače Azure.
  * Další podrobnosti o používání virtuální sítě s SSIS Integration runtime najdete v prostředí [Azure-SSIS Integration runtime do virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Chybová zpráva: Rozhraní ADO NET Source nedokázalo získat připojení... pomocí příkazu se nepodařilo vytvořit spravovaného Správce připojení.

Potenciální příčinou je to, že zprostředkovatel ADO.NET použitý v balíčku není nainstalovaný v prostředí SSIS Integration runtime. Zprostředkovatele můžete nainstalovat pomocí vlastní instalace. Další podrobnosti o vlastní instalaci najdete v [části Přizpůsobení nastavení pro prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Chybová zpráva: "Připojení"... " nebyl nalezen "

Tuto chybu může způsobit známý problém ve starších verzích aplikace SQL Server Management Studio (SSMS). Pokud balíček obsahuje vlastní komponentu (například Azure Feature Pack pro SSIS nebo komponenty partnerů), která není nainstalovaná na počítači, kde se provádí nasazení pomocí SSMS, SSMS komponentu odebere a způsobí chybu. Upgradujte [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) na nejnovější verzi s opraveným problémem.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Chybová zpráva: "ukončovací kód prováděcího modulu SSIS:-1073741819."

* Potenciální příčina & doporučené akce:
  * Tato chyba může být způsobena omezením pro zdroj a cíl aplikace Excel, pokud je více zdrojů nebo cíle aplikace Excel prováděno paralelně ve více vláknech. Toto omezení můžete vyřešit tak, že změníte komponenty aplikace Excel tak, aby se spouštěly v pořadí, nebo je rozbalíte do různých balíčků a aktivujete je pomocí možnosti Spustit úlohu balíčku s vlastností ExecuteOutOfProcess nastavenou na hodnotu true.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Chybová zpráva: "Na disku není dostatek místa"

Tato chyba znamená, že se místní disk používá v uzlu SSIS Integration runtime. Ověřte, jestli váš balíček nebo vlastní instalační program spotřebovává spoustu místa na disku:
* Pokud je disk spotřebován balíčkem, bude po dokončení spuštění balíčku uvolněn.
* Pokud je disk spotřebován vlastním nastavením, budete muset zastavit SSIS Integration runtime, upravit skript a znovu spustit prostředí Integration runtime. Celý kontejner Azure Blob, který jste zadali pro vlastní instalaci, se zkopíruje do uzlu SSIS Integration runtime, takže ověřte, jestli je v tomto kontejneru nějaký nepotřebný obsah.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Chybová zpráva: "Nepovedlo se načíst prostředek z hlavní větve. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Kód: 300004. Popis: načtení souboru "* * *" se nezdařilo. "

* Potenciální příčina & doporučené akce:
  * Pokud aktivita SSIS spouští balíček ze systému souborů (soubor balíčku nebo soubor projektu), k této chybě dojde, pokud projekt, balíček nebo konfigurační soubor není přístupný s přihlašovacími údaji pro přístup k balíčku, které jste zadali v aktivitě SSIS
    * Pokud používáte soubor Azure:
      * Cesta k souboru by \\měla začínat \\ \<názvem\>účtu úložiště\\. cesta ke sdílené složce File.Core.Windows.NET\<\>
      * Doména by měla být "Azure".
      * Uživatelské jméno by mělo \<být název účtu úložiště.\>
      * Heslo by mělo být \<přístupového klíče k úložišti.\>
    * Pokud používáte místní soubor, zkontrolujte prosím, jestli je virtuální síť, přihlašovací údaje pro přístup k balíčku a oprávnění správně nakonfigurované, aby váš prostředí Azure-SSIS Integration runtime mělo přístup k místní sdílené složce.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Chybová zpráva: Název souboru... zadaný v připojení není platný.

* Potenciální příčina & doporučené akce:
  * Je zadaný neplatný název souboru.
  * Ujistěte se, že používáte plně kvalifikovaný název domény (plně kvalifikovaný název domény) místo krátkého času ve Správci připojení.

### <a name="error-message-cannot-open-file-"></a>Chybová zpráva: Nejde otevřít soubor...

K této chybě dojde, když spuštění balíčku nemůže najít soubor na místním disku v prostředí SSIS Integration runtime. Vyzkoušejte tyto akce:
* Nepoužívejte absolutní cestu v balíčku, který je spuštěn v prostředí SSIS Integration runtime. Použijte aktuální pracovní adresář spuštění (.) nebo složku Temp (% TEMP%). takové.
* Pokud potřebujete zachovat některé soubory v uzlech SSIS Integration runtime, připravte soubory, jak je popsáno v tématu [přizpůsobení instalace](how-to-configure-azure-ssis-ir-custom-setup.md). Po dokončení spuštění budou všechny soubory v pracovním adresáři vyčištěny.
* Místo uložení souboru v uzlu Integration runtime SSIS použijte soubory Azure. Podrobnosti najdete v tématu [použití sdílených složek Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Chybová zpráva: "Databáze" SSISDB "dosáhla kvóty velikosti"

Možnou příčinou je, že databáze SSISDB vytvořená v databázi Azure SQL nebo ve spravované instanci, ve které vytváříte prostředí SSIS Integration Runtime, dosáhla své kvóty. Vyzkoušejte tyto akce:
* Zvažte navýšení počtu DTU vaší databáze. Podrobnosti najdete v tématu [Omezení prostředků SQL Database pro server služby Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Zkontrolujte, jestli váš balíček negeneruje velké množství protokolů. Pokud ano, můžete nakonfigurovat elastickou úlohu, která tyto protokoly vyčistí. Podrobnosti najdete v tématu [Vyčištění protokolů SSISDB s využitím úloh elastické databáze Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Chybová zpráva: "Omezení požadavků pro databázi je... a byl dosažen. "

Pokud mnoho balíčků běží paralelně v prostředí SSIS Integration runtime, může dojít k této chybě, protože SSISDB dosáhla svého limitu žádosti. Pokud chcete tento problém vyřešit, zvažte zvýšení SSISDB DTC. Podrobnosti najdete v tématu [Omezení prostředků SQL Database pro server služby Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Chybová zpráva: Operace SSIS se nezdařila, došlo k neočekávanému stavu operace:...

Tato chyba je většinou způsobena přechodným problémem, proto zkuste znovu spustit spuštění balíčku. Použijte vzor opakování v balíčku SSIS pomocí následujících kroků:

* Ujistěte se, že vaše balíčky SSIS můžou znovu spustit při selhání bez vedlejších účinků (například ztráty dat nebo duplikace dat).
* Na kartě **Obecné** nakonfigurujte **akci opakovat** a **interval opakování** akce **SSIS balíčku pro spuštění** . ![Nastavení vlastností na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* U ADO.NET OLE DB a zdrojové nebo cílové součásti nastavte **atributu ConnectRetryCount** a **atributu ConnectRetryInterval** ve Správci připojení v části balíček SSIS nebo aktivita SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Chybová zpráva: "Není k dispozici žádný aktivní pracovní proces."

Tato chyba obvykle znamená, že modul runtime integrace SSIS nemá stav v pořádku. Stav a podrobné chyby najdete v Azure Portal. Další informace najdete v tématu [prostředí Azure-SSIS Integration runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Chybová zpráva: "Modul runtime integrace se nedá upgradovat a nakonec přestane fungovat, protože nemůžeme získat přístup k kontejneru objektů blob Azure, který jste zadali pro vlastní instalaci."

K této chybě dojde, když prostředí Integration runtime SSIS nemá přístup k úložišti nakonfigurovanému pro vlastní instalaci. Ověřte, jestli zadaný identifikátor URI sdíleného přístupového podpisu (SAS) je platný a že nevypršela jeho platnost.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Chybová zpráva: "Poskytovatel Microsoft OLE DB pro Analysis Services. HRESULT Popis 0x80004005: ' Chyba COM: Chyba COM: mscorlib; Cíl vyvolání způsobil výjimku.

Jednou z možných příčin je, že uživatelské jméno nebo heslo s povoleným ověřováním Azure Multi-Factor Authentication je nakonfigurované pro Azure Analysis Services ověřování. Toto ověřování není podporováno v prostředí SSIS Integration runtime. Zkuste použít instanční objekt pro Azure Analysis Services ověřování:
1. Připravte instanční objekt, jak je popsáno v tématu [automatizace s instančními objekty](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. V programu Správce připojení nakonfigurujte **použití konkrétního uživatelského jména a hesla**: jako uživatelské jméno nastavte **AppID** a jako heslo **clientSecret** .

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Chybová zpráva: "ADONET zdroji se nepovedlo získat připojení {GUID} pomocí této chybové zprávy: Přihlášení uživatele NT AUTHORITY\ANONYMOUS LOGON při použití spravované identity se nezdařilo.

Pokud má parametr *ConnectUsingManagedIdentity* **hodnotu true**, ujistěte se, že jste nenakonfigurovali metodu ověřování Správce připojení jako **ověřování hesla služby Active Directory** . Místo toho ho můžete nakonfigurovat jako **ověřování SQL** , které se ignoruje, pokud je nastavená možnost *ConnectUsingManagedIdentity* .

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Neočekávaně se spouští více spuštění balíčku.

* Potenciální příčina & doporučené akce:
  * Aktivita uložená procedura ADF se používá ke spuštění SSIS balíčku. Příkaz t-SQL může způsobit přechodný problém a aktivovat opětovné spuštění, které by způsobilo více spuštění balíčku.
  * Místo toho použijte aktivitu ExecuteSSISPackage, která zajistí, že spuštění balíčku nebude znovu spuštěno, pokud uživatel nenastaví počet opakování v aktivitě. Podrobnosti najdete na adrese.[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

### <a name="package-execution-takes-too-long"></a>Provádění balíčku trvá moc dlouho.

Tady jsou možné příčiny a doporučené akce:

* V prostředí SSIS Integration runtime bylo naplánováno příliš mnoho spuštění balíčku. Všechna tato spuštění budou čekat ve frontě, aby ji vypnula.
  * Určete maximum pomocí tohoto vzorce:

    Maximální počet paralelních spuštění na hodnotu IR = počet uzlů * maximální paralelní spuštění na uzel
  * Informace o tom, jak nastavit počet uzlů a maximální paralelní spouštění na uzel, najdete [v tématu Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Prostředí SSIS Integration runtime je zastavené nebo má stav není v pořádku. Informace o tom, jak zjistit stav a chyby prostředí Integration runtime SSIS, najdete v tématu [prostředí Azure-SSIS Integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Na kartě **Obecné** také doporučujeme nastavit časový limit: ![Nastavte vlastnosti na kartě](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)Obecné.

### <a name="poor-performance-in-package-execution"></a>Nízký výkon při spuštění balíčku

Vyzkoušejte tyto akce:

* Ujistěte se, že SSIS Integration runtime je ve stejné oblasti jako zdroj dat a cíl.

* Nastavte úroveň protokolování provedení balíčku na **výkon** , aby se shromáždily informace o době trvání každé součásti v provádění. Podrobnosti najdete v tématu [protokolování integračních služeb (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Ověřte výkon uzlu IR v Azure Portal:
  * Informace o tom, jak monitorovat prostředí SSIS Integration runtime, najdete v tématu [prostředí Azure-SSIS Integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Historii procesoru/paměti pro prostředí SSIS Integration runtime můžete najít zobrazením metriky datové továrny v Azure Portal.
    ![Monitorování metrik prostředí SSIS Integration runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
