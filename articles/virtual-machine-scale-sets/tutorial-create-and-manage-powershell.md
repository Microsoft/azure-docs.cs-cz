---
title: Kurz – Vytvoření a správa škálovací sady virtuálních počítačů Azure | Microsoft Docs
description: Zjistěte, jak pomocí Azure PowerShellu vytvořit škálovací sadu virtuálních počítačů a provádět několik běžných úloh správy, jako je spuštění a zastavení instance nebo změna kapacity škálovací sady.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 54f63ec4cddf64110eadf25fff60167238f9f9a6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Kurz: Vytvoření a správa škálovací sady virtuálních počítačů pomocí Azure PowerShellu
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. V průběhu životního cyklu škálovací sady virtuálních počítačů možná budete potřebovat spustit jednu nebo více úloh správy. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření škálovací sady virtuálních počítačů a připojení k ní
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí instancí virtuálních počítačů
> * Ruční škálování škálovací sady
> * Provádění běžných úloh správy škálovací sady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure. 


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením škálovací sady virtuálních počítačů. Vytvořte skupinu prostředků pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). V tomto příkladu se vytvoří skupina prostředků *myResourceGroup* v oblasti *EastUS*. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
Název skupiny prostředků zadáte při vytváření nebo úpravě škálovací sady v rámci tohoto kurzu.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Nejprve pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce instancí virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí rutiny [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

Vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Zobrazení instancí virtuálních počítačů ve škálovací sadě
Pokud chcete zobrazit seznam instancí virtuálních počítačů ve škálovací sadě, použijte rutinu [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) následujícím způsobem:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Následující příklad výstupu ukazuje dvě instance virtuálních počítačů ve škálovací sadě:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Pokud chcete zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte k rutině [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) parametr `-InstanceId`. Následující příklad zobrazí informace o instanci virtuálního počítače *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Výpis informací o připojení
K nástroji pro vyrovnávání zatížení, který směruje provoz do jednotlivých instancí virtuálních počítačů, se přiřadí veřejná IP adresa. Ve výchozím nastavení se k nástroji pro vyrovnávání zatížení Azure, který směruje provoz vzdáleného připojení do jednotlivých virtuálních počítačů na daném portu, přidají pravidla překladu adres (NAT). Pokud se chcete připojit k instancím virtuálních počítačů ve škálovací sadě, vytvořte vzdálené připojení k přiřazené veřejné IP adrese a číslu portu.

Pokud chcete vypsat porty překladu adres pro připojení k instancím virtuálních počítačů ve škálovací sadě, získejte nejprve objekt nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Pak zobrazte pravidla příchozího překladu adres pomocí rutiny [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Následující příklad výstupu ukazuje název instance, veřejnou IP adresu nástroje pro vyrovnávání zatížení a číslo portu, na které pravidla překladu adres směrují provoz:

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

Název (*Name*) pravidla odpovídá názvu instance virtuálního počítače uvedenému ve výstupu předchozího příkazu [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Pokud se například chcete připojit k instanci virtuálního počítače *0*, použijte *myScaleSet3389.0* a připojte se k portu *50001*. Pokud se chcete připojit k instanci virtuálního počítače *1*, použijte hodnotu z *myScaleSet3389.1* a připojte se k portu *50002*. Pokud chcete využít vzdálenou komunikaci PowerShellu, připojte se k odpovídajícímu pravidlu instance virtuálního počítače pro port *TCP* *5985*.

Zobrazte veřejnou IP adresu nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Příklad výstupu:

```powershell
IpAddress
---------
52.168.121.216
```

Vytvořte vzdálené připojení k první instanci virtuálního počítače. Zadejte vlastní veřejnou IP adresu a číslo portu požadované instance virtuálního počítače uvedené ve výstupech předchozích příkazů. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření škálovací sady (v ukázkových příkazech je to ve výchozím nastavení *azureuser* a *P@ssw0rd!*). Pokud používáte Azure Cloud Shell, proveďte tento krok z příkazového řádku místního PowerShellu nebo klienta Vzdálené plochy. Následující příklad se připojí k instanci virtuálního počítače *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Po přihlášení k instanci virtuálního počítače můžete podle potřeby provést ruční změny konfigurace. Prozatím vzdálené připojení ukončete.


## <a name="understand-vm-instance-images"></a>Vysvětlení imagí instancí virtuálních počítačů
Při definování konfigurace škálovací sady pomocí rutiny [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) v předchozím kroku jste použili image Windows Serveru 2016 Datacenter. Azure Marketplace obsahuje celou řadu imagí, které je možné využít k vytváření instancí virtuálních počítačů. Pokud chcete zobrazit seznam dostupných vydavatelů, použijte příkaz [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher).

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Pokud chcete zobrazit seznam imagí daného vydavatele, použijte příkaz [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). Seznam imagí je také možné filtrovat podle parametrů `-PublisherName` nebo `–Offer`. V následujícím příkladu se v seznamu vyfiltrují všechny image s názvem vydavatele *MicrosoftWindowsServer*, jejichž nabídka je *WindowsServer*:

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

Následující příklad výstupu ukazuje všechny dostupné image Windows Serveru:

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Při vytváření škálovací sady na začátku kurzu jste pro instance virtuálních počítačů zadali výchozí image virtuálního počítače *Windows Server 2016 DataCenter*. Na základě výstupu rutiny [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) můžete zadat jinou image virtuálního počítače. Následující příklad vytvoří škálovací sadu s použitím parametru `-ImageName`, který určí image virtuálního počítače *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*. Vzhledem k tomu, že vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut, následující škálovací sadu nasazovat nemusíte:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicy "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```


## <a name="understand-vm-instance-sizes"></a>Vysvětlení velikostí instancí virtuálních počítačů
Velikost instance virtuálního počítače neboli *skladová položka* určuje množství výpočetních prostředků, jako jsou procesor, grafický procesor a paměť, které jsou pro instanci virtuálního počítače k dispozici. Velikost instancí virtuálních počítačů ve škálovací sadě je třeba správně určit podle očekávané pracovní zátěže.

### <a name="vm-instance-sizes"></a>Velikosti instancí virtuálních počítačů
V následující tabulce jsou běžné velikosti virtuálních počítačů rozdělené podle způsobů použití.

| Typ                     | Běžné velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Obecné účely](../virtual-machines/windows/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Vyvážený poměr procesorů k paměti. Ideální pro vývoj nebo testování a pro malé až střední řešení aplikací a dat.  |
| [Optimalizované z hlediska výpočetních služeb](../virtual-machines/windows/sizes-compute.md)   | Fs, F             | Vysoký poměr procesorů k paměti. Vhodné pro aplikace se středním provozem, síťová zařízení a dávkové procesy.        |
| [Optimalizované z hlediska paměti](../virtual-machines/windows/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Vysoký poměr paměti k jádrům. Velmi vhodné pro relační databáze, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované z hlediska úložiště](../virtual-machines/windows/sizes-storage.md)      | Ls                | Vysoká propustnost disku a V/V. Ideální pro databáze NoSQL, SQL a velké objemy dat.                                                         |
| [GPU](../virtual-machines/windows/sizes-gpu.md)          | NV, NC            | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa.       |
| [Vysoký výkon](../virtual-machines/windows/sizes-hpc.md) | H, A8-11          | Naše procesorově nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Vyhledání dostupných velikostí instancí virtuálních počítačů
Pokud chcete zobrazit seznam velikostí instancí virtuálních počítačů dostupných v konkrétní oblasti, použijte příkaz [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). 

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

Výstup se podobá následujícímu zhuštěnému příkladu, který ukazuje prostředky přiřazené k jednotlivým velikostem virtuálních počítačů:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Při vytváření škálovací sady na začátku kurzu jste pro instance virtuálních počítačů zadali výchozí skladovou položku virtuálního počítače *Standard_DS1_v2*. Na základě výstupu rutiny [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) můžete zadat jinou velikost instancí virtuálních počítačů. Následující příklad vytvoří škálovací sadu s použitím parametru `-VmSize`, který určí velikost instancí virtuálních počítačů *Standard_F1*. Vzhledem k tomu, že vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut, následující škálovací sadu nasazovat nemusíte:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicy "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sady
Při vytváření škálovací sady jste vyžádali dvě instance virtuálních počítačů. Pokud chcete zvýšit nebo snížit počet instancí virtuálních počítačů ve škálovací sadě, můžete ručně změnit kapacitu. Škálovací sada vytvoří nebo odebere požadovaný počet instancí virtuálních počítačů a pak nakonfiguruje nástroj pro vyrovnávání zatížení pro distribuci provozu.

Nejprve vytvořte objekt škálovací sady pomocí rutiny [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) a pak zadejte novou hodnotu `sku.capacity`. Změnu kapacity použijete pomocí rutiny [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Následující příklad nastaví počet instancí virtuálních počítačů ve vaší škálovací sadě na *3*:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Aktualizace kapacity škálovací sady trvá několik minut. Pokud chcete zobrazit počet instancí, které teď máte ve škálovací sadě, použijte rutinu [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Následující příklad výstupu ukazuje, že kapacita škálovací sady je teď *3*:

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Běžné úlohy správy
Teď můžete vytvořit škálovací sadu, vypsat informace o připojení a připojit se k instancím virtuálních počítačů. Zjistili jste, jak pro instance virtuálních počítačů použít jinou image operačního systému, vybrat jinou velikost virtuálních počítačů nebo ručně škálovat počet instancí. V rámci každodenní správy můžete potřebovat zastavit, spustit nebo restartovat instance virtuálních počítačů ve své škálovací sadě.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Zastavení a uvolnění instancí virtuálních počítačů ve škálovací sadě
Pokud chcete zastavit jeden nebo několik virtuálních počítačů ve škálovací sadě, použijte rutinu [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají zastavit. Pokud nezadáte ID instance, zastaví se všechny virtuální počítače ve škálovací sadě. Následující příklad zastaví instanci *1*:

```azurepowershell-interactive
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

Ve výchozím nastavení se zastavené virtuální počítače uvolní a neúčtují se u nich poplatky za výpočty. Pokud chcete, aby virtuální počítače po zastavení zůstaly ve zřízeném stavu, přidejte k předchozímu příkazu parametr `-StayProvisioned`. U zastavených virtuálních počítačů, které zůstanou zřízené, se účtují obvyklé poplatky za výpočty.

### <a name="start-vm-instances-in-a-scale-set"></a>Spuštění instancí virtuálních počítačů ve škálovací sadě
Pokud chcete spustit jeden nebo několik virtuálních počítačů ve škálovací sadě, použijte rutinu [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají spustit. Pokud nezadáte ID instance, spustí se všechny virtuální počítače ve škálovací sadě. Následující příklad spustí instanci *1*:

```azurepowershell-interactive
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Restartování instancí virtuálních počítačů ve škálovací sadě
Pokud chcete restartovat jeden nebo několik virtuálních počítačů ve škálovací sadě, použijte rutinu [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají restartovat. Pokud nezadáte ID instance, restartují se všechny virtuální počítače ve škálovací sadě. Následující příklad restartuje instanci *1*:

```azurepowershell-interactive
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Vyčištění prostředků
Když odstraníte skupinu prostředků, odstraní se také všechny prostředky v ní obsažené, například instance virtuálních počítačů, virtuální síť a disky. Parametr `-Force` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz. Parametr `-AsJob` vrátí řízení na příkazový řádek bez čekání na dokončení operace.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak provádět několik běžných úloh vytvoření a správy škálovací sady pomocí Azure PowerShellu:

> [!div class="checklist"]
> * Vytvoření škálovací sady virtuálních počítačů a připojení k ní
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí virtuálních počítačů
> * Ruční škálování škálovací sady
> * Provádění běžných úloh správy škálovací sady

V dalším kurzu se dozvíte něco o discích škálovacích sad.

> [!div class="nextstepaction"]
> [Použití datových disků se škálovacími sadami](tutorial-use-disks-powershell.md)
