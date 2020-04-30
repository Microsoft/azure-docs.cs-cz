---
title: Řešení potíží se sdílenými prostředky v Azure Automation
description: Naučte se řešit potíže s Azure Automation sdílenými prostředky a řešit problémy.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733576"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Řešení potíží se sdílenými prostředky v Azure Automation

Tento článek popisuje řešení problémů, ke kterým může při použití [sdílených prostředků](../automation-intro.md#shared-resources) v Azure Automation běžet.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="modules"></a>Moduly

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scénář: během importu se zablokuje modul.

#### <a name="issue"></a>Problém

Modul se zablokuje ve stavu importu při importu nebo aktualizaci Azure Automationch modulů.

#### <a name="cause"></a>Příčina

Vzhledem k tomu, že import modulů PowerShellu je složitý proces s více kroky, modul nemusí správně naimportovat a může být zablokovaný v přechodném stavu. Další informace o procesu importu najdete v tématu [importování modulu PowerShellu](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, je nutné odebrat modul, který je zablokovaný ve stavu import pomocí rutiny [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) . Pak můžete modul znovu importovat.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scénář: během importu po pokusu o aktualizaci se zablokují moduly AzureRM

#### <a name="issue"></a>Problém

Po pokusu o aktualizaci modulů AzureRM zůstane v účtu banner s následující zprávou:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Příčina

Došlo k známému problému s aktualizací AzureRM modulů v účtu Automation, který je ve skupině prostředků s číselným názvem začínajícím na 0.

#### <a name="resolution"></a>Řešení

Pokud chcete aktualizovat AzureRM moduly v účtu Automation, musí být účet ve skupině prostředků s alfanumerickým názvem. Skupiny prostředků s číselnými názvy začínající hodnotou 0 nemůžou v tuto chvíli aktualizovat moduly AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scénář: modul nelze importovat nebo rutiny nejde spustit po importu.

#### <a name="issue"></a>Problém

Modul se nepodaří importovat nebo se úspěšně importuje, ale neextrahují se žádné rutiny.

#### <a name="cause"></a>Příčina

Některé běžné důvody, proč se modul nemusí úspěšně naimportovat do Azure Automation:

* Struktura neodpovídá struktuře, která je potřebná pro automatizaci.
* Modul závisí na jiném modulu, který nebyl nasazen do vašeho účtu Automation.
* V modulu chybí závislosti ve složce.
* Rutina [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) se používá k nahrání modulu a Vy jste nezadali úplnou cestu k úložišti, nebo jste modul nespustili pomocí veřejně přístupné adresy URL.

#### <a name="resolution"></a>Řešení

K vyřešení problému použijte kterékoli z těchto řešení.

* Ujistěte se, že modul používá formát: název modulu. zip-> název modulu nebo číslo verze-> (Module. psm1, Module. psd1).
* Otevřete soubor **. psd1** a podívejte se, jestli má modul nějaké závislosti. V takovém případě nahrajte tyto moduly do účtu Automation.
* Ujistěte se, že se ve složce modulu nacházejí všechny odkazované soubory **. dll** .

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scénář: Update-AzureModule. ps1 se při aktualizaci modulů pozastaví.

#### <a name="issue"></a>Problém

Když při použití Runbooku [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) aktualizujete moduly Azure, proces aktualizace modulu se pozastaví.

#### <a name="cause"></a>Příčina

Výchozí nastavení, které určuje, kolik modulů se aktualizuje současně, je 10 při použití **Update-AzureModule. ps1**. Proces aktualizace je náchylný k chybám, když se v jednom okamžiku aktualizuje příliš mnoho modulů.

#### <a name="resolution"></a>Řešení

Není běžné, že ve stejném účtu Automation jsou vyžadovány všechny moduly AzureRM nebo AZ. Doporučuje se importovat jenom konkrétní moduly, které potřebujete.

> [!NOTE]
> Vyhněte se `Az.Automation` importování `AzureRM.Automation` celého modulu nebo, který importuje všechny obsažené moduly.

Pokud se proces aktualizace pozastaví, přidejte `SimultaneousModuleImportJobCount` parametr do skriptu **Update-AzureModules. ps1** a zadejte nižší hodnotu, než je výchozí hodnota 10. Pokud implementujete tuto logiku, doporučujeme začít s hodnotou 3 nebo 5. `SimultaneousModuleImportJobCount`je parametr Runbooku sady **Update-AutomationAzureModulesForAccount** , který se používá k aktualizaci modulů Azure. Pokud provedete tuto úpravu, proces aktualizace se spustí déle, ale bude mít lepší šanci na jejich dokončení. Následující příklad ukazuje parametr a místo vložení do sady Runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Účty Spustit jako

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scénář: nemůžete vytvořit nebo aktualizovat účet Spustit jako.

#### <a name="issue"></a>Problém

Když se pokusíte vytvořit nebo aktualizovat účet Spustit jako, zobrazí se chyba podobná této chybové zprávě:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Příčina

Nemáte oprávnění, která potřebujete k vytvoření nebo aktualizaci účtu Spustit jako, nebo zda je prostředek uzamčen na úrovni skupiny prostředků.

#### <a name="resolution"></a>Řešení

Chcete-li vytvořit nebo aktualizovat účet Spustit jako, je nutné mít příslušná [oprávnění](../manage-runas-account.md#permissions) k různým prostředkům používaným účtem spustit jako. 

Pokud k problému dochází z důvodu zámku, ověřte, že je možné zámek odebrat. Pak přejděte k prostředku, který je v Azure Portal uzamčený, klikněte pravým tlačítkem na zámek a pak klikněte na **Odstranit**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scénář: při provádění Runbooku se zobrazí chyba "v souboru DLL" iplpapi. dll "" nepovedlo se najít vstupní bod s názvem ' GetPerAdapterInfo '.

#### <a name="issue"></a>Problém

Při spouštění sady Runbook se zobrazí následující výjimka:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Příčina

Tato chyba je pravděpodobně způsobena nesprávně nakonfigurovaným [účtem spustit jako](../manage-runas-account.md).

#### <a name="resolution"></a>Řešení

Ujistěte se, že je váš účet Spustit jako správně nakonfigurovaný. Pak ověřte, že v Runbooku máte správný kód pro ověření pomocí Azure. Následující příklad ukazuje fragment kódu pro ověření v Azure v sadě Runbook pomocí účtu Spustit jako.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Další kroky

Pokud nevidíte výše uvedený problém nebo nemůžete problém vyřešit, zkuste pro další podporu použít jeden z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se [@AzureSupport](https://twitter.com/azuresupport)k, oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
