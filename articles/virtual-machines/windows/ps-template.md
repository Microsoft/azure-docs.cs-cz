---
title: Vytvoření virtuálního počítače s Windows pomocí šablony v Azure | Microsoft Docs
description: Snadno vytvářet nový virtuální počítač s Windows pomocí šablony Resource Manageru a prostředí PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: davidmu
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0b80c7016671c2d5c33fc5501bf50feaa4c5d7c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Vytvoření virtuálního počítače s Windows pomocí šablony Resource Manageru

Tento článek ukazuje, jak nasadit šablonu Azure Resource Manager pomocí prostředí PowerShell. Šablona, kterou vytvoříte nasadí jednoho virtuálního počítače se systémem Windows Server v nové virtuální sítě s jedinou podsítí.

Podrobný popis prostředek virtuálního počítače naleznete v tématu [virtuálních počítačů v šablonu Azure Resource Manager](template-description.md). Další informace o všechny prostředky v šabloně najdete v tématu [názorný Průvodce šablonou Azure Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Proveďte kroky v tomto článku má trvat asi pět minut.

## <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell

Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](../../powershell-install-configure.md), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Všechny prostředky musí být nasazený v [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md).

1. Získejte seznamu dostupných umístění, kde je možné prostředky vytvořit.
   
    ```powershell   
    Get-AzureRmLocation | sort DisplayName | Select DisplayName
    ```

2. Vytvořte skupinu prostředků v místě, které vyberete. Tento příklad ukazuje vytvoření skupiny prostředků s názvem **myResourceGroup** v **západní USA** umístění:

    ```powershell   
    New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
    ```

## <a name="create-the-files"></a>Vytvoření souborů

V tomto kroku vytvoříte soubor šablony, která nasazuje prostředky a soubor parametrů, který poskytuje hodnoty parametrů šablony. Můžete také vytvořit soubor autorizace, který se používá k provádění operací Azure Resource Manager.

1. Vytvořte soubor s názvem *CreateVMTemplate.json* a přidejte do ní tento kód JSON:

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

2. Vytvořte soubor s názvem *Parameters.JSON tímto kódem* a přidejte do ní tento kód JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

3. Vytvořte nový účet úložiště a kontejneru:

    ```powershell
    $storageName = "st" + (Get-Random)
    New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" -AccountName $storageName -Location "West US" -SkuName "Standard_LRS" -Kind Storage
    $accountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name $storageName).Value[0]
    $context = New-AzureStorageContext -StorageAccountName $storageName -StorageAccountKey $accountKey 
    New-AzureStorageContainer -Name "templates" -Context $context -Permission Container
    ```

4. Nahrání souborů do účtu úložiště:

    ```powershell
    Set-AzureStorageBlobContent -File "C:\templates\CreateVMTemplate.json" -Context $context -Container "templates"
    Set-AzureStorageBlobContent -File "C:\templates\Parameters.json" -Context $context -Container templates
    ```

    Změna cesty k umístění, kam jste uložili soubory-souborům.

## <a name="create-the-resources"></a>Vytvořit prostředky

Nasazení šablony pomocí parametrů:

```powershell
$templatePath = "https://" + $storageName + ".blob.core.windows.net/templates/CreateVMTemplate.json"
$parametersPath = "https://" + $storageName + ".blob.core.windows.net/templates/Parameters.json"
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -Name "myDeployment" -TemplateUri $templatePath -TemplateParameterUri $parametersPath 
```

> [!NOTE]
> Můžete také nasadit parametry z místní soubory a šablony. Další informace najdete v tématu [použití Azure Powershellu s Azure Storage](../../storage/common/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Další kroky

- Pokud byly nějaké problémy s nasazením, může si prohlédněte [odstraňování běžných chyb nasazení Azure pomocí Azure Resource Manageru](../../resource-manager-common-deployment-errors.md).
- Naučte se vytvářet a spravovat virtuální počítač v [vytvořit a spravovat virtuální počítače Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

