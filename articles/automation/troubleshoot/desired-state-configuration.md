---
title: Řešení potíží s konfigurací stavu automatizace Azure (DSC)
description: Tento článek obsahuje informace o řešení potíží s konfigurací stavu azure automatizace (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679310"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Řešení problémů s konfigurací stavu automatizace Azure (DSC)

Tento článek obsahuje informace o řešení problémů, které vznikají při kompilaci nebo nasazování konfigurací v konfiguraci stavu azure automatizace (DSC).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-an-issue"></a>Diagnostika problému

Když se zobrazí chyba kompilace nebo nasazení pro konfiguraci, zde je několik kroků, které vám pomohou diagnostikovat problém.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Ujistěte se, že se konfigurace úspěšně zkompiluje v místním počítači

Konfigurace stavu automatizace Azure (DSC) je postavená na konfiguraci požadovaného stavu prostředí PowerShell (DSC). Dokumentaci k jazyku DSC a syntaxi najdete v [dokumentech PowerShell DSC .](https://docs.microsoft.com/powershell/scripting/overview)

Kompilací konfigurace DSC v místním počítači můžete zjistit a vyřešit běžné chyby, například:

   - Chybějící moduly
   - Chyby syntaxe
   - Logické chyby

### <a name="2-view-dsc-logs-on-your-node"></a>2. Zobrazení protokolů DSC na uzlu

Pokud se konfigurace úspěšně zkompiluje, ale při použití na uzel se nezdaří, můžete najít podrobné informace v protokolech DSC. Informace o tom, kde tyto protokoly najít, naleznete [v tématu Kde jsou protokoly událostí DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Modul [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) vám může pomoci při analýzě podrobných informací z protokolů DSC. Pokud se obrátíte na podporu, vyžadují tyto protokoly k diagnostice problému.

Modul můžete `xDscDiagnostics` nainstalovat do místního počítače podle pokynů v [části Instalace modulu stabilní verze](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Chcete-li `xDscDiagnostics` nainstalovat modul na počítači Azure, použijte [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). Možnost **Spustit příkaz** na webu Azure portal můžete použít také podle kroků uvedených ve [skriptech Run PowerShell ve vašem virtuálním počítači s Windows pomocí příkazu Spustit](../../virtual-machines/windows/run-command.md).

Informace o použití **xDscDiagnostics**naleznete [v tématu Použití xDscDiagnostics k analýze protokolů DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Viz také [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Ujistěte se, že uzly a pracovní prostor automatizace mají požadované moduly

DSC závisí na modulech nainstalovaných v uzlu. Při použití konfigurace stavu automatizace Azure importujte všechny požadované moduly do svého účtu Automation pomocí kroků v [části Import modulech](../shared-resources/modules.md#importing-modules). Konfigurace mohou mít také závislost na konkrétních verzích modulů. Další informace naleznete [v tématu Poradce při potížích s moduly](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scénář: Konfiguraci se speciálními znaky nelze z portálu odstranit.

### <a name="issue"></a>Problém

Při pokusu o odstranění konfigurace DSC z portálu se zobrazí následující chyba:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Příčina

Tato chyba je dočasný problém, který je plánován k vyřešení.

### <a name="resolution"></a>Řešení

Pomocí rutiny [Remove-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 rutina k odstranění konfigurace.

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scénář: Nepodařilo se zaregistrovat agenta Dsc.

### <a name="issue"></a>Problém

Při [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) nebo jiné rutiny DSC se zobrazí chyba:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Příčina

Tato chyba je obvykle způsobena bránou firewall, počítač je za proxy serverem nebo jiné chyby sítě.

### <a name="resolution"></a>Řešení

Ověřte, zda má počítač přístup ke správným koncovým bodům pro DSC, a akci opakujte. Seznam potřebných portů a adres naleznete v [tématu plánování sítě](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scénář: Zprávy o stavu vrátí kód odpovědi neoprávněný

### <a name="issue"></a>Problém

Při registraci uzlu s Azure Automation State Configuration se zobrazí jedna z následujících chybových zpráv:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Příčina

Tento problém je způsoben chybný certifikát nebo vypršela jeho platnost. Viz [Vypršení platnosti a opětovného registrace certifikátu](../automation-dsc-onboarding.md#re-registering-a-node).

Tento problém může být také způsoben konfigurací proxy serveru, která neumožňuje přístup k ***.azure-automation.net**. Další informace naleznete v [tématu Konfigurace privátních sítí](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Řešení

Pomocí následujících kroků znovu zaregistrujte uzel DSC, který se lháskne.

Krok 1 – Zrušení registrace uzlu.

1. Na webu Azure Portal přejděte na účty **domácí** -> **automatizace** -> (váš účet automation) -> **konfigurace stavu (DSC).**
2. Vyberte **uzly**a klikněte na uzel s problémy.
3. Chcete-li zrušit registraci uzlu, klepněte na tlačítko **Odregistrovat.**

Krok 2 – Odinstalujte rozšíření DSC z uzlu.

1. Na webu Azure Portal přejděte na **rozšíření –>** -> **Virtual Machine** (uno se selháním) **->**.
2. Vyberte **Microsoft.Powershell.DSC**, rozšíření PowerShell DSC.
3. Klepnutím na tlačítko **Odinstalovat** odinstalujte rozšíření.

Krok 3 – Odeberte z uzlu všechny chybné certifikáty nebo certifikáty s ukončenou platností.

Na uzlu se selháním z výzvy prostředí PowerShell se zvýšenými oprávněními spusťte tyto příkazy:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

Krok 4 - Znovu zaregistrovat uzel selhání.

1. Na webu Azure Portal přejděte na účty **domácí** -> **automatizace** -> (váš účet automatizace) -> **konfigurace stavu (DSC)**
2. Vyberte **možnost Uzly**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Vyberte uzel selhání.
5. Klikněte na **Připojit** a vyberte požadované možnosti.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scénář: Uzel je ve stavu selhání s chybou "Nebyl nalezen"

### <a name="issue"></a>Problém

Uzel má sestavu se stavem Selhání a obsahující chybu:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Příčina

K této chybě obvykle dochází, když je uzel přiřazen názvu konfigurace, například **ABC**, namísto názvu konfigurace uzlu (soubor MOF), například **ABC. WebServer**.

### <a name="resolution"></a>Řešení

* Ujistěte se, že přiřazujete uzel s názvem konfigurace uzlu a nikoli s názvem konfigurace.
* Konfiguraci uzlu můžete přiřadit k uzlu pomocí portálu Azure portal nebo pomocí rutiny Prostředí PowerShell.

  * Na webu Azure Portal **Home** -> přejděte na**účet domácí automatizace** -> (váš účet automatizace) -> **konfigurace stavu (DSC),** vyberte uzel a klikněte na **Přiřadit konfiguraci uzlu**.
  * Použijte rutinu [Set-AzAutomationDscNode.](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0)

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scénář: Při kompilaci konfigurace nebyla vytvořena žádná konfigurace uzlů (soubory MOF).

### <a name="issue"></a>Problém

Úloha kompilace DSC se pozastaví s chybou:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Příčina

Pokud se výraz `Node` následující za klíčovým slovem `$null`v konfiguraci DSC vyhodnotí na , jsou vytvořeny žádné konfigurace uzlu.

### <a name="resolution"></a>Řešení

K vyřešení problému použijte jedno z následujících řešení:

* Ujistěte se, že `Node` výraz vedle klíčového slova v definici konfigurace není vyhodnocení null.
* Pokud předáváte [ConfigurationData](../automation-dsc-compile.md) při kompilaci konfigurace, ujistěte se, že předáváte hodnoty, které konfigurace očekává od konfiguračních dat.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scénář: Sestava uzlu DSC se zasekne ve stavu Probíhá

### <a name="issue"></a>Problém

Výstupy agenta DSC:

```error
No instance found with given property values
```

### <a name="cause"></a>Příčina

Upgradovali jste verzi rozhraní WMF (Windows Management Framework) a došlo k poškození služby WMI (Windows Management Instrumentation).

### <a name="resolution"></a>Řešení

Postupujte podle pokynů v [DSC známé problémy a omezení](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scénář: Nelze použít pověření v konfiguraci DSC

### <a name="issue"></a>Problém

Úloha kompilace DSC pozastavena s chybou:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Příčina

Použili jste pověření v konfiguraci, ale `ConfigurationData` neposkytuje `PSDscAllowPlainTextPassword` správné nastavení true pro každou konfiguraci uzlu.

### <a name="resolution"></a>Řešení

Ujistěte se, že `ConfigurationData` předat `PSDscAllowPlainTextPassword` správné nastavit true pro každou konfiguraci uzlu, který je uveden v konfiguraci. Viz [Kompilace konfigurací DSC v konfiguraci stavu automatizace Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Scénář: "Chyba zpracování rozšíření" chyba při připojování z rozšíření DSC

### <a name="issue"></a>Problém

Při přizapisování pomocí rozšíření DSC dojde k chybě obsahující chybu:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Příčina

K této chybě obvykle dochází, když je uzlu přiřazen název konfigurace uzlu, který ve službě neexistuje.

### <a name="resolution"></a>Řešení

* Ujistěte se, že přiřazujete uzel s názvem, který přesně odpovídá názvu ve službě.
* Můžete se rozhodnout nezahrnout název konfigurace uzlu, což má za následek připojení uzlu, ale ne přiřazení konfigurace uzlu.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Scénář: "Došlo k jedné nebo více chybám" při registraci uzlu pomocí prostředí PowerShell

### <a name="issue"></a>Problém

Při registraci uzlu pomocí [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) nebo [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)se zobrazí následující chyba:

```error
One or more errors occurred.
```

### <a name="cause"></a>Příčina

K této chybě dochází při pokusu o registraci uzlu v samostatné předplatné z toho, které používá účet automatizace.

### <a name="resolution"></a>Řešení

Zacházejte s uzly s křížovým odběrem, jako by byl definovaný pro samostatný cloud nebo místní. Zaregistrujte uzel pomocí jedné z těchto možností registrace:

* Windows – [fyzické/virtuální počítače s Windows v místním prostředí nebo v cloudu jiném než Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux – [fyzické/virtuální linuxové počítače v místním prostředí nebo v jiném cloudu než v Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scénář: Chybová zpráva – zřizování se nezdařilo.

### <a name="issue"></a>Problém

Při registraci uzlu se zobrazí chyba:

```error
Provisioning has failed
```

### <a name="cause"></a>Příčina

Tato zpráva nastane, když je problém s připojením mezi uzlem a Azure.

### <a name="resolution"></a>Řešení

Zjistěte, jestli je váš uzel ve virtuální privátní síti (VPN) nebo má jiné problémy s připojením k Azure. Viz [Poradce při řešení chodu při zapisování](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scénář: Selhání s obecnou chybou při použití konfigurace v Linuxu

### <a name="issue"></a>Problém

Při použití konfigurace v Linuxu dojde k chybě obsahující chybu:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Příčina

Pokud je umístění **/tmp** nastaveno na `noexec`, aktuální verze DSC se nezdaří použít konfigurace.

### <a name="resolution"></a>Řešení

Odeberte `noexec` tuto možnost z umístění **/tmp.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scénář: Názvy konfigurace uzlu, které se překrývají, mohou mít za následek chybné vydání

### <a name="issue"></a>Problém

Pokud použijete jeden konfigurační skript ke generování více konfigurací uzlů a některé názvy konfigurace uzlů jsou podmnožiny jiných názvů, může služba kompilace nakonec přiřadit nesprávnou konfiguraci. K tomuto problému dochází pouze při použití jednoho skriptu pro generování konfigurací s konfiguračními daty na uzel a pouze v případě, že dojde k překrytí názvu na začátku řetězce. Příkladem je jeden konfigurační skript používaný ke generování konfigurací založených na datech uzlů předaných jako hodnotitelná pomocí rutin a data uzlu zahrnují servery s názvem **server** a **1server**.

### <a name="cause"></a>Příčina

Jedná se o známý problém se službou kompilace.

### <a name="resolution"></a>Řešení

Nejlepším řešením je zkompilovat místně nebo v kanálu CI/CD a nahrát soubory MOF konfigurace uzlu přímo do služby. Pokud kompilace ve službě je požadavek, další nejlepší řešení je rozdělit kompilace úlohy tak, aby nedošlo k překrytí v názvech.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scénář: Chyba časového limitu brány při nahrávání konfigurace DSC

#### <a name="issue"></a>Problém

Při nahrávání `GatewayTimeout` konfigurace DSC se zobrazí chyba. 

### <a name="cause"></a>Příčina

Konfigurace DSC, které trvat dlouhou dobu kompilace může způsobit tuto chybu.

### <a name="resolution"></a>Řešení

Konfigurace DSC můžete analyzovat rychleji explicitně zahrnutím parametru `ModuleName` pro všechna volání [Import-DSCResource.](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)

## <a name="next-steps"></a>Další kroky

Pokud problém nevidíte výše nebo nemůžete problém vyřešit, vyzkoušejte další podporu jedním z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport)se s oficiálním účtem Microsoft Azure a vylepšete tak zákaznickou zkušenost propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.