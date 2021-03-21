---
title: Řešení potíží s Azure Automation sdílenými prostředky
description: Tento článek popisuje, jak řešit problémy s Azure Automation sdílenými prostředky a řešit potíže.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 1a822166ae4c2bf793e0fa50e93018f499fcc27a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99053615"
---
# <a name="troubleshoot-shared-resource-issues"></a>Řešení potíží se sdílenými prostředky

Tento článek popisuje problémy, které mohou nastat při používání [sdílených prostředků](../automation-intro.md#shared-resources) v Azure Automation.

## <a name="modules"></a>Moduly

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scénář: během importu se zablokuje modul.

#### <a name="issue"></a>Problém

Modul se zablokuje ve stavu *importu* při importu nebo aktualizaci Azure Automationch modulů.

#### <a name="cause"></a>Příčina

Vzhledem k tomu, že import modulů PowerShellu je složitý proces s více kroky, modul nemusí správně naimportovat a může být zablokovaný v přechodném stavu. Další informace o procesu importu najdete v tématu [importování modulu PowerShellu](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, je nutné odebrat modul, který je zablokovaný pomocí rutiny [Remove-AzAutomationModule](/powershell/module/Az.Automation/Remove-AzAutomationModule) . Pak můžete modul znovu importovat.

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

Došlo k známému problému s aktualizací AzureRM modulů v účtu Automation. Konkrétně k problému dojde, pokud jsou moduly ve skupině prostředků s číselným názvem začínajícím 0.

#### <a name="resolution"></a>Řešení

Pokud chcete aktualizovat AzureRM moduly v účtu Automation, musí být účet ve skupině prostředků s alfanumerickým názvem. Skupiny prostředků s číselnými názvy začínající hodnotou 0 nemůžou v tuto chvíli aktualizovat moduly AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scénář: modul nelze importovat nebo rutiny nejde spustit po importu.

#### <a name="issue"></a>Problém

Modul se nepodařilo importovat, nebo se úspěšně importuje, ale nejsou extrahovány žádné rutiny.

#### <a name="cause"></a>Příčina

Některé běžné důvody, proč se modul nemusí úspěšně naimportovat do Azure Automation:

* Struktura neodpovídá struktuře, která je potřebná pro automatizaci.
* Modul závisí na jiném modulu, který nebyl nasazen do vašeho účtu Automation.
* V modulu chybí závislosti ve složce.
* Rutina [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) se používá k nahrání modulu a neposkytli jste úplnou cestu k úložišti, nebo jste modul nespustili pomocí veřejně přístupné adresy URL.

#### <a name="resolution"></a>Řešení

K vyřešení problému použijte kterékoli z těchto řešení:

* Ujistěte se, že modul používá formát: ModuleName.zip-> Module nebo číslo verze-> (Module. psm1, ModuleName.psd1).
* Otevřete soubor **. psd1** a podívejte se, jestli má modul nějaké závislosti. V takovém případě nahrajte tyto moduly do účtu Automation.
* Ujistěte se, že se ve složce modulu nacházejí všechny odkazované soubory **. dll** .

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scénář: Update-AzureModule.ps1 se při aktualizaci modulů pozastaví

#### <a name="issue"></a>Problém

Když pomocí sady [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook aktualizujete moduly Azure, proces aktualizace modulu je pozastaven.

#### <a name="cause"></a>Příčina

V této sadě Runbook se ve výchozím nastavení určí, kolik modulů je současně aktualizováno 10. Proces aktualizace je náchylný k chybám, když se v jednom okamžiku aktualizuje příliš mnoho modulů.

#### <a name="resolution"></a>Řešení

Není běžné, že ve stejném účtu Automation jsou vyžadovány všechny moduly AzureRM nebo AZ. Měli byste importovat jenom konkrétní moduly, které potřebujete.

> [!NOTE]
> Vyhněte se importování celého `Az.Automation` `AzureRM.Automation` modulu nebo, který importuje všechny obsažené moduly.

Pokud se proces aktualizace pozastaví, přidejte `SimultaneousModuleImportJobCount` do skriptu **Update-AzureModules.ps1** parametr a zadejte nižší hodnotu než výchozí hodnota 10. Pokud implementujete tuto logiku, zkuste začít hodnotou 3 nebo 5. `SimultaneousModuleImportJobCount` je parametr Runbooku sady **Update-AutomationAzureModulesForAccount** , který se používá k aktualizaci modulů Azure. Pokud provedete tuto úpravu, proces aktualizace se spustí déle, ale bude mít lepší šanci na jejich dokončení. Následující příklad ukazuje parametr a místo vložení do sady Runbook:

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

Když se pokusíte vytvořit nebo aktualizovat účet Spustit jako, zobrazí se chybová zpráva podobná následující:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Příčina

Nemáte oprávnění, která potřebujete k vytvoření nebo aktualizaci účtu Spustit jako, nebo je prostředek uzamčen na úrovni skupiny prostředků.

#### <a name="resolution"></a>Řešení

Chcete-li vytvořit nebo aktualizovat účet Spustit jako, je nutné mít příslušná [oprávnění](../automation-security-overview.md#permissions) k různým prostředkům používaným účtem spustit jako.

Pokud k problému dochází z důvodu zámku, ověřte, že je možné zámek odebrat. Pak přejděte k prostředku, který je v Azure Portal uzamčený, klikněte pravým tlačítkem na zámek a vyberte **Odstranit**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scénář: zobrazí se chyba "při provádění Runbooku se zobrazí vstupní bod s názvem ' GetPerAdapterInfo ' v knihovně DLL ' iplpapi.dll '.

#### <a name="issue"></a>Problém

Při spouštění sady Runbook se zobrazí následující výjimka:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Příčina

Tato chyba je pravděpodobně způsobena nesprávně nakonfigurovaným [účtem spustit jako](../automation-security-overview.md).

#### <a name="resolution"></a>Řešení

Ujistěte se, že je váš účet Spustit jako správně nakonfigurovaný. Pak ověřte, že v Runbooku máte správný kód pro ověření pomocí Azure. Následující příklad ukazuje fragment kódu pro ověření v Azure v Runbooku pomocí účtu Spustit jako.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Další kroky

Pokud tento článek problém nevyřeší, zkuste další podporu vyzkoušet v jednom z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) . Jedná se o oficiální Microsoft Azure účet pro připojení komunity Azure ke správným zdrojům: odpovědi, podpora a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.