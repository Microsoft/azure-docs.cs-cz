---
title: Horizontální snížení nebo navýšení kapacity clusteru Service Fabric | Dokumentace Microsoftu
description: Horizontální snížení nebo navýšení kapacity clusteru Service Fabric tak, aby odpovídaly vyžádání nastavením pravidel automatického škálování pro každý uzel typu nebo virtuální počítače škálovací sady. Přidávání a odebírání uzlů do clusteru Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: aljo
ms.openlocfilehash: 1869b25756693a4a3626d713b6bd2adab035cea6
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716757"
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Horizontální snížení nebo navýšení kapacity pomocí pravidel automatického škálování clusteru Service Fabric, nebo ručně
Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Service Fabric je nastavený jako samostatný virtuální počítač škálovací sady. U každého typu uzlu pak možné horizontálně snížit nebo navýšení kapacity nezávisle na sobě mají různé sady otevřených portů a může mít různé metriky kapacity. Další informace o ho [Service Fabric nodetypes](service-fabric-cluster-nodetypes.md) dokumentu. Protože typy uzlů Service Fabric v clusteru skládají ze škálovacích sad virtuálních počítačů v back-endu, musíte nastavit pravidla automatického škálování pro každý uzel typu nebo virtuální počítače škálovací sady.

> [!NOTE]
> Předplatné musí mít dostatek jader, které chcete přidat nové virtuální počítače, které tvoří tento cluster. Proto pokud některý z limitů kvóty spuštění se zobrazí čas selhání nasazení není žádná ověření modelu v současné době.
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Zvolte typ/virtuální uzel škálovací sady škálování počítačů
V současné době nejste schopni k určení pravidel automatického škálování pro škálovací sady virtuálních počítačů pomocí portálu, proto nám dejte seznam typy uzlů a pak do nich přidejte pravidel automatického škálování pomocí Azure Powershellu (1.0 +).

Pokud chcete získat seznam škálovací sadu virtuálních počítačů, kterých se skládá cluster, spusťte následující rutiny:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Nastavení pravidel automatického škálování pro typ nebo virtuální uzel počítače škálovací sady
Pokud váš cluster má více typů uzlů, opakujte pro každý uzel typy nebo virtuální počítače škálovací sady, že chcete škálovat (dovnitř nebo ven). Před nastavením automatického škálování vezměte v úvahu potřebný počet uzlů. Minimální počet uzlů, který je potřeba pro primární typ uzlu, vychází ze zvolené úrovně spolehlivosti. Další informace o [spolehlivost](service-fabric-cluster-capacity.md).

> [!NOTE]
> Škálování a primární uzel typ na hodnotu menší než minimální počet zkontrolujte nestabilní clusteru nebo přenést. To může způsobit ztrátu dat pro vaše aplikace a služby systému.
> 
> 

Funkce automatického škálování není aktuálně využitím zatížení, které vaše aplikace může odesílajících sestavy do Service Fabric. Takže v tuto chvíli, automatické škálování, které získáte čistě doprovází čítače výkonu, které jsou emitovány každou z virtuálního počítače instance ve škálovací sadě.  

