---
title: Odebrání typu uzlu v Azure Service Fabric | Microsoft Docs
description: Naučte se, jak odebrat typ uzlu z Service Fabric clusteru se systémem v Azure.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: atsenthi
ms.openlocfilehash: 44f25adf4168f4339a31e9270c2b23a8466a8889
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599490"
---
# <a name="remove-a-service-fabric-node-type"></a>Odebrání typu Service Fabric uzlu
Tento článek popisuje, jak škálovat cluster Azure Service Fabric odebráním existujícího typu uzlu z clusteru. Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Sady škálování virtuálních počítačů jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definovaný v clusteru Azure, je [nastavený jako samostatná sada škálování](service-fabric-cluster-nodetypes.md). Každý typ uzlu se pak dá spravovat samostatně. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně odebráním typu uzlu (sada škálování virtuálního počítače) a všech jeho uzlů.  Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru.  I když se cluster škáluje, vaše aplikace se automaticky škálují.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K odebrání typu Service Fabric uzlu použijte [příkaz Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) .

Tři operace, ke kterým dochází při volání Remove-AzServiceFabricNodeType, jsou:
1.  Velikost virtuálního počítače nastavená za typem uzlu se odstraní.
2.  Typ uzlu je odebrán z clusteru.
3.  Pro každý uzel v rámci tohoto typu uzlu je celý stav pro tento uzel ze systému odstraněn. Pokud v tomto uzlu existují služby, pak se služby nejprve přesunou na jiný uzel. Pokud Správce clusteru nenašel uzel pro repliku nebo službu, operace je zpožděná nebo blokovaná.

> [!WARNING]
> Použití funkce Remove-AzServiceFabricNodeType k odebrání typu uzlu z produkčního clusteru se nedoporučuje používat na častém základě. Jedná se o nebezpečný příkaz, protože odstraňuje prostředek sady škálování virtuálního počítače za typem uzlu. 

## <a name="durability-characteristics"></a>Charakteristiky odolnosti
Bezpečnost je při použití Remove-AzServiceFabricNodeType nastavena na vyšší prioritu. Typ uzlu musí být stříbrná nebo zlatá [úroveň odolnosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster), protože:
- Bronz vám neposkytuje žádné záruky týkající se ukládání informací o stavu.
- Stříbrná a zlatá odolnost proti změnám v sadě škálování.
- Gold taky poskytuje kontrolu nad aktualizacemi Azure pod sadou škálování.

Service Fabric "orchestruje" příslušné změny a aktualizace, aby nedošlo ke ztrátě dat. Pokud ale odeberete uzel s bronzovou odolností, může dojít ke ztrátě informací o stavu. Pokud odebíráte typ primárního uzlu a vaše aplikace je Bezstavová, je k DISA bronzová. Při spouštění stavových úloh v produkčním prostředí by měla být minimální konfigurace stříbrná. Podobně v produkčních scénářích by měl být primární typ uzlu vždy stříbrná nebo zlatá.

### <a name="more-about-bronze-durability"></a>Další informace o bronzové odolnosti

Při odebírání typu uzlu, který je bronz, se okamžitě najdou všechny uzly v typu uzlu. Service Fabric nepřesahují žádné aktualizace nastavení škály bronzových uzlů. všechny virtuální počítače se pak hned dostanou. Pokud na těchto uzlech máte nějaké stav, ztratí se data. Nyní i v případě, že jste byli bezstavní, všechny uzly v Service Fabric se účastní prstence, takže celé čtvrti může být ztracena, což může destabilizovat samotný cluster.

## <a name="recommended-node-type-removal-process"></a>Doporučený proces odebrání typu uzlu

Chcete-li odebrat typ uzlu, spusťte rutinu [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) .  Dokončení rutiny trvá nějakou dobu.  Jakmile se všechny virtuální počítače odešlou (zobrazují se jako "nižší"), zobrazí se chybový stav topologie Fabric:/System/InfrastructureService/[název NodeType].

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Pak můžete prostředek clusteru aktualizovat a odebrat typ uzlu. Můžete buď použít nasazení šablony ARM, nebo upravit prostředek clusteru prostřednictvím [Azure Resource Manageru](https://resources.azure.com). Tím se spustí upgrade clusteru, který odebere službu Fabric:/System/InfrastructureService/[název NodeType], která je v chybovém stavu.

V Service Fabric Explorer se pořád zobrazují uzly "rozdálení". Na každém uzlu, který chcete odebrat, spusťte rutinu [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) .


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Další postup
- Přečtěte si další informace o [vlastnostech odolnosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)clusteru.
- Další informace o [typech uzlů a Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md).
- Přečtěte si další informace o [Service Fabric škálování clusteru](service-fabric-cluster-scaling.md).
