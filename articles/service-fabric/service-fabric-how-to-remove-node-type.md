---
title: Jak na vzdálený počítač uzlem typu v Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak odebrat uzel typu v Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53980576"
---
# <a name="remove-a-service-fabric-node-type"></a>Odebrat typ uzlu Service Fabric

Použití [Remove-azurermservicefabricnodetype:](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) k odebrání typu uzlu Service Fabric.

Dvě operace, ke kterým dochází při volání Remove-azurermservicefabricnodetype: jsou:
1.  Virtuální počítač Škálovací nastavit (VMSS) za typ uzlu je odstraněn.
2.  Pro všechny uzly v rámci uzlu typu celý stav pro tento uzel se odebere ze systému. Pokud na tomto uzlu služby, potom služby jsou nejprve přesunout do jiného uzlu. Pokud správce clusteru nelze najít uzel repliky nebo službu, tato operace je zpožděné/zablokováno.

> [!NOTE]
> Pomocí Remove-azurermservicefabricnodetype: Odebere typ uzlu z clusteru pro produkční prostředí se nedoporučuje používat často. V tomto případě je velmi nebezpečné příkaz, jak je v podstatě Odstraní prostředek Škálovací sady virtuálních počítačů za typ uzlu. Při volání Remove-azurermservicefabricnodetype: systém nemá žádný způsob, jak zjistit, pokud vás zajímají bezpečné odstranění. 

## <a name="durability-characteristics"></a>Vlastnosti odolnosti
Při použití Remove-azurermservicefabricnodetype: bezpečnosti prioritu před rychlostí. Stříbrné nebo zlaté úrovně [odolnosti charakteristiky](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) se nedoporučuje, protože:
- Bronzová vám neposkytují žádné záruky týkající se ukládání informací o stavu.
- Stříbrné a zlaté odolnosti zachytávat jakékoli změny VMSS.
- Zlatá také umožňuje kontrolu nad Azure aktualizuje pod VMSS.

Service Fabric "orchestruje" základní změny a aktualizuje tak, že nedojde ke ztrátě dat. Při odebírání uzlu s odolností bronzová však může ztratit informace o stavu. Pokud odstraňujete typ primárního uzlu a je bezstavové aplikace, bronzová je přijatelné. Při spuštění stavová zatížení v produkčním prostředí by měl být minimální požadavky na konfiguraci Silver. Podobně pro produkční scénáře primární typ uzlu musí být vždy stříbrné nebo zlaté úrovně.

### <a name="more-about-bronze-durability"></a>Další informace o odolnosti bronzová

Při odebírání typ uzlu, který je Bronzová, všechny uzly v typu uzlu ihned vypne. Service Fabric nebude zachytávat všechny aktualizace VMSS bronzová uzlů, tedy všechny virtuální počítače přejít ihned. Pokud jste měli nic stavové na těchto uzlech, data budou ztracena. Teď, i v případě, že jste byli bezstavové, všechny uzly v Service Fabric účastnit okruhu, aby celé prostředí byly pravděpodobně ztraceny, což může ovlivnit samotného clusteru.

Na rozdíl od odebrání jednoho uzlu, protože teoreticky vzato můžete odebrat jednoho uzlu současně, počkat repliky a službami, abyste přesuňte, počkejte, systému stabilizovat před odebráním jiný uzel a tak dále.  Nicméně pokud odeberete několik uzlů najednou současně, cluster může ujmout (protože Service Fabric nebude zachytávat jakékoli aktualizace VMSS s odolností bronzová).

## <a name="recommended-node-type-removal-process"></a>Doporučené procesu odebrání typu uzlu

Odebrat typ uzlu nejvíce bezpečného a rychlého způsobem:
1.  Pokud používáte bronzová odolnosti nebo nechcete, aby v systému přechod aplikace, které obsahují informace o stavu, které se bude jako součást ztraceny uzlu typu odstranění, první prázdný stavová data z uzlů, které ovlivňují odebrání typu uzlu.
2.  Spustit [odebrat ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) na každém uzlu, které mají být odstraněny.
3.  Spustit [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set) pro virtuální počítače, které ovlivňují odebrání typu uzlu.
4. Spustit [Remove-azurermservicefabricnodetype:](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) odebrat typ uzlu.

## <a name="next-steps"></a>Další postup
- Další informace o clusteru [odolnosti charakteristiky](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Další informace o [typy uzlů a VM Scale Sets](service-fabric-cluster-nodetypes.md).
- Další informace o [škálování clusteru Service Fabric](service-fabric-cluster-scaling.md).