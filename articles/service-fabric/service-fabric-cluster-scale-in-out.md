---
title: Horizontální navýšení nebo navýšení Service Fabric clusteru
description: Škálujte Service Fabric clusteru v nebo v souladu s požadavky nastavením pravidel automatického škálování pro každý typ uzlu nebo sadu škálování virtuálního počítače. Přidání uzlů do clusteru Service Fabric nebo jejich odebrání
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94409956"
---
# <a name="scale-a-cluster-in-or-out"></a>Horizontální snížení nebo navýšení kapacity clusteru

> [!WARNING]
> Přečtěte si tento oddíl před škálováním.

Škálování výpočetních prostředků ke zdroji pracovního zatížení vaší aplikace vyžaduje úmyslné plánování, téměř vždy trvá déle než hodinu, než se dokončí pro produkční prostředí, a vyžaduje, abyste porozuměli vašim úlohám a obchodním kontextům. Pokud jste tuto aktivitu ještě nikdy neudělali, doporučujeme začít čtením a porozuměním [Service Fabric posouzení kapacity clusteru](service-fabric-cluster-capacity.md), než budete pokračovat ve zbývající části tohoto dokumentu. Toto doporučení se vyhnete nezamýšleným problémům s LiveSite a také doporučujeme úspěšně otestovat operace, které se rozhodnete provést v neprodukčním prostředí. Kdykoli můžete [nahlásit výrobní problémy nebo požádat o vyplacenou podporu pro Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Pro inženýry přidělené k provádění těchto operací, které mají odpovídající kontext, Tento článek popisuje operace škálování, ale musíte se rozhodnout a pochopit, které operace jsou pro váš případ použití vhodné. například jaké prostředky se mají škálovat (procesor, úložiště, paměť), jaký směr se má škálovat (vertikálně nebo horizontálně) a jaké operace se mají provádět (Template deployment prostředků, portál, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Škálování Service Fabric clusteru pomocí pravidel automatického škálování nebo ručně
Služby Virtual Machine Scale Sets jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definovaný v clusteru Service Fabric, se nastaví jako samostatná sada škálování virtuálního počítače. Jednotlivé typy uzlů se pak dají škálovat nezávisle, mají otevřené různé sady portů a můžou mít různé metriky kapacity. Přečtěte si další informace o tom v dokumentu [typy Service Fabricch uzlů](service-fabric-cluster-nodetypes.md) . Vzhledem k tomu, že typy uzlů Service Fabric v clusteru jsou v rámci back-endu sady virtuálních počítačů, je třeba nastavit pravidla automatického škálování pro každý typ uzlu nebo sadu škálování virtuálního počítače.

> [!NOTE]
> Aby bylo možné přidat nové virtuální počítače, které tvoří tento cluster, musí mít vaše předplatné dostatek jader. V současné době se neprovádí ověřování modelu, takže pokud dojde k dosažení limitu kvóty, zobrazí se chyba v čase nasazení. Také jeden typ uzlu nemůže překročit 100 uzlů na VMSS. Možná budete muset přidat VMSS, abyste dosáhli cíleného škálování, a automatické škálování nemůže automaticky připínat do VMSS. Přidávání VMSS do živého clusteru je náročné, a to obvykle vede k tomu, že uživatelé zřídí nové clustery s příslušnými typy uzlů zřízenými během vytváření. podle potřeby [Naplánujte kapacitu clusteru](./service-fabric-cluster-capacity.md) . 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Vyberte typ uzlu/sadu škálování virtuálního počítače pro škálování
V současné době nemůžete zadat pravidla automatického škálování pro sady škálování virtuálních počítačů pomocí portálu, aby se vytvořil Cluster Service Fabric, takže nás používá Azure PowerShell (1.0 +) k vypsání typů uzlů a přidání pravidel automatického škálování do nich.

Pokud chcete získat seznam sady škálování virtuálních počítačů, které tvoří cluster, spusťte následující rutiny:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Nastavení pravidel automatického škálování pro typ uzlu/sadu škálování virtuálního počítače
Pokud má cluster více typů uzlů, opakujte tento postup pro všechny typy uzlů/sady škálování virtuálních počítačů, které chcete škálovat (nebo z nich). Před nastavením automatického škálování vezměte v úvahu potřebný počet uzlů. Minimální počet uzlů, který je potřeba pro primární typ uzlu, vychází ze zvolené úrovně spolehlivosti. Přečtěte si další informace o [úrovních spolehlivosti](service-fabric-cluster-capacity.md).

> [!NOTE]
> Škálování v primárním uzlu na menší než minimální číslo způsobí, že cluster nebude stabilní, nebo ho bude možné využít i nadále. To může mít za následek ztrátu dat pro vaše aplikace a systémové služby.
> 
> 

Funkce automatického škálování se v současné době neřídí načtenými možnostmi, které vaše aplikace mohou ohlásit Service Fabric. Takže v tomto okamžiku je automatické škálování čistě založené na čítačích výkonu, které vycházejí z instancí sady škálování virtuálních počítačů.  

Podle těchto pokynů [nastavte automatické škálování pro jednotlivé sady škálování virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> V měřítku ve scénáři, pokud váš typ uzlu nemá [úroveň odolnosti][durability] Gold nebo stříbrné, je nutné volat [rutinu Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) s odpovídajícím názvem uzlu. Pro bronzovou odolnost se nedoporučuje škálovat ve více než jednom uzlu současně.
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ruční přidání virtuálních počítačů do typu uzlu/sady škálování virtuálních počítačů

Při horizontálním navyšování kapacity přidáte do škálovací sady více instancí virtuálních počítačů. Tyto instance se stanou uzly, které bude Service Fabric používat. Service Fabric to pozná, když se do škálovací sady přidají další instance (díky horizontálnímu navýšení kapacity), a automaticky zareaguje. 

> [!NOTE]
> Přidání virtuálních počítačů trvá určitou dobu, takže neočekáváme, že se doplňují. Naplánujte, aby se předem přidala kapacita, a to po dobu více než 10 minut, než bude kapacita virtuálního počítače k dispozici pro repliky/instance služby, které se mají umístit.
> 

### <a name="add-vms-using-a-template"></a>Přidání virtuálních počítačů pomocí šablony
Podle ukázky nebo pokynů v [galerii šablon pro rychlé](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) zprovoznění změňte počet virtuálních počítačů v jednotlivých typech uzlů. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Přidání virtuálních počítačů pomocí příkazů PowerShellu nebo rozhraní příkazového řádku
Následující kód načte škálovací sadu podle názvu a zvýší její **kapacitu** o 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Tento kód nastaví kapacitu na 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ruční odebrání virtuálních počítačů z typu uzlu nebo sady škálování virtuálních počítačů
Při horizontálním navýšení kapacity v typu uzlu odeberete instance virtuálních počítačů ze sady škálování. Je-li typ uzlu Bronzová úroveň trvanlivosti, Service Fabric nevíte, co se stalo, a oznamuje, že chybí uzel. Service Fabric pak hlásí, že stav clusteru není v pořádku. Chcete-li zabránit tomuto špatnému stavu, je nutné explicitně odebrat uzel z clusteru a odebrat stav uzlu.

Systémové služby Service Fabric běží ve vašem clusteru v typu primární uzel. Při horizontálním navýšení kapacity typu primárního uzlu nikdy nepoužívejte škálování v počtu instancí na míň, než jakou zaručuje [úroveň spolehlivosti](service-fabric-cluster-capacity.md) . 
 
Pro stavovou službu potřebujete určit určitý počet uzlů, aby bylo možné trvale udržovat dostupnost a zachovat stav služby. Minimálně potřebujete, aby se počet uzlů rovnal počtu cílových sad replik oddílu/služby.

### <a name="remove-the-service-fabric-node"></a>Odebrání uzlu Service Fabric

Postup ručního odebrání stavu uzlu se vztahuje pouze na typy uzlů s *bronzovou* úrovní odolnosti.  Pro *stříbro* a *zlatou* úroveň odolnosti se tyto kroky provádí automaticky na platformě. Další informace o odolnosti najdete v článku [Plánování kapacity clusteru Service Fabric][durability].

>[!NOTE]
> Udržujte minimální počet pěti uzlů pro všechny sady škálování virtuálních počítačů, které mají povolenou úroveň odolnosti Gold nebo stříbrné. Cluster zadáte chybový stav, pokud budete škálovat pod tuto prahovou hodnotu a budete muset ručně vyčistit odebrané uzly.

V zájmu toho, aby byly uzly clusteru rovnoměrně rozdělené v doménách upgradu a doménách selhání a aby se tedy i rovnoměrně využívaly, je potřeba jako první odebrat naposledy vytvořený uzel. Jinak řečeno, uzly by se měly odebírat v opačném pořadí, než v jakém došlo k jejich vytvoření. Naposledy vytvořený uzel je uzel s nejvyšší hodnotou vlastnosti `virtual machine scale set InstanceId`. Následující příklady kódu vracejí naposledy vytvořený uzel.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Cluster Service Fabric potřebuje vědět, že se tento uzel odebere. Musíte provést tři kroky:

1. Zakázat uzel, aby už nefungoval jako replika pro data.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Zastavit uzel tak, aby se řádně ukončil provoz Service Fabricu a vaše aplikace obdržela žádost o ukončení.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Odebrat uzel z clusteru.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Jakmile na uzel aplikujete tyto tři kroky, může se odebrat ze škálovací sady. Pokud používáte nějakou jinou úroveň odolnosti než [bronzovou][durability], tyto kroky se provedou za vás při odebrání instance škálovací sady.

Následující blok kódu získá poslední vytvořený uzel a zakáže ho, zastaví a odebere z clusteru.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

V kódu **sfctl** níže slouží následující příkaz k získání hodnoty **node-name** posledního vytvořeného uzlu: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Pomocí následujících dotazů **sfctl** můžete zkontrolovat stav každého kroku.
>
> **Kontrolovat stav deaktivace**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Zaškrtnout stav zastavení**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Horizontální snížení kapacity škálovací sady

Teď, když je uzel Service Fabric odebraný z clusteru, je možné horizontálně snížit kapacitu škálovací sady virtuálních počítačů. V následujícím příkladu je kapacita škálovací sady snížená o 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Tento kód nastaví kapacitu na 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Chování, která můžete sledovat v Service Fabric Explorer
Při horizontálním navýšení kapacity clusteru bude Service Fabric Explorer odpovídat počtu uzlů (instance sady škálování virtuálních počítačů), které jsou součástí clusteru.  Při škálování clusteru v ale uvidíte, že odebraný uzel/instance virtuálního počítače se zobrazí ve stavu není v pořádku, pokud nebudete volat příkaz [Remove-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) s příslušným názvem uzlu.   

Zde je vysvětlení tohoto chování.

Uzly uvedené v Service Fabric Explorer jsou odrazem toho, co Service Fabric systémové služby (FM konkrétně) ví o počtu uzlů, které cluster má. Při škálování sady virtuálních počítačů v nástroji se virtuální počítač odstranil, ale systémová služba FM stále popřemýšleje o tom, že uzel (namapovaný na virtuální počítač se odstranil) se vrátí zpátky. Takže Service Fabric Explorer nadále tento uzel zobrazovat (přestože stav může být chyba nebo neznámá).

Abyste se ujistili, že je uzel odebraný při odebrání virtuálního počítače, máte dvě možnosti:

1. Vyberte úroveň životnosti Gold nebo stříbrného pro typy uzlů v clusteru, což vám umožní integraci infrastruktury. Tím se pak po horizontálním navýšení kapacity budou automaticky odebírat uzly ze stavu FM (System Services).
Tady najdete [Podrobnosti o úrovních trvanlivosti](service-fabric-cluster-capacity.md) .

> [!NOTE]
> Udržujte minimální počet pěti uzlů pro všechny sady škálování virtuálních počítačů, které mají povolenou úroveň odolnosti Gold nebo stříbrné. Cluster zadáte chybový stav, pokud budete škálovat pod tuto prahovou hodnotu a budete muset ručně vyčistit odebrané uzly.

2. Po škálování instance virtuálního počítače v nástroji je potřeba zavolat [rutinu Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Clustery Service Fabric vyžadují, aby určitý počet uzlů byl neustále v čase, aby bylo možné zachovat dostupnost a zachovat stav – označuje se jako Údržba kvora. Proto je obvykle bezpečné vypnout všechny počítače v clusteru, pokud jste nejprve neprováděli [úplné zálohování vašeho stavu](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Další kroky
Přečtěte si následující informace, abyste se seznámili s plánováním kapacity clusteru, upgradem clusteru a vytvářením oddílů služby:

* [Plánování kapacity clusteru](service-fabric-cluster-capacity.md)
* [Upgrady clusteru](service-fabric-cluster-upgrade.md)
* [Stavové služby pro oddíly pro maximální škálování](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
