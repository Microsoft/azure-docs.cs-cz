---
title: Správa předzálohovacích a pozálohovacích skriptů v nasazení Update Management v Azure
description: V tomto článku se dozvíte, jak nakonfigurovat a spravovat předzálohovací skripty a následné skripty pro nasazení aktualizací.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 32a8214a5bd6844c9177c7af159a453bc3673efc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450162"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Správa předzálohovacích a pozálohovacích skriptů

Předzálohovací a pozálohovací skripty jsou Runbooky, které se mají spustit ve vašem účtu Azure Automation před nasazením aktualizace (před úlohou) a po (po úloze). Předzálohovací a pozálohovací skripty běží v kontextu Azure, ne místně. Předzálohovací skripty běží na začátku nasazení aktualizace. Následné skripty se spouští na konci nasazení a po všech nakonfigurovaných restartováních.

## <a name="pre-script-and-post-script-requirements"></a>Požadavky na předchozí skript a pozálohovací skript

Aby se sada Runbook použila jako předzálohovací nebo pozálohovací skript, musíte ji naimportovat do svého účtu Automation a [sadu Runbook publikovat](../manage-runbooks.md#publish-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parametry před skriptem a po skriptu

Když konfigurujete předzálohovací skripty a následné skripty, můžete předat parametry stejně jako při plánování Runbooku. Parametry jsou definovány v době vytváření nasazení aktualizace. Předběžné skripty a pozálohovací skripty podporují následující typy:

* char
* bytové
* hmot
* dlouhou
* notaci
* konkrétní
* klepat
* Hodnotu
* řetezce

Parametry sady Runbook pre-Script a post-Script nepodporují typy logických, objektů a polí. Tyto hodnoty způsobí, že se Runbooky nezdařily. 

Pokud potřebujete jiný typ objektu, můžete ho přetypovat na jiný typ s vlastní logikou v sadě Runbook.

Kromě standardních parametrů Runbooku `SoftwareUpdateConfigurationRunContext` je k dispozici parametr (řetězec JSON typu String). Pokud definujete parametr v rámci předzálohovacího skriptu nebo sady Runbook po skriptu, automaticky se předává v nasazení aktualizace. Parametr obsahuje informace o nasazení aktualizace, což je podmnožina informací vrácených [rozhraním API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). Níže uvedené oddíly definují přidružené vlastnosti.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Vlastnosti SoftwareUpdateConfigurationRunContext

|Vlastnost  |Popis  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Název konfigurace aktualizace softwaru.        |
|SoftwareUpdateConfigurationRunId     | Jedinečné ID pro běh.        |
|SoftwareUpdateConfigurationSettings     | Kolekce vlastností souvisejících s konfigurací aktualizace softwaru.         |
|SoftwareUpdateConfigurationSettings. operatingSystem     | Operační systémy cílené pro nasazení aktualizace.         |
|SoftwareUpdateConfigurationSettings. Duration     | Maximální doba trvání nasazení aktualizace jako `PT[n]H[n]M[n]S` na ISO8601, nazývaná také časový interval pro správu a údržbu.          |
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
> `SoftwareUpdateConfigurationRunContext`Objekt může obsahovat duplicitní položky pro počítače. To může způsobit, že se ve stejném počítači několikrát spouští předběžné skripty a pozálohovací skripty. Pokud chcete toto chování obejít, použijte `Sort-Object -Unique` k výběru jenom jedinečných názvů virtuálních počítačů.

> [!NOTE]
> V současné době jsou podporovány pouze Runbooky PowerShellu jako předzálohovací skripty. Jiné typy runbooků, jako je Python, grafický, PowerShellový pracovní postup, se v tuto chvíli nepodporují jako předzálohovací skripty.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Použití předzálohovacího skriptu nebo pozálohovacího skriptu v nasazení

Pokud chcete použít předzálohovací skript nebo pozálohovací skript v nasazení aktualizace, začněte tím, že vytvoříte nasazení aktualizace. Vyberte **pre-Scripts + post-Scripts**. Tato akce otevře stránku **Vybrat před skripty a po skriptu** .

![Vybrat skripty](./media/update-mgmt-pre-post-scripts/select-scripts.png)

Vyberte skript, který chcete použít. V tomto příkladu používáme sadu Runbook **UpdateManagement-TurnOnVms** . Když vyberete Runbook, otevře se stránka **Konfigurace skriptu** . Vyberte možnost **před skriptem**a pak vyberte **OK**.

Tento postup opakujte pro skript **UpdateManagement-TurnOffVms** . Když ale zvolíte **typ skriptu**, vyberte **pozálohovací skript**.

V části **vybrané položky** se teď zobrazují oba vybrané skripty. Jeden je předzálohovací skript a druhý je po skriptu:

![Vybrané položky](./media/update-mgmt-pre-post-scripts/selected-items.png)

Dokončete konfiguraci nasazení aktualizace.

Po dokončení nasazení aktualizace můžete zobrazit výsledky tak, že přejdete na **nasazení aktualizací** . Jak vidíte, stav je k dispozici pro předzálohovací a pozálohovací skript:

![Aktualizovat výsledky](./media/update-mgmt-pre-post-scripts/update-results.png)

Když vyberete nasazení aktualizace, zobrazí se další podrobnosti o předběžných skriptech a po skriptech. K dispozici je odkaz na zdroj skriptu v době běhu.

![Výsledky spuštění nasazení](./media/update-mgmt-pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Zastavení nasazení

Pokud chcete zastavit nasazení na základě předzálohovacího skriptu, je nutné [vyvolat](../automation-runbook-execution.md#throw) výjimku. Pokud to neuděláte, nasazení a pozálohovací skript se pořád spustí. Následující fragment kódu ukazuje, jak vyvolat výjimku.

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

## <a name="interact-with-machines"></a>Interakce s počítači

Předzálohovací a následné úkoly spouštěné jako Runbooky v účtu Automation a ne přímo na počítačích ve vašem nasazení. Předběžné úkoly a následné úkoly se také spouštějí v kontextu Azure a nemají přístup k počítačům mimo Azure. V následujících částech se dozvíte, jak můžete s počítači pracovat přímo, ať už jde o virtuální počítače Azure nebo počítače mimo Azure.

### <a name="interact-with-azure-machines"></a>Interakce s počítači s Azure

Předběžné úlohy a následné úkoly se spouštějí jako Runbooky a nativně se nespouštějí na virtuálních počítačích Azure ve vašem nasazení. Abyste mohli pracovat s virtuálními počítači Azure, musíte mít následující položky:

* Účet Spustit jako
* Runbook, který chcete spustit

Pro interakci s počítači s Azure byste měli použít rutinu [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) k interakci s virtuálními počítači Azure. Příklad toho, jak to udělat, najdete v ukázce Runbooku [Update Management – spuštění skriptu pomocí příkazu Run](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interact-with-non-azure-machines"></a>Interakce s počítači mimo Azure

Předběžné úkoly a následné úkoly běží v kontextu Azure a nemají přístup k počítačům mimo Azure. Abyste mohli pracovat s počítači mimo Azure, musíte mít následující položky:

* Účet Spustit jako
* Hybrid Runbook Worker nainstalované v počítači
* Sada Runbook, kterou chcete spustit místně
* Nadřazený Runbook

Aby bylo možné pracovat s počítači mimo Azure, je spuštěn nadřazený Runbook v kontextu Azure. Tato sada runbook volá podřízený Runbook pomocí rutiny [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) . Musíte zadat `RunOn` parametr a zadat název Hybrid Runbook Worker, na kterém se má skript spustit. Podívejte se na příklad Runbooku [Update Management – spusťte skript místně](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Přerušit nasazení opravy

Pokud předzálohovací skript vrátí chybu, může být vhodné zrušit nasazení. Abyste to mohli udělat, musíte ve svém skriptu [vyvolat](/powershell/module/microsoft.powershell.core/about/about_throw) chybu pro všechny logiky, které by představovaly selhání.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>ukázky

Ukázky pro předzálohovací skripty a pozálohovací skripty najdete v [galerii centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) a v [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), nebo je můžete importovat prostřednictvím Azure Portal. Uděláte to tak, že v účtu Automation v části **Automatizace procesu**vyberete **Galerie runbooků**. Pro filtr použijte **Update Management** .

![Seznam galerie](./media/update-mgmt-pre-post-scripts/runbook-gallery.png)

Můžete je také vyhledat podle názvu skriptu, jak je znázorněno v následujícím seznamu:

* Update Management – zapnout virtuální počítače
* Update Management – vypnutí virtuálních počítačů
* Update Management – spustit skript místně
* Update Management – šablona pro skripty před/po
* Update Management – spuštění skriptu pomocí příkazu Run

> [!IMPORTANT]
> Po importu runbooků je musíte publikovat, aby bylo možné je použít. Provedete to tak, že v účtu Automation vyhledáte Runbook, vyberete **Upravit**a pak vyberete **publikovat**.

Ukázky jsou všechny na základě základní šablony, která je definována v následujícím příkladu. Tato šablona se dá použít k vytvoření vlastní sady Runbook pro použití s předzálohovacími skripty a po skriptu. K dispozici je nezbytná logika pro ověřování pomocí Azure a zpracování `SoftwareUpdateConfigurationRunContext` parametru.

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
> Pro negrafické Runbooky PowerShellu `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Tyto rutiny můžete použít nebo můžete [své moduly](../automation-update-azure-modules.md) v účtu Automation aktualizovat na nejnovější verze. Vaše moduly možná budete muset aktualizovat i v případě, že jste právě vytvořili nový účet Automation.

## <a name="next-steps"></a>Další kroky

* Podrobnosti o správě aktualizací najdete v tématu [Správa aktualizací a oprav pro vaše virtuální počítače](update-mgmt-manage-updates-for-vm.md).
