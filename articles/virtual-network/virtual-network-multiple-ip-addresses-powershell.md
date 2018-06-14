---
title: Více IP adres pro virtuální počítače Azure – prostředí PowerShell | Microsoft Docs
description: Zjistěte, jak přiřadit více IP adres k virtuálnímu počítači pomocí prostředí PowerShell | Správce prostředků.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: jdial;annahar
ms.openlocfilehash: 4c74833933642ec67bdd2a77d073b083d54a3038
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602836"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Přiřadit více IP adres virtuálních počítačů pomocí prostředí PowerShell

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Tento článek vysvětluje, jak vytvořit virtuální počítač (VM) pomocí modelu nasazení Azure Resource Manager pomocí prostředí PowerShell. Nelze přiřadit více IP adres k prostředkům, které jsou vytvořené pomocí modelu nasazení classic. Další informace o modelech nasazení Azure, najdete [pochopit modely nasazení](../resource-manager-deployment-model.md) článku.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Vytvoření virtuálního počítače s více IP adres

Kroky, které následují vysvětlují, jak vytvořit příklad virtuálních počítačů s více IP adres, jak je popsáno v tomto scénáři. Změňte hodnoty proměnných podle potřeby týkající se vaší implementace.

1. Otevřete příkazový řádek prostředí PowerShell a dokončit zbývající kroky v této části v rámci jedné relace prostředí PowerShell. Pokud ještě nemáte prostředí PowerShell nainstalovaný a nakonfigurovaný, proveďte kroky v [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) článku.
2. Přihlášení k účtu s `Connect-AzureRmAccount` příkaz.
3. Nahraďte *myResourceGroup* a *westus* s názvem a umístění dle vlastního výběru. Vytvořte skupinu prostředků. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

    ```powershell
    $RgName   = "MyResourceGroup"
    $Location = "westus"

    New-AzureRmResourceGroup `
    -Name $RgName `
    -Location $Location
    ```

4. Vytvořte virtuální síť (VNet) a podsíť ve stejném umístění jako pro skupinu prostředků:

    ```powershell
    
    # Create a subnet configuration
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name MySubnet `
    -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $VNet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyVNet `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $subnetConfig

    # Get the subnet object
    $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
    ```

5. Vytvořte skupinu zabezpečení sítě (NSG) a pravidla. NSG zabezpečuje virtuálního počítače pomocí příchozí a odchozí pravidla. V tomto případě se vytvoří příchozí pravidlo pro port 3389, které umožní příchozí připojení ke vzdálené ploše.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzureRmNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Zadejte primární konfiguraci IP adresy pro síťovou kartu. Pokud jste nepoužili hodnota definovaná dříve, změňte na platnou adresou v podsíti, kterou jste vytvořili, 10.0.0.4. Před přiřazením statické IP adresy, doporučujeme nejdřív ověřit, zda že se již používá. Zadejte příkaz `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Pokud je k dispozici na adresu, vrátí výstup *True*. Pokud není k dispozici, vrátí výstup *False* a seznam adres, které jsou k dispozici. 

    V následujících příkazech **< nahradit s vaše jedinečné name > nahraďte jedinečný název DNS k použití.** Název musí být jedinečný mezi všechny veřejné IP adresy v rámci oblasti Azure. Toto je volitelný parametr. Může být odebrán, pokud se chcete připojit k virtuálnímu počítači pomocí veřejné adresy IP.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Když přiřadíte víc konfigurací IP adres síťovou kartu, musí být přiřazena jednu konfiguraci jako *-primární*.

    > [!NOTE]
    > Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adresu, najdete [IP adresu ceny](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. Maximální počet veřejné IP adresy, které lze použít v předplatném je. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).

7. Definují sekundární konfigurace IP síťovém adaptéru. Můžete přidávat nebo odebírat konfigurace podle potřeby. Každá konfigurace IP musí mít přiřazené privátní IP adresy. Každá konfigurace můžete volitelně může mít jednu veřejnou IP adresu přiřadit.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Vytvořte na síťový adaptér a přidružte tři konfigurace protokolu IP k němu:

    ```powershell
    
    $NIC = New-AzureRmNetworkInterface `
    -Name MyNIC `
    -ResourceGroupName $RgName `
    -Location $Location `
    -NetworkSecurityGroupId $NSG.Id `
    -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```

    >[!NOTE]
    >Když na jeden síťový adaptér v tomto článku jsou přiřazeny všechny konfigurace, můžete přiřadit víc konfigurací IP adres pro každou síťovou kartu připojenou k virtuálnímu počítači. Naučte se vytvořit virtuální počítač s více síťovými kartami, přečtěte si téma [vytvoření virtuálního počítače s více síťovými kartami](../virtual-machines/windows/multiple-nics.md) článku.

