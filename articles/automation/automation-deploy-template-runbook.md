---
title: Nasadit šablonu Azure Resource Manager v runbooku automatizace Azure
description: Jak nasadit šablonu Azure Resource Manageru uložená v úložišti Azure ze sady runbook
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
keywords: prostředí PowerShell, sady runbook, json, služby azure automation
ms.openlocfilehash: 1767f2a3c14540344c2059f1f25bfc4825fa827b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Nasazení šablony Azure Resource Manageru v powershellovém runbooku Azure Automation

Můžete napsat [prostředí PowerShell Azure Automation runbook](automation-first-runbook-textual-powershell.md) které nasazuje prostředek služby Azure pomocí [šablony Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Díky tomu je možné automatizovat nasazení prostředků Azure. Je možné uchovávat vaše šablony správce prostředků v centrální a zabezpečené umístění, třeba úložiště Azure.

V tomto tématu, vytvoříme runbook prostředí PowerShell, která používá šablonu Resource Manageru uložená v [Azure Storage](../storage/common/storage-introduction.md) nasadit nový účet úložiště Azure.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud nemáte účet, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo <a href="/pricing/free-account/" target="_blank">[si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/).
* [Účet Automation](automation-sec-configure-azure-runas-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure.  Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* [Účet služby Azure Storage](../storage/common/storage-create-storage-account.md) pro uložení šablony Resource Manageru
* Azure Powershell nainstalovaný v místním počítači. V tématu [nainstalovat a nakonfigurovat Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) informace o tom, jak získat prostředí Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru

V tomto příkladu používáme šablonu Resource Manager, která nasadí nový účet úložiště Azure.

V textovém editoru zkopírujte následující text:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Uložte soubor místně jako `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Uložení šablony Resource Manageru v Azure Storage

Teď nemůžeme použít PowerShell k vytvoření sdílené složky Azure Storage a odeslat `TemplateTest.json` souboru.
Pokyny, jak vytvořit soubor sdílet a nahrát soubor na portálu Azure, najdete v části [Začínáme s Azure File storage ve Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Spusťte PowerShell na místním počítači a spusťte následující příkaz pro vytvoření sdílené složky a odeslat šablony Resource Manageru do této sdílené složky.

```powershell
# Login to Azure
Login-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Vytvoření sady runbook skript prostředí PowerShell

Teď vytvoříme skript prostředí PowerShell, který získá `TemplateTest.json` souborů ze služby Azure Storage a nasadí šablonu, kterou chcete vytvořit nový účet úložiště Azure.

V textovém editoru vložte následující text:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Uložte soubor místně jako `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Import a publikovat sadu runbook k účtu Azure Automation.

Nyní jsme naimportovat sady runbook do účtu Azure Automation pomocí prostředí PowerShell a pak publikovat sadu runbook.
Informace o tom, jak importovat a publikování sady runbook na portálu Azure najdete v tématu [vytvoření nebo import runbooku ve službě Azure Automation](automation-creating-importing-runbook.md).

Chcete-li importovat `DeployTemplate.ps1` do účtu Automation jako runbook prostředí PowerShell, spusťte následující příkazy prostředí PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Spuštění runbooku

Nyní jsme spuštění runbooku voláním [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0) rutiny.

Informace o tom, jak spuštění sady runbook na portálu Azure najdete v tématu [spuštění sady runbook ve službě Azure Automation](automation-starting-a-runbook.md).

V konzole PowerShell, spusťte následující příkazy:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Sada runbook běží, a její stav můžete zkontrolovat spuštěním `$job.Status`.

Sada runbook získá šablony Resource Manageru a použije ho k nasazení nového účtu úložiště Azure.
Můžete zjistit, zda byl vytvořen nový účet úložiště tak, že spustíte následující příkaz:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Souhrn

A to je vše! Teď můžete použít šablony Azure Automation a úložiště Azure a Resource Manager k nasazení všech vašich prostředků Azure.

## <a name="next-steps"></a>Další postup

* Další informace o šablonách Resource Manager najdete v tématu [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md)
* Začínáme s Azure Storage, najdete v tématu [Úvod do Azure Storage](../storage/common/storage-introduction.md).
* Další užitečné runbooků služeb automatizace Azure, najdete v tématu [Galerie Runbooků a modulů pro Azure Automation](automation-runbook-gallery.md).
* Další užitečné šablony Resource Manageru, najdete v tématu [šablon Azure rychlý start](https://azure.microsoft.com/resources/templates/)

