---
title: Kurz – vytvoření virtuálních počítačů, na kterých běží SQL, IIS a .NET Stack v Azure
description: V tomto kurzu zjistíte, jak na virtuální počítač s Windows v Azure nainstalovat stack SQL Azure, služby IIS a .NET.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c53194bd345c18ac582acd538f1e8f8e1e34d54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87027848"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Kurz: instalace sady SQL, IIS, .NET Stack na virtuálním počítači s Windows pomocí Azure PowerShell

V tomto kurzu nainstalujeme sadu SQL, IIS a .NET Stack pomocí Azure PowerShell. Tento stack se skládá ze dvou virtuálních počítačů s Windows Serverem 2016. Na jednom je služba IIS a .NET a na druhém SQL Server.

> [!div class="checklist"]
> * Vytvoření virtuálního počítače 
> * Instalace služby IIS a sady .NET Core SDK na virtuální počítač
> * Vytvoření virtuálního počítače s SQL Serverem
> * Instalace rozšíření SQL Serveru

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="create-an-iis-vm"></a>Vytvoření virtuálního počítače IIS 

V tomto příkladu používáme rutinu [New-AzVM](/powershell/module/az.compute/new-azvm) v powershellu Cloud Shell k rychlému vytvoření virtuálního počítače s Windows serverem 2016 a následné instalaci služby IIS a .NET Framework. Virtuální počítače se službou IIS a s SQL sdílí skupinu prostředků a virtuální síť, takže pro jejich názvy vytvoříme proměnné.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
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

Nainstalujte službu IIS a rozhraní .NET Framework pomocí rozšíření vlastních skriptů pomocí rutiny [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) .

```azurepowershell-interactive
Set-AzVMExtension `
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

Vytvořte druhou podsíť pro virtuální počítač SQL. Získání virtuální sítě pomocí [Get-AzVirtualNetwork] {/PowerShell/Module/AZ.Network/Get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Vytvořte konfiguraci pro podsíť pomocí [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Aktualizujte virtuální síť novými informacemi o podsíti pomocí [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) .
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Virtuální počítač Azure SQL

K vytvoření virtuálního počítače SQL použijte předkonfigurovanou image serveru SQL z Azure Marketplace. Nejprve vytvoříme virtuální počítač a pak na něj nainstalujeme rozšíření SQL Serveru. 


```azurepowershell-interactive
New-AzVm `
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

K přidání [rozšíření SQL Server](../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md) k virtuálnímu počítači SQL použijte [set-AzVMSqlServerExtension](/powershell/module/az.compute/set-azvmsqlserverextension) .

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nainstalovali stack SQL&#92;IIS&#92;.NET pomocí Azure PowerShellu. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače 
> * Instalace služby IIS a sady .NET Core SDK na virtuální počítač
> * Vytvoření virtuálního počítače s SQL Serverem
> * Instalace rozšíření SQL Serveru

Přejděte k dalšímu kurzu, kde se dozvíte, jak zabezpečit webový server služby IIS pomocí certifikátů TLS/SSL.

> [!div class="nextstepaction"]
> [Zabezpečený webový server služby IIS pomocí certifikátů TLS/SSL](tutorial-secure-web-server.md)
