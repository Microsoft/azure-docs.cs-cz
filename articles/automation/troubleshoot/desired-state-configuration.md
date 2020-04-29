---
title: Řešení potíží s konfigurací stavu Azure Automation (DSC)
description: Tento článek poskytuje informace o řešení potíží s konfigurací stavu Azure Automation (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679310"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Řešení potíží s konfigurací stavu Azure Automation (DSC)

Tento článek poskytuje informace o řešení problémů, které vznikají při kompilování nebo nasazování konfigurací v konfiguraci stavu Azure Automation (DSC).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-an-issue"></a>Diagnostikování problému

Když obdržíte chybu kompilace nebo nasazení pro konfiguraci, je zde několik kroků, které vám pomohou problém diagnostikovat.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Ujistěte se, že se konfigurace úspěšně zkompiluje na místním počítači.

Konfigurace stavu Azure Automation (DSC) je postavená na konfiguraci požadovaného stavu (DSC) prostředí PowerShell. Dokumentaci k jazyku DSC a syntaxi najdete v dokumentaci k [PowerShellu DSC](https://docs.microsoft.com/powershell/scripting/overview).

Zkompilováním konfigurace DSC na místním počítači můžete zjišťovat a řešit běžné chyby, jako například:

   - Chybějící moduly
   - Chyby syntaxe
   - Logické chyby

### <a name="2-view-dsc-logs-on-your-node"></a>2. zobrazení protokolů DSC v uzlu

Pokud se konfigurace úspěšně zkompiluje, ale při použití v uzlu dojde k chybě, můžete najít podrobné informace v protokolech DSC. Informace o tom, kde najít tyto protokoly, najdete v tématu [kde jsou protokoly událostí DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Modul [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) vám může pomoci při analýze podrobných informací z protokolů DSC. Pokud se obrátíte na podporu, vyžadují tyto protokoly k diagnostice vašeho problému.

`xDscDiagnostics` Modul můžete nainstalovat do místního počítače podle pokynů uvedených v tématu [instalace stabilní verze modulu](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Pokud chcete `xDscDiagnostics` modul nainstalovat do počítače Azure, použijte [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). V Azure Portal můžete použít také možnost **Spustit příkaz** , a to podle kroků uvedených v části [spuštění skriptů POWERSHELLu na virtuálním počítači s Windows pomocí příkazu Spustit](../../virtual-machines/windows/run-command.md).

Informace o použití **xDscDiagnostics**najdete v tématu věnovaném [použití xDscDiagnostics k analýze protokolů DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Viz také [rutiny xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Ujistěte se, že uzly a pracovní prostor služby Automation mají požadované moduly.

DSC závisí na modulech nainstalovaných v uzlu. Při použití konfigurace stavu Azure Automation importujte do svého účtu Automation všechny požadované moduly pomocí kroků v části [Import modulů](../shared-resources/modules.md#importing-modules). Konfigurace můžou mít taky závislost na konkrétních verzích modulů. Další informace najdete v tématu [řešení potíží s moduly](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scénář: na portálu nejde odstranit konfiguraci se speciálními znaky.

### <a name="issue"></a>Problém

Při pokusu o odstranění konfigurace DSC z portálu se zobrazí následující chyba:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Příčina

Tato chyba je dočasný problém, který se plánuje vyřešit.

### <a name="resolution"></a>Řešení

K odstranění konfigurace použijte rutinu [removehttps://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 -AzAutomationDscConfiguration] (.

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scénář: registrace agenta DSC se nezdařila

### <a name="issue"></a>Problém

Při [Nastavení rutiny Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) nebo jiné RUTINy DSC se zobrazí chyba:

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

Ověřte, že váš počítač má přístup ke správným koncovým bodům pro DSC, a zkuste to znovu. Seznam potřebných portů a adres najdete v tématu [Plánování sítě](../automation-dsc-overview.md#network-planning) .

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scénář: zprávy o stavu vrátí neautorizovaný kód odpovědi

### <a name="issue"></a>Problém

Při registraci uzlu s konfigurací stavu Azure Automation se zobrazí jedna z následujících chybových zpráv:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Příčina

Příčinou tohoto problému je chybný nebo prošlý certifikát. Viz [vypršení platnosti certifikátu a jeho registrace](../automation-dsc-onboarding.md#re-registering-a-node).

Tento problém může být taky způsoben konfigurací proxy serveru, který neumožňuje přístup k ***. Azure-Automation.NET**. Další informace najdete v tématu [Konfigurace privátních sítí](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Řešení

Pomocí níže uvedeného postupu znovu zaregistrujete neúspěšný uzel DSC.

Krok 1 – zrušení registrace uzlu

1. V Azure Portal přejděte na účty **služby Home** -> **Automation** – > (účet Automation) – > **Konfigurace stavu (DSC)**.
2. Vyberte **uzly**a klikněte na uzel, který má potíže.
3. Kliknutím na **zrušit registraci** uzel zrušte.

Krok 2 – odinstalujte rozšíření DSC z uzlu.

1. V Azure Portal přejděte na **domácí** -> **virtuální počítač** – > (selhání uzlu) – **rozšíření**>.
2. Vyberte **Microsoft. PowerShell. DSC**, rozšíření PowerShell DSC.
3. Kliknutím na **odinstalovat** odinstalujte rozšíření.

Krok 3 – odebrání všech chybných nebo neplatných certifikátů z uzlu

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

Krok 4 – opětovné zaregistrování neúspěšného uzlu.

1. V Azure Portal přejděte na účty služby **Home** -> **Automation** – > (účet Automation) – **Konfigurace > stavu (DSC)** .
2. Vyberte **uzly**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Vyberte uzel, který selhal.
5. Klikněte na **připojit** a vyberte požadované možnosti.

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
* Můžete přiřadit konfiguraci uzlu uzlu pomocí Azure Portal nebo pomocí rutiny prostředí PowerShell.

  * V Azure Portal přejděte na účty služby **Home** -> **Automation** – > (účet Automation) – > **Konfigurace stavu (DSC)**, vyberte uzel a klikněte na **přiřadit konfiguraci uzlu**.
  * Použijte rutinu [set-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scénář: při kompilaci konfigurace nevytvořily se žádné konfigurace uzlů (soubory MOF).

### <a name="issue"></a>Problém

Úloha kompilace DSC se pozastaví s chybou:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Příčina

Pokud výraz následující za `Node` klíčovým slovem v konfiguraci DSC vyhodnocuje `$null`, nebudou vytvořeny žádné konfigurace uzlů.

### <a name="resolution"></a>Řešení

K odstranění problému použijte jedno z následujících řešení:

* Ujistěte se, že výraz vedle `Node` klíčového slova v definici konfigurace není vyhodnocen na hodnotu null.
* Pokud předáváte [ConfigurationData](../automation-dsc-compile.md) při kompilování konfigurace, ujistěte se, že předáváte hodnoty, které konfigurace z konfiguračních dat očekává.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scénář: sestava uzlu DSC se zablokuje ve stavu Probíhá zpracování.

### <a name="issue"></a>Problém

Výstupy agenta DSC:

```error
No instance found with given property values
```

### <a name="cause"></a>Příčina

Upgradovali jste verzi Windows Management Frameworku (WMF) a měli jste poškozenou rozhraní WMI (Windows Management Instrumentation) (WMI).

### <a name="resolution"></a>Řešení

Postupujte podle pokynů v [DSC známé problémy a omezení](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scénář: nejde použít přihlašovací údaje v konfiguraci DSC.

### <a name="issue"></a>Problém

Úloha kompilace DSC se pozastavila s chybou:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Příčina

V konfiguraci jste použili přihlašovací údaje, ale nezadali jste pro `ConfigurationData` každou konfiguraci `PSDscAllowPlainTextPassword` uzlu správný stav pro nastavení na hodnotu true.

### <a name="resolution"></a>Řešení

Nezapomeňte předat správné `ConfigurationData` nastavení `PSDscAllowPlainTextPassword` na hodnotu true pro každou konfiguraci uzlu uvedenou v konfiguraci. Viz [kompilace konfigurací DSC v konfiguraci stavu Azure Automation](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Scénář: Chyba při zpracování rozšíření při připojování z rozšíření DSC

### <a name="issue"></a>Problém

Při připojování pomocí rozšíření DSC dojde k chybě, která obsahuje chybu:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Příčina

K této chybě obvykle dochází v případě, že se k uzlu přiřadí název konfigurace uzlu, který ve službě neexistuje.

### <a name="resolution"></a>Řešení

* Ujistěte se, že přiřazujete uzel s názvem, který přesně odpovídá názvu ve službě.
* Můžete zvolit, že nebudete zahrnovat název konfigurace uzlu, který má za následek připojování uzlu, ale nepřiřazuje konfiguraci uzlu.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Scénář: při registraci uzlu pomocí prostředí PowerShell došlo k chybě jedna nebo víc chyb.

### <a name="issue"></a>Problém

Při registraci uzlu pomocí [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) nebo [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)se zobrazí následující chyba:

```error
One or more errors occurred.
```

### <a name="cause"></a>Příčina

K této chybě dochází, když se pokusíte zaregistrovat uzel v samostatném předplatném, které používá účet Automation.

### <a name="resolution"></a>Řešení

Považovat uzel mezi předplatnými, jako by byl definován pro samostatný Cloud nebo místně. Zaregistrujte uzel pomocí některé z těchto možností připojování:

* Windows – [fyzické nebo virtuální počítače s Windows v místním prostředí nebo v jiném cloudu než Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux – [fyzické nebo virtuální počítače s Linuxem v místním prostředí nebo v jiném cloudu než Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scénář: chybová zpráva – "zřizování nebylo úspěšné"

### <a name="issue"></a>Problém

Při registraci uzlu se zobrazí chyba:

```error
Provisioning has failed
```

### <a name="cause"></a>Příčina

Tato zpráva se zobrazí, když dojde k potížím s připojením mezi uzlem a Azure.

### <a name="resolution"></a>Řešení

Zjistěte, jestli je váš uzel ve virtuální privátní síti (VPN) nebo jestli má jiné problémy s připojením k Azure. Přečtěte si článek [řešení chyb při připojování řešení](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scénář: při použití konfigurace v systému Linux došlo k chybě s obecnou chybou

### <a name="issue"></a>Problém

Při použití konfigurace v systému Linux dojde k chybě, která obsahuje chybu:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Příčina

Pokud je umístění **adresáře/TMP** nastaveno na `noexec`, aktuální verze DSC nedokáže použít konfigurace.

### <a name="resolution"></a>Řešení

Odeberte `noexec` možnost z umístění **adresáře/TMP** .

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scénář: názvy konfigurací uzlů, které se překrývají, můžou mít za následek chybnou verzi.

### <a name="issue"></a>Problém

Při použití jediného konfiguračního skriptu pro generování více konfigurací uzlů a některé názvy konfigurace uzlů jsou podmnožinami jiných názvů, může služba kompilace ukončit přiřazení nesprávné konfigurace. K tomuto problému dochází pouze při použití jediného skriptu ke generování konfigurací s konfiguračními daty na uzel a pouze v případě, že dojde k překrytí názvu na začátku řetězce. Příkladem je jediný konfigurační skript, který se používá ke generování konfigurací založených na datech uzlu předaných jako zatřiďovací tabulka pomocí rutin a data uzlu zahrnuje servery s názvem **Server** a **1server**.

### <a name="cause"></a>Příčina

Jedná se o známý problém se službou kompilace.

### <a name="resolution"></a>Řešení

Nejlepším řešením je kompilovat místně nebo v kanálu CI/CD a nahrát soubory MOF konfigurace uzlů přímo do služby. Pokud je kompilace ve službě požadavkem, další nejlepší alternativní řešení je rozdělit úlohy kompilace tak, aby se v názvech překrývaly.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scénář: Chyba časového limitu brány při nahrávání konfigurace DSC

#### <a name="issue"></a>Problém

Při nahrávání konfigurace `GatewayTimeout` DSC se zobrazí chyba. 

### <a name="cause"></a>Příčina

Tuto chybu může způsobit konfigurace DSC, které mohou být zkompilovány dlouho.

### <a name="resolution"></a>Řešení

Konfigurace DSC můžete urychlit tak, že explicitně zahrnete `ModuleName` parametr pro všechna volání [Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) .

## <a name="next-steps"></a>Další kroky

Pokud nevidíte výše uvedený problém nebo nemůžete problém vyřešit, zkuste pro další podporu použít jeden z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se [@AzureSupport](https://twitter.com/azuresupport)k, oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.