---
title: Řešení chyb pomocí Update Management
description: Naučte se řešit potíže s Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f5346f2f11df2282a1cd2592db930f7ff829a2d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75416782"
---
# <a name="troubleshooting-issues-with-update-management"></a>Řešení potíží s Update Management

Tento článek popisuje řešení problémů, se kterými se můžete setkat při použití Update Management.

U agenta Hybrid Worker je poradce při potížích s agentem, aby mohl zjistit základní problém. Další informace o poradci při potížích najdete v tématu [Poradce při potížích s aktualizací agenta](update-agent-issues.md). U všech ostatních problémů použijte následující pokyny k odstraňování potíží.

Pokud narazíte na problémy při připojování řešení na virtuálním počítači, podívejte se do protokolu **Operations Manager** v části **protokoly aplikací a služeb** v místním počítači na události s ID události 4502 a podrobností události, které obsahují **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

V následující části se zvýrazňují konkrétní chybové zprávy a možná řešení pro každé z nich. Další problémy s připojováním najdete v tématu [řešení potíží s registrací řešení](onboarding.md).

## <a name="nologs"></a>Scénář: počítače se nezobrazují na portálu v části Update Management

### <a name="issue"></a>Problém

Dochází k následujícím potížím:

* Počítač zobrazuje **nenakonfigurovaná** Update Management zobrazení virtuálního počítače.

* V zobrazení Update Management účtu Azure Automation chybí vaše počítače.

* Máte počítače, které se zobrazují jako **nehodnocené** v **souladu s dodržováním předpisů**. Data prezenčního signálu se ale zobrazí v protokolech Azure Monitor pro Hybrid Runbook Worker, ale ne pro Update Management.

### <a name="cause"></a>Příčina

K tomuto problému může dojít při potížích s místními konfiguracemi nebo nesprávně nakonfigurovaným nastavením oboru.

Možná bude nutné znovu zaregistrovat a znovu nainstalovat Hybrid Runbook Worker.

Je možné, že jste v pracovním prostoru nastavili kvótu, která je dosažená a která zabraňuje dalšímu ukládání dat.

### <a name="resolution"></a>Rozlišení

