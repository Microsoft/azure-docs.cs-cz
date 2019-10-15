---
title: Konfigurace předzálohovacích skriptů v nasazení Update Management v Azure a jejich následné spouštění
description: Tento článek popisuje, jak nakonfigurovat a spravovat předchozí a následné skripty pro nasazení aktualizací.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84cd5db812d995f1160a02917eac5857ee076c7f
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374442"
---
# <a name="manage-pre-and-post-scripts"></a>Správa předzálohovacích a pozálohovacích skriptů

Předzálohovací a pozálohovací skripty umožňují spouštět Runbooky PowerShellu v účtu Automation před (před úlohou) a po (po dokončení) nasazení aktualizace. Předzálohovací a pozálohovací skripty běží v kontextu Azure a ne místně. Předzálohovací skripty běží na začátku nasazení aktualizace. Po dokončení nasazení se skripty spustí na konci nasazení a po všech nakonfigurovaném restartování.

## <a name="runbook-requirements"></a>Požadavky na Runbook

Aby se sada Runbook mohla použít jako předzálohovací skript, musí být sada Runbook naimportována do vašeho účtu Automation a publikována. Další informace o tomto procesu najdete v tématu [publikování Runbooku](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Použití předzálohovacího skriptu

Pokud chcete v nasazení aktualizací použít předzálohovací skript nebo nebo, začněte tím, že vytvoříte nasazení aktualizace. Vyberte **pre-Scripts + post Scripts**. Tato akce otevře stránku **Vybrat před skripty a po skriptu** .

![Vybrat skripty](./media/pre-post-scripts/select-scripts.png)

Vyberte skript, který chcete použít. v tomto příkladu jste použili sadu Runbook **UpdateManagement-TurnOnVms** . Když vyberete Runbook, otevře se stránka **Konfigurace skriptu** , zvolte **předzálohovací skript**. Po dokončení klikněte na **OK** .

Tento postup opakujte pro skript **UpdateManagement-TurnOffVms** . Když ale zvolíte **typ skriptu**, zvolte **pozálohovací skript**.

V části **vybrané položky** se teď zobrazují jak vybrané skripty, tak i v případě, že je předzálohovací skript.

![Vybrané položky](./media/pre-post-scripts/selected-items.png)

Dokončete konfiguraci nasazení aktualizace.

Po dokončení nasazení aktualizace můžete zobrazit výsledky tak, že přejdete na **nasazení aktualizací** . Jak vidíte, je k dispozici stav předzálohovacího a pozálohovacího skriptu.

![Aktualizovat výsledky](./media/pre-post-scripts/update-results.png)

Kliknutím na Spustit nasazení aktualizace zobrazíte další podrobnosti ke skriptům pre a post. K dispozici je odkaz na zdroj skriptu v době běhu.

![Výsledky spuštění nasazení](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Předávání parametrů

Když nakonfigurujete předběžné a následné skripty, můžete předat parametry stejně jako při plánování Runbooku. Parametry jsou definovány v době vytváření nasazení aktualizace. Předběžné a následné skripty podporují následující typy:

* char
* bytové
* hmot
* dlouhou
* notaci
* konkrétní
* klepat
* Hodnotu
* řetezce

Pokud potřebujete jiný typ objektu, můžete ho přetypovat na jiný typ s vlastní logikou v sadě Runbook.

Kromě standardních parametrů Runbooku je k dispozici další parametr. Tento parametr je **SoftwareUpdateConfigurationRunContext**. Tento parametr je řetězec JSON, a pokud definujete parametr v rámci předplatného nebo post skriptu, automaticky se předává v nasazení aktualizace. Parametr obsahuje informace o nasazení aktualizace, které je podmnožinou informací vrácených [rozhraním API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) , v následující tabulce jsou uvedeny vlastnosti, které jsou k dispozici v proměnné:


### <a name="softwareupdateconfigurationruncontext-properties"></a>Vlastnosti SoftwareUpdateConfigurationRunContext

|Vlastnost  |Popis  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Název konfigurace aktualizace softwaru        |
|SoftwareUpdateConfigurationRunId     | Jedinečné ID pro běh.        |
|SoftwareUpdateConfigurationSettings     | Kolekce vlastností souvisejících s konfigurací aktualizace softwaru         |
|SoftwareUpdateConfigurationSettings. operatingSystem     | Operační systémy cílené na nasazení aktualizace         |
|SoftwareUpdateConfigurationSettings. Duration     | Maximální trvání nasazení aktualizace spouštěné jako `PT[n]H[n]M[n]S` jako na ISO8601, označovaného také jako časové období údržby          |
|SoftwareUpdateConfigurationSettings. Windows     | Kolekce vlastností souvisejících s počítači se systémem Windows         |
|SoftwareUpdateConfigurationSettings. Windows. excludedKbNumbers     | Seznam aktualizací KB, které jsou vyloučené z nasazení aktualizace        |
|SoftwareUpdateConfigurationSettings. Windows. includedUpdateClassifications     | Klasifikace aktualizací vybrané pro nasazení aktualizace        |
|SoftwareUpdateConfigurationSettings. Windows. rebootSetting     | Nastavení restartování pro nasazení aktualizace        |
|azureVirtualMachines     | Seznam resourceId pro virtuální počítače Azure v nasazení aktualizace        |
|nonAzureComputerNames|Seznam plně kvalifikovaných názvů domén počítačů mimo Azure v nasazení aktualizace|

V následujícím příkladu je řetězec formátu JSON předaný parametru **SoftwareUpdateConfigurationRunContext** :

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

Úplný příklad se všemi vlastnostmi najdete v těchto umístěních: [Konfigurace aktualizace softwaru – získat podle názvu](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> Objekt `SoftwareUpdateConfigurationRunContext` může obsahovat duplicitní položky pro počítače. To může způsobit, že se ve stejném počítači několikrát spustí skripty spouštěné ve více než jednom. Pokud chcete toto chování vyřešit, pomocí `Sort-Object -Unique` vyberte ve svém skriptu jenom jedinečné názvy virtuálních počítačů.


## <a name="stopping-a-deployment"></a>Zastavení nasazení

Pokud chcete zastavit nasazení na základě předzálohovacího skriptu, je nutné [vyvolat](automation-runbook-execution.md#throw) výjimku. Pokud nevyvoláte výjimku, nasazování a následného skriptu se pořád spustí. Následující fragment kódu ukazuje, jak vyvolat výjimku.

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

## <a name="samples"></a>Ukázky

Ukázky předběžných a koncových skriptů najdete v [galerii Center Script](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)nebo naimportované prostřednictvím Azure Portal. Pokud je chcete importovat přes portál, v účtu Automation v části **Automatizace procesu**vyberte **Galerie runbooků**. Pro filtr použijte **Update Management** .

![Seznam galerie](./media/pre-post-scripts/runbook-gallery.png)

Můžete je také vyhledat podle názvu skriptu, jak je vidět v následujícím seznamu:

* Update Management – zapnout virtuální počítače
* Update Management – vypnutí virtuálních počítačů
* Update Management – spustit skript místně
* Update Management – šablona pro skripty před/po
* Update Management – spuštění skriptu pomocí příkazu Run

> [!IMPORTANT]
> Po importu runbooků je musíte **publikovat** , aby bylo možné je použít. Pokud to chcete udělat, najděte Runbook ve vašem účtu Automation, vyberte **Upravit**a klikněte na **publikovat**.

Ukázky jsou všechny na základě základní šablony, která je definována v následujícím příkladu. Tato šablona se dá použít k vytvoření vlastní sady Runbook pro použití s předběžnými a následnými skripty. K dispozici je nezbytná logika pro ověřování pomocí Azure a zpracování parametru `SoftwareUpdateConfigurationRunContext`.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management Pre/Post scripts.
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

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>Interakce s počítači

Předběžná a následná úloha se spouští jako sada Runbook v účtu Automation a ne přímo na počítačích ve vašem nasazení. Předběžné a následné úkoly se také spouštějí v kontextu Azure a nemají přístup k počítačům mimo Azure. V následujících částech se dozvíte, jak můžete s počítači pracovat přímo bez ohledu na to, zda se jedná o virtuální počítač Azure nebo počítač mimo Azure:

### <a name="interacting-with-azure-machines"></a>Interakce s počítači s Azure

Předběžné a následné úkoly se spouštějí jako Runbooky a nativně se nespouštějí na virtuálních počítačích Azure ve vašem nasazení. Abyste mohli pracovat s virtuálními počítači Azure, musíte mít následující položky:

* Účet Spustit jako
* Runbook, který chcete spustit

Pro interakci s počítači s Azure byste měli použít rutinu [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) k interakci s virtuálními počítači Azure. Příklad toho, jak to udělat, najdete v ukázce Runbooku [Update Management-Run Script with Run Command](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interakce s počítači mimo Azure

Předběžná a následná úloha běží v kontextu Azure a nemají přístup k počítačům mimo Azure. Abyste mohli pracovat s počítači mimo Azure, musíte mít následující položky:

* Účet Spustit jako
* Hybrid Runbook Worker nainstalované v počítači
* Sada Runbook, kterou chcete spustit místně
* Nadřazený Runbook

Aby bylo možné pracovat s počítači mimo Azure, je spuštěn nadřazený Runbook v kontextu Azure. Tato sada runbook volá podřízený Runbook pomocí rutiny [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Je nutné zadat parametr `-RunOn` a zadat název Hybrid Runbook Worker, na kterém se má skript spustit. Příklad naleznete v tématu Ukázka Runbooku [Update Management-Run Script lokálně](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Přerušit nasazení opravy

Pokud předchozí skript vrátí chybu, může být vhodné přerušit nasazení. Chcete-li to provést, musíte ve svém skriptu [vyvolat](/powershell/module/microsoft.powershell.core/about/about_throw) chybu pro všechny logiky, které by představovaly selhání.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Známé problémy

* Při použití předběžných a pozálohovacích skriptů nelze do parametrů předat logické hodnoty, objekty ani pole. Sada Runbook selže. Úplný seznam podporovaných typů naleznete v tématu [Parameters](#passing-parameters).

## <a name="next-steps"></a>Další kroky

Přejděte k kurzu, kde se dozvíte, jak spravovat aktualizace pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Správa aktualizací a oprav pro virtuální počítače Azure s Windows](automation-tutorial-update-management.md)

