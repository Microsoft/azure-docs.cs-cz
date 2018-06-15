---
title: Škálování clusteru Service Fabric příchozí nebo odchozí | Microsoft Docs
description: Cluster Service Fabric příchozí nebo odchozí škálovat tak, aby odpovídaly vyžádání nastavením pravidla automatického škálování pro každý uzel typu nebo virtuální počítač škálovací sadu. Přidávat nebo odebírat uzly do clusteru Service Fabric
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
ms.openlocfilehash: c2479dad013bfcb738e61e67cc8cf9584b4d11cc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204810"
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Škálování clusteru Service Fabric v nebo pomocí pravidel automatického škálování, nebo ručně
Sady škálování virtuálního počítače se Azure výpočtový prostředek, který můžete použít k nasazení a správě kolekci jako sada virtuálních počítačů. Každý typ uzlu, který je definován v clusteru Service Fabric je nastavený jako sada škálování samostatný virtuální počítač. Každý typ uzlu můžete škálovat v nebo na nezávisle, mají různé sady otevřené porty a může mít různé kapacity metriky. Další informace o jeho [nodetypes Service Fabric](service-fabric-cluster-nodetypes.md) dokumentu. Vzhledem k tomu, že Service Fabric typy uzlů v clusteru jsou tvořeny sady škálování virtuálního počítače v back-end, budete muset nastavit pravidla pro automatické škálování pro každý uzel typu nebo virtuální počítač škálovací sadu.

> [!NOTE]
> Vaše předplatné musí mít dostatečný počet jader, který má-li přidat nové virtuální počítače, které tvoří tento cluster. Proto čas selhání nasazení, když se některé z maximální kvóty dosáhl neexistuje žádné ověření modelu v současné době.
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Zvolte typ nebo virtuální uzel sad škálování škálování počítače
V současné době nejste moci zadat pravidla automatického škálování pro sady škálování virtuálního počítače pomocí portálu, takže dejte nám pomocí prostředí Azure PowerShell (1.0 +) seznam typů uzlů a poté přidejte k nim pravidel automatického škálování.

Pokud chcete získat seznam škálovací sadu virtuálních počítačů, který vytváří cluster, spusťte následující rutiny:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Nastavení automatického škálování pravidel pro typ nebo virtuální uzel škálovací sadu počítačů.
Pokud váš cluster má více typů uzlu, potom opakujte pro každý uzel typy nebo virtuální počítače nastaví tato chcete škálovat (příchozí nebo odchozí). Před nastavením automatického škálování vezměte v úvahu potřebný počet uzlů. Minimální počet uzlů, který je potřeba pro primární typ uzlu, vychází ze zvolené úrovně spolehlivosti. Další informace o [úrovní spolehlivosti](service-fabric-cluster-capacity.md).

> [!NOTE]
> Škálování dolů primárním uzlu, který typ na hodnotu menší než minimální počet zkontrolujte nestabilním clusteru nebo tím, že ji. To může vést ke ztrátě dat pro vaše aplikace a systémových služeb.
> 
> 

Funkce automatického škálování není aktuálně doprovází zatížením, které vaše aplikace může být vytváření sestav do Service Fabric. Proto v současnosti automatickému škálování, které máte čistě vycházejí z čítače výkonu, které jsou vygenerované každou virtuálního počítače sady škálování instancí.  

