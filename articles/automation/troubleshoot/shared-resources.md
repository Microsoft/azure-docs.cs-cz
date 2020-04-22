---
title: Poradce při potížích se sdílenými prostředky v Azure Automation
description: Zjistěte, jak řešit a řešit problémy se sdílenými prostředky Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733576"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Poradce při potížích se sdílenými prostředky v Azure Automation

Tento článek popisuje řešení problémů, které můžete narazit při použití [sdílených prostředků](../automation-intro.md#shared-resources) v Azure Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="modules"></a>Moduly

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scénář: Modul se při importu zasekne

#### <a name="issue"></a>Problém

Modul se při importu nebo aktualizaci modulů Azure Automation zasekne ve stavu Importing.

#### <a name="cause"></a>Příčina

Vzhledem k tomu, že import modulů prostředí PowerShell je složitý proces více kroků, modul nemusí importovat správně a může být zablokovaný v přechodném stavu. Další informace o procesu importu najdete v [tématu Import modulu PowerShellu](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, je nutné odebrat modul, který je zablokovaný ve stavu Importing pomocí rutiny [Remove-AzAutomationModule.](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) Potom můžete opakovat import modulu.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scénář: Moduly AzureRM se při importu po pokusu o aktualizaci zaseknou

#### <a name="issue"></a>Problém

Banner s následující zprávou zůstane ve vašem účtu po pokusu o aktualizaci modulů AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Příčina

Je známý problém s aktualizací modulů AzureRM v účtu Automation, který je ve skupině prostředků s číselným názvem začínajícím 0.

#### <a name="resolution"></a>Řešení

Chcete-li aktualizovat moduly AzureRM v účtu Automation, musí být účet ve skupině prostředků s alfanumerickým názvem. Skupiny prostředků s číselnými názvy začínajícími na 0 nemohou v tuto chvíli aktualizovat moduly AzureRM.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scénář: Modul se nepodaří importovat nebo rutiny nelze provést po importu

#### <a name="issue"></a>Problém

Modul se nezdaří importovat nebo importuje úspěšně, ale žádné rutiny jsou extrahovány.

#### <a name="cause"></a>Příčina

Některé běžné důvody, že modul nemusí úspěšně importovat do Azure Automation jsou:

* Struktura neodpovídá struktuře, kterou automatizace potřebuje.
* Modul závisí na jiném modulu, který nebyl nasazen do vašeho účtu automation.
* Modul unikne jeho závislosti ve složce.
* Rutina [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) se používá k nahrání modulu a nedali jste úplnou cestu úložiště nebo jste nenačetli modul pomocí veřejně přístupné adresy URL.

#### <a name="resolution"></a>Řešení

Problém můžete vyřešit pomocí některého z těchto řešení.

* Ujistěte se, že modul sleduje formát: ModuleName.zip -> ModuleName nebo číslo verze -> (ModuleName.psm1, ModuleName.psd1).
* Otevřete soubor **PSD1** a zjistěte, zda modul obsahuje nějaké závislosti. Pokud ano, nahrajte tyto moduly do účtu Automatizace.
* Ujistěte se, že ve složce modulu jsou přítomny všechny odkazované soubory **DLL.**

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scénář: Update-AzureModule.ps1 pozastaví při aktualizaci modulů

#### <a name="issue"></a>Problém

Při použití sady Runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) k aktualizaci modulů Azure je proces aktualizace modulu pozastaven.

#### <a name="cause"></a>Příčina

Výchozí nastavení k určení, kolik modulů jsou aktualizovány současně je 10 při použití **Update-AzureModule.ps1**. Proces aktualizace je náchylný k chybám, když je současně aktualizováno příliš mnoho modulů.

#### <a name="resolution"></a>Řešení

Není běžné, že všechny moduly AzureRM nebo Az jsou vyžadovány ve stejném účtu automatizace. Doporučujeme importovat pouze konkrétní moduly, které potřebujete.

> [!NOTE]
> Vyhněte se `Az.Automation` `AzureRM.Automation` importu celého modulu nebo modulu, který importuje všechny obsažené moduly.

Pokud proces aktualizace pozastaví, `SimultaneousModuleImportJobCount` přidejte parametr do skriptu **Update-AzureModules.ps1** a zadejte nižší hodnotu než výchozí 10. Pokud implementujete tuto logiku, doporučujeme začít s hodnotou 3 nebo 5. `SimultaneousModuleImportJobCount`je parametr sady Runbook **systému Update-AutomationAzureModulesForAccount,** která se používá k aktualizaci modulů Azure. Pokud provedete tuto úpravu, proces aktualizace běží déle, ale má větší šanci na dokončení. Následující příklad ukazuje parametr a kam ho umístit do runbooku:

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

Chcete-li vytvořit nebo aktualizovat účet Spustit jako, musíte mít příslušná [oprávnění](../manage-runas-account.md#permissions) k různým prostředkům používaným účtem Spustit jako. 

Pokud je problém z důvodu zámku, ověřte, zda lze zámek odebrat. Potom přejděte na prostředek, který je uzamčen na webu Azure Portal, klikněte pravým tlačítkem myši na zámek a klikněte na **odstranit**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scénář: Při provádění sady Runbook se zobrazí chyba "Nelze najít vstupní bod s názvem GetPerAdapterInfo" v knihovně DLL iplpapi.dll.

#### <a name="issue"></a>Problém

Při provádění runbooku obdržíte následující výjimku:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Příčina

Tato chyba je pravděpodobně způsobena nesprávně nakonfigurovaným [účtem Spustit jako](../manage-runas-account.md).

#### <a name="resolution"></a>Řešení

Ujistěte se, že je účet Spustit jako správně nakonfigurován. Pak ověřte, že máte správný kód v runbooku k ověření pomocí Azure. Následující příklad ukazuje úryvek kódu k ověření azure v runbooku pomocí spustit jako účet.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Další kroky

Pokud problém nevidíte výše nebo nemůžete problém vyřešit, vyzkoušejte další podporu jedním z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport)se s oficiálním účtem Microsoft Azure a vylepšete tak zákaznickou zkušenost propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
