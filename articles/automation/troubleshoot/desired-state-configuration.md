---
title: Řešení potíží s konfigurací Azure Automation stavů
description: V tomto článku se dozvíte, jak řešit problémy s konfigurací stavu Azure Automation a vyřešit je.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.openlocfilehash: e6caf3fed708e89b55a88719ca5358f6174c2ac8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896524"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Řešení potíží s konfigurací Azure Automation stavů

Tento článek poskytuje informace o řešení potíží a řešení problémů, které vznikají při kompilaci nebo nasazování konfigurací v konfiguraci stavu Azure Automation. Obecné informace o funkci Konfigurace stavu najdete v tématu [Přehled konfigurace stavu Azure Automation](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Diagnostika problému

Když obdržíte chybu kompilace nebo nasazení pro konfiguraci, je zde několik kroků, které vám pomohou problém diagnostikovat.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Ujistěte se, že se konfigurace úspěšně zkompiluje na místním počítači.

Konfigurace stavu Azure Automation je postavená na konfiguraci požadovaného stavu prostředí PowerShell (DSC). Dokumentaci k jazyku DSC a syntaxi najdete v dokumentaci k [PowerShellu DSC](/powershell/scripting/overview).

Zkompilováním konfigurace DSC na místním počítači můžete zjišťovat a řešit běžné chyby, jako například:

   - Chybějící moduly.
   - Chyby syntaxe.
   - Logické chyby.

### <a name="2-view-dsc-logs-on-your-node"></a>2. zobrazení protokolů DSC v uzlu

Pokud se konfigurace úspěšně zkompiluje, ale při použití v uzlu dojde k chybě, můžete najít podrobné informace v protokolech DSC. Informace o tom, kde najít tyto protokoly, najdete v tématu [kde jsou protokoly událostí DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Modul [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) vám může pomoci při analýze podrobných informací z protokolů DSC. Pokud se obrátíte na podporu, vyžadují tyto protokoly k diagnostice vašeho problému.

Modul můžete nainstalovat `xDscDiagnostics` na místní počítač podle pokynů v části [Instalace modulu stabilní verze](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Pokud chcete modul nainstalovat do `xDscDiagnostics` počítače Azure, použijte [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand). V Azure Portal můžete použít taky možnost **Spustit příkaz** pomocí postupu v části [spuštění skriptů POWERSHELLu na virtuálním počítači s Windows pomocí příkazu Spustit](../../virtual-machines/windows/run-command.md).

Informace o použití **xDscDiagnostics** najdete v tématu věnovaném [použití xDscDiagnostics k analýze protokolů DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Viz také [rutiny xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Ujistěte se, že uzly a pracovní prostor služby Automation mají požadované moduly.

DSC závisí na modulech nainstalovaných v uzlu. Když použijete konfiguraci stavu Azure Automation, naimportujte všechny požadované moduly do svého účtu Automation podle kroků v části [Import modulů](../shared-resources/modules.md#import-modules). Konfigurace můžou mít taky závislost na konkrétních verzích modulů. Další informace najdete v tématu [řešení potíží s moduly](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scénář: na portálu nejde odstranit konfiguraci se speciálními znaky.

### <a name="issue"></a>Problém

Při pokusu o odstranění konfigurace DSC z portálu se zobrazí následující chyba:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Příčina

Tato chyba je dočasným problémem, který se plánuje vyřešit.

### <a name="resolution"></a>Řešení

Ke konfiguraci odstraňte pomocí rutiny [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) .

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Scénář: Nepodařilo se zaregistrovat agenta DSC.

### <a name="issue"></a>Problém

Při [Nastavení rutiny Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) nebo jiné RUTINy DSC se zobrazí chyba:

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

Tato chyba je obvykle způsobena bránou firewall, počítač je za proxy server nebo jinými chybami v síti.

### <a name="resolution"></a>Řešení

Ověřte, že váš počítač má přístup ke správným koncovým bodům pro DSC, a zkuste to znovu. Seznam portů a adres, které potřebujete, najdete v tématu [Plánování sítě](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Scénář: zprávy o stavu vracejí kód odpovědi neoprávněný

### <a name="issue"></a>Problém

Při registraci uzlu s konfigurací stavu Azure Automation se zobrazí jedna z následujících chybových zpráv:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Příčina

Příčinou tohoto problému je chybný nebo prošlý certifikát. Viz [opětovné registrace uzlu](../automation-dsc-onboarding.md#re-register-a-node).

Tento problém může být taky způsoben konfigurací proxy serveru, který neumožňuje přístup k ***. Azure-Automation.NET**. Další informace najdete v tématu [Konfigurace privátních sítí](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Řešení

Pomocí následujícího postupu můžete znovu zaregistrovat uzel DSC, který selhává.

#### <a name="step-1-unregister-the-node"></a>Krok 1: zrušení registrace uzlu

1. V Azure Portal přejít na účty služby **Home**  >  **Automation** > (účet Automation) > **Konfigurace stavu (DSC)**.
1. Vyberte **uzly** a vyberte uzel, který má potíže.
1. Chcete-li zrušit registraci uzlu, vyberte možnost zrušit **registraci** .

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Krok 2: Odinstalace rozšíření DSC z uzlu

1. V Azure Portal přejít na **domovskou**  >  **virtuální počítač** > (selhání uzlu) > **rozšíření**.
1. Vyberte **Microsoft. PowerShell. DSC**, rozšíření PowerShell DSC.
1. Vyberte **odinstalovat** pro odinstalaci rozšíření.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Krok 3: odebrání všech chybných nebo neplatných certifikátů z uzlu

V uzlu selhání z příkazového řádku PowerShellu se zvýšenými oprávněními spusťte tyto příkazy:

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

#### <a name="step-4-reregister-the-failing-node"></a>Krok 4: opětovná registrace uzlu, který selhal

1. V Azure Portal přejít na účty služby **Home**  >  **Automation** > (účet Automation) > **Konfigurace stavu (DSC)**.
1. Vyberte **uzly**.
1. Vyberte **Přidat**.
1. Vyberte uzel, který selhal.
1. Vyberte **připojit** a vyberte požadované možnosti.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scénář: uzel je v neúspěšném stavu s chybou "Nenalezeno".

### <a name="issue"></a>Problém

Uzel má sestavu se stavem selhání a obsahuje chybu:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Příčina

K této chybě obvykle dochází v případě, že je uzel přiřazen k názvu konfigurace, například **ABC**, namísto názvu konfigurace uzlu (soubor MOF), například **ABC. WebServer**.

### <a name="resolution"></a>Řešení

* Ujistěte se, že přiřazujete uzel s názvem konfigurace uzlu, a ne s názvem konfigurace.
* Konfiguraci uzlu můžete přiřadit uzlu pomocí Azure Portal nebo pomocí rutiny prostředí PowerShell.

  * V Azure Portal přejít na účty služby **Home**  >  **Automation** > (účet Automation) > **Konfigurace stavu (DSC)**. Pak vyberte uzel a vyberte **přiřadit konfiguraci uzlu**.
  * Použijte rutinu [set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scénář: při kompilaci konfigurace nevytvořily se žádné konfigurace uzlů (soubory MOF).

### <a name="issue"></a>Problém

Úloha kompilace DSC se pozastaví s chybou:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Příčina

Pokud výraz následující za `Node` klíčovým slovem v konfiguraci DSC vyhodnocuje `$null` , nebudou vytvořeny žádné konfigurace uzlů.

### <a name="resolution"></a>Řešení

K odstranění problému použijte jedno z následujících řešení:

* Ujistěte se, že výraz vedle `Node` klíčového slova v definici konfigurace není vyhodnocen na hodnotu null.
* Pokud při kompilaci konfigurace předáváte [ConfigurationData](../automation-dsc-compile.md) , ujistěte se, že předáváte hodnoty, které konfigurace očekává od konfiguračních dat.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scénář: sestava uzlu DSC se zablokuje ve stavu Probíhá zpracování.

### <a name="issue"></a>Problém

Výstupy agenta DSC:

```error
No instance found with given property values
```

### <a name="cause"></a>Příčina

Upgradovali jste verzi Windows Management Frameworku (WMF) a měli jste poškozenou rozhraní WMI (Windows Management Instrumentation) (WMI).

### <a name="resolution"></a>Řešení

Postupujte podle pokynů v části [DSC známé problémy a omezení](/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scénář: nejde použít přihlašovací údaje v konfiguraci DSC.

### <a name="issue"></a>Problém

Úloha kompilace DSC se pozastavila s chybou:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Příčina

V konfiguraci jste použili přihlašovací údaje, ale nezadali jste pro `ConfigurationData` každou konfiguraci uzlu správný stav pro nastavení `PSDscAllowPlainTextPassword` na hodnotu true.

### <a name="resolution"></a>Řešení

Nezapomeňte předat správné `ConfigurationData` nastavení na `PSDscAllowPlainTextPassword` hodnotu true pro každou konfiguraci uzlu uvedenou v konfiguraci. Viz [kompilace konfigurací DSC v konfiguraci stavu Azure Automation](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Scénář: Chyba při zpracování rozšíření při povolování počítače z rozšíření DSC

### <a name="issue"></a>Problém

Když počítač povolíte pomocí rozšíření DSC, dojde k chybě, která obsahuje chybu:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Příčina

K této chybě obvykle dochází v případě, že se k uzlu přiřadí název konfigurace uzlu, který ve službě neexistuje.

### <a name="resolution"></a>Řešení

* Ujistěte se, že přiřazujete uzel s názvem, který přesně odpovídá názvu ve službě.
* Můžete zvolit, že nebudete zahrnovat název konfigurace uzlu, což má za následek povolení uzlu, ale nepřiřazuje konfiguraci uzlu.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Scénář: při registraci uzlu pomocí prostředí PowerShell došlo k chybě jedna nebo víc chyb.

### <a name="issue"></a>Problém

Při registraci uzlu pomocí [registru Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) nebo [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)se zobrazí následující chyba:

```error
One or more errors occurred.
```

### <a name="cause"></a>Příčina

K této chybě dochází, když se pokusíte zaregistrovat uzel v samostatném předplatném, které používá účet Automation.

### <a name="resolution"></a>Řešení

Považovat uzel mezi předplatnými, jako by byl definován pro samostatný Cloud nebo místně. Zaregistrujte uzel pomocí jedné z těchto možností pro povolení počítačů:

* Windows: [fyzické nebo virtuální počítače s Windows v místním prostředí nebo v jiném cloudu než Azure/AWS](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [fyzické nebo virtuální počítače se systémem Linux v místním prostředí nebo v jiném cloudu než Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Scénář: chybová zpráva zřizování se nezdařilo.

### <a name="issue"></a>Problém

Při registraci uzlu se zobrazí chyba:

```error
Provisioning has failed
```

### <a name="cause"></a>Příčina

Tato zpráva se zobrazí, když dojde k potížím s připojením mezi uzlem a Azure.

### <a name="resolution"></a>Řešení

Zjistěte, jestli je váš uzel ve virtuální privátní síti (VPN) nebo jestli má jiné problémy s připojením k Azure. Přečtěte si téma [řešení potíží s nasazením funkcí](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scénář: při použití konfigurace v systému Linux došlo k chybě s obecnou chybou

### <a name="issue"></a>Problém

Při použití konfigurace v systému Linux dojde k chybě, která obsahuje chybu:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Příčina

Pokud je umístění **adresáře/TMP** nastaveno na `noexec` , aktuální verze DSC nedokáže použít konfigurace.

### <a name="resolution"></a>Řešení

Odeberte `noexec` možnost z umístění **adresáře/TMP** .

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Scénář: názvy konfigurací uzlů, které se překrývají, můžou mít za následek špatnou verzi.

### <a name="issue"></a>Problém

Při použití jediného konfiguračního skriptu pro generování více konfigurací uzlů a některé názvy konfigurace uzlů jsou podmnožinami jiných názvů, může služba kompilace ukončit přiřazení nesprávné konfigurace. K tomuto problému dochází pouze v případě, že použijete jeden skript k vygenerování konfigurací s konfiguračními daty na uzel a pouze v případě, že se název překrývá na začátku řetězce. Příkladem je jediný konfigurační skript, který se používá ke generování konfigurací založených na datech uzlu předaných jako zatřiďovací tabulka pomocí rutin a data uzlu zahrnuje servery s názvem **Server** a **1server**.

### <a name="cause"></a>Příčina

Jedná se o známý problém se službou kompilace.

### <a name="resolution"></a>Řešení

Nejlepším řešením je kompilovat místně nebo v kanálu CI/CD a nahrát soubory MOF konfigurace uzlů přímo do služby. Pokud je kompilace ve službě požadavkem, další nejlepší alternativní řešení je rozdělit úlohy kompilace tak, aby se v názvech překrývaly.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scénář: Chyba časového limitu brány při nahrávání konfigurace DSC

#### <a name="issue"></a>Problém

`GatewayTimeout`Při nahrávání konfigurace DSC se zobrazí chyba. 

### <a name="cause"></a>Příčina

Tuto chybu může způsobit konfigurace DSC, které mohou být zkompilovány dlouho.

### <a name="resolution"></a>Řešení

Konfigurace DSC můžete urychlit tak, že explicitně zahrnete `ModuleName` parametr pro všechna volání [Import-DSCResource](/powershell/scripting/dsc/configurations/import-dscresource) .

## <a name="next-steps"></a>Další kroky

Pokud tady nevidíte svůj problém nebo nemůžete problém vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky. Podpora Azure spojuje komunitu Azure s odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.
