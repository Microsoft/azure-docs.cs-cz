---
title: Více IP adres pro virtuální počítače Azure – PowerShell | Dokumenty společnosti Microsoft
description: Zjistěte, jak přiřadit více IP adres k virtuálnímu počítači pomocí PowerShellu. | Správce prostředků
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: kumud
ms.reviewer: annahar
ms.openlocfilehash: a8bd4e4779d94cfc22ac7726c9746fe755764033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279569"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Přiřazení více IP adres virtuálním počítačům pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Tento článek vysvětluje, jak vytvořit virtuální počítač (VM) prostřednictvím modelu nasazení Azure Resource Manager pomocí PowerShellu. Více ADRES IP nelze přiřadit k prostředkům vytvořeným prostřednictvím klasického modelu nasazení. Další informace o modelech nasazení Azure najdete v článku [Principy modelů nasazení.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Vytvoření virtuálního počítače s několika IP adresami

Následující kroky vysvětlují, jak vytvořit ukázkový virtuální počítač s více ADRESAMI IP, jak je popsáno ve scénáři. Změňte hodnoty proměnných podle potřeby pro implementaci.

1. Otevřete příkazový řádek Prostředí PowerShell a proveďte zbývající kroky v této části v rámci jedné relace prostředí PowerShell. Pokud ještě nemáte nainstalovaný a nakonfigurovaný PowerShell, proveďte kroky v článku [Jak nainstalovat a nakonfigurovat Azure PowerShell.](/powershell/azure/overview)
2. Přihlaste se `Connect-AzAccount` ke svému účtu pomocí příkazu.
3. Nahraďte *myResourceGroup* a *westus* názvem a umístěním podle vašeho výběru. Vytvořte skupinu prostředků. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Vytvořte virtuální síť (VNet) a podsíť ve stejném umístění jako skupina prostředků:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Vytvořte skupinu zabezpečení sítě (NSG) a pravidlo. Skupina zabezpečení zabezpečení virtuálního účtu zabezpečuje pomocí příchozích a odchozích pravidel. V tomto případě se vytvoří příchozí pravidlo pro port 3389, které umožní příchozí připojení ke vzdálené ploše.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Definujte primární konfiguraci IP pro nic. Změňte hodnotu 10.0.0.4 na platnou adresu v podsíti, kterou jste vytvořili, pokud jste dříve nepoužili definovanou hodnotu. Před přiřazením statické ADRESY IP doporučujeme nejprve potvrdit, že se již nepoužívá. Zadejte `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`příkaz . Pokud je adresa k dispozici, výstup vrátí *true*. Pokud není k dispozici, výstup vrátí *False* a seznam adres, které jsou k dispozici. 

    V následujících příkazech **nahraďte \<> nahradit jedinečným názvem jedinečným názvem.** Název musí být jedinečný ve všech veřejných IP adresách v rámci oblasti Azure. Toto je volitelný parametr. Může být odebrána, pokud se chcete připojit jenom k virtuálnímu virtuálnímu počítačůmu pomocí veřejné IP adresy.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Při přiřazení více konfigurací IP k nic, jedna konfigurace musí být přiřazena jako *-Primary*.

    > [!NOTE]
    > Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres najdete na stránce [s cenami ip adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Počet veřejných IP adres, které lze použít v předplatném, je omezen. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

7. Definujte sekundární konfigurace IP pro nic. Podle potřeby můžete přidat nebo odebrat konfigurace. Ke každé konfiguraci protokolu IP musí být přiřazena privátní adresa IP. Každé konfiguraci může být volitelně přiřazena jedna veřejná IP adresa.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Vytvořte nic a přidružte k ní tři konfigurace IP:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Přestože všechny konfigurace jsou přiřazeny k jedné nic v tomto článku, můžete přiřadit více konfigurací IP ke každé nic připojené k virtuálnímu počítači. Pokud se chcete dozvědět, jak vytvořit virtuální počítač s více síťové karty, přečtěte si článek [Vytvořit virtuální počítač s více síťové karty.](../virtual-machines/windows/multiple-nics.md)

9. Vytvořte virtuální virtuální virtuální ms zadáním následujících příkazů:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Přidejte privátní IP adresy do operačního systému virtuálního počítače dokončením kroků pro váš operační systém v části [Přidat IP adresy do operačního systému virtuálního počítače](#os-config) v tomto článku. Nepřidávejte veřejné IP adresy do operačního systému.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Přidání IP adres do virtuálního počítačů

Soukromé a veřejné IP adresy můžete přidat do síťového rozhraní Azure provedením následujících kroků. Příklady v následujících částech předpokládají, že už máte virtuální počítače se třemi konfiguracemi IP popsanými ve [scénáři](#scenario) v tomto článku, ale není to nutné.

1. Otevřete příkazový řádek Prostředí PowerShell a proveďte zbývající kroky v této části v rámci jedné relace prostředí PowerShell. Pokud ještě nemáte nainstalovaný a nakonfigurovaný PowerShell, proveďte kroky v článku [Jak nainstalovat a nakonfigurovat Azure PowerShell.](/powershell/azure/overview)
2. Změňte "hodnoty" následujících $Variables na název nic, ke které chcete přidat adresu IP, a skupinu prostředků a umístění, ve kterém nic existuje:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Pokud neznáte název nic, kterou chcete změnit, zadejte následující příkazy a změňte hodnoty předchozích proměnných:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Vytvořte proměnnou a nastavte ji na existující nic zadáním následujícího příkazu:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. V následujících příkazech změňte *myvnet* a *mysubnet* na názvy virtuální sítě a podsítě, ke které je připojena síťová síť. Zadejte příkazy pro načtení objektů virtuální sítě a podsítě, ke které je připojená síťová síť:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Pokud neznáte název virtuální sítě nebo podsítě, ke které je připojená síťová síť, zadejte následující příkaz:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Ve výstupu vyhledejte text podobný následujícímu ukázkovému výstupu:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    V tomto výstupu *je MyVnet* virtuální síť a *MySubnet* je podsíť, ke které je připojena síťová konjunkce.

5. Podle vašich požadavků proveďte kroky v jedné z následujících částí:

   **Přidání privátní IP adresy**

   Chcete-li do přenosné aplikace NIC přidat privátní adresu IP, je nutné vytvořit konfiguraci protokolu IP. Následující příkaz vytvoří konfiguraci se statickou ADRESOu IP 10.0.0.7. Při zadávání statické adresy IP musí být nepoužívaná adresa pro podsíť. Doporučujeme nejprve otestovat adresu, abyste se ujistili, `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` že je k dispozici zadáním příkazu. Pokud je k dispozici adresa IP, výstup vrátí *hodnotu True*. Pokud není k dispozici, výstup vrátí *False*a seznam adres, které jsou k dispozici.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Vytvořte tolik konfigurací, kolik potřebujete, pomocí jedinečných názvů konfigurací a privátních IP adres (pro konfigurace se statickými ADRESAMI IP).

   Přidejte privátní IP adresu do operačního systému virtuálního počítače dokončením kroků pro váš operační systém v části [Přidat IP adresy do operačního systému virtuálního počítače](#os-config) v tomto článku.

   **Přidání veřejné IP adresy**

   Veřejná IP adresa je přidána spojením prostředku veřejné IP adresy k nové konfiguraci IP nebo k existující konfiguraci IP. Podle potřeby proveďte kroky v jedné z následujících částí.

   > [!NOTE]
   > Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres najdete na stránce [s cenami ip adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Počet veřejných IP adres, které lze použít v předplatném, je omezen. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
   >

   **Přidružení prostředku veřejné IP adresy k nové konfiguraci PROTOKOLU IP**

   Kdykoli přidáte veřejnou IP adresu do nové konfigurace IP, musíte také přidat privátní IP adresu, protože všechny konfigurace IP musí mít privátní IP adresu. Můžete buď přidat existující prostředek veřejné IP adresy, nebo vytvořit nový. Chcete-li vytvořit nový, zadejte následující příkaz:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Chcete-li vytvořit novou konfiguraci IP se statickou privátní IP adresou a přidruženým zdrojem veřejné IP adresy *myPublicIp3,* zadejte následující příkaz:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Přidružení prostředku veřejné IP adresy k existující konfiguraci PROTOKOLU IP**

   Prostředek veřejné IP adresy lze přidružit pouze ke konfiguraci IP adresy, která ještě nemá jeden přidružený prostředek. Zadáním následujícího příkazu můžete určit, zda má konfigurace protokolu IP přidruženou veřejnou adresu IP:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Zobrazí se podobný výstup viz následující:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Vzhledem k tomu, že sloupec **PublicIpAddress** pro *Protokol IpConfig-3* je prázdný, není k němu aktuálně přidružen žádný prostředek veřejné IP adresy. Do protokolu IpConfig-3 můžete přidat existující prostředek veřejné IP adresy nebo zadat následující příkaz pro jeho vytvoření:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Zadáním následujícího příkazu přidružte prostředek veřejné IP adresy k existující konfiguraci IP s názvem *IpConfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Nastavte nic s novou konfigurací IP zadáním následujícího příkazu:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Zobrazte soukromé IP adresy a prostředky veřejných IP adres přiřazené k nicnici zadáním následujícího příkazu:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Přidejte privátní IP adresu do operačního systému virtuálního počítače dokončením kroků pro váš operační systém v části [Přidat IP adresy do operačního systému virtuálního počítače](#os-config) v tomto článku. Nepřidávejte veřejnou IP adresu do operačního systému.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]