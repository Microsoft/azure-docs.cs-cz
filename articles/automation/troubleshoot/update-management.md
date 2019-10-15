---
title: Řešení chyb pomocí Update Management
description: Naučte se řešit potíže s Update Management
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 89c5c849ffdbe70ae449f41ac08471c84f2fb5bc
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372632"
---
# <a name="troubleshooting-issues-with-update-management"></a>Řešení potíží s Update Management

Tento článek popisuje řešení problémů, ke kterým může při použití Update Management běžet.

Došlo k potížím s agentem pro Hybrid Worker agenta, aby bylo možné zjistit příslušný problém. Další informace o poradci při potížích najdete v tématu [Poradce při potížích s aktualizací agenta](update-agent-issues.md). Všechny ostatní problémy najdete v podrobných informacích o možných problémech.

Pokud narazíte na problémy při pokusu o připojení řešení na virtuálním počítači, podívejte se do protokolu událostí **Operations Manager** v části **protokoly aplikací a služeb** v místním počítači pro události s ID události **4502** a zprávou události. obsahuje **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

V následující části se zvýrazňují konkrétní chybové zprávy a možné řešení pro každou z nich. Další problémy s připojováním najdete v tématu [řešení potíží s registrací řešení](onboarding.md).

## <a name="nologs"></a>Scénář: počítače se nezobrazují na portálu v části Update Management

### <a name="issue"></a>Problém

Můžete spouštět v následujících scénářích:

* Váš počítač zobrazuje **nenakonfigurovaný** Update Management zobrazení virtuálního počítače.

* V zobrazení Update Management účtu Automation chybí vaše počítače.

* Máte počítače, které se zobrazují jako **nehodnocené** v **souladu s dodržováním předpisů**, ale data prezenčního signálu se zobrazí v protokolu Azure monitor pro Hybrid Runbook Worker, ale ne Update Management.

### <a name="cause"></a>Příčina

To může být způsobeno potenciálními problémy s místní konfigurací nebo nesprávně nakonfigurovanou konfigurací oboru.

Je možné, že bude nutné znovu zaregistrovat a znovu nainstalovat Hybrid Runbook Worker.

Možná jste v pracovním prostoru nadefinovali kvótu, která byla dostupná a zastavují se data, která se mají ukládat.

### <a name="resolution"></a>Rozlišení

