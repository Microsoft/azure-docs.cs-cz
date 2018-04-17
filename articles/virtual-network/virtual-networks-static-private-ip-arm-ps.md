---
title: Nakonfigurovat privátní IP adresy pro virtuální počítače - prostředí Azure PowerShell | Microsoft Docs
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0e8153f1d0cecd4efe66dc7cce64addd6ed62aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-powershell"></a>Nakonfigurovat privátní IP adresy pro virtuální počítač pomocí prostředí PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

Azure nabízí dva modely nasazení: Azure Resource Manager a Classic. Microsoft doporučuje vytváření prostředků prostřednictvím modelu nasazení Resource Manager. Další informace o rozdílech mezi těmito dvěma modely najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se týká modelu nasazení Resource Manager. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení classic](virtual-networks-static-private-ip-classic-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Ukázka jednoduché prostředí už vytvořený očekávat níže uvedené příkazy prostředí PowerShell založené na výše uvedené scénáře. Pokud chcete ke spuštění příkazů, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvořit virtuální síť](quick-create-powershell.md).

## <a name="create-a-vm-with-a-static-private-ip-address"></a>Vytvoření virtuálního počítače se statickou privátní IP adresou
Vytvoření virtuálního počítače s názvem *DNS01* v *front-endu* podsíť virtuální sítě s názvem *TestVNet* se statickou privátní IP z *192.168.1.101*, postupujte podle následujících kroků:

1. Nastavení proměnných pro účet úložiště, umístění, skupiny prostředků a přihlašovací údaje k použití. Musíte zadat uživatelské jméno a heslo pro virtuální počítač. Již musí existovat skupině účtů a prostředků úložiště.

    ```powershell
    $stName  = "vnetstorage"
    $locName = "Central US"
    $rgName  = "TestRG"
    $cred    = Get-Credential -Message "Type the name and password of the local administrator account."
    ```

2. Načíst virtuální síť a podsíť, kterou chcete vytvořit virtuální počítač v.

    ```powershell
    $vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    $subnet = $vnet.Subnets[0].Id
    ```

3. V případě potřeby vytvořte veřejnou IP adresu z Internetu přístup k virtuálnímu počítači.

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName `
    -Location $locName -AllocationMethod Dynamic
    ```

4. Vytvořte pomocí statickou privátní IP adresu, kterou chcete přiřadit k virtuálnímu počítači síťový adaptér. Ujistěte se, že IP adresa z rozsahu podsítě, který přidáváte virtuálního počítače. Toto je hlavní krok k tomuto článku, kde můžete nastavit privátní IP statická.

    ```powershell
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName `
    -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id `
    -PrivateIpAddress 192.168.1.101
    ```

5. Vytvoření virtuálního počítače pomocí seskupování:

    ```powershell
    $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01 `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri `
    -CreateOption fromImage
    New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 
    ```

Doporučuje se, že nepřiřadíte staticky privátní IP přiřazené k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud nezbytné, jako např. kdy [přiřazení více IP adres pro virtuální počítač s Windows](virtual-network-multiple-ip-addresses-powershell.md). Pokud ručně nastavit privátní IP adresu v operačním systému, zajistěte, aby byl stejnou adresu jako přiřazené Azure privátní IP adresy [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo můžete ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení. Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače.

## <a name="retrieve-static-private-ip-address-information-for-a-network-interface"></a>Načíst statickou privátní IP adresu informace pro síťové rozhraní
Chcete-li zobrazit statické soukromé informace IP adresu pro virtuální počítač vytvořený pomocí skriptu pro výše uvedené, spusťte následující příkaz prostředí PowerShell a sledovat hodnoty pro *PrivateIpAddress* a *PrivateIpAllocationMethod*:

```powershell
Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
```

Očekávaný výstup:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="remove-a-static-private-ip-address-from-a-network-interface"></a>Odeberte statickou privátní IP adresu z rozhraní sítě
Odeberte statickou privátní IP adresu přidat do virtuálního počítače ve skriptu výše, spusťte následující příkazy prostředí PowerShell:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Očekávaný výstup:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="add-a-static-private-ip-address-to-a-network-interface"></a>Přidat statickou privátní IP adresou k síťovému rozhraní
Chcete-li přidat statickou privátní IP adresu na virtuální počítač vytvořený skript výše, spusťte následující příkazy:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Doporučuje se, že nepřiřadíte staticky privátní IP přiřazené k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud nezbytné, jako např. kdy [přiřazení více IP adres pro virtuální počítač s Windows](virtual-network-multiple-ip-addresses-powershell.md). Pokud ručně nastavit privátní IP adresu v operačním systému, zajistěte, aby byl stejnou adresu jako přiřazené Azure privátní IP adresy [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo můžete ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení. Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače.

## <a name="change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>Změnit metodu přidělení privátní IP adresy přiřazené k síťovému rozhraní

Privátní IP adresy přiřazeny metoda přidělení statickou nebo dynamickou síťový adaptér. Dynamické IP adresy můžete změnit po spuštění virtuálního počítače, která byla dříve v zastaveném stavu (deallocated). Pokud virtuální počítač je hostitelem služby, která vyžaduje stejnou IP adresu, i po restartování z zastaveném stavu (deallocated) to může potenciálně způsobit problémy. Statické IP adresy jsou uchovány až do odstranění virtuálního počítače. Chcete-li změnit metodu přidělení IP adresy, spusťte následující skript, který změní metoda přidělení z dynamické na statické. Pokud je metoda přidělení pro aktuální privátní IP adresy statické, změnit *statické* k *dynamické* před spuštěním skriptu.

```powershell
$RG = "TestRG"
$NIC_name = "testnic1"

$nic = Get-AzureRmNetworkInterface -ResourceGroupName $RG -Name $NIC_name
$nic.IpConfigurations[0].PrivateIpAllocationMethod = 'Static'
Set-AzureRmNetworkInterface -NetworkInterface $nic 
$IP = $nic.IpConfigurations[0].PrivateIpAddress

Write-Host "The allocation method is now set to"$nic.IpConfigurations[0].PrivateIpAllocationMethod"for the IP address" $IP"." -NoNewline
```

Pokud neznáte název síťového adaptéru, můžete zobrazit seznam síťových adaptérů ve skupině prostředků tak, že zadáte následující příkaz:

```powershell
Get-AzureRmNetworkInterface -ResourceGroupName $RG | Where-Object {$_.ProvisioningState -eq 'Succeeded'} 
```

## <a name="next-steps"></a>Další postup

Další informace o správě [nastavení IP adresy](virtual-network-network-interface-addresses.md).