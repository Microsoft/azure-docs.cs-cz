---
title: Řešení potíží s Update managementem
description: Zjistěte, jak řešit problémy s Update managementem
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 01f72b8d41c1a973c7d187f519a43ce62929a23e
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359353"
---
# <a name="troubleshooting-issues-with-update-management"></a>Řešení potíží s Update managementem

Tento článek popisuje řešení Chcete-li vyřešit problémy, které můžete spouštět napříč při použití správy aktualizací.

Není k dispozici Poradce při potížích agenta pro agenta Hybrid Worker, chcete-li zjistit příčinu problému. Další informace o Poradce při potížích s, najdete v článku [Poradce při potížích aktualizace agenta](update-agent-issues.md). U všech ostatních typů problémů najdete v podrobné informace o možných problémech níže.

## <a name="general"></a>Obecné

### <a name="components-enabled-not-working"></a>Scénář: Komponenty pro řešení "Správa aktualizací" byly povoleny a nyní je tento virtuální počítač konfigurován

#### <a name="issue"></a>Problém

Bude pořád zobrazovat následující zpráva na virtuálním počítači 15 minut po připojení:

```
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Příčina

Tato chyba může být způsobeno z následujících důvodů:

1. Blokuje komunikaci zpět do účtu Automation.
2. Virtuální počítač právě připojili může pocházet z klonovaného počítače, který nebyl Sysprep s nainstalovaným agentem sledování Microsoft.

#### <a name="resolution"></a>Řešení

1. Navštíví, [plánování sítě](../automation-hybrid-runbook-worker.md#network-planning) Další informace o tom, které adresy a porty je potřeba povolit správu aktualizací pro práci.
2. Pokud pomocí nejprve image něm Klonovaná image nástroj sysprep a instalace agenta MMA po jejich výskytu.

### <a name="multi-tenant"></a>Scénář: Obdržíte chybu propojenému předplatnému při vytváření nasazení aktualizací pro počítače v jiném tenantovi Azure.

#### <a name="issue"></a>Problém

Při pokusu o vytvoření nasazení aktualizací pro počítače v jiném tenantovi Azure, zobrazí se následující chyba:

```
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Příčina

K této chybě dochází, když vytvoříte nasazení aktualizace, která má virtuální počítače Azure v jiném tenantovi zahrnuté v nasazení aktualizací.

#### <a name="resolution"></a>Řešení

Budete muset použít k získání je naplánováno následující alternativní řešení. Můžete použít [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule?view=azurermps-6.13.0) rutiny s přepínačem `-ForUpdate` vytvoření plánu a použití [AzureRmAutomationSoftwareUpdateConfiguration nový](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration?view=azurermps-6.13.0
) rutiny a předejte mu v druhém tenantovi pro počítače `-NonAzureComputer` parametru. Následující příklad ukazuje příklad o tom, jak to udělat:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Scénář: Aktualizace správy dat se nezobrazuje v Log Analytics pro počítač

#### <a name="issue"></a>Problém

Máte počítače, které se zobrazují jako **nevyhodnoceno** pod **dodržování předpisů**, ale uvidíte prezenčního signálu dat v Log Analytics pro Hybrid Runbook Worker, ale ne Update Management.

#### <a name="cause"></a>Příčina

Funkce Hybrid Runbook Worker možná muset znovu zaregistrovat a opětovném nainstalování.

#### <a name="resolution"></a>Řešení

Postupujte podle kroků uvedených v [nasadit Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) přeinstalovat hybridních pracovních procesů pro Windows nebo [nasazení Linuxu Hybrid Runbook Worker](../automation-linux-hrw-install.md) pro Linux.

## <a name="windows"></a>Windows

Pokud dojde k potížím při pokusu o připojení řešení na virtuálním počítači, zkontrolujte **nástroje Operations Manager** protokolu událostí v rámci **protokoly aplikací a služeb** na místním počítači pro události se ID události **4502** a zprávou události obsahující **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

Následující část se zaměřuje určité chybové zprávy a možné řešení pro každý. Pro další připojení zobrazit problémy, [připojování k řešení potíží s](onboarding.md).

### <a name="machine-already-registered"></a>Scénář: Počítač je již zaregistrován jiný účet

#### <a name="issue"></a>Problém

Zobrazí se následující chybová zpráva:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Příčina

Na počítači už je připojený k jinému pracovnímu prostoru pro správu aktualizací.

#### <a name="resolution"></a>Řešení

Proveďte vyčištění starých artefaktů na počítači pomocí [odstraněním hybridních runbooků](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) a zkuste to znovu.

### <a name="machine-unable-to-communicate"></a>Scénář: Počítač je schopen komunikovat se službou

#### <a name="issue"></a>Problém

Zobrazí jednu z následujících chybových zpráv:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Příčina

Může být proxy server, brána nebo brána firewall blokuje komunikaci sítě.

