---
title: 'Kurz: vytvoření & Správa škálování virtuálního počítače Azure – Azure PowerShell'
description: Zjistěte, jak pomocí Azure PowerShellu vytvořit škálovací sadu virtuálních počítačů a provádět několik běžných úloh správy, jako je spuštění a zastavení instance nebo změna kapacity škálovací sady.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/18/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 102ab619a3f341658383dc4997f9fbfc2cd12a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519176"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Kurz: Vytvoření a správa škálovací sady virtuálních počítačů pomocí Azure PowerShellu

Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. V průběhu životního cyklu škálovací sady virtuálních počítačů možná budete potřebovat spustit jednu nebo více úloh správy. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření škálovací sady virtuálních počítačů a připojení k ní
> * Výběr a použití imagí virtuálních počítačů
> * Zobrazení a použití specifických velikostí instancí virtuálních počítačů
> * Ruční škálování škálovací sady
> * Provádění běžných úloh správy škálovací sady

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]



## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením škálovací sady virtuálních počítačů. Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . V tomto příkladu se vytvoří skupina prostředků *myResourceGroup* v oblasti *EastUS*. 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
Název skupiny prostředků zadáte při vytváření nebo úpravě škálovací sady v rámci tohoto kurzu.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Nejprve pomocí rutiny [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) nastavte uživatelské jméno a heslo správce instancí virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Teď vytvořte sadu škálování virtuálního počítače pomocí [New-AzVmss](/powershell/module/az.compute/new-azvmss). Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985:

```azurepowershell-interactive
New-AzVmss `
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

> [!IMPORTANT]
> Pokud se nemůžete připojit k sadě škálování, možná budete muset vytvořit skupinu zabezpečení sítě přidáním parametru *[-SecurityGroupName "mySecurityGroup"](/powershell/module/az.compute/new-azvmss)* .


