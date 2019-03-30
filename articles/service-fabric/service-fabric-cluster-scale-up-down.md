---
title: Horizontální snížení nebo navýšení kapacity clusteru Service Fabric | Dokumentace Microsoftu
description: Horizontální snížení nebo navýšení kapacity clusteru Service Fabric tak, aby odpovídaly vyžádání nastavením pravidel automatického škálování pro každý uzel typu nebo virtuální počítače škálovací sady. Přidávání a odebírání uzlů do clusteru Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2019
ms.author: aljo
ms.openlocfilehash: f201ac1f0ea5a4bc07e8c052e7653194140e8759
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669363"
---
# <a name="scale-a-cluster-in-or-out"></a>Horizontální snížení nebo navýšení kapacity clusteru

> [!WARNING]
> Tuto část si přečtěte, než převedete

Škálování výpočetních prostředků ke zdroji pracovní zatížení aplikace vyžaduje úmyslné plánování, bude téměř vždy trvat déle než hodinu k dokončení pro produkční prostředí a vyžaduje, abyste úlohy a obchodní kontext; ve skutečnosti Pokud tato aktivita před nikdy uděláte, se doporučuje začnete tím, že čtení a pochopení [informace o plánování kapacity clusteru Service Fabric](service-fabric-cluster-capacity.md), než budete pokračovat zbytek tohoto dokumentu. Toto doporučení je aby se zabránilo neúmyslnému LiveSite problémům a doporučuje se také, že je úspěšně otestovat operace, které se rozhodnete provést proti mimo produkční prostředí. Kdykoli můžete [oznámit problémy v produkčním prostředí nebo požádat o placené podpory pro Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Pro techniky přidělené k provedení těchto operací, které mají odpovídající kontext Tento článek popisuje, operace škálování, ale je nutné rozhodnout a pochopit operace, které jsou vhodné pro případy použití; například prostředky škálování (CPU, úložiště, paměť) směr škálování (vertikálně nebo horizontálně) a jaké operace k provedení (nasazení prostředku šablony, portálu, Powershellu nebo rozhraní příkazového řádku).

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Horizontální snížení nebo navýšení kapacity pomocí pravidel automatického škálování clusteru Service Fabric, nebo ručně
Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Service Fabric je nastavený jako samostatný virtuální počítač škálovací sady. U každého typu uzlu pak možné horizontálně snížit nebo navýšení kapacity nezávisle na sobě mají různé sady otevřených portů a může mít různé metriky kapacity. Další informace o ho [typy uzlů Service Fabric](service-fabric-cluster-nodetypes.md) dokumentu. Protože typy uzlů Service Fabric v clusteru skládají ze škálovacích sad virtuálních počítačů v back-endu, musíte nastavit pravidla automatického škálování pro každý uzel typu nebo virtuální počítače škálovací sady.

> [!NOTE]
> Předplatné musí mít dostatek jader, které chcete přidat nové virtuální počítače, které tvoří tento cluster. Proto pokud některý z limitů kvóty spuštění se zobrazí čas selhání nasazení není žádná ověření modelu v současné době. Také jednoho typu může mít maximálně pouze 100 uzlů na VMSS. Budete muset přidat VMSS je jedině tak dosáhnete cílové škálování a automatickým Škálováním můžete není automagically přidat VMSS společnosti. Přidání VMSS na místě k živému clusteru je náročné úlohy a výsledkem je obvykle uživatelům zřizování nových clusterů se příslušný uzel typy zřízený v okamžiku vytvoření; [plánování kapacity clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) odpovídajícím způsobem. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Zvolte typ/virtuální uzel škálovací sady škálování počítačů
V současné době nejste schopni k určení pravidel automatického škálování pro škálovací sady virtuálních počítačů pomocí portálu k vytvoření clusteru Service Fabric, proto nám dejte seznam typy uzlů a pak do nich přidejte pravidel automatického škálování pomocí Azure Powershellu (1.0 +).

Pokud chcete získat seznam škálovací sadu virtuálních počítačů, kterých se skládá cluster, spusťte následující rutiny:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Sada pravidel automatického škálování pro škálovací sadu typu nebo virtuální počítače uzlu
Pokud váš cluster má více typů uzlů, opakujte pro každý uzel typy nebo virtuální počítače škálovací sady, že chcete škálovat (dovnitř nebo ven). Před nastavením automatického škálování vezměte v úvahu potřebný počet uzlů. Minimální počet uzlů, který je potřeba pro primární typ uzlu, vychází ze zvolené úrovně spolehlivosti. Další informace o [spolehlivost](service-fabric-cluster-capacity.md).

> [!NOTE]
> Škálování a primární uzel typ na hodnotu menší než minimální počet zkontrolujte nestabilní clusteru nebo přenést. To může způsobit ztrátu dat pro vaše aplikace a služby systému.
> 
> 

Funkce automatického škálování není aktuálně využitím zatížení, které vaše aplikace může odesílajících sestavy do Service Fabric. Takže v tuto chvíli, automatické škálování, které získáte čistě doprovází čítače výkonu, které jsou emitovány každou z virtuálního počítače instance ve škálovací sadě.  

Postupujte podle těchto pokynů [nastavení automatického škálování pro každou škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> V vertikálního snižování kapacity scénář Pokud má váš typ uzlu [úroveň odolnosti] [ durability] Gold a Silver potřebné k volání [rutiny Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) s název odpovídající uzlu. Kvůli vyšší odolnosti bronzová ne doporučujeme vertikálně snížit kapacitu více než jeden uzel v čase.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ruční přidání virtuálních počítačů na uzlu typu nebo virtuální počítače škálovací sady

Při horizontálním navyšování kapacity přidáte do škálovací sady více instancí virtuálních počítačů. Tyto instance se stanou uzly, které bude Service Fabric používat. Service Fabric to pozná, když se do škálovací sady přidají další instance (díky horizontálnímu navýšení kapacity), a automaticky zareaguje. 

> [!NOTE]
> Přidání virtuálních počítačů trvá určitou dobu, proto Nečekejte dotazoval doplňky. Proto naplánujte navyšovat kapacitu i předem, aby se více než 10 minut, než je k dispozici pro instance repliky nebo služby získat umístěny kapacitu virtuálních počítačů.
> 

### <a name="add-vms-using-a-template"></a>Přidání virtuálních počítačů pomocí šablony
Ukázka/podle pokynů [Galerie šablon rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Chcete-li změnit počet virtuálních počítačů v každém typu uzlu. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Přidání virtuálních počítačů pomocí příkazů prostředí PowerShell nebo rozhraní příkazového řádku
Následující kód načte škálovací sadu podle názvu a zvýší její **kapacitu** o 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Tento kód nastaví kapacitu na 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ručně odeberte z uzlu typu nebo virtuální počítače škálovací sady virtuálních počítačů
Když horizontálně snížíte kapacitu v typu uzlu, odeberete instance virtuálních počítačů ze škálovací sady. Pokud typ uzlu je ten bronzová úroveň odolnosti, Service Fabric Nepozná, co se stalo a hlásí, že uzel se ztratilo. Service Fabric pak hlásí, že stav clusteru není v pořádku. Pokud chcete tomuto nežádoucímu stavu zabránit, musíte explicitně odebrat uzel z clusteru a odebrat stav uzlu.

Systémové služby service fabric spusťte v primární typ uzlu v clusteru. Během vertikálního snižování primární typ uzlu, nikdy vertikálně snížit kapacitu počtu instancí na hodnotu menší než co [úroveň spolehlivosti](service-fabric-cluster-capacity.md) zaručuje. 
 
Stavové služby je nutné je určitý počet uzlů se vždy až zachování dostupnosti a zachovat stav vaší služby. Ve velmi minimální je třeba počet uzlů roven počtu cílových replik sady oddílu/služby.

### <a name="remove-the-service-fabric-node"></a>Odebrání uzlu Service Fabric

Postup za ruční odebrání stav uzlu se vztahuje pouze na typy uzlů *bronzová* úroveň odolnosti.  Pro *Silver* a *Gold* úroveň odolnosti, tyto kroky se provádějí automaticky platformou. Další informace o odolnosti najdete v článku [Plánování kapacity clusteru Service Fabric][durability].

V zájmu toho, aby byly uzly clusteru rovnoměrně rozdělené v doménách upgradu a doménách selhání a aby se tedy i rovnoměrně využívaly, je potřeba jako první odebrat naposledy vytvořený uzel. Jinak řečeno, uzly by se měly odebírat v opačném pořadí, než v jakém došlo k jejich vytvoření. Naposledy vytvořený uzel je uzel s nejvyšší hodnotou vlastnosti `virtual machine scale set InstanceId`. Následující příklady kódu vracejí naposledy vytvořený uzel.

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
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

```azurecli
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
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Tento kód nastaví kapacitu na 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Chování, které můžete sledovat v Service Fabric Exploreru
Při škálování clusteru Service Fabric Explorer bude odrážet počet uzlů (virtuálních počítačů instance ve škálovací sadě), které jsou součástí clusteru.  Ale když horizontálně snížíte kapacitu clusteru seznamu se zobrazí odebral uzel nebo instanci virtuálního počítače. zobrazí ve stavu není v pořádku, pokud zavoláte [odebrat ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) s názvem příslušný uzel.   

Následuje vysvětlení tohoto chování.

Uzly uvedené v Service Fabric Exploreru se odraz jaké systémové služby Service Fabric (převzetí služeb při selhání konkrétně) ví o počet uzlů clusteru měly nebo má. Když horizontálně snížíte kapacitu škálovací sady virtuálních počítačů, virtuální počítač byl odstraněn, ale služba převzetí služeb při selhání systému stále domnívá, že uzel, (, který je namapovaný na virtuální počítač, který byl odstraněn) bude vracet. Service Fabric Exploreru tak stále zobrazuje tento uzel (i když stav může být chyba nebo neznámá).

Pokud chcete mít jistotu, že je uzel odebrán při odebrání virtuálního počítače, máte dvě možnosti:

1. Zvolte úroveň odolnosti Gold a Silver pro typy uzlů v clusteru, který poskytuje integraci infrastruktury. Které pak automaticky odebere uzly z našich stavu služby (převzetí služeb při selhání) systému při vertikálně snížit kapacitu.
Odkazovat na [podrobnosti o sem úrovně odolnosti](service-fabric-cluster-capacity.md)

2. Jakmile zmenšování instance virtuálního počítače je potřeba volat [rutiny Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Clustery Service Fabric vyžadují určitý počet uzlů dá zvládnout za celou dobu, aby bylo možné zachovat dostupnost a zachovat stav – označuje jako "Správa kvora." Proto je obvykle bezpečné vypnout všechny počítače v clusteru, pokud jste nejdřív provedli [úplného zálohování vašeho státu](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Další postup
Přečtěte si následující také další informace o plánování kapacity clusteru, inovace clusteru a vytváření oddílů služby:

* [Plánování kapacity clusteru](service-fabric-cluster-capacity.md)
* [Upgrade clusteru](service-fabric-cluster-upgrade.md)
* [Oddíl stavové služby pro maximální měřítko](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
