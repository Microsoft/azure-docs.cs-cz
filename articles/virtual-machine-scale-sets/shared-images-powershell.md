---
title: Vytvoření škálovací sady v Azure pomocí sdílených ibi virtuálních počítačích
description: Zjistěte, jak pomocí Azure PowerShellu vytvářet sdílené image virtuálních virtuálních zařízení, které se použijí k nasazení škálovacích sad virtuálních strojů v Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 5f4eca88614a98f0caf87d04847029328042edd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368736"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Vytváření a používání sdílených ibipro škálovací sady virtuálních strojů pomocí Azure PowerShellu

Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. Služba Galerie sdílených obrázků výrazně zjednodušuje vlastní sdílení obrázků v celé organizaci. Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. 

Galerie sdílených obrázků umožňuje sdílet vlastní image virtuálních počítačů s ostatními uživateli ve vaší organizaci, v rámci nebo napříč oblastmi, v rámci klienta AAD. Zvolte, které obrázky chcete sdílet, ve kterých oblastech je chcete zpřístupnit a s kým je chcete sdílet. Můžete vytvořit více galerií, abyste mohli logicky seskupit sdílené obrázky. 

Galerie je prostředek nejvyšší úrovně, který poskytuje úplné řízení přístupu na základě rolí (RBAC). Image může být verzí a můžete se rozhodnout replikovat každou verzi bitové kopie do jiné sady oblastí Azure. Galerie pracuje pouze se spravovanými obrázky. 

Funkce Galerie sdílených obrázků má více typů prostředků. Budeme používat nebo stavební tyto v tomto článku:

| Prostředek | Popis|
|----------|------------|
| **Spravovaná bitová kopie** | Jedná se o základní obrázek, který lze použít samostatně nebo použít k vytvoření **verze obrázku** v galerii obrázků. Spravované bitové kopie se vytvářejí z generalizovaných virtuálních měn. Spravovaná bitová kopie je speciální typ virtuálního pevného disku, který lze použít k vytvoření více virtuálních počítačů a nyní lze použít k vytvoření verzí sdílených bitových obrázků. |
| **Galerie obrázků** | Stejně jako Azure Marketplace, **galerie obrázků** je úložiště pro správu a sdílení bitových kopií, ale máte řízení, kdo má přístup. |
| **Definice obrázku** | Obrázky jsou definovány v galerii a nesou informace o obrázku a požadavky na jeho použití interně. To zahrnuje, zda je bitová kopie Windows nebo Linux, poznámky k verzi a minimální a maximální požadavky na paměť. Jedná se o definici typu obrazu. |
| **Verze obrázku** | **Verze bitové kopie** je to, co používáte k vytvoření virtuálního počítačů při použití galerie. Podle potřeby pro vaše prostředí můžete mít více verzí bitové kopie. Stejně jako spravovaná **image version** bitová kopie, když k vytvoření virtuálního počítače použijete verzi image k vytvoření nových disků pro virtuální počítače. Verze obrázků lze použít vícekrát. |

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

Chcete-li dokončit příklad v tomto článku, musíte mít existující spravovanou bitovou kopii. Můžete postupovat [podle kurzu: Vytvoření a použití vlastní image pro škálovací sady virtuálních strojů s Azure PowerShell](tutorial-use-custom-image-powershell.md) em a v případě potřeby ji vytvořit. Pokud spravovaná bitová kopie obsahuje datový disk, velikost datového disku nesmí být větší než 1 TB.

Při práci v článku, nahradit skupinu prostředků a názvy virtuálních počítačů v případě potřeby.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Vytvoření škálovací sady z verze sdíleného obrázku

Vytvořte škálovací sadu virtuálních strojů s [novou AzVmss](/powershell/module/az.compute/new-azvmss). Následující příklad vytvoří škálovací sadu z nové verze bitové kopie v datovém centru *–střed USA.* Po zobrazení výzvy nastavte vlastní pověření pro správu pro instance virtuálních počítače ve škálovací sadě:


```azurepowershell-interactive
# Define variables
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"
$cred = Get-Credential

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a netowrking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageVersion.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]


## <a name="next-steps"></a>Další kroky

Prostředek Galerie sdílených obrázků můžete také vytvořit pomocí šablon. K dispozici je několik šablon Azure QuickStart: 

- [Vytvoření galerie sdílených obrázků](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definice obrazu ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze obrázku ve sdílené galerii obrázků](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Vytvoření virtuálního virtuálního virtuálního mísy z verze bitové kopie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Další informace o sdílených galeriích obrázků naleznete v [tématu Přehled](shared-image-galleries.md). Pokud narazíte na problémy, [přečtěte si článek Poradce při potížích s galeriemi sdílených obrázků](troubleshooting-shared-images.md).
