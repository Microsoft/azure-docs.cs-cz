---
title: Několik IP adres pro virtuální počítače Azure – PowerShell | Microsoft Docs
description: Naučte se, jak přiřadit virtuálnímu počítači víc IP adres pomocí PowerShellu. | Správce prostředků
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: allensu
ms.openlocfilehash: d86d4248b449ad3961a7798fd36a320eb6a74009
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217069"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Přiřazení více IP adres k virtuálním počítačům pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Tento článek vysvětluje, jak pomocí PowerShellu vytvořit virtuální počítač (VM) pomocí modelu nasazení Azure Resource Manager. K prostředkům vytvořeným prostřednictvím modelu nasazení Classic nelze přiřadit více IP adres. Další informace o modelech nasazení Azure najdete v článku [Principy modelů nasazení](../azure-resource-manager/management/deployment-models.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Vytvoření virtuálního počítače s několika IP adresami

Následující postup vysvětluje, jak vytvořit ukázkový virtuální počítač s více IP adresami, jak je popsáno ve scénáři. Změňte hodnoty proměnných podle požadavků vaší implementace.

1. Otevřete příkazový řádek PowerShellu a dokončete zbývající kroky v této části v rámci jedné relace PowerShellu. Pokud ještě nemáte nainstalované a nakonfigurované prostředí PowerShell, proveďte kroky popsané v článku [Postup instalace a konfigurace Azure PowerShell](/powershell/azure/) .
2. Pomocí příkazu se přihlaste ke svému účtu `Connect-AzAccount` .
3. Položky *myResourceGroup* a *westus* nahraďte názvem a umístěním, které zvolíte. Vytvořte skupinu prostředků. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

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

5. Vytvořte skupinu zabezpečení sítě (NSG) a pravidlo. NSG zabezpečuje virtuální počítač pomocí příchozích a odchozích pravidel. V tomto případě se vytvoří příchozí pravidlo pro port 3389, které umožní příchozí připojení ke vzdálené ploše.

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

6. Definujte primární konfiguraci IP pro síťovou kartu. Pokud jste nepoužili dříve definovanou hodnotu, změňte 10.0.0.4 na platnou adresu v podsíti, kterou jste vytvořili. Před přiřazením statické IP adresy doporučujeme, abyste nejdřív zkontrolovali, že se už nepoužívá. Zadejte příkaz `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet` . Pokud je adresa k dispozici, vrátí výstup *hodnotu true*. Pokud není k dispozici, vrátí výstup *hodnotu false* a seznam adres, které jsou k dispozici. 

    V následujících příkazech **nahraďte \<replace-with-your-unique-name> jedinečným názvem DNS, který se má použít.** Název musí být jedinečný napříč všemi veřejnými IP adresami v oblasti Azure. Toto je volitelný parametr. Pokud se chcete k virtuálnímu počítači připojit jenom pomocí veřejné IP adresy, můžete ho odebrat.

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

    Pokud přiřadíte více konfigurací protokolu IP k síťovému rozhraní, musí být jedna konfigurace přiřazena jako *primární*.

    > [!NOTE]
    > Veřejné IP adresy mají nominální poplatek. Pokud se chcete dozvědět víc o cenách IP adres, přečtěte si stránku [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) . Počet veřejných IP adres, které se dají použít v předplatném, je omezený. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

7. Definujte sekundární konfiguraci protokolu IP pro síťovou kartu. Konfigurace můžete podle potřeby přidávat nebo odebírat. Každá konfigurace protokolu IP musí mít přiřazenou privátní IP adresu. Každé konfiguraci může volitelně mít přiřazenou jednu veřejnou IP adresu.

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

8. Vytvořte síťovou kartu a přidružte k ní tři konfigurace protokolu IP:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >I když jsou všechny konfigurace přiřazené k jedné síťové kartě v tomto článku, můžete pro každou síťovou kartu připojenou k virtuálnímu počítači přiřadit několik konfigurací IP. Pokud chcete zjistit, jak vytvořit virtuální počítač s více síťovými rozhraními, přečtěte si článek [Vytvoření virtuálního počítače s několika síťovými](../virtual-machines/windows/multiple-nics.md) kartami.

9. Vytvořte virtuální počítač zadáním následujících příkazů:

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

