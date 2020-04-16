---
title: Poradce při potížích s konfigurací požadovaného stavu Azure Automation (DSC)
description: Tento článek obsahuje informace o řešení potíží s konfigurací požadovaného stavu (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aeffa0bb736f03403bf483b22775ef468bbcb2bd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405468"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Řešení problémů s konfigurací požadovaného stavu azure automation (DSC)

Tento článek obsahuje informace o řešení problémů s konfigurací požadovaného stavu (DSC).

## <a name="diagnosing-an-issue"></a>Diagnostika problému

Pokud máte chyby při kompilaci nebo nasazování konfigurací v konfiguraci stavu Azure, tady je několik kroků, které vám pomůžou diagnostikovat problém.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Ujistěte se, že se konfigurace úspěšně zkompiluje v místním počítači

Konfigurace stavu Azure je postavená na PowerShellu DSC. Dokumentaci k jazyku DSC a syntaxi najdete v [dokumentech PowerShell DSC .](https://docs.microsoft.com/powershell/scripting/overview)

Kompilací konfigurace DSC v místním počítači můžete zjistit a vyřešit běžné chyby, například:

   - Chybějící moduly
   - Chyby syntaxe
   - Logické chyby

### <a name="2-view-dsc-logs-on-your-node"></a>2. Zobrazení protokolů DSC na uzlu

Pokud se konfigurace úspěšně zkompiluje, ale při použití na uzel se nezdaří, můžete najít podrobné informace v protokolech DSC. Informace o tom, kde tyto protokoly najít, naleznete [v tématu Kde jsou protokoly událostí DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Modul [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) vám může pomoci při analýzě podrobných informací z protokolů DSC. Pokud se obrátíte na podporu, vyžadují tyto protokoly k diagnostice problému.

Modul xDscDiagnostics můžete nainstalovat do místního počítače podle pokynů v [části Instalace modulu stabilní verze](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Chcete-li nainstalovat modul xDscDiagnostics do počítače Azure, použijte [příkaz Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Můžete také použít možnost **Spustit příkaz** z portálu podle kroků uvedených v [spuštění skriptů prostředí PowerShell v virtuálním počítači systému Windows s příkazem Spustit](../../virtual-machines/windows/run-command.md).

Informace o použití xDscDiagnostics naleznete [v tématu Použití xDscDiagnostics k analýze protokolů DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Viz také [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Ujistěte se, že uzly a pracovní prostor automatizace mají požadované moduly

DSC závisí na modulech nainstalovaných v uzlu. Při použití konfigurace stavu automatizace Azure importujte všechny požadované moduly do svého účtu Automation pomocí kroků uvedených v [části Import Modules](../shared-resources/modules.md#importing-modules). Konfigurace mohou mít také závislost na konkrétních verzích modulů. Další informace naleznete [v tématu Poradce při potížích s moduly](shared-resources.md#modules).

## <a name="common-errors-when-working-with-dsc"></a>Běžné chyby při práci s DSC

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scénář: Konfiguraci se speciálními znaky nelze z portálu odstranit.

#### <a name="issue"></a>Problém

Při pokusu o odstranění konfigurace DSC z portálu se zobrazí následující chyba:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Příčina

Tato chyba je dočasný problém, který je plánován k vyřešení.

#### <a name="resolution"></a>Řešení

* K odstranění konfigurace použijte rutinu Az "Remove-AzAutomationDscConfiguration".
* Dokumentace pro tuto rutinu ještě nebyla aktualizována.  Do té doby naleznete v dokumentaci k modulu AzureRM.
  * [Odebrat konfiguraci AzureRmAutomationDSC](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scénář: Nepodařilo se zaregistrovat agenta Dsc.

#### <a name="issue"></a>Problém

Při pokusu `Set-DscLocalConfigurationManager` o spuštění nebo jiné rutiny DSC se zobrazí chyba:

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

#### <a name="cause"></a>Příčina

Tato chyba je obvykle způsobena bránou firewall, počítač je za proxy serverem nebo jiné chyby sítě.

#### <a name="resolution"></a>Řešení

Ověřte, zda váš počítač má přístup ke správným koncovým bodům pro Azure Automation DSC a zkuste to znovu. Seznam potřebných portů a adres naleznete v [tématu plánování sítě](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scénář: Zprávy o stavu vrátí kód odpovědi "Neoprávněný"

#### <a name="issue"></a>Problém

Při registraci uzlu s konfigurací stavu (DSC) se zobrazí jedna z následujících chybových zpráv:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Příčina

Tento problém je způsoben chybný certifikát nebo vypršela jeho platnost.  Další informace naleznete v [tématu Vypršení platnosti certifikátu a jeho opětovné registrace](../automation-dsc-onboarding.md#re-register-a-node).

Tento problém může být také způsoben konfigurací proxy neumožňuje přístup k ***.azure-automation.net**. Další informace naleznete v [tématu Konfigurace privátních sítí](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Řešení

Postupujte podle následujících kroků a znovu zaregistrujte uzel DSC, který selhal.

Nejprve zrušit registraci uzlu pomocí následujících kroků.

1. Z portálu Azure v části Účty **domácí** -> **automatizace**-> {Váš účet automatizace} -> **konfigurace stavu (DSC)**
2. Klikněte na "Uzly" a klikněte na uzel, který má potíže.
3. Chcete-li zrušit registraci uzlu, klepněte na tlačítko Zrušit registraci.

Za druhé odinstalujte rozšíření DSC z uzlu.

1. Z portálu Azure v části **Domácí** -> **virtuální počítač** -> {Failing node} -> **Rozšíření**
2. Klikněte na "Microsoft.Powershell.DSC".
3. Chcete-li odinstalovat rozšíření PowerShell DSC, klepněte na tlačítko Odinstalovat.

Za třetí, odeberte všechny chybné nebo vypršela platnost certifikáty z uzlu.

Na uzlu selhání z výzvy se zvýšenými oprávněními prostředí Powershell spusťte následující:

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

Nakonec znovu zaregistrujte uzel selhání pomocí následujících kroků.

1. Z portálu Azure v části Účty **domácí** -> **automatizace** -> {Váš účet automatizace} -> **konfigurace stavu (DSC)**
2. Klikněte na "Uzly".
3. Klikněte na tlačítko "Přidat".
4. Vyberte uzel selhání.
5. Klikněte na tlačítko "Připojit" a vyberte požadované možnosti.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scénář: Uzel je ve stavu selhání s chybou "Nebyl nalezen"

#### <a name="issue"></a>Problém

Uzel má sestavu se stavem **Selhání** a obsahující chybu:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Příčina

K této chybě obvykle dochází, když je uzel přiřazen názvu konfigurace (například ABC) namísto názvu konfigurace uzlu (například ABC. webserver).

#### <a name="resolution"></a>Řešení

* Ujistěte se, že přiřazujete uzel s názvem konfigurace uzlu a nikoli "názvem konfigurace".
* Konfiguraci uzlu můžete přiřadit k uzlu pomocí portálu Azure portal nebo pomocí rutiny Prostředí PowerShell.

  * Chcete-li přiřadit konfiguraci uzlu k uzlu pomocí portálu Azure, otevřete stránku **Uzly DSC,** vyberte uzel a klikněte na tlačítko **Přiřadit konfigurační** uzel.
  * Chcete-li přiřadit konfiguraci uzlu pomocí rutiny prostředí PowerShell, použijte rutinu **Set-AzureRmAutomationDscNode**

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>Scénář: Při kompilaci konfigurace nebyly vytvořeny žádné konfigurace uzlů (soubory MOF).

#### <a name="issue"></a>Problém

Úloha kompilace DSC se pozastaví s chybou:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Příčina

Pokud se výraz za klíčovým slovem **Uzel** v `$null`konfiguraci DSC vyhodnotí na , nebudou vytvořeny žádné konfigurace uzlů.

#### <a name="resolution"></a>Řešení

Problém vyřeší některou z následujících řešení:

* Ujistěte se, že výraz vedle klíčového slova **Uzel** v definici konfigurace není vyhodnocení na $null.
* Pokud předáváte ConfigurationData při kompilaci konfigurace, ujistěte se, že předáváte očekávané hodnoty, které konfigurace vyžaduje od [ConfigurationData](../automation-dsc-compile.md).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>Scénář: Sestava uzlu DSC se zasekne ve stavu "probíhá"

#### <a name="issue"></a>Problém

Výstupy agenta DSC:

```error
No instance found with given property values
```

#### <a name="cause"></a>Příčina

Upgradovali jste verzi WMF a poškodili jste systém WMI.

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, postupujte podle pokynů v článku [známé problémy a omezení DSC.](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scénář: Nelze použít pověření v konfiguraci DSC

#### <a name="issue"></a>Problém

Úloha kompilace DSC byla pozastavena s chybou:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Příčina

Použili jste pověření v konfiguraci, ale **neposkytli** správné ConfigurationData nastavit **PSDscAllowPlainTextPassword** na true pro každou konfiguraci uzlu.

#### <a name="resolution"></a>Řešení

* Ujistěte se, že předat správné **ConfigurationData** nastavit **PSDscAllowPlainTextPassword** true pro každou konfiguraci uzlu, který je uveden v konfiguraci. Další informace naleznete [v tématu Kompilace konfigurací DSC v konfiguraci stavu automatizace Azure](../automation-dsc-compile.md).

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>Scénář: Registrace z rozšíření dsc, chyba "Rozšíření zpracování selhání"

#### <a name="issue"></a>Problém

Při přizapisování pomocí rozšíření DSC dojde k chybě obsahující chybu:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Příčina

K této chybě obvykle dochází, když je uzlu přiřazen název konfigurace uzlu, který ve službě neexistuje.

#### <a name="resolution"></a>Řešení

* Ujistěte se, že přiřazujete uzel s názvem konfigurace uzlu, který přesně odpovídá názvu ve službě.
* Můžete se rozhodnout nezahrnout název konfigurace uzlu, což bude mít za následek připojení uzlu, ale ne přiřazení konfigurace uzlu

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>Scénář: Registrace uzlu s Prostředím PowerShell vrátí chybu "Došlo k jedné nebo více chybám"

#### <a name="issue"></a>Problém

Při registraci uzlu `Register-AzAutomationDSCNode` pomocí `Register-AzureRMAutomationDSCNode`nebo , se zobrazí následující chyba.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Příčina

K této chybě dochází při pokusu o registraci uzlu, který žije v samostatné masce než účet automatizace.

#### <a name="resolution"></a>Řešení

Zacházejte s uzly s křížovým odběrem, jako by se naněm nachází v samostatném cloudu nebo místně.

Chcete-li uzel zaregistrovat, postupujte podle následujících kroků.

* Windows – [fyzické/virtuální počítače s Windows v místním prostředí nebo v cloudu jiném než Azure/AWS](../automation-dsc-onboarding.md#onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure).
* Linux – [fyzické/virtuální linuxové počítače v místním prostředí nebo v jiném cloudu než v Azure](../automation-dsc-onboarding.md#onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scénář: Chybová zpráva – zřizování se nezdařilo.

#### <a name="issue"></a>Problém

Při registraci uzlu se zobrazí chyba:

```error
Provisioning has failed
```

#### <a name="cause"></a>Příčina

Tato zpráva nastane, když je problém s připojením mezi uzlem a Azure.

#### <a name="resolution"></a>Řešení

Zjistěte, jestli je váš uzel v privátní virtuální síti nebo jestli má jiné problémy s připojením k Azure.

Další informace naleznete [v tématu Poradce při řešeních při zapisování](onboarding.md).

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>Scénář: Použití konfigurace v Linuxu, dojde k chybě s obecnou chybou

#### <a name="issue"></a>Problém

Při použití konfigurace v Linuxu dojde k chybě obsahující chybu:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Příčina

Zákazníci zjistili, `/tmp` že pokud `noexec`je umístění nastaveno na , aktuální verze DSC se nezdaří použít konfigurace.

#### <a name="resolution"></a>Řešení

* Odeberte `noexec` možnost `/tmp` z umístění.

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scénář: Názvy konfigurace uzlu, které se překrývají, mohou mít za následek chybné vydání

#### <a name="issue"></a>Problém

Pokud jeden konfigurační skript se používá ke generování více konfigurací uzlů a některé konfigurace uzlů mají název, který je podmnožinou jiných, problém ve službě kompilace může mít za následek přiřazení nesprávné konfigurace.  K tomu dochází pouze při použití jednoho skriptu ke generování konfigurací s konfiguračními daty na uzel a pouze v případě, že k překrytí názvu dochází na začátku řetězce.

Například pokud jeden konfigurační skript se používá ke generování konfigurace na základě dat uzlu předaných jako hashtable pomocí rutiny a data uzlu obsahuje server s názvem "server" a "1server".

#### <a name="cause"></a>Příčina

Známý problém se službou kompilace.

#### <a name="resolution"></a>Řešení

Nejlepším řešením by bylo zkompilovat místně nebo v kanálu CI/CD a nahrát soubory MOF přímo do služby.  Pokud kompilace ve službě je požadavek, další nejlepší řešení by bylo rozdělit kompilace úlohy tak, aby nedošlo k překrytí v názvech.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scénář: Chyba časového limitu brány při nahrávání konfigurace DSC

#### <a name="issue"></a>Problém

Při nahrávání `GatewayTimeout` konfigurace DSC se zobrazí chyba. 

#### <a name="cause"></a>Příčina

Konfigurace DSC, které trvat dlouhou dobu kompilace může způsobit tuto chybu.

#### <a name="resolution"></a>Řešení

Konfigurace DSC můžete analyzovat rychleji explicitně včetně `ModuleName` parametrpro `Import-DscResource` všechna volání. Další informace naleznete [v tématu Using Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1).

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s – oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete nastolet incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
