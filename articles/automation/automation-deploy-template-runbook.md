---
title: Nasazení šablony Azure Resource Manageru v runbooku Azure Automation
description: Jak nasadit šablonu Azure Resource Manager uložených v Azure Storage z runbooku
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 575f954b346edb7d682e3fd0b432a257486bbfbb
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383281"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Nasazení šablony Azure Resource Manageru v powershellovém runbooku Azure Automation

Runbook [Azure Automation PowerShell,](automation-first-runbook-textual-powershell.md) který nasazuje prostředek Azure pomocí [šablony Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Tímto způsobem můžete automatizovat nasazení prostředků Azure. Šablony Správce prostředků můžete udržovat v centrálním zabezpečeném umístění, jako je Azure Storage.

V tomto článku vytvoříme runbook prostředí PowerShell, který používá šablonu Správce prostředků uloženou ve [službě Azure Storage](../storage/common/storage-introduction.md) k nasazení nového účtu Úložiště Azure.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující položky:

* Předplatné Azure. Pokud ještě nemáte, můžete [aktivovat výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/).
* [Účet Automation](automation-sec-configure-azure-runas-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure.  Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* [Účet Azure Storage,](../storage/common/storage-create-storage-account.md) do kterého se má uložit šablona Správce prostředků
* Azure PowerShell nainstalovaný v místním počítači. Informace o tom, jak získat Azure PowerShell, najdete [v tématu Instalace modulu Azure PowerShellu.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)

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

Uložte soubor místně jako **TemplateTest.json**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Uložení šablony Správce prostředků do Úložiště Azure

Teď použijeme PowerShell k vytvoření sdílené složky úložiště Azure a nahrání souboru **TemplateTest.json.**
Pokyny k vytvoření sdílené složky a nahrání souboru na webu Azure Portal najdete v tématu [Začínáme s úložištěm souborů Azure ve Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Spusťte prostředí PowerShell v místním počítači a spusťte následující příkazy, abyste vytvořili sdílenou složku a nahráli šablonu Správce prostředků do této sdílené složky.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

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

Teď vytvoříme skript PowerShellu, který získá soubor **TemplateTest.json** z Azure Storage a nasadí šablonu k vytvoření nového účtu Azure Storage.

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
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Uložte soubor místně jako **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Import a publikování runbooku do účtu Azure Automation

Teď použijeme PowerShell k importu runbooku do vašeho účtu Azure Automation a pak ji publikujeme.
Informace o tom, jak importovat a publikovat runbook na webu Azure Portal, najdete v tématu [Správa runbooků v Azure Automation](manage-runbooks.md).

Chcete-li importovat **soubor DeployTemplate.ps1** do účtu Automation jako runbook prostředí PowerShell, spusťte následující příkazy prostředí PowerShell:

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

Nyní spustíme runbook voláním rutiny [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) Informace o tom, jak spustit runbook na webu Azure Portal, najdete v [tématu Spuštění runbooku v Azure Automation](automation-starting-a-runbook.md).

V konzole PowerShell uveřejujte následující příkazy:

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

Runbook se spustí a můžete zkontrolovat `$job.Status`jeho stav spuštěním .

Runbook získá šablonu Správce prostředků a použije ji k nasazení nového účtu úložiště Azure.
Můžete vidět, že nový účet úložiště byl vytvořen spuštěním následujícího příkazu:

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Souhrn

A to je vše! Teď můžete použít Azure Automation a Azure Storage se šablonami Správce prostředků k nasazení všech prostředků Azure.

## <a name="next-steps"></a>Další kroky

* Další informace o šablonách Správce prostředků najdete v [tématu Přehled Správce prostředků Azure](../azure-resource-manager/management/overview.md).
* Pokud chcete začít s Azure Storage, [najdete v tématu Úvod do Azure Storage](../storage/common/storage-introduction.md).
* Další užitečné runbooky Azure Automation najdete v článku [Runbook a galerie modulů pro Azure Automation](automation-runbook-gallery.md).
* Další užitečné šablony Správce prostředků najdete v [tématu Šablony rychlého spuštění Azure](https://azure.microsoft.com/resources/templates/).
* Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
