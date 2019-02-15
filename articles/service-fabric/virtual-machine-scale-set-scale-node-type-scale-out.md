---
title: Přidat typ uzlu do clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak pro horizontální navýšení kapacity clusteru Service Fabric tak, že přidáte Škálovací sady virtuálních počítačů.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: aljo
ms.openlocfilehash: 01d4af8349d3f5a0f58c4c3fa56b489d739c7b42
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301702"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Horizontální navýšení kapacity clusteru Service Fabric tak, že přidáte škálovací sadu virtuálních počítačů
Tento článek popisuje, jak škálování clusteru Azure Service Fabric přidáním nového typu uzlu do existujícího clusteru. Cluster Service Fabric je síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, který použijete k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure je [nastavit jako samostatné škálovací sada](service-fabric-cluster-nodetypes.md). Každý typ uzlu je pak spravovat samostatně. Po vytvoření clusteru Service Fabric, můžete škálovat cluster horizontálně přidáním nového typu uzlu (škálovací sady virtuálních počítačů) do existujícího clusteru.  Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru.  Škálování clusteru, vaše aplikace automaticky škálovat směrem také.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Přidat další škálovací sady do existujícího clusteru
Přidání nového typu uzlu (která je založená na škálovací sadu virtuálních počítačů) ve stávajícím clusteru je podobný [upgradu primárního uzlu typu](service-fabric-scale-up-node-type.md)s výjimkou případů nebudete používat stejné NodeTypeRef; zřejmě nebude možné zakázat některé aktivně používá škálovací sady virtuálních počítačů a neumožňuje můžete přijít o dostupnost clusteru při aktualizaci není primární typ uzlu. 

Vlastnost NodeTypeRef je deklarována v rámci virtuálního počítače škálovací sady vlastností rozšíření Service Fabric:
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

Kromě toho budete muset přidat tento nový typ uzlu prostředku clusteru Service Fabric:

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

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [vertikálně navýšit kapacitu primární typ uzlu](service-fabric-scale-up-node-type.md)
* Další informace o [aplikace škálovatelnost](service-fabric-concepts-scalability.md).
* [Škálování clusteru Azure snížení nebo navýšení kapacity](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru Azure prostřednictvím kódu programu](service-fabric-cluster-programmatic-scaling.md) pomocí fluent Azure compute SDK.
* [Horizontální snížení nebo navýšení kapacity samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md).

