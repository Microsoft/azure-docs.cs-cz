---
title: Kurz – vytvoření virtuální počítače s SQL, služby IIS a .NET zásobníku v Azure | Dokumentace Microsoftu
description: V tomto kurzu zjistíte, jak na virtuální počítač s Windows v Azure nainstalovat stack SQL Azure, služby IIS a .NET.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c5fa818a7f0d25a91ea6d272c30384de4c1765b3
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887100"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Kurz: Instalace SQL, služby IIS a .NET zásobníku ve virtuálním počítači Windows pomocí Azure Powershellu

V tomto kurzu nainstalujeme SQL, IIS, .NET zásobníku pomocí Azure Powershellu. Tento stack se skládá ze dvou virtuálních počítačů s Windows Serverem 2016. Na jednom je služba IIS a .NET a na druhém SQL Server.

> [!div class="checklist"]
> * Vytvoření virtuálního počítače 
> * Instalace služby IIS a sady .NET Core SDK na virtuální počítač
> * Vytvoření virtuálního počítače s SQL Serverem
> * Instalace rozšíření SQL Serveru

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul AzureRM.Compute verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM.Compute`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-iis-vm"></a>Vytvoření virtuálního počítače se službou IIS 

V tomto příkladu pomocí rutiny [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) v PowerShellu nebo službě Cloud Shell rychle vytvoříme virtuální počítač s Windows Serverem 2016 a pak nainstalujeme službu IIS a rozhraní .NET Framework. Virtuální počítače se službou IIS a s SQL sdílí skupinu prostředků a virtuální síť, takže pro jejich názvy vytvoříme proměnné.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Instalace IIS a rozhraní .NET framework pomocí rozšíření vlastních skriptů [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny.

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Vytvoření další podsítě

Vytvořte druhou podsíť pro virtuální počítač SQL. Virtuální síť získáte pomocí rutiny [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Vytvořte konfiguraci podsítě pomocí rutiny [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Aktualizujte virtuální síť s použitím informací o nové podsíti pomocí rutiny [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>Virtuální počítač Azure SQL

K vytvoření virtuálního počítače SQL použijte předkonfigurovanou image serveru SQL z Azure Marketplace. Nejprve vytvoříme virtuální počítač a pak na něj nainstalujeme rozšíření SQL Serveru. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Pomocí rutiny [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) přidejte na virtuální počítač SQL [rozšíření SQL Serveru](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nainstalovali stack SQL&#92;IIS&#92;.NET pomocí Azure PowerShellu. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače 
> * Instalace služby IIS a sady .NET Core SDK na virtuální počítač
> * Vytvoření virtuálního počítače s SQL Serverem
> * Instalace rozšíření SQL Serveru

V dalším kurzu se dozvíte, jak zabezpečit webový server služby IIS pomocí certifikátů SSL.

> [!div class="nextstepaction"]
> [Zabezpečení webového serveru služby IIS pomocí certifikátů SSL](tutorial-secure-web-server.md)

