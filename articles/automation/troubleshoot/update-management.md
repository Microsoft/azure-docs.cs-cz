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
ms.openlocfilehash: 884ded67c25aca78225baef2d7e4c5de1cc94fd0
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782294"
---
# <a name="troubleshooting-issues-with-update-management"></a>Řešení potíží s Update Management

Tento článek popisuje řešení problémů, ke kterým může při použití Update Management běžet.

Došlo k potížím s agentem pro Hybrid Worker agenta, aby bylo možné zjistit příslušný problém. Další informace o poradci při potížích najdete v tématu [Poradce při potížích s aktualizací agenta](update-agent-issues.md). Všechny ostatní problémy najdete v podrobných informacích o možných problémech.

## <a name="general"></a>Obecné

### <a name="components-enabled-not-working"></a>Případě Součásti pro řešení ' Update Management ' byly povoleny a nyní je tento virtuální počítač konfigurován

#### <a name="issue"></a>Problém

Na virtuálním počítači se po 15 minutách po registraci stále zobrazuje následující zpráva:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobena následujícími důvody:

1. Komunikace zpět s účtem Automation je zablokovaná.
2. Virtuální počítač, který se připojuje, může pocházet z klonovaného počítače, který se nástroje Sysprep s nainstalovanou Microsoft Monitoring Agent.

#### <a name="resolution"></a>Řešení

