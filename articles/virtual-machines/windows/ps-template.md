---
title: Vytvoření virtuálního počítače s Windows pomocí šablony v Azure | Dokumentace Microsoftu
description: Můžete snadno vytvořit nový virtuální počítač s Windows pomocí šablony Resource Manageru a Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 893ef999907c7f807fdf3a82b2372ece9c9a6a39
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112424"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Vytvoření virtuálního počítače s Windows pomocí šablony Resource Manageru

V tomto článku se dozvíte, jak nasadit šablonu Azure Resource Manageru pomocí Powershellu. Šablona, kterou vytvoříte nasadí jeden virtuální počítač s Windows serverem v nové virtuální sítě s jedinou podsítí.

Podrobný popis prostředku virtuálního počítače najdete v tématu [virtuální počítače v šabloně Azure Resource Manageru](template-description.md). Další informace o všech prostředcích v šabloně najdete v tématu [názorném průvodci k šablonám Azure Resource Manageru](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Proveďte kroky v tomto článku má trvat přibližně během pěti minut.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.3 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Connect-AzAccount` vytvořit připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Všechny prostředky musí být nasazený v [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md).

1. Získejte seznamu dostupných umístění, kde je možné prostředky vytvořit.
   
    ```powershell   
    Get-AzLocation | sort-object DisplayName | Select DisplayName
    ```

2. Vytvořte skupinu prostředků v umístění, které jste vybrali. Tento příklad ukazuje vytvoření skupiny prostředků s názvem **myResourceGroup** v **USA – západ** umístění:

    ```powershell   
    New-AzResourceGroup -Name "myResourceGroup" -Location "West US"
    ```

## <a name="create-the-files"></a>Vytvoření souborů

V tomto kroku vytvoříte soubor šablony, který se nasazuje prostředky a parametry souboru, který poskytuje hodnoty parametrů v šabloně. Můžete také vytvořit soubor autorizace, který se používá k provádění operací Azure Resource Manageru. 

1. Vytvořte soubor s názvem *CreateVMTemplate.json* a k němu přidat kód JSON. Nahraďte hodnotu `domainNameLabel` s vlastním jedinečným názvem.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]" 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

2. Vytvořte soubor s názvem *Parameters.json* a přidejte tento kód JSON do ní:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
      "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

3. Vytvoření nového účtu úložiště a kontejneru:

    ```powershell
    $storageName = "st" + (Get-Random)
    New-AzStorageAccount -ResourceGroupName "myResourceGroup" -AccountName $storageName -Location "West US" -SkuName "Standard_LRS" -Kind Storage
    $accountKey = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -Name $storageName).Value[0]
    $context = New-AzureStorageContext -StorageAccountName $storageName -StorageAccountKey $accountKey 
    New-AzureStorageContainer -Name "templates" -Context $context -Permission Container
    ```

4. Nahrání souborů do účtu úložiště:

    ```powershell
    Set-AzureStorageBlobContent -File "C:\templates\CreateVMTemplate.json" -Context $context -Container "templates"
    Set-AzureStorageBlobContent -File "C:\templates\Parameters.json" -Context $context -Container templates
    ```

    Změna umístění, kam jste uložili soubory / cesty k souborům.

## <a name="create-the-resources"></a>Vytvoření prostředků

Nasazení šablony pomocí parametrů:

```powershell
$templatePath = "https://" + $storageName + ".blob.core.windows.net/templates/CreateVMTemplate.json"
$parametersPath = "https://" + $storageName + ".blob.core.windows.net/templates/Parameters.json"
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -Name "myDeployment" -TemplateUri $templatePath -TemplateParameterUri $parametersPath 
```

> [!NOTE]
> Můžete také nasadit šablony a parametrů z místních souborů. Další informace najdete v tématu [pomocí Azure Powershellu s Azure Storage](../../storage/common/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Další kroky

- Pokud byly nějaké problémy s nasazením, může trvat podívat [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](../../resource-manager-common-deployment-errors.md).
- Zjistěte, jak vytvářet a spravovat virtuální počítač v [vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o vytváření šablon, zobrazí se syntaxe JSON a vlastnosti pro typy prostředků, které jste nasadili:

* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)

