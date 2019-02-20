---
title: Nasazení šablony Azure Resource Manageru v runbooku Azure Automation
description: Jak nasadit šablonu Azure Resource Manageru uložené ve službě Azure Storage ze sady runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: prostředí PowerShell, sady runbook, json, služby azure automation
ms.openlocfilehash: ef97d4e2fd0951e30a725e8f2f9603a73c61f1ca
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416110"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Nasazení šablony Azure Resource Manageru v powershellovém runbooku Azure Automation

Můžete napsat [Powershellového runbooku Azure Automation](automation-first-runbook-textual-powershell.md) , která nasadí prostředek Azure s použitím [šablony Azure Resource Manageru](../azure-resource-manager/resource-manager-create-first-template.md).

Tímto způsobem můžete automatizovat nasazení prostředků Azure. Abyste mohli své šablony Resource Manageru v centrální a zabezpečené umístění, jako je Azure Storage.

V tomto článku jsme vytvořte Powershellový runbook, který používá šablonu Resource Manageru uložených v [služby Azure Storage](../storage/common/storage-introduction.md) nasadit nový účet úložiště Azure.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující položky:

* Předplatné Azure. Pokud jeden ještě nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
* [Účet Automation](automation-sec-configure-azure-runas-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure.  Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* [Účet služby Azure Storage](../storage/common/storage-create-storage-account.md) pro uložení šablony Resource Manageru
* Na místním počítači nainstalovaný Azure Powershell. Zobrazit [nainstalovat a nakonfigurovat Azure Powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.1.0) informace o tom, jak získat Azure Powershellu.

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru

V tomto příkladu používáme šablony Resource Manageru, která nasadí nový účet úložiště Azure.

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

Uložte soubor místně jako `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Uložit šablonu Resource Manageru ve službě Azure Storage

Nyní jsme pomocí prostředí PowerShell k vytvoření sdílené složky Azure Storage a nahrajte `TemplateTest.json` souboru.
Návod, jak vytvořit soubor sdílet a nahrát soubor na webu Azure Portal, naleznete v tématu [Začínáme s Azure File storage ve Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Spuštění Powershellu v místním počítači a spusťte následující příkazy k vytvoření sdílené složky a nahrát do ní šablony Resource Manageru.

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

## <a name="create-the-powershell-runbook-script"></a>Vytvoření sady runbook Powershellového skriptu

Teď můžeme vytvořit Powershellový skript, který získá `TemplateTest.json` souborů ze služby Azure Storage a nasadí šablonu, kterou chcete vytvořit nový účet úložiště Azure.

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

Uložte soubor místně jako `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Import a publikování sady runbook do účtu Azure Automation

Nyní jsme pomocí prostředí PowerShell a importujte runbook do účtu Azure Automation a potom publikovat sadu runbook.
Informace o tom, jak importovat a publikovat sadu runbook na portálu Azure portal najdete v tématu [správu sad runbook ve službě Azure Automation](manage-runbooks.md).

Chcete-li importovat `DeployTemplate.ps1` do vašeho účtu Automation jako runbook Powershellu, spusťte následující příkazy Powershellu:

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

Teď můžeme spouštět sadu runbook pomocí volání [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0) rutiny.

Informace o tom, jak spustit sadu runbook na portálu Azure portal najdete v tématu [spuštění runbooku ve službě Azure Automation](automation-starting-a-runbook.md).

V konzole Powershellu spusťte následující příkazy:

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

Sada runbook běží, a jeho stav můžete zkontrolovat spuštěním `$job.Status`.

Runbook načte šablonu Resource Manageru a použije ho k nasazení nového účtu úložiště Azure.
Uvidíte, že byl vytvořen nový účet úložiště, spuštěním následujícího příkazu:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Souhrn

A to je vše! Teď můžete použít šablony Azure Automation a Azure Storage a Resource Manageru k nasazení všech vašich prostředků Azure.

## <a name="next-steps"></a>Další postup

* Další informace o šablonách Resource Manageru najdete v tématu [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md)
* Začínáme s Azure Storage, najdete v článku [Úvod do služby Azure Storage](../storage/common/storage-introduction.md).
* Další užitečné runbooků Azure Automation najdete v tématu [Galerie modulů a Runbooků Azure Automation](automation-runbook-gallery.md).
* Další užitečné šablony Resource Manageru najdete v tématu [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/)