* V závislosti na operačním systému spusťte Poradce při potížích pro [Windows](update-agent-issues.md#troubleshoot-offline) nebo [Linux](update-agent-issues-linux.md#troubleshoot-offline) .

* Ujistěte se, že váš počítač hlásí správný pracovní prostor. Ověřte, do jakého pracovního prostoru se oznamuje váš počítač. Pokyny k tomu, jak to ověřit, najdete v tématu [ověření připojení agenta k Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Pak ověřte, že se jedná o pracovní prostor propojený s vaším účtem Azure Automation. Potvrďte to tak, že přejdete do svého účtu Automation a kliknete na **propojený pracovní prostor** v části **související prostředky**.

* Zkontrolujte, jestli se počítače zobrazovaly v pracovním prostoru Log Analytics. Spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation. Pokud počítač ve výsledcích dotazu nevidíte, váš počítač se v poslední době nevrátil, což znamená, že došlo k nejpravděpodobnějšímu problému s místní konfigurací a můžete [agenta znovu nainstalovat](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Pokud se Váš počítač zobrazí ve výsledcích dotazu, je potřeba, abyste nastavili konfiguraci oboru určenou v následující odrážce.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Vyhledejte problémy s konfigurací oboru. [Konfigurace oboru](../automation-onboard-solutions-from-automation-account.md#scope-configuration) určuje, které počítače se nakonfigurují pro řešení. Pokud se Váš počítač zobrazuje v pracovním prostoru, ale nezobrazuje se na portálu **Update Management** , budete muset nakonfigurovat konfiguraci oboru pro cílení na počítače. Další informace o tom, jak to udělat, najdete [v tématu zprovoznění počítačů v pracovním prostoru](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* V pracovním prostoru spusťte následující dotaz. Pokud vidíte výsledek `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, máte ve svém pracovním prostoru definovanou kvótu, která byla dosažená a zastavila ukládání dat. V pracovním prostoru přejděte na **využití a odhadované náklady** > **Správa objemu dat** a ověřte kvótu nebo odeberte kvótu, kterou máte.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

* Pokud výše uvedené kroky nevyřešily váš problém, postupujte podle kroků v části [nasazení windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) k přeinstalování Hybrid Worker pro Windows nebo [nasazení Hybrid Runbook Worker Linux](../automation-linux-hrw-install.md) pro Linux.

## <a name="rp-register"></a>Scénář: nejde zaregistrovat poskytovatele prostředků služby Automation pro předplatná.

### <a name="issue"></a>Problém

Při práci s řešeními ve vašem účtu Automation se může zobrazit následující chyba.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Příčina

Zprostředkovatel prostředků Automation není v předplatném zaregistrován.

### <a name="resolution"></a>Rozlišení

Zprostředkovatele prostředků automatizace můžete zaregistrovat provedením následujících kroků v Azure Portal:

1. Klikněte na **všechny služby** v dolním seznamu služeb Azure a pak vyberte **předplatná** ve skupině _Obecné_ služby.
2. Vyberte své předplatné.
3. V části _Nastavení_klikněte na **poskytovatelé prostředků** .
4. V seznamu poskytovatelů prostředků ověřte, jestli je zaregistrovaný poskytovatel prostředků **Microsoft. Automation** .
5. Pokud poskytovatel není uveden, zaregistrujte poskytovatele **Microsoft. Automation** pomocí kroků uvedených v části [ ](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="components-enabled-not-working"></a>Scénář: součásti pro řešení Update Management jsou povolené a teď se konfiguruje tento virtuální počítač.

### <a name="issue"></a>Problém

Na virtuálním počítači se po 15 minutách po registraci stále zobrazuje následující zpráva:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Příčina

Tato chyba může být způsobena následujícími důvody:

1. Komunikace zpět s účtem Automation je zablokovaná.
2. Virtuální počítač, který se připojuje, může pocházet z klonovaného počítače, který se nástroje Sysprep s nainstalovanou Microsoft Monitoring Agent.

### <a name="resolution"></a>Rozlišení

1. Navštivte téma [Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning) , kde se dozvíte, které adresy a porty je potřeba povolit, aby Update Management fungovalo.
2. Pokud používáte Klonovaný obrázek:
   1. V pracovním prostoru Log Analytics odeberte virtuální počítač z uloženého hledání pro konfiguraci oboru `MicrosoftDefaultScopeConfig-Updates`, pokud je zobrazený. Uložená hledání najdete v části **Obecné** v pracovním prostoru.
   2. Spusťte `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`.
   3. Spusťte `Restart-Service HealthService` pro restartování `HealthService`. Tím se znovu vytvoří klíč a vygeneruje se nový UUID.
   4. Pokud to nepomůže, nástroj Sysprep nejprve napřed a po faktu nainstalujte agenta MMA.

## <a name="multi-tenant"></a>Scénář: při vytváření nasazení aktualizace pro počítače v jiném tenantovi Azure se zobrazí chyba propojeného odběru.

### <a name="issue"></a>Problém

Při pokusu o vytvoření nasazení aktualizace pro počítače v jiném tenantovi Azure se zobrazí následující chyba:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Příčina

K této chybě dojde, když vytvoříte nasazení aktualizace, které má virtuální počítače Azure v jiném tenantovi, které jsou součástí nasazení aktualizace.

### <a name="resolution"></a>Rozlišení

K jejich naplánování budete muset použít následující alternativní řešení. Pomocí rutiny [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) s přepínačem `-ForUpdate` můžete vytvořit plán a použít rutinu [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) a předat počítače do jiného tenanta do parametru `-NonAzureComputer`. Následující příklad ukazuje příklad toho, jak to provést:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Scénář: Neobjasněná restartování

### <a name="issue"></a>Problém

Nakonfigurovali jste **řízení restartování** bez **restartování**, ale počítače se pořád po nainstalovaných aktualizacích restartují.

### <a name="cause"></a>Příčina

Chování web Windows Update lze upravit pomocí několika klíčů registru, které mohou změnit chování při restartování.

### <a name="resolution"></a>Rozlišení

Zkontrolujte klíče registru uvedené v části [Konfigurace automatických aktualizací úpravou registru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) a [klíčů registru, které slouží ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , abyste měli jistotu, že vaše počítače jsou správně nakonfigurované.

## <a name="failed-to-start"></a>Scénář: na počítači se nepovedlo spustit v nasazení aktualizace.

### <a name="issue"></a>Problém

Počítač se **nepovedlo spustit** kvůli stavu počítače. Při zobrazení konkrétních podrobností pro počítač se zobrazí následující chyba:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z některého z následujících důvodů:

* Počítač už neexistuje.
* Počítač je vypnutý a nedosažitelný.
* Počítač má problém s připojením k síti a hybridní pracovní proces v počítači je nedosažitelný.
* Byla zjištěna aktualizace Microsoft Monitoring Agent, která změnila SourceComputerId
* Pokud jste v účtu Automation dosáhli limitu počtu souběžných úloh 2 000, vaše spuštění aktualizace může být omezené. Každé nasazení se považuje za úlohu a každý počítač v nasazení aktualizace se počítá jako úloha. Jakékoli jiné úlohy služby Automation nebo nasazení aktualizací, které aktuálně běží ve vašem účtu Automation, se počítá s limitem souběžných úloh.

### <a name="resolution"></a>Rozlišení

V případě potřeby použijte pro nasazení aktualizací [dynamické skupiny](../automation-update-management-groups.md) .

* Ověřte, že počítač stále existuje a je dostupný. Pokud neexistuje, upravte nasazení a odeberte počítač.
* Seznam portů a adres, které jsou potřeba pro Update Management a ověření, že váš počítač splňuje tyto požadavky, najdete v části věnované [Plánování sítě](../automation-update-management.md#ports) .
* Spusťte následující dotaz v Log Analytics k vyhledání počítačů ve vašem prostředí, jejichž změna `SourceComputerId` se změnila. Vyhledá počítače, které mají stejnou hodnotu `Computer`, ale odlišnou hodnotu `SourceComputerId`. Po nalezení ovlivněných počítačů je nutné upravit nasazení aktualizace, která cílí na tyto počítače, a odebrat a znovu přidat počítače, aby `SourceComputerId` odráželo správnou hodnotu.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

## <a name="updates-nodeployment"></a>Scénář: aktualizace se instalují bez nasazení.

### <a name="issue"></a>Problém

Při registraci počítače se systémem Windows v nástroji Update Management můžete zobrazit aktualizace instalovat bez nasazení.

### <a name="cause"></a>Příčina

Ve Windows se aktualizace nainstalují automaticky, jakmile budou k dispozici. To může způsobit nejasnost, pokud jste neplánovali nasazení aktualizace na počítač.

### <a name="resolution"></a>Rozlišení

Klíč registru systému Windows, `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`, je výchozí hodnota "4" – **Automatické stažení a instalaci**.

U Update Management klientů doporučujeme nastavit tento klíč na "3" – **automaticky stahovat, ale neinstalovat automaticky**.

Další informace najdete v tématu [Konfigurace automatických aktualizací](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Scénář: počítač je už zaregistrovaný na jiný účet.

### <a name="issue"></a>Problém

Zobrazí se následující chybová zpráva:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Příčina

Počítač je již připojen k jinému pracovnímu prostoru pro Update Management.

### <a name="resolution"></a>Rozlišení

1. Postupujte podle kroků v části počítače, které se [nezobrazují na portálu v části Update Management](#nologs) , abyste zajistili, že se počítač hlásí do správného pracovního prostoru.
2. Proveďte vyčištění starých artefaktů na počítači [odstraněním skupiny Hybrid Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) a zkuste to znovu.

## <a name="machine-unable-to-communicate"></a>Scénář: počítač nemůže komunikovat se službou.

### <a name="issue"></a>Problém

Zobrazí se některá z následujících chybových zpráv:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

### <a name="cause"></a>Příčina

Může se jednat o server proxy, bránu nebo bránu firewall blokující síťovou komunikaci.

### <a name="resolution"></a>Rozlišení

Zkontrolujte své sítě a ujistěte se, že jsou povolené příslušné porty a adresy. Seznam portů a adres, které jsou vyžadovány Update Management a hybridními pracovními procesy Runbooku, najdete v tématu [požadavky na síť](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="unable-to-create-selfsigned-cert"></a>Scénář: Nejde vytvořit certifikát podepsaný svým držitelem.

### <a name="issue"></a>Problém

Zobrazí se některá z následujících chybových zpráv:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Příčina

Hybrid Runbook Worker se nepodařilo vygenerovat certifikát podepsaný svým držitelem.

### <a name="resolution"></a>Rozlišení

Ověřte, že účet System má oprávnění ke čtení pro složku **C:\ProgramData\Microsoft\Crypto\RSA** , a zkuste to znovu.

## <a name="mw-exceeded"></a>Scénář: naplánovaná Správa aktualizací selhala s chybou MaintenanceWindowExceeded.

### <a name="issue"></a>Problém

Výchozí časové období údržby pro aktualizace je 120 minut. Časový interval pro správu a údržbu můžete zvýšit na maximálně 6 (6) hodin nebo 360 minut.

### <a name="resolution"></a>Rozlišení

Upravte všechna neúspěšná naplánovaná nasazení aktualizací a zvyšte časový interval pro správu a údržbu.

Další informace o časových obdobích údržby najdete v tématu [install Updates](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Scénář: počítač se zobrazuje jako nehodnocený a zobrazuje výjimku HResult.

### <a name="issue"></a>Problém

* Máte počítače, které se zobrazují jako **nehodnocené** v **souladu s dodržováním předpisů**, a pod ní se zobrazí zpráva s výjimkou.
* Máte počítače, které se ukážou jako nehodnocené
* Na portálu se zobrazí kód chyby HRESULT.

### <a name="cause"></a>Příčina

Agent aktualizace (agent web Windows Update v systému Windows, správce balíčků pro distribuci pro Linux) není správně nakonfigurovaný. Update Management spoléhá na agenta aktualizace počítače, aby poskytoval aktualizace, které jsou potřeba, stav opravy a výsledky nasazených oprav. Bez těchto informací Update Management nemusí správně hlásit informace o opravách, které jsou potřeba nebo nainstalované.

### <a name="resolution"></a>Rozlišení

Probíhá pokus o provedení aktualizací místně na počítači. Pokud se to nepovede, obvykle to znamená chybu konfigurace agenta aktualizace.

Obvyklé příčiny selhání:

* Konfigurace sítě a brány firewall.
* V případě systému Linux zkontrolujte příslušnou dokumentaci a ujistěte se, že máte přístup ke koncovému bodu sítě úložiště balíčků.
* V případě systému Windows ověřte konfiguraci agenta, jak je uvedeno v [části aktualizace nejsou stahovány z intranetového koncového bodu (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Pokud jsou počítače nakonfigurované pro web Windows Update, ujistěte se, že jste se mohli spojit s koncovými body uvedenými [ ](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)v části.
  * Pokud jsou počítače nakonfigurované pro službu WSUS, ujistěte se, že máte přístup k serveru služby WSUS, který je nakonfigurován pomocí [klíče registru WUServer](/windows/deployment/update/waas-wu-settings).

Pokud se zobrazí hodnota HRESULT, dvakrát klikněte na výjimku zobrazenou červenou a zobrazte celou zprávu o výjimce. V následující tabulce najdete další možná řešení nebo akce, které je potřeba provést:

|Výjimka  |Řešení nebo akce  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Vyhledejte v [seznamu kód chyby ve službě Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) příslušný kód chyby, kde najdete další podrobnosti o příčině výjimky.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Tyto chyby jsou problémy s připojením k síti. Ujistěte se, že váš počítač má správné síťové připojení k Update Management. Seznam portů a adres, které se vyžadují, najdete v části věnované [Plánování sítě](../automation-update-management.md#ports) .        |
|`0x8024001E`| Operace aktualizace nebyla dokončena, protože služba nebo systém se vypnul.|
|`0x8024002E`| Služba web Windows Update je zakázána.|
|`0x8024402C`     | Pokud používáte server WSUS, zajistěte, aby hodnoty registru pro `WUServer` a `WUStatusServer` v klíči registru `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` měly správný server WSUS.        |
|`0x80072EE2`|Potíže s připojením k síti nebo potíže s nakonfigurovaným serverem WSUS. Zkontrolujte nastavení služby WSUS a ujistěte se, že je přístupná z klienta.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Ujistěte se, že služba web Windows Update (wuauserv) je spuštěná a není zakázaná.        |
|Jakákoli jiná obecná výjimka     | Prohledejte na internetu možná řešení a pracujte s místní podporou IT.         |

Kontrola `windowsupdate.log` vám pomůže se pokusit určit možnou příčinu. Další informace o tom, jak číst protokol, najdete v tématu [postup čtení souboru Windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Kromě toho můžete stáhnout a spustit [Poradce při potížích se web Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , abyste zkontrolovali, jestli na počítači nejsou nějaké nějaké problémy s web Windows Update.

> [!NOTE]
> [Poradce při potížích s web Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) uvádí, že je pro klienty Windows, ale funguje i na Windows serveru.

## <a name="scenario-update-run-returns-status-failed"></a>Scénář: příkaz Update Run vrátí stav "neúspěšné".

### <a name="issue"></a>Problém

Spustí se aktualizace, ale během běhu dojde k chybám.

### <a name="cause"></a>Příčina

Možné příčiny:

* Správce balíčků není v pořádku.
* Aktualizace agenta (WUA pro Windows, distribuce pro konkrétního správce balíčků pro Linux) je chybně nakonfigurovaná.
* Konkrétní balíčky můžou narušit cloudové opravy na základě cloudu.
* Počítač je nedosažitelný.
* Aktualizace obsahovaly závislosti, které nebyly vyřešeny.

### <a name="resolution"></a>Rozlišení

Pokud dojde k selhání během hromadné postupné aktualizace po úspěšném spuštění, [Podívejte se ve výstupu úlohy](../manage-update-multi.md#view-results-of-an-update-deployment) na příslušný počítač v běhu. Můžete najít konkrétní chybové zprávy z počítačů, které můžete prozkoumat a provádět s nimi akce. Update Management vyžaduje, aby správce balíčků byl v pořádku pro úspěšná nasazení aktualizací.

Pokud se určité opravy, balíčky nebo aktualizace zobrazují hned před tím, než se úloha nezdařila, můžete se je pokusit [vyloučit](../automation-tutorial-update-management.md#schedule-an-update-deployment) z příštího nasazení aktualizace. Chcete-li shromáždit informace protokolu z web Windows Update, přečtěte si téma [web Windows Update soubory protokolu](/windows/deployment/update/windows-update-logs).

Pokud problém s opravou nemůžete vyřešit, vytvořte kopii následujícího souboru protokolu a zachovejte ji **před** zahájením nasazení příští aktualizace:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-are-not-installed"></a>Opravy nejsou nainstalovány.

### <a name="machines-do-not-install-updates"></a>Počítače neinstalují aktualizace

* Zkuste aktualizace spustit přímo na počítači. Pokud počítač nejde aktualizovat, projděte si [seznam potenciálních chyb v průvodci odstraňováním potíží](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Pokud se aktualizace spustí místně, zkuste na počítači odebrat a znovu nainstalovat agenta podle pokynů v části [Odebrání virtuálního počítače z řešení Update Management](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management).

### <a name="i-know-updates-are-available-but-they-dont-show-as-needed-on-my-machines"></a>Ví, že aktualizace jsou k dispozici, ale v mých počítačích se nezobrazuje podle potřeby

* Často k tomu dochází v případě, že jsou počítače nakonfigurované tak, aby získávaly aktualizace z WSUS/SCCM, ale WSUS/SCCM aktualizace neschválí.
* Pokud chcete zkontrolovat, jestli jsou počítače nakonfigurované pro WSUS/SCCM, můžete [vzájemně porovnat klíč registru UseWUServer s klíči registru uvedenými v tomto dokumentu v části Konfigurace automatických aktualizací prostřednictvím úpravy registru](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).
* Pokud aktualizace nejsou schváleny ve službě WSUS, nebudou nainstalovány. V Log Analytics můžete vyhledat neschválené aktualizace s následujícím dotazem.

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Aktualizace ukazují jako nainstalované, ale nemůžu na mém počítači najít.

* Aktualizace se často nahrazují jinými aktualizacemi. Další informace najdete v části [Aktualizace se nahradila v průvodci odstraňováním potíží s webem Windows Update](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="installing-updates-by-classification-on-linux"></a>Instalace aktualizací podle klasifikace v Linuxu

* Pro nasazování aktualizací v Linuxu podle klasifikace (důležité aktualizace a aktualizace zabezpečení) platí důležité výhody a rizika, a to zejména pro CentOS. Tato [omezení jsou popsaná na stránce s přehledem řešení Update Management](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently--missing"></a>KB2267602 se konzistentně schází.

* KB2267602 je [aktualizace definice programu Windows Defender](https://www.microsoft.com/wdsi/definitions). Aktualizuje se každý den.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
