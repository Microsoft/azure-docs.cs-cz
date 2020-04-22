---
title: Správa předskriptů a postskriptů v nasazení správy aktualizací v Azure
description: Tento článek popisuje, jak konfigurovat a spravovat předběžné skripty a postskripty pro nasazení aktualizací.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 00cde5255f9c9a2baa7c7042ae2a8f73448da0ae
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679989"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Správa předskriptů a postskriptů

Předskripty a post-skripty jsou runbooky, které se mají spouštět ve vašem účtu Azure Automation před (před úlohou) a po nasazení (po instalaci) aktualizace. Předběžné skripty a post-skripty se spouštějí v kontextu Azure, ne místně. Předběžné skripty jsou spuštěny na začátku nasazení aktualizace. Post-skripty spustit na konci nasazení a po všech restartování, které jsou nakonfigurovány.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="pre-script-and-post-script-requirements"></a>Požadavky na předskriptování a postskript

Aby se soubor Runbook použil jako předskript nebo post-script, musíte jej importovat do účtu Automation a [publikovat soubor Runbook](manage-runbooks.md#publishing-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parametry před skriptem a postskriptu

Při konfiguraci předskriptů a post-scriptů můžete předat parametry stejně jako plánování runbooku. Parametry jsou definovány v době vytvoření nasazení aktualizace. Pre-skripty a post-skripty podporují následující typy:

* To je v pořádku.
* [bajt]
* [int]
* To je v pořádku.
* [desetinná]
* [svobodná]
* To je v pořádku.
* [DateTime]
* [řetězec]

Parametry runbooku před skriptem a po skriptu nepodporují logické typy, typy objektů nebo polí. Tyto hodnoty způsobí selhání runbooků. 

Pokud potřebujete jiný typ objektu, můžete jej přetypovat na jiný typ s vlastní logikou v runbooku.

Kromě standardních parametrů runbooku `SoftwareUpdateConfigurationRunContext` je k dispozici parametr (typ řetězce JSON). Pokud definujete parametr v předskriptové nebo post-script runbook, je automaticky předán nasazení aktualizace. Parametr obsahuje informace o nasazení aktualizace, což je podmnožina informací vrácených [rozhraním API SoftwareUpdateconfigurations .](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) Níže uvedené oddíly definují přidružené vlastnosti.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Vlastnosti SoftwareUpdateConfigurationRunContext

|Vlastnost  |Popis  |
|---------|---------|
|Název konfigurace aktualizace softwaru     | Název konfigurace aktualizace softwaru.        |
|SoftwareUpdateConfigurationRunId     | Jedinečné ID pro spuštění.        |
|SoftwareUpdateConfigurationSettings     | Kolekce vlastností souvisejících s konfigurací aktualizace softwaru.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Operační systémy určené pro nasazení aktualizace.         |
|SoftwareUpdateConfigurationSettings.duration     | Maximální doba nasazení aktualizace bude `PT[n]H[n]M[n]S` spuštěna podle ISO8601; také nazývá okno údržby.          |
|SoftwareUpdateConfigurationSettings.Windows     | Kolekce vlastností souvisejících s počítači se systémem Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Seznam kbs, které jsou vyloučeny z nasazení aktualizace.        |
|SoftwareUpdateConfigurationSettings.Windows.includedAktualizace     | Aktualizace klasifikace vybrané pro nasazení aktualizace.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Nastavení restartování pro nasazení aktualizace.        |
|azureVirtualMachines     | Seznam resourceIds pro virtuální počítače Azure v nasazení aktualizace.        |
|názvy počítačů bez Azure|Seznam nepočítačů Azure v oblasti virtuálních hedn v nasazení aktualizace.|

Následující příklad je řetězec JSON předaný parametru **SoftwareUpdateConfigurationRunContext:**

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Úplný příklad se všemi vlastnostmi naleznete na adrese: [Získat konfiguraci aktualizace softwaru podle názvu](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Objekt `SoftwareUpdateConfigurationRunContext` může obsahovat duplicitní položky pro počítače. To může způsobit, že předběžné skripty a post-skripty spustit vícekrát na stejném počítači. Chcete-li toto `Sort-Object -Unique` chování obejít, použijte k výběru pouze jedinečné názvy virtuálních počítačů.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>Použití předběžného nebo postskriptu v nasazení

Chcete-li použít předskript nebo post-script v nasazení aktualizace, začněte vytvořením nasazení aktualizace. Vyberte **možnost Předběžné skripty + Post-Skripty**. Tato akce otevře stránku **Vybrat předběžné skripty + Post-skripty.**

![Výběr skriptů](./media/pre-post-scripts/select-scripts.png)

Vyberte skript, který chcete použít. V tomto příkladu používáme **updateManagement-TurnOnVms** runbook. Když vyberete runbook, otevře se stránka **Konfigurovat skript.** Vyberte **Možnost Předskriptování**a pak vyberte **OK**.

Tento postup opakujte pro skript **UpdateManagement-TurnOffVms.** Ale když zvolíte **typ skriptu**, vyberte **Post-Script**.

V části **Vybrané položky** se nyní zobrazí oba vybrané skripty. Jeden je pre-script a druhý je post-skript:

![Vybrané položky](./media/pre-post-scripts/selected-items.png)

Dokončete konfiguraci nasazení aktualizace.

Po dokončení nasazení aktualizace můžete přejít na **aktualizace nasazení** zobrazíte výsledky. Jak můžete vidět, stav je k dispozici pro pre-script a post-script:

![Aktualizovat výsledky](./media/pre-post-scripts/update-results.png)

Výběrem spuštění nasazení aktualizace se zobrazí další podrobnosti o předskriptech a post-scriptech. Je k dispozici odkaz na zdroj skriptu v době spuštění.

![Výsledky spuštění nasazení](./media/pre-post-scripts/deployment-run.png)

ve vašem scénáři.

## <a name="stopping-a-deployment"></a>Zastavení nasazení

Pokud chcete zastavit nasazení na základě předběžného skriptu, musíte [vyvolat](automation-runbook-execution.md#throw) výjimku. Pokud tak neučiníte, nasazení a post-script bude stále spuštěna. Následující fragment kódu ukazuje, jak vyvolat výjimku.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```



## <a name="interacting-with-machines"></a>Interakce se stroji

Předskripty a post-úlohy spustit jako runbook ve vašem účtu automatizace a ne přímo na počítačích ve vašem nasazení. Předúkoly a post-úlohy také spustit v kontextu Azure a nemají přístup k počítačům mimo Azure. Následující části ukazují, jak můžete pracovat s počítači přímo, ať už jsou to virtuální počítače Azure nebo počítače než Azure.

### <a name="interact-with-azure-machines"></a>Interakce s počítači Azure

Předúkoly a po úkolech se spouštějí jako runbooky a ve vašem nasazení se nativně nespouštějí na virtuálních počítačích Azure. Chcete-li pracovat s virtuálními počítači Azure, musíte mít následující položky:

* Účet Spustit jako
* Runbook, který chcete spustit

Chcete-li komunikovat s počítači Azure, měli byste použít rutinu [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) k interakci s virtuálními počítači Azure. Příklad, jak to provést, naleznete v příkladu sady Runbook [Update Management – spuštění skriptu s příkazem Spustit](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interact-with-non-azure-machines"></a>Interakce s počítači, které nejsou azure

Předběžné úkoly a následné úlohy se spouštějí v kontextu Azure a nemají přístup k počítačům, které nejsou azure. Chcete-li pracovat s počítači, které nejsou Azure, musíte mít následující položky:

* Účet Spustit jako
* Hybridní pracovník runbooku nainstalovaný v počítači
* Runbook, který chcete spustit místně
* Nadřazená runbook

Pro interakci s počítači, které nejsou azure, se nadřazený runbook spouštěný v kontextu Azure. Tento runbook volá podřízený runbook s rutinou [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) Je nutné `RunOn` zadat parametr a zadat název hybridního pracovníka runbooku, na který má být skript spuštěn. Podívejte se na příklad sady [Runbook Update Management – spusťte skript místně](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="aborting-patch-deployment"></a>Přerušení nasazení opravy

Pokud předskript vrátí chybu, můžete přerušit nasazení. Chcete-li to provést, je nutné [vyvolat](/powershell/module/microsoft.powershell.core/about/about_throw) chybu ve skriptu pro všechny logiky, které by představovaly selhání.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Ukázky

Ukázky pro předskripty a post-skripty najdete v [Galerii Centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) a [PowerShell Gallery](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), nebo je můžete importovat prostřednictvím portálu Azure. Chcete-li to provést, vyberte v části **Automatizace procesů**v části Automatizace procesů **v galerii sad Runbook**. Pro filtr použijte **správu aktualizací.**

![Seznam galerií](./media/pre-post-scripts/runbook-gallery.png)

Nebo je můžete vyhledat podle názvu skriptu, jak je znázorněno v následujícím seznamu:

* Správa aktualizací – zapnutí virtuálních počítačů
* Správa aktualizací – vypnutí virtuálních měn
* Správa aktualizací – spuštění skriptu místně
* Správa aktualizací – šablona pro skripty před/po
* Správa aktualizací – spuštění skriptu pomocí příkazu Spustit

> [!IMPORTANT]
> Po importu sad Runbook je nutné je před použitím publikovat. Chcete-li to provést, vyhledejte runbook v účtu Automation, vyberte **Upravit**a pak vyberte **Publikovat**.

Ukázky jsou založeny na základní šabloně, která je definována v následujícím příkladu. Tuto šablonu lze použít k vytvoření vlastního runbooku pro použití s předskripty a post-skripty. Obsahuje se potřeba logika pro ověřování `SoftwareUpdateConfigurationRunContext` pomocí Azure a zpracování parametru.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Pro negrafické sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell a jsou aliasy pro [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Můžete použít tyto rutiny nebo můžete [aktualizovat moduly](automation-update-azure-modules.md) v účtu Automation na nejnovější verze. Možná budete muset aktualizovat moduly, i když jste právě vytvořili nový účet Automatizace.

## <a name="next-steps"></a>Další kroky

Přejděte k následujícímu kurzu, kde se dozvíte, jak spravovat aktualizace pro virtuální počítače s Windows:

> [!div class="nextstepaction"]
> [Správa aktualizací a oprav pro virtuální počítače Azure s Windows](automation-tutorial-update-management.md)