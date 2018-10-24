---
title: Nakonfigurujte skripty před a po nasazení správy aktualizací v Azure (Preview)
description: Tento článek popisuje, jak konfigurovat a spravovat před a po skriptů pro nasazení aktualizací
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 245cbd86ae43560e4e41f4b97350f9a5857e1b25
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956610"
---
# <a name="manage-pre-and-post-scripts-preview"></a>Spravovat skripty před a po (Preview)

Skripty před a po umožňují spustit Powershellové runbooky ve vašem účtu Automation před (předběžné úkolu) a po nasazení (po úloh) aktualizace. Skripty před a po spuštění v rámci Azure a ne místně. Spustit skripty před začátkem nasazení aktualizace. Spustit skripty příspěvek na konci nasazení a po žádné restartování počítače, které jsou nakonfigurované.

## <a name="runbook-requirements"></a>Požadavky na sady Runbook

Sady runbook má být použit jako před nebo po skriptu že runbook bude potřebovat lze importovat do účtu automation a publikovaná. Další informace o tomto procesu najdete v tématu [publikování runbooku](automation-creating-importing-runbook.md#publishing-a-runbook).

## <a name="using-a-prepost-script"></a>Pomocí předzálohovacího nebo pozálohovacího skriptu

Chcete-li používat skript a před nebo po nasazení aktualizace, jednoduše začněte tím, že vytvoření nasazení aktualizace. Vyberte **předzálohovacích skriptů a skripty Post (Preview)**. Tím se otevře **vyberte předzálohovacích skriptů a pozálohovacích skriptů** stránky.  

![Vyberte skriptů](./media/pre-post-scripts/select-scripts.png)

Vyberte skript, který chcete použít v tomto příkladu, můžete použít **UpdateManagement TurnOnVms** sady runbook. Když vyberte sadu runbook **konfigurace skriptu** otevře se stránka, zadejte hodnoty pro parametry a zvolte **předzálohovací skript**. Klikněte na tlačítko **OK** až budete hotovi.

![Konfigurace skriptu](./media/pre-post-scripts/configure-script.png)

Opakujte tento postup u **UpdateManagement TurnOffVms** skriptu. Ale při volbě **typ skriptu**, zvolte **pozálohovací skript**.

**Vybraných položek** části nyní zobrazuje obě skripty vybrané a na se předzálohovací skript a druhý je pozálohovací skript.

![Vybrané položky](./media/pre-post-scripts/selected-items.png)

Dokončení konfigurace nasazení aktualizace.

Po dokončení aktualizace nasazení můžete přejít na **nasazení aktualizací** zobrazíte výsledky. Jak vidíte stav předsnímkových a posnímkových skriptů jsou zadané.

![Aktualizovat výsledky](./media/pre-post-scripts/update-results.png)

Po kliknutí do nasazení aktualizací, kterým jsou k dispozici další podrobnosti, aby skripty před a po. Odkaz na zdroj skriptu v době běhu je k dispozici.

![Výsledky spustit nasazení](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Předávání parametrů

Když konfigurujete před a po skripty, které můžete předat parametry stejně, jako je plánování runbooku. Parametry jsou definované v době vytvoření nasazení aktualizace. Kromě standardní sada runbook parametry je k dispozici další parametr. Tento parametr je **SoftwareUpdateConfigurationRunContext**. Tento parametr je řetězec formátu JSON, a pokud definujete ve skriptu před nebo po parametru, je automaticky předána v nasazení aktualizací. Parametr obsahuje informace o nasazení aktualizace, která je podmnožinou vrácených podle informací [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) v následující tabulce se dozvíte, vlastnosti, které jsou k dispozici v proměnné:

### <a name="softwareupdateconfigurationruncontext-properties"></a>Vlastnosti SoftwareUpdateConfigurationRunContext

|Vlastnost  |Popis  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Název konfigurace aktualizace softwaru        |
|SoftwareUpdateConfigurationRunId     | Jedinečné id spuštění.        |
|SoftwareUpdateConfigurationSettings     | Kolekce vlastností související s konfigurací aktualizací softwaru         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Operační systémy, je určená pro nasazení aktualizace         |
|SoftwareUpdateConfigurationSettings.duration     | Maximální doba trvání nasazení aktualizace spustit jako `PT[n]H[n]M[n]S` podle ISO8601, také nazývané "okno údržby"          |
|SoftwareUpdateConfigurationSettings.Windows     | Kolekce vlastností související s počítači s Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Seznam aktualizací KB, které jsou vyloučeny z aktualizace nasazení        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Klasifikacích aktualizací vybrané pro nasazení aktualizace        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Restartování nastavení pro nasazení aktualizace        |
|azureVirtualMachines     | Seznam ID prostředků: pro virtuální počítače Azure v nasazení aktualizací        |
|nonAzureComputerNames|Seznam počítačů mimo Azure plně kvalifikovaných názvů domén v nasazení aktualizací|

Následuje příklad předané do řetězce JSON **SoftwareUpdateConfigurationRunContext** parametr:

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

Kompletní příklad se všechny vlastnosti najdete tady: [konfigurace aktualizace softwaru - získat podle názvu](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> Počítače přidat do nasazení s použitím [dynamické skupiny (preview)](automation-update-management.md#using-dynamic-groups) nejsou aktuálně součástí **SoftwareUpdateConfigurationRunContext** parametru.

## <a name="samples"></a>Ukázky

Ukázky pro skripty před a po najdete v [Galerie centra skriptů](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), nebo importovat na webu Azure portal. V části Import prostřednictvím portálu, ve vašem účtu Automation **automatizace procesů**vyberte **Galerie Runbooků**. Použití **Update Management** filtru.

![Seznam galerie](./media/pre-post-scripts/runbook-gallery.png)

Nebo můžete vyhledat je podle názvu skriptu jak je znázorněno v následujícím seznamu:

* Správa aktualizací - zapnout na virtuálních počítačích
* Správa aktualizací - vypněte virtuální počítače
* Správa aktualizací – spustit skript místně
* Update Management – šablony Předzálohovacího nebo Pozálohovacího skriptů
* Správa aktualizací - spuštění skriptu pomocí spuštění příkazu

> [!IMPORTANT]
> Jakmile dokončíte import sady runbook, je nutné **publikovat** ještě dříve, než je možné. Provedete to, který najít sady runbook ve vašem účtu Automation vyberte **upravit**a klikněte na tlačítko **publikovat**.

Ukázky jsou založeny na základní šablony, která je definována v následujícím příkladu. Tato šablona slouží k vytvoření vlastní sady runbook pomocí skriptů před a po. Nezbytnou logiku pro ověřování s Azure, stejně jako zpracování `SoftwareUpdateConfigurationRunContext` parametru jsou zahrnuty.

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
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines 
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

## <a name="interacting-with-non-azure-machines"></a>Interakce s počítače mimo Azure

Úlohy před a po spuštění v rámci Azure a nebudou mít přístup pro počítače mimo Azure. Aby bylo možné pracovat s počítače mimo Azure musíte mít následující:

* Účet Spustit jako
* Funkce hybrid Runbook Worker nainstalován na počítači
* Sady runbook, kterou chcete spustit místně
* Nadřízený runbook

K interakci s počítače mimo Azure, je nadřazená sada runbook spustil v rámci Azure. Tato sada runbook volá podřízeného runbooku se [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) rutiny. Je nutné zadat `-RunOn` parametr a zadejte název funkce Hybrid Runbook Worker pro spuštění skriptu.

```powershell
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$resourceGroup = "AzureAutomationResourceGroup"
$aaName = "AzureAutomationAccountName"

$output = Start-AzureRmAutomationRunbook -Name "StartService" -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName -RunOn "hybridWorker"

$status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
while ($status.status -ne "Completed")
{ 
    Start-Sleep -Seconds 5
    $status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
}

$summary = Get-AzureRmAutomationJobOutput -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName

if ($summary.Type -eq "Error")
{
    Write-Error -Message $summary.Summary
}
```

## <a name="known-issues"></a>Známé problémy

* Pro parametry nelze předat objekty nebo pole, při použití skriptů před a po. Runbook se nezdaří.
* Sady Runbook, které nejsou publikovány se zobrazí při výběru skriptů před a po jako volitelnou. Jenom runbooky, které jsou publikovány je třeba zvolit, jak zrušit publikované sady runbook nelze spustit a se nezdaří.
* Počítače přidat do nasazení s použitím [dynamické skupiny (preview)](automation-update-management.md#using-dynamic-groups) nejsou aktuálně součástí **SoftwareUpdateConfigurationRunContext** parametr, který je předán do skriptů před a po.

## <a name="next-steps"></a>Další postup

Pokračujte ke kurzu se naučíte spravovat aktualizace pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Správa aktualizací a oprav pro virtuální počítače Azure s Windows](automation-tutorial-update-management.md)
