---
title: Nasazení šablony Azure Resource Manageru v runbooku Azure Automation
description: Jak nasadit šablonu Azure Resource Manager uložených v Azure Storage z runbooku
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: d4adbea42cda54380ad32dce40cfa0d8391ee490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75366630"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Nasazení šablony Azure Resource Manageru v powershellovém runbooku Azure Automation

Runbook [Azure Automation PowerShell,](automation-first-runbook-textual-powershell.md) který nasazuje prostředek Azure pomocí [šablony Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Tímto způsobem můžete automatizovat nasazení prostředků Azure. Šablony Správce prostředků můžete udržovat v centrálním zabezpečeném umístění, jako je Azure Storage.

V tomto článku vytvoříme runbook prostředí PowerShell, který používá šablonu Správce prostředků uloženou ve [službě Azure Storage](../storage/common/storage-introduction.md) k nasazení nového účtu Úložiště Azure.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující položky:

* Předplatné Azure. Pokud ještě nemáte, můžete [aktivovat výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/).
* [Účet Automation](automation-sec-configure-azure-runas-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure.  Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* [Účet Azure Storage,](../storage/common/storage-create-storage-account.md) do kterého se má uložit šablona Správce prostředků
* Azure Powershell nainstalovaný v místním počítači. Informace o tom, jak získat Azure PowerShell, najdete v tématu [Instalace a konfigurace Azure PowerShellu.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru

V tomto příkladu používáme šablonu Správce prostředků, která nasazuje nový účet úložiště Azure.

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
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
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
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
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

Uložte soubor místně `TemplateTest.json`jako .

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Uložení šablony Správce prostředků do Úložiště Azure

Teď použijeme PowerShell k vytvoření sdílené složky úložiště Azure a nahrání souboru. `TemplateTest.json`
Pokyny k vytvoření sdílené složky a nahrání souboru na webu Azure Portal najdete v tématu [Začínáme s úložištěm souborů Azure ve Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Spusťte prostředí PowerShell v místním počítači a spusťte následující příkazy, abyste vytvořili sdílenou složku a nahráli šablonu Správce prostředků do této sdílené složky.

```powershell
# Login to Azure
Connect-AzureRmAccount

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

## <a name="create-the-powershell-runbook-script"></a>Vytvoření skriptu runbooku prostředí PowerShell

Teď vytvoříme skript PowerShellu, který získá `TemplateTest.json` soubor z Azure Storage a nasadí šablonu k vytvoření nového účtu Azure Storage.

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
Connect-AzureRmAccount `
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

Uložte soubor místně `DeployTemplate.ps1`jako .

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Import a publikování runbooku do účtu Azure Automation

Teď použijeme PowerShell k importu runbooku do vašeho účtu Azure Automation a pak ji publikujeme.
Informace o tom, jak importovat a publikovat runbook na webu Azure Portal, najdete v tématu [Správa runbooků v Azure Automation](manage-runbooks.md).

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

Nyní spustíme runbook voláním rutiny [Start-AzureRmAutomationRunbook.](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)

Informace o tom, jak spustit runbook na webu Azure Portal, najdete v [tématu Spuštění runbooku v Azure Automation](automation-starting-a-runbook.md).

V konzole PowerShell uveřejujte následující příkazy:

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

Runbook se spustí a můžete zkontrolovat `$job.Status`jeho stav spuštěním .

Runbook získá šablonu Správce prostředků a použije ji k nasazení nového účtu úložiště Azure.
Můžete vidět, že nový účet úložiště byl vytvořen spuštěním následujícího příkazu:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Souhrn

A to je vše! Teď můžete použít Azure Automation a Azure Storage a šablony Správce prostředků k nasazení všech prostředků Azure.

## <a name="next-steps"></a>Další kroky

* Další informace o šablonách Správce prostředků najdete v [tématu Přehled Správce prostředků Azure.](../azure-resource-manager/management/overview.md)
* Pokud chcete začít s Azure Storage, [najdete v tématu Úvod do Azure Storage](../storage/common/storage-introduction.md).
* Další užitečné runbooky Azure Automation najdete v článku [Runbook a galerie modulů pro Azure Automation](automation-runbook-gallery.md).
* Další užitečné šablony Správce prostředků najdete v [tématu Šablony rychlých startů Azure](https://azure.microsoft.com/resources/templates/)


