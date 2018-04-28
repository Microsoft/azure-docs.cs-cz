---
title: Ověření konfigurace účtu Azure Automation.
description: Tento článek popisuje způsob ověření, že konfigurace vašeho účtu Automation je nastavená správně.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6b8e1f2923657965f4bab89e7a0f5f08faa1d27e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Test ověření účtu Azure Automation Spustit jako
Po úspěšném vytvoření účtu Automation můžete provést jednoduchý test a zkontrolovat, že se jde úspěšně ověřit v nasazení Azure Resource Manager nebo Azure Classic pomocí nově vytvořeného nebo aktualizovaného účtu Automation Spustit jako.    

## <a name="automation-run-as-authentication"></a>Ověření pomocí účtu Automation Spustit jako
Pomocí níže uvedeného ukázkového kódu [vytvořte powershellový runbook](automation-creating-importing-runbook.md), který bude kontrolovat ověřování pomocí účtu Spustit jako a také ve vašich vlastních runboocích ověřovat a spravovat prostředky Resource Manageru pomocí vašeho účtu služby Automation.   

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Connect-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Všimněte si, rutina, kterou používá pro ověřování v sadě runbook - **Connect-AzureRmAccount**, používá *ServicePrincipalCertificate* sadu parametrů.  Ověřování provádí pomocí certifikátu objektu služby a ne pomocí přihlašovacích údajů.  

Při můžete [spuštění sady runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) k ověření účtu spustit jako [úlohy runbooku](automation-runbook-execution.md) vytvoření úlohy se zobrazí stránka a stav úlohy se zobrazí v **Souhrn úlohy** dlaždici. Počáteční stav úlohy bude *Zařazeno ve frontě*. To označuje, že čekáte na zpřístupnění pracovního procesu runbooku v cloudu. Když pracovní proces úlohu přijme, změní se stav na *Spouštění*, a když se runbook skutečně spustí, změní se na *Spuštěno*.  Po dokončení úlohy runbooku by se měla zobrazit zpráva **Dokončeno**.

Pokud chcete zobrazit podrobné výsledky runbooku, klikněte na dlaždici **Výstup**.  Na stránce **Výstup** byste měli vidět, že se úspěšně ověřil a vrátil seznam všech prostředků ve všech skupinách prostředků v rámci vašeho předplatného.  

Jenom nezapomeňte odebrat blok kódu začínající komentářem `#Get all ARM resources from all resource groups`, pokud kód znovu použijete pro vlastní runbooky.

## <a name="classic-run-as-authentication"></a>Ověření pomocí účtu Spustit jako pro Azure Classic
Pomocí níže uvedeného ukázkového kódu [vytvořte powershellový runbook](automation-creating-importing-runbook.md), který bude kontrolovat ověřování pomocí účtu Spustit jako pro Azure Classic a také ve vašich vlastních runboocích ověřovat a spravovat prostředky v modelu nasazení Classic.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

Když [spustíte runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) pro ověření účtu Spustit jako, vytvoří se [úloha runbooku](automation-runbook-execution.md), zobrazí se stránka Úloha a na dlaždici **Souhrn úlohy** se zobrazí stav úlohy. Počáteční stav úlohy bude *Zařazeno ve frontě*. To označuje, že čekáte na zpřístupnění pracovního procesu runbooku v cloudu. Když pracovní proces úlohu přijme, změní se stav na *Spouštění*, a když se runbook skutečně spustí, změní se na *Spuštěno*.  Po dokončení úlohy runbooku by se měla zobrazit zpráva **Dokončeno**.

Pokud chcete zobrazit podrobné výsledky runbooku, klikněte na dlaždici **Výstup**.  Na stránce **Výstup** byste měli vidět, že se úspěšně ověřil a vrátil seznam všech virtuálních počítačů Azure nasazených ve vašem předplatném podle názvu.  

Jenom nezapomeňte odebrat rutinu **Get-AzureVM**, pokud kód znovu použijete pro vlastní runbooky.

## <a name="next-steps"></a>Další postup
* První kroky s powershellovými runbooky najdete v článku [Můj první powershellový runbook](automation-first-runbook-textual-powershell.md).
* Další informace o vytváření grafického obsahu najdete v článku [Vytváření grafického obsahu v Azure Automation](automation-graphical-authoring-intro.md).
