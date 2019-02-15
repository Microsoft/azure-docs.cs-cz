---
title: Odebrat typ uzlu v Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak odebrat typ uzlu z clusteru Service Fabric běžící v Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 63a18b6a24d922c48129df56045ec3e1d67bac53
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300988"
---
# <a name="remove-a-service-fabric-node-type"></a>Odebrat typ uzlu Service Fabric
Tento článek popisuje, jak škálování clusteru Azure Service Fabric odebráním existujícího typu uzlu z clusteru. Cluster Service Fabric je síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, který použijete k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure je [nastavit jako samostatné škálovací sada](service-fabric-cluster-nodetypes.md). Každý typ uzlu je pak spravovat samostatně. Po vytvoření clusteru Service Fabric, můžete horizontálně škálovat cluster odebráním typu uzlu (škálovací sady virtuálních počítačů) a všechny jeho uzly.  Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru.  Škálování clusteru, vaše aplikace automaticky škálovat směrem také.

Použití [Remove-azurermservicefabricnodetype:](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) k odebrání typu uzlu Service Fabric.

Jsou tři operace, ke kterým dochází při Remove-azurermservicefabricnodetype: volání:
1.  Odstranění virtuálního počítače škálovací sadě za nástrojem typ uzlu.
2.  Typ uzlu je odebrat z clusteru.
3.  Pro každý uzel v rámci uzlu typu celého stavu pro tento uzel odebrat ze systému. Pokud na tomto uzlu služby, potom služby jsou nejprve přesunout do jiného uzlu. Pokud správce clusteru nelze najít uzel repliky nebo službu, tato operace je zpožděné/zablokováno.

> [!WARNING]
> Pomocí Remove-azurermservicefabricnodetype: Odebere typ uzlu z clusteru pro produkční prostředí se nedoporučuje používat často. Odstraní prostředek sady škálování virtuálního počítače za typ uzlu je nebezpečný příkaz. 

## <a name="durability-characteristics"></a>Vlastnosti odolnosti
Při použití Remove-azurermservicefabricnodetype: bezpečnosti prioritu před rychlostí. Typ uzlu musí být stříbrné nebo zlaté úrovně [úroveň odolnosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster), protože:
- Bronzová vám neposkytují žádné záruky týkající se ukládání informací o stavu.
- Stříbrné a zlaté odolnosti zachytávat všechny změny do škálovací sady.
- Zlatá také umožňuje kontrolu nad Azure aktualizuje pod škálovací sady.

Service Fabric "orchestruje" základní změny a aktualizuje tak, že nedojde ke ztrátě dat. Při odebírání uzlu s odolností bronzová však může ztratit informace o stavu. Pokud odstraňujete typ primárního uzlu a je bezstavové aplikace, bronzová je přijatelné. Při spuštění stavová zatížení v produkčním prostředí by měl být minimální požadavky na konfiguraci Silver. Podobně pro produkční scénáře primární typ uzlu musí být vždy stříbrné nebo zlaté úrovně.

### <a name="more-about-bronze-durability"></a>Další informace o odolnosti bronzová

Při odebírání typ uzlu, který je Bronzová, všechny uzly v typu uzlu ihned vypne. Service Fabric nebude zachytávat jakékoli aktualizace bronzová uzly škálovací sady, tak všechny virtuální počítače přejít ihned. Pokud jste měli nic stavové na těchto uzlech, data budou ztracena. Teď, i v případě, že jste byli bezstavové, všechny uzly v Service Fabric účastnit okruhu, aby celé prostředí byly pravděpodobně ztraceny, což služeb můžou destabilizovat samotného clusteru.

## <a name="recommended-node-type-removal-process"></a>Doporučené procesu odebrání typu uzlu

Pokud chcete odebrat typ uzlu, spusťte [Remove-azurermservicefabricnodetype:](/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) rutiny.  Rutina trvá delší dobu.  Potom spusťte [odebrat ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) na každém uzlu, které mají být odstraněny.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzureRmServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Další postup
- Další informace o clusteru [odolnosti charakteristiky](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Další informace o [typy uzlů a VM Scale Sets](service-fabric-cluster-nodetypes.md).
- Další informace o [škálování clusteru Service Fabric](service-fabric-cluster-scaling.md).
