---
title: Řešení potíží s Azure Automation konfigurace požadovaného stavu (DSC)
description: Tento článek poskytuje informace o řešení potíží s konfigurací požadovaného stavu (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a45ed90b2b2c4a3a4f8eb11c4618c11e6d66761
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849356"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Řešení potíží s konfigurací požadovaného stavu (DSC)

Tento článek poskytuje informace o řešení problémů s požadovanou konfigurací stavu (DSC).

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>Postup řešení potíží s konfigurací požadovaného stavu (DSC)

Pokud máte chyby při kompilaci nebo nasazování konfigurací v konfiguraci stavu Azure, najdete tady několik kroků, které vám pomůžou problém diagnostikovat.

1. **Ujistěte se, že se konfigurace úspěšně zkompiluje na místním počítači:**  Konfigurace stavu Azure je postavená na PowerShell DSC. Dokumentaci k jazyku DSC a syntaxi najdete v dokumentaci k [PowerShellu DSC](https://docs.microsoft.com/powershell/scripting/overview).

   Zkompilováním konfigurace DSC na místním počítači můžete zjišťovat a řešit běžné chyby, jako například:

   - **Chybějící moduly**
   - **Chyby syntaxe**
   - **Logické chyby**

2. **Zobrazit protokoly DSC v uzlu:** Pokud se konfigurace úspěšně zkompiluje, ale při použití v uzlu dojde k chybě, můžete najít podrobné informace v protokolech. Informace o tom, kde najít protokoly DSC, najdete v tématu [kde jsou protokoly událostí DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

   Kromě toho vám [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) může pomoct při analýze podrobných informací z protokolů DSC. Pokud se obrátíte na podporu, budou tyto protokoly vyžadovat diagnostiku vašeho problému.

   **XDscDiagnostics** můžete na svém místním počítači nainstalovat podle pokynů uvedených v části [Instalace modulu stabilní verze](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

   Pokud chcete nainstalovat **xDscDiagnostics** na počítač Azure, můžete použít příkaz [AZ VM Run-Command](/cli/azure/vm/run-command) nebo [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Na portálu můžete taky použít možnost **Spustit příkaz** , a to podle kroků uvedených v části [spuštění skriptů POWERSHELLu na virtuálním počítači s Windows pomocí příkazu Spustit](../../virtual-machines/windows/run-command.md).

   Informace o použití **xDscDiagnostics**najdete v tématu [použití xDscDiagnostics k analýze protokolů DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)a také [rutin xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).
3. **Ujistěte se, že uzly a pracovní prostor služby Automation mají požadované moduly:** Požadovaná konfigurace stavu závisí na modulech nainstalovaných v uzlu.  Při použití konfigurace stavu Azure Automation importujte do svého účtu Automation všechny požadované moduly pomocí kroků uvedených v části [Import modulů](../shared-resources/modules.md#import-modules). Konfigurace můžou mít taky závislost na konkrétních verzích modulů.  Další informace najdete v tématu [řešení potíží s moduly](shared-resources.md#modules).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Běžné chyby při práci s konfigurací požadovaného stavu (DSC)

### <a name="unsupported-characters"></a>Scénář: na portálu nejde odstranit konfiguraci se speciálními znaky.

#### <a name="issue"></a>Problém

Při pokusu o odstranění konfigurace DSC z portálu se zobrazí následující chyba:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Příčina

Tato chyba je dočasný problém, který se plánuje vyřešit.

#### <a name="resolution"></a>Rozlišení

* Pomocí příkazu AZ rutina Remove-AzAutomationDscConfiguration konfiguraci odstraňte.
* Dokumentace k této rutině se ještě neaktualizovala.  Do té doby se v dokumentaci k modulu AzureRM Podívejte.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scénář: registrace agenta DSC se nezdařila

#### <a name="issue"></a>Problém

Při pokusu o spuštění `Set-DscLocalConfigurationManager` nebo jiné rutiny DSC se zobrazí chyba:

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

Tato chyba je obvykle způsobena bránou firewall, počítač je za proxy server nebo jinými chybami v síti.

#### <a name="resolution"></a>Rozlišení

Ověřte, že váš počítač má přístup ke správným koncovým bodům pro Azure Automation DSC, a zkuste to znovu. Seznam potřebných portů a adres najdete v tématu [Plánování sítě](../automation-dsc-overview.md#network-planning) .

### <a name="failed-not-found"></a>Scénář: uzel je v neúspěšném stavu s chybou "Nenalezeno".

#### <a name="issue"></a>Problém

Uzel má sestavu se stavem **selhání** a obsahuje chybu:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Příčina

K této chybě obvykle dochází v případě, že je uzel přiřazen k názvu konfigurace (například ABC) namísto názvu konfigurace uzlu (například ABC. WebServer).

#### <a name="resolution"></a>Rozlišení

* Ujistěte se, že přiřadíte uzel s názvem konfigurace uzlu, nikoli "název konfigurace".
* Můžete přiřadit konfiguraci uzlu uzlu pomocí Azure Portal nebo pomocí rutiny prostředí PowerShell.

  * Chcete-li přiřadit konfiguraci uzlu uzlu pomocí Azure Portal, otevřete stránku **uzly DSC** a pak vyberte uzel a klikněte na tlačítko **přiřadit konfiguraci uzlu** .
  * Pokud chcete přiřadit konfiguraci uzlu k uzlu pomocí rutiny prostředí PowerShell, použijte rutinu **set-AzureRmAutomationDscNode** .

### <a name="no-mof-files"></a>Scénář: při kompilaci konfigurace nevytvořily se žádné konfigurace uzlů (soubory MOF).

#### <a name="issue"></a>Problém

Úloha kompilace DSC se pozastaví s chybou:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Příčina

Pokud je výraz následující po klíčovém slově **uzlu** v konfiguraci DSC vyhodnocen jako `$null`, nebudou vytvořeny žádné konfigurace uzlů.

#### <a name="resolution"></a>Rozlišení

Problém vyřeší některá z následujících řešení:

* Ujistěte se, že výraz vedle klíčového slova **Node** v definici konfigurace není vyhodnocen jako $null.
* Pokud předáváte ConfigurationData při kompilování konfigurace, ujistěte se, že předáváte očekávané hodnoty, které konfigurace vyžaduje od [ConfigurationData](../automation-dsc-compile.md).

### <a name="dsc-in-progress"></a>Scénář: sestava uzlu DSC se zablokuje ve stavu probíhá.

#### <a name="issue"></a>Problém

Výstupy agenta DSC:

```error
No instance found with given property values
```

#### <a name="cause"></a>Příčina

Provedli jste upgrade verze WMF a máte poškozenou službu WMI.

#### <a name="resolution"></a>Rozlišení

Pokud chcete problém vyřešit, postupujte podle pokynů v článku [známé problémy a omezení DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) .

### <a name="issue-using-credential"></a>Scénář: nejde použít přihlašovací údaje v konfiguraci DSC.

#### <a name="issue"></a>Problém

Úloha kompilace DSC byla pozastavena s chybou:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Příčina

V konfiguraci jste použili přihlašovací údaje, ale neposkytli správné **ConfigurationData** pro nastavení **PSDscAllowPlainTextPassword** na hodnotu true pro každou konfiguraci uzlu.

#### <a name="resolution"></a>Rozlišení

* Nezapomeňte předat správný **ConfigurationData** a nastavit **PSDscAllowPlainTextPassword** na hodnotu true pro každou konfiguraci uzlu uvedenou v konfiguraci. Další informace najdete v tématu [assety in Azure Automation DSC](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation).

### <a name="failure-processing-extension"></a>Scénář: Chyba při připojování z rozšíření DSC, Chyba při neúspěšném rozšíření pro zpracování

#### <a name="issue"></a>Problém

Při připojování pomocí rozšíření DSC dojde k chybě, která obsahuje chybu:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Příčina

K této chybě obvykle dochází v případě, že se k uzlu přiřadí název konfigurace uzlu, který ve službě neexistuje.

#### <a name="resolution"></a>Rozlišení

* Ujistěte se, že přiřazujete uzel s názvem konfigurace uzlu, který přesně odpovídá názvu ve službě.
* Můžete zvolit, že nebudete zahrnovat název konfigurace uzlu, což bude mít za následek připojování uzlu, ale ne přiřazení konfigurace uzlu.

### <a name="failure-linux-temp-noexec"></a>Scénář: použití konfigurace v systému Linux, při obecné chybě dojde k selhání.

#### <a name="issue"></a>Problém

Při použití konfigurace v systému Linux dojde k chybě, která obsahuje chybu:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Příčina

Zákazníci zjistili, že pokud je `/tmp` umístění nastavené na `noexec`, aktuální verze DSC se nepodaří použít konfigurace.

#### <a name="resolution"></a>Rozlišení

* Z umístění `/tmp` odeberte možnost `noexec`.

### <a name="compilation-node-name-overlap"></a>Scénář: názvy konfigurací uzlů, které se překrývají, by mohly vést k chybné verzi.

#### <a name="issue"></a>Problém

Pokud je pro generování více konfigurací uzlů použit jediný konfigurační skript a některé konfigurace uzlů mají název, který je podmnožinou dalších, může problém ve službě kompilace způsobit přiřazení nesprávné konfigurace.  K tomu dochází pouze při použití jediného skriptu ke generování konfigurací s konfiguračními daty na uzel a pouze v případě, že dojde k překrytí názvu na začátku řetězce.

Pokud je například jeden konfigurační skript použit ke generování konfigurací založených na datech uzlu předaných jako zatřiďovací tabulka pomocí rutin a data uzlu zahrnuje server s názvem "Server" a "1server".

#### <a name="cause"></a>Příčina

Známý problém se službou kompilace.

#### <a name="resolution"></a>Rozlišení

Nejlepším alternativním řešením je kompilace místně nebo v kanálu CI/CD a nahrání souborů MOF přímo do služby.  Pokud je kompilace ve službě požadavkem, další nejlepší alternativní řešení by mělo rozdělit úlohy kompilace, aby se v názvech překrývaly.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