## <a name="view-the-vm-instances-in-a-scale-set"></a>Zobrazení instancí virtuálních počítačů ve škálovací sadě
Pokud chcete zobrazit seznam instancí virtuálních počítačů v sadě škálování, použijte [příkaz Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) následujícím způsobem:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Následující příklad výstupu ukazuje dvě instance virtuálních počítačů ve škálovací sadě:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Pokud chcete zobrazit další informace o konkrétní instanci virtuálního počítače, přidejte `-InstanceId` parametr do [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Následující příklad zobrazí informace o instanci virtuálního počítače *1*:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Výpis informací o připojení
K nástroji pro vyrovnávání zatížení, který směruje provoz do jednotlivých instancí virtuálních počítačů, se přiřadí veřejná IP adresa. Ve výchozím nastavení se k nástroji pro vyrovnávání zatížení Azure, který směruje provoz vzdáleného připojení do jednotlivých virtuálních počítačů na daném portu, přidají pravidla překladu adres (NAT). Pokud se chcete připojit k instancím virtuálních počítačů ve škálovací sadě, vytvořte vzdálené připojení k přiřazené veřejné IP adrese a číslu portu.

Pokud chcete zobrazit seznam portů NAT pro připojení k instancím virtuálních počítačů v sadě škálování, nejdřív získejte objekt nástroje pro vyrovnávání zatížení pomocí [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Pak zobrazte pravidla příchozího překladu adres (NAT) pomocí [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
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

*Název* pravidla se zarovnává s názvem instance virtuálního počítače, jak je znázorněno v předchozím příkazu [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) . Pokud se například chcete připojit k instanci virtuálního počítače *0*, použijte *myScaleSet3389.0* a připojte se k portu *50001*. Pokud se chcete připojit k instanci virtuálního počítače *1*, použijte hodnotu z *myScaleSet3389.1* a připojte se k portu *50002*. Pokud chcete využít vzdálenou komunikaci PowerShellu, připojte se k odpovídajícímu pravidlu instance virtuálního počítače pro port *TCP**5985*.

Zobrazení veřejné IP adresy nástroje pro vyrovnávání zatížení pomocí [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Příklad výstupu:

```powershell
IpAddress
---------
52.168.121.216
```

Vytvořte vzdálené připojení k první instanci virtuálního počítače. Zadejte vlastní veřejnou IP adresu a číslo portu požadované instance virtuálního počítače uvedené ve výstupech předchozích příkazů. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření sady škálování (ve výchozím nastavení ve vzorových příkazech, *azureuser* a *P \@ ssw0rd!*). Pokud používáte Azure Cloud Shell, proveďte tento krok z příkazového řádku místního PowerShellu nebo klienta Vzdálené plochy. Následující příklad se připojí k instanci virtuálního počítače *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Po přihlášení k instanci virtuálního počítače můžete podle potřeby provést ruční změny konfigurace. Prozatím vzdálené připojení ukončete.


## <a name="understand-vm-instance-images"></a>Vysvětlení imagí instancí virtuálních počítačů
Azure Marketplace obsahuje celou řadu imagí, které je možné využít k vytváření instancí virtuálních počítačů. Pokud chcete zobrazit seznam dostupných vydavatelů, použijte příkaz [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) .

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Chcete-li zobrazit seznam obrázků pro daného vydavatele, použijte [příkaz Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku). Seznam imagí je také možné filtrovat podle parametrů `-PublisherName` nebo `-Offer`. V následujícím příkladu se v seznamu vyfiltrují všechny image s názvem vydavatele *MicrosoftWindowsServer*, jejichž nabídka je *WindowsServer*:

```azurepowershell-interactive
Get-AzVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
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

Při vytváření škálovací sady na začátku kurzu jste pro instance virtuálních počítačů zadali výchozí image virtuálního počítače *Windows Server 2016 DataCenter*. Na základě výstupu z [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku)můžete zadat jinou bitovou kopii virtuálního počítače. Následující příklad vytvoří škálovací sadu s použitím parametru `-ImageName`, který určí image virtuálního počítače *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*. Vzhledem k tomu, že vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut, následující škálovací sadu nasazovat nemusíte:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```

> [!IMPORTANT]
> Doporučujeme použít *nejnovější* verzi image. Pokud chcete použít nejnovější verzi image dostupnou v době nasazení, zadejte ' nejnovější '. Poznámka: i když použijete možnost nejnovější, image virtuálního počítače se po nasazení automaticky neaktualizuje, i když bude k dispozici nová verze.

## <a name="understand-vm-instance-sizes"></a>Vysvětlení velikostí instancí virtuálních počítačů
Velikost instance virtuálního počítače neboli *skladová položka* určuje množství výpočetních prostředků, jako jsou procesor, grafický procesor a paměť, které jsou pro instanci virtuálního počítače k dispozici. Velikost instancí virtuálních počítačů ve škálovací sadě je třeba správně určit podle očekávané pracovní zátěže.

### <a name="vm-instance-sizes"></a>Velikosti instancí virtuálních počítačů
V následující tabulce jsou běžné velikosti virtuálních počítačů rozdělené podle způsobů použití.

| Typ                     | Běžné velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Obecné účely](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Vyvážený poměr procesorů k paměti. Ideální pro vývoj nebo testování a pro malé až střední řešení aplikací a dat.  |
| [Optimalizované pro výpočty](../virtual-machines/sizes-compute.md)   | Fs, F             | Vysoký poměr procesorů k paměti. Vhodné pro aplikace se středním provozem, síťová zařízení a dávkové procesy.        |
| [Optimalizované pro paměť](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Vysoký poměr paměti k jádrům. Velmi vhodné pro relační databáze, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované pro úložiště](../virtual-machines/sizes-storage.md)      | Ls                | Vysoká propustnost disku a V/V. Ideální pro databáze NoSQL, SQL a velké objemy dat.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa.       |
| [Vysoký výkon](../virtual-machines/sizes-hpc.md) | H, A8-11          | Naše procesorově nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Vyhledání dostupných velikostí instancí virtuálních počítačů
Pokud chcete zobrazit seznam velikostí instancí virtuálních počítačů, které jsou k dispozici v konkrétní oblasti, použijte příkaz [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) . 

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
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

Při vytváření škálovací sady na začátku kurzu jste pro instance virtuálních počítačů zadali výchozí skladovou položku virtuálního počítače *Standard_DS1_v2*. Na základě výstupu z [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize)můžete zadat jinou velikost instance virtuálního počítače. Následující příklad vytvoří škálovací sadu s použitím parametru `-VmSize`, který určí velikost instancí virtuálních počítačů *Standard_F1*. Vzhledem k tomu, že vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut, následující škálovací sadu nasazovat nemusíte:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Změna kapacity škálovací sady
Při vytváření škálovací sady jste vyžádali dvě instance virtuálních počítačů. Pokud chcete zvýšit nebo snížit počet instancí virtuálních počítačů ve škálovací sadě, můžete ručně změnit kapacitu. Škálovací sada vytvoří nebo odebere požadovaný počet instancí virtuálních počítačů a pak nakonfiguruje nástroj pro vyrovnávání zatížení pro distribuci provozu.

Nejprve vytvořte objekt sady škálování pomocí [Get-AzVmss](/powershell/module/az.compute/get-azvmss)a pak zadejte novou hodnotu pro `sku.capacity` . Pokud chcete použít změnu kapacity, použijte [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Následující příklad nastaví počet instancí virtuálních počítačů ve vaší škálovací sadě na *3*:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Aktualizace kapacity škálovací sady trvá několik minut. Pokud chcete zobrazit počet instancí, které teď máte v sadě škálování, použijte [příkaz Get-AzVmss](/powershell/module/az.compute/get-azvmss):

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
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
Pokud chcete zastavit jeden nebo více virtuálních počítačů v sadě škálování, použijte [stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají zastavit. Pokud nezadáte ID instance, zastaví se všechny virtuální počítače ve škálovací sadě. Následující příklad zastaví instanci *1*:

```azurepowershell-interactive
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

Ve výchozím nastavení se zastavené virtuální počítače uvolní a neúčtují se u nich poplatky za výpočty. Pokud chcete, aby virtuální počítače po zastavení zůstaly ve zřízeném stavu, přidejte k předchozímu příkazu parametr `-StayProvisioned`. U zastavených virtuálních počítačů, které zůstanou zřízené, se účtují obvyklé poplatky za výpočty.

### <a name="start-vm-instances-in-a-scale-set"></a>Spuštění instancí virtuálních počítačů ve škálovací sadě
Pokud chcete spustit jeden nebo více virtuálních počítačů v sadě škálování, použijte [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají spustit. Pokud nezadáte ID instance, spustí se všechny virtuální počítače ve škálovací sadě. Následující příklad spustí instanci *1*:

```azurepowershell-interactive
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Restartování instancí virtuálních počítačů ve škálovací sadě
Pokud chcete restartovat jeden nebo víc virtuálních počítačů v sadě škálování, použijte [restart-AzVmss](/powershell/module/az.compute/restart-azvmss). Pomocí parametru `-InstanceId` můžete zadat jeden nebo několik virtuálních počítačů, které se mají restartovat. Pokud nezadáte ID instance, restartují se všechny virtuální počítače ve škálovací sadě. Následující příklad restartuje instanci *1*:

```azurepowershell-interactive
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Vyčištění prostředků
Když odstraníte skupinu prostředků, odstraní se také všechny prostředky v ní obsažené, například instance virtuálních počítačů, virtuální síť a disky. Parametr `-Force` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz. Parametr `-AsJob` vrátí řízení na příkazový řádek bez čekání na dokončení operace.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
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
