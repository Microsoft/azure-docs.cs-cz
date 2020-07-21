---
title: Shromažďování podrobností o všech virtuálních počítačích v rámci předplatného s využitím PowerShellu
description: Shromažďování podrobností o všech virtuálních počítačích v rámci předplatného s využitím PowerShellu
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: a45f0a882c58c7035badcc1270c66bd9c6fb252a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526907"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Shromažďování podrobností o všech virtuálních počítačích v rámci předplatného s využitím PowerShellu

Tento skript vytvoří sdílený svazek clusteru, který obsahuje název virtuálního počítače, název skupiny prostředků, oblast, velikost virtuálního počítače, Virtual Network, podsíť, privátní IP adresu, typ operačního systému a veřejnou IP adresu virtuálních počítačů v zadaném předplatném.

Pokud ještě nemáte [předplatné Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="sample-script"></a>Ukázkový skript

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VmSize, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VmSize = $vm.HardwareProfile.VmSize
        $info.VirturalNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VmSize, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá následující příkazy k vytvoření exportu CSV s podrobnostmi o virtuálních počítačích v rámci předplatného. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

|Příkaz|Poznámky|
|-|-|
|[Vybrat – AzSubscription](/powershell/module/az.accounts/set-azcontext)|Nastaví klienta, předplatné a prostředí pro rutiny, které se použijí v aktuální relaci.|
|[Get-AzVM](/powershell/module/az.compute/get-azvm)|Získá vlastnosti virtuálního počítače.|
|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)|Získá veřejnou IP adresu.|
|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface)|Načte síťové rozhraní.|

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=/azure/virtual-machines/windows/toc.json).