10. Dokončete kroky pro operační systém v části [Přidání IP adres do operačního systému](#os-config) virtuálního počítače v tomto článku přidáním privátních IP adres do operačního systému virtuálního počítače. Nepřidávat veřejné IP adresy do operačního systému.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Přidání IP adres k virtuálnímu počítači

K síťovému rozhraní Azure můžete přidat privátní a veřejné IP adresy provedením následujících kroků. V příkladech v následujících oddílech se předpokládá, že už máte virtuální počítač se třemi konfiguracemi protokolu IP, které jsou popsané ve [scénáři](#scenario) tohoto článku, ale nemusíte to dělat.

1. Otevřete příkazový řádek PowerShellu a dokončete zbývající kroky v této části v rámci jedné relace PowerShellu. Pokud ještě nemáte nainstalované a nakonfigurované prostředí PowerShell, proveďte kroky popsané v článku [Postup instalace a konfigurace Azure PowerShell](/powershell/azure/) .
2. Změňte hodnoty z následujících $Variables na název síťové karty, do které chcete přidat IP adresu, a do skupiny prostředků a umístění, ve kterém síťová karta existuje:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Pokud neznáte název síťového adaptéru, který chcete změnit, zadejte následující příkazy a pak změňte hodnoty předchozích proměnných:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Vytvořte proměnnou a nastavte ji na stávající síťové rozhraní zadáním následujícího příkazu:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. V následujících příkazech změňte *MyVNet* a *MySubnet* na názvy virtuální sítě a podsíť, ke které je síťová karta připojena. Zadejte příkazy pro načtení virtuální sítě a objektů podsítě, ke kterým je síťová karta připojená:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Pokud neznáte virtuální síť nebo název podsítě, ke které je síťová karta připojena, zadejte následující příkaz:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Ve výstupu vyhledejte text podobný následujícímu příkladu výstupu:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    V tomto výstupu je *MyVnet* virtuální síť a *MySubnet* je podsíť, ke které je síťová karta připojená.

5. V závislosti na vašich požadavcích proveďte kroky v jedné z následujících částí:

   **Přidat privátní IP adresu**

   Chcete-li přidat privátní IP adresu do síťového rozhraní, je nutné vytvořit konfiguraci protokolu IP. Následující příkaz vytvoří konfiguraci se statickou IP adresou 10.0.0.7. Když zadáte statickou IP adresu, musí se jednat o nepoužitou adresu podsítě. Doporučuje se nejdřív otestovat adresu, abyste měli jistotu, že je k dispozici, zadáním `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` příkazu. Pokud je IP adresa dostupná, vrátí výstup *hodnotu true*. Pokud není k dispozici, vrátí výstup *hodnotu false* a zobrazí seznam adres, které jsou k dispozici.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Pomocí jedinečných názvů konfigurací a privátních IP adres (pro konfigurace se statickými IP adresami) vytvořte tolik konfigurací, kolik jich vyžadujete.

   Dokončete kroky pro operační systém v části [Přidání IP adres do operačního systému](#os-config) virtuálního počítače v tomto článku a přidejte tak privátní IP adresu do operačního systému virtuálního počítače.

   **Přidat veřejnou IP adresu**

   Veřejná IP adresa se přidá pomocí přidružení prostředku veřejné IP adresy k nové konfiguraci protokolu IP nebo existující konfiguraci protokolu IP. Proveďte kroky v jedné z částí, které následují, jak budete potřebovat.

   > [!NOTE]
   > Veřejné IP adresy mají nominální poplatek. Pokud se chcete dozvědět víc o cenách IP adres, přečtěte si stránku [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) . Počet veřejných IP adres, které se dají použít v předplatném, je omezený. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
   >

   **Přidružte prostředek veřejné IP adresy k nové konfiguraci protokolu IP.**

   Kdykoli přidáte veřejnou IP adresu v nové konfiguraci protokolu IP, musíte taky přidat privátní IP adresu, protože všechny konfigurace IP adres musí mít privátní IP adresu. Můžete buď přidat existující prostředek veřejné IP adresy, nebo vytvořit nový. Pokud chcete vytvořit nový, zadejte následující příkaz:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Pokud chcete vytvořit novou konfiguraci protokolu IP se statickou privátní IP adresou a přiřazeným prostředkem veřejné IP adresy *myPublicIp3* , zadejte následující příkaz:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Přidružte prostředek veřejné IP adresy k existující konfiguraci protokolu IP.**

   Prostředek veřejné IP adresy se dá přidružit jenom ke konfiguraci protokolu IP, která ještě nemá přidruženou žádnou přidruženou adresu. Zadáním následujícího příkazu můžete určit, jestli má konfigurace protokolu IP přidruženou veřejnou IP adresu:

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

   Vzhledem k tomu, že sloupec **PublicIpAddress** pro *příkaz ipconfig-3* je prázdný, není k němu aktuálně PŘIDRUŽEN žádný prostředek veřejné IP adresy. Existující prostředek veřejné IP adresy můžete přidat do příkazu IpConfig-3 nebo můžete zadat následující příkaz a vytvořit ho:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Zadejte následující příkaz pro přidružení prostředku veřejné IP adresy k existující konfiguraci protokolu IP s názvem *ipconfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Zadáním následujícího příkazu nastavte síťovou kartu s novou konfigurací IP adresy:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Zadáním následujícího příkazu Zobrazte soukromé IP adresy a prostředky veřejné IP adresy přiřazené k síťovému rozhraní:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Dokončete kroky pro operační systém v části [Přidání IP adres do operačního systému](#os-config) virtuálního počítače v tomto článku a přidejte tak privátní IP adresu do operačního systému virtuálního počítače. Nepřidejte veřejnou IP adresu k operačnímu systému.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]