1. Navštivte téma [Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning) , kde se dozvíte, které adresy a porty je potřeba povolit, aby Update Management fungovalo.
2. Pokud používáte Klonovaný obrázek:
   1. V pracovním prostoru Log Analytics odeberte virtuální počítač z uloženého hledání konfigurace `MicrosoftDefaultScopeConfig-Updates` oboru, pokud je zobrazený. Uložená hledání najdete v části **Obecné** v pracovním prostoru.
   2. Spustit `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Spusťte `Restart-Service HealthService` příkaz a `HealthService`restartujte. Tím se znovu vytvoří klíč a vygeneruje se nový UUID.
   4. Pokud to nepomůže, nástroj Sysprep nejprve napřed a po faktu nainstalujte agenta MMA.

### <a name="multi-tenant"></a>Případě Při vytváření nasazení aktualizace pro počítače v jiném tenantovi Azure se zobrazí chyba propojeného odběru.

#### <a name="issue"></a>Problém

Při pokusu o vytvoření nasazení aktualizace pro počítače v jiném tenantovi Azure se zobrazí následující chyba:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Příčina

K této chybě dojde, když vytvoříte nasazení aktualizace, které má virtuální počítače Azure v jiném tenantovi, které jsou součástí nasazení aktualizace.

#### <a name="resolution"></a>Řešení

K jejich naplánování budete muset použít následující alternativní řešení. K vytvoření plánu můžete použít rutinu [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) s `-ForUpdate` přepínačem a použít [rutinu New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) a předat počítače do druhé. do `-NonAzureComputer` parametru tenant. Následující příklad ukazuje příklad toho, jak to provést:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Případě Na portálu se nezobrazují počítače v části Update Management

#### <a name="issue"></a>Problém

Můžete spouštět v následujících scénářích:

* Váš počítač zobrazuje nenakonfigurovaný Update Management zobrazení virtuálního počítače.

* V zobrazení Update Management účtu Automation chybí vaše počítače.

* Máte počítače, které se zobrazují jako nehodnocené v **souladu s dodržováním předpisů**, ale data prezenčního signálu se zobrazí v protokolu Azure monitor pro Hybrid Runbook Worker, ale ne Update Management.

#### <a name="cause"></a>Příčina

To může být způsobeno potenciálními problémy s místní konfigurací nebo nesprávně nakonfigurovanou konfigurací oboru.

Je možné, že bude nutné znovu zaregistrovat a znovu nainstalovat Hybrid Runbook Worker.

Možná jste v pracovním prostoru nadefinovali kvótu, která byla dostupná a zastavují se data, která se mají ukládat.

#### <a name="resolution"></a>Řešení

* Ujistěte se, že váš počítač hlásí správný pracovní prostor. Ověřte, do jakého pracovního prostoru se oznamuje váš počítač. Pokyny k tomu, jak to ověřit, najdete v tématu [ověření připojení agenta k Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Pak ověřte, že se jedná o pracovní prostor propojený s vaším účtem Azure Automation. Potvrďte to tak, že přejdete do svého účtu Automation a kliknete na **propojený pracovní prostor** v části **související prostředky**.

* Zkontrolujte, jestli se počítače zobrazovaly v pracovním prostoru Log Analytics. Spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation. Pokud Váš počítač ve výsledcích dotazu nevidíte, váš počítač není prezenční signál, což znamená, že se jedná pravděpodobně o problém s místní konfigurací. Můžete spustit Poradce při potížích pro [systém Windows](update-agent-issues.md#troubleshoot-offline) nebo [Linux](update-agent-issues-linux.md#troubleshoot-offline) v závislosti na operačním systému, nebo můžete [agenta znovu nainstalovat](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Pokud se Váš počítač zobrazí ve výsledcích dotazu, je potřeba, abyste nastavili konfiguraci oboru určenou v následující odrážce.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Vyhledejte problémy s konfigurací oboru. [Konfigurace oboru](../automation-onboard-solutions-from-automation-account.md#scope-configuration) určuje, které počítače se nakonfigurují pro řešení. Pokud se Váš počítač zobrazuje v pracovním prostoru, ale nezobrazuje se vám, musíte nakonfigurovat konfiguraci oboru pro cílení na počítače. Další informace o tom, jak to udělat, najdete [v tématu zprovoznění počítačů v pracovním prostoru](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Pokud výše uvedené kroky nevyřešily váš problém, postupujte podle kroků v části [nasazení windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) k přeinstalování Hybrid Worker pro Windows nebo [nasazení Hybrid Runbook Worker Linux](../automation-linux-hrw-install.md) pro Linux.

* V pracovním prostoru spusťte následující dotaz. Pokud vidíte výsledek `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` , máte ve svém pracovním prostoru vytvořenou kvótu, která byla dosažená a zastavila ukládání dat. V pracovním prostoru přejděte na **využití a odhadované náklady** > **Správa objemu dat** a ověřte kvótu nebo odeberte kvótu, kterou máte.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Pokud narazíte na problémy při pokusu o připojení řešení na virtuálním počítači, podívejte se do protokolu událostí **Operations Manager** v části **protokoly aplikací a služeb** v místním počítači pro události s ID události **4502** a zprávou události. obsahuje **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

V následující části se zvýrazňují konkrétní chybové zprávy a možné řešení pro každou z nich. Další problémy s připojováním najdete v tématu [řešení potíží s registrací řešení](onboarding.md).

### <a name="machine-already-registered"></a>Případě Počítač je už zaregistrovaný na jiný účet.

#### <a name="issue"></a>Problém

Zobrazí se následující chybová zpráva:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Příčina

Počítač je již připojen k jinému pracovnímu prostoru pro Update Management.

#### <a name="resolution"></a>Řešení

Proveďte vyčištění starých artefaktů na počítači [odstraněním skupiny Hybrid Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) a zkuste to znovu.

### <a name="machine-unable-to-communicate"></a>Případě Počítač nemůže komunikovat se službou.

#### <a name="issue"></a>Problém

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

#### <a name="cause"></a>Příčina

Může se jednat o server proxy, bránu nebo bránu firewall blokující síťovou komunikaci.

#### <a name="resolution"></a>Řešení

Zkontrolujte své sítě a ujistěte se, že jsou povolené příslušné porty a adresy. Seznam portů a adres, které jsou vyžadovány Update Management a hybridními pracovními procesy Runbooku, najdete v tématu [požadavky na síť](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="unable-to-create-selfsigned-cert"></a>Případě Nepovedlo se vytvořit certifikát podepsaný svým držitelem.

#### <a name="issue"></a>Problém

Zobrazí se některá z následujících chybových zpráv:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Příčina

Hybrid Runbook Worker se nepodařilo vygenerovat certifikát podepsaný svým držitelem.

#### <a name="resolution"></a>Řešení

Ověřte, že účet System má oprávnění ke čtení pro složku **C:\ProgramData\Microsoft\Crypto\RSA** , a zkuste to znovu.

### <a name="failed-to-start"></a>Případě Spuštění počítače v nasazení aktualizace se nezdařilo.

#### <a name="issue"></a>Problém

Počítač se nepovedlo **Spustit** kvůli stavu počítače. Při zobrazení konkrétních podrobností pro počítač se zobrazí následující chyba:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Příčina

K této chybě může dojít z některého z následujících důvodů:

* Počítač už neexistuje.
* Počítač je vypnutý a nedosažitelný.
* Počítač má problém s připojením k síti a hybridní pracovní proces v počítači je nedosažitelný.
* Byla zjištěna aktualizace Microsoft Monitoring Agent, která změnila SourceComputerId
* Pokud jste v účtu Automation dosáhli limitu počtu souběžných úloh 2 000, vaše spuštění aktualizace může být omezené. Každé nasazení se považuje za úlohu a každý počítač v nasazení aktualizace se počítá jako úloha. Jakékoli jiné úlohy služby Automation nebo nasazení aktualizací, které aktuálně běží ve vašem účtu Automation, se počítá s limitem souběžných úloh.

#### <a name="resolution"></a>Řešení

V případě potřeby použijte pro nasazení aktualizací [dynamické skupiny](../automation-update-management.md#using-dynamic-groups) .

* Ověřte, že počítač stále existuje a je dostupný. Pokud neexistuje, upravte nasazení a odeberte počítač.
* Seznam portů a adres, které jsou potřeba pro Update Management a ověření, že váš počítač splňuje tyto požadavky, najdete v části věnované [Plánování sítě](../automation-update-management.md#ports) .
* Spusťte následující dotaz v Log Analytics k vyhledání počítačů ve vašem prostředí, jejichž `SourceComputerId` změna se změnila. Hledejte počítače, které mají stejnou `Computer` hodnotu, ale jinou `SourceComputerId` hodnotu. Po nalezení ovlivněných počítačů je nutné upravit nasazení aktualizace, která cílí na tyto počítače, a odebrat a znovu přidat počítače tak, aby `SourceComputerId` odrážely správnou hodnotu.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Případě Počítač se zobrazuje jako nehodnocený a zobrazuje výjimku HResult.

#### <a name="issue"></a>Problém

Máte počítače, které se zobrazují jako nehodnocené v **souladu s dodržováním předpisů**, a pod ní se zobrazí zpráva s výjimkou.

#### <a name="cause"></a>Příčina

Web Windows Update nebo WSUS není v počítači správně nakonfigurován. Update Management spoléhá na web Windows Update nebo WSUS k poskytnutí potřebných aktualizací, stavu opravy a výsledků nasazených oprav. Bez těchto informací Update Management nemusí správně hlásit informace o opravách, které jsou potřeba nebo nainstalované.

#### <a name="resolution"></a>Řešení

Dvojitým kliknutím na výjimku zobrazenou červeně zobrazíte celou zprávu o výjimce. V následující tabulce najdete další možná řešení nebo akce, které je potřeba provést:

|Výjimka  |Řešení nebo akce  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Vyhledejte v [seznamu kód chyby ve službě Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) příslušný kód chyby, kde najdete další podrobnosti o příčině výjimky.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Tyto chyby jsou problémy s připojením k síti. Ujistěte se, že váš počítač má správné síťové připojení k Update Management. Seznam portů a adres, které se vyžadují, najdete v části věnované [Plánování sítě](../automation-update-management.md#ports) .        |
|`0x8024001E`| Operace aktualizace nebyla dokončena, protože služba nebo systém se vypnul.|
|`0x8024002E`| Služba web Windows Update je zakázána.|
|`0x8024402C`     | Pokud používáte server WSUS, ujistěte se, že hodnoty registru pro `WUServer` a `WUStatusServer` v klíči `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` registru mají správný server WSUS.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Ujistěte se, že služba web Windows Update (wuauserv) je spuštěná a není zakázaná.        |
|Jakákoli jiná obecná výjimka     | Prohledejte na internetu možná řešení a pracujte s místní podporou IT.         |

Projděte `windowsupdate.log` si téma, které vám pomůže se pokusit určit možnou příčinu. Další informace o tom, jak číst protokol, najdete v tématu [postup čtení souboru Windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Kromě toho můžete stáhnout a spustit [Poradce při potížích se web Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , abyste zkontrolovali, jestli na počítači nejsou nějaké nějaké problémy s web Windows Update.

> [!NOTE]
> [Poradce při potížích s web Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) uvádí, že je pro klienty Windows, ale funguje i na Windows serveru.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scénář: Spuštění aktualizace se nezdařilo.

#### <a name="issue"></a>Problém

Spuštění aktualizace se nepodařilo spustit na počítači se systémem Linux.

#### <a name="cause"></a>Příčina

Hybrid Worker pro Linux není v pořádku.

#### <a name="resolution"></a>Řešení

Vytvořte kopii následujícího souboru protokolu a zachovejte ji pro účely řešení potíží:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scénář: Spustí se aktualizace spuštění, ale dojde k chybám.

#### <a name="issue"></a>Problém

Spustí se aktualizace, ale během běhu dojde k chybám.

#### <a name="cause"></a>Příčina

Možné příčiny:

* Správce balíčků není v pořádku.
* Konkrétní balíčky můžou kolidovat s použitím cloudových oprav.
* Jiné důvody

#### <a name="resolution"></a>Řešení

Pokud dojde k selhání během hromadné postupné aktualizace po úspěšném spuštění v systému Linux, podívejte se na výstup úlohy z daného počítače v běhu. Můžete najít konkrétní chybové zprávy od správce balíčků vašeho počítače, které můžete prozkoumat a provést s nimi akce. Update Management vyžaduje, aby správce balíčků byl v pořádku pro úspěšná nasazení aktualizací.

V některých případech může aktualizace balíčku narušit Update Management brání dokončení nasazení aktualizace. Pokud to vidíte, budete muset tyto balíčky vyloučit z budoucích aktualizací, nebo je ručně nainstalovat sami.

Pokud problém s opravou nemůžete vyřešit, vytvořte kopii následujícího souboru protokolu a zachovejte ji **před** zahájením nasazení příští aktualizace:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

### <a name="other"></a>Případě Můj problém není uvedený výše.

### <a name="issue"></a>Problém

Máte problém, který není vyřešen jinými uvedenými scénáři.

### <a name="cause"></a>Příčina

Nesprávně nakonfigurované nebo chybějící klíče registru můžou způsobit problémy s Update Management.

### <a name="resolution"></a>Řešení

Odstraňte klíč `HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker` registru a restartujte **HealthService**.

Můžete také použít následující příkazy PowerShellu.

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
