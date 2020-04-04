---
title: Konfigurace předa po skriptech v nasazení správy aktualizací v Azure
description: Tento článek popisuje, jak konfigurovat a spravovat předběžné skripty a postskripty pro nasazení aktualizací.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: bbf7f2945ad6a94c51cfd0c7db1e8c85d739c6ed
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631624"
---
# <a name="manage-pre-and-post-scripts"></a>Správa předa skriptů a po skriptech

Předskripty a postskripty umožňují spouštět runbooky PowerShellu ve vašem účtu Azure Automation před (před úlohou) a po nasazení (po úkolu) nasazení mažete. Před a po skripty spustit v kontextu Azure, nikoli místně. Předběžné skripty jsou spuštěny na začátku nasazení aktualizace. Post-skripty spustit na konci nasazení a po všech restartování, které jsou nakonfigurovány.

## <a name="runbook-requirements"></a>Požadavky sady Runbook

Aby se runbook použil jako před nebo po skriptu, musí být runbook importován do vašeho účtu Automation a publikován. Další informace o tomto procesu najdete [v tématu Publikování runbooku](manage-runbooks.md#publishing-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Použití předskriptu nebo postskriptu

Chcete-li použít předskript nebo post-script v nasazení aktualizace, začněte vytvořením nasazení aktualizace. Vyberte **možnost Předběžné skripty + Post-Skripty**. Tato akce otevře stránku **Vybrat předběžné skripty + Post-skripty.**

![Výběr skriptů](./media/pre-post-scripts/select-scripts.png)

Vyberte skript, který chcete použít. V tomto příkladu používáme **updateManagement-TurnOnVms** runbook. Když vyberete runbook, otevře se stránka **Konfigurovat skript.** Vyberte **Možnost Předskriptování**a pak vyberte **OK**.

Tento postup opakujte pro skript **UpdateManagement-TurnOffVms.** Ale když zvolíte **typ skriptu**, vyberte **Post-Script**.

V části **Vybrané položky** se nyní zobrazí oba vybrané skripty. Jeden je pre-script a druhý je post-skript:

![Vybrané položky](./media/pre-post-scripts/selected-items.png)

Dokončete konfiguraci nasazení aktualizace.

Po dokončení nasazení aktualizace můžete přejít na **aktualizace nasazení** zobrazíte výsledky. Jak můžete vidět, stav je k dispozici pro pre-script a post-script:

![Aktualizovat výsledky](./media/pre-post-scripts/update-results.png)

Výběrem spuštění nasazení aktualizace se zobrazí další podrobnosti o předa po skriptech. Je k dispozici odkaz na zdroj skriptu v době spuštění.

![Výsledky spuštění nasazení](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Předávání parametrů

Při konfiguraci před a po skripty, můžete předat parametry, stejně jako plánování runbook. Parametry jsou definovány v době vytvoření nasazení aktualizace. Předa po skripty podporují následující typy:

* To je v pořádku.
* [bajt]
* [int]
* To je v pořádku.
* [desetinná]
* [svobodná]
* To je v pořádku.
* [DateTime]
* [řetězec]

Pokud potřebujete jiný typ objektu, můžete jej přetypovat na jiný typ s vlastní logikou v runbooku.

Kromě standardních parametrů sady Runbook je k dispozici další parametr: **SoftwareUpdateConfigurationRunContext**

Tento parametr je řetězec JSON a pokud definujete parametr v před nebo post-script, je automaticky předán nasazení aktualizace. Parametr obsahuje informace o nasazení aktualizace, což je podmnožina informací vrácených [rozhraním API SoftwareUpdateconfigurations .](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) 

V následující tabulce jsou uvedeny vlastnosti, které jsou k dispozici v proměnné.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Vlastnosti SoftwareUpdateConfigurationRunContext

|Vlastnost  |Popis  |
|---------|---------|
|Název konfigurace aktualizace softwaru     | Název konfigurace aktualizace softwaru.        |
|SoftwareUpdateConfigurationRunId     | Jedinečné ID pro spuštění.        |
|SoftwareUpdateConfigurationSettings     | Kolekce vlastností souvisejících s konfigurací aktualizace softwaru.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Operační systémy určené pro nasazení aktualizace.         |
|SoftwareUpdateConfigurationSettings.duration     | Maximální doba nasazení aktualizace bude `PT[n]H[n]M[n]S` spuštěna podle ISO8601; také nazývá *okno údržby*.          |
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
> Objekt `SoftwareUpdateConfigurationRunContext` může obsahovat duplicitní položky pro počítače. To může způsobit, že před a po skripty spustit vícekrát na stejném počítači. Chcete-li toto `Sort-Object -Unique` chování obejít, použijte k výběru pouze jedinečné názvy virtuálních počítačů ve skriptu.


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

## <a name="samples"></a>ukázky

Ukázky pro předa po skripty najdete v [Galerii Centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) a [PowerShell Galerie](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), nebo je můžete importovat prostřednictvím portálu Azure. Chcete-li to provést, vyberte v části **Automatizace procesů**v části Automatizace procesů **v galerii sad Runbook**. Pro filtr použijte **správu aktualizací.**

![Seznam galerií](./media/pre-post-scripts/runbook-gallery.png)

Nebo je můžete vyhledat podle názvu skriptu, jak je znázorněno v následujícím seznamu:

* Správa aktualizací – zapnutí virtuálních počítačů
* Správa aktualizací – vypnutí virtuálních měn
* Správa aktualizací – spuštění skriptu místně
* Správa aktualizací – šablona pro skripty před/po
* Správa aktualizací – spuštění skriptu pomocí příkazu Spustit

> [!IMPORTANT]
> Po importu sad Runbook je nutné je před použitím publikovat. Chcete-li to provést, vyhledejte runbook v účtu Automation, vyberte **Upravit**a pak vyberte **Publikovat**.

Ukázky jsou založeny na základní šabloně, která je definována v následujícím příkladu. Tuto šablonu lze použít k vytvoření vlastní runbook pro použití s pre a post-skripty. Obsahuje se potřeba logika pro ověřování `SoftwareUpdateConfigurationRunContext` pomocí Azure a zpracování parametru.

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

## <a name="interacting-with-machines"></a>Interakce se stroji

Předa po úlohy spustit jako runbook ve vašem účtu automatizace a ne přímo na počítačích ve vašem nasazení. Předa po úkolech se taky spouštějí v kontextu Azure a nemají přístup k počítačům, které nejsou azure. Následující části ukazují, jak můžete pracovat s počítači přímo, ať už jsou to virtuální počítače Azure nebo počítače než Azure.

### <a name="interacting-with-azure-machines"></a>Interakce s počítači Azure

Předa po úlohy se spouštějí jako runbooky a nespouštějí se nativně na virtuálních počítačích Azure ve vašem nasazení. Chcete-li pracovat s virtuálními počítači Azure, musíte mít následující položky:

* Účet Spustit jako
* Runbook, který chcete spustit

Chcete-li pracovat s počítači Azure, měli byste použít rutinu [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) k interakci s virtuálními počítači Azure. Příklad, jak to provést, naleznete v příkladu sady Runbook [Update Management – spuštění skriptu s příkazem Spustit](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interakce s počítači, které nejsou azure

Předa po úkolech se spouštějí v kontextu Azure a nemají přístup k počítačům, které nejsou azure. Chcete-li pracovat s počítači, které nejsou Azure, musíte mít následující položky:

* Účet Spustit jako
* Hybridní pracovník runbooku nainstalovaný v počítači
* Runbook, který chcete spustit místně
* Nadřazená runbook

Pro interakci s počítači, které nejsou azure, se nadřazený runbook spouštěný v kontextu Azure. Tato runbook volá podřízený runbook s rutinou [Start-AzureRmAutomationRunbook.](/powershell/module/azurerm.automation/start-azurermautomationrunbook) Je nutné `-RunOn` zadat parametr a zadat název hybridního pracovníka runbooku, na který má být skript spuštěn. Další informace najdete v tématu runbook příklad [Update Management – spusťte skript místně](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Přerušit nasazení oprav

Pokud předskript vrátí chybu, můžete přerušit nasazení. Chcete-li to provést, je nutné [vyvolat](/powershell/module/microsoft.powershell.core/about/about_throw) chybu ve skriptu pro všechny logiky, které by představovaly selhání.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Známé problémy

* Při použití předa postskriptů nelze předat logickým hodnotám, objektům nebo polím parametrům. Pokud tak učiníte, runbook se nezdaří. Úplný seznam podporovaných typů naleznete v tématu [Předávání parametrů](#passing-parameters).

## <a name="next-steps"></a>Další kroky

Přejděte k následujícímu kurzu, kde se dozvíte, jak spravovat aktualizace pro virtuální počítače s Windows:

> [!div class="nextstepaction"]
> [Správa aktualizací a oprav pro virtuální počítače Azure s Windows](automation-tutorial-update-management.md)