9. Vytvoření virtuálního počítače tak, že zadáte následující příkazy:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a sername and password for the VM you're reating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzureRmVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzureRmVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Přidání privátních IP adres do operačního systému virtuálního počítače pomocí kroků pro operační systém v [přidat IP adresy na operační systém virtuálního počítače](#os-config) tohoto článku. Nepřidávejte veřejné IP adresy v operačním systému.

## <a name="add"></a>Přidání IP adres pro virtuální počítač

Provedením následujících kroků můžete přidat privátní a veřejné IP adresy rozhraní sítě Azure. Příklady v následujících částech předpokládají, že už máte virtuální počítač s tři konfigurace protokolu IP, které jsou popsané v [scénář](#Scenario) v tomto článku, ale není to nutné, abyste provedli.

1. Otevřete příkazový řádek prostředí PowerShell a dokončit zbývající kroky v této části v rámci jedné relace prostředí PowerShell. Pokud ještě nemáte prostředí PowerShell nainstalovaný a nakonfigurovaný, proveďte kroky v [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) článku.
2. Změňte název síťového adaptéru, který chcete přidat IP adresu a skupinu prostředků a umístění, které síťový adaptér existuje v "hodnoty" $Variables následující:

    ```powershell
    $NicName  = "MyNIC"
    $RgName   = "MyResourceGroup"
    $Location = "westus"
    ```

    Pokud neznáte název síťového adaptéru, který chcete změnit, zadejte následující příkazy, pak změňte hodnoty proměnných předchozí:

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
3. Vytvoření proměnné a nastavte ji na stávající síťové karty tak, že zadáte následující příkaz:

    ```powershell
    $MyNIC = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName
    ```
4. V následujících příkazech změnit *MyVNet* a *MySubnet* na názvy virtuálních sítí a podsítí, které jsou na síťový adaptér je připojen k. Zadejte příkazy pro načtení objektů virtuálních sítí a podsítí, které síťový adaptér je připojen k:

    ```powershell
    $MyVNet = Get-AzureRMVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
    $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
    ```
    Pokud neznáte název virtuální síť nebo podsíť, které síťový adaptér je připojen k, zadejte následující příkaz:
    ```powershell
    $MyNIC.IpConfigurations
    ```
    Ve výstupu vyhledejte text podobné výstupu v následujícím příkladu:
    
    ```
    "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
    ```
    V tento výstup *MyVnet* je síť VNet a *MySubnet* je síťový adaptér je připojený k podsíti.

5. Proveďte kroky v jednom z těchto částí, podle potřeb:

    **Přidejte privátní IP adresy**

    Chcete-li přidat privátní IP adresy pro síťový adaptér, je nutné vytvořit konfiguraci IP adres. Následující příkaz vytvoří konfiguraci se statickou IP adresou 10.0.0.7. Při zadávání statickou IP adresu, musí být nepoužívané adresu podsítě. Doporučuje se nejdřív otestovat adresu, zda je k dispozici zadáním `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` příkaz. Pokud IP adresa je k dispozici, vrátí výstup *True*. Pokud není k dispozici, vrátí výstup *False*a seznam adres, které jsou k dispozici.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
    $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    Vytvořte tolik konfigurace, jak požadujete, pomocí konfigurace jedinečné názvy a privátní IP adresy (pro konfigurace s statické IP adresy).

    Přidání privátní IP adresu do operačního systému virtuálního počítače pomocí kroků pro operační systém v [přidat IP adresy na operační systém virtuálního počítače](#os-config) tohoto článku.

    **Přidejte veřejnou IP adresu**

    Veřejná IP adresa se přidá tím, že přidružíte prostředek veřejné IP adresy na novou konfiguraci protokolu IP nebo existující konfiguraci IP adres. Proveďte kroky v jednom z následujících, potřebujete.

    > [!NOTE]
    > Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adresu, najdete [IP adresu ceny](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. Maximální počet veřejné IP adresy, které lze použít v předplatném je. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    - **Přidružte prostředek veřejné IP adresy na novou konfiguraci protokolu IP**
    
        Vždy, když přidáte veřejnou IP adresu v novou konfiguraci protokolu IP, musíte taky přidat privátní IP adresy, protože všechny konfigurace protokolu IP, musí mít privátní IP adresy. Můžete přidat existující prostředek veřejné IP adresy, nebo vytvořte novou. Pokud chcete vytvořit nový, zadejte následující příkaz:
    
        ```powershell
        $myPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "myPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location `
        -AllocationMethod Static
        ```

        Chcete-li vytvořit novou konfiguraci protokolu IP se statickou privátní IP adresou a přidruženého *myPublicIp3* veřejných IP adres prostředků, zadejte následující příkaz:

        ```powershell
        Add-AzureRmNetworkInterfaceIpConfig `
        -Name IPConfig-4 `
        -NetworkInterface $myNIC `
        -Subnet $Subnet `
        -PrivateIpAddress 10.0.0.7 `
        -PublicIpAddress $myPublicIp3
        ```

    - **Přidružte prostředek veřejné IP adresy na existující konfiguraci IP adres**

        Prostředek veřejné IP adresy lze přidružit pouze pro konfiguraci IP adres, který ho přidružené neobsahuje. Můžete určit, zda má konfiguraci IP adres přidružené veřejnou IP adresu tak, že zadáte následující příkaz:

        ```powershell
        $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
        ```

        Zobrazí výstup podobný následujícímu:

        ```     
        Name       PrivateIpAddress PublicIpAddress                                           Primary
        
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False
        ```

        Vzhledem k tomu **PublicIpAddress** sloupec pro *IpConfig 3* je prázdné, žádný prostředek veřejné IP adresy je aktuálně k ní přidružena. Můžete přidat existující prostředek veřejné IP adresy na IpConfig 3, nebo zadejte následující příkaz k jeho vytvoření:

        ```powershell
        $MyPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "MyPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location -AllocationMethod Static
        ```

        Zadejte následující příkaz pro přidružení prostředek veřejné IP adresy ke stávající konfiguraci IP adresy s názvem *IpConfig 3*:
    
        ```powershell
        Set-AzureRmNetworkInterfaceIpConfig `
        -Name IpConfig-3 `
        -NetworkInterface $mynic `
        -Subnet $Subnet `
        -PublicIpAddress $myPublicIp3
        ```

6. Síťový adaptér s novou konfigurací IP adresy nastavte tak, že zadáte následující příkaz:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $MyNIC
    ```

7. Zobrazení privátních IP adres a prostředky veřejné adresy IP adresy přiřazené na síťový adaptér tak, že zadáte následující příkaz:

    ```powershell   
    $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. Přidání privátní IP adresu do operačního systému virtuálního počítače pomocí kroků pro operační systém v [přidat IP adresy na operační systém virtuálního počítače](#os-config) tohoto článku. Nepřidávejte veřejnou IP adresu do operačního systému.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
