---
title: Řešení chyb s Azure Automation sdílenými prostředky
description: Naučte se řešit problémy s Azure Automation sdílenými prostředky podporujícími Runbooky.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769859"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Řešení chyb se sdílenými prostředky

Tento článek popisuje řešení problémů, ke kterým může při použití sdílených prostředků v Azure Automation běžet.

## <a name="modules"></a>Moduly

### <a name="module-stuck-importing"></a>Scénář: modul se zablokuje při importu.

#### <a name="issue"></a>Problém

Když naimportujete nebo aktualizujete moduly ve službě Azure Automation, modul se zablokuje ve stavu **importu** .

#### <a name="cause"></a>Příčina

Import modulů PowerShellu je složitý proces s více kroky. Tento proces zavádí možnost nesprávného importu modulu. Pokud k tomuto problému dojde, modul, který importujete, se může zablokovat v přechodném stavu. Další informace o tomto procesu najdete v tématu [importování modulu PowerShellu](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Rozlišení

Chcete-li tento problém vyřešit, je nutné odebrat modul, který je zablokovaný ve stavu **Import** pomocí rutiny [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) . Pak můžete modul znovu importovat.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Scénář: moduly AzureRM se zablokují import po pokusu o jejich aktualizaci.

#### <a name="issue"></a>Problém

Po pokusu o aktualizaci modulů AzureRM zůstane v účtu banner s následující zprávou:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Příčina

Došlo k známému problému s aktualizací AzureRM modulů v účtu Automation, který je ve skupině prostředků s číselným názvem, který začíná na 0.

#### <a name="resolution"></a>Rozlišení

Pokud chcete v účtu Automation aktualizovat moduly Azure, musí být ve skupině prostředků, která má alfanumerický název. Skupiny prostředků s číselnými názvy začínající hodnotou 0 nemůžou v tuto chvíli aktualizovat moduly AzureRM.

### <a name="module-fails-to-import"></a>Scénář: modul nelze importovat nebo rutiny nejde spustit po importu.

#### <a name="issue"></a>Problém

Modul se nepodařilo úspěšně importovat nebo importovat, ale neextrahují se žádné rutiny.

#### <a name="cause"></a>Příčina

Některé běžné důvody, proč se modul nemusí úspěšně naimportovat do Azure Automation:

* Struktura neodpovídá struktuře, ve které je automatizace potřebuje.
* Modul závisí na jiném modulu, který nebyl nasazen do vašeho účtu Automation.
* V modulu chybí závislosti ve složce.
* Rutina `New-AzureRmAutomationModule` slouží k nahrání modulu a neobdrželi jste celou cestu k úložišti, nebo jste nenapsali modul pomocí veřejně přístupné adresy URL.

#### <a name="resolution"></a>Rozlišení

Problém vyřeší některá z následujících řešení:

* Ujistěte se, že modul používá následující formát: název modulu. zip **->** název modulu nebo číslo verze **->** (Module. psm1, Module. psd1).
* Otevřete soubor. psd1 a podívejte se, jestli má modul nějaké závislosti. V takovém případě nahrajte tyto moduly do účtu Automation.
* Ujistěte se, že se ve složce modulu nacházejí všechny odkazované knihovny DLL.

### <a name="all-modules-suspended"></a>Scénář: Update-AzureModule. ps1 se při aktualizaci modulů pozastaví.

#### <a name="issue"></a>Problém

Když při použití Runbooku [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) aktualizujete moduly Azure, aktualizace modulu proces aktualizace pozastaví.

#### <a name="cause"></a>Příčina

Výchozí nastavení, které určuje, kolik modulů se má aktualizovat současně, je 10 při použití skriptu `Update-AzureModule.ps1`. Proces aktualizace je náchylný k chybám, když se v jednom okamžiku aktualizuje příliš mnoho modulů.

#### <a name="resolution"></a>Rozlišení

Není běžné, že všechny moduly AzureRM jsou nutné ve stejném účtu Automation. Doporučuje se importovat jenom moduly AzureRM, které potřebujete.

> [!NOTE]
> Vyhněte se importování modulu **AzureRM** . Import modulů **AzureRM** způsobí import všech modulů **AzureRM.\*** , takže to není doporučeno.

Je-li proces aktualizace pozastaven, je nutné přidat parametr `SimultaneousModuleImportJobCount` do skriptu `Update-AzureModules.ps1` a zadat nižší hodnotu, než je výchozí hodnota 10. Doporučuje se, Pokud implementujete tuto logiku, abyste začali s hodnotou 3 nebo 5. `SimultaneousModuleImportJobCount` je parametr Runbooku `Update-AutomationAzureModulesForAccount` systému, který se používá k aktualizaci modulů Azure. Tato změna způsobí, že proces bude delší dobu, ale bude mít lepší šanci na jeho dokončení. Následující příklad ukazuje parametr a místo vložení do sady Runbook:

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

### <a name="unable-create-update"></a>Scénář: nemůžete vytvořit nebo aktualizovat účet Spustit jako.

#### <a name="issue"></a>Problém

Když se pokusíte vytvořit nebo aktualizovat účet Spustit jako, zobrazí se chyba podobná této chybové zprávě:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Příčina

Nemáte oprávnění, která potřebujete k vytvoření nebo aktualizaci účtu Spustit jako, nebo zda je prostředek uzamčen na úrovni skupiny prostředků.

#### <a name="resolution"></a>Rozlišení

Chcete-li vytvořit nebo aktualizovat účet Spustit jako, je nutné mít příslušná oprávnění k různým prostředkům používaným účtem spustit jako. Další informace o oprávněních potřebných k vytvoření nebo aktualizaci účtu Spustit jako najdete v tématu [oprávnění účtu Spustit jako](../manage-runas-account.md#permissions).

Pokud k problému dochází z důvodu zámku, ověřte, že zámek je OK, aby ho bylo možné odebrat. Pak přejděte k prostředku, který je uzamčený, klikněte pravým tlačítkem na zámek a zvolte **Odstranit** , aby se zámek odebral.

### <a name="iphelper"></a>Scénář: při provádění Runbooku se zobrazí chyba "v souboru DLL" iplpapi. dll "" nelze najít vstupní bod s názvem ' GetPerAdapterInfo '.

#### <a name="issue"></a>Problém

Při spouštění sady Runbook se zobrazí následující výjimka:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Příčina

Tato chyba je pravděpodobně způsobena nesprávně nakonfigurovaným [účtem spustit jako](../manage-runas-account.md).

#### <a name="resolution"></a>Rozlišení

Ujistěte se, že je váš [účet Spustit jako](../manage-runas-account.md) správně nakonfigurovaný. Jakmile je správně nakonfigurována, ujistěte se, že máte ve svém Runbooku správný kód pro ověření pomocí Azure. Následující příklad ukazuje fragment kódu pro ověření v Azure v sadě Runbook pomocí účtu Spustit jako.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
