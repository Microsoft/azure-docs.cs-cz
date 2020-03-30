---
title: Škálování clusteru Service Fabric dovnitř nebo ven
description: Škálování clusteru Service Fabric v nebo mimo tak, aby odpovídaly poptávce nastavením pravidla automatického škálování pro každý typ uzlu nebo virtuální stroj škálovací sady. Přidání nebo odebrání uzlů do clusteru Service Fabric
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 26ef13f38d525e4e493ad933bfb906dd36ed0070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258730"
---
# <a name="scale-a-cluster-in-or-out"></a>Horizontální snížení nebo navýšení kapacity clusteru

> [!WARNING]
> Přečtěte si tuto část před použitím měřítka

Škálování výpočetních prostředků pro zdroj pracovní zátěže aplikace vyžaduje záměrné plánování, bude téměř vždy trvat déle než hodinu pro produkční prostředí a vyžaduje, abyste pochopili vaše pracovní vytížení a obchodní kontext; Ve skutečnosti, pokud jste nikdy neprovedli tuto činnost dříve, je doporučeno začít čtením a pochopení [service fabric aspekty plánování clusteru](service-fabric-cluster-capacity.md), před pokračováním zbývající části tohoto dokumentu. Toto doporučení je, aby se zabránilo nezamýšleným problémům LiveSite a je také doporučeno úspěšně otestovat operace, které se rozhodnete provést proti neprodukčním prostředí. Kdykoli můžete [nahlásit problémy s výrobou nebo požádat o placenou podporu pro Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Pro inženýry přidělené k provádění těchto operací, které mají odpovídající kontext, tento článek bude popisovat operace škálování, ale musíte rozhodnout a pochopit, které operace jsou vhodné pro případ použití; například jaké prostředky škálovat (procesor, úložiště, paměť), jaký směr škálování (svisle nebo vodorovně) a jaké operace se mají provádět (nasazení šablony prostředků, portál, powershell/cli).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Škálování clusteru Service Fabric při použití nebo získaní pomocí pravidel automatického škálování nebo ručně
Škálovací sady virtuálních počítačů jsou výpočetní prostředek Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Service Fabric, je nastaven jako samostatná škálovací sada virtuálních strojů. Každý typ uzlu pak lze škálovat nebo zmenšovat nezávisle, mají různé sady portů otevřené a může mít různé metriky kapacity. Další informace o tom načtete v dokumentu [typů uzlů Service Fabric.](service-fabric-cluster-nodetypes.md) Vzhledem k tomu, že typy uzlů Service Fabric v clusteru jsou tvořeny škálovacími sadami virtuálních počítačů v back-endu, je třeba nastavit pravidla automatického škálování pro každý typ uzlu/sadu škálovacísady virtuálních počítačů.

> [!NOTE]
> Vaše předplatné musí mít dostatek jader pro přidání nových virtuálních počítačů, které tvoří tento cluster. V současné době neexistuje žádné ověření modelu, takže získáte selhání času nasazení, pokud jsou přístupná některá z omezení kvóty. Také jeden typ uzlu nemůže jednoduše překročit 100 uzlů na VMSS. Možná budete muset přidat VMSS k dosažení cílové škálování a automatické škálování nelze automaticky přidat VMSS. Přidání vmss je na místě do živého clusteru je náročná úloha a obvykle to má za následek uživatelům zřizování nových clusterů s příslušnými typy uzlů zřízených v době vytvoření; [odpovídajícím](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) způsobem plánovat kapacitu clusteru. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Volba škálovací sady typu uzlu/virtuálního počítače pro škálování
V současné době nejste schopni zadat pravidla automatického škálování pro škálovací sady virtuálních počítačů pomocí portálu k vytvoření clusteru Service Fabric, takže použijte Azure PowerShell (1.0+) k vypsat typy uzlů a pak k nim přidat pravidla automatického škálování.

Chcete-li získat seznam škálovací sady virtuálních strojů, které tvoří váš cluster, spusťte následující rutiny:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Nastavení pravidel automatického škálování pro škálovací sadu typu uzlu nebo virtuálního počítače
Pokud má váš cluster více typů uzlů, opakujte to pro každý typ uzlů nebo škálovací sady virtuálních počítačů, které chcete škálovat (dovnitř nebo ven). Před nastavením automatického škálování vezměte v úvahu potřebný počet uzlů. Minimální počet uzlů, který je potřeba pro primární typ uzlu, vychází ze zvolené úrovně spolehlivosti. Přečtěte si další informace o [úrovních spolehlivosti](service-fabric-cluster-capacity.md).

> [!NOTE]
> Škálování typu primárního uzlu na menší než minimální počet způsobit, že cluster nestabilní nebo přenést dolů. To může mít za následek ztrátu dat pro vaše aplikace a pro systémové služby.
> 
> 

V současné době funkce automatického škálování není řízena zatížení, které vaše aplikace může být hlášení Service Fabric. Takže v tomto okamžiku automatické škálování získáte je čistě řízenčí výkon čítače, které jsou vydávány každý z instancí škálovací sady virtuálních počítačů.  

Podle těchto pokynů [nastavte automatické škálování pro každou škálovací sadu virtuálních strojů](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Ve scénáři škálování dolů, pokud typ uzlu má [úroveň odolnosti][durability] zlaťáky nebo stříbra, musíte volat [rutinu Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) s příslušným názvem uzlu. U bronzové odolnosti se nedoporučuje zmenšovat více uzlů najednou.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ruční přidání virtuálních počítačů do škálovací sady typu uzlu nebo virtuálního počítače

Při horizontálním navyšování kapacity přidáte do škálovací sady více instancí virtuálních počítačů. Tyto instance se stanou uzly, které bude Service Fabric používat. Service Fabric to pozná, když se do škálovací sady přidají další instance (díky horizontálnímu navýšení kapacity), a automaticky zareaguje. 

> [!NOTE]
> Přidání virtuálních operátorů nějakou dobu trvá, takže neočekávejte, že dodatky budou okamžité. Takže plán přidat kapacitu v dostatečném předstihu, aby více než 10 minut před kapacitou virtuálního počítače je k dispozici pro repliky nebo služby instance získat umístěny.
> 

### <a name="add-vms-using-a-template"></a>Přidání virtuálních počítače pomocí šablony
Podle ukázky/pokynů v [galerii šablon rychlého startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) změňte počet virtuálních počítačů v jednotlivých typech uzlů. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Přidání virtuálních proudů pomocí příkazů PowerShellu nebo CLI
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

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ruční odebrání virtuálních počítačů z škálovací sady typu uzlu nebo virtuálního počítače
Při škálování v typu uzlu odeberete instance virtuálních virtuálních můek z škálovací sady. Pokud typ uzlu je bronzová úroveň trvanlivosti, Service Fabric neví, co se stalo a hlásí, že uzel zmizel. Service Fabric pak hlásí, že stav clusteru není v pořádku. Chcete-li tomuto chybnému stavu zabránit, je nutné explicitně odebrat uzel z clusteru a odebrat stav uzlu.

Systémové služby service fabric jsou spuštěny v primárním typu uzlu v clusteru. Při škálování dolů typu primárníuzel nikdy vertikálně snížit počet instancí na méně, než co [vyžaduje úroveň spolehlivosti.](service-fabric-cluster-capacity.md) 
 
Pro stavové služby, budete potřebovat určitý počet uzlů, které mají být vždy až k udržení dostupnosti a zachování stavu služby. Minimálně potřebujete počet uzlů rovnající se počet cílových replik sady oddílu nebo služby.

### <a name="remove-the-service-fabric-node"></a>Odebrání uzlu Service Fabric

Kroky pro ruční odebrání stavu uzlu platí pouze pro typy uzlů s *úrovní odolnosti bronz.*  U úrovně odolnosti *stříbra* a *zlaťáků* jsou tyto kroky prováděny automaticky platformou. Další informace o odolnosti najdete v článku [Plánování kapacity clusteru Service Fabric][durability].

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
> **Kontrola stavu deaktivace**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Zkontrolovat stav zastavení**
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

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Chování, která můžete pozorovat v aplikaci Service Fabric Explorer
Při vertikálně navýšit kapacitu clusteru Aplikace Service Fabric Explorer bude odrážet počet uzlů (instance škálovací sady virtuálních strojů), které jsou součástí clusteru.  Však při škálování clusteru dolů se zobrazí odebránuzel/virtuální hod instance zobrazí v stavu není v pořádku, pokud zavoláte [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) s příslušným názvem uzlu.   

Zde je vysvětlení pro toto chování.

Uzly uvedené v aplikaci Service Fabric Explorer jsou odrazem toho, co systémové služby Service Fabric (FM konkrétně) ví o počtu uzlů, které cluster měl nebo má. Při škálování nastavování velikosti virtuálního počítače byl odstraněn virtuální počítač, ale systémová služba FM si stále myslí, že uzel (který byl namapován na virtuální počítač, který byl odstraněn) se vrátí. Takže Service Fabric Explorer nadále zobrazovat, že uzel (i když stav může být chyba nebo neznámý).

Chcete-li se ujistit, že uzel je odebrán při odebrání virtuálního virtuálního zařízení, máte dvě možnosti:

1. Zvolte úroveň odolnosti zlaťáků nebo stříbra pro typy uzlů v clusteru, což vám umožní integraci infrastruktury. Který pak automaticky odebere uzly ze stavu našich systémových služeb (FM) při škálování dolů.
Podrobnosti [o úrovních trvanlivosti](service-fabric-cluster-capacity.md) naleznete zde

2. Po zmenšení instance virtuálního soudu je třeba volat [rutinu Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Clustery Service Fabric vyžadují určitý počet uzlů, které mají být vždy nahoru, aby byla zachována dostupnost a zachovat stav - označované jako "udržování kvora." Proto je obvykle nebezpečné vypnout všechny počítače v clusteru, pokud jste nejprve provedli [úplnou zálohu stavu](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Další kroky
Přečtěte si následující informace o plánování kapacity clusteru, upgradu clusteru a dělení služeb:

* [Plánování kapacity clusteru](service-fabric-cluster-capacity.md)
* [Upgrady clusteru](service-fabric-cluster-upgrade.md)
* [Stavové služby oddílů pro maximální škálování](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