Postupujte podle těchto pokynů [nastavení automatického škálování pro každou škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> V vertikálně scénář, pokud váš typ uzlu má úroveň odolnosti Gold a Silver potřebné k volání [rutiny Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) s názvem příslušný uzel. Kvůli vyšší odolnosti bronzová ne doporučujeme vertikálně snížit kapacitu více než jeden uzel v čase.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ručně přidáte k typu uzlu nebo virtuální počítače škálovací sady virtuálních počítačů
Ukázka/podle pokynů [Galerie šablon rychlý start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Chcete-li změnit počet virtuálních počítačů v každé Nodetype. 

> [!NOTE]
> Přidání virtuálních počítačů trvá určitou dobu, proto Nečekejte dotazoval doplňky. Proto naplánujte navyšovat kapacitu dobře v čase, chcete-li povolit pro více než 10 minut, než je k dispozici pro repliky kapacitu virtuálních počítačů / service instancí k získání umístěn.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Ručně odeberte z typu nebo virtuální primárního uzlu počítače škálovací sady virtuálních počítačů
> [!NOTE]
> Systémové služby service fabric spusťte v primární typ uzlu v clusteru. Proto by nikdy vypnout vertikálně zvýšit nebo snížit počet instancí v tomto typy uzlů, které zaručuje nižší než úroveň spolehlivosti. Odkazovat na [podrobnosti o spolehlivosti úrovních zde](service-fabric-cluster-capacity.md). 
> 
> 

Je nutné provést následující kroky jeden virtuální počítač instance najednou. To umožňuje systémové služby (a stavové služby) se řádně ukončit na instanci virtuálního počítače, kterou chcete odebrat a nové repliky vytvořené na jiných uzlech.

1. Spustit [Disable-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) s cílem je "RemoveNode" Zakázat uzel, Chystáte se odebrat (nejvyšší instance tohoto typu).
2. Spustit [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) abyste měli jistotu, že uzel má skutečně převeden na zakázáno. Pokud ne, počkejte, dokud nebude uzel je zakázaná. Tento krok nelze hurry.
3. Ukázka/podle pokynů [Galerie šablon rychlý start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Chcete-li změnit počet virtuálních počítačů jednou v tomto typu uzlu. Odebrání instance je nejvyšší instance virtuálního počítače. 
4. Opakujte kroky 1 až 3 podle potřeby, ale nikdy vertikálně snížit kapacitu počtu instancí ve primárního uzlu typy menší, než co zaručuje úroveň spolehlivosti. Odkazovat na [podrobnosti o spolehlivosti úrovních zde](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Ručně odeberte z typu nebo virtuální uzel není váš primární počítač škálovací sady virtuálních počítačů
> [!NOTE]
> Stavové služby je nutné je určitý počet uzlů se vždy až zachování dostupnosti a zachovat stav vaší služby. Ve velmi minimální je třeba počet uzlů roven počtu cílových replik sady oddílu/služby. 
> 
> 

Je nutné provést následující kroky jeden virtuální počítač instance najednou. To umožňuje systémové služby (a stavové služby) se řádně ukončit na instancích virtuálních počítačů, které odebíráte a nové repliky vytvořit jiného umístění.

1. Spustit [Disable-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) s cílem je "RemoveNode" Zakázat uzel, Chystáte se odebrat (nejvyšší instance tohoto typu).
2. Spustit [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) abyste měli jistotu, že uzel má skutečně převeden na zakázáno. Pokud ne, počkejte, dokud nebude uzel je zakázaná. Tento krok nelze hurry.
3. Ukázka/podle pokynů [Galerie šablon rychlý start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Chcete-li změnit počet virtuálních počítačů jednou v tomto typu uzlu. Tato akce odebere teď nejvyšší instance virtuálního počítače. 
4. Opakujte kroky 1 až 3 podle potřeby, ale nikdy vertikálně snížit kapacitu počtu instancí ve primárního uzlu typy menší, než co zaručuje úroveň spolehlivosti. Odkazovat na [podrobnosti o spolehlivosti úrovních zde](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Chování, které můžete sledovat v Service Fabric Exploreru
Při škálování clusteru Service Fabric Explorer bude odrážet počet uzlů (virtuálních počítačů instance ve škálovací sadě), které jsou součástí clusteru.  Ale když horizontálně snížíte kapacitu clusteru seznamu se zobrazí odebral uzel nebo instanci virtuálního počítače. zobrazí ve stavu není v pořádku, pokud zavoláte [odebrat ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) s názvem příslušný uzel.   

Následuje vysvětlení tohoto chování.

Uzly uvedené v Service Fabric Exploreru se odraz jaké systémové služby Service Fabric (převzetí služeb při selhání konkrétně) ví o počet uzlů clusteru měly nebo má. Když horizontálně snížíte kapacitu škálovací sady virtuálních počítačů, virtuální počítač byl odstraněn, ale služba převzetí služeb při selhání systému stále domnívá, že uzel, (, který je namapovaný na virtuální počítač, který byl odstraněn) bude vracet. Service Fabric Exploreru tak stále zobrazuje tento uzel (i když stav může být chyba nebo neznámá).

Pokud chcete mít jistotu, že je uzel odebrán při odebrání virtuálního počítače, máte dvě možnosti:

1) Zvolte úroveň odolnosti Gold a Silver pro typy uzlů v clusteru, který poskytuje integraci infrastruktury. Které pak automaticky odebere uzly z našich stavu služby (převzetí služeb při selhání) systému při vertikálně snížit kapacitu.
Odkazovat na [podrobnosti o sem úrovně odolnosti](service-fabric-cluster-capacity.md)

2) Jakmile zmenšování instance virtuálního počítače je potřeba volat [rutiny Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

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
