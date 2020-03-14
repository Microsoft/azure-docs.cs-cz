---
title: Horizontální navýšení nebo navýšení Service Fabric clusteru
description: Škálujte Service Fabric clusteru v nebo v souladu s požadavky nastavením pravidel automatického škálování pro každý typ uzlu nebo sadu škálování virtuálního počítače. Přidávání a odebírání uzlů do clusteru Service Fabric
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 26ef13f38d525e4e493ad933bfb906dd36ed0070
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258730"
---
# <a name="scale-a-cluster-in-or-out"></a>Horizontální snížení nebo navýšení kapacity clusteru

> [!WARNING]
> Přečtěte si tento oddíl před škálováním.

Škálování výpočetních prostředků ke zdroji pracovního zatížení vaší aplikace vyžaduje úmyslné plánování, téměř vždy trvá déle než hodinu, než se dokončí pro produkční prostředí, a vyžaduje, abyste porozuměli vašim úlohám a obchodním kontextům. Pokud jste tuto aktivitu ještě nikdy neudělali, doporučujeme začít čtením a porozuměním [Service Fabric posouzení kapacity clusteru](service-fabric-cluster-capacity.md), než budete pokračovat ve zbývající části tohoto dokumentu. Toto doporučení je aby se zabránilo neúmyslnému LiveSite problémům a doporučuje se také, že je úspěšně otestovat operace, které se rozhodnete provést proti mimo produkční prostředí. Kdykoli můžete [nahlásit výrobní problémy nebo požádat o vyplacenou podporu pro Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Pro techniky přidělené k provedení těchto operací, které mají odpovídající kontext Tento článek popisuje, operace škálování, ale je nutné rozhodnout a pochopit operace, které jsou vhodné pro případy použití; například prostředky škálování (CPU, úložiště, paměť) směr škálování (vertikálně nebo horizontálně) a jaké operace k provedení (nasazení prostředku šablony, portálu, Powershellu nebo rozhraní příkazového řádku).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Horizontální snížení nebo navýšení kapacity pomocí pravidel automatického škálování clusteru Service Fabric, nebo ručně
Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Service Fabric je nastavený jako samostatný virtuální počítač škálovací sady. U každého typu uzlu pak možné horizontálně snížit nebo navýšení kapacity nezávisle na sobě mají různé sady otevřených portů a může mít různé metriky kapacity. Přečtěte si další informace o tom v dokumentu [typy Service Fabricch uzlů](service-fabric-cluster-nodetypes.md) . Vzhledem k tomu, že typy uzlů Service Fabric v clusteru jsou v rámci back-endu sady virtuálních počítačů, je třeba nastavit pravidla automatického škálování pro každý typ uzlu nebo sadu škálování virtuálního počítače.

> [!NOTE]
> Předplatné musí mít dostatek jader, které chcete přidat nové virtuální počítače, které tvoří tento cluster. Proto pokud některý z limitů kvóty spuštění se zobrazí čas selhání nasazení není žádná ověření modelu v současné době. Také jednoho typu může mít maximálně pouze 100 uzlů na VMSS. Budete muset přidat VMSS je jedině tak dosáhnete cílové škálování a automatickým Škálováním můžete není automagically přidat VMSS společnosti. Přidávání VMSS do živého clusteru je náročné, a to obvykle vede k tomu, že uživatelé zřídí nové clustery s příslušnými typy uzlů zřízenými během vytváření. podle potřeby [Naplánujte kapacitu clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) . 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Zvolte typ/virtuální uzel škálovací sady škálování počítačů
V současné době nejste schopni k určení pravidel automatického škálování pro škálovací sady virtuálních počítačů pomocí portálu k vytvoření clusteru Service Fabric, proto nám dejte seznam typy uzlů a pak do nich přidejte pravidel automatického škálování pomocí Azure Powershellu (1.0 +).

Pokud chcete získat seznam škálovací sadu virtuálních počítačů, kterých se skládá cluster, spusťte následující rutiny:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Nastavení pravidel automatického škálování pro typ uzlu/sadu škálování virtuálního počítače
Pokud má cluster více typů uzlů, opakujte tento postup pro všechny typy uzlů/sady škálování virtuálních počítačů, které chcete škálovat (nebo z nich). Před nastavením automatického škálování vezměte v úvahu potřebný počet uzlů. Minimální počet uzlů, který je potřeba pro primární typ uzlu, vychází ze zvolené úrovně spolehlivosti. Přečtěte si další informace o [úrovních spolehlivosti](service-fabric-cluster-capacity.md).

> [!NOTE]
> Škálování a primární uzel typ na hodnotu menší než minimální počet zkontrolujte nestabilní clusteru nebo přenést. To může způsobit ztrátu dat pro vaše aplikace a služby systému.
> 
> 

Funkce automatického škálování není aktuálně využitím zatížení, které vaše aplikace může odesílajících sestavy do Service Fabric. Takže v tuto chvíli, automatické škálování, které získáte čistě doprovází čítače výkonu, které jsou emitovány každou z virtuálního počítače instance ve škálovací sadě.  

Podle těchto pokynů [nastavte automatické škálování pro jednotlivé sady škálování virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Ve scénáři horizontálního navýšení kapacity, pokud váš typ uzlu nemá [úroveň odolnosti][durability] Gold nebo stříbrné, je nutné volat [rutinu Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) s odpovídajícím názvem uzlu. Pro bronzovou odolnost se nedoporučuje škálovat více než jeden uzel současně.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ruční přidání virtuálních počítačů do typu uzlu/sady škálování virtuálních počítačů

Při horizontálním navyšování kapacity přidáte do škálovací sady více instancí virtuálních počítačů. Tyto instance se stanou uzly, které bude Service Fabric používat. Service Fabric to pozná, když se do škálovací sady přidají další instance (díky horizontálnímu navýšení kapacity), a automaticky zareaguje. 

> [!NOTE]
> Přidání virtuálních počítačů trvá určitou dobu, takže neočekáváme, že se doplňují. Proto si naplánujte, abyste předem nastavili kapacitu ještě více než 10 minut, než bude k dispozici kapacita virtuálního počítače pro repliky/instance služby, které se mají načíst.
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

Systémové služby Service Fabric běží ve vašem clusteru v typu primární uzel. Při horizontálním navýšení kapacity typu primární uzel nikdy nezmenšujte počet instancí na míň, než kolik zaručuje [úroveň spolehlivosti](service-fabric-cluster-capacity.md) . 
 
Stavové služby je nutné je určitý počet uzlů se vždy až zachování dostupnosti a zachovat stav vaší služby. Ve velmi minimální je třeba počet uzlů roven počtu cílových replik sady oddílu/služby.

### <a name="remove-the-service-fabric-node"></a>Odebrání uzlu Service Fabric

Postup ručního odebrání stavu uzlu se vztahuje pouze na typy uzlů s *bronzovou* úrovní odolnosti.  Pro *stříbro* a *zlatou* úroveň odolnosti se tyto kroky provádí automaticky na platformě. Další informace o odolnosti najdete v tématu [Service Fabric plánování kapacity clusteru][durability].

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

Jakmile na uzel aplikujete tyto tři kroky, může se odebrat ze škálovací sady. Pokud používáte jakoukoli úroveň odolnosti kromě [bronzu][durability], jsou tyto kroky provedeny po odebrání instance sady škálování.

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
> **Kontrola stavu zastavení**
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

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Chování, které můžete sledovat v Service Fabric Exploreru
Při škálování clusteru Service Fabric Explorer bude odrážet počet uzlů (virtuálních počítačů instance ve škálovací sadě), které jsou součástí clusteru.  Když ale škálovat cluster dolů, uvidíte, že odebraný uzel/instance virtuálního počítače se zobrazí ve stavu není v pořádku, pokud nebudete volat příkaz [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) s příslušným názvem uzlu.   

Následuje vysvětlení tohoto chování.

Uzly uvedené v Service Fabric Exploreru se odraz jaké systémové služby Service Fabric (převzetí služeb při selhání konkrétně) ví o počet uzlů clusteru měly nebo má. Když horizontálně snížíte kapacitu škálovací sady virtuálních počítačů, virtuální počítač byl odstraněn, ale služba převzetí služeb při selhání systému stále domnívá, že uzel, (, který je namapovaný na virtuální počítač, který byl odstraněn) bude vracet. Service Fabric Exploreru tak stále zobrazuje tento uzel (i když stav může být chyba nebo neznámá).

Pokud chcete mít jistotu, že je uzel odebrán při odebrání virtuálního počítače, máte dvě možnosti:

1. Zvolte úroveň odolnosti Gold a Silver pro typy uzlů v clusteru, který poskytuje integraci infrastruktury. Které pak automaticky odebere uzly z našich stavu služby (převzetí služeb při selhání) systému při vertikálně snížit kapacitu.
Tady najdete [Podrobnosti o úrovních trvanlivosti](service-fabric-cluster-capacity.md) .

2. Po horizontálním navýšení kapacity instance virtuálního počítače je potřeba zavolat [rutinu Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Clustery Service Fabric vyžadují určitý počet uzlů dá zvládnout za celou dobu, aby bylo možné zachovat dostupnost a zachovat stav – označuje jako "Správa kvora." Proto je obvykle bezpečné vypnout všechny počítače v clusteru, pokud jste nejprve neprováděli [úplné zálohování vašeho stavu](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Další kroky
Přečtěte si následující také další informace o plánování kapacity clusteru, inovace clusteru a vytváření oddílů služby:

* [Plánování kapacity clusteru](service-fabric-cluster-capacity.md)
* [Upgrady clusteru](service-fabric-cluster-upgrade.md)
* [Stavové služby pro oddíly pro maximální škálování](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
