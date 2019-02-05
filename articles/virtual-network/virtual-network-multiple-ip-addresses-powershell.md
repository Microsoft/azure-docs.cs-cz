---
title: Několik IP adres pro Azure virtual machines – PowerShell | Dokumentace Microsoftu
description: Zjistěte, jak přiřadit několik IP adres k virtuálnímu počítači pomocí Powershellu | Resource Manager.
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
ms.openlocfilehash: 0e8ab6a130bb99c3b0b1b811f52559273ac3c483
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691570"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Přiřadit několik IP adres virtuálních počítačů pomocí Powershellu

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Tento článek vysvětluje, jak vytvořit virtuální počítač (VM) prostřednictvím modelu nasazení Azure Resource Manageru pomocí Powershellu. Několik IP adres nelze přiřadit k prostředkům vytvořeným prostřednictvím modelu nasazení classic. Další informace o modelech nasazení Azure najdete v článku [vysvětlení modelů nasazení](../resource-manager-deployment-model.md) článku.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Vytvoření virtuálního počítače s několika IP adresami

Následující kroky popisují, jak vytvořit příklad virtuálního počítače s několika IP adresami, jak je popsáno ve scénáři. Změna hodnot proměnných, jak je vyžadováno pro vaši implementaci.

1. Otevřete příkazový řádek Powershellu a dokončete zbývající kroky v této části v rámci jedné relace Powershellu. Pokud ještě nemáte Powershellu nainstalovanou a nakonfigurovanou, dokončete kroky [instalace a konfigurace Azure Powershellu](/powershell/azure/overview) článku.
2. Přihlášení k účtu se `Connect-AzureRmAccount` příkazu.
3. Nahraďte *myResourceGroup* a *westus* se název a umístění podle vašeho výběru. Vytvořte skupinu prostředků. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

    ```powershell
    $RgName   = "MyResourceGroup"
    $Location = "westus"

    New-AzureRmResourceGroup `
    -Name $RgName `
    -Location $Location
    ```

4. Vytvoření virtuální sítě (VNet) a podsíti ve stejném umístění jako skupina prostředků:

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

5. Vytvořte skupinu zabezpečení sítě (NSG) a pravidla. Skupiny zabezpečení sítě zabezpečuje virtuální počítač pomocí příchozích a odchozích pravidel. V tomto případě se vytvoří příchozí pravidlo pro port 3389, které umožní příchozí připojení ke vzdálené ploše.

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

6. Definovat primární konfigurace protokolu IP pro síťové rozhraní Pokud jste nepoužili hodnotu, dříve definovanou, změňte na platnou adresou v podsíti, ve které jste vytvořili, 10.0.0.4. Před přiřazením statické IP adresy, doporučujeme nejdřív ověřit, že není již používáno. Zadejte příkaz `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Pokud je k dispozici na adresu, na výstupu vrátí *True*. Pokud není k dispozici, na výstupu vrátí *False* a seznam adres, které jsou k dispozici. 

    V následujících příkazech **< nahradit s your jedinečný – name > nahraďte jedinečný název DNS k použití.** Název musí být jedinečný mezi všechny veřejné IP adresy v rámci oblasti Azure. Toto je volitelný parametr. Je možné odebrat, pokud se chcete připojit k virtuálnímu počítači pomocí veřejné IP adresy.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Pokud přiřadíte více konfigurací protokolu IP k síťové KARTĚ, jedna konfigurace musí být přiřazena jako *– primární*.

    > [!NOTE]
    > Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres, [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. Platí omezení na počet veřejné IP adresy, které je možné v rámci předplatného. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).

7. Definovat sekundární konfigurace IP pro síťové rozhraní Můžete přidat nebo odebrat konfigurace podle potřeby. Každou konfiguraci IP adresy musí mít přiřazenou privátní IP adresu. Každá konfigurace můžou mít přiřazenou jednu veřejnou IP adresu.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
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

8. Vytvořte síťové rozhraní a přidružte tří konfigurací protokolu IP k ní:

    ```powershell
    
    $NIC = New-AzureRmNetworkInterface `
    -Name MyNIC `
    -ResourceGroupName $RgName `
    -Location $Location `
    -NetworkSecurityGroupId $NSG.Id `
    -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```

    >[!NOTE]
    >I když jsou přiřazeny všech konfigurací s jedním síťovým rozhraním v tomto článku, můžete přiřadit více konfigurací protokolu IP pro každé síťové rozhraní připojené k virtuálnímu počítači. Zjistěte, jak vytvořit virtuální počítač s několika síťovými kartami, přečtěte si [vytvoření virtuálního počítače s několika síťovými kartami](../virtual-machines/windows/multiple-nics.md) článku.

