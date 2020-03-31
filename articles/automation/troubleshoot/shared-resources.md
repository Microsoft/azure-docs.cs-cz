---
title: Poradce při potížích se sdílenými prostředky Azure Automation
description: Zjistěte, jak řešit a řešit problémy se sdílenými prostředky Azure Automation podporujícími sady Runbook.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278321"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Poradce při potížích se sdílenými prostředky

Tento článek popisuje řešení problémů, které můžete narazit při použití sdílených prostředků v Azure Automation.

## <a name="modules"></a>Moduly

### <a name="scenario-a-module-is-stuck-importing"></a><a name="module-stuck-importing"></a>Scénář: Modul je zablokovaný import

#### <a name="issue"></a>Problém

Modul se při importu nebo aktualizaci modulů v automatizaci Azure zasekne ve stavu **Importing.**

#### <a name="cause"></a>Příčina

Import modulů prostředí PowerShell je složitý vícekrokový proces. Tento proces zavádí možnost, že modul nebude importován správně. Pokud nastane tento problém, modul, který importujete může být zaseknutý v přechodném stavu. Další informace o tomto procesu najdete [v tématu Import modulu Prostředí PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, je nutné odebrat modul, který je zablokovaný ve stavu **importu** pomocí rutiny [Remove-AzureRmAutomationModule.](/powershell/module/azurerm.automation/remove-azurermautomationmodule) Potom můžete opakovat import modulu.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-importing-after-trying-to-update-them"></a><a name="update-azure-modules-importing"></a>Scénář: Moduly AzureRM se zaseknou importují po pokusu o jejich aktualizaci

#### <a name="issue"></a>Problém

Banner s následující zprávou zůstane ve vašem účtu po pokusu o aktualizaci modulů AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Příčina

Je známý problém s aktualizací modulů AzureRM v automatizačním účtu, který je ve skupině prostředků s číselným názvem začínajícím na 0.

#### <a name="resolution"></a>Řešení

Chcete-li aktualizovat moduly Azure ve vašem účtu Automation, musí být ve skupině prostředků, která má alfanumerický název. Skupiny prostředků s číselnými názvy začínajícími na 0 nemohou v tuto chvíli aktualizovat moduly AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scénář: Modul se nepodaří importovat nebo rutiny nelze provést po importu

#### <a name="issue"></a>Problém

Modul se nezdaří importovat nebo importovat úspěšně, ale žádné rutiny jsou extrahovány.

#### <a name="cause"></a>Příčina

Některé běžné důvody, že modul nemusí úspěšně importovat do Azure Automation jsou:

* Struktura neodpovídá struktuře, kterou automatizace potřebuje.
* Modul závisí na jiném modulu, který nebyl nasazen do vašeho účtu automation.
* Modul unikne jeho závislosti ve složce.
* Rutina se `New-AzureRmAutomationModule` používá k nahrání modulu a nedali jste úplnou cestu úložiště nebo jste nenačetli modul pomocí veřejně přístupné adresy URL.

#### <a name="resolution"></a>Řešení

Problém vyřeší některou z následujících řešení:

* Ujistěte se, že modul následuje podle následujícího formátu: **->** **->** ModuleName.Zip ModuleName nebo číslo verze (ModuleName.psm1, ModuleName.psd1)
* Otevřete soubor PSD1 a zjistěte, zda modul obsahuje nějaké závislosti. Pokud ano, nahrajte tyto moduly do účtu Automatizace.
* Ujistěte se, že všechny odkazované dlls jsou přítomny ve složce modulu.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scénář: Update-AzureModule.ps1 pozastaví při aktualizaci modulů

#### <a name="issue"></a>Problém

Při použití sady Runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) k aktualizaci modulů Azure se aktualizuje proces aktualizace, proces aktualizace se pozastaví.

#### <a name="cause"></a>Příčina

Výchozí nastavení k určení, kolik modulů získat aktualizovány `Update-AzureModule.ps1` současně je 10 při použití skriptu. Proces aktualizace je náchylný k chybám, když je současně aktualizováno příliš mnoho modulů.

#### <a name="resolution"></a>Řešení

Není běžné, že všechny moduly AzureRM jsou vyžadovány ve stejném účtu Automatizace. Doporučujeme importovat jenom moduly AzureRM, které potřebujete.

> [!NOTE]
> Vyhněte se importu modulu **AzureRM.** Import modulů **AzureRM** způsobí import všech modulů **AzureRM.\* ** to není recommened.

Pokud se proces aktualizace pozastaví, `SimultaneousModuleImportJobCount` je třeba `Update-AzureModules.ps1` přidat parametr do skriptu a zadat nižší hodnotu než výchozí hodnota, která je 10. Doporučujeme, pokud implementujete tuto logiku, začít s hodnotou 3 nebo 5. `SimultaneousModuleImportJobCount`je parametr systému `Update-AutomationAzureModulesForAccount` runbook, který se používá k aktualizaci modulů Azure. Tato změna umožňuje proces běžet déle, ale má větší šanci na dokončení. Následující příklad ukazuje parametr a kam ho umístit do runbooku:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scénář: Nelze vytvořit nebo aktualizovat účet Spustit jako

#### <a name="issue"></a>Problém

Při pokusu o vytvoření nebo aktualizaci účtu Spustit jako se zobrazí chyba podobná následující chybové zprávě:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Příčina

Nemáte oprávnění, která potřebujete k vytvoření nebo aktualizaci účtu Spustit jako nebo je prostředek uzamčen na úrovni skupiny prostředků.

#### <a name="resolution"></a>Řešení

Chcete-li vytvořit nebo aktualizovat účet Spustit jako, musíte mít příslušná oprávnění k různým prostředkům používaným účtem Spustit jako. Informace o oprávněních potřebných k vytvoření nebo aktualizaci účtu Spustit jako naleznete v tématu [Spustit jako oprávnění účtu](../manage-runas-account.md#permissions).

Pokud je problém z důvodu zámku, ověřte, zda je zámek v pořádku jej odebrat. Pak přejděte na prostředek, který je uzamčen, klikněte pravým tlačítkem myši na zámek a zvolte **Odstranit** odebrat zámek.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scénář: Při provádění sady Runbook se zobrazí chyba "Nelze najít vstupní bod s názvem GetPerAdapterInfo" v knihovně DLL iplpapi.dll.

#### <a name="issue"></a>Problém

Při provádění runbooku obdržíte následující výjimku:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Příčina

Tato chyba je pravděpodobně způsobena nesprávně nakonfigurovaným [účtem Spustit jako](../manage-runas-account.md).

#### <a name="resolution"></a>Řešení

Zkontrolujte, zda je [účet Spustit jako](../manage-runas-account.md) správně nakonfigurován. Jakmile je správně nakonfigurovaný, ujistěte se, že máte správný kód ve vašem runbooku k ověření pomocí Azure. Následující příklad ukazuje úryvek kódu k ověření azure v runbooku pomocí spustit jako účet.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s – oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete nastolet incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
