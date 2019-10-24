---
title: Konfigurace předzálohovacích skriptů v nasazení Update Management v Azure a jejich následné spouštění
description: Tento článek popisuje, jak nakonfigurovat a spravovat předzálohovací skripty a následné skripty pro nasazení aktualizací.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 860eaf5d37b3d3064e3b10bd1dab02c04b95ab5b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755516"
---
# <a name="manage-pre-and-post-scripts"></a>Správa předzálohovacích a pozálohovacích skriptů

Předzálohovací a pozálohovací skripty umožňují spouštět Runbooky PowerShellu v účtu Azure Automation před (před úlohou) a po (po dokončení) nasazení aktualizace. Předzálohovací a pozálohovací skripty běží v kontextu Azure, ne místně. Předzálohovací skripty běží na začátku nasazení aktualizace. Následné skripty se spouští na konci nasazení a po všech nakonfigurovaných restartováních.

## <a name="runbook-requirements"></a>Požadavky na Runbook

Aby se sada Runbook mohla použít jako předzálohovací nebo pozálohovací skript, musí být sada Runbook naimportována do vašeho účtu Automation a publikována. Další informace o tomto procesu najdete v tématu [publikování Runbooku](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Použití předzálohovacího skriptu nebo pozálohovacího skriptu

Pokud chcete použít předzálohovací skript nebo pozálohovací skript v nasazení aktualizace, začněte tím, že vytvoříte nasazení aktualizace. Vyberte **pre-Scripts + post-Scripts**. Tato akce otevře stránku **Vybrat před skripty a po skriptu** .

![Vybrat skripty](./media/pre-post-scripts/select-scripts.png)

Vyberte skript, který chcete použít. V tomto příkladu používáme sadu Runbook **UpdateManagement-TurnOnVms** . Když vyberete Runbook, otevře se stránka **Konfigurace skriptu** . Vyberte možnost **před skriptem**a pak vyberte **OK**.

Tento postup opakujte pro skript **UpdateManagement-TurnOffVms** . Když ale zvolíte **typ skriptu**, vyberte **pozálohovací skript**.

V části **vybrané položky** se teď zobrazují oba vybrané skripty. Jeden je předzálohovací skript a druhý je po skriptu:

![Vybrané položky](./media/pre-post-scripts/selected-items.png)

Dokončete konfiguraci nasazení aktualizace.

Po dokončení nasazení aktualizace můžete zobrazit výsledky tak, že přejdete na **nasazení aktualizací** . Jak vidíte, stav je k dispozici pro předzálohovací a pozálohovací skript:

![Aktualizovat výsledky](./media/pre-post-scripts/update-results.png)

Když vyberete nasazení aktualizace, zobrazí se další podrobnosti ke skriptům pre a post. K dispozici je odkaz na zdroj skriptu v době běhu.

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

Kromě standardních parametrů Runbooku je k dispozici další parametr: **SoftwareUpdateConfigurationRunContext**

Tento parametr je řetězec JSON, a pokud definujete parametr v předzálohovacím nebo pozálohovacím skriptu, automaticky se předává v nasazení aktualizace. Parametr obsahuje informace o nasazení aktualizace, což je podmnožina informací vrácených [rozhraním API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). 

V následující tabulce jsou uvedeny vlastnosti, které jsou k dispozici v proměnné.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Vlastnosti SoftwareUpdateConfigurationRunContext

|Vlastnost  |Popis  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Název konfigurace aktualizace softwaru.        |
|SoftwareUpdateConfigurationRunId     | Jedinečné ID pro běh.        |
|SoftwareUpdateConfigurationSettings     | Kolekce vlastností souvisejících s konfigurací aktualizace softwaru.         |
|SoftwareUpdateConfigurationSettings. operatingSystem     | Operační systémy cílené pro nasazení aktualizace.         |
|SoftwareUpdateConfigurationSettings. Duration     | Maximální trvání nasazení aktualizace spouštěné jako `PT[n]H[n]M[n]S` podle ISO8601; nazývá se také časový *interval pro správu a údržbu*.          |
|SoftwareUpdateConfigurationSettings. Windows     | Kolekce vlastností souvisejících s počítači se systémem Windows.         |
|SoftwareUpdateConfigurationSettings. Windows. excludedKbNumbers     | Seznam aktualizací KB, které jsou vyloučené z nasazení aktualizace.        |
|SoftwareUpdateConfigurationSettings. Windows. includedUpdateClassifications     | Klasifikace aktualizací vybrané pro nasazení aktualizace        |
|SoftwareUpdateConfigurationSettings. Windows. rebootSetting     | Nastavení restartování pro nasazení aktualizace.        |
|azureVirtualMachines     | Seznam resourceId pro virtuální počítače Azure v nasazení aktualizace.        |
|nonAzureComputerNames|Seznam plně kvalifikovaných názvů domén počítačů mimo Azure v nasazení aktualizace.|

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

Úplný příklad se všemi vlastnostmi najdete v: [získání konfigurace aktualizace softwaru podle názvu](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Objekt `SoftwareUpdateConfigurationRunContext` může obsahovat duplicitní položky pro počítače. To může způsobit, že se ve stejném počítači několikrát spustí předběžná a pozálohovací skript. Pokud chcete toto chování obejít, použijte `Sort-Object -Unique` k výběru jenom jedinečných názvů virtuálních počítačů ve vašem skriptu.


## <a name="stopping-a-deployment"></a>Zastavení nasazení

Pokud chcete zastavit nasazení na základě předzálohovacího skriptu, je nutné [vyvolat](automation-runbook-execution.md#throw) výjimku. Pokud to neuděláte, nasazení a pozálohovací skript se pořád spustí. Následující fragment kódu ukazuje, jak vyvolat výjimku.

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

Ukázky pro předzálohovací a pozálohovací skripty najdete v [galerii Center skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) a v [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), nebo je můžete importovat pomocí Azure Portal. Uděláte to tak, že v účtu Automation v části **Automatizace procesu**vyberete **Galerie runbooků**. Pro filtr použijte **Update Management** .

![Seznam galerie](./media/pre-post-scripts/runbook-gallery.png)

Můžete je také vyhledat podle názvu skriptu, jak je znázorněno v následujícím seznamu:

* Update Management – zapnout virtuální počítače
* Update Management – vypnutí virtuálních počítačů
* Update Management – spustit skript místně
* Update Management – šablona pro skripty před/po
* Update Management – spuštění skriptu pomocí příkazu Run

> [!IMPORTANT]
> Po importu runbooků je musíte publikovat, aby bylo možné je použít. Provedete to tak, že v účtu Automation vyhledáte Runbook, vyberete **Upravit**a pak vyberete **publikovat**.

Ukázky jsou všechny na základě základní šablony, která je definována v následujícím příkladu. Tato šablona se dá použít k vytvoření vlastní sady Runbook pro použití s předběžnými a následnými skripty. K dispozici je nezbytná logika pro ověřování pomocí Azure a zpracování parametru `SoftwareUpdateConfigurationRunContext`.

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

Předběžná a následná úloha se spouští jako sada Runbook v účtu Automation a ne přímo na počítačích ve vašem nasazení. Předběžné a následné úkoly se také spouštějí v kontextu Azure a nemají přístup k počítačům mimo Azure. V následujících částech se dozvíte, jak můžete s počítači pracovat přímo, ať už jde o virtuální počítače Azure nebo počítače mimo Azure.

### <a name="interacting-with-azure-machines"></a>Interakce s počítači s Azure

Předběžné a následné úkoly se spouštějí jako Runbooky a nativně se nespouštějí na virtuálních počítačích Azure ve vašem nasazení. Abyste mohli pracovat s virtuálními počítači Azure, musíte mít následující položky:

* Účet Spustit jako
* Runbook, který chcete spustit

Pro interakci s počítači s Azure byste měli použít rutinu [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) k interakci s virtuálními počítači Azure. Příklad toho, jak to udělat, najdete v ukázce Runbooku [Update Management – spuštění skriptu pomocí příkazu Run](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interakce s počítači mimo Azure

Předběžná a následná úloha běží v kontextu Azure a nemají přístup k počítačům mimo Azure. Abyste mohli pracovat s počítači mimo Azure, musíte mít následující položky:

* Účet Spustit jako
* Hybrid Runbook Worker nainstalované v počítači
* Sada Runbook, kterou chcete spustit místně
* Nadřazený Runbook

Aby bylo možné pracovat s počítači mimo Azure, je spuštěn nadřazený Runbook v kontextu Azure. Tato sada runbook volá podřízený Runbook pomocí rutiny [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Je nutné zadat parametr `-RunOn` a zadat název Hybrid Runbook Worker, na kterém se má skript spustit. Další informace naleznete v ukázce Runbooku [Update Management – spusťte skript místně](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Přerušit nasazení opravy

Pokud předzálohovací skript vrátí chybu, může být vhodné zrušit nasazení. Abyste to mohli udělat, musíte ve svém skriptu [vyvolat](/powershell/module/microsoft.powershell.core/about/about_throw) chybu pro všechny logiky, které by představovaly selhání.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Známé problémy

* Pokud používáte předběžné a následné skripty, nelze do parametrů předat logické hodnoty, objekty ani pole. Pokud to uděláte, sada Runbook se nezdařila. Úplný seznam podporovaných typů naleznete v tématu [předávání parametrů](#passing-parameters).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak spravovat aktualizace pro virtuální počítače s Windows, Projděte si následující kurz:

> [!div class="nextstepaction"]
> [Správa aktualizací a oprav pro virtuální počítače Azure s Windows](automation-tutorial-update-management.md)