9. Vytvoření virtuálního počítače tak, že zadáte následující příkazy:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
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

10. Přidat privátních IP adres do operačního systému virtuálního počítače pomocí kroků pro váš operační systém v [přidat IP adresy na operační systém virtuálního počítače](#os-config) části tohoto článku. Nepřidávejte veřejných IP adres do operačního systému.

## <a name="add"></a>Přidejte IP adresy virtuálního počítače

Privátní a veřejné IP adresy Azure síťovému rozhraní můžete přidat provedením následujících kroků. Příklady v následujících částech Předpokládejme, že máte již virtuální počítač s tři konfigurace protokolu IP je popsáno v [scénář](#Scenario) v tomto článku, ale není nutné, abyste udělali.

1. Otevřete příkazový řádek Powershellu a dokončete zbývající kroky v této části v rámci jedné relace Powershellu. Pokud ještě nemáte Powershellu nainstalovanou a nakonfigurovanou, dokončete kroky [instalace a konfigurace Azure Powershellu](/powershell/azure/overview) článku.
2. Změňte název síťového rozhraní, které chcete přidat IP adresu, která a skupinu prostředků a umístění, které se síťové rozhraní existuje v "hodnoty" $Variables následující:

    ```powershell
    $NicName  = "MyNIC"
    $RgName   = "MyResourceGroup"
    $Location = "westus"
    ```

    Pokud si nejste jisti názvem síťového adaptéru, kterou chcete změnit, zadejte následující příkazy, změňte hodnoty proměnných předchozí:

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
3. Vytvořte proměnnou a nastavte ho na existující síťovou kartu tak, že zadáte následující příkaz:

    ```powershell
    $MyNIC = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName
    ```
4. V následujících příkazech změnit *MyVNet* a *MySubnet* názvy virtuální sítě a podsítě, síťová karta je připojená k. Zadejte virtuální síť a podsíť objekty, které je síťové rozhraní připojené k načtení příkazy:

    ```powershell
    $MyVNet = Get-AzureRMVirtualNetwork -Name MyVNet -ResourceGroupName $RgName
    $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
    ```
    Pokud si nejste jisti, které je síťové rozhraní připojené k název virtuální sítě nebo podsítě, zadejte následující příkaz:
    ```powershell
    $MyNIC.IpConfigurations
    ```
    Ve výstupu vyhledejte text podobně jako následující příklad výstupu:
    
    ```
    "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
    ```
    Tento výstup *MyVnet* je virtuální síť a *MySubnet* je síťová karta je připojená k podsíti.

5. Proveďte kroky v jednom z následujících částí, na základě vašich požadavků:

    **Přidejte privátní IP adresy**

    Chcete-li přidat privátní IP adresu k síťové KARTĚ, musíte vytvořit konfiguraci IP. Následující příkaz vytvoří konfiguraci se statickou IP adresou 10.0.0.7. Při zadávání statickou IP adresu, musí být nepoužívaná adresa podsítě. Doporučuje se nejdřív otestovat adresu zajistit, je k dispozici tak, že zadáte `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` příkazu. Pokud je k dispozici IP adresu, na výstupu vrátí *True*. Pokud není k dispozici, na výstupu vrátí *False*a seznam adres, které jsou k dispozici.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
    $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    Vytvořte tolik konfigurace podle potřeby, pomocí jedinečnou konfiguraci názvy a privátní IP adresy (pro konfigurace se statickými IP adresami).

    Přidejte privátní IP adresu v operačním systému virtuálního počítače pomocí kroků pro váš operační systém v [přidat IP adresy na operační systém virtuálního počítače](#os-config) části tohoto článku.

    **Přidání veřejné IP adresy**

    Veřejná IP adresa se přidá tím, že přidružíte prostředek veřejné IP adresy pro novou konfiguraci protokolu IP nebo existující konfigurace protokolu IP. Proveďte kroky v jednom z částí, které následují, podle potřeby.

    > [!NOTE]
    > Veřejné IP adresy mají nominální poplatek. Další informace o cenách IP adres, [ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses) stránky. Platí omezení na počet veřejné IP adresy, které je možné v rámci předplatného. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    - **Přidružte prostředek veřejné IP adresy na novou konfiguraci protokolu IP**
    
        Pokaždé, když přidáte novou konfiguraci IP veřejnou IP adresu, musíte také přidat privátní IP adresu, protože všechny konfigurace protokolu IP, musí mít privátní IP adresu. Můžete přidat existující prostředek veřejné IP adresy, nebo vytvořte novou. Chcete-li vytvořit nový, zadejte následující příkaz:
    
        ```powershell
        $myPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "myPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location `
        -AllocationMethod Static
        ```

        Chcete-li vytvořit novou konfiguraci IP adresy se statickou privátní IP adresou a přidružené *myPublicIp3* veřejné IP adresy prostředků, zadejte následující příkaz:

        ```powershell
        Add-AzureRmNetworkInterfaceIpConfig `
        -Name IPConfig-4 `
        -NetworkInterface $myNIC `
        -Subnet $Subnet `
        -PrivateIpAddress 10.0.0.7 `
        -PublicIpAddress $myPublicIp3
        ```

    - **Přidružte prostředek veřejné IP adresy do existující konfigurace IP**

        Prostředek veřejné IP adresy lze přidružit pouze ke konfiguraci IP, která ještě neexistuje spojené. Můžete určit, zda má konfiguraci IP přidružených veřejnou IP adresu tak, že zadáte následující příkaz:

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

        Protože **PublicIpAddress** sloupec pro *IpConfig 3* je prázdný, žádný prostředek veřejné IP adresy je aktuálně k ní přidružena. Můžete přidat existující prostředek veřejné IP adresy na IpConfig 3, nebo zadejte následující příkaz k jejímu vytvoření:

        ```powershell
        $MyPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "MyPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location -AllocationMethod Static
        ```

        Zadejte následující příkaz, který přidružte prostředek veřejné IP adresy ke stávající konfiguraci IP adresy s názvem *IpConfig 3*:
    
        ```powershell
        Set-AzureRmNetworkInterfaceIpConfig `
        -Name IpConfig-3 `
        -NetworkInterface $mynic `
        -Subnet $Subnet `
        -PublicIpAddress $myPublicIp3
        ```

6. Nastavte síťové karty s novou konfigurací protokolu IP tak, že zadáte následující příkaz:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $MyNIC
    ```

7. Zobrazte soukromé IP adresy a prostředky veřejné adresy IP adresy přiřazené k síťovému rozhraní tak, že zadáte následující příkaz:

    ```powershell   
    $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. Přidejte privátní IP adresu v operačním systému virtuálního počítače pomocí kroků pro váš operační systém v [přidat IP adresy na operační systém virtuálního počítače](#os-config) části tohoto článku. Nepřidávejte veřejnou IP adresu do operačního systému.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
