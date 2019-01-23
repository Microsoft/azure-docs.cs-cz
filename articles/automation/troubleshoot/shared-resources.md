---
title: Řešení potíží s Azure Automation sdílené prostředky
description: Zjistěte, jak řešit potíže s prostředky Azure Automation, které jsou sdílené s
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 911f592c43865ea8bdfe85c1ad1071c7112ae9b6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475437"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Řešení potíží s chybami se sdílenými prostředky

Tento článek popisuje řešení Chcete-li vyřešit problémy, které můžete spouštět napříč při používání sdílených prostředků ve službě Azure Automation.

## <a name="modules"></a>Moduly

### <a name="module-stuck-importing"></a>Scénář: Modul se zasekne importu

#### <a name="issue"></a>Problém

Modul se zasekla v automatickém **import** stavu při importu nebo aktualizovat moduly ve službě Azure automation.

#### <a name="cause"></a>Příčina

Import modulů Powershellu je komplexní vícefázový proces. Tento proces představuje možnost Modul není správně importu. Pokud k tomuto problému dochází, můžete modul, který importujete zaseknout v přechodovém stavu. Další informace o tomto procesu najdete v tématu [importování modulu prostředí PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Řešení

Chcete-li vyřešit tento problém, musíte odebrat modul, který se zasekla v automatickém **import** stavu pomocí [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) rutiny. Můžete pak opakujte import modulu.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="module-fails-to-import"></a>Scénář: Chyby modulu k importu nebo rutin nelze provést po dokončení importu

#### <a name="issue"></a>Problém

Modul nejde importovat nebo se importuje správně, ale jsou extrahovány žádné rutiny.

#### <a name="cause"></a>Příčina

Je několik běžných příčin, které modul nemusí úspěšně importovat do Azure Automation:

* Struktura neodpovídá struktuře Automation musí být v.
* Modul závisí na jiný modul, který nebyl nasazen do vašeho účtu Automation.
* Modul chybí jeho závislosti do složky.
* `New-AzureRmAutomationModule` Rutina se používá k nahrání modulu a nedali úložiště úplnou cestu nebo nebyly načteny modulu pomocí adresy URL veřejně přístupná.

#### <a name="resolution"></a>Řešení

Některé z následujících řešení tento problém vyřešit:

* Ujistěte se, že modul má tento formát: ModuleName.Zip **->** název modulu nebo číslo verze **->** (ModuleName.psm1, ModuleName.psd1)
* Otevření souboru .psd1 a zjistěte, jestli modul mají všechny závislosti. Pokud ano, nahrajte do účtu Automation tyto moduly.
* Ujistěte se, že jsou ve složce modulu všechny odkazované knihovny DLL debuggle.

### <a name="all-modules-suspended"></a>Scénář: Aktualizace AzureModule.ps1 pozastaví při aktualizaci modulů

#### <a name="issue"></a>Problém

Při použití [aktualizace AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) sady runbook, chcete-li aktualizovat moduly Azure pozastaven aktualizace modulu procesu aktualizace.

#### <a name="cause"></a>Příčina

Ve výchozím nastavení k určení, kolik modulů aktualizovat současně je 10 při použití `Update-AzureModule.ps1` skriptu. Proces aktualizace je náchylná k chybám, když se aktualizují příliš mnoho modulů ve stejnou dobu.

#### <a name="resolution"></a>Řešení

Není běžné, že všechny moduly AzureRM nejsou požadovány v rámci stejného účtu Automation. Doporučuje se pouze naimportovat moduly AzureRM, které potřebujete.

> [!NOTE]
> Vyhněte se import **AzureRM** modulu. Import **AzureRM** moduly způsobí, že všechny **AzureRM.\***  moduly, které se mají naimportovat, to není doporučeno.

Pokud proces aktualizace pozastaví, budete muset přidat `SimultaneousModuleImportJobCount` parametr `Update-AzureModules.ps1` skript a zadejte hodnotu nižší než ve výchozím nastavení je 10. Doporučuje se, Pokud implementujete tuto logiku začínat hodnotu 3 nebo 5. `SimultaneousModuleImportJobCount` je parametr `Update-AutomationAzureModulesForAccount` systému sady runbook, který se používá k aktualizaci moduly Azure. Tato změna umožňuje spuštění procesu již, ale má větší šanci dokončení. Následující příklad ukazuje parametr a umístění v sadě runbook:

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

## <a name="run-as-accounts"></a>Účty spustit jako

### <a name="unable-create-update"></a>Scénář: Nemůžete vytvořit nebo aktualizovat účet Spustit jako

#### <a name="issue"></a>Problém

Při pokusu o vytvoření nebo aktualizace účtu spustit jako, zobrazí se chybová zpráva podobná následující chybová zpráva:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Příčina

Na úrovni skupiny prostředků nemáte příslušná oprávnění, které je potřeba vytvořit nebo aktualizovat účet Spustit jako nebo prostředek je uzamčený.

#### <a name="resolution"></a>Řešení

Vytvořit nebo aktualizovat účet Spustit jako, musí mít příslušná oprávnění k různým prostředkům používá účet Spustit jako. Další informace o oprávnění potřebná k vytvoření nebo aktualizace účtu spustit jako najdete v tématu [oprávnění účtu spustit jako](../manage-runas-account.md#permissions).

Pokud se problém z důvodu zámku, ověřte, že je v pořádku. odeberte zámek. Potom přejděte k prostředku, který je uzamčen, klikněte pravým tlačítkem na zámek a zvolte **odstranit** odebrat zámek.

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.