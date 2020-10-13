---
title: Nasazení šablony Azure Resource Manageru v powershellovém runbooku Azure Automation
description: Tento článek popisuje, jak nasadit šablonu Azure Resource Manager uloženou v Azure Storage z Runbooku PowerShellu.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
keywords: PowerShell, Runbook, JSON, Azure Automation
ms.openlocfilehash: 18f1d4ced2a80f9adb5da2c209987fc1997a3f22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91304147"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Nasazení šablony Azure Resource Manager v Runbooku PowerShellu

Můžete napsat [Azure Automation powershellový Runbook](./learn/automation-tutorial-runbook-textual-powershell.md) , který nasadí prostředek Azure pomocí [šablony správy prostředků Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Šablony vám umožňují použít Azure Automation k automatizaci nasazení prostředků Azure. Šablony Správce prostředků můžete udržovat v centrálním, bezpečném umístění, jako je například Azure Storage.

V tomto článku vytvoříme PowerShellový Runbook, který používá šablonu Správce prostředků uloženou v [Azure Storage](../storage/common/storage-introduction.md) k nasazení nového účtu Azure Storage.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho ještě nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si [zaregistrovat bezplatný účet](https://azure.microsoft.com/free/).
* [Účet Automation](./manage-runas-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure. Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* [Azure Storage účet](../storage/common/storage-account-create.md) , do kterého chcete uložit šablonu správce prostředků.
* Azure PowerShell nainstalované na místním počítači. Informace o tom, jak získat Azure PowerShell, najdete v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) .

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru

V tomto příkladu používáme šablonu Správce prostředků, která nasadí nový účet Azure Storage.

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

Uložte soubor místně jako **TemplateTest.jsna**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Uložit šablonu Správce prostředků v Azure Storage

Nyní používáme PowerShell k vytvoření Azure Storage sdílené složky a nahrání **TemplateTest.jsdo** souboru. Pokyny k vytvoření sdílené složky a nahrání souboru v Azure Portal najdete v tématu Začínáme [se službou Azure File Storage ve Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Spusťte na svém místním počítači prostředí PowerShell a spuštěním následujících příkazů vytvořte sdílenou složku a nahrajte do této sdílené složky šablonu Správce prostředků.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Vytvoření skriptu PowerShellového Runbooku

Nyní vytvoříme skript prostředí PowerShell, který získá **TemplateTest.js** ze souboru z Azure Storage a nasadí šablonu pro vytvoření nového účtu Azure Storage.

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
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
```

Uložte soubor místně jako **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Import a publikování Runbooku na účet Azure Automation

Nyní používáme PowerShell k importování Runbooku do účtu Azure Automation a potom sadu Runbook publikujete. Informace o importu a publikování Runbooku v Azure Portal najdete v tématu [Správa runbooků v Azure Automation](manage-runbooks.md).

Pokud chcete **DeployTemplate.ps1** importovat do svého účtu Automation jako powershellový Runbook, spusťte následující příkazy PowerShellu:

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
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Spuštění runbooku

Nyní spustíme Runbook voláním rutiny [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) . Informace o tom, jak spustit sadu Runbook v Azure Portal, najdete v tématu [spuštění sady Runbook v Azure Automation](./start-runbooks.md).

V konzole PowerShellu spusťte následující příkazy:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json'
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Po spuštění sady Runbook můžete zjistit její stav načtením hodnoty vlastnosti objektu úlohy `$job.Status` .

Sada Runbook získá šablonu Správce prostředků a použije ji k nasazení nového účtu Azure Storage. Nový účet úložiště můžete zobrazit spuštěním následujícího příkazu:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Další kroky

* Další informace o šablonách Správce prostředků najdete v tématu [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md).
* Pokud chcete začít s Azure Storage, přečtěte si téma [Úvod do Azure Storage](../storage/common/storage-introduction.md).
* Další užitečné sady Runbook Azure Automation najdete v tématu [použití runbooků a modulů v Azure Automation](automation-runbook-gallery.md).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation#automation).
