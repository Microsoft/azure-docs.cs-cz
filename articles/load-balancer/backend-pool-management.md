---
title: Správa fondu back-endu
titleSuffix: Azure Load Balancer
description: Začněte s učením, jak nakonfigurovat a spravovat back-end fond Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: c49a721a4db758965c9cf8d71f5d73b5754b6088
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654471"
---
# <a name="backend-pool-management"></a>Správa fondu back-endu
Back-end fond je kritickou součástí nástroje pro vyrovnávání zatížení. Back-end fond definuje skupinu prostředků, které budou obsluhovat provoz pro dané pravidlo vyrovnávání zatížení.

Existují dva způsoby konfigurace fondu back-endu:
* Síťová karta (NIC)
* Kombinace ID prostředku IP adresy a Virtual Network (VNET)

Nakonfigurujte svůj fond back-end podle síťových adaptérů při použití existujících virtuálních počítačů a služby Virtual Machine Scale Sets. Tato metoda vytváří nejpřímější propojení mezi vaším prostředkem a back-end fondem. 

Při předrozdělování back-endu s rozsahem IP adres, který plánujete později vytvořit virtuální počítače a služby Virtual Machine Scale Sets, nakonfigurujte svůj fond back-end podle kombinace IP adresy a ID virtuální sítě.

Pro stejný nástroj pro vyrovnávání zatížení můžete nakonfigurovat fondy back-endu založených na protokolu IP a síťových adaptérů, ale nemůžete vytvořit jeden back-end fond, který kombinuje zálohované adresy, na které cílí síťové karty a IP adresy v rámci stejného fondu.

Konfigurační oddíly tohoto článku se zaměří na:

* Azure PowerShell
* Azure CLI
* REST API
* Šablony Azure Resource Manageru 

Tyto části poskytují přehled o struktuře back-end fondů pro každou možnost konfigurace.

## <a name="configuring-backend-pool-by-nic"></a>Konfigurace fondu back-endu podle síťové karty
Back-end fond je vytvořen jako součást operace nástroje pro vyrovnávání zatížení. Vlastnost konfigurace protokolu IP síťového rozhraní se používá k přidání členů fondu back-end.

Následující příklady jsou zaměřené na operace vytvoření a naplnění fondu back-end pro zdůraznění tohoto pracovního postupu a vztahu.

  >[!NOTE] 
  >Je důležité si uvědomit, že back-endové fondy nakonfigurované přes síťové rozhraní se nedají aktualizovat v rámci operace ve fondu back-endu. Jakékoli přidání nebo odstranění prostředků back-endu se musí objevit v síťovém rozhraní prostředku.

### <a name="powershell"></a>PowerShell
Vytvořit nový back-end fond:
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Vytvořte nové síťové rozhraní a přidejte ho do fondu back-end:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

Načtěte informace o fondu back-end pro nástroj pro vyrovnávání zatížení, abyste potvrdili, že toto síťové rozhraní je přidané do fondu back-endu:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

Vytvořte nový virtuální počítač a připojte síťové rozhraní k umístění do back-endu fondu:

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>Rozhraní příkazového řádku
Vytvořte back-end fond:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

Vytvořte nové síťové rozhraní a přidejte ho do fondu back-end:

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

Načtěte back-end fond pro potvrzení, že se IP adresa přidala správně:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

Vytvořte nový virtuální počítač a připojte síťové rozhraní k umístění do back-endu fondu:

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Podle tohoto [rychlého startu správce prostředků šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) nasaďte Nástroj pro vyrovnávání zatížení a virtuální počítače a přidejte virtuální počítače do back-endového fondu prostřednictvím síťového rozhraní.

Podle tohoto [rychlého startu správce prostředků šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-ip-configured-backend-pool) nasaďte Nástroj pro vyrovnávání zatížení a virtuální počítače a přidejte virtuální počítače do back-endu fondu prostřednictvím IP adresy.


## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>Konfigurace fondu back-endu podle IP adresy a virtuální sítě
Ve scénářích s předem vyplněnými back-end fondy použijte IP a virtuální síť.

Veškerá správa back-end fondu se provádí přímo na objektu back-end fondu, který je zvýrazněný v níže uvedených příkladech.

### <a name="powershell"></a>PowerShell
Vytvořit nový back-end fond:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

Aktualizovat back-end fond s novou IP adresou z existující virtuální sítě:
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

Načtěte informace o fondu back-end pro nástroj pro vyrovnávání zatížení, abyste ověřili, že se back-endové adresy přidávají do back-endu.

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
Vytvořte síťové rozhraní a přidejte ho do fondu back-end. Nastavte IP adresu na jednu z back-endové adresy:

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

Vytvořte virtuální počítač a připojte síťovou kartu s IP adresou ve fondu back-endu:
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>Rozhraní příkazového řádku
Pomocí rozhraní příkazového řádku můžete buď naplnit back-end fond prostřednictvím parametrů příkazového řádku nebo pomocí konfiguračního souboru JSON. 

Vytvořte a naplňte back-end fond prostřednictvím parametrů příkazového řádku:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

Vytvořte a naplňte back-end fond prostřednictvím konfiguračního souboru JSON:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

Konfigurační soubor JSON:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

Načtěte informace o fondu back-end pro nástroj pro vyrovnávání zatížení, abyste ověřili, že se back-endové adresy přidávají do back-endu.

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

Vytvořte síťové rozhraní a přidejte ho do fondu back-end. Nastavte IP adresu na jednu z back-endové adresy:

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Vytvořte virtuální počítač a připojte síťovou kartu s IP adresou ve fondu back-endu:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```
 
### <a name="limitations"></a>Omezení
Back-end fond konfigurovaný podle IP adresy má následující omezení:
  * Dá se použít jenom pro standardní nástroje pro vyrovnávání zatížení.
  * Limit 100 IP adres ve fondu back-endu
  * Back-endové prostředky musí být ve stejné virtuální síti jako nástroj pro vyrovnávání zatížení.
  * Load Balancer s back-end fondem založeným na IP adrese nemůže fungovat jako služba privátního propojení.
  * Tato funkce se v současnosti v Azure Portal nepodporuje.
  * Tato funkce aktuálně nepodporuje kontejnery ACI.
  * Nástroje pro vyrovnávání zatížení nebo služby, jako je Application Gateway, se nedají umístit do back-endu fondu nástroje pro vyrovnávání zatížení.
  * Příchozí pravidla překladu adres (NAT) nejde zadat podle IP adresy.

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli o Azure Load Balancer správě fondu back-endu a o tom, jak nakonfigurovat back-end fond podle IP adresy a virtuální sítě.

Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).

Přečtěte si [REST API](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerbackendaddresspools/createorupdate) pro správu problémových na základě IP adres.