Postupujte podle těchto pokynů [nastavení automatického škálování pro každý škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Vertikálně scénář, pokud váš typ uzlu má úroveň odolnosti zlatý nebo Silver je nutné volat [rutinu Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) s názvem příslušné uzlu.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ručně přidáte do typu uzlu nebo virtuální počítač škálovací sadu virtuálních počítačů
Ukázka nebo podle pokynů [Galerie šablon úvodní](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Chcete-li změnit počet virtuálních počítačů v každém uzlu. 

> [!NOTE]
> Přidání virtuálních počítačů má čas, proto Nečekejte, budou přidány jako okamžitou. Proto naplánujte přidat kapacitu i v průběhu času, povolit pro více než 10 minut, než je k dispozici pro repliky kapacita virtuálního počítače nebo služby instance, které chcete získat umístit.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Ručně odeberte virtuální počítače z typu nebo virtuální primárního uzlu škálovací sadu počítačů
> [!NOTE]
> V typu primárního uzlu v clusteru spusťte service fabric systémových služeb. Tak, aby měli nikdy vypnout nebo snížit počet instancí v tomto uzlu typech poskytuje na menší, než jakou úroveň spolehlivosti. Odkazovat na [informace o spolehlivosti vrstev zde](service-fabric-cluster-capacity.md). 
> 
> 

Je třeba provést následující kroky jeden virtuální počítač instance současně. To umožňuje korektně vypnout instanci virtuálního počítače, kterou chcete odebrat a nové repliky vytvořené na jiných uzlech systémových služeb (a vaše stavové služby).

1. Spustit [zakázat ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) se záměrem 'RemoveNode' Chcete-li zakázat uzlu se chystáte odebrat (nejvyšší instance typu uzlu).
2. Spustit [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) a ujistěte se, že uzel přešla skutečně na zakázáno. Pokud ne, počkejte, až uzel je zakázána. Tento krok nelze hurry.
3. Ukázka nebo podle pokynů [Galerie šablon úvodní](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Chcete-li změnit počet virtuálních počítačů pomocí jedné v tomto typu uzlu. Instance odebrat je nejvyšší instance virtuálního počítače. 
4. Opakujte kroky 1 až 3 podle potřeby, ale nikdy snižovat počet instancí v primárním uzlu typy menší než co zaručuje úroveň spolehlivosti. Odkazovat na [informace o spolehlivosti vrstev zde](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Ručně odeberte z typu nebo virtuální uzel není primární počítač škálovací sadu virtuálních počítačů
> [!NOTE]
> Pro stavové služby budete potřebovat k určitému počtu uzlů se vždy až zachování dostupnosti a zároveň zachovat stav služby. Ve velmi minimální je třeba počet uzlů rovná počtu cílových replik sadu oddílu nebo služby. 
> 
> 

Musíte provést následující kroky jeden virtuální počítač instance současně. To umožňuje, aby korektně vypnout na instanci virtuálního počítače, kterou chcete odebrat systémových služeb (a vaše stavové služby) a vytvořit nové repliky else where.

1. Spustit [zakázat ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) se záměrem 'RemoveNode' Chcete-li zakázat uzlu se chystáte odebrat (nejvyšší instance typu uzlu).
2. Spustit [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) a ujistěte se, že uzel přešla skutečně na zakázáno. Pokud ne, počkejte, až uzel je zakázána. Tento krok nelze hurry.
3. Ukázka nebo podle pokynů [Galerie šablon úvodní](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) Chcete-li změnit počet virtuálních počítačů pomocí jedné v tomto typu uzlu. Tato akce odebere teď nejvyšší instance virtuálního počítače. 
4. Opakujte kroky 1 až 3 podle potřeby, ale nikdy snižovat počet instancí v primárním uzlu typy menší než co zaručuje úroveň spolehlivosti. Odkazovat na [informace o spolehlivosti vrstev zde](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Chování můžete pozorovat v Service Fabric Exploreru
Při škálování cluster Service Fabric Explorer se projeví počet uzlů (instancí sady škálování virtuálního počítače), které jsou součástí clusteru.  Při změně měřítka však se cluster dolů můžete zobrazí instance odebrané uzlu nebo virtuální počítač zobrazí stav v pořádku, pokud zavoláte [odebrat ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) s názvem příslušné uzlu.   

Následuje vysvětlení tohoto chování.

Uzly uvedené v Service Fabric Explorer jsou odraz jaké služby Service Fabric systému (FM konkrétně) ví o počtu uzlů clusteru měl/má. Při změně měřítka sad škálování virtuálního počítače, virtuální počítač byl odstraněn, ale FM systému služby stále domnívá, že uzel (který je namapovaný na virtuální počítač, který byl odstraněn) se vraťte. Proto Service Fabric Explorer stále zobrazuje tento uzel (i když stav může být chyba nebo neznámá).

Pokud chcete mít jistotu, že uzel je odebrán, když dojde k odebrání virtuálního počítače, máte dvě možnosti:

1) Zvolte si úroveň odolnosti zlatý nebo Silver pro typy uzlů v clusteru, která umožňuje integraci infrastruktury. Které pak automaticky odebere uzly z našich služeb (FM) stavu systému při změně měřítka.
Odkazovat na [podrobnosti o sem úrovně odolnosti](service-fabric-cluster-capacity.md)

2) Jakmile byla změněna velikost instance virtuálního počítače, je třeba volat [rutinu Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

> [!NOTE]
> Clusterů Service Fabric vyžadovat určité počet uzlů na být až na celou dobu pro zachování dostupnosti a zachovávají stav – označuje jako "údržbu kvora." Ano, je obvykle unsafe vypnout všechny počítače v clusteru, pokud jste provedli nejprve [úplného zálohování vaší stavu](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Další postup
Přečtěte si následující také další informace o plánování kapacity clusteru, upgradu clusteru a rozdělení do oddílů služby:

* [Plánování kapacity vašeho clusteru](service-fabric-cluster-capacity.md)
* [Upgrade clusteru](service-fabric-cluster-upgrade.md)
* [Oddíl stavové služby pro maximální měřítko](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