#### <a name="resolution"></a>Řešení

Zkontrolujte sítě a ujistěte se, že jsou povolené příslušné porty a adresy. Zobrazit [požadavky na síťovou](../automation-hybrid-runbook-worker.md#network-planning), seznam portů a adres, které jsou vyžadované Update Management a procesy Hybrid Runbook Worker.

### <a name="unable-to-create-selfsigned-cert"></a>Scénář: Nelze vytvořit certifikát podepsaný svým držitelem

#### <a name="issue"></a>Problém

Zobrazí jednu z následujících chybových zpráv:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Příčina

Funkce Hybrid Runbook Worker nebyl schopen generovat certifikát podepsaný svým držitelem

#### <a name="resolution"></a>Řešení

Ověřte systémový účet má oprávnění ke čtení do složky **C:\ProgramData\Microsoft\Crypto\RSA** a zkuste to znovu.

### <a name="hresult"></a>Scénář: Počítač zobrazuje jako nevyhodnoceno a ukazuje výjimku HResult

#### <a name="issue"></a>Problém

Máte počítače, které se zobrazují jako **nevyhodnoceno** pod **dodržování předpisů**, a zobrazí zprávu o výjimce pod ní.

#### <a name="cause"></a>Příčina

Windows Update nebo WSUS není v počítači správně nakonfigurován. Update Management využívá Windows Update nebo WSUS za účelem zajištění aktualizací, které jsou nezbytné a stav opravy a výsledků nasazení oprav. Bez těchto informací nemůže Update Management zprávu o opravy, které jsou potřeba nebo nainstalovaná správně.

#### <a name="resolution"></a>Řešení

Poklikáním na výjimku zobrazí červeně naleznete ve zprávě celý. Projděte si následující tabulku pro potenciální řešení nebo akcí:

|Výjimka  |Řešení nebo akce  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Vyhledávání kódu relevantní chyby v [Windows aktualizujte seznam chyb kódu](https://support.microsoft.com/help/938205/windows-update-error-code-list) zobrazíte další podrobnosti příčina výjimky.        |
|`0x8024402C` nebo `0x8024401C`     | Tyto chyby jsou problémy se síťovým připojením. Ujistěte se, že váš počítač má správné síťové připojení ke správě aktualizací. Naleznete v části [plánování sítě](../automation-update-management.md#ports) seznam portů a adres, které jsou požadovány.        |
|`0x8024402C`     | Pokud používáte WSUS server, ujistěte se, že hodnoty registru `WUServer` a `WUStatusServer` v klíči registru `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` mít správný server služby WSUS.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Ujistěte se, že službu Windows Update (wuauserv) je spuštěná a není zakázáno.        |
|Další obecné výjimky     | Hledáním na Internetu možná řešení a pracovat s místním podpory IT.         |

Kromě toho můžete stáhnout a spustit [Poradce při potížích s Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) k zkontrolujte, jestli jsou všechny problémy na počítači s Windows Update.

> [!NOTE]
> [Poradce při potížích s Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) uvedeno, je pro klienty Windows, ale funguje stejně v systému Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scénář: Hromadná postupná aktualizace se nepodaří spustit

#### <a name="issue"></a>Problém

Selhání spuštění aktualizace spustíte na počítači s Linuxem.

#### <a name="cause"></a>Příčina

Funkce Hybrid Worker Linuxu není v pořádku.

#### <a name="resolution"></a>Řešení

Vytvořte kopii v následujícím souboru protokolu a uchovat pro účely odstraňování potíží:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scénář: Spustí hromadnou postupnou aktualizaci, ale dojde k chybám

#### <a name="issue"></a>Problém

Při hromadné postupné aktualizaci se spustí, ale dojde k chybám za běhu.

#### <a name="cause"></a>Příčina

Možných příčin může být:

* Správce balíčků není v pořádku
* Konkrétní balíčky může být v rozporu s opravami cloudové
* Jiné důvody

#### <a name="resolution"></a>Řešení

Pokud dojde k selhání během aktualizace spouští potom, co se úspěšně spustí v systému Linux, zkontrolujte výstup z napadeného počítače při spuštění úlohy. Může se stát specifické chybové zprávy z vašeho počítače Správce balíčků, kterou můžete prozkoumat a provést akci. Správa aktualizací vyžaduje správce balíčku se stavem v pořádku pro nasazení úspěšné aktualizace.

V některých případech může aktualizace balíčků může narušovat Update Management, které brání v dokončení nasazení aktualizace. Pokud uvidíte, že, budete mít k vyloučení těchto balíčků hromadné postupné aktualizace budoucí nebo je nainstalovat ručně sami.

Pokud nemůžete vyřešit problém s opravami, vytvořte kopii v následujícím souboru protokolu a zachovat jeho **před** dalšího nasazení aktualizace spustí pro účely odstraňování potíží:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.