* Spusťte Poradce při potížích pro [Windows](update-agent-issues.md#troubleshoot-offline) nebo [Linux](update-agent-issues-linux.md#troubleshoot-offline), a to v závislosti na operačním systému.

* Ujistěte se, že váš počítač hlásí správný pracovní prostor. Pokyny k tomu, jak tento aspekt ověřit, najdete v tématu [ověření připojení agenta k Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Také se ujistěte, že je tento pracovní prostor propojený s vaším účtem Azure Automation. Potvrďte to tak, že přejdete na svůj účet Automation a v části **související prostředky**vyberete **propojený pracovní prostor** .

* Zajistěte, aby se počítače zobrazovaly v pracovním prostoru Log Analytics. Spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Pokud počítač ve výsledcích dotazu nevidíte, nedošlo v poslední době k vrácení se změnami, což znamená, že došlo k potížím s místní konfigurací, a měli byste [agenta přeinstalovat](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Pokud se Váš počítač zobrazí ve výsledcích dotazu, je nutné ověřit konfiguraci oboru zadanou v další odrážkové položce v tomto seznamu.

* Vyhledejte problémy s konfigurací oboru. [Konfigurace oboru](../automation-onboard-solutions-from-automation-account.md#scope-configuration) určuje, které počítače se nakonfigurují pro řešení. Pokud se Váš počítač zobrazuje v pracovním prostoru, ale ne na portálu **Update Management** , budete muset nakonfigurovat konfiguraci oboru pro cílení na počítače. Další informace o tom, jak to udělat, najdete [v tématu zprovoznění počítačů v pracovním prostoru](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* V pracovním prostoru spusťte následující dotaz:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Pokud se zobrazí výsledek `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, je ve vašem pracovním prostoru k dispozici kvóta, která byla dosažena a byla zastavena ukládání dat. V pracovním prostoru můžete přejít na **využití a odhadované náklady** > **Správa objemu dat** a ověřit kvótu nebo ji odebrat.

* Pokud tyto kroky problém nevyřeší, postupujte podle kroků v části [nasazení Hybrid Runbook Worker systému Windows](../automation-windows-hrw-install.md) a přeinstalujte Hybrid Worker pro Windows. Nebo pro Linux [Nasaďte Hybrid Runbook Worker pro Linux](../automation-linux-hrw-install.md).

## <a name="rp-register"></a>Scénář: nejde zaregistrovat poskytovatele prostředků služby Automation pro předplatná.

### <a name="issue"></a>Problém

Při práci s řešeními ve vašem účtu Automation dojde k následující chybě:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Příčina

Zprostředkovatel prostředků Automation není v předplatném zaregistrován.

### <a name="resolution"></a>Rozlišení

Chcete-li zaregistrovat poskytovatele prostředků služby Automation, postupujte podle následujících kroků v Azure Portal:

1. V seznamu služeb Azure v dolní části portálu vyberte **všechny služby**a potom vyberte **předplatná** ve skupině obecné služby.
2. Vyberte své předplatné.
3. V části **Nastavení**vyberte **poskytovatelé prostředků**.
4. V seznamu poskytovatelů prostředků ověřte, že je zaregistrován poskytovatel prostředků **Microsoft. Automation** .
5. Pokud není v seznamu uveden, zaregistrujte poskytovatele **Microsoft. Automation** podle kroků v části [řešení chyb pro registraci poskytovatele prostředků](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="components-enabled-not-working"></a>Scénář: součásti pro řešení Update Management jsou povolené a teď se konfiguruje tento virtuální počítač.

### <a name="issue"></a>Problém

Na virtuálním počítači se po 15 minutách po registraci stále zobrazuje následující zpráva:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z následujících důvodů:

- Komunikace s účtem Automation je zablokovaná.
- Virtuální počítač, který se připojuje, může pocházet z klonovaného počítače, který se nástroje Sysprep s nainstalovanou sadou Microsoft Monitoring Agent (MMA).

### <a name="resolution"></a>Rozlišení

1. Pokud chcete zjistit, které adresy a porty musí Update Management fungovat, přejít na [Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning) .
2. Pokud používáte Klonovaný obrázek:
   1. V pracovním prostoru Log Analytics odeberte virtuální počítač z uloženého hledání konfigurace `MicrosoftDefaultScopeConfig-Updates` oboru, pokud je zobrazený. Uložená hledání najdete v části **Obecné** v pracovním prostoru.
   2. Spusťte `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`.
   3. Spusťte `Restart-Service HealthService` pro restartování `HealthService`. Tím se klíč znovu vytvoří a vygeneruje se nový UUID.
   4. Pokud tento přístup nefunguje, nejprve na imagi spusťte nástroj Sysprep a pak nainstalujte MMA.

## <a name="multi-tenant"></a>Scénář: při vytváření nasazení aktualizace pro počítače v jiném tenantovi Azure se zobrazí chyba propojeného předplatného.

### <a name="issue"></a>Problém

Při pokusu o vytvoření nasazení aktualizace pro počítače v jiném tenantovi Azure dojde k následující chybě:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Příčina

K této chybě dojde, když vytvoříte nasazení aktualizace, které obsahuje virtuální počítače Azure v jiném tenantovi, které jsou zahrnuté v nasazení aktualizace.

### <a name="resolution"></a>Rozlišení

K naplánování těchto položek použijte následující alternativní řešení. K vytvoření plánu můžete použít rutinu [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) s přepínačem `-ForUpdate`. Pak použijte rutinu [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) a předejte počítače do jiného tenanta do parametru `-NonAzureComputer`. Následující příklad ukazuje, jak to provést:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Scénář: Neobjasněná restartování

### <a name="issue"></a>Problém

I když jste nastavili možnost **ovládacího prvku restartování** na **Nikdy nerestartovat**, počítače se pořád po instalaci aktualizací restartují.

### <a name="cause"></a>Příčina

Web Windows Update může upravit několik klíčů registru, z nichž některé můžou upravovat chování při restartování.

### <a name="resolution"></a>Rozlišení

Zkontrolujte klíče registru uvedené v části [Konfigurace automatických aktualizací úpravou registru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) a [klíčů registru používaných ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) a ujistěte se, že jsou vaše počítače správně nakonfigurované.

## <a name="failed-to-start"></a>Scénář: v nasazení aktualizace se v počítači zobrazí zpráva "Nepodařilo se spustit"

### <a name="issue"></a>Problém

Počítač zobrazuje stav **neúspěšného spuštění** . Při zobrazení konkrétních podrobností pro počítač se zobrazí následující chyba:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Příčina

K této chybě může dojít z některého z následujících důvodů:

* Počítač už neexistuje.
* Počítač je vypnutý a nedosažitelný.
* Počítač má problém s připojením k síti, a proto je hybridní pracovní proces na počítači nedosažitelný.
* Byla zjištěna aktualizace MMA, která změnila SourceComputerId.
* Pokud jste dosáhli limitu 2 000 souběžných úloh v účtu Automation, vaše spuštění aktualizace bylo omezené. Každé nasazení se považuje za úlohu a každý počítač v nasazení aktualizace se počítá jako úloha. Jakékoli jiné úlohy služby Automation nebo nasazení aktualizací, které aktuálně běží ve vašem účtu Automation, se počítá s limitem souběžných úloh.

### <a name="resolution"></a>Rozlišení

V případě potřeby použijte pro nasazení aktualizací [dynamické skupiny](../automation-update-management-groups.md) . A dále:

* Ověřte, že počítač stále existuje a je dostupný. Pokud neexistuje, upravte nasazení a odeberte počítač.
* V části [Plánování sítě](../automation-update-management.md#ports) najdete seznam portů a adres, které jsou potřeba pro Update Management, a pak ověřte, že váš počítač splňuje tyto požadavky.
* Spusťte následující dotaz v Log Analytics k vyhledání počítačů ve vašem prostředí, jejichž `SourceComputerId` se změnila. Vyhledejte počítače se stejnou `Computer`ovou hodnotou, ale jinou hodnotou `SourceComputerId`. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   Po nalezení ovlivněných počítačů upravte nasazení aktualizace, která cílí na tyto počítače, a pak je odeberte a znovu přidejte, aby `SourceComputerId` odrážela správnou hodnotu.

## <a name="updates-nodeployment"></a>Scénář: aktualizace se instalují bez nasazení.

### <a name="issue"></a>Problém

Při registraci počítače se systémem Windows v nástroji Update Management se zobrazí aktualizace nainstalované bez nasazení.

### <a name="cause"></a>Příčina

Ve Windows se aktualizace nainstalují automaticky, jakmile budou k dispozici. Toto chování může způsobit nejasnost, pokud jste neplánovali nasazení aktualizace na počítač.

### <a name="resolution"></a>Rozlišení

Klíč registru `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` se standardně nastavuje na hodnotu 4: **automaticky stáhnout a nainstalovat**.

U Update Management klientů doporučujeme nastavit tento klíč na 3: **automaticky stahovat, ale neinstalovat automaticky**.

Další informace najdete v tématu [Konfigurace automatických aktualizací](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Scénář: počítač je už zaregistrovaný na jiný účet.

### <a name="issue"></a>Problém

Zobrazí se tato chybová zpráva:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Příčina

Počítač už je zaregistrované do jiného pracovního prostoru pro Update Management.

### <a name="resolution"></a>Rozlišení

1. Postupujte podle kroků v části [počítače se nezobrazují na portálu v části Update Management](#nologs) , abyste se ujistili, že se počítač hlásí do správného pracovního prostoru.
2. Vyčistěte staré artefakty na počítači [odstraněním skupiny Hybrid Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)a akci opakujte.

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

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Příčina

Server proxy, brány nebo brány firewall můžou blokovat síťovou komunikaci. 

### <a name="resolution"></a>Rozlišení

Zkontrolujte své sítě a ujistěte se, že jsou povolené příslušné porty a adresy. Seznam portů a adres, které jsou vyžadovány Update Management a hybridními pracovními procesy Runbooku, najdete v části [požadavky na síť](../automation-hybrid-runbook-worker.md#network-planning) .

## <a name="unable-to-create-selfsigned-cert"></a>Scénář: Nejde vytvořit certifikát podepsaný svým držitelem.

### <a name="issue"></a>Problém

Zobrazí se některá z následujících chybových zpráv:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Příčina

Hybrid Runbook Worker se nepovedlo vygenerovat certifikát podepsaný svým držitelem.

### <a name="resolution"></a>Rozlišení

Ověřte, že účet System má oprávnění ke čtení pro složku **C:\ProgramData\Microsoft\Crypto\RSA** , a zkuste to znovu.

## <a name="mw-exceeded"></a>Scénář: naplánovaná aktualizace selhala s chybou MaintenanceWindowExceeded.

### <a name="issue"></a>Problém

Výchozí časové období údržby pro aktualizace je 120 minut. Časový interval pro správu a údržbu můžete zvýšit na maximálně 6 hodin nebo 360 minut.

### <a name="resolution"></a>Rozlišení

Upravte všechna neúspěšná naplánovaná nasazení aktualizací a zvyšte časový interval pro správu a údržbu.

Další informace o časových obdobích údržby najdete v tématu [install Updates](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Scénář: počítač se zobrazuje jako "nehodnocený" a zobrazuje výjimku HResult

### <a name="issue"></a>Problém

* Máte počítače, které se zobrazují jako **nehodnocené** v **souladu s dodržováním předpisů**, a pod ní se zobrazí zpráva s výjimkou.
* Máte počítače, které se zobrazují jako nehodnocené.
* Na portálu se zobrazí kód chyby HRESULT.

### <a name="cause"></a>Příčina

Agent aktualizace (agent web Windows Update v systému Windows; Správce balíčků pro distribuci pro Linux) není správně nakonfigurovaný. Update Management spoléhá na agenta aktualizace počítače, aby poskytoval aktualizace, které jsou potřeba, stav opravy a výsledky nasazených oprav. Bez těchto informací Update Management nedokáže správně ohlásit potřebné opravy, které jsou potřeba nebo nainstalované.

### <a name="resolution"></a>Rozlišení

Zkuste provést aktualizace místně na počítači. Pokud se to nepovede, obvykle to znamená, že došlo k chybě konfigurace agenta aktualizací.

Tato chyba je často způsobena konfigurací sítě a problémy s bránou firewall. Vyzkoušejte následující kroky:

* V případě systému Linux zkontrolujte příslušnou dokumentaci a ujistěte se, že máte přístup ke koncovému bodu sítě úložiště balíčků.
* V případě systému Windows ověřte konfiguraci agenta, jak je uvedeno v [části aktualizace nejsou stahovány z intranetového koncového bodu (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Pokud jsou počítače nakonfigurované pro web Windows Update, ujistěte se, že máte přístup ke koncovým bodům popsaným v tématu [problémy související s HTTP/proxy serverem](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Pokud jsou počítače nakonfigurované pro Windows Server Update Services (WSUS), ujistěte se, že se můžete připojit k serveru WSUS nakonfigurovanému pomocí [klíče registru WUServer](/windows/deployment/update/waas-wu-settings).

Pokud se zobrazí hodnota HRESULT, dvakrát klikněte na výjimku zobrazenou červenou a zobrazte celou zprávu o výjimce. V následující tabulce najdete potenciální řešení nebo doporučené akce:

|Výjimka  |Řešení nebo akce  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Vyhledejte v [seznamu kód chyby ve službě Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) příslušný kód chyby, kde najdete další podrobnosti o příčině výjimky.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ty naznačují problémy s připojením k síti. Ujistěte se, že váš počítač má síťové připojení k Update Management. Seznam požadovaných portů a adres najdete v části [Plánování sítě](../automation-update-management.md#ports) .        |
|`0x8024001E`| Operace aktualizace nebyla dokončena, protože služba nebo systém se vypnul.|
|`0x8024002E`| Služba web Windows Update je zakázána.|
|`0x8024402C`     | Pokud používáte server WSUS, ujistěte se, že hodnoty registru pro `WUServer` a `WUStatusServer` v klíči registru `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` určují správný server WSUS.        |
|`0x80072EE2`|Došlo k potížím s připojením k síti nebo k potížím při komunikaci s nakonfigurovaným serverem WSUS. Zkontrolujte nastavení služby WSUS a ujistěte se, že je služba z klienta přístupná.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Ujistěte se, že služba web Windows Update (wuauserv) je spuštěná a není zakázaná.        |
|`0x80070005`| Chyba odepření přístupu může být způsobena některou z následujících příčin:<br> Nakažený počítač<br> Nastavení web Windows Update není správně nakonfigurováno.<br> Chyba oprávnění souboru ve složce%WinDir%\SoftwareDistribution<br> Nedostatek místa na disku na systémové jednotce (C:).
|Jakákoli jiná obecná výjimka     | Spusťte hledání na internetu z hlediska možných řešení a pracujte s místní podporou IT.         |

Kontrola souboru%Windir%\Windowsupdate.log vám také pomůže určit možné příčiny. Další informace o tom, jak číst protokol, najdete v tématu [postup čtení souboru Windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Můžete si také stáhnout a spustit [Poradce při potížích se web Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) a vyhledat případné problémy s web Windows Update v počítači.

> [!NOTE]
> V dokumentaci k [poradci při potížích se web Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) označuje, že se má používat na klientech Windows, ale funguje taky na Windows serveru.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scénář: příkaz Update Run vrátí stav neúspěchu (Linux).

### <a name="issue"></a>Problém

Spustí se aktualizace, ale během běhu dojde k chybám.

### <a name="cause"></a>Příčina

Možné příčiny:

* Správce balíčků není v pořádku.
* Aktualizace agenta (WUA pro Windows, distribuce pro konkrétního správce balíčků pro Linux) je chybně nakonfigurovaná.
* Konkrétní balíčky jsou v konfliktu s využitím cloudových oprav.
* Počítač je nedosažitelný.
* Aktualizace obsahovaly závislosti, které nebyly vyřešeny.

### <a name="resolution"></a>Rozlišení

Pokud dojde k selhání během hromadné postupné aktualizace po úspěšném spuštění, [Podívejte se ve výstupu úlohy](../manage-update-multi.md#view-results-of-an-update-deployment) na příslušný počítač v běhu. Můžete najít konkrétní chybové zprávy z počítačů, které můžete prozkoumat a provádět s nimi akce. Update Management vyžaduje, aby správce balíčků byl v pořádku pro úspěšná nasazení aktualizací.

Pokud se určité opravy, balíčky nebo aktualizace zobrazují hned před tím, než se úloha nezdařila, můžete se je pokusit [vyloučit](../automation-tutorial-update-management.md#schedule-an-update-deployment) z příštího nasazení aktualizace. Chcete-li shromáždit informace protokolu z web Windows Update, přečtěte si téma [web Windows Update soubory protokolu](/windows/deployment/update/windows-update-logs).

Pokud nemůžete vyřešit problém s opravou, vytvořte kopii následujícího souboru protokolu a zachovejte ji pro účely řešení potíží *před* spuštěním příštího nasazení aktualizace:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Opravy nejsou nainstalované.

### <a name="machines-dont-install-updates"></a>Počítače neinstalují aktualizace

* Zkuste aktualizace spustit přímo na počítači. Pokud počítač nemůže aktualizace použít, Projděte si [seznam potenciálních chyb v Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Pokud se aktualizace spouštějí místně, zkuste odebrat a znovu nainstalovat agenta v počítači podle pokynů uvedených v části [Odebrání virtuálního počítače z Update Management](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Ví, že aktualizace jsou k dispozici, ale nezobrazují se jako dostupné na mých počítačích

* K tomu často dochází, když jsou počítače nakonfigurované tak, aby získaly aktualizace ze služby WSUS nebo System Center Configuration Manager (SCCM), ale služby WSUS a SCCM aktualizace neschválily.
* [V části Konfigurace automatických aktualizací úpravou registru v tomto článku](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)můžete ověřit, jestli jsou počítače nakonfigurované pro WSUS a SCCM pomocí křížového odkazu na klíč registru UseWUServer na klíče registru.
* Pokud se aktualizace ve službě WSUS neschvalují, nebudou nainstalovány. Neschválené aktualizace můžete vyhledat v Log Analytics spuštěním následujícího dotazu:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Aktualizace ukazují jako nainstalované, ale nemůžu na mém počítači najít.

* Aktualizace se často nahrazují jinými aktualizacemi. Další informace najdete v tématu [aktualizace je nahrazená](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) v Průvodci odstraňováním potíží s web Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalace aktualizací podle klasifikace v Linuxu

* Pro nasazování aktualizací v Linuxu podle klasifikace (důležité aktualizace a aktualizace zabezpečení) platí důležité výhody a rizika, a to zejména pro CentOS. Tato omezení jsou popsaná na [stránce přehled Update Management](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 se konzistentně schází.

* KB2267602 je [aktualizace definice programu Windows Defender](https://www.microsoft.com/wdsi/definitions). Aktualizuje se každý den.

## <a name="next-steps"></a>Další kroky

Pokud jste nezobrazili svůj problém nebo ho nemůžete vyřešit, zkuste další podporu vyzkoušet u jednoho z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport)oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
