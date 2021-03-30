---
title: Přidání typu uzlu do clusteru Azure Service Fabric
description: Naučte se škálovat Cluster Service Fabric přidáním sady škálování virtuálního počítače.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: efd329c07b4881c6710d4173857b4186965438d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88719321"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Horizontální škálování Service Fabric clusteru přidáním sady škálování virtuálních počítačů
Tento článek popisuje, jak škálovat cluster Azure Service Fabric přidáním nového typu uzlu do existujícího clusteru. Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Sady škálování virtuálních počítačů jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definovaný v clusteru Azure, je [nastavený jako samostatná sada škálování](service-fabric-cluster-nodetypes.md). Každý typ uzlu se pak dá spravovat samostatně. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně přidáním nového typu uzlu (sada škálování virtuálního počítače) do existujícího clusteru.  Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru.  I když se cluster škáluje, vaše aplikace se automaticky škálují.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Přidání další sady škálování do existujícího clusteru
Přidání nového typu uzlu (který je zálohovaný sadou škálování virtuálního počítače) na stávající cluster se podobá [upgradu primárního typu uzlu](service-fabric-scale-up-primary-node-type.md)s tím rozdílem, že nebudete používat stejný NodeTypeRef; zjevně nebude nutné zakázat žádné aktivně používané sady škálování virtuálních počítačů a neztratíte dostupnost clusteru, pokud neaktualizujete typ primárního uzlu. 

Vlastnost NodeTypeRef je deklarovaná v rámci vlastností rozšíření sady škálování virtuálního počítače Service Fabric:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Kromě toho budete muset přidat tento nový typ uzlu do prostředku Service Fabric clusteru:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [škálovat typ primárního uzlu](service-fabric-scale-up-primary-node-type.md)
* Přečtěte si o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* Horizontální navýšení nebo navýšení [kapacity clusteru Azure](service-fabric-tutorial-scale-cluster.md)
* [Škálujte cluster Azure pomocí programu](service-fabric-cluster-programmatic-scaling.md) Fluent Azure COMPUTE SDK.
* Horizontální navýšení [nebo zmenšení kapacity samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md)

